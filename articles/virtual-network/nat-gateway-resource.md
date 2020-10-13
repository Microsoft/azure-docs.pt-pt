---
title: Projetar redes virtuais com recursos de gateway NAT
titleSuffix: Azure Virtual Network NAT
description: Saiba como desenhar redes virtuais com recursos de gateway NAT.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
Customer intent: As an IT administrator, I want to learn more about how to design virtual networks with NAT gateway resources.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: allensu
ms.openlocfilehash: 62c1b323899f03a043904f4b10d5fe3bb551e0f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91441761"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources"></a>Projetar redes virtuais com recursos de gateway NAT

Os recursos de gateway NAT fazem parte da [Rede Virtual NAT](nat-overview.md) e fornecem conectividade de saída à Internet para uma ou mais sub-redes de uma rede virtual. A sub-rede da rede virtual indica quais o gateway NAT que será utilizado. A NAT fornece tradução de endereço de rede de origem (SNAT) para uma sub-rede.  Os recursos de gateway NAT especificam quais endereços IP estáticos utilizam máquinas virtuais ao criar fluxos de saída. Os endereços IP estáticos provêm de recursos de endereços IP públicos (PIP), recursos prefixos IP públicos, ou ambos. Se for utilizado um recurso prefixo IP público, todos os endereços IP de todo o recurso prefixo IP público são consumidos por um recurso de gateway NAT. Um recurso de gateway NAT pode usar um total de até 16 endereços IP estáticos de qualquer um dos dois.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of virtual machines and a virtual machine scale set." width="256" title="Rede Virtual NAT para saída para a Internet">
</p>

*Figura: Rede Virtual NAT para saída para a Internet*

## <a name="how-to-deploy-nat"></a>Como implantar o NAT

Configurar e utilizar o gateway NAT é intencionalmente simplificado:  

