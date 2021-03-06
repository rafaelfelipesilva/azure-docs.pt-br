---
title: Aprender a gerenciar contas de banco de dados no Azure Cosmos DB
description: Aprender a gerenciar contas de banco de dados no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: b2b5e58ca480aa3abaa0766319977b8d1160ebeb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59282994"
---
# <a name="manage-an-azure-cosmos-account"></a>Gerenciar uma conta do Azure Cosmos

Este artigo descreve como gerenciar sua conta do Azure Cosmos. Você aprenderá a instalar uma hospedagem múltipla, adicionar ou remover uma região, configurar várias regiões de gravação e definir prioridades de failover. 

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

### <a id="create-database-account-via-portal"></a>Portal do Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

```bash
# Create an account
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group Name>
```

## <a name="configure-clients-for-multi-homing"></a>Configurar clientes para hospedagem múltipla

### <a id="configure-clients-multi-homing-dotnet"></a>SDK do .NET v2

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");

// Pass the connection policy with the preferred locations on it to the client.
DocumentClient client = new DocumentClient(new Uri(this.accountEndpoint), this.accountKey, policy);
```

### <a id="configure-clients-multi-homing-dotnet-v3"></a>SDK do .NET v3 (versão prévia)

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

### <a id="configure-clients-multi-homing-java-async"></a>SDK de Java Assíncrono

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

### <a id="configure-clients-multi-homing-javascript"></a>SDK de Node.js/JavaScript/TypeScript

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

### <a id="configure-clients-multi-homing-python"></a>SDK do Python

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="addremove-regions-from-your-database-account"></a>Adicionar ou remover regiões de sua conta de banco de dados

### <a id="add-remove-regions-via-portal"></a>Portal do Azure

1. Vá até sua conta do Azure Cosmos e abra o menu **Replicar dados globalmente**.

2. Para adicionar regiões, marque os hexágonos no mapa com o rótulo **+** que corresponde às regiões desejadas. Como alternativa, para adicionar uma região, escolha a opção **+Adicionar região** e escolha uma região no menu suspenso.

3. Para remover regiões, desmarque uma ou mais regiões do mapa marcando os hexágonos azuis com marcas de seleção. Ou escolha o ícone de "cesto de lixo" (🗑) ao lado da região no canto direito.

4. Para salvar suas alterações, escolha **OK**.

   ![Adicionar ou remover o menu de regiões](./media/how-to-manage-database-account/add-region.png)

Em um modo de gravação de região única, não é possível remover a região de gravação. Você deverá realizar o failover para uma região diferente antes de excluir a região de gravação atual.

Em um modo de gravação de várias regiões, você poderá adicionar ou remover qualquer região, se você tiver pelo menos uma região.

### <a id="add-remove-regions-via-cli"></a>Azure CLI

```bash
# Create an account with 1 region
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations eastus=0

# Add a region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations eastus=0 westus=1

# Remove a region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations westus=0
```

## <a name="configure-multiple-write-regions"></a>Configurar várias regiões de gravação

### <a id="configure-multiple-write-regions-portal"></a>Portal do Azure

Quando você cria uma conta de banco de dados, verifique se a opção **Gravação em Várias Regiões** está habilitada.

![Captura de tela de criação de conta do Azure Cosmos](./media/how-to-manage-database-account/account-create.png)

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

```bash
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-arm"></a>modelo do Resource Manager

O código JSON a seguir é um modelo do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) de exemplo. Você pode usá-lo para implantar uma conta do Azure Cosmos com [nível de consistência de desatualização limitada](consistency-levels.md). O intervalo máximo de desatualização é definido como 5 segundos. O número máximo de solicitações obsoletas toleradas é definido como 100. Para saber mais sobre o formato e a sintaxe do modelo do Resource Manager e a sintaxe, confira [Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "locationName": {
            "type": "String"
        },
        "defaultExperience": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {
                "defaultExperience": "[parameters('defaultExperience')]"
            },
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "BoundedStaleness",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "[concat(parameters('name'), '-', parameters('location'))]",
                        "failoverPriority": 0,
                        "locationName": "[parameters('locationName')]"
                    }
                ],
                "isVirtualNetworkFilterEnabled": false,
                "enableMultipleWriteLocations": true,
                "virtualNetworkRules": [],
                "dependsOn": []
            }
        }
    ]
}
```


## <a id="manual-failover"></a>Habilitar failover manual para a conta do Azure Cosmos

### <a id="enable-manual-failover-via-portal"></a>Portal do Azure

1. Vá até sua conta do Azure Cosmos e abra o menu **Replicar dados globalmente**.

2. Na parte superior do menu, escolha **Failover Manual**.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No menu **Failover Manual**, escolha a nova região de gravação. Marque a caixa de seleção para indicar que você entende que essa opção alterará sua região de gravação.

4. Para disparar o failover, escolha **OK**.

   ![Menu do portal de failover manual](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

```bash
# Given your account currently has regions with priority: eastus=0 westus=1
# Change the priority order to trigger a failover of the write region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations westus=0 eastus=1
```

## <a id="automatic-failover"></a>Habilitar o failover automático para a conta do Azure Cosmos DB

### <a id="enable-automatic-failover-via-portal"></a>Portal do Azure

1. Na sua conta do Azure Cosmos DB, abra o painel **Replicar dados globalmente**. 

2. Escolha **Failover Automático** na parte superior do painel.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No painel **Failover Automático**, verifique se a opção **Habilitar Failover Automático** está definida como **ATIVADO**. 

4. Clique em **Salvar**.

   ![Menu do portal Failover automático](./media/how-to-manage-database-account/automatic-failover.png)

Você também pode definir as prioridades de failover nesse menu.

### <a id="enable-automatic-failover-via-cli"></a>Azure CLI

```bash
# Enable automatic failover on account creation
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Enable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Disable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover false
```

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Definir prioridades de failover para a conta do Azure Cosmos

### <a id="set-failover-priorities-via-portal"></a>Portal do Azure

1. Na sua conta do Azure Cosmos, abra o painel **Replicar dados globalmente**. 

2. Escolha **Failover Automático** na parte superior do painel.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No painel **Failover Automático**, verifique se a opção **Habilitar Failover Automático** está definida como **ATIVADO**. 

4. Para modificar a prioridade de failover, arraste as regiões de leitura nos três pontos à esquerda da linha que aparecem quando o mouse é passado sobre ela. 

5. Clique em **Salvar**.

   ![Menu do portal Failover automático](./media/how-to-manage-database-account/automatic-failover.png)

Não é possível modificar a região de gravação nesse menu. Para alterar a região de gravação manualmente você deve fazer um failover manual.

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

```bash
# Assume region order is initially eastus=0 westus=1 automatic failover on account creation
az cosmosdb failover-priority-change --name <Azure Cosmos account name> --resource-group <Resource Group name> --failover-policies westus=0 eastus=1
```

## <a name="next-steps"></a>Próximas etapas

Leia os seguintes artigos:

* [Gerenciar a consistência](how-to-manage-consistency.md)
* [Gerenciar conflitos entre regiões](how-to-manage-conflicts.md)
* [Distribuição global – nos bastidores](global-dist-under-the-hood.md)
* [Como configurar vários mestres em seus aplicativos](how-to-multi-master.md)
* [Configurar clientes para multihoming](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Como adicionar/remover regiões da conta do Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Criar uma política de resolução de conflitos personalizada](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)

