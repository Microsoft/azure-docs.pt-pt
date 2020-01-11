---
title: O que é o Balanceador de Carga do Azure?
titleSuffix: Azure Load Balancer
description: Descrição geral das funcionalidades, da arquitetura e da implementação do Balanceador de Carga do Azure. Saiba como o Load Balancer funciona e como aproveitá-lo na nuvem.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2019
ms.author: allensu
ms.openlocfilehash: c4f06afb63279f2b493c8938da2e9954b2c7e6c3
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894475"
---
# <a name="what-is-azure-load-balancer"></a>O que é o Balanceador de Carga do Azure?

O *balanceamento de carga* refere-se à distribuição eficiente de carga ou tráfego de rede de entrada em um grupo de recursos de back-end ou servidores. O Azure oferece uma [variedade de opções de balanceamento de carga](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) que você pode escolher de acordo com suas necessidades. Este documento aborda o Azure Load Balancer.

Azure Load Balancer opera na camada quatro do modelo OSI (interconexão de sistemas abertos). É o único ponto de contato para clientes. O Load Balancer distribui novos fluxos de entrada que chegam às instâncias de front-end do pool de back-ends do Load Balancer, de acordo com as regras de balanceamento de carga e as investigações de integridade especificadas. As instâncias do pool de back-end podem ser máquinas virtuais ou instâncias do Azure em um conjunto de dimensionamento de máquinas virtuais. 

Com o Azure Load Balancer, você pode dimensionar seus aplicativos e criar serviços de alta disponibilidade. O Load Balancer dá suporte a cenários de entrada e de saída, fornece baixa latência e alta taxa de transferência e escala verticalmente para milhões de fluxos para todos os aplicativos TCP e UDP.

