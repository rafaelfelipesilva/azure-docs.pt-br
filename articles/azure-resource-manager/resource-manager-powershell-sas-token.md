---
title: Implantar o modelo do Azure com o token SAS e o PowerShell | Microsoft Docs
description: Use o Azure Resource Manager e o Azure PowerShell para implantar recursos para o Azure de um modelo que é protegido com o token SAS.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
ms.openlocfilehash: 818aa63ced56d7cf382536f10bb6150199ab74e9
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268933"
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-powershell"></a>Implantar o modelo particular do Resource Manager com o Azure PowerShell e o token SAS

Quando seu modelo reside em uma conta de armazenamento, você pode restringir o acesso a ele e fornecer um token de SAS (Assinatura de Acesso Compartilhado) durante a implantação. Este tópico explica como usar o Azure PowerShell com modelos do Resource Manager para fornecer um token SAS durante a implantação. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-private-template-to-storage-account"></a>Adicionar modelo privado à conta de armazenamento

Você pode adicionar seus modelos a uma conta de armazenamento e vinculá-los durante a implantação com um token SAS.

> [!IMPORTANT]
> Seguindo as etapas abaixo, o blob que contém o modelo fica acessível somente para o proprietário da conta. No entanto, quando você cria um token SAS para o blob, o blob fica acessível para qualquer pessoa com o URI. Se outro usuário interceptar o URI, esse usuário será capaz de acessar o modelo. Usar um token SAS é uma boa maneira de limitar o acesso aos seus modelos, mas você não deve incluir dados confidenciais como senhas diretamente no modelo.
> 
> 

O seguinte exemplo configura um contêiner de conta de armazenamento privado e carrega um modelo:
   
```powershell
# create a storage account for templates
New-AzResourceGroup -Name ManageGroup -Location "South Central US"
New-AzStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name} -Type Standard_LRS -Location "West US"
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# create a container and upload template
New-AzStorageContainer -Name templates -Permission Off
Set-AzStorageBlobContent -Container templates -File c:\MyTemplates\storage.json
```

## <a name="provide-sas-token-during-deployment"></a>Forneça um token SAS durante a implantação
Para implantar um modelo privado em uma conta de armazenamento, gere um token SAS e inclua-o no URI para o modelo. Defina a hora de vencimento de forma a permitir que haja tempo suficiente para concluir a implantação.
   
```powershell
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken -Container templates -Blob storage.json -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri $templateuri
```

Para ver um exemplo de como usar um token SAS com modelos vinculados, consulte [Usando modelos vinculados com o Azure Resource Manager](resource-group-linked-templates.md).


## <a name="next-steps"></a>Próximas etapas
* Para obter uma introdução à implantação de modelos, veja [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](resource-group-template-deploy.md).
* Para um script de exemplo completo que implanta um modelo, veja [Implantar o script de modelo do Resource Manager](resource-manager-samples-powershell-deploy.md)
* Para definir os parâmetros no modelo, consulte [Criando modelos](resource-group-authoring-templates.md#parameters).
