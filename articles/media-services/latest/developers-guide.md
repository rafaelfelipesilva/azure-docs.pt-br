---
title: SDKs dos Serviços de Mídia do Azure v3 – Azure
description: Este artigo fornece uma visão geral de como iniciar o desenvolvimento com a API dos Serviços de Mídia v3 usando SDKs.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 04/11/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 9fb4d1561a661387f759aada9e776d43a95aa5c7
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564501"
---
# <a name="develop-against-media-services-v3-api-using-sdks"></a>Desenvolvimento na API dos Serviços de Mídia v3 usando SDKs

Como desenvolvedor, você pode usar a [API REST](https://aka.ms/ams-v3-rest-ref) dos Serviços de Mídia ou bibliotecas de clientes que permitem interagir com a API REST para criar, gerenciar e manter fluxos de trabalho de mídia personalizados com facilidade. A API dos [Serviços de Mídia v3](https://aka.ms/ams-v3-rest-sdk) se baseia na especificação do OpenAPI (anteriormente conhecida como um Swagger).

> [!NOTE]
> Os SDKs dos Serviços de Mídia do Azure v3 não têm garantia de serem thread-safe. Ao desenvolver um aplicativo com multi-thread, você deverá adicionar sua própria lógica de sincronização de thread para proteger o cliente ou usar um novo objeto AzureMediaServicesClient por thread. Você também deve tomar cuidado com problemas de multi-threading introduzidos por objetos opcionais fornecidos pelo código ao cliente (como uma instância do HttpClient no .NET).

Este tópico fornece links para os SDKs, as ferramentas e os guias de instruções.

## <a name="prerequisites"></a>Pré-requisitos

Para iniciar o desenvolvimento nos Serviços de Mídia, você precisará:

- Uma assinatura ativa do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [Saiba mais sobre conceitos fundamentais](concepts-overview.md)
- Examine [Desenvolvimento com as APIs dos Serviços de Mídia v3](media-services-apis-overview.md)
- [Criar uma conta dos Serviços de Mídia – CLI](create-account-cli-how-to.md)

## <a name="start-developing-with-sdks"></a>Começar a desenvolver com SDKs

### <a name="net"></a>.NET

Use o [SDK do .NET](https://aka.ms/ams-v3-dotnet-sdk) para [se conectar aos Serviços de Mídia](configure-connect-dotnet-howto.md).

Explore a documentação de [referência do .NET](https://aka.ms/ams-v3-dotnet-ref) dos Serviços de Mídia.

### <a name="java"></a>Java

Use o [SDK do Java](https://aka.ms/ams-v3-java-sdk) para [se conectar aos Serviços de Mídia](configure-connect-java-howto.md).

Examine a documentação de [referência do Java](https://aka.ms/ams-v3-java-ref) dos Serviços de Mídia.

### <a name="nodejs"></a>Node.js

Use o [SDK do Node.js](https://aka.ms/ams-v3-nodejs-sdk) para [se conectar aos Serviços de Mídia](configure-connect-nodejs-howto.md).

Explore a documentação de [referência do Node.js](https://aka.ms/ams-v3-nodejs-ref) dos Serviços de Mídia e confira as [amostras](https://github.com/Azure-Samples/media-services-v3-node-tutorials) que explicam como usar a API dos Serviços de Mídia com o Node.js.

### <a name="python"></a>Python

Use o [SDK do Python](https://aka.ms/ams-v3-python-sdk).

Examine a documentação de [referência do Python](https://aka.ms/ams-v3-python-ref) dos Serviços de Mídia.

### <a name="go"></a>Go

Use o [SDK do Go](https://aka.ms/ams-v3-go-sdk).

Examine a documentação de [referência do Go](https://aka.ms/ams-v3-go-ref) dos Serviços de Mídia.

### <a name="ruby"></a>Ruby

Use o [SDK do Ruby](https://aka.ms/ams-v3-ruby-sdk).

## <a name="azure-media-services-explorer"></a>Gerenciador dos Serviços de Mídia do Azure

O [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) (Gerenciador dos Serviços de Mídia do Azure) é uma ferramenta disponível para clientes do Windows que desejam saber mais sobre os Serviços de Mídia. O AMSE é um aplicativo do WinForms/C# que carrega, baixa, codifica e transmite VoD e conteúdo ao vivo com os Serviços de Mídia. A ferramenta AMSE destina-se aos clientes que desejam testar os Serviços de Mídia sem escrever nenhum código. O código AMSE é fornecido como um recurso para clientes que desejam desenvolver com os Serviços de Mídia.

O AMSE é um projeto de software livre, cujo suporte é fornecido pela comunidade (os problemas podem ser relatados a https://github.com/Azure/Azure-Media-Services-Explorer/issues). Este projeto adotou o [Código de Conduta Microsoft Open Source](https://opensource.microsoft.com/codeofconduct/). Para obter mais informações, confira as [Perguntas frequentes sobre o Código de Conduta](https://opensource.microsoft.com/codeofconduct/faq/) ou contate opencode@microsoft.com para enviar outras perguntas ou comentários.

## <a name="next-steps"></a>Próximas etapas

[Visão geral](media-services-overview.md)
