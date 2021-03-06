---
title: Problema ao usar o acesso de aplicativo de autoatendimento | Microsoft Docs
description: Solucione problemas relacionados ao acesso de aplicativo de autoatendimento
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd3ae06c42452d425f041543715336827048ccb2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60293980"
---
# <a name="problem-using-self-service-application-access"></a>Problema ao usar o acesso de aplicativo de autoatendimento

O acesso de aplicativo de autoatendimento é uma ótima maneira de permitir que os usuários descubram aplicativos por conta própria e, ainda, permitir que o grupo de negócios aprove o acesso a esses aplicativos. Você pode permitir que o grupo de negócios gerencie as credenciais atribuídas a esses usuários para Aplicativos de logon único com senha, diretamente de seus painéis de acesso.

Antes que seus usuários possam descobrir por conta própria aplicativos de seu painel de acesso, você precisa habilitar o **Acesso de aplicativo de autoatendimento** a todos os aplicativos que você quiser permitir que os usuários descubram e solicitem acesso por conta própria.

## <a name="general-issues-to-check-first"></a>Problemas gerais para verificar primeiro

-   Verifique se o acesso do aplicativo de autoatendimento está configurado corretamente. Consulte "Como configurar o acesso de aplicativo de autoatendimento".

-   Verifique se o usuário ou o grupo foi habilitado para solicitar acesso de aplicativos de autoatendimento.

-   Verifique se o usuário está visitando o local correto para acesso de aplicativos de autoatendimento. Os usuários poderão navegar para seu [Painel de Acesso do Aplicativo](https://myapps.microsoft.com/) e clicar no botão **+Adicionar** para encontrar os aplicativos aos quais você habilitou o acesso de autoatendimento.

-   Se o acesso de aplicativo de autoatendimento tiver sido configurado recentemente, tente entrar e sair novamente do Painel de Acesso do usuário após alguns minutos para ver se as alterações de acesso de autoatendimento apareceram.

## <a name="how-to-configure-self-service-application-access"></a>Como configurar o acesso de aplicativo de autoatendimento

Para habilitar o acesso de aplicativos de autoatendimento a um aplicativo, siga as etapas abaixo:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6. Selecione na lista o aplicativo ao qual você deseja habilitar o acesso do autoatendimento.

7. Após o carregamento do aplicativo, clique em **Autoatendimento** no menu de navegação esquerdo do aplicativo.

8. Para habilitar o acesso de aplicativos de autoatendimento a este aplicativo, coloque o controle de alternância **Permitir aos usuários solicitar acesso a esse aplicativo?** na posição **Sim.**

9. Em seguida, para selecionar o grupo ao qual os usuários que solicitam acesso a esse aplicativo devem ser adicionados, clique no seletor ao lado do rótulo **A qual grupo os usuários atribuídos devem ser adicionados?** e selecione um grupo.

10. **Opcional:** Se quiser exigir uma aprovação de negócios antes que os usuários tenham permissão de acesso, coloque o controle de alternância **Exigir aprovação antes de conceder acesso a esse aplicativo?** na posição **Sim**.

11. **Opcional: Somente para aplicativos que usam logon único com senha,** se quiser permitir que os aprovadores de negócios especifiquem as senhas que são enviadas para esse aplicativo aos usuários aprovados, coloque o controle de alternância **Permitir que os aprovadores definam senhas do usuário para este aplicativo?** na posição **Sim**.

12. **Opcional:** Para especificar os aprovadores de negócios que têm permissão para aprovar o acesso ao aplicativo, clique no seletor ao lado do rótulo **Quem tem permissão para aprovar o acesso a esse aplicativo?** para selecionar até 10 aprovadores de negócios individuais.

    >[!NOTE]
    > Não há suporte para grupos.
    >
    >

13. **Opcional:** **para aplicativos que expõem funções**, se quiser atribuir usuários aprovados de autoatendimento a uma função, clique no seletor ao lado de **A que função os usuários devem ser atribuídos neste aplicativo?** para selecionar a função a que esses usuários devem ser atribuídos.

14. Clique no botão **Salvar** na parte superior da folha para terminar.

Após você concluir a configuração do aplicativo de autoatendimento, os usuários poderão navegar para seu [Painel de Acesso do Aplicativo](https://myapps.microsoft.com/) e clicar no botão **+Adicionar** para encontrar os aplicativos aos quais você habilitou o acesso de autoatendimento. Aprovadores de negócios também recebem uma notificação em seu [Painel de Acesso do Aplicativo](https://myapps.microsoft.com/). Você pode habilitar um email que notifica a eles quando um usuário solicitar acesso a um aplicativo que requer sua aprovação. 

Essas aprovações dão suporte a fluxos de trabalho de aprovação únicos, o que significa que, se você especificar vários aprovadores, qualquer aprovador individual poderá aprovar o acesso ao aplicativo.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Se essas etapas de solução de problemas não resolverem o problema 

Abra um tíquete de suporte com as informações a seguir, se estiverem disponíveis:

-   ID de erro de correlação

-   UPN (endereço de email de usuário)

-   TenantID

-   Tipo de navegador

-   Fuso horário e hora/cronograma durante o erro

-   Rastreamentos do Fiddler

## <a name="next-steps"></a>Próximos passos
[Configuração do Azure Active Directory para gerenciamento de grupo de autoatendimento](../users-groups-roles/groups-self-service-management.md)
