---
title: Exemplo do PowerShell para detecção de ameaças e auditoria do Banco de Dados SQL do Azure | Microsoft Docs
description: Script de exemplo do Azure PowerShell para configurar a detecção de ameaças e auditoria em um Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: security
ms.devlang: PowerShell
ms.topic: sample
author: ronitr
ms.author: ronitr
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: a3117f2cd75ae2d85b5241fee87f6d5940e8e116
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59356847"
---
# <a name="use-powershell-to-configure-sql-database-auditing-and-threat-detection"></a>Use o PowerShell para configurar a detecção de ameaças e auditoria do Banco de Dados SQL

Este exemplo de script do PowerShell configura a detecção de ameaças e auditoria do Banco de Dados SQL.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o AZ PowerShell 1.4.0 ou posterior. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/database-auditing-and-threat-detection/database-auditing-and-threat-detection.ps1?highlight=15-16 "Configure auditing and threat detection")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor do Banco de Dados SQL que hospeda um banco de dados individual ou um pool elástico. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria um banco de dados individual ou um pool elástico. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Cria uma conta de armazenamento. |
| [Set-AzSqlDatabaseAuditing](/powershell/module/az.sql/set-azsqldatabaseauditing) | Define a política de auditoria para um banco de dados. |
| [Set-AzSqlDatabaseThreatDetectionPolicy](/powershell/module/az.sql/set-azsqldatabasethreatdetectionpolicy) | Define uma política de detecção de ameaças em um banco de dados. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Outros exemplos de script do PowerShell para Banco de Dados SQL podem ser encontrados nos [scripts do PowerShell para Banco de Dados SQL do Azure](../sql-database-powershell-samples.md).