Um **[Load Balancer público](#publicloadbalancer)** pode fornecer conexões de saída para VMs (máquinas virtuais) dentro de sua rede virtual, convertendo seus endereços IP privados em endereços IP públicos. Os balanceadores de carga públicos são usados para o tráfego de Internet do balanceador de carga para suas VMs.

Um **[Load balancer interno (ou privado)](#internalloadbalancer)** pode ser usado para cenários em que apenas endereços IP privados são necessários no front-end. Os balanceadores de carga internos são usados para balancear a carga do tráfego dentro de uma rede virtual. Você também pode acessar um front-end Load Balancer de uma rede local em um cenário híbrido.

## <a name="load-balancer-components"></a>Componentes do Load Balancer
* **Configurações de IP de front-end**: o endereço IP do Load Balancer. É o ponto de contato para clientes. Eles podem ser endereços IP públicos ou privados, criando, portanto, os balanceadores de carga públicos ou internos, respectivamente.
* **Pool de back-end**: o grupo de máquinas virtuais ou instâncias no conjunto de dimensionamento de máquinas virtuais que vai atender à solicitação de entrada. Para dimensionar de forma econômica para atender a grandes volumes de práticas recomendadas de computação de tráfego de entrada geralmente recomenda a adição de mais instâncias ao pool de back-end. Load Balancer reconfigura-se instantaneamente por meio da reconfiguração automática quando você dimensiona as instâncias para cima ou para baixo. Adicionar ou remover VMs do conjunto de back-ends reconfigura o Balanceador de Carga sem que sejam necessárias operações adicionais no recurso do Balanceador de Carga.
* **Investigações de integridade**: uma investigação de integridade é usada para determinar a integridade das instâncias no pool de back-end. Você pode definir o limite não íntegro para suas investigações de integridade. Quando uma sonda não consegue responder, o Balanceador de Carga deixa de enviar ligações novas para as instâncias em mau estado de funcionamento. Uma falha de investigação não afeta as conexões existentes. A conexão continua até que o aplicativo encerre o fluxo, um tempo limite de ociosidade ocorra ou a VM seja desligada. Load Balancer fornece tipos diferentes de investigação de integridade para pontos de extremidade TCP, HTTP e HTTPS. Para obter mais informações, consulte [tipos de investigação](load-balancer-custom-probe-overview.md#types).
* **Regras de balanceamento**de carga: as regras de balanceamento de carga são aquelas que dizem ao Load Balancer o que precisa ser feito quando. 
* **Regras de NAT de entrada**: uma regra NAT de entrada encaminha o tráfego de uma porta específica de um endereço IP de front-end específico para uma porta específica de uma instância de back-end específica dentro da rede virtual. O encaminhamento de porta é feito pela mesma distribuição baseada em hash que o balanceamento de carga. Cenários comuns para esse recurso são protocolo RDP (RDP) ou Secure Shell (SSH) sessões para instâncias de VM individuais dentro de uma rede virtual do Azure. Você pode mapear vários pontos de extremidade internos para portas no mesmo endereço IP de front-end. Você pode usar os endereços IP de front-end para administrar remotamente suas VMs sem uma caixa de salto adicional.
* **Regras de saída**: uma regra de saída configura a NAT (conversão de endereços de rede) de saída para todas as máquinas virtuais ou instâncias identificadas pelo pool de back-end a serem convertidas no front-end.

## <a name="load-balancer-concepts"></a>Conceitos de Load Balancer

O Balanceador de Carga proporciona as seguintes capacidades básicas para as aplicações TCP e UDP:

* **Algoritmo de balanceamento de carga**: com Azure Load Balancer, você pode criar uma regra de balanceamento de carga para distribuir o tráfego que chega às instâncias do pool de front-ends para o back-end. Load Balancer usa um algoritmo de hash para distribuição de fluxos de entrada e reescreve os cabeçalhos de fluxos para instâncias de pool de back-end. Um servidor está disponível para receber novos fluxos quando uma investigação de integridade indica um ponto de extremidade de back-end íntegro.
Por padrão, Load Balancer usa um hash de 5 tuplas. O hash inclui o endereço IP de origem, a porta de origem, o endereço IP de destino, a porta de destino e o número do protocolo IP para mapear os fluxos para os servidores disponíveis. Você pode criar afinidade para um endereço IP de origem usando um hash de 2 ou 3 tuplas para uma determinada regra. Todos os pacotes do mesmo fluxo de pacotes são entregues na mesma instância, atrás do front-end com carga balanceada. Quando o cliente inicia um novo fluxo do mesmo IP de origem, a porta de origem é alterada. Como resultado, o hash de 5 tuplas pode fazer com que o tráfego vá para um ponto de extremidade de back-end diferente.
Para obter mais informações, consulte [Configurar o modo de distribuição para Azure Load Balancer](load-balancer-distribution-mode.md). A imagem seguinte apresenta a distribuição baseada em hashes:

  ![Distribuição baseada em hashes](./media/load-balancer-overview/load-balancer-distribution.png)

  *Figura: distribuição baseada em hashes*

* **Independência de aplicativo e transparência**: Load Balancer não interage diretamente com TCP ou UDP ou a camada de aplicativo. Qualquer cenário de aplicativo TCP ou UDP pode ter suporte. Load Balancer não encerra ou origina fluxos, interage com a carga do fluxo ou fornece qualquer função de gateway de camada de aplicativo. Os Handshakes de protocolo sempre ocorrem diretamente entre o cliente e a instância do pool de back-end. Uma resposta a um fluxo de entrada é sempre uma resposta de uma máquina virtual. Quando o fluxo chega à máquina virtual, o endereço IP de origem original também é preservado.
  * Cada ponto final só recebe uma resposta de uma VM. Por exemplo, um handshake TCP sempre ocorre entre o cliente e a VM de back-end selecionada. Uma resposta a uma solicitação para um front-end é uma resposta gerada por uma VM de back-end. Quando você valida com êxito a conectividade para um front-end, você está validando a conectividade de ponta a ponta para pelo menos uma máquina virtual de back-end.
  * As cargas de aplicativo são transparentes para Load Balancer. Qualquer aplicativo UDP ou TCP pode ter suporte.
  * Como Load Balancer não interage com a carga de TCP e fornece o descarregamento de TLS, você pode criar cenários criptografados de ponta a ponta. O uso de Load Balancer ganha grande escala horizontal para aplicativos TLS encerrando a conexão TLS na própria VM. Por exemplo, a capacidade de criação de chaves de sessão TLS é limitada apenas pelo tipo e pelo número de VMs que você adiciona ao pool de back-ends.

* **Conexões de saída (SNAT)** : todos os fluxos de saída de endereços IP privados dentro de sua rede virtual para endereços IP públicos na Internet podem ser convertidos em um endereço IP de front-end da Load Balancer. Quando um front-end público está vinculado a uma VM de back-end por meio de uma regra de balanceamento de carga, o Azure converte as conexões de saída para o endereço IP público de front-end. Essa configuração tem as seguintes vantagens:
  * Atualização fácil e recuperação de desastre de serviços, pois o front-end pode ser mapeado dinamicamente para outra instância do serviço.
  * Gerenciamento de ACL (lista de controle de acesso) mais fácil. As ACLs expressas como IPs de front-end não mudam à medida que os serviços são expandidos ou reduzidos ou reimplantados. A tradução de conexões de saída para um número menor de endereços IP do que computadores reduz a carga da implementação de listas de destinatários seguros.
Para obter mais informações, consulte [conexões de saída no Azure](load-balancer-outbound-connections.md).
O Standard Load Balancer tem recursos adicionais específicos do SKU além desses conceitos básicos, conforme descrito abaixo.

## <a name="skus"></a> Comparação de SKUs do Balanceador de Carga

O Load Balancer dá suporte a SKUs Basic e Standard. Essas SKUs diferem em escala de cenário, recursos e preços. Qualquer cenário possível com Load Balancer básica pode ser criado com Standard Load Balancer. As APIs para ambos os SKUs são semelhantes e são invocadas por meio da especificação de uma SKU. A API para oferecer suporte a SKUs para Load Balancer e o IP público está disponível a partir da API de `2017-08-01`. Ambos os SKUs compartilham a mesma API e estrutura geral.

A configuração completa do cenário pode variar ligeiramente dependendo do SKU. A documentação do Balanceador de Carga indica se um artigo se aplica apenas a um SKU específico. Para comparar e compreender as diferenças, veja a tabela seguinte. Para obter mais informações, consulte [visão geral do Azure Standard Load Balancer](load-balancer-standard-overview.md).

>[!NOTE]
> A Microsoft recomenda Standard Load Balancer.
As VMs autónomas, os conjuntos de disponibilidade e os conjuntos de dimensionamento de máquinas virtuais só podem ser ligados a um SKU, nunca a ambos. Load Balancer e o SKU do endereço IP público devem corresponder quando você os usa com endereços IP públicos. Load Balancer e SKUs de IP público não são mutáveis.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Para obter mais informações, consulte [limites do balanceador de carga](https://aka.ms/lblimits). Relativamente aos detalhes do Balanceador de Carga Standard, veja a [descrição geral](load-balancer-standard-overview.md), os [preços](https://aka.ms/lbpricing) e o [SLA](https://aka.ms/lbsla).

## <a name="load-balancer-types"></a>Tipos de Load Balancer

### <a name = "publicloadbalancer"></a>Balanceador de Carga Público

Um Load Balancer público mapeia o endereço IP público e a porta do tráfego de entrada para o endereço IP privado e a porta da VM. Load Balancer mapeia o tráfego o contrário para o tráfego de resposta da VM. Você pode distribuir tipos específicos de tráfego entre várias VMs ou serviços aplicando regras de balanceamento de carga. Por exemplo, pode distribuir a carga do tráfego de pedidos da Web entre múltiplos servidores Web.

>[!NOTE]
>Você pode implementar apenas um Load Balancer público e um Load Balancer interno por conjunto de disponibilidade.

A figura a seguir mostra um ponto de extremidade com balanceamento de carga para o tráfego da Web que é compartilhado entre três VMs para a porta pública e TCP 80. Estas três VMs estão num conjunto com carga balanceada.

![Exemplo de balanceador de carga público](./media/load-balancer-overview/IC727496.png)

*Figura: equilibrando o tráfego da Web usando um Load Balancer público*

Os clientes da Internet enviam solicitações da página da Web para o endereço IP público de um aplicativo de rede na porta TCP 80. Azure Load Balancer distribui as solicitações entre as três VMs no conjunto de balanceamento de carga. Para obter mais informações sobre algoritmos de Load Balancer, consulte [conceitos de Load Balancer](load-balancer-overview.md#load-balancer-concepts).

O Azure Load Balancer distribui o tráfego de rede igualmente entre várias instâncias de VM por padrão. Também pode configurar a afinidade de sessão. Para obter mais informações, consulte [Configurar o modo de distribuição para Azure Load Balancer](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Balanceador de Carga Interno

Um Load Balancer interno direciona o tráfego apenas para os recursos que estão dentro de uma rede virtual ou que usam uma VPN para acessar a infraestrutura do Azure, em oposição a uma Load Balancer pública. A infraestrutura do Azure restringe o acesso aos endereços IP de front-end com balanceamento de carga de uma rede virtual. Os endereços IP front-end e as redes virtuais nunca são expostos diretamente a um ponto de extremidade da Internet. As aplicações de linha de negócio internas são executadas no Azure e acedidas de dentro do Azure ou a partir de recursos no local.

Os balanceadores de carga internos permitem os seguintes tipos de balanceador de carga:

* **Em uma rede virtual**: balanceamento de carga de VMs na rede virtual para um conjunto de VMs que estão na mesma rede virtual.
* **Para uma rede virtual entre locais**: balanceamento de carga de computadores locais para um conjunto de VMs que estão na mesma rede virtual.
* **Para aplicativos de várias camadas**: balanceamento de carga para aplicativos de várias camadas voltados para a Internet em que as camadas de back-end não são voltadas para a Internet. As camadas de back-end exigem o balanceamento de carga de tráfego da camada voltada para a Internet. Veja a próxima figura.
* **Para aplicações de linha de negócio**: balanceamento de carga para aplicações de linha de negócio que estão alojadas no Azure sem hardware ou software adicional de balanceador de carga. Esse cenário inclui servidores locais que estão no conjunto de computadores cujo tráfego tem balanceamento de carga.

![Exemplo de Balanceador de Carga Interno](./media/load-balancer-overview/IC744147.png)

*Figura: balanceamento de aplicativos de várias camadas usando Load Balancer públicos e internos*

## <a name="pricing"></a>Preços

O uso de Standard Load Balancer é cobrado.

* Número de regras de balanceamento de carga e de saída configuradas. As regras de NAT de entrada não contam no número total de regras.
* Quantidade de dados processados de entrada e saída independentes de regras.

Para Standard Load Balancer informações sobre preços, consulte [preços de Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

O Balanceador de Carga Básico é disponibilizado sem custos.

## <a name="sla"></a>SLA

Para obter informações sobre o SLA de Standard Load Balancer, consulte [SLA para Load Balancer](https://aka.ms/lbsla).

## <a name="limitations"></a>Limitações

* Load Balancer fornece balanceamento de carga e encaminhamento de porta para protocolos TCP ou UDP específicos. As regras de balanceamento de carga e as regras de NAT de entrada dão suporte a TCP e UDP, mas não a outros protocolos IP, incluindo ICMP.

  Load Balancer não termina, responde ou interage com a carga de um fluxo UDP ou TCP. Não é um proxy. A validação bem-sucedida da conectividade com um front-end deve ocorrer em banda com o mesmo protocolo usado em um balanceamento de carga ou em uma regra NAT de entrada. Pelo menos uma de suas máquinas virtuais deve gerar uma resposta para um cliente ver uma resposta de um front-end.

  Não receber uma resposta em banda do front-end de Load Balancer indica que nenhuma máquina virtual pode responder. Nada pode interagir com um front-end Load Balancer sem uma máquina virtual capaz de responder. Esse princípio também se aplica a conexões de saída em que a porta disfarçar SNAT só tem suporte para TCP e UDP. Todos os outros protocolos IP, incluindo ICMP, falham. Atribua um endereço IP público em nível de instância para atenuar esse problema. Para obter mais informações, consulte [Understanding SNAT and Pat](load-balancer-outbound-connections.md#snat).

* Os balanceadores de carga internos não convertem conexões originadas de saída para o front-end de um Load Balancer interno porque ambos estão no espaço de endereço IP privado. Os balanceadores de carga públicos fornecem [conexões de saída](load-balancer-outbound-connections.md) de endereços IP privados dentro da rede virtual para endereços IP públicos. Para balanceadores de carga internos, essa abordagem evita o esgotamento potencial da porta SNAT dentro de um espaço de endereço IP interno exclusivo, em que a conversão não é necessária.

  Um efeito colateral é que, se um fluxo de saída de uma VM no pool de back-end tentar um fluxo para front-end do Load Balancer interno em seu pool _e_ for mapeado de volta para ele mesmo, as duas pernas do fluxo não corresponderão. Como eles não correspondem, o fluxo falha. O fluxo terá sucesso se o fluxo não for mapeado de volta para a mesma VM no pool de back-end que criou o fluxo para o front-end.

  Quando o fluxo mapeia de volta para ele mesmo, o fluxo de saída parece originar da VM para o front-end e o fluxo de entrada correspondente parece originar-se da VM para si mesmo. Do ponto de vista do sistema operacional convidado, as partes de entrada e saída do mesmo fluxo não correspondem dentro da máquina virtual. A pilha TCP não reconhecerá essas metades do mesmo fluxo que fazem parte do mesmo fluxo. A origem e o destino não correspondem. Quando o fluxo é mapeado para qualquer outra VM no pool de back-end, as metades do fluxo são correspondentes e a VM pode responder ao fluxo.

  O sintoma para esse cenário é o tempo limite de conexão intermitente quando o fluxo retorna ao mesmo back-end que originou o fluxo. Soluções alternativas comuns incluem a inserção de uma camada proxy por trás do Load Balancer interno e o uso de regras de estilo de retorno de servidor direto (DSR). Para obter mais informações, consulte [vários front-ends para Azure Load Balancer](load-balancer-multivip-overview.md).

  Você pode combinar um Load Balancer interno com qualquer proxy de terceiros ou usar o [Gateway de aplicativo](../application-gateway/application-gateway-introduction.md) interno para cenários de proxy com http/https. Embora você possa usar um Load Balancer público para atenuar esse problema, o cenário resultante é propenso a [esgotamento de SNAT](load-balancer-outbound-connections.md#snat). Evite essa segunda abordagem, a menos que seja cuidadosamente gerenciada.

* Em geral, os fragmentos de IP de encaminhamento não têm suporte em regras de balanceamento de carga. A fragmentação de IP de pacotes UDP e TCP não tem suporte em regras de balanceamento de carga. As portas de alta disponibilidade regras de balanceamento de carga podem ser usadas para encaminhar os fragmentos IP existentes. Para obter mais informações, consulte [visão geral de portas de alta disponibilidade](load-balancer-ha-ports-overview.md).

## <a name="next-steps"></a>Passos seguintes

Consulte [criar um Standard Load Balancer público](quickstart-load-balancer-standard-public-portal.md) para começar a usar um Load Balancer: criar um, criar VMs com uma extensão personalizada do IIS instalada e balancear a carga do aplicativo Web entre as VMs.
