---
title: Modelos do Azure Resource Manager para a API do Gremlin do Azure Cosmos DB
description: Use modelos do Azure Resource Manager para criar e configurar a API do Gremlin do Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: d1928606a22eba180ebd3f1e979362e75edaf2d7
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077779"
---
# <a name="create-azure-cosmos-db-gremlin-api-resources-from-a-resource-manager-template"></a>Criar recursos de API do Gremlin do Azure Cosmos DB de um modelo do Resource Manager

Saiba como criar um recurso de API do Gremlin do Azure Cosmos DB usando um modelo do Azure Resource Manager. O exemplo a seguir cria uma API do Gremlin do Azure Cosmos DB de uma [modelo de início rápido do Azure](https://aka.ms/gremlin-arm-qs). Este modelo criará uma conta do Azure Cosmos para a API do Gremlin com dois gráficos que compartilham uma taxa de transferência de 400 RU/s no nível do banco de dados.

Veja uma cópia do modelo:

[!code-json[create-cosmos-gremlin](~/quickstart-templates/101-cosmosdb-gremlin/azuredeploy.json)]

## <a name="deploy-with-azure-cli"></a>Implantar com a CLI do Azure

Para implantar o modelo do Resource Manager usando a CLI do Azure, **cópia** script e selecione **Experimente** para abrir o Azure Cloud shell. Para colar o script, o shell e, em seguida, selecione **colar**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first graph name: ' graph1Name
read -p 'Enter the second graph name: ' graph2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   graph1Name=$graph1Name graph2Name=$graph2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

O `az cosmosdb show` comando mostra a conta recém-criada do Azure Cosmos depois que ele foi provisionado. Se você optar por usar uma versão do CLI do Azure instalada localmente em vez de usar CloudShell, consulte [Interface de linha de comando do Azure (CLI)](/cli/azure/) artigo.

No exemplo anterior, você referenciou um modelo que é armazenado no GitHub. Você também pode baixar o modelo em seu computador local ou criar um novo modelo e especifique o caminho local com o `--template-file` parâmetro.

## <a name="next-steps"></a>Próximas etapas

Estes são alguns recursos adicionais:

- [Documentação do Azure Resource Manager](/azure/azure-resource-manager/)
- [Esquema do provedor de recursos do Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelos de início rápido do BD Cosmos do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Solucionar problemas de erros comuns de implantação do Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)