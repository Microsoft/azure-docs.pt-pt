---
title: Conceber redes virtuais com recursos de gateway NAT
titleSuffix: Azure Virtual Network NAT
description: Aprenda a desenhar redes virtuais com recursos de gateway NAT.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to learn more about how to design virtual networks with NAT gateway resources.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2020
ms.author: allensu
ms.openlocfilehash: 6bb53539c105cda99c842b6b0fa236f0e18a85ea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82182485"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources"></a>Conceber redes virtuais com recursos de gateway NAT

Os recursos de gateway NAT fazem parte da [Rede Virtual NAT](nat-overview.md) e fornecem conectividade de saída da Internet para uma ou mais subnets de uma rede virtual. A sub-rede da rede virtual indica que o portal NAT será utilizado. O NAT fornece tradução de endereço de rede fonte (SNAT) para uma sub-rede.  Os recursos de gateway NAT especificam quais os endereços IP estáticos que as máquinas virtuais utilizam quando criam fluxos de saída. Os endereços IP estáticos provêm de recursos públicos de endereçoIP, recursos de prefixo IP públicos, ou ambos. Se for utilizado um recurso público de pré-fixação IP, todos os endereços IP de todo o recurso público de pré-fixação IP são consumidos por um recurso de gateway NAT. Um recurso de gateway NAT pode usar um total de até 16 endereços IP estáticos de qualquer um deles.


<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Rede Virtual NAT para saída para a Internet">
</p>

*Figura: Rede Virtual NAT para saída para a Internet*

## <a name="how-to-deploy-nat"></a>Como implantar o NAT

Configurar e utilizar o gateway NAT é intencionalmente simplificado:  

