---
title: Propriedades padrão nos registros de log do Azure Monitor | Microsoft Docs
description: Descreve as propriedades que são comuns a vários tipos de dados nos logs do Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/20/2019
ms.author: bwren
ms.openlocfilehash: c01cdb967fd7f9516b4403aa4f0c76f2577d5050
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60394516"
---
# <a name="standard-properties-in-azure-monitor-log-records"></a>Propriedades padrão nos registros de log do Azure Monitor
Os dados de log no Azure Monitor são [armazenados como um conjunto de registros](../log-query/log-query-overview.md), cada um com um determinado tipo de dados, com um conjunto exclusivo de propriedades. Muitos tipos de dados terão propriedades padrão comuns a vários tipos. Este artigo descreve essas propriedades e fornece exemplos de como você pode usá-las em consultas.

Algumas dessas propriedades ainda estão no processo de implementação, portanto, você poderá vê-las em alguns tipos de dados, mas não em outros.

## <a name="timegenerated"></a>TimeGenerated
A propriedade **TimeGenerated** contém a data e a hora em que o registro foi criado. Ela fornece uma propriedade comum a ser usada para filtrar ou para resumir pela hora. Quando você seleciona um intervalo de tempo para uma exibição ou um painel no portal do Azure, ele usa TimeGenerated para filtrar os resultados.

### <a name="examples"></a>Exemplos

A consulta a seguir retorna o número de eventos de erro criados em cada dia da semana anterior.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

## <a name="type"></a>Type
A propriedade **Type** contém o nome da tabela da qual o registro foi recuperado, que também pode ser considerada como o tipo de registro. Essa propriedade é útil em consultas que combinam registros de várias tabelas, como aqueles que usam o operador `search`, para distinguir entre registros de tipos diferentes. **$table** pode ser usado no lugar de **Type** em alguns locais.

### <a name="examples"></a>Exemplos
A consulta a seguir retorna a contagem de registros por tipo coletados na última hora.

```Kusto
search * 
| where TimeGenerated > ago(1h) 
| summarize count() by Type 
```

## <a name="resourceid"></a>\_ResourceId
A propriedade **\__ResourceId** contém um identificador exclusivo do recurso ao qual o registro está associado. Isso lhe dá uma propriedade padrão a ser usada para definir o escopo de sua consulta apenas aos registros de um recurso específico, ou para unir dados relacionados em várias tabelas.

Para recursos do Azure, o valor de **_ResourceId** é a [URL de ID de recurso do Azure](../../azure-resource-manager/resource-group-template-functions-resource.md). Atualmente, a propriedade está limitada aos recursos do Azure, mas será estendida a recursos fora do Azure, como em computadores locais.

> [!NOTE]
> Alguns tipos de dados já têm campos que contêm a ID de recurso do Azure ou, pelo menos, partes dele, como a ID da assinatura. Embora esses campos sejam mantidos para manter a compatibilidade com versões anteriores, é recomendável usar _ResourceId para executar a correlação cruzada, uma vez que ela é mais consistente.

### <a name="examples"></a>Exemplos
A consulta a seguir une dados de desempenho e de eventos de cada computador. Ele mostra todos os eventos com a ID _101_ e utilização do processador acima de 50%.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

A consulta a seguir une registros de _AzureActivity_ com registros de _SecurityEvent_. Ele mostra todas as operações de atividade com usuários que foram registradas nesses computadores.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

A consulta a seguir analisa **_ResourceId** e agregações cobrado volumes de dados por assinatura do Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

Use estas consultas `union withsource = tt *` com moderação como verificações em tipos de dados que são caros para executar.

## <a name="isbillable"></a>\_IsBillable
A propriedade **\_IsBillable** especifica se os dados ingeridos são faturáveis. Dados com **\_IsBillable** igual a _false_ são coletados de graça e não são cobrados da sua conta do Azure.

### <a name="examples"></a>Exemplos
Para obter uma lista de computadores que estão enviando os tipos de dados cobrados, use a seguinte consulta:

> [!NOTE]
> Use consultas com `union withsource = tt *` com moderação, pois a execução de exames entre diferentes tipos de dados é cara. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Isso pode ser estendido para retornar a contagem de computadores por hora que estão enviando tipos de dados cobrados:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="billedsize"></a>\_BilledSize
A propriedade **\_BilledSize** especifica o tamanho em bytes de dados que são cobrados na sua conta do Azure se **\_IsBillable** é true.

### <a name="examples"></a>Exemplos
Para ver os tamanho de eventos cobráveis ingeridos por computador, use a propriedade `_BilledSize` que fornece o tamanho em bytes:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Para ver a contagem de eventos ingeridos por computador, use a seguinte consulta:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

Para ver a contagem de eventos cobráveis ingeridos por computador, use a seguinte consulta: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Se você quiser ver as contagens dos tipos de dados faturáveis que estão enviando dados para um computador específico, use a seguinte consulta:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```


## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre como os [dados de log do Azure Monitor são armazenados](../log-query/log-query-overview.md).
- Obtenha uma lição sobre como [escrever consultas de log](../../azure-monitor/log-query/get-started-queries.md).
- Obtenha uma lição sobre como [unir tabelas em consultas de log](../../azure-monitor/log-query/joins.md).
