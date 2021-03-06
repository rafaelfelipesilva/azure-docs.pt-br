---
title: Perguntas frequentes do DNS do Azure
description: Perguntas frequentes sobre o DNS do Azure
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 3/21/2019
ms.author: victorh
ms.openlocfilehash: 4f0800dfd264059e1dc8aac32a54f216f777647f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096158"
---
# <a name="azure-dns-faq"></a>Perguntas frequentes do DNS do Azure

## <a name="about-azure-dns"></a>Sobre o DNS do Azure

### <a name="what-is-azure-dns"></a>O que é o DNS do Azure?

O Sistema de Nomes de Domínio (DNS) traduz ou resolve um nome de site ou serviço para seu endereço IP. O DNS do Azure é um serviço de hospedagem para domínios DNS. Ele fornece resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e faturamento que os outros serviços do Azure.

Domínios DNS no DNS do Azure estão hospedados na rede global do Azure de servidores de nomes DNS. Esse sistema usa a rede Anycast para que cada consulta DNS seja respondida pelo servidor DNS mais próximo disponível. O DNS do Azure fornece desempenho rápido e alta disponibilidade para seu domínio.

O DNS do Azure é baseado no Azure Resource Manager. O DNS do Azure se beneficia dos recursos do Resource Manager, como controle de acesso baseado em função, logs de auditoria e bloqueio de recursos. Você pode gerenciar domínios e registros por meio do portal do Azure, dos cmdlets do Azure PowerShell e da CLI do Azure de plataforma cruzada. Os aplicativos que exigem gerenciamento automático de DNS podem se integrar ao serviço por meio da API REST e dos SDKs.

### <a name="how-much-does-azure-dns-cost"></a>Quanto custa o DNS do Azure?

O modelo de faturamento do DNS do Azure é baseado no número de zonas DNS hospedadas no DNS do Azure. Também é baseado no número de consultas DNS recebidas. Descontos são fornecidos com base no uso.

