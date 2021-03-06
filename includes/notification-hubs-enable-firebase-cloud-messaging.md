---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 02/05/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: e6b949824ec5da60c5e2485be830e61d156a11ff
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55830659"
---
1. Faça logon no [console do Firebase](https://firebase.google.com/console/). Crie um novo projeto do Firebase se você ainda não tiver um.
2. Depois de criar seu projeto, selecione **Adicionar Firebase ao seu aplicativo Android**. 

    ![Adicione o Firebase ao seu aplicativo Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Na página **Adicionar Firebase ao seu aplicativo Android**, siga estas etapas: 
    1. Para **Nome do pacote Android**, copie o valor de **applicationId** no arquivo **build.gradle** do aplicativo. Neste exemplo, é `com.fabrikam.fcmtutorial1app`. 

        ![Especificar o nome do pacote](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Selecione **Registrar aplicativo**. 
4. Selecione **Baixar google-services.json**, salve o arquivo na pasta **app** do projeto e, em seguida, selecione **Avançar**. 

    ![Baixar google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Faça as seguintes **alterações na configuração** do seu projeto no Android Studio. 
    1.  No arquivo **project-level build.gradle** (&lt;project&gt;/build.gradle), adicione a seguinte instrução à seção de **dependências**. 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. No arquivo **build.gradle no nível do aplicativo** (&lt;project&gt;/&lt;app-module&gt;/build.gradle), adicione a seguinte instrução à seção de **dependências**. 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.1'
        ```

    3. Adicione a seguinte linha ao final do arquivo **build.gradle no nível do aplicativo** após a seção de dependências. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
 
        ![Alterações na configuração de build.gradle](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Selecione **Avançar** na página. 
7. Selecione **Ignorar esta etapa** na página. 

    ![Ignorar a última etapa](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)1. 
8. No console do Firebase, selecione a engrenagem para seu projeto. Em seguida, selecione **Configurações do Projeto**.

    ![Selecione Configurações do Projeto](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Se você não tiver baixado o arquivo **google-services.json** para a pasta **app** do projeto do Android Studio, será possível fazer isso nesta página. 
5. Mude para a guia **Mensagens de Nuvem** na parte superior. 
6. Copie e salve a **Chave do servidor** para uso posterior. Você usará esse valor para configurar o hub de notificação.
