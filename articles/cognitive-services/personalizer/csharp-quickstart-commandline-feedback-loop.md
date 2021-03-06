---
title: Loop de comentários – Personalizador
titleSuffix: Azure Cognitive Services
description: Personalize o conteúdo neste Início Rápido do C# com o serviço Personalizador.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: c566d1fd4b151efc0d28b7059504e60a1451c034
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025506"
---
# <a name="quickstart-personalize-content-using-c"></a>Início Rápido: Personalizar o conteúdo usando o C# 

Exiba o conteúdo personalizado neste início rápido do C# com o serviço Personalizador.

Esta amostra descreve como usar a biblioteca de clientes de personalização para o C# para executar as seguintes ações: 

 * Classificar uma lista de ações para personalização.
 * Relatar a recompensa para alocar a ação com melhor classificação de acordo com a seleção do usuário para o evento especificado.

A introdução à personalização envolve as seguintes etapas:

1. Referenciar o SDK 
1. Escrever um código para classificar as ações que você deseja mostrar aos usuários.
1. Escrever um código para enviar as recompensas para treinar o loop.

## <a name="prerequisites"></a>Pré-requisitos

* É necessário ter uma chave de assinatura e uma URL do serviço de emissão de token.
* [Visual Studio 2015 ou 2017](https://visualstudio.microsoft.com/downloads/).
* O pacote NuGet do SDK de Microsoft.Azure.CognitiveServices.Personalization. As instruções de instalação são fornecidas abaixo.

## <a name="creating-a-new-console-app-and-referencing-the-personalizer-sdk"></a>Como criar um aplicativo de console e referenciar o SDK do Personalizador 

<!--
Get the latest code as a Visual Studio solution from [GitHub] (add link).
-->

1. Crie um novo aplicativo de console do Visual C# no Visual Studio.
1. Instale o pacote NuGet da biblioteca de clientes da personalização. No menu, selecione **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** e, em seguida, **Gerenciar Pacotes NuGet para a Solução**.
1. Selecione a guia **Procurar** e, na caixa **Pesquisa**, digite `Microsoft.Azure.CognitiveServices.Personalization`.
1. Selecione **Microsoft.Azure.CognitiveServices.Personalization** quando ele for exibido.
1. Marque a caixa de seleção ao lado do nome do projeto e selecione **Instalar**.

## <a name="add-the-code-and-put-in-your-personalizer-and-azure-keys"></a>Adicionar o código e colocá-lo nas chaves do Personalizador e do Azure

1. Substitua Program.cs pelo código a seguir. 
1. Substitua o valor `serviceKey` por sua chave de assinatura válida do Personalizador.
1. Substitua `serviceEndpoint` pelo ponto de extremidade de serviço. Um exemplo é `https://westus2.api.cognitive.microsoft.com/`.
1. Execute o programa.

## <a name="add-code-to-rank-the-actions-you-want-to-show-to-your-users"></a>Adicionar um código para classificar as ações que você deseja mostrar aos usuários

O código C# a seguir é uma listagem completa para passar informações de usuário, recursos e informações sobre o conteúdo, as _ações_, para o Personalizador usando o SDK. O Personalizador retorna a ação com a melhor classificação a ser mostrada ao usuário.  

```csharp
using Microsoft.Azure.CognitiveServices.Personalization;
using Microsoft.Azure.CognitiveServices.Personalization.Models;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;

namespace PersonalizationExample
{
    class Program
    {
        // The key specific to your personalization service instance; e.g. "0123456789abcdef0123456789ABCDEF"
        private const string serviceKey = "";

        // The endpoint specific to your personalization service instance; e.g. https://westus2.api.cognitive.microsoft.com/
        private const string serviceEndpoint = "";

        static void Main(string[] args)
        {
            int iteration = 1;
            bool runLoop = true;

            Uri url = new Uri(serviceEndpoint);

            // Get the actions list to choose from personalization with their features.
            IList<RankableAction> actions = GetActions();

            // Initialize Personalization client.
            PersonalizationClient client = InitializePersonalizationClient(url);

            do
            {
                Console.WriteLine("\nIteration: " + iteration++);

                // Get context information from the user.
                string timeOfDayFeature = GetUsersTimeOfDay();
                string tasteFeature = GetUsersTastePreference();

                // Create current context from user specified data.
                IList<object> currentContext = new List<object>() {
                    new { time = timeOfDayFeature },
                    new { taste = tasteFeature }
                };

                // Exclude an action for personalization ranking. This action will be held at its current position.
                IList<string> excludeActions = new List<string> { "juice" };

                // Generate an ID to associate with the request.
                string eventId = Guid.NewGuid().ToString();

                // Rank the actions
                var request = new RankRequest(actions, currentContext, excludeActions, eventId);
                RankResponse response = client.Rank(request);

                Console.WriteLine("\nPersonalization service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

                float reward = 0.0f;
                string answer = GetKey();

                if (answer == "Y")
                {
                    reward = 1;
                    Console.WriteLine("\nGreat! Enjoy your food.");
                }
                else if (answer == "N")
                {
                    reward = 0;
                    Console.WriteLine("\nYou didn't like the recommended food choice.");
                }
                else
                {
                    Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
                }

                Console.WriteLine("\nPersonalization service ranked the actions with the probabilities as below:");
                foreach (var rankedResponse in response.Ranking)
                {
                    Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
                }

                // Send the reward for the action based on user response.
                client.Reward(response.EventId, new RewardRequest(reward));

                Console.WriteLine("\nPress q to break, any other key to continue:");
                runLoop = !(GetKey() == "Q");

            } while (runLoop);
        }

        /// <summary>
        /// Initializes the personalization client.
        /// </summary>
        /// <param name="url">Azure endpoint</param>
        /// <returns>Personalization client instance</returns>
        static PersonalizationClient InitializePersonalizationClient(Uri url)
        {
            PersonalizationClient client = new PersonalizationClient(url,
            new ApiKeyServiceClientCredentials(serviceKey),
            new DelegatingHandler[] { });

            return client;
        }

        /// <summary>
        /// Get users time of the day context.
        /// </summary>
        /// <returns>Time of day feature selected by the user.</returns>
        static string GetUsersTimeOfDay()
        {
            string[] timeOfDayFeatures = new string[] { "morning", "afternoon", "evening", "night" };

            Console.WriteLine("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night");
            if (!int.TryParse(GetKey(), out int timeIndex) || timeIndex < 1 || timeIndex > timeOfDayFeatures.Length)
            {
                Console.WriteLine("\nEntered value is invalid. Setting feature value to " + timeOfDayFeatures[0] + ".");
                timeIndex = 1;
            }

            return timeOfDayFeatures[timeIndex - 1];
        }

        /// <summary>
        /// Gets user food preference.
        /// </summary>
        /// <returns>Food taste feature selected by the user.</returns>
        static string GetUsersTastePreference()
        {
            string[] tasteFeatures = new string[] { "salty", "sweet" };

            Console.WriteLine("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet");
            if (!int.TryParse(GetKey(), out int tasteIndex) || tasteIndex < 1 || tasteIndex > tasteFeatures.Length)
            {
                Console.WriteLine("\nEntered value is invalid. Setting feature value to " + tasteFeatures[0] + ".");
                tasteIndex = 1;
            }

            return tasteFeatures[tasteIndex - 1];
        }

        /// <summary>
        /// Creates personalization actions feature list.
        /// </summary>
        /// <returns>List of actions for personalization.</returns>
        static IList<RankableAction> GetActions()
        {
            IList<RankableAction> actions = new List<RankableAction>
            {
                new RankableAction
                {
                    Id = "pasta",
                    Features =
                    new List<object>() { new { taste = "salty", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
                },

                new RankableAction
                {
                    Id = "ice cream",
                    Features =
                    new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
                },

                new RankableAction
                {
                    Id = "juice",
                    Features =
                    new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
                },

                new RankableAction
                {
                    Id = "salad",
                    Features =
                    new List<object>() { new { taste = "salty", spiceLevel = "low" }, new { nutritionLevel = 8 } }
                }
            };

            return actions;
        }

        private static string GetKey()
        {
            return Console.ReadKey().Key.ToString().Last().ToString().ToUpper();
        }
    }
}
```

## <a name="run-the-program"></a>Execute o programa

Compile e execute o programa. O programa de Início Rápido faz algumas perguntas para coletar as preferências do usuário, conhecidas como recursos e, em seguida, fornece a ação principal.

![O programa de Início Rápido faz algumas perguntas para coletar as preferências do usuário, conhecidas como recursos e, em seguida, fornece a ação principal.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Limpar recursos
Quando você terminar com o início rápido, remova todos os arquivos criados nesse início rápido. 

## <a name="next-steps"></a>Próximas etapas

[Como funciona o Personalizador](how-personalizer-works.md)


