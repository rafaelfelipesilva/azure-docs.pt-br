---
title: Registrar um novo dispositivo do Visual Studio Code – Azure IoT Edge | Microsoft Docs
description: Use o Visual Studio Code para criar um dispositivo IoT Edge no seu hub IoT do Azure e recuperar a cadeia de conexão
author: kgremban
manager: philmea
ms.author: v-yiso
origin.date: 01/03/2019
ms.date: 01/28/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2b851e7f2ebdbff08fa09002765fbd7d7927deba
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126283"
---
# <a name="register-a-new-azure-iot-edge-device-from-visual-studio-code"></a>Registre um novo dispositivo Azure IoT Edge do Visual Studio Code

Antes que você possa usar os seus dispositivos IoT com o Azure IoT Edge, você precisa registrá-los com o seu Hub IoT. Quando você registrar um dispositivo, você receberá uma cadeia de caracteres de conexão que pode ser usada para configurar o seu dispositivo para cargas de trabalho Edge. 

Este artigo descreve como registrar um novo dispositivo do IoT Edge usando Visual Studio Code (VS Code). Há múltiplas maneiras de fazer mais operações no VS Code. Este artigo usa o Explorer, mas você pode também usar a Paleta de Comandos para executar a maior parte dos passos. 

## <a name="prerequisites"></a>Pré-requisitos

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na assinatura do Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Ferramentas de IoT do Microsoft Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para Visual Studio Code

## <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o Hub IoT

É possível usar as extensões do Azure IoT para Visual Studio Code para executar operações com o Hub IoT. Para que essas operações funcionem, é necessário entrar na conta do Azure e selecionar o hub IoT em que está trabalhando.

1. No Visual Studio Code, abra a exibição do **Explorer**.

2. Na parte inferior do Explorer, expanda a seção **Dispositivos do Hub IoT**. 

   ![Expanda a seção de Dispositivos do Azure Hub IoT](./media/how-to-register-device-vscode/azure-iot-hub-devices.png)

3. Clique em **...** no cabeçalho da seção **Dispositivos do Hub IoT**. Se você não visualizar as reticências, clique ou passe o mouse sobre o cabeçalho. 

4. Escolha **Selecionar Hub IoT**.

5. Se não estiver conectado à conta do Azure, siga as instruções para fazer isso. 

6. Selecione sua assinatura do Azure. 

7. Selecione seu Hub IoT. 

## <a name="create-a-device"></a>Criar um dispositivo

1. No explorador do VS Code, expanda a seção **Dispositivos do Hub IoT do Azure**. 

2. Clique em **...** no cabeçalho da seção **Dispositivos do Hub IoT**. Se você não visualizar as reticências, clique ou passe o mouse sobre o cabeçalho. 

3. Selecione **Criar dispositivo IoT Edge**. 

4. Na caixa de texto que abrir, dê a seu dispositivo um ID. 

Na tela de saída, você verá o resultado do comando. A informação do dispositivo é impressa, o que inclui o **deviceId** que você forneceu e o **connectionString** que você consegue usar para conectar seu dispositivo físico ao Hub IoT. 

## <a name="view-all-devices"></a>Exibir todos os dispositivos

Todos os dispositivos que conectam ao seu Hub IoT estão listados na seção **Dispositivos do Azure IoT Hub** do Visual Studio Code Explorer. Dipositivos IoT Edge são distinguíveis dos dispositivos não-Edge com um ícone diferente, e o fato de eles poderem ser expandidos para mostrarem os módulos implantados em cada dispositivo. 

   ![Exibir todos os dispositivos do IoT Edge no hub IoT](./media/how-to-register-device-vscode/view-devices.png)

## <a name="retrieve-the-connection-string"></a>Recuperar a cadeia de conexão

Quando você estiver pronto para configurar o seu dispositivo, você precisará de uma cadeia de caracteres de conexão que conecte o seu dispositivo físico com a sua identidade no Hub IoT.

1. Clique com o botão direito na ID do seu dispositivo na seção **Dispositivos Azure Hub IoT**. 
2. Selecione **Copiar cadeia de conexão de dispositivo**.

   A cadeia de conexão é copiada à sua área de transferência. 

Você também pode selecionar **Pegar Informação do Dispositivo** do menu de botão direito para ver todas as informações do dispositivo, incluindo a cadeia de conexão, na janela de saída. 


## <a name="next-steps"></a>Próximas etapas

Saiba como [Implantar módulos em um dispositivo com o Visual Studio Code](how-to-deploy-modules-vscode.md).
