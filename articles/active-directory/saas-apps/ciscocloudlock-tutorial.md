---
title: 'Tutorial: Integração do Azure Active Directory ao The Cloud Security Fabric | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o The Cloud Security Fabric.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 549e8810-1b3b-4351-bf4b-f07de98980d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a556b38ca4947b71555ba7b023607b392900bdaf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60429285"
---
# <a name="tutorial-azure-active-directory-integration-with-the-cloud-security-fabric"></a>Tutorial: Integração do Azure Active Directory ao The Cloud Security Fabric

Neste tutorial, você aprenderá a integrar o The Cloud Security Fabric ao Azure AD (Azure Active Directory).

A integração do The Cloud Security Fabric ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao The Cloud Security Fabric.
- É possível permitir que os usuários se conectem automaticamente ao The Cloud Security Fabric (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao The Cloud Security Fabric, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura do The Cloud Security Fabric habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do The Cloud Security Fabric da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-the-cloud-security-fabric-from-the-gallery"></a>Adição do The Cloud Security Fabric da galeria
Para configurar a integração do The Cloud Security Fabric com o Azure AD, é necessário adicionar o The Cloud Security Fabric da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o The Cloud Security Fabric da galeria, execute estas etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **The Cloud Security Fabric**, selecione **The Cloud Security Fabric** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![The Cloud Security Fabric na lista de resultados](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o The Cloud Security Fabric, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do The Cloud Security Fabric é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do The Cloud Security Fabric.

Para configurar e testar o logon único do Azure AD com o The Cloud Security Fabric, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do The Cloud Security Fabric](#create-a-the-cloud-security-fabric-test-user)** – para ter um equivalente de Brenda Fernandes no The Cloud Security Fabric que esteja vinculado à representação de usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo The Cloud Security Fabric.

**Para configurar o logon único do Azure AD com o The Cloud Security Fabric, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo do **The Cloud Security Fabric**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_samlbase.png)

1. Na seção **Domínio e URLs do The Cloud Security Fabric**, execute as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do The Cloud Security Fabric](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_url.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL:

    | |
    |--|
    | `https://platform.cloudlock.com` |
    | `https://app.cloudlock.com` |

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:
    
    | |
    |--|
    | `https://platform.cloudlock.com/gate/saml/sso/<subdomain>` |
    | `https://app.cloudlock.com/gate/saml/sso/<subdomain>` |

    > [!NOTE]
    > O valor do Identificador não é real. Atualize o valor com o identificador real. Entre em contato com a [equipe de suporte ao cliente do The Cloud Security Fabric](mailto:support@cloudlock.com) para obter o valor. 

1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_certificate.png)

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/ciscocloudlock-tutorial/tutorial_general_400.png)

1. Para configurar o logon único no lado do **The Cloud Security Fabric**, é necessário enviar o **XML de Metadados** baixado para a [equipe de suporte do The Cloud Security Fabric](mailto:support@cloudlock.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/ciscocloudlock-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/ciscocloudlock-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/ciscocloudlock-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/ciscocloudlock-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.

### <a name="create-a-the-cloud-security-fabric-test-user"></a>Criar um usuário de teste do The Cloud Security Fabric

Nesta seção, você cria um usuário chamado Brenda Fernandes no The Cloud Security Fabric. Trabalhe com a  [equipe de suporte do The Cloud Security Fabric](mailto:support@cloudlock.com)  para adicionar os usuários à plataforma do The Cloud Security Fabric. Os usuários devem ser criados e ativados antes de usar o logon único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao The Cloud Security Fabric.

![Atribuir a função de usuário][200]

**Para atribuir Brenda Fernandes ao The Cloud Security Fabric, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

1. Na lista de aplicativos, selecione **The Cloud Security Fabric** .

    ![O link do Cloud Security Fabric na lista de aplicativos](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco The Cloud Security Fabric no Painel de Acesso, você entrará automaticamente no aplicativo The Cloud Security Fabric.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/ciscocloudlock-tutorial/tutorial_general_01.png
[2]: ./media/ciscocloudlock-tutorial/tutorial_general_02.png
[3]: ./media/ciscocloudlock-tutorial/tutorial_general_03.png
[4]: ./media/ciscocloudlock-tutorial/tutorial_general_04.png

[100]: ./media/ciscocloudlock-tutorial/tutorial_general_100.png

[200]: ./media/ciscocloudlock-tutorial/tutorial_general_200.png
[201]: ./media/ciscocloudlock-tutorial/tutorial_general_201.png
[202]: ./media/ciscocloudlock-tutorial/tutorial_general_202.png
[203]: ./media/ciscocloudlock-tutorial/tutorial_general_203.png
