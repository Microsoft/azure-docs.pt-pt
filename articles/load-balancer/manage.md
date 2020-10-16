---
title: Definições do portal Azure Load Balancer
description: Começar a aprender sobre as definições do portal Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/8/2020
ms.author: allensu
ms.openlocfilehash: e1080aea12e70f4312fbee07b063d5a5cfbd1201
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89596312"
---
# <a name="azure-load-balancer-portal-settings"></a>Definições do portal Azure Load Balancer

Ao criar o Azure Load Balancer, as informações neste artigo irão ajudá-lo a aprender mais sobre as definições individuais e qual é a configuração certa para si.

## <a name="create-load-balancer"></a>Criar um balanceador de carga

O Azure Load Balancer é um equilibrador de carga de rede que distribui o tráfego através de instâncias VM na piscina de backend.

### <a name="basics"></a>Noções básicas

No separador Básicos da página do portal do balanceador de carga, verá as **seguintes** informações:



| Definição |  Detalhes |
| ---------- | ---------- |
| Subscrição  | Selecione a sua subscrição. Esta seleção é a subscrição em que pretende que o seu equilibrador de carga seja implantado. |
| Grupo de recursos | **Selecione Criar novo** e escrever o nome para o seu grupo de recursos na caixa de texto. Se tiver um grupo de recursos existente criado, selecione-o. |
| Nome | Esta definição é o nome do seu Balançador de Carga Azure. |
| Region | Selecione uma região Azure em que gostaria de colocar o seu equilibrador de carga. |
| Tipo | O equilibrador de carga tem dois tipos: </br> **Interno (Privado)** </br> **Público (Externo)**.</br> Um equilibrador de carga interno (ILB) encaminha o tráfego para remusar os membros do pool através de um endereço IP privado.</br> Um equilibrador de carga pública direciona os pedidos dos clientes através da internet para a piscina de backend.</br> Saiba mais sobre [tipos de equilibradores de carga.](components.md#frontend-ip-configuration-)|
| SKU  | Selecione **Standard**. </br> O equilibrador de carga tem dois SKUs: **Básico** e **Standard**. </br> Basic tem funcionalidade limitada. </br> **Recomenda-se** a norma para a carga de trabalho de produção. </br> Saiba mais sobre [os SKUs.](skus.md) |

Se selecionar **o Público** como seu tipo, verá as seguintes informações:

| Definição |  Detalhes |
| ---------- | ---------- |
| Endereço IP público | **Selecione Criar novo** para criar um endereço IP público para o seu balanceador de carga pública. </br> Se tiver um IP público existente, selecione **Utilizar a existência**.  |
| Nome do endereço IP público | O nome do endereço IP público.|
| Endereço IP público SKU | Os endereços IP públicos têm dois SKUs: **Básico** e **Standard**. </br> O básico não suporta a resiliência da zona e os atributos zonais. </br> **Recomenda-se** a norma para a carga de trabalho de produção. </br> O balançador de carga e o endereço IP público SKUs **devem coincidir**. |
| Atribuição | **A estática** é selecionada automaticamente para o padrão. </br> Os IPs públicos básicos têm dois tipos: **Dinâmico** e **Estático.** </br> Os endereços IP públicos dinâmicos não são atribuídos até à criação. </br> Os IPs podem ser perdidos se o recurso for eliminado. </br> São recomendados endereços IP estáticos. |
| Zona de disponibilidade | Selecione **Zona redundante** para criar um equilibrador de carga resistente. </br> Para criar um equilibrador de carga zonal, selecione uma zona específica a partir de **1,** **2**ou **3**. </br> Balanceador de carga padrão e zonas de suporte de IPs públicos. </br> Saiba mais sobre [o balanceador de carga e as zonas de disponibilidade.](load-balancer-standard-availability-zones.md) </br> Não verá seleção de zona para o básico. O equilibrador de carga básico não suporta zonas. |
| Adicionar um endereço IPv6 público | O equilibrador de carga suporta endereços **IPv6** para o seu frontend. </br> Saiba mais sobre [a carga Balancer e IPv6](load-balancer-ipv6-overview.md)
| Preferência de encaminhamento | Selecione **Microsoft Network**. </br> A Microsoft Network significa que o tráfego é encaminhado através da rede global da Microsoft. </br> A Internet significa que o tráfego é encaminhado através da rede de fornecedores de serviços de internet. </br> Saiba mais sobre [Preferências de Encaminhamento](../virtual-network/routing-preference-overview.md)|

:::image type="content" source="./media/manage/create-public-load-balancer-basics.png" alt-text="Criar o balanceador de carga público." border="true":::

Se selecionar **Internal** in Type, verá as seguintes informações:

| Definição |  Detalhes |
| ---------- | ---------- |
| Rede virtual | A rede virtual de que pretende fazer parte do seu equilibrador interno. </br> O endereço IP de frontend privado que seleciona para o seu balançador de carga interno será desta rede virtual. |
| Atribuição de endereços IP | As suas opções são **Estáticas** ou **Dinâmicas.** </br> A estática garante que o IP não muda. Um IP dinâmico pode mudar. |
| Zona de disponibilidade | As opções são: </br> **Zona redundante** </br> **Zona 1** </br> **Zona 2** </br> **Zona 3** </br> Para criar um equilibrador de carga altamente disponível e resistente a falhas de zona de disponibilidade, selecione um IP **redundante de zona.** |

:::image type="content" source="./media/manage/create-internal-load-balancer-basics.png" alt-text="Criar o balanceador de carga público." border="true":::

## <a name="frontend-ip-configuration"></a>Configuração IP frontend

O endereço IP do seu Balançador de Carga Azure. É o ponto de contacto dos clientes. 

Pode ter uma ou muitas configurações IP frontend. Se tivesse passado pela secção básica acima, já teria criado um frontend para o seu equilibrador de carga. 

Se pretender adicionar uma configuração IP frontal ao seu equilibrador de carga, vá ao seu equilibrador de carga no portal Azure, selecione **a configuração IP frontend**e, em seguida, selecione **+Adicionar**.

| Definição |  Detalhes |
| ---------- | ---------- |
| Nome | O nome da configuração IP do frontend. |
| Versão do IP | A versão ip address que gostaria que o seu frontend tivesse. </br> O equilibrador de carga suporta as configurações IP do iPv4 e do IPv6 frontend. |
| Tipo IP | O tipo IP determina se um único endereço IP está associado ao seu frontend ou a uma série de endereços IP utilizando um Prefix IP. </br> Um [prefixo IP público](../virtual-network/public-ip-address-prefix.md) ajuda quando precisa de ligar-se repetidamente ao mesmo ponto final. O prefixo garante que são dadas portas suficientes para ajudar nas questões portuárias do SNAT. |
| Endereço IP público (ou Prefixo se selecionou prefixo acima) | Selecione ou crie um novo IP público (ou prefixo) para o frontend do seu balançador de carga. |

:::image type="content" source="./media/manage/frontend.png" alt-text="Criar o balanceador de carga público." border="true":::

## <a name="backend-pools"></a>Piscinas de backend

Um conjunto de endereços de backend contém os endereços IP das interfaces de rede virtuais no pool backend. 

Se pretender adicionar uma piscina de backend ao seu equilibrador de carga, vá ao seu equilibrador de carga no portal Azure, selecione **pools backend**, e, em seguida, selecione **+Adicionar**.

| Definição | Detalhes |
| ---------- |  ---------- |
| Nome | O nome da sua piscina de backend. |
| Rede virtual | A rede virtual que os seus casos de backend são. |
| Versão do IP | As suas opções são **IPv4** ou **IPv6**. |

Pode adicionar máquinas virtuais ou conjuntos de balanças de máquinas virtuais à piscina de backend do seu Balançador de Carga Azure. Crie primeiro as máquinas virtuais ou os conjuntos de balança de máquinas virtuais. Em seguida, adicione-os ao equilibrador de carga no portal.

:::image type="content" source="./media/manage/backend.png" alt-text="Criar o balanceador de carga público." border="true":::

## <a name="health-probes"></a>Sondas do estado de funcionamento

Uma sonda de saúde é usada para monitorizar o estado dos seus VMs ou instâncias de backend. O estado da sonda de saúde determina quando novas ligações são enviadas para um caso baseado em exames de saúde. 

Se pretender adicionar uma sonda de saúde ao seu equilibrador de carga, vá ao seu equilibrador de carga no portal Azure, selecione **sondas de saúde,** em seguida, selecione **+Adicionar**.

| Definição | Detalhes |
| ---------- | ---------- |
| Nome | O nome da sua sonda de saúde. |
| Protocolo | O protocolo selecionado determina o tipo de verificação utilizado para determinar se a(s) instância de backend(s) é saudável. </br> As opções são: </br> **TCP** </br> **HTTPS** </br> **HTTP** </br> Certifique-se de que está a usar o protocolo certo. Esta seleção dependerá da natureza da sua candidatura. </br> A configuração da sonda de saúde e as respostas da sonda determinam quais as instâncias de backend pool que receberão novos fluxos. </br> Pode utilizar sondas de saúde para detetar a falha de uma aplicação num ponto final de backend. </br> Saiba mais sobre [as sondas de saúde.](load-balancer-custom-probe-overview.md) |
| Porta | O porto de destino para a sonda de saúde. </br> Esta definição é a porta no caso de backend que a sonda de saúde usará para determinar a saúde do caso. |
| Intervalo | O número de segundos entre tentativas de sonda. </br> O intervalo determinará com que frequência a sonda de saúde tentará chegar à instância de backend. </br> Se selecionar 5, a segunda tentativa de sonda será feita após 5 segundos e assim por diante. |
| Limiar com funcionamento incorreto | O número de falhas consecutivas da sonda que devem ocorrer antes de um VM é considerado insalubre.</br> Se selecionar 2, não serão definidos novos fluxos para esta instância de backend após duas falhas consecutivas. |

:::image type="content" source="./media/manage/health-probe.png" alt-text="Criar o balanceador de carga público." border="true":::

## <a name="load-balancing-rules"></a>Regras de equilíbrio de carga

Define como o tráfego de entrada é distribuído a todas as instâncias dentro da piscina de backend. Uma regra de equilíbrio de carga mapeia uma determinada configuração IP frontal e porta para vários endereços IP backend e portas.

Se pretender adicionar uma regra do balançador de carga ao seu equilibrador de carga, vá ao seu equilibrador de carga no portal Azure, selecione **regras de equilíbrio de cargas**e, em seguida, selecione **+Adicionar**.
    
| Definição | Detalhes |
| ---------- | ---------- |
| Nome | O nome da regra do balançador de carga. |
| Versão IP | As suas opções são **IPv4** ou **IPv6**.  |
| Endereço IP frontend | Selecione o endereço IP frontend. </br> O endereço IP frontend do seu equilibrador de carga deseja a regra do balançador de carga associada.|
| Protocolo | Azure Load Balancer é um equilibrador de carga de rede de camada 4. </br> As suas opções são: **TCP** ou **UDP**. |
| Porta | Esta definição é a porta associada ao IP frontend que pretende que o tráfego seja distribuído com base nesta regra de equilíbrio de carga. |
| Porta de back-end | Esta definição é a porta nas instâncias da piscina de backend para onde pretende que o equilibrador de carga envie o tráfego. Esta definição pode ser a mesma que a porta frontal ou diferente se precisar da flexibilidade para a sua aplicação. |
| Conjunto de back-end | O pool de backend que você gostaria que esta regra do balançador de carga fosse aplicada. |
| Sonda de estado de funcionamento | A sonda de saúde que criou para verificar o estado das instâncias na piscina de backend. </br> Só casos saudáveis receberão novo tráfego. |
| Persistência da sessão |  As opções são: </br> **Nenhuma** </br> **IP do Cliente** </br> **IP do cliente e protocolo**</br> </br> Mantenha o tráfego de um cliente para a mesma máquina virtual na piscina de backend. Este tráfego será mantido durante a duração da sessão. </br> **Nenhum** especifica que os pedidos sucessivos do mesmo cliente podem ser tratados por qualquer máquina virtual. </br> **O Client IP** especifica que os pedidos sucessivos a partir do mesmo endereço IP do cliente serão tratados pela mesma máquina virtual. </br> **O IP do cliente e** o protocolo asseguram que os pedidos sucessivos do mesmo endereço IP do cliente e do protocolo serão tratados pela mesma máquina virtual. </br> Saiba mais sobre [os modos de distribuição.](load-balancer-distribution-mode.md) |
| Tempo de 20 minutos (minutos) | Mantenha uma ligação **TCP** ou **HTTP** aberta sem depender dos clientes para enviar mensagens de vida |  
| Reset TCP | O equilibrador de carga pode enviar **resets de TCP** para ajudar a criar um comportamento de aplicação mais previsível quando a ligação está inativa. </br> Saiba mais sobre [o reset da TCP](load-balancer-tcp-reset.md)|
| IP Flutuante | Ip flutuante é a terminologia de Azure para uma parte do que é conhecido como **Retorno direto do Servidor (DSR)**. </br> A DSR é constituída por duas partes: <br> 1. Topologia do fluxo </br> 2. Um esquema de mapeamento de endereços IP a nível da plataforma. </br></br> O Azure Load Balancer funciona sempre numa topologia de fluxo DSR quer o IP flutuante esteja ativado ou não. </br> Esta operação significa que a parte de saída de um fluxo é sempre corretamente reescrita para fluir diretamente de volta à origem. </br> Sem IP flutuante, a Azure expõe um esquema tradicional de mapeamento de endereços IP equilibrador de carga, o IP das instâncias VM. </br> Ativar o IP flutuante altera o mapeamento do endereço IP para o IP frontend do Balancer de carga para permitir uma flexibilidade adicional. </br> Para obter mais informações, consulte [vários frontends para Azure Load Balancer](load-balancer-multivip-overview.md).|
| Criar regras implícitas de saída | Selecione **Não**. </br> **Predefinição: desativarOutboundSnat = falso**  </br> Neste caso, a saída ocorre através do mesmo FRONTend IP. </br></br> **desativarOutboundSnat = verdadeiro** </br>Neste caso, são necessárias regras de saída para a saída. |

:::image type="content" source="./media/manage/load-balancing-rule.png" alt-text="Criar o balanceador de carga público." border="true":::

## <a name="inbound-nat-rules"></a>Regras NAT de entrada

Uma regra NAT de entrada encaminha o tráfego de entrada enviado para o endereço IP frontend e combinação de porta. 

O tráfego é enviado para uma máquina virtual específica ou instância na piscina de backend. O encaminhamento portuário é feito pela mesma distribuição baseada em haxixe que o equilíbrio de carga.

Se o seu cenário requer sessões de Protocolo de Ambiente de Trabalho Remoto (RDP) ou Secure Shell (SSH) para separar as instâncias VM num pool de backend. Vários pontos finais internos podem ser mapeados para portas no mesmo endereço IP frontend. 

Os endereços IP frontend podem ser utilizados para administrar remotamente os seus VMs sem uma caixa de salto adicional.

Se pretender adicionar uma regra nat de entrada ao seu equilibrador de carga, vá ao seu equilibrador de carga no portal Azure, selecione **as regras INBOUND NAT**e, em seguida, selecione **+Adicionar**.

| Definição | Detalhes |
| ---------- | ---------- |
| Nome | O nome da sua regra NAT de entrada |
| Endereço IP frontend | Selecione o endereço IP frontend. </br> O endereço IP frontend do seu equilibrador de carga deseja a regra NAT de entrada associada. |
| Versão IP | As suas opções são IPv4 e IPv6. |
| Serviço | O tipo de serviço que vai executar no Azure Load Balancer. </br> Uma seleção aqui atualizará adequadamente as informações do porto. |
| Protocolo | Azure Load Balancer é um equilibrador de carga de rede de camada 4. </br> As suas opções são: TCP ou UDP. |
| Tempo de 20 minutos (minutos) | Mantenha uma ligação TCP ou HTTP aberta sem depender dos clientes para enviar mensagens de vida. |
| TCP Reset | O Balancer de Carga pode enviar resets de TCP para ajudar a criar um comportamento de aplicação mais previsível quando a ligação está inativa. </br> Saiba mais sobre [o reset da TCP](load-balancer-tcp-reset.md) |
| Porta | Esta definição é a porta associada ao IP frontend que pretende que o tráfego seja distribuído com base nesta regra NAT de entrada. |
| Máquina virtual alvo | A parte virtual da máquina do pool de backend a que gostaria que esta regra fosse associada. |
| Mapeamento portuário | Esta definição pode ser padrão ou personalizada com base na sua preferência de aplicação. |

:::image type="content" source="./media/manage/inbound-nat-rule.png" alt-text="Criar o balanceador de carga público." border="true":::

## <a name="outbound-rules"></a>Regras de saída

As regras de saída do balançador de carga configuram sNAT de saída para VMs na piscina de backend.

Se pretender adicionar uma regra de saída ao seu equilibrador de carga, vá ao seu equilibrador de carga no portal Azure, selecione **as regras de saída**e, em seguida, selecione **+Adicionar**.

| Definição | Detalhes |
| ------- | ------ |
| Nome | O nome da sua regra de saída. |
| Endereço IP frontend | Selecione o endereço IP frontend. </br> O endereço IP frontend do seu equilibrador de carga deseja que a regra de saída seja associada. |
| Protocolo | Azure Load Balancer é um equilibrador de carga de rede de camada 4. </br> As suas opções são: **Todas,** **TCP**ou **UDP.** |
| Tempo de 20 minutos (minutos) | Mantenha uma ligação **TCP** ou **HTTP** aberta sem depender dos clientes para enviar mensagens de vida. |
| TCP Reset | O equilibrador de carga pode enviar **resets de TCP** para ajudar a criar um comportamento de aplicação mais previsível quando a ligação está inativa. </br> Saiba mais sobre [o reset da TCP](load-balancer-tcp-reset.md) |
| Conjunto de back-end | A piscina de backend que você gostaria que esta regra de saída fosse aplicada. |

### <a name="port-allocation"></a>Dotação portuária

| Definição | Detalhes |
| ------- | ------ |
| Dotação portuária | Recomendamos selecionar **manualmente o número de portas de saída.**|

### <a name="outbound-ports"></a>Portas de saída

| Definição | Detalhes |
| ------- | ------ |
| Escolha por | Selecione **portas por exemplo** |
| Portos por exemplo | Insira **10.000**. |

:::image type="content" source="./media/manage/outbound-rule.png" alt-text="Criar o balanceador de carga público." border="true":::

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu sobre os diferentes termos e configurações no portal Azure para O Balançador de Carga Azure.

* [Saiba](./load-balancer-overview.md) mais sobre o Azure Load Balancer.
* [Perguntas frequentes](./load-balancer-faqs.md) para Azure Load Balancer.