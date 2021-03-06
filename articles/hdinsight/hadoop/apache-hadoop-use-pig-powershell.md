---
title: Usar o Apache Pig com PowerShell no HDInsight - Azure
description: Saiba como enviar trabalhos do Pig Apache para um cluster do Apache Hadoop no HDInsight usando o Azure PowerShell.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 9ad788989273f28f38ee95f8d669fdf17f1fd785
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64725683"
---
# <a name="use-azure-powershell-to-run-apache-pig-jobs-with-hdinsight"></a>Usar o Azure PowerShell para executar trabalhos do Apache Pig com o HDInsight

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Este documento fornece um exemplo de como usar o Azure PowerShell para enviar trabalhos do Apache Pig para um Apache Hadoop no cluster HDInsight. O Pig permite que você escreva trabalhos do MapReduce usando uma linguagem (Pig Latin) que modela transformações de dados, em vez das funções mapear e reduzir.

> [!NOTE]  
> Esse documento não fornece uma descrição detalhada do que fazem as instruções Pig Latin usadas nos exemplos. Para obter informações sobre o Pig Latin usado neste exemplo, consulte [Usar Apache Pig com Apache Hadoop no HDInsight](hdinsight-use-pig.md).

## <a id="prereq"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* **Um cluster Azure HDInsight**

  > [!IMPORTANT]  
  > O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Uma estação de trabalho com o PowerShell do Azure.**

## <a id="powershell"></a>Executar um trabalho do Apache Pig

O PowerShell do Azure fornece *cmdlets* que permitem executar remotamente trabalhos do Pig no HDInsight. Internamente, o PowerShell usa chamadas REST para [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) em execução no cluster HDInsight.

Os seguintes cmdlets são usados ao executar trabalhos do Pig em um cluster HDInsight remoto:

* **Connect-AzAccount**: Autentica o Azure PowerShell à Assinatura do Azure.
* **New-AzHDInsightPigJobDefinition**: Cria uma *definição de trabalho*, usando as instruções do Pig Latin especificadas.
* **Start-AzHDInsightJob**: Envia a definição de trabalho para HDInsight e inicia o trabalho. Um objeto *job* é retornado.
* **Wait-AzHDInsightJob**: Usa o objeto de trabalho para verificar o status do trabalho. Ele aguarda até que o trabalho seja concluído ou que o tempo de espera seja excedido.
* **Get-AzHDInsightJobOutput**: Usado para recuperar a saída do trabalho.

As etapas a seguir demonstram como usar esses cmdlets para executar um trabalho no seu cluster HDInsight.

1. Usando um editor, salve o código a seguir como **pigjob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-pig/use-pig.ps1?range=5-51)]

1. Abra um novo prompt de comando do Windows PowerShell. Altere os diretórios para o local do arquivo **pigjob.ps1** e use o seguinte comando para executar o script:

        .\pigjob.ps1

    Você deverá fazer logon em sua assinatura do Azure. Em seguida, você deverá fornecer o nome da conta e senha de Administrador/HTTPs do cluster HDInsight.

2. Quando o trabalho for concluído, ele deverá retornar informações semelhantes ao seguinte texto:

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="troubleshooting"></a>Solucionar problemas

Se nenhuma informação for retornada quando o trabalho for concluído, exiba os logs de erro. Para exibir informações de erro para esse trabalho, adicione o seguinte ao final do arquivo **pigjob.ps1** , salve o arquivo e execute-o novamente.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Esse cmdlet retorna as informações gravadas em STDERR durante o processamento do trabalho.

## <a id="summary"></a>Resumo
Como você pode ver, o PowerShell do Azure fornece uma maneira fácil de executar trabalhos do Pig em um cluster HDInsight, monitorar o status do trabalho e recuperar a saída.

## <a id="nextsteps"></a>Próximas etapas
Para obter informações gerais sobre o Pig no HDInsight:

* [Use o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Use o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)
* [Usar MapReduce com Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)
