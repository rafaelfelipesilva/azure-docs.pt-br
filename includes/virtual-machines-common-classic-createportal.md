---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 361d0ce5091d80198d47e4ad164f7cba8e21a55d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485260"
---
Uma máquina virtual *personalizada* significa simplesmente uma máquina virtual que você cria usando um **Aplicativo em destaque** do **Marketplace** porque ele realiza grande parte do trabalho para você. Ainda assim, você pode fazer escolhas de configuração que incluem os seguintes itens:

* Conectar a máquina virtual a uma rede virtual.
* Instalar o agente de máquina Virtual do Azure e extensões de máquina Virtual do Azure, como para antimalware.
* Adicionar a máquina virtual a serviços de nuvem existentes.
* Adicionar a máquina virtual a uma conta de armazenamento existente.
* Adicionar a máquina virtual a um conjunto de disponibilidade.

<!--
> [!IMPORTANT]
> If you want your virtual machine to use a virtual network so you can connect to it directly by host name or set up cross-premises connections, make sure that you specify the virtual network when you create the virtual machine. A virtual machine can be configured to join a virtual network only when you create the virtual machine. For details on virtual networks, see [Azure Virtual Network overview](../articles/virtual-network/virtual-networks-overview.md).
>
>
 -->

> [!IMPORTANT]
> Se deseja que sua máquina virtual use uma rede virtual, especifique a rede virtual ao criar a máquina virtual.
> * Dois benefícios do uso de uma rede virtual são: conectar-se diretamente à máquina virtual e configurar conexões entre instalações.
> * Uma máquina virtual pode ser configurada para ingressar em uma rede virtual somente quando você criar a máquina virtual. Para mais detalhes sobre redes virtuais, consulte a seção [Visão geral da rede virtual do Azure](../articles/virtual-network/virtual-networks-overview.md).
>
>

## <a name="to-create-the-virtual-machine"></a>Para criar a máquina virtual

<!-- Update_Description: update meta properties -->