Para saber mais, veja a [página de preços do DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>O que é o SLA para DNS do Azure?

O Azure garante que as solicitações válidas de DNS recebam uma resposta de pelo menos um servidor de nomes DNS do Azure 100% do tempo.

Para saber mais, confira a [página de SLA do DNS do Azure](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>O que é uma zona DNS? É a mesma coisa que um domínio DNS? 

Um domínio é um nome exclusivo no sistema de nomes de domínio. Um exemplo é contoso.com.

Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Por exemplo, o domínio contoso.com pode conter vários registros DNS. Os registros podem incluir mail.contoso.com para um servidor de email e www\.contoso.com para um site. Esses registros estão hospedados na zona DNS contoso.com.

É um nome de domínio *apenas um nome*. Uma zona DNS é um recurso de dados que contém os registros DNS de um nome de domínio. Você pode usar o DNS do Azure para hospedar uma zona DNS e gerenciar os registros DNS para um domínio no Azure. Ele também fornece servidores de nomes de DNS para resolver consultas de DNS da Internet.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>Preciso comprar um nome de domínio DNS para usar o DNS do Azure? 

Não necessariamente.

Você não precisa comprar um domínio para hospedar uma zona DNS no DNS do Azure. É possível criar uma zona DNS a qualquer momento sem ser proprietário do nome de domínio. As consultas DNS para essa zona só serão resolvidas se forem direcionadas para os servidores de nomes DNS do Azure atribuídos à zona.

Para vincular sua zona DNS à hierarquia DNS global, você deve comprar o nome de domínio. Então, as consultas DNS de qualquer lugar do mundo encontram sua zona DNS e respondem com seus registros DNS.

## <a name="azure-dns-features"></a>Recursos do DNS do Azure

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Existem restrições ao usar registros de alias para um apex de nome de domínio com o Gerenciador de Tráfego?

Sim. Você deve usar endereços IP públicos estáticos com o Gerenciador de Tráfego do Azure. Configure o destino **External endpoint** usando um endereço IP estático. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>O DNS do Azure dá suporte a roteamento de tráfego com base em DNS ou failover de ponto de extremidade?

Roteamento de tráfego baseado em DNS e failover de ponto de extremidade são fornecidos pelo Gerenciador de Tráfego. O Gerenciador de Tráfego é um serviço do Azure separado que pode ser usado com o DNS do Azure. Para saber mais, confira a [Visão Geral do Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md).

O DNS do Azure só oferece suporte à hospedagem de domínios DNS estáticos, em que cada consulta DNS de um determinado registro DNS sempre recebe a mesma resposta de DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>O DNS do Azure dá suporte ao registro de nome de domínio?

Não. O DNS do Azure atualmente não oferece suporte à opção de comprar nomes de domínio. Para comprar domínios, você deve usar um registrador de nomes de domínio de terceiros. O registrador normalmente cobra uma pequena taxa anual. Os domínios, em seguida, podem ser hospedados no DNS do Azure para gerenciamento de registros DNS. Confira [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md)para saber mais.

O recurso para comprar nomes de domínio é rastreado no backlog do Azure. Use o site de feedback para [registrar seu suporte para esse recurso](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>O DNS do Azure oferece suporte a DNSSEC?

Não. O DNS do Azure atualmente não oferece suporte às extensões de segurança do sistema de nomes de domínio (DNSSEC).

O recurso DNSSEC é rastreado no backlog de DNS do Azure. Use o site de feedback para [registrar seu suporte para esse recurso](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>O DNS do Azure dá suporte para transferências de zona (AXFR/IXFR)?

Não. O DNS do Azure não suporta atualmente as transferências de zona. As zonas DNS podem ser [importadas para o DNS do Azure usando a CLI do Azure](dns-import-export.md). Os registros DNS são gerenciados por meio do [portal de gerenciamento do DNS do Azure](dns-operations-recordsets-portal.md), [API REST](https://docs.microsoft.com/powershell/module/az.dns), [SDK](dns-sdk.md), [cmdlets do PowerShell](dns-operations-recordsets.md) ou da [ferramenta CLI](dns-operations-recordsets-cli.md).

O recurso de transferência de zona é rastreado no backlog de DNS do Azure. Use o site de feedback para [registrar seu suporte para esse recurso](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>O DNS do Azure oferece suporte a redirecionamentos de URL?

Não. Os serviços de redirecionamento de URL não são um serviço DNS. Eles trabalham no nível HTTP, e não no nível DNS. Alguns provedores DNS agrupam um serviço de redirecionamento de URL como parte de sua oferta geral. No momento, esse serviço não é suportado pelo DNS do Azure.

O recurso de redirecionamento de URL é rastreado no backlog de DNS do Azure. Use o site de feedback para [registrar seu suporte para esse recurso](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>O DNS do Azure oferece suporte à codificação ASCII estendida (8 bits) definida para conjuntos de registros TXT?

Sim. O DNS do Azure suporta o conjunto de codificação ASCII estendido para conjuntos de registros TXT. Mas você deve usar a versão mais recente das APIs REST do Azure, SDKs, PowerShell e CLI. Versões anteriores a 1º de outubro de 2017 ou SDK 2.1 não suportam o conjunto ASCII estendido. 

Por exemplo, você pode fornecer uma string como o valor para um registro TXT que tem o caractere ASCII estendido \128. Um exemplo é "abcd\128efgh". O DNS do Azure usa o valor de byte desse caractere, que é 128, na representação interna. No momento da resolução do DNS, esse valor de byte é retornado na resposta. Observe também que "abc" e "\097\098\099" são intercambiáveis com relação à resolução. 

Seguimos as regras de escape de formatação mestre de arquivo da zona [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) para registros TXT. Por exemplo, `\` agora realmente escapa de tudo pelo RFC. Se você especificar `A\B` como o valor do registro TXT, ele será representado e resolvido como apenas `AB`. Se você realmente quer que o registro TXT tenha `A\B` na resolução, você precisa escapar de `\` novamente. Como exemplo, especifique `A\\B`.

Atualmente, esse suporte não está disponível para registros TXT criados no portal do Azure.

## <a name="alias-records"></a>Registros de alias

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Quais são alguns cenários em que os registros de alias são úteis?

Consulte a seção de cenários na [visão geral dos registros de alias do DNS do Azure](dns-alias.md).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Quais tipos de registro têm suporte para conjuntos de registros de alias?

Os conjuntos de registros de alias são suportados para os seguintes tipos de registro em uma zona DNS do Azure:
 
- O  
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Quais recursos têm suporte como destinos para os conjuntos de registros de alias?

- **Aponte para um recurso IP público de um conjunto de registros DNS A / AAAA.** Você pode criar um conjunto de registros A / AAAA e torná-lo um conjunto de registros de alias para apontar para um recurso IP público.
- **Aponte para um perfil do Gerenciador de Tráfego de um conjunto de registros DNS A / AAAA / CNAME.** Você pode apontar para o CNAME de um perfil do Gerenciador de Tráfego a partir de um conjunto de registros CNAME do DNS. Um exemplo é contoso.trafficmanager.net. Agora, você também pode apontar para um perfil do Gerenciador de Tráfego que tenha pontos de extremidade externos de um registro A ou AAAA definido em sua zona DNS.
- **Aponte para um ponto de extremidade do Azure Content Delivery Network (CDN)**. Isso é útil quando você cria sites estático usando o armazenamento do Azure e CDN do Azure.
- **Apontar para outro conjunto de registros DNS dentro da mesma zona.** Registros de alias podem fazer referência a outros conjuntos de registros do mesmo tipo. Por exemplo, você pode ter um conjunto de registros DNS CNAME como um alias para outro conjunto de registros CNAME do mesmo tipo. Essa organização é útil se você quiser que alguns conjuntos de registros sejam aliases e alguns não-aliases.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Posso criar e atualizar os registros de alias do portal do Azure?

Sim. Você pode criar ou gerenciar registros de alias no portal do Azure junto com as APIs REST do Azure, o PowerShell, a CLI e os SDKs.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Os registros de alias ajudam a garantir que meu conjunto de registros DNS seja excluído quando o IP público subjacente é excluído?

Sim. Esse recurso é um dos principais recursos dos registros de alias. Isso ajuda a evitar possíveis interrupções para os usuários do seu aplicativo.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Os registros de alias ajudam a garantir que meu conjunto de registros DNS seja atualizado para o endereço IP correto quando o endereço IP público subjacente for alterado?

Sim. Esse recurso é um dos principais recursos dos registros de alias. Isso ajuda a evitar possíveis interrupções ou riscos de segurança para seu aplicativo.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Há alguma restrição ao usar conjuntos de registros de alias para registros A ou AAAA para apontar para o Gerenciador de Tráfego?

Sim. Para apontar para um perfil do Gerenciador de Tráfego como um alias de um conjunto de registros A ou AAAA, o perfil do Gerenciador de Tráfego deve usar apenas pontos de extremidade externos. Ao criar os terminais externos no Gerenciador de Tráfego, forneça os endereços IP reais dos nós de extremidade.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>Existe um custo adicional para usar registros de alias?

Os registros de alias são uma qualificação em um conjunto de registros DNS válido. Não há cobrança adicional para registros de alias.

## <a name="use-azure-dns"></a>Use o DNS do Azure

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>É possível co-hospedar um domínio usando o DNS do Azure e outro provedor de DNS?

Sim. O DNS do Azure oferece suporte a domínios de co-hospedagem com outros serviços de DNS.

Para configurar a co-hospedagem, modificar os registros NS para o domínio apontar para os servidores de nome de ambos os provedores. Os registros do servidor de nomes (NS) controlam quais provedores recebem consultas DNS para o domínio. Você pode modificar esses registros do NS no DNS do Azure, no outro provedor e na zona pai. A zona pai geralmente é configurada por meio do registrador de nomes de domínio. Para saber mais sobre delegação de DNS, veja [Delegação de domínio de DNS](dns-domain-delegation.md).

Além disso, verifique se os registros DNS do domínio estão sincronizados entre os dois provedores de DNS. O DNS do Azure atualmente não oferece suporte a transferências de zona DNS. Os registros DNS devem ser sincronizados usando o [portal de gerenciamento do DNS do Azure](dns-operations-recordsets-portal.md), [API REST](https://docs.microsoft.com/powershell/module/az.dns), [SDK](dns-sdk.md), [cmdlets do PowerShell](dns-operations-recordsets.md) ou [Ferramenta CLI](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Preciso delegar meu domínio para todos os quatro servidores de nome de DNS do Azure?

Sim. O DNS do Azure atribui quatro servidores de nomes a cada zona DNS. Esse arranjo é para isolamento de falhas e maior resiliência. Para se qualificar para o SLA de DNS do Azure, delegue seu domínio a todos os quatro servidores de nomes.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Quais são os limites de uso do DNS do Azure?

Os limites padrão a seguir se aplicam quando você usa o DNS do Azure.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Posso mover uma zona DNS do Azure entre grupos de recursos ou entre assinaturas?

Sim. As zonas DNS podem ser movidas entre grupos de recursos ou entre assinaturas.

Não há efeito nas consultas DNS quando você move uma zona DNS. Os servidores de nomes atribuídos à zona permanecem os mesmos. Consultas DNS são processadas normalmente.

Para saber mais e instruções sobre como mover as zonas DNS, veja [Mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/resource-group-move-resources.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Quanto tempo leva para as alterações de DNS entrarem em vigor?

Novas zonas DNS e registros DNS geralmente aparecem nos servidores de nomes DNS do Azure rapidamente. O tempo é de alguns segundos.

Alterações nos registros DNS existentes podem demorar um pouco mais. Eles geralmente aparecem nos servidores de nomes DNS do Azure em 60 segundos. O armazenamento em cache de DNS por clientes DNS e resolvedores recursivos de DNS fora do DNS do Azure também pode afetar o tempo. Para controlar essa duração do cache, use a propriedade Time-To-Live (TTL) de cada conjunto de registros.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Como posso proteger minhas zonas DNS contra exclusão acidental?

O DNS do Azure é gerenciado usando o Azure Resource Manager. O DNS do Azure se beneficia dos recursos de controle de acesso fornecidos pelo Azure Resource Manager. O controle de acesso baseado em função controla quais usuários têm acesso de leitura ou gravação a zonas DNS e conjuntos de registros. Bloqueios de recursos impedem a modificação acidental ou a exclusão de zonas DNS e conjuntos de registros.

Para obter mais informações, consulte [Proteger zonas DNS e registros](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Como posso configurar registros de SPF no DNS do Azure?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Servidores de nome DNS do Azure são resolvidas pelo IPv6? 

Sim. Os servidores de nomes DNS do Azure são de pilha dupla. Pilha dupla significa que eles têm endereços IPv4 e IPv6. Para localizar o endereço IPv6 dos servidores de nomes DNS do Azure atribuídos à sua zona DNS, use uma ferramenta como nslookup. Um exemplo é `nslookup -q=aaaa <Azure DNS Nameserver>`.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Como configuro um IDN no DNS do Azure?

Os nomes de domínio internacionalizados (IDNs) codificam cada nome DNS usando [punycode](https://en.wikipedia.org/wiki/Punycode). Consultas DNS são feitas usando esses nomes codificados com punycode.

Para configurar IDNs no DNS do Azure, converta o nome da zona ou o nome do conjunto de registros em punycode. No momento, o DNS do Azure não oferece suporte à conversão interna de ou para o punycode.

## <a name="private-dns"></a>DNS privado

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

### <a name="does-azure-dns-support-private-domains"></a>O DNS do Azure oferece suporte a domínios particulares?

O suporte para domínios privados é implementado usando o recurso Zonas Privadas. Esse recurso está disponível atualmente em visualização pública. As zonas privadas são gerenciadas usando as mesmas ferramentas que as zonas DNS do Azure voltadas para a Internet. Eles são resolvíveis apenas de dentro de suas redes virtuais especificadas. Para mais informações, consulte a [visão geral](private-dns-overview.md).

Atualmente, as zonas privadas não têm suporte no portal do Azure.

Para obter informações sobre outras opções de DNS interno no Azure, consulte [Resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="whats-the-difference-between-registration-virtual-network-and-resolution-virtual-network-in-the-context-of-private-zones"></a>Qual é a diferença entre a rede virtual de registro e a rede virtual de resolução no contexto de zonas privadas?

Você pode vincular redes virtuais a uma zona privada do DNS como uma rede virtual de Registro ou como uma rede virtual de Resolução. Em qualquer um dos casos, as máquinas virtuais na rede virtual resolvem com êxito os registros na zona privada. Com uma rede virtual de Registro, os registros DNS são automaticamente registrados na zona para as máquinas virtuais na rede virtual. Quando uma máquina virtual em uma rede virtual de registro é excluída, o registro DNS correspondente da zona privada vinculada é automaticamente removido. 

### <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>As Zonas Privadas do DNS do Azure funcionarão em todas as regiões do Azure?

Sim. As zonas privadas são suportadas para resolução de DNS entre redes virtuais nas regiões do Azure. As zonas privadas funcionam mesmo sem espiar explicitamente as redes virtuais. Todas as redes virtuais devem ser especificadas como redes virtuais de resolução para a zona privada. Os clientes podem precisar que as redes virtuais sejam analisadas para que o tráfego TCP / HTTP flua de uma região para outra.

### <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>A conectividade com a Internet de redes virtuais é necessária para zonas privadas?

Não. As zonas privadas funcionam junto com as redes virtuais. Os clientes os usam para gerenciar domínios para máquinas virtuais ou outros recursos dentro e entre redes virtuais. A conectividade com a Internet não é necessária para a resolução de nomes. 

### <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>A mesma zona privada pode ser usada para várias redes virtuais para resolução?

Sim. Os clientes podem associar até 10 redes virtuais de resolução com uma única zona privada.

### <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-resolution-virtual-network-to-a-private-zone"></a>Uma rede virtual que pertence a uma assinatura diferente pode ser adicionada como uma rede virtual de resolução a uma zona privada?

Sim. Você precisa ter permissão de operação de gravação nas redes virtuais e na zona DNS privado. A permissão de gravação pode ser concedida para várias funções do RBAC. Por exemplo, a função de RBAC do Colaborador da Rede Clássica tem permissões de gravação nas redes virtuais. Para obter mais informações sobre funções do RBAC, consulte [Controle de acesso baseado em função](../role-based-access-control/overview.md).

### <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-the-virtual-machines-are-deleted-by-the-customer"></a>Os registros DNS da máquina de virtual que são registrados automaticamente em uma zona privada serão excluídos automaticamente quando as máquinas virtuais forem excluídas pelo cliente?

Sim. Se você excluir uma máquina virtual em uma rede virtual de Registro, os registros DNS registrados na zona serão excluídos automaticamente. 

### <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-registration-virtual-network-be-deleted-manually"></a>Um registro de máquina virtual registrado automaticamente em uma zona privada de uma rede virtual de Registro pode ser excluído manualmente?

Não. Os registros DNS da máquina virtual que são registrados automaticamente em uma zona privada a partir de uma rede virtual de Registro não são visíveis ou editáveis pelos clientes. Você pode regravar os registros DNS registrados automaticamente com um registro DNS criado manualmente na zona. A pergunta e resposta a seguir abordam este tópico.

### <a name="what-happens-when-we-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-registration-virtual-network"></a>O que acontece quando tentamos criar manualmente um novo registro DNS em uma zona privada que tenha o mesmo nome de host de uma máquina virtual existente registrada automaticamente em uma rede virtual de Registro?

Você tenta criar manualmente um novo registro DNS em uma zona privada que tenha o mesmo nome de host de uma máquina virtual registrada automaticamente existente em uma rede virtual de registro. Quando você faz isso, o novo registro DNS sobrescreve o registro de máquina virtual registrado automaticamente. Se você tentar excluir esse registro DNS criado manualmente da zona novamente, a exclusão será bem-sucedida. O registro automático acontece novamente, contanto que a máquina virtual ainda exista e tenha um IP privado anexado a ela. O registro DNS é recriado automaticamente na zona.

### <a name="what-happens-when-we-unlink-a-registration-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>O que acontece quando desvinculamos uma rede virtual de registro de uma zona privada? Os registros da máquina virtual registrados automaticamente da rede virtual também serão removidos da zona?

Sim. Para desvincular uma rede virtual de registro de uma zona privada, atualize a zona DNS para remover a rede virtual de registro associada. Nesse processo, os registros da máquina virtual que foram registrados automaticamente são removidos da zona. 

### <a name="what-happens-when-we-delete-a-registration-or-resolution-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-registration-or-resolution--virtual-network-from-the-zone"></a>O que acontece quando excluímos uma rede virtual de registro ou resolução vinculada a uma zona privada? Precisamos atualizar manualmente a zona privada para desvincular a rede virtual como uma rede virtual de registro ou de resolução da zona?

Sim. Quando você exclui uma rede virtual de Registro ou Resolução sem desvinculá-la de uma zona privada primeiro, sua operação de exclusão é bem-sucedida. Mas a rede virtual não é desvinculada automaticamente da sua zona privada, se houver. Você deve desvincular manualmente a rede virtual da zona privada. Por esse motivo, desvincule sua rede virtual da sua zona privada antes de excluí-la.

### <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Resolução DNS, usando o padrão FQDN (internal.cloudapp.net) ainda funciona mesmo quando uma zona privada (por exemplo, private.contoso.com) é vinculada a uma rede virtual?

Sim. As zonas privadas não substituem as resoluções DNS padrão usando a zona interna.cloudapp.net fornecida pelo Azure. É oferecido como um recurso ou aprimoramento adicional. Independentemente de você confiar no internal.cloudapp.net fornecido pelo Azure ou em sua própria zona privada, use o FQDN da zona que deseja resolver. 

### <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>O sufixo DNS em máquinas virtuais em uma rede virtual vinculada será alterado para o da zona privada?

Não. O sufixo DNS nas máquinas virtuais em sua rede virtual vinculada permanece como o sufixo fornecido pelo Azure padrão ("*.internal.cloudapp.net"). Você pode alterar manualmente esse sufixo DNS em suas máquinas virtuais para aquela da zona privada. 

### <a name="are-there-any-limitations-for-private-zones-during-this-preview"></a>Há alguma limitação para zonas privadas durante essa visualização?

Sim. Durante a pré-visualização pública, existem as seguintes limitações.
* Uma rede virtual de registro por zona privada.
* Até 10 redes virtuais de resolução por zona privada.
* Uma determinada rede virtual vincula-se a apenas uma zona privada como uma rede virtual de registro.
* Uma determinada rede virtual é vinculada a até 10 zonas privadas como uma rede virtual de resolução.
* Se uma rede virtual de registro for especificada, os registros DNS das VMs dessa rede virtual que estão registradas na zona privada não poderão ser visualizados ou recuperados do PowerShell, da CLI ou das APIs. Os registros da VM são registrados e resolvidos com sucesso.
* O DNS reverso funciona apenas para o espaço IP privado na rede virtual de registro.
* O DNS reverso de um IP privado que não está registrado na zona privada retorna "internal.cloudapp.net" como o sufixo DNS. Este sufixo não pode ser resolvido. Um exemplo é um IP privado para uma máquina virtual em uma rede virtual vinculada como uma rede virtual de Resolução a uma zona privada.
* Uma rede virtual precisa estar vazia quando é vinculada pela primeira vez a uma zona privada como uma rede virtual de registro ou de resolução. A rede virtual pode, então, não ficar vazia para futuras vinculações como uma rede virtual de Registro ou Resolução a outras zonas privadas.
* O encaminhamento condicional não é suportado, por exemplo, para permitir a resolução entre o Azure e as redes locais. Saiba como os clientes podem realizar esse cenário por meio de outros mecanismos. Veja [Resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

### <a name="are-there-any-quotas-or-limits-on-zones-or-records-for-private-zones"></a>Existem cotas ou limites em zonas ou registros para zonas privadas?

Não há limites para o número de zonas permitidas por assinatura para zonas privadas. Não há limites no número de conjuntos de registros por zona para zonas privadas. Ambas as zonas públicas e privadas contam para os limites gerais do DNS. Para obter mais informações, consulte os [limites de assinatura e serviço do Azure](../azure-subscription-service-limits.md#azure-dns-limits)

### <a name="is-there-portal-support-for-private-zones"></a>Existe suporte de portal para zonas privadas?

As zonas privadas que já são criadas por meio de APIs, PowerShell, CLI e SDKs são visíveis no portal do Azure. Mas os clientes não podem criar novas zonas privadas ou gerenciar associações com redes virtuais. Para redes virtuais associadas como redes virtuais de registro, os registros de VM registrados automaticamente não são visíveis no portal. 

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre o DNS do Azure](dns-overview.md).

- [Saiba mais sobre como usar o DNS do Azure para domínios privados](private-dns-overview.md).

- [Saiba mais sobre as zonas DNS e registros](dns-zones-records.md).

- [Introdução ao Azure DNS](dns-getstarted-portal.md).
