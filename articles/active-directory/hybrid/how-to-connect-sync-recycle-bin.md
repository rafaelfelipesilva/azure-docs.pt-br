---
title: 'Sincronização do Azure AD Connect: Habilitar a lixeira do AD | Microsoft Docs'
description: Este tópico recomenda o uso do recurso Lixeira do AD com o Azure AD Connect.
services: active-directory
keywords: Lixeira do AD, exclusão acidental, âncora de origem
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/17/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fe7d3ea7d4f6d648438efc1a484d5909ade2f23
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60382888"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Sincronização do Azure AD Connect: Habilitar lixeira do AD
É recomendável que você habilite o recurso Lixeira do AD para seus Active Directories locais, sincronizados com o Azure AD. 

Se você excluiu acidentalmente um objeto de usuário do AD local e o restaurou usando o recurso, o Azure AD restaura o objeto de usuário do Azure AD correspondente.  Para obter informações sobre o recurso Lixeira do AD, consulte o artigo [Scenario Overview for Restoring Deleted Active Directory Objects (Visão geral do cenário de restauração de objetos do Active Directory excluídos)](https://technet.microsoft.com/library/dd379542.aspx).

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Benefícios da habilitação da lixeira do AD
Esse recurso ajuda a restaurar os objetos de usuário do Azure AD, fazendo o seguinte:

* Se você excluiu acidentalmente um objeto de usuário do AD local, o objeto de usuário do Azure AD correspondente será excluído no próximo ciclo de sincronização. Por padrão, o Azure AD mantém o objeto de usuário excluído do Azure AD em estado de exclusão temporária durante 30 dias.

* Se você o recurso Lixeira do AD local estiver habilitado, será possível restaurar o objeto de usuário excluído do AD local sem alterar seu valor de Âncora de origem. Quando o objeto de usuário recuperado do AD local for sincronizado com o Azure AD, o Azure AD restaurará o objeto de usuário excluído temporariamente correspondente do Azure AD. Para obter informações sobre o atributo Âncora de Origem, consulte o artigo [Azure AD Connect: Conceitos de design](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor).

* Se você não tiver o recurso Lixeira do AD local habilitado, talvez seja necessário criar um objeto de usuário do AD para substituir o objeto excluído. Se o Azure AD Connect Synchronization Service tiver sido configurado para usar o atributo do AD gerado pelo sistema (por exemplo, ObjectGuid) para o atributo Âncora de origem, o objeto de usuário do AD recém-criado não terá o mesmo valor de Âncora de origem que o objeto de usuário excluído do AD. Quando o objeto de usuário recém-criado do AD for sincronizado com o Azure AD, o Azure AD criará um novo objeto de usuário do Azure AD em vez de restaurar o objeto de usuário excluído temporariamente do Azure AD.

> [!NOTE]
> Por padrão, o Azure AD mantém objetos de usuário excluídos do Azure AD em estado de exclusão temporária durante 30 dias antes que eles sejam excluídos permanentemente. No entanto, os administradores podem acelerar a exclusão desses objetos. Depois que os objetos forem excluídos permanentemente, eles não poderão mais ser recuperados, mesmo se o recurso Lixeira do AD local estiver habilitado.

## <a name="next-steps"></a>Próximos passos
**Tópicos de visão geral**

* [Sincronização do Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)

* [Integração de suas identidades locais com o Active Directory do Azure](whatis-hybrid-identity.md)
