---
title: Configurar endereços IP para máquinas virtuais (Clássicas) - Portal do Azure | Microsoft Docs
description: Saiba como configurar endereços IP para máquinas virtuais (Clássico) usando o Portal do Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 72d1c4d2ea3adf7d8751adfbb013435f8f2530f0
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125739"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Configurar endereços IP particulares para uma máquina virtual (Clássica) usando o Portal do Azure

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo aborda o modelo de implantação clássico. Você também pode [gerenciar um endereço IP privado estático no modelo de implantação do Gerenciador de Recursos](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

As etapas de exemplo a seguir esperam um ambiente simples já criado. Se você quiser executar as etapas da forma como elas aparecem neste documento, primeiro crie o ambiente de teste descrito em [criar uma vnet](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Como especificar um endereço IP privado estático ao criar uma VM
Para criar uma VM denominada *DNS01* na sub-rede *FrontEnd* de uma VNet chamada *TestVNet* com o endereço IP privado estático *192.168.1.101*, conclua as seguintes etapas:

1. Em um navegador, navegue até https://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Selecione **NOVO** > **Computação** > **Windows Server 2012 R2 Datacenter**, observe que a lista **Selecionar um modelo de implantação** já mostra **Clássico** e, em seguida, selecione **Criar**.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. Em **Criar VM**, insira o nome da VM a ser criada (*DNS01* no cenário), a conta de administrador local e a senha.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Selecione **Configuração opcional** > **Rede** > **Rede Virtual**, e, em seguida, selecione **TestVNet**. Se **TestVNet** não estiver disponível, verifique se você está usando o *Centro dos EUA* como local e que você criou o ambiente de teste descrito no início deste artigo.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. Em **Rede**, certifique-se de que a sub-rede selecionada atualmente é *FrontEnd*, em seguida, selecione **Endereços IP**, em **Atribuição de endereço IP** selecione **Estático**, e, em seguida, digite *192.168.1.101* para **Endereço IP** conforme mostrado abaixo.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Selecione **OK** em **Endereços IP**, selecione **OK** em **Rede**e, em seguida, selecione **OK** em **Configuração opcional**.
7. Em **Criar VM**, selecione **Criar**. Observe o bloco abaixo exibido no painel:
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Como recuperar informações do endereço IP privado estático de uma VM
Para exibir as informações de endereço IP privado estático para a VM criada com as etapas acima, execute as etapas abaixo.

1. No Portal do Azure, selecione **PROCURAR TUDO** > **Máquinas virtuais (clássico)** > **DNS01** > **Todas as configurações** > **Endereços IP** e observe o endereço IP e a atribuição de endereço IP, conforme mostrado abaixo.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Como remover um endereço IP privado estático de uma VM

Em **Endereços IP**, selecione **Dinâmico** à direita da **Atribuição de endereço IP**, selecione **Salvar**e, em seguida, selecione **Sim**, conforme mostrado na figura a seguir:
   
    ![Create VM in Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Como adicionar um endereço IP privado estático a uma VM existente

1. Em **Endereços IP**, mostrado anteriormente, selecione **Estático** à direita de **Atribuição de endereço IP**.
2. Tipo *192.168.1.101* para **Endereço IP**, selecione **Salvar** e, em seguida, selecione **Sim**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Definir endereços IP no sistema operacional

É recomendável que você não atribua estaticamente o IP privado atribuído à máquina virtual do Azure no sistema operacional de uma VM, a menos que seja necessário. Se você definir manualmente o endereço IP privado no sistema operacional, verifique se é o mesmo endereço que o endereço IP privado atribuído à VM do Azure ou se é possível perder a conectividade com a máquina virtual. Nunca atribua manualmente o endereço IP público atribuído a uma máquina virtual do Azure no sistema operacional da máquina virtual.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre endereços [IP públicos reservados](virtual-networks-reserved-public-ip.md) .
* Saiba mais sobre endereços [ILPIP (IP público em nível de instância)](virtual-networks-instance-level-public-ip.md) .
* Consulte as [APIs REST de IP reservado](https://msdn.microsoft.com/library/azure/dn722420.aspx).

