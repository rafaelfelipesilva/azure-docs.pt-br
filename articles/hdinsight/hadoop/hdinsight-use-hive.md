---
title: O que é o Apache Hive e HiveQL - Azure HDInsight?
description: O Apache Hive é um sistema de data warehouse para Apache Hadoop. Você pode consultar dados armazenados no Hive usando o HiveQL, que é semelhante ao Transact-SQL. Neste documento, você aprenderá a usar o Hive e HiveQL com o Azure HDInsight.
keywords: hiveql,o que é o hive,hadoop hiveql,como usar o hive,aprender hive,o que é o hive
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/26/2019
ms.openlocfilehash: fbf10e992843d9297363398ca5ea13b8e0aa10a6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64715633"
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Saiba mais sobre o Apache Hive e o HiveQL no Azure HDInsight?

[Apache Hive](https://hive.apache.org/) é um sistema de data warehouse para Apache Hadoop. O Hive permite o resumo de dados, consultas e análise de dados. Consultas de hive são escritas em HiveQL, que é uma linguagem de consulta semelhante ao SQL.

O Hive permite que você projete estrutura em grandes volumes de dados sem estrutura. Depois de definir a estrutura, você pode usar o HiveQL para consultar os dados sem conhecimento de Java ou do MapReduce.

O HDInsight fornece vários tipos de cluster, que são ajustados para cargas de trabalho específicas. Os seguintes tipos de cluster geralmente são usados para consultas de Hive:

* __Consulta Interativa__: Um cluster Hadoop que fornece funcionalidade [LLAP (Processamento Analítico de Baixa Latência)](https://cwiki.apache.org/confluence/display/Hive/LLAP) para melhorar os tempos de resposta de consultas interativas. Para obter mais informações, confira o documento [Introdução à Consulta Interativa no HDInsight](../interactive-query/apache-interactive-query-get-started.md).

* __Hadoop__: Um cluster Hadoop que está ajustado para cargas de trabalho de processamento em lotes. Para obter mais informações, confira o documento [Introdução ao Apache Hadoop no HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md).

* __Spark__: O Apache Spark tem funcionalidade interna para trabalhar com o Hive. Para obter mais informações, confira o documento [Introdução ao Apache Spark no HDInsight](../spark/apache-spark-jupyter-spark-sql.md).

* __HBase__: O HiveQL pode ser usado para consultar dados armazenados no Apache HBase. Para obter mais informações, confira o documento [Introdução ao Apache HBase no HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md).

## <a name="how-to-use-hive"></a>Como usar o Hive

Use a tabela a seguir para descobrir diferentes formas de usar o Hive com HDInsight:

| **Use esse método** se você quiser... | ...consultas **interativas** | ...Processamento em**lotes** | ... desse **sistema operacional cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [Ferramentas do HDInsight para Visual Studio Code](../hdinsight-for-vscode.md) |✔ |✔ | Linux, Unix, Mac OS X ou Windows |
| [Ferramentas do HDInsight para Visual Studio](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ | Windows |
| [Exibição de Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Qualquer um (baseado em navegador) |
| [Cliente de Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux, Unix, Mac OS X ou Windows |
| [API REST](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux, Unix, Mac OS X ou Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ | Windows |


## <a name="hiveql-language-reference"></a>Referência da linguagem HiveQL

A referência da linguagem HiveQL está disponível no [manual de linguagem (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Hive e estrutura de dados

O Hive sabe como trabalhar com dados estruturados e semi-estruturados. Por exemplo, arquivos de texto onde os campos são delimitados por caracteres específicos. A seguinte instrução do HiveQL cria uma tabela com dados delimitados por espaços:

```hiveql
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
```

O Hive também dá suporte a **serializador/desserializadores (SerDe)** personalizados para dados complexos ou com estrutura irregular. Para saber mais, consulte o documento [Como usar SerDe JSON personalizado com o HDInsight](https://web.archive.org/web/20190217104719/ https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

Para obter mais informações sobre formatos de arquivo compatíveis com o Hive, consulte o [Manual de linguagem (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

### <a name="hive-internal-tables-vs-external-tables"></a>Tabelas internas Hive vs. tabelas externas

Há dois tipos de tabelas que você pode criar com o Hive:

* __Interna__: Os dados são armazenados no data warehouse do Hive. O data warehouse está localizado no `/hive/warehouse/` no armazenamento padrão para o cluster.

    Use tabelas internas quando uma das condições a seguir se aplicar:

    * Os dados são temporários.
    * Você quer que o Hive gerencie o ciclo de vida da tabela e dos dados.

* __Externa__: Os dados são armazenados fora do data warehouse. Os dados podem ser armazenados em qualquer armazenamento acessível pelo cluster.

    Use tabelas externas quando uma das condições a seguir se aplicar:

    * Os dados também são usados fora do Hive. Por exemplo, os arquivos de dados são atualizados por outro processo (que não bloqueia os arquivos.)
    * Os dados devem permanecer no local anterior, mesmo depois de descartar a tabela.
    * Você precisa de um local personalizado, como uma conta de armazenamento não padrão.
    * Um programa que não seja o hive gerencia o formato de dados, local etc.

Para obter mais informações, confira a publicação do blog [Introdução às tabelas internas e externas do Hive][cindygross-hive-tables].

## <a name="user-defined-functions-udf"></a>UDF (Funções definidas pelo usuário)

O Hive também pode ser estendido por meio de **UDF (funções definidas pelo usuário)**. As UDF permitem que você implemente funcionalidade ou lógica que não é facilmente modelada em HiveQL. Para obter um exemplo de uso de UDFs com o Hive, confira os seguinte documentos:

* [Usar uma função definida pelo usuário do Java com o Apache Hive](../hadoop/apache-hadoop-hive-java-udf.md)

* [Usar uma função definida pelo usuário do Python com o Apache Hive](../hadoop/python-udf-hdinsight.md)

* [Usar uma função definida C# pelo usuário do Java com o Apache Hive](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Como adicionar uma função definida pelo usuário personalizado de Apache Hive no HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Um exemplo de função definida pelo usuário do Apache Hive para converter formatos de data/hora para carimbo de data/hora do Hive](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a id="data"></a>Dados de exemplo

O Hive no HDInsight vem pré-carregado com uma tabela interna chamada `hivesampletable`. O HDInsight também fornece conjuntos de dados de exemplo que podem ser usados com o Hive. Esses conjuntos de dados são armazenados nos diretórios `/example/data` e `/HdiSamples`. Esses diretórios estão no armazenamento padrão do cluster.

## <a id="job"></a>Exemplo de consulta do Hive

As seguintes instruções HiveQL projetam colunas para o arquivo `/example/data/sample.log`:

```hiveql
DROP TABLE log4jLogs;
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs 
    WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' 
    GROUP BY t4;
```

No exemplo anterior, as instruções HiveQL executam as seguintes ações:


* `DROP TABLE`: Se a tabela já existir, exclua-a.

* `CREATE EXTERNAL TABLE`: Cria uma nova tabela **externa** no Hive. Tabelas externas só armazenam a definição da tabela no Hive. Os dados são mantidos no local e no formato originais.

* `ROW FORMAT`: Informa ao Hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.

* `STORED AS TEXTFILE LOCATION`: Informa ao Hive o local em que os dados são armazenados (o diretório `example/data`) e se são armazenados como texto. Os dados podem estar em um arquivo ou distribuídos em vários arquivos dentro do diretório.

* `SELECT`: Seleciona uma contagem de todas as linhas nas quais a coluna **t4** contém o valor **[ERROR]**. Essa instrução retorna um valor de **3**, já que há três linhas que contêm esse valor.

* `INPUT__FILE__NAME LIKE '%.log'`- O Hive tenta aplicar o esquema a todos os arquivos no diretório. Nesse caso, o diretório contém arquivos que não correspondem ao esquema. Para evitar dados incorretos nos resultados, essa instrução informa ao Hive que devemos retornar apenas dados de arquivos que terminam em .log.

> [!NOTE]  
> As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa. Por exemplo, um processo de upload de dados automatizado ou uma operação MapReduce.
>
> Descartar uma tabela externa **não** exclui os dados, apenas a definição da tabela.

Para criar uma tabela **interna** em vez de externa, use o HiveQL a seguir:

```hiveql
CREATE TABLE IF NOT EXISTS errorLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
STORED AS ORC;
INSERT OVERWRITE TABLE errorLogs
SELECT t1, t2, t3, t4, t5, t6, t7 
    FROM log4jLogs WHERE t4 = '[ERROR]';
```

Essas instruções executam as seguintes ações:

* `CREATE TABLE IF NOT EXISTS`: Se a tabela não existir, crie uma. Uma vez que a palavra-chave **EXTERNA** não é usada, essa instrução cria uma tabela interna. Uma tabela interna é armazenada no data warehouse do Hive e é totalmente gerenciada pelo Hive.

* `STORED AS ORC`: Armazena os dados no formato OCR (Optimized Row Columnar). Esse é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive.

* `INSERT OVERWRITE ... SELECT`: Seleciona as linhas na tabela **log4jLogs** que contém **[ERROR]** e insere os dados na tabela **errorLogs**.

> [!NOTE]  
> Diferentemente de tabelas externas, o descarte de uma tabela interna excluirá também os dados subjacentes.

## <a name="improve-hive-query-performance"></a>Como melhorar o desempenho de consulta de Hive

### <a id="usetez"></a>Apache Tez

[Apache Tez](https://tez.apache.org) é uma estrutura que permite que aplicativos com uso intenso de dados como o Hive executem de maneira muito mais eficiente em escala. O Tez está habilitado por padrão.  Os [documentos de design do Apache Hive no Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) contêm vários detalhes das escolhas de implantação e configurações de ajuste.

### <a name="low-latency-analytical-processing-llap"></a>Processamento analítico de baixa latência (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (também conhecido como vida longa e processo) é um novo recurso no Hive 2.0 que permite armazenar as consultas em cache na memória. O LLAP acelera as consultas de Hive em até [26 vezes mais rápido do que o Hive 1.x em alguns casos](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

O HDInsight fornece LLAP no tipo de cluster da Consulta Interativa. Para obter mais informações, confira o documento [Introdução à Consulta Interativa](../interactive-query/apache-interactive-query-get-started.md).

## <a name="scheduling-hive-queries"></a>Programação de consultas de Hive

Há vários serviços que podem ser usados para executar consultas de Hive como parte de um fluxo de trabalho programado ou sob demanda.

### <a name="azure-data-factory"></a>Fábrica de dados do Azure

O Azure Data Factory permite que você use o HDInsight como parte de um pipeline do Data Factory. Para obter mais informações sobre como usar o Hive de um pipeline, consulte o documento [Transformar dados usando a atividade do Hive no Azure Data Factory](../../data-factory/transform-data-using-hadoop-hive.md).

### <a name="hive-jobs-and-sql-server-integration-services"></a>Trabalhos do Hive e SQL Server Integration Services

Também é possível usar o SSIS (SQL Server Integration Services) para executar um trabalho do Hive. O Feature Pack do Azure para SSIS fornece os seguintes componentes que funcionam com trabalhos do Hive no HDInsight.

* [Tarefa do Hive do Azure HDInsight][hivetask]

* [Gerenciador de Conexões de Assinatura do Azure][connectionmanager]

Para obter mais informações, consulte a documentação [Pacote de Recursos do Azure][ssispack].

### <a name="apache-oozie"></a>Apache Oozie

O Apache Oozie é um sistema de fluxo de trabalho e coordenação que gerencia trabalhos do Hadoop. Para obter mais informações sobre como usar Oozie com o Hive, consulte o documento [Usar o Apache Oozie para definir e executar um fluxo de trabalho](../hdinsight-use-oozie-linux-mac.md).

## <a id="nextsteps"></a>Próximas etapas

Agora que você aprendeu a usar a transmissão de trabalhos do MapReduce com o HDInsight, use os links abaixo para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Carregar dados no HDInsight][hdinsight-upload-data]
* [Usar Apache Pig com o HDInsight][hdinsight-use-pig]
* [Usar trabalhos do MapReduce com o HDInsight][hdinsight-use-mapreduce]

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: https://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: https://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: https://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx


[cindygross-hive-tables]: https://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
