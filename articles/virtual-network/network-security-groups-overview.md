---
title: Visão geral dos grupos de segurança da rede Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre grupos de segurança de rede. Os grupos de segurança da rede ajudam-no a filtrar o tráfego de rede entre os recursos da Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: kumud
ms.reviewer: kumud
ms.custom: contperf-fy21q1
ms.openlocfilehash: 4e23c6f25145724a5300c9e5cdcb55431fb0b4f2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97028772"
---
# <a name="network-security-groups"></a>Grupos de segurança de rede
<a name="network-security-groups"></a>

Pode utilizar um grupo de segurança da rede Azure para filtrar o tráfego da rede de e para os recursos Azure numa rede virtual Azure. Os grupos de segurança de rede contêm [regras de segurança](#security-rules) que permitem ou negam o tráfego de entrada ou de saída de e para vários tipos de recursos do Azure. Para cada regra, pode especificar a origem e o destino, a porta e o protocolo.

Este artigo descreve propriedades de uma regra de grupo de segurança de rede, [as regras de segurança padrão](#default-security-rules) que são aplicadas, e as propriedades de regra que você pode modificar para criar uma [regra de segurança aumentada](#augmented-security-rules).

## <a name="security-rules"></a><a name="security-rules"></a> Regras de segurança

Os grupos de segurança de rede contêm zero ou tantas regras conforme pretender, dentro dos [limites](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) das subscrições do Azure. Cada regra especifica as propriedades seguintes:

|Propriedade  |Explicação  |
|---------|---------|
|Nome|Um nome exclusivo no grupo de segurança de rede.|
|Prioridade | Um número entre 100 e 4096. As regras são processadas por ordem de prioridade, sendo os números mais baixos processados antes dos mais elevados, uma vez que têm uma prioridade superior. Quando o tráfego corresponder a uma regra, o processamento para. Como resultado, qualquer regra que exista com prioridades inferiores (números mais elevados) e que tenham os mesmos atributos das regras com prioridades superiores não é processada.|
|Origem ou destino| Qualquer endereço IP ou um endereço IP individual, um bloco CIDR (Classless Inter-domain Routing) (por exemplo, 10.0.0.0/24), uma etiqueta de serviço ou um grupo de segurança de aplicação. Se especificar um endereço para um recurso do Azure, indique o endereço IP privado atribuído ao mesmo. Os grupos de segurança de rede são processados depois de o Azure traduzir um endereço IP público num privado para tráfego de entrada e antes de traduzir um endereço IP privado num público para tráfego de saída. . Especificar um intervalo, uma etiqueta de serviço ou um grupo de segurança de aplicações permite-lhe criar menos regras de segurança. A capacidade de especificar vários endereços IP individuais e intervalos de endereços IP (não pode indicar etiquetas de serviço ou grupos de aplicações) numa regra é denominada [regras de segurança aumentadas](#augmented-security-rules). As regras de segurança aumentadas só podem ser criadas em grupos de segurança de rede gerados através do modelo de implementação do Resource Manager. Não pode especificar vários endereços IP nem intervalos de endereços IP em grupos de segurança de rede criados com o modelo de implementação clássica.|
|Protocolo     | TCP, UDP, ICMP ou Qualquer outro.|
|Direção| Indica se a regra se aplica a tráfego de entrada ou de saída.|
|Intervalo de portas     |Pode especificar uma porta individual ou um intervalo de portas. Por exemplo, pode indicar 80 ou 10000-10005. Especificar intervalos permite-lhe criar menos regras de segurança. As regras de segurança aumentadas só podem ser criadas em grupos de segurança de rede gerados através do modelo de implementação do Resource Manager. Não pode especificar várias portas nem intervalos de portas na mesma regra de segurança em grupos de segurança de rede criados com o modelo de implementação clássica.   |
|Ação     | Permitir ou negar        |

As regras de segurança dos grupos de segurança de rede são avaliadas por prioridade, utilizando as informações de cinco cadeias de identificação (origem, porta de origem, destino, porta de destino e protocolo) para permitir ou negar o tráfego. Não pode criar duas regras de segurança com a mesma prioridade e direção. É criado um registo de fluxo para as ligações existentes. A comunicação é permitida ou negada com base no estado da ligação do registo do fluxo. O registo do fluxo permite que um grupo de segurança de rede tenha monitoração de estado. Se especificar uma regra de segurança de saída para qualquer endereço através da porta 80, por exemplo, não é necessário especificar uma regra de segurança de entrada para a resposta ao tráfego de saída. Só tem de especificar uma regra de segurança de entrada se a comunicação for iniciada externamente. O oposto também se aplica. Se o tráfego de entrada for permitido numa porta, não é necessário especificar uma regra de segurança de saída para responder ao tráfego através da porta.

As ligações existentes não podem ser interrompidas quando remover uma regra de segurança que ativou o fluxo. Os fluxos de tráfego são interrompidos quando as ligações são paradas e não há qualquer tráfego a fluir em qualquer direção, pelo menos, durante alguns minutos.

Há limites ao número de regras de segurança que pode criar num grupo de segurança de rede. Para obter mais detalhes, veja [Limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

### <a name="default-security-rules"></a><a name="default-security-rules"></a> Regras de segurança predefinidos

O Azure cria as seguintes regras predefinidas em cada grupo de segurança de rede que criar:

#### <a name="inbound"></a>Entrada

##### <a name="allowvnetinbound"></a>AllowVNetInBound

|Prioridade|Origem|Portas de origem|Destino|Portas de destino|Protocolo|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Qualquer|Permitir|

##### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Prioridade|Origem|Portas de origem|Destino|Portas de destino|Protocolo|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Qualquer|Permitir|

##### <a name="denyallinbound"></a>DenyAllInbound

|Prioridade|Origem|Portas de origem|Destino|Portas de destino|Protocolo|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Qualquer|Negar|

#### <a name="outbound"></a>Saída

##### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Prioridade|Origem|Portas de origem| Destino | Portas de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Qualquer | Permitir |

##### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Prioridade|Origem|Portas de origem| Destino | Portas de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Qualquer | Permitir |

##### <a name="denyalloutbound"></a>DenyAllOutBound

|Prioridade|Origem|Portas de origem| Destino | Portas de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Qualquer | Negar |

Nas colunas **Source** (Origem) e **Destination** (Destino), *VirtualNetwork*, *AzureLoadBalancer* e *Internet* são [etiquetas de serviço](service-tags-overview.md) e não endereços IP. Na coluna do protocolo, **qualquer abarca** TCP, UDP e ICMP. Ao criar uma regra, pode especificar TCP, UDP, ICMP ou Qualquer. *0.0.0.0/0* nas colunas **Source** e **Destination** representa todos os endereços. Clientes como o portal Azure, Azure CLI ou PowerShell podem usar * ou qualquer um para esta expressão.
 
Não pode remover as regras predefinidas, mas pode criar regras com prioridades superiores para substituí-las.

### <a name="augmented-security-rules"></a><a name="augmented-security-rules"></a> Regras de segurança aumentadas

As regras de segurança aumentadas simplificam a definição de segurança das redes virtuais, permitindo-lhe definir políticas de segurança de rede maiores e mais complexas com menos regras. Pode combinar várias portas e vários endereços IP explícitos e intervalos numa regra de segurança individual e facilmente compreendida. Utilize as regras aumentadas nos campos de origem, destino e porta das regras. Para simplificar a manutenção da definição de segurança de rede, combine regras de segurança aumentadas com [etiquetas de serviço](service-tags-overview.md) ou [grupos de segurança de aplicações](#application-security-groups). Existem limites para o número de endereços, intervalos e portas que pode especificar numa regra. Para obter mais detalhes, veja [Limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

#### <a name="service-tags"></a>Etiquetas de serviço

Uma etiqueta de serviço representa um grupo de prefixos de endereço IP de um determinado serviço Azure. Ajuda a minimizar a complexidade de atualizações frequentes sobre as regras de segurança da rede.

Para mais informações, consulte [as etiquetas de serviço da Azure.](service-tags-overview.md) Para um exemplo sobre como utilizar a etiqueta de serviço de Armazenamento para restringir o acesso à rede, consulte [restringir o acesso da rede aos recursos paaS](tutorial-restrict-network-access-to-resources.md).

#### <a name="application-security-groups"></a>Grupos de segurança de aplicações

Os grupos de segurança de aplicações permitem-lhe configurar a segurança de rede como uma extensão natural da estrutura de uma aplicação, possibilitando o agrupamento de máquinas virtuais e a definição de políticas de segurança de rede com base nesses grupos. Pode reutilizar a política de segurança em escala sem a manutenção manual de endereços IP explícitos. Para saber mais, consulte [os grupos de segurança da Aplicação.](application-security-groups.md)

## <a name="azure-platform-considerations"></a>Considerações sobre a plataforma do Azure

- **IP virtual do nó de anfitrião**: Serviços básicos de infraestrutura como DHCP, DNS, IMDS e monitorização de saúde são fornecidos através dos endereços IP de hospedeiro virtualizado 168.63.129.16 e 169.254.169.254. Estes endereços IP pertencem à Microsoft e são os únicos endereços IP virtualizados utilizados em todas as regiões para este fim. Regras de segurança eficazes e rotas eficazes não incluirão estas regras da plataforma. Para anular esta comunicação básica de infraestrutura, pode criar uma regra de segurança para negar o tráfego utilizando as [seguintes etiquetas](service-tags-overview.md) de serviço nas regras do Grupo de Segurança da Rede: AzurePlatformDNS, AzurePlatformIMDS, AzurePlatformLKM. Saiba como [diagnosticar a filtragem do tráfego da rede](diagnose-network-traffic-filter-problem.md) e diagnosticar o [encaminhamento da rede](diagnose-network-routing-problem.md).
- **Licenciamento (Serviço de Gestão de Chaves):** as imagens do Windows em execução nas máquinas virtuais têm de ser licenciadas. Para garantir o licenciamento, é enviado um pedido para os servidores de anfitrião do Key Management Service que processam estas consultas. O pedido é feito através da porta 1688 de saída. Para implementações que utilizam a configuração de [rota predefinida 0.0.0.0/0](virtual-networks-udr-overview.md#default-route), esta regra de plataforma será desativada.
- **Máquinas virtuais em conjuntos com balanceamento de carga**: a porta de destino e o intervalo de endereços aplicados são provenientes do computador de origem, não do balanceador de carga. A porta de destino e o intervalo de endereços destinam-se ao computador de destino, não ao balanceador de carga.
- **Instâncias de serviço do Azure**: as instâncias de vários serviços do Azure, como o HDInsight, os Ambientes de Serviço de Aplicações e os Conjuntos de Dimensionamento de Máquinas Virtuais, são implementados em sub-redes da rede virtual. Para obter uma lista completa dos serviços que pode implementar em redes virtuais, veja [Rede virtual para os serviços do Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Certifique-se de que se familiariza com os requisitos de portas de cada serviço antes de aplicar um grupo de segurança de rede à sub-rede na qual o recurso está implementado. Se negar portas de que os serviços precisam, estes não funcionarão corretamente.
- **Envio de e-mail de saída**: A Microsoft recomenda que utilize serviços de retransmissão SMTP autenticados (normalmente ligados através da porta TCP 587, mas muitas vezes outros, bem como) para enviar e-mails de Máquinas Virtuais Azure. Os serviços de reencaminhamento de SMTP especializam-se na reputação do remetente, para minimizar a possibilidade de os fornecedores de e-mail de terceiros rejeitarem mensagens. Esses serviços de reencaminhamento de SMTP incluem, sem estarem limitados, o Exchange Online Protection e o SendGrid. A utilização dos serviços de reencaminhamento de SMTP não está de forma alguma restringida no Azure, independentemente do seu tipo de subscrição. 

  Se tiver criado a sua subscrição do Azure antes de 15 de novembro de 2017, para além de poder utilizar os serviços de reencaminhamento de SMTP, também pode enviar e-mails diretamente através da porta TCP 25. Se tiver criado a sua subscrição após 15 de novembro de 2017, poderá não conseguir enviar e-mails diretamente através da porta 25. O comportamento da comunicação de saída através da porta 25 depende do seu tipo de subscrição, da seguinte forma:

     - **Contrato Enterprise**: a comunicação de saída através da porta 25 é permitida. É possível enviar um e-mail de saída diretamente de máquinas virtuais para fornecedores de e-mail externos, sem restrições da plataforma Azure. 
     - **Pay as you go:** a comunicação de saída através da porta 25 está bloqueada a partir de todos os recursos. Se tiver de enviar e-mails a partir de uma máquina virtual diretamente para fornecedores de e-mail externos (sem utilizar um reencaminhamento de SMTP autenticado), pode fazer um pedido para remover a restrição. Os pedidos são analisados e aprovados à discrição da Microsoft, apenas sendo concedidos após as verificações contra fraudes. Para fazer um pedido, abra um processo de suporte com o tipo de problema *Técnico*, *Conectividade da Rede Virtual*, *Não é possível enviar e-mails (SMTP/Porta 25)*. No processo de suporte, inclua detalhes sobre o motivo pelo qual a subscrição tem de enviar e-mails diretamente para fornecedores de e-mail, em vez de utilizar um reencaminhamento de SMTP autenticado. Se a sua subscrição for isenta, apenas as máquinas virtuais criadas após a data de isenção conseguem realizar comunicações de saída através da porta 25.
     - **MSDN, Azure Pass, Azure no Open, Education, BizSpark e Avaliação gratuita**: a comunicação de saída através da porta 25 está bloqueada a partir de todos os recursos. Não pode fazer pedidos para remover a restrição, porque os pedidos não são concedidos. Se tiver de enviar e-mails a partir da sua máquina virtual, tem de utilizar um serviço de reencaminhamento de SMTP.
     - **Fornecedor de serviços cloud**: Os clientes que consomem recursos do Azure através de um fornecedor de serviços cloud podem criar um pedido de suporte no respetivo fornecedor de serviços cloud e pedir que o fornecedor crie um pedido de desbloqueio em seu nome, se não for possível utilizar uma transmissão SMTP segura.

  Se o Azure lhe permitir enviar e-mails através da porta 25, a Microsoft não pode garantir que os fornecedores de e-mail aceitarão e-mails enviados a partir da sua máquina virtual. Se um fornecedor específico rejeitar e-mails da sua máquina virtual, trabalhe diretamente com o fornecedor para resolver quaisquer problemas relacionados com a entrega de mensagens ou com a filtragem de spam, ou tem de utilizar um serviço de reencaminhamento de SMTP autenticado.

## <a name="next-steps"></a>Passos seguintes

* Para saber quais os recursos Azure que podem ser implantados numa rede virtual e ter grupos de segurança de rede associados a eles, consulte a [integração da rede Virtual para serviços Azure](virtual-network-for-azure-services.md)
* Para saber como o tráfego é avaliado com grupos de segurança de rede, veja [como funcionam os grupos de segurança da rede.](network-security-group-how-it-works.md)
* Se nunca tiver criado um grupo de segurança de rede, pode seguir um [tutorial](tutorial-filter-network-traffic.md) rápido para ganhar experiência na criação de um.
* Se estiver familiarizado com os grupos de segurança de rede e tiver de geri-los, veja [Manage a network security group](manage-network-security-group.md) (Gerir um grupo de segurança de rede). 
* Se estiver com problemas de comunicação e precisar de resolver problemas nos grupos de segurança de rede, veja [Diagnose a virtual machine network traffic filter problem](diagnose-network-traffic-filter-problem.md) (Diagnosticar problemas de filtro de tráfego de rede de uma máquina virtual). 
* Saiba como permitir [que os registos de fluxo de grupos](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de segurança de rede analisem o tráfego de rede de e para recursos que tenham um grupo de segurança de rede associado.
