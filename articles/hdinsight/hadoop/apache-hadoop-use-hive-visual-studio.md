---
title: Ferramentas do Apache Hive com Data Lake (Apache Hadoop) para Visual Studio – Azure HDInsight
description: Saiba como usar as ferramentas de Data Lake para Visual Studio para executar consultas do Apache Hive com Apache Hadoop no Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 3a2e81234702e1fcff0349a14a4bc2852d257ad6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64686179"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Executar consultas do Apache Hive usando as ferramentas do Data Lake para Visual Studio

Saiba como usar as ferramentas do Data Lake para Visual Studio para consultar o Apache Hive. As ferramentas de Data Lake permitem que você facilmente crie, envie e monitore consultas de Hive para Apache Hadoop no Azure HDInsight.

## <a id="prereq"></a>Pré-requisitos

* Um cluster Azure HDInsight (Apache Hadoop no HDInsight)

  > [!IMPORTANT]  
  > O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Visual Studio (uma das versões a seguir):

    * Visual Studio 2013 Community/Professional/Premium/Ultimate com Atualização 4

    * Visual Studio 2015 (qualquer edição)

    * Visual Studio 2017 (qualquer edição)

* Ferramentas do HDInsight para Visual Studio ou ferramentas do Azure Data Lake para Visual Studio. Confira [Começar a usar as ferramentas Hadoop para HDInsight do Visual Studio](apache-hadoop-visual-studio-tools-get-started.md) para obter informações sobre como instalar e configurar as ferramentas.

## <a id="run"></a> Executar consultas Apache Hive usando o Visual Studio

1. Abra o **Visual Studio** e selecione **Novo** > **Projeto** > **Azure Data Lake** > **HIVE** > **Aplicativo Hive**. Forneça um nome para esse projeto.

2. Abra o arquivo **Script.hql** criado com esse projeto e cole as seguintes instruções HiveQL:

   ```hiveql
   set hive.execution.engine=tez;
   DROP TABLE log4jLogs;
   CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
   STORED AS TEXTFILE LOCATION '/example/data/';
   SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   ```

    Essas instruções executam as seguintes ações:

   * `DROP TABLE`: Se a tabela existir, esta instrução a excluirá.

   * `CREATE EXTERNAL TABLE`: Cria uma nova tabela ‘externa’ no Hive. As tabelas externas armazenam apenas a definição da tabela no Hive (os dados são mantidos no local original).

     > [!NOTE]  
     > As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa. Por exemplo, um trabalho de MapReduce ou serviço do Azure.
     >
     > Remover uma tabela externa **não** exclui os dados, somente a definição de tabela.

   * `ROW FORMAT`: Informa ao Hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.

   * `STORED AS TEXTFILE LOCATION`: Informa ao Hive o local em que os dados são armazenados no diretório de exemplo/de dados e que eles estão armazenados como texto.

   * `SELECT`: Seleciona uma contagem de todas as linhas, nas quais a coluna `t4` contém o valor `[ERROR]`. Essa instrução retorna um valor de `3`, já que há três linhas que contêm esse valor.

   * `INPUT__FILE__NAME LIKE '%.log'`: informa ao Hive que só devemos retornar dados de arquivos que terminam em .log. Essa cláusula restringe a pesquisa para o arquivo sample.log que contém os dados.

3. Na barra de ferramentas, selecione o **Cluster HDInsight** que você deseja usar nessa consulta. Selecione **Enviar** para executar as instruções como um trabalho do Hive.

   ![Barra de envio](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

4. O **Resumo do Trabalho do Hive** aparecerá e exibirá informações sobre o trabalho em execução. Use o link **Atualizar** para atualizar as informações do trabalho, até o **Status do Trabalho** ser alterado para **Concluído**.

   ![resumo do trabalho exibindo um trabalho concluído](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

5. Use o link **Saída de Trabalho** para exibir a saída desse trabalho. Ele exibe `[ERROR] 3`, que é o valor retornado por essa consulta.

6. Você também pode executar consultas Hive sem criar um projeto. Usando o **Gerenciador de Servidores**, expanda **Azure** > **HDInsight**, clique com o botão direito do mouse no seu servidor do HDInsight e selecione **Escrever uma Consulta de Hive**.

7. No documento **temp.hql** que aparece, adicione as seguintes instruções HiveQL:

   ```hiveql
   set hive.execution.engine=tez;
   CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
   INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   ```

    Essas instruções executam as seguintes ações:

   * `CREATE TABLE IF NOT EXISTS`: Crie uma tabela, se ela ainda não existir. Uma vez que a palavra-chave `EXTERNAL` não é usada, essa instrução cria uma tabela interna. As tabelas internas são armazenadas no data warehouse do Hive e gerenciadas por ele.

     > [!NOTE]  
     > Ao contrário das tabelas `EXTERNAL`, o descarte de uma tabela interna excluirá também os dados subjacentes.

   * `STORED AS ORC`: Armazena os dados no formato colunar de linha otimizado (ORC). Esse é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive.

   * `INSERT OVERWRITE ... SELECT`: Seleciona linhas da tabela `log4jLogs` que contêm `[ERROR]` e, então, insere os dados na tabela `errorLogs`.

8. Na barra de ferramentas, selecione no menu suspenso de **Enviar** para executar o trabalho. Use o **Status do Trabalho** para determinar se o trabalho foi concluído com êxito.

9. Para verificar se o trabalho criou a tabela, use o **Gerenciador de Servidores** e expanda **Azure** > **HDInsight** > seu cluster HDInsight > **Bancos de Dados do Hive** > **padrão**. As tabelas **errorLogs** e **log4jLogs** são listadas.

## <a id="nextsteps"></a>Próximas etapas

Como você pode ver, as ferramentas do HDInsight para o Visual Studio fornecem uma maneira fácil de trabalhar com as consultas do Hive no HDInsight.

Para obter informações gerais sobre o Hive no HDInsight:

* [Use o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Use o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)

* [Usar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Para obter mais informações sobre as ferramentas do HDInsight para o Visual Studio:

* [Introdução às ferramentas do HDInsight para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
