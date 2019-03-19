---
title: O que é o Balanceador de Carga do Azure?
titlesuffix: Azure Load Balancer
description: Descrição geral das funcionalidades, da arquitetura e da implementação do Balanceador de Carga do Azure. Saiba como o Balanceador de Carga funciona e como tirar partido do mesmo na cloud.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/11/2019
ms.author: kumud
ms.openlocfilehash: c2f6a614524f0dfb242db11618fda94ce57e6e6a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58111536"
---
# <a name="what-is-azure-load-balancer"></a>O que é o Balanceador de Carga do Azure?

Com o Balanceador de Carga do Azure, pode dimensionar as aplicações e criar elevada disponibilidade para os seus serviços. O Balanceador de Carga suporta cenários de entrada e de saída, oferece baixa latência e alto débito e dimensiona-se até milhões de fluxos para todas as aplicações TCP e UDP.  

O Balanceador de Carga distribui os fluxos de entrada novos que chegam ao front-end do Balanceador de Carga para as instâncias do conjunto de back-ends, de acordo com regras e sondas de estado de funcionamento. 

Além disso, um balanceador de carga público pode fornecer ligações de saída para as máquinas virtuais (VMs) que estejam dentro da sua rede virtual, ao traduzir os respetivos endereços IP em endereços IP públicos.

O Balanceador de carga do Azure está disponível em dois SKUs: Basic e Standard. Há diferenças em termos de dimensionamento, funcionalidades e preços. Qualquer cenário possível no Balanceador de Carga Básico também pode ser criado com o Balanceador de Carga Standard, embora as abordagens possam ser ligeiramente diferentes. À medida que fica a conhecer o Balanceador de Carga, é importante que se familiarize com os conceitos básicos e as diferenças específicas de cada SKU.

## <a name="why-use-load-balancer"></a>Porquê utilizar o Balanceador de Carga 

Pode utilizar o Balanceador de Carga do Azure para:

