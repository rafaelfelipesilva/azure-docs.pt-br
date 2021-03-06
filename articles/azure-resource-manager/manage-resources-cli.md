---
title: Gerenciar recursos do Azure, usando a CLI do Azure | Microsoft Docs
description: Use a CLI do Azure e o Azure Resource Manager para gerenciar seus recursos.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 076c57f5415a4f6f19252fb5a3546e5e9a8a23f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550233"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Gerenciar recursos do Azure, usando a CLI do Azure

Saiba como usar a CLI do Azure com [do Azure Resource Manager](resource-group-overview.md) para gerenciar seus recursos do Azure. Para gerenciar grupos de recursos, consulte [grupos de recursos do Azure gerenciar usando a CLI do Azure](./manage-resource-groups-cli.md).

Outros artigos sobre o gerenciamento de recursos:

- [Gerenciar recursos do Azure usando o portal do Azure](./manage-resources-portal.md)
- [Gerenciar recursos do Azure usando o PowerShell do Azure](./manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Implantar recursos em um grupo de recursos

Você pode implantar os recursos do Azure diretamente usando o Azure PowerShell ou implantar um modelo do Resource Manager para criar recursos do Azure.

### <a name="deploy-a-resource"></a>Implantar um recurso

O script a seguir cria uma conta de armazenamento.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account create --resource-group $resourceGroupName --name $storageAccountName --location $location --sku Standard_LRS --kind StorageV2 &&
az storage account show --resource-group $resourceGroupName --name $storageAccountName 
```

### <a name="deploy-a-template"></a>Implantar um modelo

O script a seguir cria implantar um modelo de início rápido para criar uma conta de armazenamento. Para saber mais, confira [Início Rápido: Criar modelos do Azure Resource Manager usando o Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group deployment create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Para obter mais informações, veja [Implantar recursos com modelos do Resource Manager e a CLI do Azure](./resource-group-template-deploy-cli.md).

## <a name="deploy-a-resource-group-and-resources"></a>Implantar um grupo de recursos e recursos

Você pode criar um grupo de recursos e implantar recursos no grupo. Para saber mais, confira [Create resource group and deploy resources](./deploy-to-subscription.md#create-resource-group-and-deploy-resources) (Criar grupo de recursos e implantar recursos).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Implantar recursos em várias assinaturas ou grupos de recursos

Normalmente, você deve implantar todos os recursos em seu modelo em um único grupo de recursos. No entanto, há cenários em que você deseja implantar um conjunto de recursos de uma vez, mas colocá-los em diferentes grupos de recursos ou assinaturas. Para obter mais informações, consulte [implantar recursos do Azure para várias assinaturas ou grupos de recursos](./resource-manager-cross-resource-group-deployment.md).

## <a name="delete-resources"></a>Excluir recursos

O script a seguir mostra como excluir uma conta de armazenamento.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

Para obter mais informações sobre como o Azure Resource Manager ordena a exclusão de recursos, consulte [exclusão do grupo de recursos do Azure Resource Manager](./resource-group-delete.md).

## <a name="move-resources"></a>Mover recursos

O script a seguir mostra como remover uma conta de armazenamento de um grupo de recursos para outro grupo de recursos.

```azurecli-interactive
echo "Enter the source Resource Group name:" &&
read srcResourceGroupName &&
echo "Enter the destination Resource Group name:" &&
read destResourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $srcResourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az resource move --destination-group $destResourceGroupName --ids $storageAccount
```

Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](resource-group-move-resources.md).

## <a name="lock-resources"></a>Bloquear recursos

O bloqueio impede que outros usuários na sua organização acidentalmente excluam ou modifiquem recursos críticos, como a assinatura do Azure, grupo de recursos ou recurso. 

O script a seguir bloqueia uma conta de armazenamento, a conta não pode ser excluído.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

O script a seguir obtém todos os bloqueios para uma conta de armazenamento:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

O script a seguir exclui um bloqueio de uma conta de armazenamento:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

Para saber mais, confira [Bloquear recursos com o Gerenciador de Recursos do Azure](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Recursos de marca

Ajuda a organizar logicamente o seu grupo de recursos e recursos de marcação. Para obter informações, consulte [usando marcas para organizar seus recursos do Azure](./resource-group-using-tags.md#azure-cli).

## <a name="manage-access-to-resources"></a>Gerenciar acesso aos recursos

O [Controle de acesso baseado em função (RBAC)](../role-based-access-control/overview.md) é a maneira de gerenciar o acesso aos recursos no Azure. Para obter mais informações, consulte [gerenciar o acesso usando a CLI do Azure e RBAC](../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Próximas etapas

- Para saber o Azure Resource Manager, consulte [visão geral do Azure Resource Manager](./resource-group-overview.md).
- Para obter a sintaxe do modelo do Resource Manager, consulte [entender a estrutura e sintaxe dos modelos do Azure Resource Manager](./resource-group-authoring-templates.md).
- Para saber como desenvolver modelos, consulte o [tutoriais passo a passo](/azure/azure-resource-manager/).
- Para exibir os esquemas de modelo do Azure Resource Manager, consulte [referência de modelo](/azure/templates/).
