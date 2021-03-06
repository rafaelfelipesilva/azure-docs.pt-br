---
title: Perguntas comuns - recuperação de desastre do VMware para Azure com o Azure Site Recovery | Microsoft Docs
description: Este artigo resume comuns perguntas fir recuperação de desastre de VMs do VMware locais para o Azure usando o Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 04/26/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 22d3bdf8c60e6682c360395b44fe6f1dcc1207b0
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925510"
---
# <a name="common-questions---vmware-to-azure-replication"></a>Perguntas comuns - replicação do VMware para Azure

Este artigo fornece respostas para perguntas comuns quando você estiver implantando a recuperação de desastre de VMs do VMware locais para o Azure. 

## <a name="general"></a>Geral 
### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>O que é necessário para recuperação de desastres de VM do VMware?

[Saiba mais sobre](vmware-azure-architecture.md) os componentes envolvidos na recuperação de desastre de VMs VMware. 

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Pode usar o Site Recovery para migrar VMs VMware para Azure?

Sim, além de usar o Site Recovery para configurar a recuperação de desastre completo para VMs do VMware, você também pode usar Site Recovery para migrar VMs do VMware locais para o Azure. Nesse cenário, você replica VMs VMware locais para o armazenamento do Azure. Em seguida, você faz failover do local para o Azure. Após o failover, os aplicativos e as cargas de trabalho estarão disponíveis e em execução nas VMs do Azure. O processo é semelhante à configuração de recuperação de desastres completa, exceto que em uma migração não é possível fazer failback do Azure.


### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Minha conta do Azure precisa de permissões para criar VMs?
Se você é um administrador da assinatura, tem as permissões de replicação necessárias. Se você não tiver, você precisa de permissões para criar uma VM do Azure no grupo de recursos e especifique quando você configurar a recuperação de Site e permissões para gravar na conta de armazenamento selecionada ou gerenciado disco com base em sua configuração de rede virtual. [Saiba mais](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="what-applications-can-i-replicate"></a>Quais aplicativos posso replicar?
É possível replicar qualquer aplicativo ou carga de trabalho em execução em uma VM VMware que esteja em conformidade com os [requisitos de replicação](vmware-physical-azure-support-matrix.md##replicated-machines).
- O Site Recovery fornece suporte para replicação com reconhecimento de aplicativo, de modo que os aplicativos possam fazer failover e failback para um estado inteligente.
- O Site Recovery integra-se a aplicativos da Microsoft como SharePoint, Exchange, Dynamics, SQL Server e Active Directory e trabalha em conjunto com os principais fornecedores, incluindo Oracle, SAP, IBM e Red Hat.
- [Saiba mais](site-recovery-workload.md) sobre a proteção de carga de trabalho.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Pode usar uma licença de servidor do sistema operacional convidado no Azure?
Sim, os clientes do Microsoft Software Assurance podem usar o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) para economizar nos custos de licenciamento para **computadores do Windows Server** que são migrados para o Azure ou usar o Azure para a recuperação de desastre.

## <a name="security"></a>Segurança

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Que acesso o Site Recovery precisa para os servidores VMware?
O Site Recovery precisa de acesso aos servidores VMware para:

- Configurar uma VM VMware executando o servidor de configuração do Site Recovery
- Descobrir automaticamente as VMs para replicação. 


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Que acesso o Site Recovery precisa para VMs VMware?

- Para replicar, uma VM VMware deve ter o serviço de Mobilidade do Site Recovery instalado e em execução. É possível implantar a ferramenta manualmente ou especificar que o Site Recovery faça uma instalação por push do serviço, quando você habilitar a replicação para uma VM. 
- Durante a replicação, as VMs comunicam-se com o Site Recovery da seguinte maneira:
    - As VMs comunicam-se com o servidor de configuração na porta HTTPS 443 para gerenciamento de replicação.
    - As VMs enviam os dados de replicação para o servidor em processo na porta HTTPS 9443 (pode ser modificada).
    - Se você habilitar a consistência de várias VMs, as VMs irão comunicar-se pela porta 20004.


### <a name="is-replication-data-sent-to-site-recovery"></a>Os dados de replicação são enviados para o Site Recovery?
Não, o Site Recovery não intercepta dados replicados e não tem informações sobre o que está sendo executado nas VMs. Os dados de replicação são trocados entre os hipervisores VMware e o armazenamento do Azure. O Site Recovery não tem a capacidade de interceptar os dados. Somente os metadados necessários para administrar a replicação e o failover é que são enviados para o serviço de Recuperação de Site.  

O Site Recovery é certificado pela ISO 27001:2013, 27018, HIPAA, DPA e está em processo de conclusão de avaliação dos padrões SOC2 e FedRAMP JAB.


## <a name="pricing"></a>Preços
### <a name="how-can-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Como para calcular cobranças aproximadas para a recuperação de desastres do VMware?

Você pode usar o [Calculadora de preços](https://aka.ms/asr_pricing_calculator) estimar os custos ao usar o Site Recovery.

Para a previsão detalhada nos custos, executar a ferramenta de Planejador de implantação para [VMware](https://aka.ms/siterecovery_deployment_planner)e usar o [relatório de estimativa de custo](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Há qualquer diferença de custo entre replicando para o armazenamento ou diretamente em discos gerenciados?

Discos gerenciados são cobrados um pouco diferente de contas de armazenamento. [Saiba mais](https://azure.microsoft.com/pricing/details/managed-disks/) sobre os preços de disco gerenciado.

## <a name="mobility-service"></a>Serviço de mobilidade

### <a name="where-can-i-find-the-mobility-service-installers"></a>Onde é possível localizar os instaladores do Serviço de Mobilidade?
Os instaladores estão na **%programdata%\asr\home\svsystems\pushinstallsvc\repository.** pasta no servidor de configuração.

## <a name="how-do-i-install-the-mobility-service"></a>Como fazer para instalar o Serviço Mobility?
Instalar em cada VM que você deseja replicar, usando uma série de métodos:
- [Instalação por push](vmware-physical-mobility-service-overview.md#push-installation)
- [Instalação manual](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui) da interface do usuário ou do Powershell.
- Implantação usando uma ferramenta de implantação, como [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md).

## <a name="managed-disks"></a>Discos gerenciados

### <a name="where-does-site-recovery-replicate-data-to"></a>Onde o Site Recovery replicar dados?

Site Recovery replica as VMs do VMware locais e servidores físicos para o managed disks no Azure.
- O servidor de processo de recuperação de Site grava os logs de replicação para uma conta de armazenamento de cache na região de destino.
- Esses logs são usados para criar pontos de recuperação no Azure discos que têm o prefixo de asrseeddisk gerenciados.
- Quando ocorre failover, o ponto de recuperação que você seleciona é usado para criar um novo disco gerenciado de destino. Esse disco gerenciado é anexado à VM no Azure.
- As VMs que anteriormente foram replicadas para uma conta de armazenamento (antes de março de 2019) não são afetadas.


### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Pode replicar máquinas novas contas de armazenamento?

Não, a partir de março de 2019, no portal, você pode replicar para o Azure managed disks. 

Replicação de novas VMs para uma conta de armazenamento só está disponível usando o PowerShell ou a API REST (versão 2018-01-10 ou 2016-08-10).

### <a name="what-are-the-benefits-in-replicating-to-managed-disks"></a>Quais são os benefícios do replicando para discos gerenciados?

[Saiba como](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery simplifica recuperação de desastre com discos gerenciados.


### <a name="can-i-change-the-managed-disk-type-after-machine-is-protected"></a>Posso alterar o tipo de disco gerenciado depois que o computador é protegido?

Sim, você pode facilmente [alterar o tipo de disco gerenciado](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) para replicações em andamento. Antes de alterar o tipo, certifique-se de que nenhuma URL de SAS é gerado no disco gerenciado. Vá para o recurso de disco gerenciado no portal do Azure e verifique se você tem uma faixa de URL SAS na folha de visão geral. Se estiver presente, clique nela para cancelar a exportação em andamento. Quando terminar, altere o tipo do disco de dentro de alguns minutos. No entanto, se você alterar o tipo de disco gerenciado, aguarde para pontos de recuperação atualizado seja gerado pelo Azure Site Recovery. Use os novos pontos de recuperação para qualquer failover de teste ou failover no futuro.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Posso mudar a replicação de discos gerenciados para discos não gerenciados?

Não, alternando de gerenciado para não gerenciado não tem suporte.

## <a name="replication"></a>Replicação


### <a name="what-are-the-replicated-vm-requirements"></a>Quais são os requisitos de VM replicadas?

[Saiba mais](vmware-physical-azure-support-matrix.md##replicated-machines) sobre requisitos do servidor VM VMware/físicos e o suporte.

### <a name="how-often-can-i-replicate-to-azure"></a>Com que frequência é possível replicar para o Azure?
A replicação é contínua ao replicar VMs VMware para o Azure.


### <a name="can-i-extend-replication"></a>É possível estender replicação?
Esse tipo de replicação estendida ou encadeada não tem suporte. Solicite esse recurso no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>É possível fazer uma replicação inicial offline?
Não há suporte para isso. Solicite esse recurso no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).


### <a name="what-is-asrseeddisk"></a>O que é asrseeddisk?
Para cada disco de origem, os dados são replicados para um disco gerenciado no Azure. Este disco tem o prefixo de asrseeddisk. Ele armazena a cópia do disco de origem e todos os instantâneos de ponto de recuperação.

### <a name="can-i-exclude-disks-from-replication"></a>Pode excluir discos da replicação?
Sim, você pode excluir discos.

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>É possível replicar VMs com discos dinâmicos?
Discos dinâmicos podem ser replicados. O disco do sistema operacional deve ser um disco básico.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Se eu usar grupos de replicação para consistência de várias VMs, posso adicionar uma nova VM a um grupo de replicação existente?
Sim, você pode adicionar novas VMs a um grupo de replicação existente quando ativar a replicação para elas.
- Não é possível adicionar uma VM a um grupo de replicação existente, depois que a replicação é iniciada.
- Você não pode criar um grupo de replicação para VMs existentes.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Posso modificar as VMs que estão replicando adicionando ou redimensionando discos?

Para replicação do VMware no Azure, você pode modificar o tamanho do disco. Se você quiser adicionar novos discos, precisará adicionar o disco e reativar a proteção para a VM.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Posso migrar máquinas locais para um novo servidor do vCenter sem afetar a replicação em andamento?
Não, migrar ou alterar o vCenter terá impacto sobre a replicação em andamento. Você precisa configurar a recuperação de Site com o novo servidor do vCenter e habilite a replicação para máquinas novamente.

### <a name="can-i-replicate-to-a-cachetarget-storage-account-which-has-a-vnet-with-azure-storage-firewalls-configured-on-it"></a>É possível replicar para uma conta de armazenamento de cache de destino que tem uma rede virtual (com firewalls de armazenamento do Azure) configurada nele?
Não, Site Recovery não dá suporte a replicação para o armazenamento em rede virtual.





## <a name="component-upgrade"></a>Atualização de componente

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>Minha versão do servidor de configuração ou o agente de serviços do Mobility é antigo e minha atualização falhou. O que devo fazer?  

Recuperação de site segue o modelo de suporte n-4. [Saiba mais](https://aka.ms/asr_support_statement) sobre como atualizar de versões muito antigas.

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-azure-site-recovery"></a>Onde posso encontrar as notas sobre a versão/pacotes cumulativos de atualizações do Azure Site Recovery?

[Saiba mais](site-recovery-whats-new.md) sobre novas atualizações, e [Obtenha informações do pacote cumulativo de atualizações](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Onde posso encontrar informações de atualização para recuperação de desastres no Azure?

[Saiba mais sobre](https://aka.ms/asr_vmware_upgrades) atualizando.

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>É necessário reinicializar as máquinas de origem para cada atualização?

Embora seja recomendável, a reinicialização não é obrigatória a cada atualização. [Saiba mais](https://aka.ms/asr_vmware_upgrades).


## <a name="configuration-server"></a>Servidor de configuração

### <a name="what-does-the-configuration-server-do"></a>O que o servidor de configuração faz?

O servidor de configuração executa os componentes locais do Site Recovery, incluindo:
- O servidor de configuração em si que coordena as comunicações entre local e o Azure e gerencia a replicação de dados.
- Servidor em processo que age como um gateway de replicação. Ele recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para o Armazenamento do Azure. O servidor de processo também faz uma instalação por push do serviço de mobilidade em VMs e executa a descoberta automática de VMs do VMware no local.
- O servidor de destino mestre que manipula os dados de replicação durante failback do Azure.

[Saiba mais](vmware-azure-architecture.md) sobre os componentes e processos do servidor de configuração.

### <a name="where-do-i-set-up-the-configuration-server"></a>Onde o servidor de configuração deve ser configurado?
É necessária uma única VM VMware local altamente disponível para o servidor de configuração. Para recuperação de desastres do servidor físico, você pode instalar o servidor de configuração em uma máquina física.

### <a name="what-do-i-need-for-the-configuration-server"></a>O que é necessário para o servidor de configuração?

Analise os [pré-requisitos](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>É possível configurar manualmente o servidor de configuração em vez de utilizar um modelo?
É recomendável que você [criar VM do servidor de configuração](vmware-azure-deploy-configuration-server.md) com a versão mais recente do modelo de OVF. Se por alguma razão você não pode, por exemplo você não tiver acesso ao servidor do VMware, você pode [baixar](physical-azure-set-up-source.md) o arquivo de instalação do portal e configurar o servidor de configuração.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Um servidor de configuração pode replicar para mais de uma região?
Não. Para fazer isso, você precisa de um servidor de configuração em cada região.

### <a name="can-i-host-a-configuration-server-in-azure"></a>É possível hospedar um servidor de configuração no Azure?
Embora seja possível, a VM do Azure em execução no servidor de configuração precisa comunicar-se com a infraestrutura VMware e VMs locais. Isso aumenta a latência e afeta a replicação em andamento.

### <a name="how-do-i-update-the-configuration-server"></a>Como fazer para atualizar o servidor de configuração?

[Saiba mais](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) como atualizar o servidor de configuração.
- Você pode encontrar as informações de atualização mais recentes sobre o [página de atualizações do Azure](https://azure.microsoft.com/updates/?product=site-recovery).
- Você pode baixar a versão mais recente do portal. Como alternativa, você pode baixar a versão mais recente do servidor de configuração diretamente a [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Se sua versão for mais de quatro versões mais antigas que a versão atual, consulte nosso [oferecem suporte a instrução](https://aka.ms/asr_support_statement) para orientação de atualização.

### <a name="should-i-back-up-the-configuration-server"></a>Deve fazer backup do servidor de configuração?
É recomendável fazer backups agendados regulares do servidor de configuração.
- Para failback bem-sucedida, o que está sendo executado o failback de VM deve existir no banco de dados de servidor de configuração.
- O servidor de configuração deve estar em execução e em um estado conectado.
- [Saiba mais](vmware-azure-manage-configuration-server.md) sobre tarefas comuns de gerenciamento de servidor de configuração.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Quando estou configurando o servidor de configuração, posso fazer o download e instalar o MySQL manualmente?

Sim. Faça o download o MySQL e colocá-lo na pasta **C:\Temp\ASRSetup**. Em seguida, instale-o manualmente. Quando você configura a VM do servidor de configuração e aceita os termos, o MySQL será listado como **Já instalado** em **Faça o download e instale**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Posso evitar de fazer o download de MySQL mas permitir que o Site Recovery instale-o?

Sim. Faça o download do instalador do MySQL e coloque-o na pasta **C:\Temp\ASRSetup**.  Quando você configura a VM do servidor de configuração, aceite os termos e clique em **Baixe e instale o**. O portal usa o instalador que você adicionou para instalar o MySQL.
 
### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Posso usar a VM do servidor de configuração para mais alguma coisa?
Não, você só deve usar a VM para o servidor de configuração. 

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Posso clonar um servidor de configuração e usá-lo para orquestração?
Não, você deve configurar um servidor de configuração atualizados para evitar problemas de registro.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Posso alterar o cofre no qual o servidor de configuração está registrado?
Não. Depois que um cofre está associado com o servidor de configuração, ele não pode ser alterado. Revisão [deste artigo](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) para saber mais sobre como registrar novamente.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Posso usar o mesmo servidor de configuração para recuperação de desastres de VMs VMware e servidores físicos
Sim, mas observe que esse computador físico pode ser feito apenas para uma VM do VMware.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Onde posso fazer o download da frase secreta para o servidor de configuração?
[Saiba como](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) baixar a frase secreta.

### <a name="where-can-i-download-vault-registration-keys"></a>Em que local posso baixar as chaves de registro do cofre?

No cofre de serviços de recuperação, clique em **servidores de configuração** na **infraestrutura do Site Recovery** > **gerenciar**. Em seguida, na **servidores**, selecione **baixar a chave de registro** para baixar o arquivo de credenciais do cofre.

## <a name="process-server"></a>Servidor de processo

### <a name="unable-to-select-process-server-during-enable-replication"></a>Não é possível selecionar o servidor de processo durante a habilitar a replicação

Da versão 9.24, os aprimoramentos foram feitos para fornecer [processar alertas do servidor](vmware-physical-azure-monitor-process-server.md#process-server-alerts) sobre quando configurar um servidor de processo de escalonamento horizontal. Isso é para evitar a limitação do servidor de processo e evitar o uso do servidor de processo não íntegro.

### <a name="what-should-i-do-to-obtain-accurate-health-status-of-process-server"></a>O que devo fazer para obter o status de integridade precisa de servidor de processo?

Atualizar os componentes do Site Recovery para o [versões mais recentes](service-updates-how-to.md#links-to-currently-supported-update-rollups) (pelo menos 9.24 ou superior).

## <a name="failover-and-failback"></a>Failover e failback
### <a name="can-i-use-the-process-server-at-on-premises-for-failback"></a>Posso usar o servidor de processo no local para failback?
É altamente recomendável criar um servidor de processo no Azure para fins de failback, para evitar as latências de transferência de dados. Além disso, no caso de você separado da rede de VMs de origem com a rede voltado para o Azure no servidor de configuração, é essencial para usar o servidor de processo criado no Azure para failback.

### <a name="can-i-retain-the-ip-address-on-failover"></a>Posso manter o endereço IP durante o failover?
Sim, você pode manter o endereço IP durante o failover. Certifique-se de que você especifique o endereço IP de destino nas configurações 'Computação e rede' para a VM antes do failover. Além disso, desligue as máquinas no momento do failover para evitar conflitos de endereço IP durante o failback.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Posso alterar o tamanho da VM de destino ou o tipo VM antes do failover?
Sim, você pode alterar o tipo ou tamanho da VM a qualquer momento antes do failover nas configurações de computação e rede da Vm replicada, no portal.

### <a name="how-far-back-can-i-recover"></a>A que tempo é possível fazer failback de recuperação?
De VMware para o Azure, o ponto de recuperação mais antigo que você pode utilizar é de 72 horas.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Como fazer para acessar VMs do Azure após o failover?
Após o failover, você poderá acessar as VMs do Azure por meio de uma conexão segura com a Internet, por meio de uma VPN site a site ou por meio do Microsoft Azure ExpressRoute. Você precisará preparar uma série de coisas para se conectar. [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Failover de dados é resiliente?

O Azure foi desenvolvido para resiliência. O Site Recovery foi projetado para failover em um datacenter do Azure secundário, de acordo com o SLA do Azure. Quando ocorre um failover, garantimos que seus metadados e cofres permaneçam na mesma região geográfica escolhida para o cofre.

### <a name="is-failover-automatic"></a>O failover é automático?
[Failover](site-recovery-failover.md) não é automático. Iniciar failovers com um único clique no portal, ou você pode usar [PowerShell](/powershell/module/az.recoveryservices) para disparar um failover.

### <a name="can-i-fail-back-to-a-different-location"></a>É possível fazer failback para um local diferente?
Sim, se você fez failover no Azure poderá fazer failback para um local diferente, se o original não estiver disponível. [Saiba mais](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>Por que preciso de uma VPN ou ExpressRoute para fazer failback?
Ao fazer failback do Azure, os dados do Azure são copiados de volta para a VM local e o acesso privado é necessário.

### <a name="can-i-resize-the-azure-vm-after-failover"></a>Eu posso redimensionar a VM do Azure após failover?
Não, você não pode alterar o tamanho nem o tipo da VM de destino após o failover.


## <a name="automation-and-scripting"></a>Automação e scripts

### <a name="can-i-set-up-replication-with-scripting"></a>É possível configurar a replicação com scripts?
Sim. Você pode automatizar fluxos de trabalho do Site Recovery, utilizando a API Rest, o PowerShell ou o SDK do Azure.[Saiba mais](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Desempenho e capacidade
### <a name="can-i-throttle-replication-bandwidth"></a>É possível limitar a largura de banda de replicação?
Sim. [Saiba mais](site-recovery-plan-capacity-vmware.md).


## <a name="next-steps"></a>Próximas etapas
* [Analisar](vmware-physical-azure-support-matrix.md) os requisitos de suporte.
* [Configurar](vmware-azure-tutorial.md) replicação de VMware para o Azure.
