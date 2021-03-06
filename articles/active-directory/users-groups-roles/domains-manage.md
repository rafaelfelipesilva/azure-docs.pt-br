---
title: Adicionar e verificar nomes de domínio personalizados - Azure Active Directory | Microsoft Docs
description: Conceitos de gerenciamento e instruções para gerenciar um nome de domínio no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 908ae768ae471ab6f49452c99323c31d34772d45
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60472293"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Gerenciando nomes de domínio personalizados no Azure Active Directory

Um nome de domínio é uma parte importante do identificador para muitos recursos de diretório: ele é parte de um nome de usuário ou endereço de email para um usuário, parte do endereço para um grupo e algumas vezes é parte do URI da ID do aplicativo para um aplicativo. Um recurso no Azure AD (Azure Active Directory) pode incluir um nome de domínio pertencente ao diretório que contém o recurso. Somente um Administrador Global pode gerenciar domínios no Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Definir o nome de domínio primário para o diretório do Azure AD

Quando o diretório é criado, o nome de domínio inicial, como 'contoso.onmicrosoft.com', também é o nome de domínio primário. O domínio primário é o nome de domínio padrão para um novo usuário quando você cria um novo usuário. Configurar um nome de domínio primário simplifica o processo de criação de novos usuários no portal por um administrador. Para alterar o nome de domínio primário:

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta que é um Administrador Global do diretório.
2. Selecione **Azure Active Directory**.
3. Selecione **Personalizar nomes de domínio**.
  
   ![Abrindo a página de gerenciamento de usuário](./media/domains-manage/add-custom-domain.png)
4. Selecione o nome do domínio que gostaria que fosse o domínio primário.
5. Selecione o comando **Tornar primário**. Confirme sua escolha quando solicitado.
  
   ![Verifique um domínio nome primário](./media/domains-manage/make-primary-domain.png)

Você pode alterar o nome de domínio primário para o seu diretório para qualquer domínio personalizado verificado não federado. Alterar o domínio primário para o seu diretório não alterará o nome de usuário de nenhum usuário existente.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Adicionar nomes de domínio personalizados ao locatário do Azure AD

É possível adicionar até 900 nomes de domínio gerenciado. Se estiver configurando todos os domínios para federação com Active Directory local, será possível adicionar até 450 nomes de domínio em cada diretório.

## <a name="add-subdomains-of-a-custom-domain"></a>Adicionar subdomínios de um domínio personalizado

Se você quiser adicionar um nome de domínio de terceiro nível como 'europe.contoso.com' ao seu diretório, deverá primeiro adicionar e verificar o domínio de segundo nível, como contoso.com. O subdomínio é verificado automaticamente pelo Azure AD. Para ver que o subdomínio que você adicionou foi verificado, atualize a lista de domínios no navegador.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>O que fazer se você alterar o registrador DNS para o nome de domínio personalizado

Se você altera os registradores DNS, não há nenhuma tarefa de configuração adicional no Azure AD. Você pode continuar usando o nome de domínio com o Azure AD sem interrupção. Se você usa o nome de domínio personalizado com o Office 365, o Intune ou outros serviços que dependem de nomes de domínio personalizado no Azure AD, veja a documentação desses serviços.

## <a name="delete-a-custom-domain-name"></a>Excluir um nome de domínio personalizado

Você poderá excluir um nome de domínio do Azure AD se sua organização não usar esse nome de domínio ou se for preciso usar o nome de domínio com outro Azure AD.

Para excluir um nome de domínio personalizado, primeiro você deve assegurar que nenhum recurso em seu diretório dependa do nome de domínio. Você não poderá excluir um nome de domínio do diretório se:

* Qualquer usuário tiver um nome de usuário, endereço de email ou endereço proxy que inclua o nome do domínio.
* Qualquer grupo tiver um endereço de email ou endereço de proxy que inclua o nome de domínio.
* Qualquer aplicativo no Azure AD tiver um URI da ID do aplicativo que inclua o nome de domínio.

Você deve alterar ou excluir qualquer recurso desse tipo em seu diretório do Azure AD antes que possa excluir o nome de domínio personalizado.

### <a name="forcedelete-option"></a>Opção ForceDelete

