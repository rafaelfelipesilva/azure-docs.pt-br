---
title: Otimizar a coleta de estatísticas de consulta em um banco de dados do Azure para PostgreSQL – servidor único
description: Este artigo descreve como você pode otimizar a coleta de estatísticas de consulta em um banco de dados do Azure para PostgreSQL – servidor único
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 7425ee7916fd71625f336a7af35f6481d1ed2474
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068953"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>Otimizar a coleta de estatísticas de consulta em um banco de dados do Azure para PostgreSQL – servidor único
Este artigo descreve como otimizar a coleção de estatísticas de consulta em um servidor do Banco de Dados do Azure para PostgreSQL.

## <a name="use-pgstatsstatements"></a>Usar pg_stats_statements
**Pg_stat_statements** é uma extensão do PostgreSQL habilitada por padrão no Banco de Dados do Azure para PostgreSQL. A extensão fornece um modo para rastrear as estatísticas de execução de todas as instruções SQL executadas por um servidor. Esse módulo é acoplado a cada execução de consulta e é fornecido com um custo de desempenho não trivial. Habilitar **pg_stat_statements** força as gravações de texto de consulta em arquivos no disco.

Se você tiver consultas exclusivas com texto de consulta longo ou não monitorar ativamente **pg_stat_statements**, desabilite **pg_stat_statements** para melhorar o desempenho. Para fazer isso, altere a configuração para `pg_stat_statements.track = NONE`.

Algumas cargas de trabalho de clientes têm um aumento de desempenho de até 50% quando **pg_stat_statements** é desabilitado. Em compensação, quando você desabilita pg_stat_statements, perde a capacidade de solucionar problemas de desempenho.

Para definir `pg_stat_statements.track = NONE`:

- No portal do Azure, vá para a [página de gerenciamento de recursos do PostgreSQL e escolha a folha de parâmetros do servidor](howto-configure-server-parameters-using-portal.md).

  ![Folha de parâmetros do servidor PostgreSQL](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Use configuração de servidor az postgres da [CLI do Azure](howto-configure-server-parameters-using-cli.md) definida como `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="use-the-query-store"></a>Usar o Repositório de Consultas 
O recurso [Repositório de Consultas](concepts-query-store.md) no Banco de Dados do Azure para PostgreSQL fornece um método mais eficaz para rastrear as estatísticas da consulta. Recomendamos esse recurso como uma alternativa ao uso de *pg_stats_statements*. 

## <a name="next-steps"></a>Próximas etapas
Considere configurar `pg_stat_statements.track = NONE` no [portal do Azure](howto-configure-server-parameters-using-portal.md) ou usar a [CLI do Azure](howto-configure-server-parameters-using-cli.md).

Para obter mais informações, consulte: 
- [Cenários de uso do Repositório de Consultas](concepts-query-store-scenarios.md) 
- [Práticas recomendadas para o Repositório de Consultas](concepts-query-store-best-practices.md) 
