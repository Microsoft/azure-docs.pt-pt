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
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: d920bde856521f1e662536c1187881e143612039
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359099"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources-public-preview"></a>Conceber redes virtuais com recursos de gateway NAT (Visualização Pública)

Os recursos de gateway NAT fazem parte da [Rede Virtual NAT](nat-overview.md) e fornecem conectividade de saída da Internet para uma ou mais subnets de uma rede virtual. A sub-rede da rede virtual indica que o portal NAT será utilizado. O NAT fornece tradução de endereço de rede fonte (SNAT) para uma sub-rede.  Os recursos de gateway NAT especificam quais os endereços IP estáticos que as máquinas virtuais utilizam quando criam fluxos de saída. Os endereços IP estáticos provêm de recursos públicos de endereçoIP, recursos de prefixo IP públicos, ou ambos. Um recurso de gateway NAT pode usar até 16 endereços IP estáticos de qualquer um deles.


<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Rede Virtual NAT para saída para a Internet">
</p>

*Figura: Rede Virtual NAT para saída para a Internet*


>[!NOTE] 
>A Rede Virtual NAT está disponível como pré-visualização pública neste momento. Atualmente só está disponível num conjunto limitado de [regiões.](nat-overview.md#region-availability) Esta pré-visualização é fornecida sem um acordo de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms) para obter mais informações.

## <a name="how-to-deploy-nat"></a>Como implantar o NAT

Configurar e utilizar o gateway NAT é intencionalmente simplificado:  

Recurso de gateway NAT:
- Criar recursos de gateway NAT regionais ou zonais (isolados de zona),
- Atribuir endereços IP,
- Modificar o tempo de paragem inativa (opcional).

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

Pode migrar de cenários de equilíbrio de carga padrão, incluindo [regras de saída,](../load-balancer/load-balancer-outbound-rules-overview.md)para gateway NAT. Para migrar, mover os recursos públicos ip e ip prefixo público saem de frontends de equilibradores de carga para gateway NAT. Não são necessários novos endereços IP para gateway NAT. O IP e o prefixo públicopadrão podem ser reutilizados desde que o total não exceda 16 endereços IP. Plano para a migração com interrupção do serviço em mente durante a transição.  Pode minimizar a interrupção automatizando o processo. Teste a migração num ambiente de preparação primeiro.  Durante a transição, os fluxos originados de entrada não são afetados.

O exemplo seguinte criaria um recurso de gateway NAT chamado _myNATGateway_ é criado na região _East US 2, AZ 1_ com um tempo de 4 _minutos_ inativo. Os endereços IP de saída fornecidos são:
- Um conjunto de recursos de endereço IP público _myIP1_ e _myIP2_ e 
- Um conjunto de recursos públicos de prefixo _IP myPrefix1_ e _myPrefix2_. 

O número total de endereços IP fornecidos pelos quatro recursos de endereçoIP não pode exceder 16 endereços IP no total. É permitido qualquer número de endereços IP entre 1 e 16.

```json
{
"name": "myNATGateway",
   "type": "Microsoft.Network/natGateways",
   "apiVersion": "2018-11-01",
   "location": "East US 2",
   "sku": { "name": "Standard" },
   "zones": [ "1" ],
   "properties": {
      "idleTimeoutInMinutes": 4, 
      "publicIPPrefixes": [
         {
            "id": "ref to myPrefix1"
         },
         {
            "id": "ref to myPrefix2"
         }
      ],
      "publicIPAddresses": [
         {
            "id": "ref to myIP1"
         },
         {
            "id": "ref to myIP2"
         }
      ]
   }
}
```

Quando o recurso de gateway NAT foi criado, pode ser usado em uma ou mais subredes de uma rede virtual. Especifique quais as subredes que utilizam este recurso de gateway NAT. Um portal NAT não é capaz de abranger mais do que uma rede virtual. Não é necessário atribuir a mesma porta de entrada NAT a todas as subredes de uma rede virtual. As subredes individuais podem ser configuradas com diferentes recursos de gateway NAT.

Os cenários que não utilizem zonas de disponibilidade serão regionais (nenhuma zona especificada). Se estiver a usar zonas de disponibilidade, pode especificar uma zona para isolar o NAT numa zona específica. A redundância da zona não é apoiada. Rever [as zonas de disponibilidade](#availability-zones)do NAT.


```json
{
   "name": "myVNet",
   "apiVersion": "2018-11-01",
   "type": "Microsoft.Network/virtualNetworks",
   "location": "myRegion", 
   "properties": {
      "addressSpace": {
          "addressPrefixes": [
           "192.168.0.0/16"
          ]
      },
      "subnets": [
         {
            "name": "mySubnet1",
            "properties": {
               "addressPrefix": "192.168.0.0/24",
               "natGateway": {
                  "id": "ref to myNATGateway"
               }
            }
         } 
      ]
   }
}
```
Os gateways NAT são definidos com uma propriedade numa subnet dentro de uma rede virtual. Os fluxos criados por máquinas virtuais na subnet _mySubnet1_ da rede virtual _myVNet_ usarão o gateway NAT. Toda a conectividade de saída utilizará os endereços IP associados ao _myNatGateway_ como endereço IP de origem.


## <a name="design-guidance"></a>Orientação de Design

Reveja esta secção para se familiarizar com considerações para conceber redes virtuais com NAT.  

1. [Otimização de custos](#cost-optimization)
1. [Coexistência de entrada e saída](#coexistence-of-inbound-and-outbound)
2. [Gestão de recursos básicos](#managing-basic-resources)
3. [Zonas de Disponibilidade](#availability-zones)

### <a name="cost-optimization"></a>Otimização de custos

[Os pontos finais](virtual-network-service-endpoints-overview.md) do serviço e a [ligação privada](../private-link/private-link-overview.md) são duas opções a considerar para otimizar o custo onde o NAT não é necessário.  Qualquer tráfego direcionado para pontos finais de serviço ou ligação privada não é processado pelo NAT da rede virtual.  

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

Mesmo sem zonas de disponibilidade, o NAT é resiliente e pode sobreviver a múltiplas falhas na componente de infraestrutura. Quando as zonas de disponibilidade fazem parte do seu cenário, deve configurar o NAT para uma zona específica.  As operações de controlo do avião e o avião de dados estão limitados à zona especificada. Espera-se que o fracasso numa zona diferente da que existe o seu cenário não tenha impacto no NAT. O tráfego de saída de máquinas virtuais na mesma zona falhará devido ao isolamento da zona.

<p align="center">
  <img src="media/nat-overview/az-directions.svg" width="425" title="Rede Virtual NAT com zonas de disponibilidade">
</p>

*Figura: Rede Virtual NAT com zonas de disponibilidade*

Um portal NAT isolado de zona requer endereços IP que correspondam à zona do gateway NAT. Os recursos de gateway NAT com endereços IP de uma zona diferente ou sem uma zona não são suportados.

As redes virtuais e as subredes são regionais e não alinhadas zonais. Um VM deve estar na mesma zona que a porta de entrada da NAT para uma promessa zonal de ligações de saída. O isolamento da zona é criado através da criação de uma "pilha" zonal por zona de disponibilidade. Uma promessa zonal não existirá quando atravessar zonas de um gateway nat zonal ou usar uma porta de entrada nat regional com VMs zonais.

Quando implanta conjuntos de escala de máquinas virtuais para utilizar com NAT, implanta-se uma balança zonal definida na sua própria sub-rede e fixa a porta de entrada NAT da zona correspondente a essa subrede. Se utilizar conjuntos de escala de extensão de zonas (uma escala definida em duas ou mais zonas), o NAT não proporcionará uma promessa zonal.  O NAT não apoia a redundância da zona.  Apenas o isolamento regional ou de zona é apoiado.

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" width="425" title="rede virtual de zona nat">
</p>

*Figura: Rede Virtual nata da rede de zonas*

A propriedade das zonas não é mutável.  Reutilizar o recurso de gateway NAT com a preferência regional ou de zona pretendida.

>[!NOTE] 
>Os endereços IP por si só não são redundantes se nenhuma zona for especificada.  O frontend de um [Balancer de Carga Padrão é redundante](../load-balancer/load-balancer-standard-availability-zones.md#frontend) se um endereço IP não for criado numa zona específica.  Isto não se aplica ao NAT.  Apenas o isolamento regional ou de zona é apoiado.

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

O NAT precisa de um inventário suficiente da porta SNAT para o cenário completo de saída. A escala na tNat é principalmente uma função de gestão do inventário de portas SNAT partilhado e disponível. É necessário um inventário suficiente para fazer face ao caudal máximo de saída de todas as subredes ligadas a um recurso de gateway NAT.

O SNAT mapeia vários endereços privados para um endereço público e utiliza vários IPs públicos à escala.

Um recurso de gateway NAT utilizará 64.000 portas (portas SNAT) de um endereço IP público.  Estas portas SNAT tornam-se o inventário disponível para o mapeamento de fluxos públicos privados. E adicionar mais endereços IP públicos aumenta as portas sNAT de inventário disponíveis. Os recursos de gateway NAT podem escalar até 16 endereços IP e portas SNAT 1M.  TCP e UDP são inventários de portas SNAT separados e não relacionados.

Os recursos de gateway na NAT reutilizam oportunisticamente as portas de origem. Para efeitos de escala, deve assumir que cada fluxo requer uma nova porta SNAT e escalar o número total de endereços IP disponíveis para tráfego de saída.

### <a name="protocols"></a>Protocolos

Os recursos de gateway NAT interagem com os cabeçalhos de transporte IP e IP dos fluxos UDP e TCP e são agnósticos para as cargas da camada de aplicação.  Outros protocolos ip não são suportados.

### <a name="timers"></a>Temporizadores

O tempo limite pode ser ajustado de 4 minutos (predefinido) a 120 minutos (2 horas) para todos os fluxos.  Além disso, pode repor o temporizador inativo com o tráfego no fluxo.  Um padrão recomendado para refrescar ligações longas e deteção de vida de ponto final é a manutenção de TCP.  As vidas de manutenção do TCP aparecem como ACKs duplicados para os pontos finais, são baixos e invisíveis para a camada de aplicação.

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
- A família de endereços IPv4 é apoiada.  O NAT não interage com a família iPv6.  O NAT não pode ser implantado numa sub-rede com prefixo IPv6.
- A exploração de fluxo sanções da NSG não é suportada quando se utiliza NAT.
- O NAT não pode abranger várias redes virtuais.

## <a name="preview-participation"></a>Visualização da participação

Siga [as instruções para ativar a sua subscrição](nat-overview.md#public-preview-participation).

## <a name="feedback"></a>Comentários

Queremos saber como podemos melhorar o serviço. Partilhe connosco o seu [feedback sobre a Pré-Visualização Pública.](https://aka.ms/natfeedback)  E pode propor e votar o que devemos construir a seguir na [UserVoice para o NAT](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Passos seguintes

* Conheça a [rede virtual NAT](nat-overview.md).
* Conheça [métricas e alertas para os recursos](nat-metrics.md)de gateway NAT .
* Saiba mais sobre a resolução de recursos de [gateway na NAT.](troubleshoot-nat.md)
* [Diga-nos o que construir a seguir para a Rede Virtual NAT no UserVoice](https://aka.ms/natuservoice).
* [Forneça feedback sobre a Pré-Visualização Pública](https://aka.ms/natfeedback).
* Tutorial para validar na NaT Gateway
  - [Azure CLI,](tutorial-create-validate-nat-gateway-cli.md)
  - [PowerShell,](tutorial-create-validate-nat-gateway-cli.md)
  - [Portal](tutorial-create-validate-nat-gateway-cli.md)
* Quickstart para implantar um recurso de gateway NAT
  - [Azure CLI,](./quickstart-create-nat-gateway-cli.md)
  - [PowerShell,](./quickstart-create-nat-gateway-powershell.md)
  - [Portal.](./quickstart-create-nat-gateway-portal.md)
* Conheça a API do recurso de gateway NAT
  - [REST API,](https://docs.microsoft.com/rest/api/virtualnetwork/natgateways)
  - [Azure CLI,](https://docs.microsoft.com/cli/azure/network/nat/gateway?view=azure-cli-latest)
  - [PowerShell](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway).
* Conheça [as zonas de disponibilidade.](../availability-zones/az-overview.md)
* Saiba mais sobre o [equilíbrio de carga padrão.](../load-balancer/load-balancer-standard-overview.md)
* Conheça [as zonas de disponibilidade e o equilibrador de carga padrão.](../load-balancer/load-balancer-standard-availability-zones.md)