Você pode aplicar **ForceDelete** a um nome de domínio no [Centro de administração do Azure AD](https://aad.portal.azure.com) ou usando a [API do Microsoft Graph](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta). Essas opções usam uma operação assíncrona e atualizam todas as referências de nome de domínio personalizado, assim como "user@contoso.com", para o nome de domínio padrão inicial, tal como "user@contoso.onmicrosoft.com". 

Para chamar **ForceDelete** no portal do Azure, você deve garantir que há menos de 1.000 referências ao nome de domínio, e quaisquer referências em que o Exchange é o serviço de provisionamento devem ser atualizadas ou removidas no [Centro de administração do Exchange](https://outlook.office365.com/ecp/). Isso inclui listas distribuídas e grupos de segurança habilitados por email do Exchange. Para obter mais informações, veja [Remover grupos de segurança habilitados por email](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups). Além disso, a operação **ForceDelete** não será bem-sucedida se qualquer uma das seguintes opções for verdadeira:

* Você adquiriu um domínio por meio de serviços de assinatura de domínio do Office 365
* Você é um parceiro administrando em nome de outro locatário do cliente

As ações a seguir são executadas como parte da operação **ForceDelete**:

* Renomeia o nome UPN, o EmailAddress e o ProxyAddress de usuários com referências ao nome de domínio personalizado para o nome de domínio padrão inicial.
* Renomeia o EmailAddress de grupos com referências ao nome de domínio personalizado para o nome de domínio padrão inicial.
* Renomeia os identifierUris de aplicativos com referências ao nome de domínio personalizado para o nome de domínio padrão inicial.

Um erro é retornado quando:

* O número de objetos a ser renomeado é maior que 1.000
* Um dos aplicativos a ser renomeado é um aplicativo multilocatário

### <a name="frequently-asked-questions"></a>Perguntas frequentes

**P: Por que a exclusão do domínio está falhando com um erro afirmando que eu tenho grupos controlados pelo Exchange nesse nome de domínio?** <br>
**R:** Hoje em dia, determinados grupos como grupos de segurança habilitados por email e listas distribuídas são provisionados pelo Exchange e precisem ser limpos manualmente no [EAC (Centro de administração do Exchange)](https://outlook.office365.com/ecp/). Poderá haver ProxyAddresses remanescentes que dependam do nome de domínio personalizado e precisarão ser atualizados manualmente para outro nome de domínio. 

**P: Eu estou conectado como administrador\@contoso.com, mas eu não é possível excluir o nome de domínio "contoso.com"?**<br>
**R:** Você não pode referenciar o nome de domínio personalizado que você está tentando excluir em seu nome de conta de usuário. Verifique se a conta de Administrador Global está usando o nome de domínio padrão inicial (.onmicrosoft.com), assim como admin@contoso.onmicrosoft.com. Entre com uma conta de Administrador Global diferente, tal como admin@contoso.onmicrosoft.com, ou com outro nome de domínio personalizado, tal como "fabrikam.com", em que a conta é admin@fabrikam.com.

**P: Eu cliquei no botão Excluir domínio e vejo o status `In Progress` para a operação de exclusão. Quanto tempo demora? O que acontecerá se ela falhar?**<br>
**R:** A operação de exclusão do domínio é uma tarefa assíncrona em segundo plano que renomeia todas as referências para o nome de domínio. Ela deve ser concluída dentro de um ou dois minutos. Se a exclusão do domínio falhar, verifique se você não tem:

* Aplicativos configurados no nome de domínio com o appIdentifierURI
* Qualquer grupo habilitado por email referenciando o nome de domínio personalizado
* Mais de 1.000 referências ao nome de domínio

Se você achar que alguma das condições ainda não foi atendida, limpe as referências manualmente e tente excluir o domínio novamente.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Usar o PowerShell ou o API do Graph para gerenciar nomes de domínio

A maioria das tarefas de gerenciamento para nomes de domínio no Azure Active Directory também pode ser concluída usando o Microsoft PowerShell ou de forma programática, usando a API do Graph do Azure AD.

* [Como usar o PowerShell para gerenciar nomes de domínio no Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Como usar a API do Graph para gerenciar nomes de domínio no Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Próximas etapas

* [Adicionar nomes de domínio personalizados](/azure/active-directory/fundamentals/add-custom-domain?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Remover grupos de segurança habilitados por email do Exchange no Centro de administração do Exchange em um nome de domínio personalizado no Azure AD](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)
* [Aplicar ForceDelete a um nome de domínio personalizado com a API do Microsoft Graph](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta)
