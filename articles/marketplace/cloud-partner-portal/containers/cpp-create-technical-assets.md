---
title: Criar imagem de contêineres do Azure ativos técnicos | O Azure Marketplace
description: Crie os recursos técnicos para um contêiner do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: 32002f8b88a2fbeba29f15b3b9289ecf3d7560d0
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942726"
---
# <a name="prepare-your-container-technical-assets"></a>Prepare seus recursos técnicos de contêiner

Este artigo descreve as etapas e requisitos para configurar uma oferta de contêiner do Azure Marketplace.

## <a name="before-you-begin"></a>Antes de começar

Revise a documentação [Instâncias de Contêiner do Azure](https://docs.microsoft.com/azure/container-instances), que fornece o Quickstarts, Tutoriais e Amostras.

## <a name="fundamental-technical-knowledge"></a>Conhecimento técnico fundamental

Projetar, criar e testar esses recursos leva tempo e exige conhecimento técnico da plataforma do Azure e das tecnologias usadas para criar a oferta.
 
Além do domínio da solução, a equipe de engenharia deverá ter conhecimento das seguintes tecnologias Microsoft:

-   Noções básicas sobre os [Serviços do Azure](https://azure.microsoft.com/services/) 
-   Como [projetar e arquitetar aplicativos do Azure](https://azure.microsoft.com/solutions/architecture/)
-   Conhecimento prático de [Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [Armazenamento do Microsoft Azure](https://azure.microsoft.com/services/?filter=storage) e [Rede do Azure](https://azure.microsoft.com/services/?filter=networking)
-   Conhecimento de prático do [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Conhecimento prático de [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Ferramentas sugeridas

Escolha um ou ambos dos seguintes ambientes de script para ajudar a gerenciar sua imagem do contêiner:

-   [PowerShell do Azure](https://docs.microsoft.com/powershell/azure/overview)
-   [CLI do Azure](https://docs.microsoft.com/cli/azure)

Além disso, é recomendável adicionar as seguintes ferramentas ao ambiente de desenvolvimento:

-   [Gerenciador de Armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Extensão: [Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Extensão: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Também é recomendável que você revise as ferramentas disponíveis na página [Ferramentas para Desenvolvedores do Azure](https://azure.microsoft.com/tools/) e, se estiver usando o Visual Studio, o [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Crie a imagem do contêiner

- Crie e configure o disco rígido virtual (VHD) para sua máquina virtual de contêiner (VM). Este VHD contém o sistema operacional (Windows, Linux ou Ubuntu) para o contêiner. Discos de dados adicionais podem ser necessários.
- Configure o sistema operacional da VM, o tamanho da VM, as portas a serem abertas e os discos de dados conectados.
- Instale o aplicativo e outro software necessário para sua oferta. Por exemplo: software de banco de dados, software de terceiros ou um aplicativo personalizado.

## <a name="next-steps"></a>Próximas etapas

[Criar sua oferta de contêiner](./cpp-create-offer.md)