* Balancear a carga do tráfego de entrada da Internet para as suas VMs. Esta configuração é denominada [Balanceador de Carga Público](#publicloadbalancer).
* Balancear a carga do tráfego entre VMs dentro de uma rede virtual. Também pode alcançar um front-end do Balanceador de Carga a partir de uma rede no local num cenário híbrido. Ambos os cenários utilizam uma configuração conhecida como [Balanceador de Carga Interno](#internalloadbalancer).
* Encaminhar o tráfego de uma porta para uma porta específica em determinadas VMs com regras de tradução de endereços de rede (NAT).
* Utilize um balanceador de carga público para fornecer [conectividade de saída](load-balancer-outbound-connections.md) às VMs dentro da sua rede virtual.


>[!NOTE]
> O Azure oferece um conjunto de soluções de balanceamento de carga totalmente geridas para os seus cenários. Se estiver à procura de terminação de protocolo TLS (Transport Layer Security) (“descarga de SSL”) ou de processamento de camada de aplicação por pedido HTTP/HTTPS, reveja [Gateway de Aplicação](../application-gateway/application-gateway-introduction.md). Se pretender balanceamento de carga de DNS global, reveja [Gestor de Tráfego](../traffic-manager/traffic-manager-overview.md). Combinar estas soluções conforme necessário poderá trazer benefícios aos seus cenários completos.

## <a name="what-are-load-balancer-resources"></a>O que são os recursos do Balanceador de Carga?

Os recursos do Balanceador de Carga podem existir como um balanceador de carga público ou interno. As funções dos recursos do Balanceador de Carga são expressos como um front-end, uma regra, uma sonda de estado de funcionamento e uma definição de conjunto de back-ends. Para colocar as VMs no conjunto de back-ends, especifique esse conjunto a partir da VM.

Os recursos do Balanceador de Carga são objetos dentro dos quais pode expressar de que forma é que o Azure deve programar a sua infraestrutura multi-inquilinos para chegar ao cenário que pretende criar. Não há uma relação direta entre os recursos do Balanceador de Carga e a infraestrutura propriamente dita. A criação de um balanceador de carga não cria uma instância e a capacidade está sempre disponível. 

## <a name="fundamental-load-balancer-features"></a>Funcionalidades básicas do Balanceador de Carga

O Balanceador de Carga proporciona as seguintes capacidades básicas para as aplicações TCP e UDP:

* **Balanceamento de carga**

    Com o Balanceador de Carga do Azure, pode criar uma regra de balanceamento de carga para distribuir o tráfego que chega ao front-end para instâncias do conjunto de back-ends. O Balanceador de Carga utiliza um algoritmo baseado em hash para a distribuição dos fluxos de entrada e reescreve os cabeçalhos dos fluxos nas instâncias do conjunto de back-ends, em conformidade. Um servidor está disponível para receber fluxos novos quando uma sonda de estado de funcionamento indica um ponto final de back-end em bom estado de funcionamento.
    
    Por predefinição, o Balanceador de Carga utiliza um hash de 5 tuplas, o qual é composto por endereço IP de origem, porta de origem, endereço IP de destino, porta de destino e número do protocolo IP (Internet Protocol), para mapear os fluxos para os servidores disponíveis. Numa determinada regra, pode optar por um hash de 2 ou 3 tuplas para escolher criar afinidade com um endereço IP de origem específico. Todos os pacotes do mesmo fluxo de pacotes são entregues na mesma instância, atrás do front-end com carga balanceada. Quando o cliente inicia um fluxo novo a partir do mesmo IP de origem, a porta de origem altera-se. Como resultado, as 5 tuplas podem fazer com que o tráfego vá para outro ponto final de back-end.

    Para obter mais informações, veja [Load Balancer distribution mode](load-balancer-distribution-mode.md) (Modo de distribuição do Balanceador de Carga) A imagem seguinte apresenta a distribuição baseada em hashes:

    ![Distribuição baseada em hashes](./media/load-balancer-overview/load-balancer-distribution.png)

    *Figura: Distribuição baseada em hash*

* **Encaminhamento de portas**

    Com o Balanceador de Carga, pode criar uma regra NAT de entrada para fazer o encaminhamento de portas do tráfego de uma porta específica de um endereço IP de front-end específico para uma porta específica de uma instância de back-end específica dentro da rede virtual. Esse encaminhamento também pode ser obtido pela mesma distribuição baseada em hashes como balanceamento de carga. Os cenários comuns para esta capacidade são as sessões protocolo RDP (Remote Desktop Protocol) ou Secure Shell (SSH) em instâncias de VMs individuais dentro da Rede Virtual do Azure. Pode mapear vários pontos finais internos para as diversas portas no mesmo endereço IP do front-end. Pode usar os endereços IP de front-end para administrar remotamente as suas VMs através da internet sem a necessidade de uma jumpbox adicionais.

* **Independente de aplicações e transparente**

    O Balanceador de Carga não interage diretamente com TCP ou UDP nem com a camada de aplicação e permite qualquer cenário de aplicação TCP ou UDP.  O Balanceador de Carga não termina nem origina fluxos, não interage com o payload dos fluxos nem fornece nenhuma função de gateway de camada de aplicação e o protocolos e os handshakes ocorrem sempre entre o cliente e a instância do conjunto de back-ends.  Uma resposta a um fluxo de entrada é sempre uma resposta de uma máquina virtual.  Quando o fluxo chega à máquina virtual, o endereço IP de origem original também é preservado.  Veja dois exemplos que ilustram melhor a transparência:
    - Cada ponto final só recebe uma resposta de uma VM.  Por exemplo, um handshake de TCP ocorre sempre entre o cliente e a VM de back-end selecionada.  Uma resposta a um pedido a um front-end é uma resposta gerada pela VM de back-end. Quando a conectividade a um front-end é validada com êxito, está a ser validada a conectividade ponto a ponto a, pelo menos, uma máquina virtual de back-end.
    - Os payloads das aplicações são transparentes para o Balanceador de Carga e qualquer aplicação UDP ou TCP pode ser suportada. Relativamente a cargas de trabalho que precisem de processamento por pedido HTTP ou da manipulação dos payloads da camada de aplicação (por exemplo, analisar URLs HTTP), deve utilizar um balanceador de carga de 7 camadas, como o [Gateway de Aplicação](https://azure.microsoft.com/services/application-gateway).
    - Uma vez que o Balanceador de Carga é independente do payload TCP e a descarga de TLS (“SSL”) não é fornecida, pode utilizar o Balanceador de Carga para criar cenários encriptados completos e obter um amplo escalamento horizontal para as aplicações TLS mediante a terminação da ligação TLS na própria VM.  Por exemplo, a capacidade de criação de chaves de sessão TLS só é limitada pelo tipo e o número de VMs que adicionar ao conjunto de back-ends.  Se precisar de “descarga de SSL” ou de tratamento da camada de aplicação ou quiser delegar a gestão de certificados ao Azure, deve utilizar o [Gateway de Aplicação](https://azure.microsoft.com/services/application-gateway), o balanceador de carga de 7 camadas do Azure.
        

* **Reconfiguração automática**

    Quando aumenta ou reduz instâncias verticalmente, o Balanceador de Carga reconfigura-se a si próprio instantaneamente. Adicionar ou remover VMs do conjunto de back-ends reconfigura o Balanceador de Carga sem que sejam necessárias operações adicionais no recurso do Balanceador de Carga.

* **Sondas de estado de funcionamento**

    Para determinar o estado de funcionamento das instâncias no conjunto de back-ends, o Balanceador de Carga utiliza as sondas de estado de funcionamento que forem definidas por si. Quando uma sonda não consegue responder, o Balanceador de Carga deixa de enviar ligações novas para as instâncias em mau estado de funcionamento. As ligações já existentes não são afetadas e continuam até a aplicação terminar o fluxo, até ocorrer um tempo limite de inatividade ou até a VM ser encerrada.
     
    O Balanceador de Carga disponibiliza [diferentes tipos de sondas de estado de funcionamento](load-balancer-custom-probe-overview.md#types) para os pontos finais TCP, HTTP e HTTPS.

    Além disso, ao utilizar os serviços de nuvem de clássico, é permitido um tipo adicional:  [Agente convidado](load-balancer-custom-probe-overview.md#guestagent).  Este deve ser encarado como uma sonda de estado de funcionamento de último recurso e não é recomendado caso as outras opções sejam viáveis.
    
* **Ligações de saída (SNAT)**

    Todos os fluxos de saída de endereços IP privados dentro da sua rede virtual para endereços IP públicos na Internet podem ser traduzidos para um endereço IP de front-end do Balanceador de Carga. Se um front-end público estiver associado a uma VM de back-end por meio de uma regra de balanceamento de carga, o Azure programa as ligações de saída para serem traduzidas automaticamente para o endereço IP do front-end público.

  * Permite a atualização e a recuperação após desastre fácil dos serviços, porque o front-end pode ser mapeado dinamicamente para outra instância do serviço.
  * Facilita a gestão das listas de controlo de acesso (ACL). As ACLs expressas em termos de IPs de front-end não se alteram quando os serviços são aumentados ou reduzidos verticalmente ou reimplementados.  A tradução das ligações de saída para um número mais pequeno de endereços IP do que de máquinas pode reduzir a tarefa de criação de listas de permissões.

    Para obter mais informações, veja [Outbound Connections](load-balancer-outbound-connections.md) (Ligações de Saída).

Para além destas capacidades básicas, o Balanceador de Carga Standard tem capacidades específicas de SKU adicionais. Leia o resto do artigo para ver os detalhes.

## <a name="skus"></a> Comparação de SKUs do Balanceador de Carga

O Balanceador de Carga suporta os SKUs Básico e Standard, cuja escala de cenários, funcionalidades e preços diferem. Qualquer cenário possível no Balanceador de Carga Básico pode ser criado com o Balanceador de Carga Standard. Na verdade, as APIs para ambos os SKUs são semelhantes e invocadas através da especificação de um SKU. A API para suportar SKUs para o Balanceador de Carga e o IP público está disponível a partir da API 2017-08-01. Os dois SKUs têm a mesma API e a mesma estrutura geral.

No entanto, dependendo do SKU que for escolhido, a configuração completa do cenário poderá ser ligeiramente diferente. A documentação do Balanceador de Carga indica se um artigo se aplica apenas a um SKU específico. Para comparar e compreender as diferenças, veja a tabela seguinte. Para obter mais informações, veja [Standard Load Balancer overview](load-balancer-standard-overview.md) (Descrição geral do Balanceador de Carga Standard).

>[!NOTE]
> Os designs novos devem adotar o Balanceador de Carga Standard. 

As VMs autónomas, os conjuntos de disponibilidade e os conjuntos de dimensionamento de máquinas virtuais só podem ser ligados a um SKU, nunca a ambos. Quando os utiliza com os endereços IP públicos, tanto o Balanceador de Carga, como o SKU do endereço IP público, têm de corresponder. O Balanceador de Carga e os SKUs de IP público não são mutáveis entre si.

_É boa prática especificar os SKUs explicitamente, embora não seja obrigatório._  Nesta fase, estamos a manter as alterações obrigatórias no mínimo. Se não for especificado um SKU, pressupõe-se que a intenção é utilizar a versão 2017-08-01 da API do SKU Básico.

>[!IMPORTANT]
>O Balanceador de Carga Standard é um produto novo do Balanceador de Carga e consiste, em grande medida, num superconjunto do Balanceador de Carga Básico. Há diferenças importantes e propositadas entre ambos os produtos. Qualquer cenário completo possível no Balanceador de Carga Básico também pode ser criado com o Balanceador de Carga Standard. Se já estiver habituado ao Balanceador de Carga Básico, deve familiarizar-se com o Standard, para compreender as últimas alterações ao comportamento entre o Básico e o Standard e o respetivo impacto. Leia esta secção atentamente.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Para obter mais informações, veja os [limites de serviço do Balanceador de Carga](https://aka.ms/lblimits). Relativamente aos detalhes do Balanceador de Carga Standard, veja a [descrição geral](load-balancer-standard-overview.md), os [preços](https://aka.ms/lbpricing) e o [SLA](https://aka.ms/lbsla).

## <a name="concepts"></a>Conceitos

### <a name = "publicloadbalancer"></a>Balanceador de Carga Público

Os balanceadores de carga públicos mapeiam o endereço IP público e o número de porta do tráfego de entrada para o endereço IP privado e o número de porta da VM e vice-versa, relativamente ao tráfego de resposta da VM. Mediante a aplicação de regras de balanceamento de carga, pode distribuir tipos específicos de tráfego entre vários serviços ou VMs. Por exemplo, pode distribuir a carga do tráfego de pedidos da Web entre múltiplos servidores Web.

A figura abaixo mostra um ponto final com carga balanceada para tráfego da Web que é partilhado entre três VMs no balanceador de carga público e na porta TCP 80. Estas três VMs estão num conjunto com carga balanceada.

![Exemplo de balanceador de carga público](./media/load-balancer-overview/IC727496.png)

*Figura: Balanceamento de tráfego da web utilizando um balanceador de carga público*

Quando os clientes de Internet enviam pedidos de páginas Web para o endereço IP público de uma aplicação Web na porta TCP 80, o Balanceador de Carga do Azure distribui-os entre as três VMs no conjunto com carga balanceada. Para obter mais informações sobre os algoritmos do Balanceador de Carga, veja a secção [Funcionalidade do Balanceador de Carga](load-balancer-overview.md##fundamental-load-balancer-features) deste artigo.

Por predefinição, o Balanceador de Carga do Azure distribui o tráfego de rede igualmente entre várias instâncias da VM. Também pode configurar a afinidade de sessão. Para obter mais informações, veja [Load Balancer distribution mode](load-balancer-distribution-mode.md) (Modo de distribuição do Balanceador de Carga)

### <a name = "internalloadbalancer"></a> Balanceador de Carga Interno

Os balanceadores de carga internos direcionam o tráfego apenas para recursos que estejam dentro de uma rede virtual ou que acedam à infraestrutura do Azure através de uma VPN. Neste aspeto, estes balanceadores de carga são diferentes dos públicos. A infraestrutura do Azure limita o acesso aos endereços IP do front-end com carga balanceada de uma rede virtual. endereços IP de front-end e redes virtuais são expostas nunca diretamente para um ponto de extremidade de internet. As aplicações de linha de negócio internas são executadas no Azure e acedidas de dentro do Azure ou a partir de recursos no local.

Os balanceadores de carga internos permitem os seguintes tipos de balanceador de carga:

* **Dentro de uma rede virtual**: Balanceamento de carga de VMs na rede virtual para um conjunto de VMs que residam na mesma rede virtual.
* **Para uma rede virtual em vários locais**: Balanceamento de carga de computadores no local para um conjunto de VMs que residam na mesma rede virtual. 
* **Para aplicações de várias camadas**: O balanceamento de carga para aplicações de várias camadas de acesso à internet em que os escalões de back-end não são destinados à internet. As camadas do back-end requerem o balanceamento de carga do tráfego da camada acessível pela Internet (veja a figura abaixo).
* **Para aplicações de linha de negócio**: Balanceamento de carga para aplicativos de linha de negócio que estão alojados no Azure sem hardware de Balanceador de carga adicional ou software. Este cenário inclui servidores no local que estão no conjunto de computadores cujo tráfego tem a carga balanceada.

![Exemplo de Balanceador de Carga Interno](./media/load-balancer-overview/IC744147.png)

*Figura: Balanceamento de carga aplicações de várias camadas, com Balanceador de carga públicos e internos*

## <a name="pricing"></a>Preços

Utilização de Balanceador de carga Standard é cobrada.

- Número de configurado regras de balanceamento de carga e de saída (regras NAT de entrada não contam para o número total de regras)
- Quantidade de dados processados entrada e saída irrespective de regra. 

Para obter as informações de preços do Balanceador de Carga Standard, aceda à página [Preços de Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

O Balanceador de Carga Básico é disponibilizado sem custos.

## <a name="sla"></a>SLA

Para obter informações sobre o SLA do Balanceador de Carga Standard, aceda à página [SLA para Load Balancer](https://aka.ms/lbsla). 

## <a name="limitations"></a>Limitações

- O Balanceador de Carga é um produto TCP ou UDP para balanceamento de carga e encaminhamento de portas para estes dois protocolos IP específicos.  As regras de balanceamento de carga e as regras NAT de entrada são suportadas para TCP e UDP, mas não para os outros protocolos IP, incluindo o ICMP. O Balanceador de Carga não termina, não responde nem interage com o payload dos fluxos UDP ou TCP. Não é um proxy. A validação bem-sucedida da conectividade a um front-end deve ter lugar na banda com o mesmo protocolo utilizado numa regra de balanceamento de carga ou de NAT de entrada (TCP ou UDP) _e_, para que um cliente veja uma resposta de um front-end, pelo menos uma das suas máquinas virtuais tem de gerar uma resposta.  Não receber uma resposta em banda do front-end do Balanceador de Carga indica que nenhuma máquina virtual conseguiu responder.  Não é possível interagir com um front-end do Balanceador de Carga sem uma máquina virtual que consiga responder.  Isto também se aplica às ligações de saída, em que o [SNAT de máscara de rede](load-balancer-outbound-connections.md#snat) só é suportado para TCP e UDP; qualquer outro protocolo IP, incluindo ICMP, falhará.  Para mitigar o problema, atribua um endereço IP público ao nível da instância.
- Ao contrário dos balanceadores de carga públicos, que fornecem [ligações de saída](load-balancer-outbound-connections.md) durante a transição de endereços IP privados dentro da rede virtual para endereços IP públicos, os balanceadores de carga internos não traduzem as ligações originadas na saída para o front-end de um balanceador de carga interno, porque ambos estão no espaço de endereços IP privados.  Isto evita a possibilidade de esgotamento a porta SNAT dentro de um espaço de endereços IP internos exclusivo em que a tradução não é necessária.  O efeito secundário é que, caso um fluxo de saída de uma VM no conjunto de back-ends tentar um fluxo para o front-end do balanceador de carga interno no conjunto em que reside _e_ é mapeado para o próprio, as partes do fluxo não corresponderão e o fluxo falhará.  Se o fluxo não se tiver mapeado para a mesma VM no conjunto de back-ends que criou o fluxo para o front-end, o fluxo será bem-sucedido.   Quando o fluxo se mapeia para si próprio, parecerá que o fluxo de saída tem origem na VM para o front-end e o fluxo de entrada correspondente parecerá ter origem na VM para si próprio. Do ponto de vista do SO convidado, as partes de entrada e saída do mesmo fluxo não correspondem dentro da máquina virtual. A pilha TCP não reconhecerá essas partes do fluxo como fazendo parte do mesmo fluxo, pois a origem e o destino não correspondem.  Quando o fluxo se mapeia para outra VM no conjunto de back-ends, as partes do mesmo corresponderão e a VM poderá responder corretamente ao fluxo.  O sintoma deste cenário são tempos limite de ligação intermitentes quando o fluxo regressa ao mesmo back-end que o originou. Existem várias soluções comuns para chegar a este cenário de forma fiável (fluxos com origem num conjunto de back-ends para os conjuntos de back-end ou para o front-end do balanceador de carga interno), que incluem a inserção de uma camada de proxy por trás do balanceador de carga interno ou a [utilização de regras de estilo DSR](load-balancer-multivip-overview.md).  Os clientes podem combinar um balanceador de carga interno com qualquer proxy de terceiros ou substituir o [Gateway de Aplicação](../application-gateway/application-gateway-introduction.md) interno para os cenários de proxy limitados a HTTP/HTTPS. Embora possa utilizar um balanceador de carga público para mitigar o problema, o cenário resultante é propenso a [esgotamento de SNAT](load-balancer-outbound-connections.md#snat) e deve ser evitado, salvo se for gerido cuidadosamente.

## <a name="next-steps"></a>Passos Seguintes

Agora, já tem uma perceção geral do Balanceador de Carga do Azure. Para começar a utilizar um balanceador de carga, crie um balanceador, crie VMs com uma extensão de IIS personalizada instalada e faça o balanceamento da carga da aplicação Web entre as VMs. Para saber como o fazer, veja o início rápido [Criar um Balanceador de Carga Básico](quickstart-create-basic-load-balancer-portal.md).