Recurso de gateway NAT:
- Criar recursos de gateway NAT regionais ou zonais (isolados de zona),
- Atribuir endereços IP,
- Se necessário, modifique o tempo de paragem do TCP (opcional).  Reveja os [temporizadores](#timers) <ins>antes</ins> de alterar o padrão.

Rede virtual:
- Configure a sub-rede virtual para utilizar um portal NAT.

Não são necessárias rotas definidas pelo utilizador.

## <a name="resource"></a>Recurso

O recurso foi concebido para ser simples, como pode ver pelo exemplo do Gestor de Recursos Azure num formato semelhante ao modelo.  Este formato semelhante ao modelo é mostrado aqui para ilustrar os conceitos e estrutura.  Modifique o exemplo para as suas necessidades.  Este documento não se destina a ser um tutorial.

O diagrama seguinte mostra as referências recedíveis entre os diferentes recursos do Gestor de Recursos Azure.  A seta indica a direção da referência, originária de onde é reconelegível. Rever 

<p align="center">
  <img src="media/nat-overview/flow-map.svg" width="256" title="Modelo de objeto nat rede virtual">
</p>

*Figura: Modelo de objeto nat da rede virtual*

O NAT é recomendado para a maioria das cargas de trabalho, a menos que tenha uma dependência específica da conectividade de saída do [Load Balancer baseado na piscina.](../load-balancer/load-balancer-outbound-connections.md)  

Pode migrar de cenários de equilíbrio de carga padrão, incluindo [regras de saída,](../load-balancer/load-balancer-outbound-rules-overview.md)para gateway NAT. Para migrar, mover os recursos públicos ip e ip prefixo público saem de frontends de equilibradores de carga para gateway NAT. Não são necessários novos endereços IP para gateway NAT. Os recursos de endereçoip público padrão e o recurso público de prefixo IP podem ser reutilizados desde que o total não exceda 16 endereços IP. Plano para a migração com interrupção do serviço em mente durante a transição.  Pode minimizar a interrupção automatizando o processo. Teste a migração num ambiente de preparação primeiro.  Durante a transição, os fluxos originados de entrada não são afetados.


O exemplo seguinte é um corte de um modelo de Gestor de Recursos Azure.  Este modelo implementa vários recursos, incluindo um gateway NAT.  O modelo tem os seguintes parâmetros neste exemplo:

- **natgatewayname** - Nome da porta de entrada NAT.
- **localização** - Região azure onde se encontra recurso.
- **nome público** - Nome do IP público de saída associado à porta de entrada NAT.
- **vnetname** - Nome da rede virtual.
- **subnome** - Nome da subnet associada ao gateway NAT.

O número total de endereços IP fornecidos por todos os endereços IP e recursos de prefixo não podem exceder 16 endereços IP no total. É permitido qualquer número de endereços IP entre 1 e 16.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="81-96":::

Quando o recurso de gateway NAT foi criado, pode ser usado em uma ou mais subredes de uma rede virtual. Especifique quais as subredes que utilizam este recurso de gateway NAT. Um portal NAT não é capaz de abranger mais do que uma rede virtual. Não é necessário atribuir a mesma porta de entrada NAT a todas as subredes de uma rede virtual. As subredes individuais podem ser configuradas com diferentes recursos de gateway NAT.

Os cenários que não utilizem zonas de disponibilidade serão regionais (nenhuma zona especificada). Se estiver a usar zonas de disponibilidade, pode especificar uma zona para isolar o NAT numa zona específica. A redundância da zona não é apoiada. Rever [as zonas de disponibilidade](#availability-zones)do NAT.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="1-146" highlight="81-96":::

Os gateways NAT são definidos com uma propriedade numa subnet dentro de uma rede virtual. Os fluxos criados por máquinas virtuais no **subnome subnet de sub-rede** de **vnetname** de rede virtual usarão o gateway NAT. Toda a conectividade de saída utilizará os endereços IP associados ao **nome natgateway** como endereço IP de origem.

Para obter mais informações sobre o modelo do Gestor de Recursos Azure utilizado neste exemplo, consulte:

- [Quickstart: Criar um gateway NAT - Modelo de Gestor de Recursos](quickstart-create-nat-gateway-template.md)
- [Rede Virtual NAT](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm/)

## <a name="design-guidance"></a>Orientação de Design

Reveja esta secção para se familiarizar com considerações para conceber redes virtuais com NAT.  

1. [Otimização de custos](#cost-optimization)
1. [Coexistência de entrada e saída](#coexistence-of-inbound-and-outbound)
2. [Gestão de recursos básicos](#managing-basic-resources)
3. [Zonas de Disponibilidade](#availability-zones)

### <a name="cost-optimization"></a>Otimização de custos

[Os pontos finais](virtual-network-service-endpoints-overview.md) do serviço e o [link privado](../private-link/private-link-overview.md) são opções a considerar para otimizar o custo. O NAT não é necessário para estes serviços. O tráfego direcionado para pontos finais de serviço ou ligação privada não é processado pelo NAT da rede virtual.  

Os pontos finais do serviço ligam os recursos de serviço azure à sua rede virtual e controlam o acesso aos seus recursos de serviço Azure. Por exemplo, quando aceder ao armazenamento do Azure, utilize um ponto final de serviço para armazenamento para evitar encargos nat processados de dados. Os pontos finais de serviço são gratuitos.

O link privado expõe o serviço Azure PaaS (ou outros serviços hospedados com ligação privada) como um ponto final privado dentro de uma rede virtual.  A ligação privada é faturada com base na duração e nos dados tratados.

Avalie se ambas as abordagens são adequadas para o seu cenário e utilização conforme necessário.

### <a name="coexistence-of-inbound-and-outbound"></a>Coexistência de entrada e saída

O portal NAT é compatível com:

 - Equilibrador de carga padrão
 - IP público padrão
 - Prefixo IP público padrão

Ao desenvolver uma nova implementação, comece com as SKUs padrão.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Rede Virtual NAT para saída para a Internet">
</p>

*Figura: Rede Virtual NAT para saída para a Internet*

O único cenário de saída da Internet fornecido pelo portal NAT pode ser expandido com a entrada a partir da funcionalidade da Internet. Cada recurso está ciente da direção em que um fluxo é originado. Numa sub-rede com um portal NAT, todos os cenários de saída para a Internet são substituídos pela porta de entrada na NAT. A entrada dos cenários da Internet é fornecida pelo respetivo recurso.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>NAT e VM com IP público de nível de instância

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" width="300" title="Rede Virtual NAT e VM com IP público de nível de instância">
</p>

*Figura: Rede Virtual NAT e VM com IP público de nível de instância*

| Direção | Recurso |
|:---:|:---:|
| Entrada | VM com IP público de nível de instância |
| Saída | NAT Gateway |

VM usará porta de entrada NAT para saída.  A entrada originada não é afetada.

#### <a name="nat-and-vm-with-public-load-balancer"></a>NAT e VM com Balancer de Carga Pública

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" width="350" title="Rede Virtual NAT e VM com Balancer de Carga Pública">
</p>

*Figura: Rede Virtual NAT e VM com Balancer de Carga Pública*

| Direção | Recurso |
|:---:|:---:|
| Entrada | equilibrador de carga público |
| Saída | NAT Gateway |

Qualquer configuração de saída de uma regra de equilíbrio de carga ou regras de saída é substituído pelo gateway NAT.  A entrada originada não é afetada.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-public-load-balancer"></a>NAT e VM com IP público de nível de instância e balancedor de carga público

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" width="425" title="Rede Virtual NAT e VM com IP público de nível de instância e balancedor de carga público">
</p>

*Figura: Rede Virtual NAT e VM com IP público de nível de instância e balancedor de carga público*

| Direção | Recurso |
|:---:|:---:|
| Entrada | VM com IP público de nível de exemplo e balancedor de carga público |
| Saída | NAT Gateway |

Qualquer configuração de saída de uma regra de equilíbrio de carga ou regras de saída é substituído pelo gateway NAT.  O VM também utilizará o portal NAT para a saída.  A entrada originada não é afetada.

### <a name="managing-basic-resources"></a>Gestão de recursos básicos

O equilíbrio de carga padrão, o IP público e o prefixo IP público são compatíveis com o gateway NAT. Os portais NAT operam no âmbito de uma sub-rede. O SKU básico destes serviços deve ser implantado numa subnet sem um portal NAT. Esta separação permite que ambas as variantes SKU coexistam na mesma rede virtual.

Os gateways naNA têm precedência sobre os cenários de saída da sub-rede. O equilíbrio de carga básico ou IP público (e qualquer serviço gerido construído com eles) não pode ser ajustado com as traduções corretas. O portal NAT assume o controlo sobre a saída para o tráfego da Internet numa subnet. O tráfego de entrada para o equilíbrio de carga básico e ip público não está disponível. O tráfego de entrada para um equilíbrio de carga básico e, ou um ip público configurado num VM não estará disponível.

### <a name="availability-zones"></a>Zonas de Disponibilidade

#### <a name="zone-isolation-with-zonal-stacks"></a>Isolamento de zona com pilhas zonais

<p align="center">
  <img src="media/nat-overview/az-directions.svg" width="425" title="Rede Virtual NAT com isolamento de zona, criando múltiplos "zonal stacks"">
</p>

*Figura: Rede Virtual NAT com isolamento de zona, criando múltiplas "pilhas zonais"*

Mesmo sem zonas de disponibilidade, o NAT é resiliente e pode sobreviver a múltiplas falhas na componente de infraestrutura.  As zonas de disponibilidade baseiam-se nesta resiliência com cenários de isolamento de zonas para o NAT.

As redes virtuais e as suas subredes são construções regionais.  As subredes não se restringem a uma zona.

Existe uma promessa zonal para o isolamento de zonas quando uma máquina virtual que utiliza um recurso de gateway NAT está na mesma zona que o recurso de gateway NAT e os seus endereços IP públicos. O padrão que pretende utilizar para o isolamento de zonas é criar uma "pilha zonal" por zona de disponibilidade.  Esta "pilha zonal" consiste em instâncias de máquinas virtuais, recursos de gateway NAT, endereço IP público e/ou recursos de prefixo numa subrede que se presume servir apenas a mesma zona.   As operações do avião de controlo e o plano de dados estão então alinhados e limitados à zona especificada. 

Espera-se que o fracasso numa zona diferente da que existe o seu cenário não tenha impacto no NAT. O tráfego de saída de máquinas virtuais na mesma zona falhará devido ao isolamento da zona.  

#### <a name="integrating-inbound-endpoints"></a>Integração de pontos finais de entrada

Se o seu cenário requer pontos finais de entrada, tem duas opções:

| Opção | Padrão | Exemplo | Pro | Con |
|---|---|---|---|---|
| (1) | **Alinhe** os pontos finais de entrada com as **respetivas pilhas zonais** que está a criar para sair. | Crie um equilibrador de carga padrão com extremidade frontal zonal. | O mesmo modelo de saúde e modo de avaria para entrada e saída. Mais simples de operar. | Os endereços IP individuais por zona podem ter de ser mascarados por um nome DNS comum. |
| (2) | **Sobreponha** as pilhas zonais com um ponto final de **entrada** transversal. | Crie um equilíbrio de carga padrão com extremidade frontal redundante. | Endereço IP único para ponto final de entrada. | Vários modelos de saúde e modos de falha para entrada e saída.  Mais complexo para operar. |

>[!NOTE]
> Um portal NAT isolado de zona requer endereços IP que correspondam à zona do gateway NAT. Não são permitidos recursos de gateway NAT com endereços IP de uma zona diferente ou sem uma zona.

#### <a name="cross-zone-outbound-scenarios-not-supported"></a>Cenários de saída de zona transversal não suportados

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" width="425" title="Rede Virtual NAT não compatível com subnet de extensão de zona">
</p>

*Figura: Rede Virtual NAT não compatível com subnet de extensão de zona*

Não é possível cumprir uma promessa zonal com recursos de gateway NAT quando as instâncias de máquinas virtuais são implantadas em várias zonas dentro da mesma subnet.   E mesmo que houvesse vários gateways nat zonal ligados a uma subnet, a instância virtual da máquina não saberia qual recurso de gateway NAT escolher.

Uma promessa zonal não existe quando a) a zona de uma máquina virtual e as zonas de um gateway nat zonal não estão alinhadas, ou b) um recurso regional de gateway NAT é usado com instâncias de máquinas virtuais zonais.

Embora o cenário pareça funcionar, o seu modelo de saúde e o seu modo de falha não estão definidos do ponto de vista da zona de disponibilidade. Considere ir com pilhas zonais ou todas regionais.

>[!NOTE]
>A propriedade de zonas de um recurso de gateway NAT não é mutável.  Reutilizar o recurso de gateway NAT com a preferência regional ou de zona pretendida.

>[!NOTE] 
>Os endereços IP por si só não são redundantes se nenhuma zona for especificada.  O frontend de um [Balancer de Carga Padrão é redundante](../load-balancer/load-balancer-standard-availability-zones.md#frontend) se um endereço IP não for criado numa zona específica.  Isto não se aplica ao NAT.  Apenas o isolamento regional ou de zona é apoiado.

## <a name="performance"></a>Desempenho

Cada recurso de gateway NAT pode fornecer até 50 Gbps de entrada. Pode dividir as suas implementações em várias subredes e atribuir a cada sub-rede ou grupos de subredes uma porta de entrada NAT para escalar.

Cada gateway NAT pode suportar 64.000 ligações por endereço IP de saída atribuído.  Reveja a seguinte secção sobre tradução de endereços de rede de origem (SNAT) para mais detalhes, bem como o artigo de resolução de [problemas](https://docs.microsoft.com/azure/virtual-network/troubleshoot-nat) para orientação específica de resolução de problemas.

## <a name="source-network-address-translation"></a>Tradução de endereço de rede de origem

A tradução de endereços de rede de origem (SNAT) reescreve a fonte de um fluxo originário de um endereço IP diferente.  Os recursos de gateway NAT utilizam uma variante de SNAT comumente referida à tradução do endereço portuário (PAT). Pat reescreve o endereço de origem e a porta de origem. Com a SNAT, não há uma relação fixa entre o número de endereços privados e as suas moradas públicas traduzidas.  

### <a name="fundamentals"></a>Noções Básicas

Vejamos um exemplo de quatro fluxos para explicar o conceito básico.  O portal NAT está a utilizar o recurso de endereço IP público 65.52.0.2.

| Fluxo | Tuple de origem | Destino tuple |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

Estes fluxos podem parecer assim depois da PAT ter ocorrido:

| Fluxo | Tuple de origem | SNAT'ed fonte tuple | Destino tuple | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.2:234 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.2:235 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.2:236 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:237 | 65.52.0.2:80 |

O destino verá a fonte do fluxo como 65.52.0.2 (tuple de origem SNAT) com a porta atribuída mostrada.  Pat, como mostrado na tabela anterior, também é chamado de sNAT de máscara portuária.  Várias fontes privadas são mascaradas atrás de um IP e porto.

Não tome uma dependência da forma específica como os portos de origem são atribuídos.  O anterior é apenas uma ilustração do conceito fundamental.

O SNAT fornecido pelo NAT é diferente do [Load Balancer](../load-balancer/load-balancer-outbound-connections.md) em vários aspetos.

### <a name="on-demand"></a>A pedido

O NAT fornece portos SNAT a pedido para novos fluxos de tráfego de saída. Todas as portas SNAT disponíveis em inventário são utilizadas por qualquer máquina virtual em subredes configuradas com NAT. 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" width="550" title="Rede Virtual NAT a pedido snat">
</p>

*Figura: Rede Virtual NAT a pedido de saída SNAT*

Qualquer configuração IP de uma máquina virtual pode criar fluxos de saída a pedido conforme necessário.  Não é necessária pré-atribuição, por exemplo, incluindo por exemplo o pior excesso de oferta, não é necessário.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" width="550" title="Diferenças nos cenários de exaustão">
</p>

*Figura: Diferenças nos cenários de exaustão*

Uma vez que uma porta SNAT se solta, está disponível para utilização por qualquer máquina virtual em subredes configuradas com NAT.  A atribuição a pedido permite que cargas de trabalho dinâmicas e divergentes nas subredes utilizem portas SNAT conforme necessário.  Enquanto houver inventário de porta SNAT disponível, os fluxos de SNAT terão sucesso. Os pontos quentes da porta SNAT beneficiam do inventário maior. As portas SNAT não são deixadas sem uso para máquinas virtuais que não precisam deles ativamente.

### <a name="scaling"></a>Dimensionamento

A escala na tNat é principalmente uma função de gestão do inventário de portas SNAT partilhado e disponível. O NAT necessita de um inventário suficiente da porta SNAT para os fluxos de saída de pico esperados para todas as subredes ligadas a um recurso de gateway NAT.  Você pode usar recursos de endereço IP público, recursos públicos de prefixo IP, ou ambos para criar inventário de porta SNAT.  

>[!NOTE]
>Se estiver a atribuir um recurso público de pré-fixação IP, todo o prefixo IP público será utilizado.  Não é possível atribuir um recurso público de pré-fixação ip e, em seguida, abrir endereços IP individuais para atribuir a outros recursos.  Se pretender atribuir endereços IP individuais de um prefixo IP público a vários recursos, precisa de criar endereços IP públicos individuais a partir do recurso público de prefixo IP e atribuí-los conforme necessário em vez do próprio recurso de pré-fixação IP público.

O SNAT mapeia endereços privados para um ou mais endereços IP públicos, reescrevendo endereço de origem e porta de origem nos processos. Um recurso de gateway NAT utilizará 64.000 portas (portas SNAT) por endereço IP público configurado para esta tradução. Os recursos de gateway NAT podem escalar até 16 endereços IP e portas SNAT 1M. Se for fornecido um recurso público de pré-fixação IP, cada endereço IP dentro do prefixo está a fornecer o inventário da porta SNAT. E adicionar mais endereços IP públicos aumenta as portas sNAT de inventário disponíveis. TCP e UDP são inventários de portas SNAT separados e não relacionados.

Os recursos de gateway na NAT reutilizam oportunisticamente as portas de origem. Para efeitos de escala, deve assumir que cada fluxo requer uma nova porta SNAT e escalar o número total de endereços IP disponíveis para tráfego de saída.

### <a name="protocols"></a>Protocolos

Os recursos de gateway NAT interagem com os cabeçalhos de transporte IP e IP dos fluxos UDP e TCP e são agnósticos para as cargas da camada de aplicação.  Outros protocolos ip não são suportados.

### <a name="timers"></a>Temporizadores

>[!IMPORTANT]
>O temporizador longo pode aumentar desnecessariamente a probabilidade de exaustão de SNAT. Quanto mais tempo especificar, mais tempo o NAT irá manter-se nas portas SNAT até que, eventualmente, fiquem inativos. Se os seus fluxos estiverem inativos, eles irão eventualmente falhar de qualquer maneira e consumir desnecessariamente o inventário da porta SNAT.  Os fluxos que falham às 2 horas também teriam falhado no padrão de 4 minutos. Aumentar o tempo inativo é uma opção de último recurso que deve ser usada com moderação. Se um fluxo nunca ficar inativo, não será afetado pelo temporizador inativo.

O tempo de paragem inativo do TCP pode ser ajustado de 4 minutos (predefinido) a 120 minutos (2 horas) para todos os fluxos.  Além disso, pode repor o temporizador inativo com o tráfego no fluxo.  Um padrão recomendado para refrescar ligações longas e deteção de vida de ponto final é a manutenção de TCP.  As vidas de manutenção do TCP aparecem como ACKs duplicados para os pontos finais, são baixos e invisíveis para a camada de aplicação.

Os seguintes tempos são utilizados para a libertação da porta SNAT:

| Temporizador | Valor |
|---|---|
| TCP FIN | 60 segundos |
| TCP RST | 10 segundos |
| TCP meio aberto | 30 segundos |

Uma porta SNAT está disponível para reutilização para o mesmo endereço IP de destino e porta de destino após 5 segundos.

>[!NOTE] 
>Estas definições do temporizador estão sujeitas a alterações. Os valores são fornecidos para ajudar a resolver problemas e não deve tomar uma dependência de tempos específicos neste momento.

## <a name="limitations"></a>Limitações

- O NAT é compatível com os recursos padrão de IP público sku, prefixo ip público e equilibrador de carga.   Os recursos básicos (por exemplo, o equilíbrio básico de carga) e quaisquer produtos derivados deles não são compatíveis com o NAT.  Os recursos básicos devem ser colocados numa sub-rede não configurada com NAT.
- A família de endereços IPv4 é apoiada.  O NAT não interage com a família iPv6.  O NAT não pode ser implantado numa sub-rede com um prefixo IPv6.
- A exploração de fluxo sanções da NSG não é suportada quando se utiliza NAT.
- O NAT não pode abranger várias redes virtuais.


## <a name="feedback"></a>Comentários

Queremos saber como podemos melhorar o serviço. Falta-lhe capacidade? Faça o seu caso para o que devemos construir a seguir na [UserVoice para NAT](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Passos seguintes

* Conheça a [rede virtual NAT](nat-overview.md).
* Conheça [métricas e alertas para os recursos](nat-metrics.md)de gateway NAT .
* Saiba mais sobre a resolução de recursos de [gateway na NAT.](troubleshoot-nat.md)
* Tutorial para validar na NaT Gateway
  - [CLI do Azure](tutorial-create-validate-nat-gateway-cli.md)
  - [PowerShell](tutorial-create-validate-nat-gateway-powershell.md)
  - [Portal](tutorial-create-validate-nat-gateway-portal.md)
* Quickstart para implantar um recurso de gateway NAT
  - [CLI do Azure](./quickstart-create-nat-gateway-cli.md)
  - [PowerShell](./quickstart-create-nat-gateway-powershell.md)
  - [Portal](./quickstart-create-nat-gateway-portal.md)
  - [Modelo](./quickstart-create-nat-gateway-template.md)
* Conheça a API do recurso de gateway NAT
  - [API REST](https://docs.microsoft.com/rest/api/virtualnetwork/natgateways)
  - [CLI do Azure](https://docs.microsoft.com/cli/azure/network/nat/gateway?view=azure-cli-latest)
  - [PowerShell](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway)
* Conheça [as zonas de disponibilidade.](../availability-zones/az-overview.md)
* Saiba mais sobre o [equilíbrio de carga padrão.](../load-balancer/load-balancer-standard-overview.md)
* Conheça [as zonas de disponibilidade e o equilibrador de carga padrão.](../load-balancer/load-balancer-standard-availability-zones.md)
* [Diga-nos o que construir a seguir para a Rede Virtual NAT no UserVoice](https://aka.ms/natuservoice).


