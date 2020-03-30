---
title: Visão geral dos grupos de segurança da rede Azure
titlesuffix: Azure Virtual Network
description: Conheça os grupos de segurança da rede. Os grupos de segurança da rede ajudam-no a filtrar o tráfego da rede entre os recursos do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 8f3497f113981ae563023750ad8979c88c640f5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123341"
---
# <a name="network-security-groups"></a>Grupos de segurança de rede
<a name="network-security-groups"></a>

Pode filtrar o tráfego de rede de e para recursos do Azure numa rede virtual do Azure com um grupo de segurança de rede. Os grupos de segurança de rede contêm regras de segurança que permitem ou negam o tráfego de entrada ou de saída de e para vários tipos de recursos do Azure. Para saber que recursos do Azure podem ser implementados numa rede virtual e associar grupos de segurança de rede aos mesmos, veja [Virtual network integration for Azure services](virtual-network-for-azure-services.md) (Integração da rede virtual para serviços do Azure). Para cada regra, pode especificar a origem e o destino, a porta e o protocolo.

Este artigo explica os conceitos dos grupos de segurança de rede para ajudá-lo a utilizá-los eficientemente. Se nunca tiver criado um grupo de segurança de rede, pode seguir um [tutorial](tutorial-filter-network-traffic.md) rápido para ganhar experiência na criação de um. Se estiver familiarizado com os grupos de segurança de rede e tiver de geri-los, veja [Manage a network security group](manage-network-security-group.md) (Gerir um grupo de segurança de rede). Se estiver com problemas de comunicação e precisar de resolver problemas nos grupos de segurança de rede, veja [Diagnose a virtual machine network traffic filter problem](diagnose-network-traffic-filter-problem.md) (Diagnosticar problemas de filtro de tráfego de rede de uma máquina virtual). Pode ativar [registos de fluxos de grupos de segurança de rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para analisar o tráfego de rede de e para recursos que têm um grupo de segurança de rede associado.

## <a name="security-rules"></a>Regras de segurança

Os grupos de segurança de rede contêm zero ou tantas regras conforme pretender, dentro dos [limites](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) das subscrições do Azure. Cada regra especifica as propriedades seguintes:

|Propriedade  |Explicação  |
|---------|---------|
|Nome|Um nome exclusivo no grupo de segurança de rede.|
|Prioridade | Um número entre 100 e 4096. As regras são processadas por ordem de prioridade, sendo os números mais baixos processados antes dos mais elevados, uma vez que têm uma prioridade superior. Quando o tráfego corresponder a uma regra, o processamento para. Como resultado, qualquer regra que exista com prioridades inferiores (números mais elevados) e que tenham os mesmos atributos das regras com prioridades superiores não é processada.|
|Origem ou destino| Qualquer endereço IP ou um endereço IP individual, um bloco CIDR (Classless Inter-domain Routing) (por exemplo, 10.0.0.0/24), [etiqueta de serviço](service-tags-overview.md) ou [grupo de segurança de aplicações](#application-security-groups). Se especificar um endereço para um recurso do Azure, indique o endereço IP privado atribuído ao mesmo. Os grupos de segurança de rede são processados depois de o Azure traduzir um endereço IP público num privado para tráfego de entrada e antes de traduzir um endereço IP privado num público para tráfego de saída. Saiba mais sobre os [endereços IP](virtual-network-ip-addresses-overview-arm.md) do Azure. Especificar um intervalo, uma etiqueta de serviço ou um grupo de segurança de aplicações permite-lhe criar menos regras de segurança. A capacidade de especificar vários endereços IP individuais e intervalos de endereços IP (não pode indicar etiquetas de serviço ou grupos de aplicações) numa regra é denominada [regras de segurança aumentadas](#augmented-security-rules). As regras de segurança aumentadas só podem ser criadas em grupos de segurança de rede gerados através do modelo de implementação do Resource Manager. Não pode especificar vários endereços IP nem intervalos de endereços IP em grupos de segurança de rede criados com o modelo de implementação clássica. Saiba mais sobre os [modelos de implementação do Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|Protocolo     | TCP, UDP, ICMP ou Any.|
|Direção| Indica se a regra se aplica a tráfego de entrada ou de saída.|
|Intervalo de portas     |Pode especificar uma porta individual ou um intervalo de portas. Por exemplo, pode indicar 80 ou 10000-10005. Especificar intervalos permite-lhe criar menos regras de segurança. As regras de segurança aumentadas só podem ser criadas em grupos de segurança de rede gerados através do modelo de implementação do Resource Manager. Não pode especificar várias portas nem intervalos de portas na mesma regra de segurança em grupos de segurança de rede criados com o modelo de implementação clássica.   |
|Ação     | Permitir ou negar        |

As regras de segurança dos grupos de segurança de rede são avaliadas por prioridade, utilizando as informações de cinco cadeias de identificação (origem, porta de origem, destino, porta de destino e protocolo) para permitir ou negar o tráfego. É criado um registo de fluxo para as ligações existentes. A comunicação é permitida ou negada com base no estado da ligação do registo do fluxo. O registo do fluxo permite que um grupo de segurança de rede tenha monitoração de estado. Se especificar uma regra de segurança de saída para qualquer endereço através da porta 80, por exemplo, não é necessário especificar uma regra de segurança de entrada para a resposta ao tráfego de saída. Só tem de especificar uma regra de segurança de entrada se a comunicação for iniciada externamente. O oposto também se aplica. Se o tráfego de entrada for permitido numa porta, não é necessário especificar uma regra de segurança de saída para responder ao tráfego através da porta.
As ligações existentes não podem ser interrompidas quando remover uma regra de segurança que ativou o fluxo. Os fluxos de tráfego são interrompidos quando as ligações são paradas e não há qualquer tráfego a fluir em qualquer direção, pelo menos, durante alguns minutos.

Há limites ao número de regras de segurança que pode criar num grupo de segurança de rede. Para obter mais detalhes, veja [Limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

### <a name="default-security-rules"></a>Regras de segurança predefinidas

O Azure cria as seguintes regras predefinidas em cada grupo de segurança de rede que criar:

#### <a name="inbound"></a>Entrada

##### <a name="allowvnetinbound"></a>AllowVNetInBound

|Prioridade|Origem|Portas de origem|Destino|Portas de destino|Protocolo|Acesso|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Qualquer|Permitir|

##### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Prioridade|Origem|Portas de origem|Destino|Portas de destino|Protocolo|Acesso|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Qualquer|Permitir|

##### <a name="denyallinbound"></a>DenyAllInbound

|Prioridade|Origem|Portas de origem|Destino|Portas de destino|Protocolo|Acesso|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Qualquer|Negar|

#### <a name="outbound"></a>Saída

##### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Prioridade|Origem|Portas de origem| Destino | Portas de destino | Protocolo | Acesso |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Qualquer | Permitir |

##### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Prioridade|Origem|Portas de origem| Destino | Portas de destino | Protocolo | Acesso |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Qualquer | Permitir |

##### <a name="denyalloutbound"></a>DenyAllOutBound

|Prioridade|Origem|Portas de origem| Destino | Portas de destino | Protocolo | Acesso |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Qualquer | Negar |

Nas colunas **Source** (Origem) e **Destination** (Destino), *VirtualNetwork*, *AzureLoadBalancer* e *Internet* são [etiquetas de serviço](service-tags-overview.md) e não endereços IP. Na coluna do protocolo, **qualquer** engloba TCP, UDP e ICMP. Ao criar uma regra, pode especificar TCP, UDP, ICMP ou Any. *0.0.0.0/0* nas colunas **Source** e **Destination** representa todos os endereços. Clientes como o portal Azure, O ClI Azure ou powerShell podem usar * ou qualquer outro para esta expressão.
 
Não pode remover as regras predefinidas, mas pode criar regras com prioridades superiores para substituí-las.

### <a name="augmented-security-rules"></a>Regras de segurança aumentadas

As regras de segurança aumentadas simplificam a definição de segurança das redes virtuais, permitindo-lhe definir políticas de segurança de rede maiores e mais complexas com menos regras. Pode combinar várias portas e vários endereços IP explícitos e intervalos numa regra de segurança individual e facilmente compreendida. Utilize as regras aumentadas nos campos de origem, destino e porta das regras. Para simplificar a manutenção da definição de segurança de rede, combine regras de segurança aumentadas com [etiquetas de serviço](service-tags-overview.md) ou [grupos de segurança de aplicações](#application-security-groups). Existem limites para o número de endereços, gamas e portas que pode especificar numa regra. Para obter mais detalhes, veja [Limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

#### <a name="service-tags"></a>Etiquetas de serviço

Uma etiqueta de serviço representa um grupo de prefixos de endereço IP de um determinado serviço Azure. Ajuda a minimizar a complexidade das atualizações frequentes sobre as regras de segurança da rede.

Para mais informações, consulte as etiquetas de [serviço Azure.](service-tags-overview.md) Para um exemplo sobre como usar a etiqueta do serviço de armazenamento para restringir o acesso à rede, consulte Restringir o [acesso da rede aos recursos PaaS](tutorial-restrict-network-access-to-resources.md).

#### <a name="application-security-groups"></a>Grupos de segurança de aplicações

Os grupos de segurança de aplicações permitem-lhe configurar a segurança de rede como uma extensão natural da estrutura de uma aplicação, possibilitando o agrupamento de máquinas virtuais e a definição de políticas de segurança de rede com base nesses grupos. Pode reutilizar a política de segurança em escala sem a manutenção manual de endereços IP explícitos. Para saber mais, consulte [os grupos de segurança da Aplicação](application-security-groups.md).

## <a name="how-traffic-is-evaluated"></a>Como o tráfego é avaliado

Pode implementar recursos de vários serviços do Azure numa rede virtual do Azure. Para obter uma lista completa, veja [Services that can be deployed into a virtual network](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (Serviços que podem ser implementados numa rede virtual). Pode associar nenhum ou um grupo de segurança de rede à [sub-rede](virtual-network-manage-subnet.md#change-subnet-settings) e à [interface de rede](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) de cada rede virtual numa máquina virtual. O mesmo grupo de segurança de rede pode ser associado a tantas sub-redes e interfaces de rede que escolher.

A imagem seguinte ilustra diferentes cenários de implementação dos grupos de segurança de rede para permitir o tráfego de rede de e para a Internet a partir da porta TCP 80:

![NSG-processing](./media/security-groups/nsg-interaction.png)

Veja a imagem anterior, juntamente com o texto seguinte, para compreender de que forma é que o Azure processa as regras de entrada e de saída para os grupos de segurança de rede:

### <a name="inbound-traffic"></a>Tráfego de entrada

Relativamente ao tráfego de entrada, o Azure processa primeiro as regras num grupo de segurança de rede associado a uma sub-rede, se existir alguma, e, depois, as regras num grupo de segurança de rede associado à interface de rede, se existir alguma.

- **VM1**: as regras de segurança em *NSG1* são processadas, uma vez que está associado a *Subnet1* e *VM1* está em *Subnet1*. A menos que tenha criado uma regra que permita a porta de entrada 80, o tráfego é negado pela regra de segurança predefinida [DenyAllInbound](#denyallinbound) e nunca é avaliado por *NSG2*, pois *NSG2* está associado à interface de rede. Se *NSG1* tiver uma regra de segurança que permite a porta 80, o tráfego é processado por *NSG2*. Para permitir a porta 80 na máquina virtual, tanto *NSG1*, como *NSG2*, têm de ter uma regra que permite a porta 80 a partir da Internet.
- **VM2**: as regras em *NSG1* são processadas, porque *VM2* também está em *Subnet1*. Uma vez que *VM2* não tem um grupo de segurança de rede associado à respetiva interface de rede, recebe todo o tráfego permitido através *NSG1* ou é-lhe negado todo o tráfego negado por *NSG1*. O tráfego é permitido ou negado para todos os recursos na mesma sub-rede se um grupo de segurança de rede estiver associado a uma sub-rede.
- **VM3**: uma vez que não existe nenhum grupo de segurança de rede associado a *Subnet2*, o tráfego é permitido na sub-rede e processado por *NSG2*, pois *NSG2* está associado à interface de rede anexada a *VM3*.
- **VM4**: o tráfego é permitido para *VM4,* porque não existe nenhum grupo de segurança de rede associado a *Subnet3* ou à interface de rede na máquina virtual. Todo o tráfego de rede é permitido através de uma sub-rede e de uma interface de rede se não houver nenhum grupo de segurança de rede associado às mesmas.

### <a name="outbound-traffic"></a>Tráfego de saída

Relativamente ao tráfego de saída, o Azure processa primeiro as regras num grupo de segurança de rede associado a uma interface de rede, se existir alguma, e, depois, as regras num grupo de segurança de rede associado à sub-rede, se existir alguma.

- **VM1**: as regras de segurança em *NSG2* são processadas. A menos que crie uma regra de segurança que negue a porta 80 de saída para a Internet, o tráfego é permitido pela regra de segurança predefinida [AllowInternetOutbound](#allowinternetoutbound) em *NSG1* e *NSG2*. Se *NSG2* tiver uma regra de segurança que negue a porta 80, o tráfego é negado e nunca avaliado por *NSG1*. Para negar a porta 80 a partir da máquina virtual, um ou ambos os grupos de segurança de rede têm de ter uma regra que negue a porta 80 para a Internet.
- **VM2**: todo o tráfego é enviado da interface de rede para a sub-rede, uma vez que a interface de rede anexada a *VM2* não tem um grupo de segurança de rede associado a si. As regras em *NSG1* são processadas.
- **VM3**: se *NSG2* tiver uma regra de segurança que negue a porta 80, o tráfego é negado. Se *NSG2* tiver uma regra de segurança que permita a porta 80, é permitido tráfego de saída para a Internet na mesma, uma vez que não existe nenhum grupo de segurança de rede associado a *Subnet2*.
- **VM4**: todo o tráfego de rede é permitido a partir de *VM4,* porque não está associado nenhum grupo de segurança de rede à interface de rede anexada à máquina virtual ou a *Subnet3*.


### <a name="intra-subnet-traffic"></a>Tráfego intra-subnet

É importante notar que as regras de segurança numa NSG associadas a uma subnet podem afetar a conectividade entre os VM's dentro dela. Por exemplo, se for adicionada uma regra ao *NSG1* que negue todo o tráfego de entrada e saída, o *VM1* e o *VM2* deixarão de poder comunicar entre si. Outra regra teria de ser acrescentada especificamente para o permitir. 



Pode ver as [regras de segurança em vigor](virtual-network-network-interface.md#view-effective-security-rules) numa interface de rede para ver facilmente as regras agregadas que estão aplicadas à mesma. Também pode utilizar a capacidade [Verificação de fluxo de IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Observador de Rede do Azure para saber se a comunicação é permitida de ou para uma interface de rede. O fluxo de IP indica se a comunicação é permitida ou negada e que regra de segurança de rede permite ou nega o tráfego.

> [!NOTE]
> Os grupos de segurança da rede estão associados a subredes ou a máquinas virtuais e serviços de nuvem implantados no modelo clássico de implementação, bem como a subredes ou interfaces de rede no modelo de implementação do Gestor de Recursos. Para saber mais sobre os modelos de implementação do Azure, veja [Understand Azure deployment models](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Compreender os modelos de implementação do Azure).

> [!TIP]
> A menos que tenha um motivo específico, recomendamos que associe um grupo de segurança de rede a uma sub-rede ou a uma interface de rede, mas não a ambas. Uma vez que as regras num grupo de segurança de rede associado a uma sub-rede podem entrar em conflito com as regras num grupo associado a uma interface de rede, poderão ocorrer problemas de comunicação inesperados que exijam resolução.

## <a name="azure-platform-considerations"></a>Considerações sobre a plataforma do Azure

- **IP virtual do nó anfitrião**: Serviços básicos de infraestrutura como DHCP, DNS, IMDS e monitorização da saúde são fornecidos através dos endereços IP de acolhimento virtualizados 168.63.129.16 e 169.254.169.254. Estes endereços IP pertencem à Microsoft e são os únicos endereços IP virtualizados utilizados em todas as regiões para o efeito. Regras de segurança eficazes e rotas eficazes não incluirão estas regras da plataforma. Para anular esta comunicação básica de infraestrutura, pode criar uma regra de segurança para negar o tráfego utilizando as [seguintes etiquetas](service-tags-overview.md) de serviço nas suas regras do Grupo de Segurança da Rede: AzurePlatformDNS, AzurePlatformIMDS, AzurePlatformLKM. Saiba como [diagnosticar a filtragem](diagnose-network-traffic-filter-problem.md) do tráfego da rede e [diagnosticar o encaminhamento da rede](diagnose-network-routing-problem.md).
- **Licenciamento (Serviço de Gestão de Chaves):** as imagens do Windows em execução nas máquinas virtuais têm de ser licenciadas. Para garantir o licenciamento, é enviado um pedido para os servidores de anfitrião do Key Management Service que processam estas consultas. O pedido é feito através da porta 1688 de saída. Para implementações que utilizam a configuração de [rota predefinida 0.0.0.0/0](virtual-networks-udr-overview.md#default-route), esta regra de plataforma será desativada.
- **Máquinas virtuais em conjuntos com balanceamento de carga**: a porta de destino e o intervalo de endereços aplicados são provenientes do computador de origem, não do balanceador de carga. A porta de destino e o intervalo de endereços destinam-se ao computador de destino, não ao balanceador de carga.
- **Instâncias de serviço do Azure**: as instâncias de vários serviços do Azure, como o HDInsight, os Ambientes de Serviço de Aplicações e os Conjuntos de Dimensionamento de Máquinas Virtuais, são implementados em sub-redes da rede virtual. Para obter uma lista completa dos serviços que pode implementar em redes virtuais, veja [Rede virtual para os serviços do Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Certifique-se de que se familiariza com os requisitos de portas de cada serviço antes de aplicar um grupo de segurança de rede à sub-rede na qual o recurso está implementado. Se negar portas de que os serviços precisam, estes não funcionarão corretamente.
- **Envio de e-mail de saída**: A Microsoft recomenda que utilize serviços de retransmissão SMTP autenticados (normalmente ligados através da porta TCP 587, mas muitas vezes outros, também) para enviar e-mail a partir de Máquinas Virtuais Azure. Os serviços de reencaminhamento de SMTP especializam-se na reputação do remetente, para minimizar a possibilidade de os fornecedores de e-mail de terceiros rejeitarem mensagens. Esses serviços de reencaminhamento de SMTP incluem, sem estarem limitados, o Exchange Online Protection e o SendGrid. A utilização dos serviços de reencaminhamento de SMTP não está de forma alguma restringida no Azure, independentemente do seu tipo de subscrição. 

  Se tiver criado a sua subscrição do Azure antes de 15 de novembro de 2017, para além de poder utilizar os serviços de reencaminhamento de SMTP, também pode enviar e-mails diretamente através da porta TCP 25. Se tiver criado a sua subscrição após 15 de novembro de 2017, poderá não conseguir enviar e-mails diretamente através da porta 25. O comportamento da comunicação de saída através da porta 25 depende do seu tipo de subscrição, da seguinte forma:

     - **Contrato Enterprise**: a comunicação de saída através da porta 25 é permitida. Pode enviar e-mails de saída diretamente a partir de máquinas virtuais para fornecedores de e-mail externos, sem restrições impostas pela plataforma do Azure. 
     - **Pay as you go:** a comunicação de saída através da porta 25 está bloqueada a partir de todos os recursos. Se tiver de enviar e-mails a partir de uma máquina virtual diretamente para fornecedores de e-mail externos (sem utilizar um reencaminhamento de SMTP autenticado), pode fazer um pedido para remover a restrição. Os pedidos são analisados e aprovados à discrição da Microsoft, apenas sendo concedidos após as verificações contra fraudes. Para fazer um pedido, abra um processo de suporte com o tipo de problema *Técnico*, *Conectividade da Rede Virtual*, *Não é possível enviar e-mails (SMTP/Porta 25)*. No processo de suporte, inclua detalhes sobre o motivo pelo qual a subscrição tem de enviar e-mails diretamente para fornecedores de e-mail, em vez de utilizar um reencaminhamento de SMTP autenticado. Se a sua subscrição for isenta, apenas as máquinas virtuais criadas após a data de isenção conseguem realizar comunicações de saída através da porta 25.
     - **MSDN, Azure Pass, Azure no Open, Education, BizSpark e Avaliação gratuita**: a comunicação de saída através da porta 25 está bloqueada a partir de todos os recursos. Não pode fazer pedidos para remover a restrição, porque os pedidos não são concedidos. Se tiver de enviar e-mails a partir da sua máquina virtual, tem de utilizar um serviço de reencaminhamento de SMTP.
     - **Fornecedor de serviços cloud**: Os clientes que consomem recursos do Azure através de um fornecedor de serviços cloud podem criar um pedido de suporte no respetivo fornecedor de serviços cloud e pedir que o fornecedor crie um pedido de desbloqueio em seu nome, se não for possível utilizar uma transmissão SMTP segura.

  Se o Azure lhe permitir enviar e-mails através da porta 25, a Microsoft não pode garantir que os fornecedores de e-mail aceitarão e-mails enviados a partir da sua máquina virtual. Se um fornecedor específico rejeitar e-mails da sua máquina virtual, trabalhe diretamente com o fornecedor para resolver quaisquer problemas relacionados com a entrega de mensagens ou com a filtragem de spam, ou tem de utilizar um serviço de reencaminhamento de SMTP autenticado.

## <a name="next-steps"></a>Passos seguintes

* Leia o artigo [Create a network security group](tutorial-filter-network-traffic.md) (Criar um grupo de segurança de rede).