Recurso de gateway NAT:
- Criar recurso de gateway NAT regional ou zonal (isolado em zona),
- Atribuir endereços IP,
- Se necessário, modifique o tempo limite de marcha lenta (opcional) da TCP.  Reveja [os temporizadores](#timers) <ins>antes</ins> de alterar o padrão.

Rede virtual:
- Configure a sub-rede de rede virtual para utilizar um gateway NAT.

As rotas definidas pelo utilizador não são necessárias.

## <a name="resource"></a>Recurso

O recurso foi concebido para ser simples como pode ver a partir do exemplo do Gestor de Recursos Azure num formato semelhante ao do modelo.  Este formato semelhante ao modelo é mostrado aqui para ilustrar os conceitos e estrutura.  Modifique o exemplo para as suas necessidades.  Este documento não se destina a ser tutorial.

O diagrama seguinte mostra as referências escritas entre os diferentes recursos do Gestor de Recursos Azure.  A seta indica a direção da referência, originária de onde é escrita. Revisão 

<p align="center">
  <img src="media/nat-overview/flow-map.svg" alt="Figure depicts a NAT receiving traffic from internal subnets and directing it to a public IP and an IP prefix." width="256" title="Modelo de objeto NAT de rede virtual">
</p>

*Figura: Modelo de objeto NAT de rede virtual*

O NAT é recomendado para a maioria das cargas de trabalho, a menos que tenha uma dependência específica da [conectividade de saída do Balancer de Carga baseada na piscina.](../load-balancer/load-balancer-outbound-connections.md)  

Você pode migrar de cenários de balanceadores de carga padrão, incluindo [regras de saída,](../load-balancer/load-balancer-outbound-rules-overview.md)para o gateway NAT. Para migrar, mova os recursos ip e prefixos ip públicos de frontends de balançadores de carga para gateway NAT. Não são necessários novos endereços IP para gateway NAT. Os recursos de endereço IP público padrão e o recurso prefixo IP público podem ser reutilizados desde que o total não exceda 16 endereços IP. Plano de migração com interrupção de serviço em mente durante a transição.  Pode minimizar a interrupção automatizando o processo. Teste a migração em um ambiente de preparação primeiro.  Durante a transição, os fluxos originados de entrada não são afetados.


O exemplo a seguir é um corte de um modelo de Gestor de Recursos Azure.  Este modelo implementa vários recursos, incluindo uma porta de entrada NAT.  O modelo tem os seguintes parâmetros neste exemplo:

- **natgatewayname** - Nome do portal NAT.
- **localização** - Região de Azure onde o recurso está localizado.
- **nome publicitário** - Nome do IP público de saída associado à porta de entrada NAT.
- **vnetname** - Nome da rede virtual.
- **subnetname** - Nome da sub-rede associada ao gateway NAT.

O número total de endereços IP fornecidos por todos os endereços IP e recursos prefixos não pode exceder 16 endereços IP no total. Qualquer número de endereços IP entre 1 e 16 é permitido.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="81-96":::

Quando o recurso de gateway NAT foi criado, pode ser usado em uma ou mais sub-redes de uma rede virtual. Especifique quais sub-redes utilizam este recurso de gateway NAT. Um portal NAT não é capaz de abranger mais do que uma rede virtual. Não é necessário atribuir o mesmo portal NAT a todas as sub-redes de uma rede virtual. As sub-redes individuais podem ser configuradas com diferentes recursos de gateway NAT.

Os cenários que não utilizarem zonas de disponibilidade serão regionais (nenhuma zona especificada). Se estiver a utilizar zonas de disponibilidade, pode especificar uma zona para isolar o NAT numa zona específica. A redundância de zona não é apoiada. Rever [as zonas de disponibilidade do](#availability-zones)NAT.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="1-146" highlight="81-96":::

Os gateways NAT são definidos com uma propriedade numa sub-rede dentro de uma rede virtual. Os fluxos criados por máquinas virtuais no **subnetname da subnetname** da rede virtual **vnetname** utilizarão o gateway NAT. Toda a conectividade de saída utilizará os endereços IP associados ao **nome natgateway** como endereço IP de origem.

Para obter mais informações sobre o modelo Azure Resource Manager utilizado neste exemplo, consulte:

- [Quickstart: Criar uma porta de entrada NAT - modelo de gestor de recursos](quickstart-create-nat-gateway-template.md)
- [Rede Virtual NAT](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm/)

## <a name="design-guidance"></a>Orientação de design

Reveja esta secção para se familiarizar com considerações para conceber redes virtuais com NAT.  

1. [Otimização de custos](#cost-optimization)
1. [Coexistência de entrada e saída](#coexistence-of-inbound-and-outbound)
2. [Gestão de recursos básicos](#managing-basic-resources)
3. [Zonas de Disponibilidade](#availability-zones)

### <a name="cost-optimization"></a>Otimização de custos

[Os pontos finais de serviço](virtual-network-service-endpoints-overview.md) e [o link privado](../private-link/private-link-overview.md) são opções a considerar para otimizar o custo. A NAT não é necessária para estes serviços. O tráfego direcionado para pontos finais de serviço ou ligação privada não é processado pelo NAT da rede virtual.  

Os pontos finais do serviço ligam os recursos de serviço da Azure à sua rede virtual e controlam o acesso aos seus recursos de serviço Azure. Por exemplo, quando aceder ao armazenamento do Azure, utilize um ponto final de serviço para armazenamento para evitar encargos NAT processados por dados. Os pontos finais de serviço são gratuitos.

O link privado expõe o serviço Azure PaaS (ou outros serviços alojados com ligação privada) como um ponto final privado dentro de uma rede virtual.  O link privado é faturado com base na duração e nos dados processados.

Avalie se qualquer uma ou ambas as abordagens são adequadas para o seu cenário e usar conforme necessário.

### <a name="coexistence-of-inbound-and-outbound"></a>Coexistência de entrada e saída

O gateway NAT é compatível com:

 - Equilibrador de carga padrão
 - IP público padrão
 - Prefixo IP público padrão

Ao desenvolver uma nova implementação, comece com SKUs padrão.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network." width="256" title="Rede Virtual NAT para saída para a Internet">
</p>

*Figura: Rede Virtual NAT para saída para a Internet*

O único cenário de saída da Internet fornecido pelo GATEWAY NAT pode ser expandido com a entrada da funcionalidade da Internet. Cada recurso está ciente da direção em que um fluxo é originado. Numa sub-rede com uma porta de entrada NAT, todos os cenários de saída para a Internet são substituídos pelo portal NAT. Os cenários de entrada na Internet são fornecidos pelo respetivo recurso.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>NAT e VM com IP público de nível de instância

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP." width="300" title="Rede Virtual NAT e VM com IP público de nível de instância">
</p>

*Figura: Virtual Network NAT e VM com IP público de nível de instância*

| Direção | Recurso |
|:---:|:---:|
| Entrada | VM com IP público de nível de instância |
| Saída | NAT Gateway |

A VM utilizará o gateway NAT para a saída.  A entrada originada não é afetada.

#### <a name="nat-and-vm-with-public-load-balancer"></a>NAT e VM com balanceador de carga público

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with a public load balancer." width="350" title="Rede Virtual NAT e VM com balanceador de carga público">
</p>

*Figura: Rede Virtual NAT e VM com balanceador de carga público*

| Direção | Recurso |
|:---:|:---:|
| Entrada | balanceador de carga pública |
| Saída | NAT Gateway |

Qualquer configuração de saída de uma regra de equilíbrio de carga ou regras de saída é substituído pelo gateway NAT.  A entrada originada não é afetada.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-public-load-balancer"></a>NAT e VM com IP público de nível de instância e balanceador de carga público

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP and a public load balancer." width="425" title="Rede Virtual NAT e VM com IP público de nível de instância e balanceador de carga público">
</p>

*Figura: Rede Virtual NAT e VM com IP público de nível de instância e balanceador de carga público*

| Direção | Recurso |
|:---:|:---:|
| Entrada | VM com IP público de nível de instância e balanceador de carga pública |
| Saída | NAT Gateway |

Qualquer configuração de saída de uma regra de equilíbrio de carga ou regras de saída é substituído pelo gateway NAT.  O VM também utilizará o gateway NAT para saída.  A entrada originada não é afetada.

### <a name="managing-basic-resources"></a>Gestão de recursos básicos

O balanceador de carga padrão, o IP público e o prefixo IP público são compatíveis com o gateway NAT. Os gateways NAT funcionam no âmbito de uma sub-rede. O SKU básico destes serviços deve ser implantado numa sub-rede sem porta de entrada NAT. Esta separação permite que ambas as variantes SKU coexistam na mesma rede virtual.

Os gateways NAT têm precedência sobre os cenários de saída da sub-rede. O balanceador de carga básico ou o IP público (e qualquer serviço gerido construído com eles) não pode ser ajustado com as traduções corretas. O gateway NAT assume o controlo sobre a saída para o tráfego da Internet numa sub-rede. O tráfego de entrada para balanceador de carga básica e ip público não está disponível. O tráfego de entrada para um equilibrador de carga básico e, ou uma IP pública configurada em um VM não estará disponível.

### <a name="availability-zones"></a>Zonas de Disponibilidade

#### <a name="zone-isolation-with-zonal-stacks"></a>Isolamento de zona com pilhas zonais

<p align="center">
  <img src="media/nat-overview/az-directions.svg" alt="Figure depicts three zonal stacks, each of which contains a NAT gateway and a subnet." width="425" title="Rede Virtual NAT com isolamento de zona, criando múltiplos "zonal stacks"">
</p>

*Figura: Rede Virtual NAT com isolamento de zona, criando múltiplas "pilhas zonais"*

Mesmo sem zonas de disponibilidade, o NAT é resistente e pode sobreviver a múltiplas falhas nos componentes da infraestrutura.  As zonas de disponibilidade baseiam-se nesta resiliência com cenários de isolamento de zona para o NAT.

As redes virtuais e as suas sub-redes são construções regionais.  As sub-redes não se restringem a uma zona.

Existe uma promessa zonal para o isolamento da zona quando uma caixa de máquina virtual usando um recurso de gateway NAT está na mesma zona que o recurso de gateway NAT e seus endereços IP públicos. O padrão que você quer usar para o isolamento de zona é criar uma "pilha zonal" por zona de disponibilidade.  Esta "pilha zonal" consiste em casos de máquinas virtuais, recursos de gateway NAT, endereço IP público e/ou recursos prefixos numa sub-rede que se presume estar a servir apenas a mesma zona.   As operações do avião de controlo e o plano de dados estão então alinhados e limitados à zona especificada. 

Espera-se que a falha numa zona diferente da de onde o seu cenário exista não tenha impacto no NAT. O tráfego de saída de máquinas virtuais na mesma zona falhará devido ao isolamento da zona.  

#### <a name="integrating-inbound-endpoints"></a>Integração de pontos finais de entrada

Se o seu cenário requer pontos finais de entrada, tem duas opções:

| Opção | Padrão | Exemplo | Pro | Rio Con |
|---|---|---|---|---|
| (1) | **Alinhe** os pontos finais de entrada com as **respetivas pilhas zonais** que está a criar para a saída. | Crie um equilibrador de carga padrão com frontend zonal. | O mesmo modelo de saúde e modo de falha para entrada e saída. Mais simples de operar. | Os endereços IP individuais por zona podem ter de ser mascarados por um nome DNS comum. |
| (2) | **Sobreponha** as pilhas zonais com um ponto final de entrada **de zona transversal.** | Crie um equilibrador de carga padrão com frontend redundante de zona. | Endereço IP único para o ponto final de entrada. | Diferentes modos de saúde e modos de avaria para entrada e saída.  Mais complexo para operar. |

>[!NOTE]
> Um gateway NAT isolado em zona requer endereços IP para corresponder à zona do gateway NAT. Não são permitidos recursos de gateway NAT com endereços IP de uma zona diferente ou sem zona.

#### <a name="cross-zone-outbound-scenarios-not-supported"></a>Cenários de saída de zona cruzada não suportados

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" alt="Figure depicts three zonal stacks, each of which contains a NAT gateway and a subnet, with the connections between to of the gateways and their subnets broken." width="425" title="Rede Virtual NAT não compatível com sub-rede de abranggem de zonas">
</p>

*Figura: Rede Virtual NAT não compatível com sub-rede de abranggem de zonas*

Não é possível alcançar uma promessa zonal com recursos de gateway NAT quando as instâncias de máquinas virtuais são implantadas em várias zonas dentro da mesma sub-rede.   E mesmo que houvesse vários gateways zonais NAT ligados a uma sub-rede, a instância da máquina virtual não saberia qual recurso de gateway NAT para selecionar.

Uma promessa zonal não existe quando a) a zona de uma instância de máquina virtual e a zona de um gateway nat zonal não estão alinhadas, ou b) um recurso regional de gateway NAT é usado com instâncias de máquina virtual zonais.

Embora o cenário pareça funcionar, o seu modelo de saúde e modo de falha é indefinido do ponto de vista da zona de disponibilidade. Considere ir com pilhas zonais ou todas regionais.

>[!NOTE]
>As zonas propriedade de um recurso de gateway NAT não são mutáveis.  Reimplantar recurso de gateway NAT com a preferência regional ou de zona pretendida.

>[!NOTE] 
>Os endereços IP por si só não são redundantes em zona se nenhuma zona for especificada.  O frontend de um [Balancer de Carga Padrão é redundante](../load-balancer/load-balancer-standard-availability-zones.md#frontend) de zona se um endereço IP não for criado numa zona específica.  Isto não se aplica à NAT.  Apenas o isolamento regional ou de zona é apoiado.

## <a name="performance"></a>Desempenho

Cada recurso de gateway NAT pode fornecer até 50 Gbps de produção. Pode dividir as suas implementações em várias sub-redes e atribuir a cada sub-rede ou grupos de sub-redes uma porta de entrada NAT para escalar.

Cada gateway NAT pode suportar 64.000 fluxos para TCP e UDP, respectivamente, por endereço IP de saída atribuído.  Reveja a seguinte secção sobre tradução de endereços de rede de origem (SNAT) para obter mais informações, bem como o [artigo de resolução de problemas](https://docs.microsoft.com/azure/virtual-network/troubleshoot-nat) para obter orientações específicas de resolução de problemas.

## <a name="source-network-address-translation"></a>Tradução de endereço de rede de origem

A tradução de endereços de rede de origem (SNAT) reescreve a origem de um fluxo originário de um endereço IP diferente.  Os recursos de gateway NAT utilizam uma variante do SNAT comumente referida à tradução de endereços portuários (PAT). Pat reescreve o endereço de origem e a porta de origem. Com o SNAT, não há uma relação fixa entre o número de endereços privados e as suas moradas públicas traduzidas.  

### <a name="fundamentals"></a>Noções básicas

Vejamos um exemplo de quatro fluxos para explicar o conceito básico.  O gateway NAT está a utilizar o recurso de endereço IP público 65.52.1.1 e o VM está a fazer ligações a 65.52.0.1.

| Fluxo | Tuple de origem | Tuple destino |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |

Estes fluxos podem parecer assim depois da PAT ter ocorrido:

| Fluxo | Tuple de origem | Tuple fonte SNAT'ed | Tuple destino | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | **65.52.1.1:1234** | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | **65.52.1.1:1235** | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | **65.52.1.1:1236** | 65.52.0.1:80 |

O destino verá a fonte do fluxo como 65.52.0.1 (tuple fonte SNAT) com a porta atribuída mostrada.  Pat, como mostrado na tabela anterior, também é chamado de SNAT disfarçado de porta.  Várias fontes privadas estão mascaradas atrás de um IP e porto.  

#### <a name="source-snat-port-reuse"></a>reutilização portuária de fonte (SNAT)

Gateways NAT oportunisticamente reutilizam portas de origem (SNAT).  O seguinte ilustra este conceito como um fluxo adicional para o conjunto de fluxos pré-cessindo.  O VM no exemplo é um fluxo para 65.52.0.2.

| Fluxo | Tuple de origem | Tuple destino |
|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

Um gateway NAT provavelmente traduzirá o fluxo 4 para uma porta que pode ser usada para outros destinos também.  Consulte [o Dimensionamento](https://docs.microsoft.com/azure/virtual-network/nat-gateway-resource#scaling) para obter uma discussão adicional sobre o dimensionamento correto do fornecimento do seu endereço IP.

| Fluxo | Tuple de origem | Tuple fonte SNAT'ed | Tuple destino | 
|:---:|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.1.1:**1234** | 65.52.0.2:80 |

Não assuma uma dependência da forma específica como as portas de origem são atribuídas no exemplo acima.  O anterior é apenas uma ilustração do conceito fundamental.

O SNAT fornecido pela NAT é diferente do [Balanceador](../load-balancer/load-balancer-outbound-connections.md) de Carga em vários aspetos.

### <a name="on-demand"></a>A pedido

A NAT fornece portas SNAT a pedido para novos fluxos de tráfego de saída. Todas as portas SNAT disponíveis em inventário são utilizadas por qualquer máquina virtual em sub-redes configuradas com NAT. 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T." width="550" title="SNAT de saída de rede virtual NAT a pedido">
</p>

*Figura: Rede Virtual NAT on-demand outbound SNAT*

Qualquer configuração IP de uma máquina virtual pode criar fluxos de saída a pedido, conforme necessário.  A pré-atribuição, por caso, o planeamento, incluindo por exemplo, o pior caso de superprovisionamento, não é necessário.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T with exhaustion threshold." width="550" title="Diferenças nos cenários de exaustão">
</p>

*Figura: Diferenças nos cenários de exaustão*

Uma vez que uma porta SNAT é lançada, está disponível para ser usada por qualquer máquina virtual em sub-redes configuradas com NAT.  A atribuição a pedido permite que cargas de trabalho dinâmicas e divergentes nas sub-redes utilizem as portas SNAT conforme precisam.  Enquanto houver inventário portuário SNAT disponível, os fluxos SNAT serão bem sucedidos. Os pontos quentes do porto SNAT beneficiam do inventário maior. As portas SNAT não são deixadas sem ser tão bem as máquinas virtuais que não necessitam ativamente delas.

### <a name="scaling"></a>Dimensionamento

O dimensionamento do NAT é principalmente uma função de gestão do inventário portuário SNAT partilhado e disponível. A NAT necessita de um inventário portuário SNAT suficiente para os fluxos de saída de pico esperados para todas as sub-redes anexadas a um recurso de gateway NAT.  Você pode usar recursos de endereço IP público, recursos prefixos IP públicos, ou ambos para criar inventário portuário SNAT.  

>[!NOTE]
>Se estiver a atribuir um recurso de prefixo IP público, todo o prefixo IP público será utilizado.  Não é possível atribuir um recurso de prefixo IP público e, em seguida, abrir endereços IP individuais para atribuir a outros recursos.  Se pretender atribuir endereços IP individuais de um prefixo IP público a múltiplos recursos, precisa de criar endereços IP públicos individuais a partir do recurso prefixo IP público e atribuí-los conforme necessário em vez do próprio recurso de prefixo IP público.

O SNAT mapeia endereços privados para um ou mais endereços IP públicos, reescrita de endereço de origem e porta de origem nos processos. Um recurso de gateway NAT utilizará 64.000 portas (portas SNAT) por endereço IP público configurado para esta tradução. Os recursos de gateway NAT podem escalar até 16 endereços IP e portas SNAT de 1M. Se for fornecido um recurso de prefixo IP público, cada endereço IP dentro do prefixo está a fornecer o inventário portuário SNAT. E adicionar mais endereços IP públicos aumenta as portas SNAT de inventário disponível. TCP e UDP são inventários portuários SNAT separados e não relacionados.

Os recursos de gateway NAT reutilizam oportunisticamente as portas de origem (SNAT). Como orientação de design para fins de escala, deve assumir que cada fluxo requer uma nova porta SNAT e escalar o número total de endereços IP disponíveis para tráfego de saída.  Deve considerar cuidadosamente a escala para a qual está a desenhar e providenciar quantidades de endereços IP em conformidade.

As portas SNAT para diferentes destinos são mais prováveis de serem reutilizadas quando possível. E à medida que a exaustão portuária SNAT se aproxima, os fluxos podem não ter sucesso.  

Consulte [os fundamentos do SNAT,](https://docs.microsoft.com/azure/virtual-network/nat-gateway-resource#source-network-address-translation) por exemplo.


### <a name="protocols"></a>Protocolos

Os recursos de gateway NAT interagem com cabeçalhos de transporte IP e IP dos fluxos UDP e TCP e são agnósticos às cargas de camadas de aplicação.  Outros protocolos IP não são suportados.

### <a name="timers"></a>Temporizadores

>[!IMPORTANT]
>O temporizador de marcha lenta pode aumentar desnecessariamente a probabilidade de exaustão SNAT. Quanto mais tempo especificar, mais tempo o NAT se manterá nas portas SNAT até que, eventualmente, o tempo limite seja inativo. Se os seus fluxos forem inativos, eles falharão eventualmente de qualquer forma e consumirão desnecessariamente o inventário portuário SNAT.  Fluxos que falham a 2 horas também teriam falhado no padrão de 4 minutos. Aumentar o tempo limite de marcha lenta é uma opção de último recurso que deve ser usada com moderação. Se um fluxo nunca ficar inativo, não será afetado pelo temporizador inativo.

O tempo de 20 minutos (predefinição) para 120 minutos (2 horas) para todos os fluxos pode ser ajustado de 4 minutos (predefinidos) para 120 minutos (2 horas) para todos os fluxos.  Além disso, pode repor o temporizador inativo com o tráfego no fluxo.  Um padrão recomendado para refrescar ligações longas e desativação de ponto final são as keepalives TCP.  As keepalivas TCP aparecem como ACKs duplicados para os pontos finais, são baixas sobrecargas e invisíveis para a camada de aplicação.

São utilizados os seguintes temporizadores para a libertação da porta SNAT:

| Temporizador | Valor |
|---|---|
| BARBATANA TCP | 60 segundos |
| TCP RST | 10 segundos |
| TCP meio aberto | 30 segundos |

Uma porta SNAT está disponível para reutilização para o mesmo endereço IP de destino e porta de destino após 5 segundos.

>[!NOTE] 
>Estas definições do temporizador estão sujeitas a alterações. Os valores são fornecidos para ajudar na resolução de problemas e você não deve assumir uma dependência de temporizadores específicos neste momento.

## <a name="limitations"></a>Limitações

- O NAT é compatível com o IP público SKU padrão, prefixo IP público e recursos do balançador de carga.   Os recursos básicos (por exemplo, o balanceador de carga básica) e quaisquer produtos derivados deles não são compatíveis com o NAT.  Os recursos básicos devem ser colocados numa sub-rede não configurada com o NAT.
- A família iPv4 é apoiada.  A NAT não interage com a família IPv6.  O NAT não pode ser implantado numa sub-rede com um prefixo IPv6.
- A NAT não pode abranger várias redes virtuais.

## <a name="suggestions"></a>Sugestões

Queremos saber como podemos melhorar o serviço. Falta-lhe uma capacidade? Faça o seu caso para o que devemos construir a seguir no [UserVoice para NAT](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a rede virtual NAT.](nat-overview.md)
* Saiba mais [sobre métricas e alertas para os recursos de gateway DA NAT.](nat-metrics.md)
* Saiba mais sobre [a resolução de problemas dos recursos de gateway DA NAT.](troubleshoot-nat.md)
* Tutorial para validação do NAT Gateway
  - [CLI do Azure](tutorial-create-validate-nat-gateway-cli.md)
  - [PowerShell](tutorial-create-validate-nat-gateway-powershell.md)
  - [Portal](tutorial-create-validate-nat-gateway-portal.md)
* Quickstart para implantar um recurso de gateway NAT
  - [CLI do Azure](./quickstart-create-nat-gateway-cli.md)
  - [PowerShell](./quickstart-create-nat-gateway-powershell.md)
  - [Portal](./quickstart-create-nat-gateway-portal.md)
  - [Modelo](./quickstart-create-nat-gateway-template.md)
* Saiba mais sobre o recurso nat gateway API
  - [API REST](https://docs.microsoft.com/rest/api/virtualnetwork/natgateways)
  - [CLI do Azure](https://docs.microsoft.com/cli/azure/network/nat/gateway)
  - [PowerShell](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway)
* Saiba mais [sobre as zonas de disponibilidade.](../availability-zones/az-overview.md)
* Saiba mais sobre o [balanceador de carga padrão](../load-balancer/load-balancer-standard-overview.md).
* Saiba mais sobre [as zonas de disponibilidade e o balanceador de carga padrão.](../load-balancer/load-balancer-standard-availability-zones.md)
* [Diga-nos o que construir a seguir para o NAT de Rede Virtual no UserVoice](https://aka.ms/natuservoice).
