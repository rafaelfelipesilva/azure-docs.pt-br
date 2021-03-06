---
title: Adicionar ou remover um grupo de outro grupo – Azure Active Directory | Microsoft Docs
description: Instruções sobre como adicionar ou remover um grupo de outro grupo usando o Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68b6c1e037992930a70630b0d218cc98beba931d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60249264"
---
# <a name="add-or-remove-a-group-from-another-group-using-azure-active-directory"></a>Adicionar ou remover um grupo de outro grupo usando o Azure Active Directory
Este artigo explica como adicionar e remover um grupo de outro grupo usando o Azure Active Directory.

>[!Note]
>Se você estiver tentando excluir o grupo pai, consulte [Como atualizar ou excluir um grupo e seus membros](active-directory-groups-delete-group.md).

## <a name="add-a-group-to-another-group"></a>Adicionar um grupo a outro grupo
É possível adicionar um grupo de segurança existente a outro grupo de segurança existente (também conhecido como grupos aninhados), criando um grupo de membros (subgrupo) e um grupo pai. O grupo de membros herda os atributos e propriedades do grupo pai, economizando tempo de configuração.

>[!Important]
>No momento, não há suporte para:<ul><li>Adição de grupos a um grupo sincronizado com o Active Directory local.</li><li>Adição de grupos de segurança a grupos do Office 365.</li><li>Adição de grupos do Office 365 a grupos de segurança ou outros grupos do Office 365.</li><li>Atribuição de aplicativos a grupos aninhados.</li><li>Aplicação de licenças a grupos aninhados.</li></ul>

### <a name="to-add-a-group-as-a-member-of-another-group"></a>Para adicionar um grupo como membro de outro grupo

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta de administrador Global para o diretório.

2. Selecione **Azure Active Directory** e, em seguida, selecione **Grupos**.

3. Na página **Grupos - Todos os grupos**, pesquise e selecione o grupo que se tornará membro de outro grupo. Para esse exercício estamos usando o grupo **Política de MDM - Oeste**.

    >[!Note]
    >Você pode adicionar seu grupo como membro a apenas um grupo de cada vez. Além disso, a caixa **Selecionar Grupo** filtra a exibição com base na correspondência de sua entrada para qualquer parte de um usuário ou nome do dispositivo. Porém, não há suporte para caracteres curinga.

    ![Página Grupos - Todos os grupos com o grupo Política de MDM - Oeste selecionado](media/active-directory-groups-membership-azure-portal/group-all-groups-screen.png)

4. Na página **Política de MDM - Oeste - Associações de grupo**, selecione **Associações de grupo**, selecione **Adicionar**, localize o grupo no qual você quer que seu grupo seja membro e, em seguida, escolha **Selecionar**. Para esse exercício, estamos usando o grupo **Política de MDM - Toda org**.

    O grupo **Política de MDM - Oeste** agora é membro da **Política de MDM - Toda org.**, herdando todas as propriedades e configurações do grupo Política de MDM - Toda org.

    ![Criar uma associação de grupo, adicionando grupo a outro grupo](media/active-directory-groups-membership-azure-portal/add-group-membership.png)

5. Revise a página **Política de MDM - Oeste - Associações de grupo** para ver o relacionamento entre o grupo e o membro.

    ![Política de MDM - Oeste - Associações de grupo mostrando o grupo pai](media/active-directory-groups-membership-azure-portal/group-membership-blade.png)

6. Para uma exibição mais detalhada da relação de membro e grupo, selecione o nome do grupo (**Política de MDM - Toda org.**) e dê uma olhada nos detalhes da página **Política de MDM - Oeste**.

    ![Página de associação de grupo mostrando os detalhes do membro e do grupo](media/active-directory-groups-membership-azure-portal/group-membership-review.png)

## <a name="remove-a-group-from-another-group"></a>Remover um grupo de outro grupo
É possível remover um grupo de segurança existente de outro grupo de segurança. No entanto, a remoção do grupo também remove os atributos e propriedades herdados de seus membros.

### <a name="to-remove-a-member-group-from-another-group"></a>Para remover um grupo de membros de outro grupo
1. Na página **Grupos - Todos os grupos**, pesquise e selecione o grupo que será removido como membro de outro grupo. Para esse exercício, estamos novamente usando o grupo **Política de MDM - Oeste**.

2. Na página de **Visão geral da Política de MDM - Oeste**, selecione **Associações de grupo**.

    ![Página de visão geral da Política de MDM - Oeste](media/active-directory-groups-membership-azure-portal/group-membership-overview.png)

3. Selecione o grupo **Política de MDM - Toda org.** na página **Política de MDM - Oeste - Associações de grupo** e, em seguida, selecione **Remover** dos detalhes da página **Política de MDM - Oeste**.

    ![Página de associação de grupo mostrando os detalhes do membro e do grupo](media/active-directory-groups-membership-azure-portal/group-membership-remove.png)


## <a name="additional-information"></a>Informações adicionais
Esses artigos fornecem mais informações sobre o Active Directory do Azure.

- [Exibir grupos e membros](active-directory-groups-view-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

- [Adicionar ou remover membros de um grupo](active-directory-groups-members-azure-portal.md)

- [Editar as configurações de grupo](active-directory-groups-settings-azure-portal.md)

- [Uso de um grupo para gerenciar o acesso a aplicativos SaaS](../users-groups-roles/groups-saasapps.md)

- [Cenários, limitações e problemas conhecidos usando grupos para gerenciar o licenciamento no Azure Active Directory](../users-groups-roles/licensing-group-advanced.md#limitations-and-known-issues)
