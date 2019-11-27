---
title: O que é o Balanceador de Carga do Azure?
titleSuffix: Azure Load Balancer
description: Descrição geral das funcionalidades, da arquitetura e da implementação do Balanceador de Carga do Azure. Saiba como o Balanceador de Carga funciona e como tirar partido do mesmo na cloud.
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
ms.date: 11/21/2019
ms.author: allensu
ms.openlocfilehash: 335549f4ccae01fa36921e0e4668fa15e8b33835
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423906"
---
# <a name="what-is-azure-load-balancer"></a>O que é o Balanceador de Carga do Azure?

Com o Balanceador de Carga do Azure, pode dimensionar as aplicações e criar elevada disponibilidade para os seus serviços. O Balanceador de Carga suporta cenários de entrada e de saída, oferece baixa latência e alto débito e dimensiona-se até milhões de fluxos para todas as aplicações TCP e UDP.

O Load Balancer distribui novos fluxos de entrada que chegam às instâncias de front-end do pool de back-ends do Load Balancer, de acordo com as regras especificadas e as investigações de integridade.

Um Load Balancer público pode fornecer conexões de saída para VMs (máquinas virtuais) dentro de sua rede virtual, convertendo seus endereços IP privados em endereços IP públicos.

Azure Load Balancer está disponível em dois tipos de preço ou *SKUs*: básico e Standard. Há diferenças em termos de dimensionamento, funcionalidades e preços. Qualquer cenário que seja possível com o Load Balancer básico também pode ser criado com Standard Load Balancer, embora as abordagens sejam ligeiramente diferentes. Ao saber mais sobre Load Balancer, familiarize-se com os conceitos básicos e as diferenças específicas de SKU.

## <a name="why-use-load-balancer"></a>Porquê utilizar o Balanceador de Carga

Pode utilizar o Balanceador de Carga do Azure para:

* Balancear a carga do tráfego de entrada da Internet para suas VMs. Essa configuração é conhecida como um [Load Balancer público](#publicloadbalancer).
* Balancear a carga do tráfego entre VMs dentro de uma rede virtual. Também pode alcançar um front-end do Balanceador de Carga a partir de uma rede no local num cenário híbrido. Ambos os cenários usam uma configuração que é conhecida como um [Load balancer interno](#internalloadbalancer).
* Encaminhar o tráfego de uma porta para uma porta específica em determinadas VMs com regras de tradução de endereços de rede (NAT).
* Utilize um balanceador de carga público para fornecer [conectividade de saída](load-balancer-outbound-connections.md) às VMs dentro da sua rede virtual.

>[!NOTE]
> O Azure oferece um conjunto de soluções de balanceamento de carga totalmente geridas para os seus cenários. Se você estiver procurando a terminação do protocolo TLS ("descarregamento SSL") ou a solicitação por HTTP/HTTPS, o processamento da camada de aplicativo, consulte [o que é aplicativo Azure gateway?](../application-gateway/overview.md) Se você estiver procurando balanceamento de carga de DNS global, consulte [o que é o Gerenciador de tráfego?](../traffic-manager/traffic-manager-overview.md) Seus cenários de ponta a ponta podem se beneficiar com a combinação dessas soluções.
>
> Para obter uma comparação das opções de balanceamento de carga do Azure, consulte [visão geral das opções de balanceamento de carga no Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="what-are-load-balancer-resources"></a>O que são os recursos do Balanceador de Carga?

Recursos de Load Balancer são objetos que especificam como o Azure deve programar sua infraestrutura multilocatário para obter o cenário que você deseja criar. Não há nenhuma relação direta entre Load Balancer recursos e a infraestrutura real. A criação de um balanceador de carga não cria uma instância e a capacidade está sempre disponível.

Um recurso de Load Balancer pode ser um Load Balancer público ou um Load Balancer interno. As funções do recurso Load Balancer são definidas por um front-end, uma regra, uma investigação de integridade e uma definição de pool de back-end. Coloque as VMs no pool de back-end especificando o pool de back-end da VM.

## <a name="fundamental-load-balancer-features"></a>Funcionalidades básicas do Balanceador de Carga

O Balanceador de Carga proporciona as seguintes capacidades básicas para as aplicações TCP e UDP:

* **Balanceamento de carga**

  Com o Azure Load Balancer, você pode criar uma regra de balanceamento de carga para distribuir o tráfego que chega às instâncias de front-end do pool de back-ends. Load Balancer usa um algoritmo de hash para distribuição de fluxos de entrada e reescreve os cabeçalhos de fluxos para instâncias de pool de back-end. Um servidor está disponível para receber novos fluxos quando uma investigação de integridade indica um ponto de extremidade de back-end íntegro.

  Por padrão, Load Balancer usa um hash de 5 tuplas. O hash inclui o endereço IP de origem, a porta de origem, o endereço IP de destino, a porta de destino e o número do protocolo IP para mapear os fluxos para os servidores disponíveis. Você pode criar afinidade para um endereço IP de origem usando um hash de 2 ou 3 tuplas para uma determinada regra. Todos os pacotes do mesmo fluxo de pacotes são entregues na mesma instância, atrás do front-end com carga balanceada. Quando o cliente inicia um novo fluxo do mesmo IP de origem, a porta de origem é alterada. Como resultado, o hash de 5 tuplas pode fazer com que o tráfego vá para um ponto de extremidade de back-end diferente.

  Para obter mais informações, consulte [Configurar o modo de distribuição para Azure Load Balancer](load-balancer-distribution-mode.md). A imagem seguinte apresenta a distribuição baseada em hashes:

  ![Distribuição baseada em hashes](./media/load-balancer-overview/load-balancer-distribution.png)

  *Figura: distribuição baseada em hashes*

* **Encaminhamento de portas**

  Com Load Balancer, você pode criar uma regra NAT de entrada. Essa regra NAT encaminha o tráfego de uma porta específica de um endereço IP de front-end específico para uma porta específica de uma instância específica de back-end dentro da rede virtual. Esse encaminhamento é feito pela mesma distribuição baseada em hash que o balanceamento de carga. Cenários comuns para esse recurso são protocolo RDP (RDP) ou Secure Shell (SSH) sessões para instâncias de VM individuais dentro de uma rede virtual do Azure.
  
  Você pode mapear vários pontos de extremidade internos para portas no mesmo endereço IP de front-end. Você pode usar os endereços IP de front-end para administrar remotamente suas VMs sem uma caixa de salto adicional.

* **Independência de aplicativo e transparência**

  Load Balancer não interage diretamente com TCP ou UDP ou a camada de aplicativo. Qualquer cenário de aplicativo TCP ou UDP pode ter suporte. Load Balancer não encerra ou origina fluxos, interage com a carga do fluxo ou fornece qualquer função de gateway de camada de aplicativo. Os Handshakes de protocolo sempre ocorrem diretamente entre o cliente e a instância do pool de back-end. Uma resposta a um fluxo de entrada é sempre uma resposta de uma máquina virtual. Quando o fluxo chega à máquina virtual, o endereço IP de origem original também é preservado.

  * Cada ponto final só recebe uma resposta de uma VM. Por exemplo, um handshake TCP sempre ocorre entre o cliente e a VM de back-end selecionada. Uma resposta a uma solicitação para um front-end é uma resposta gerada por uma VM de back-end. Quando você valida com êxito a conectividade para um front-end, você está validando a conectividade de ponta a ponta para pelo menos uma máquina virtual de back-end.
  * As cargas de aplicativo são transparentes para Load Balancer. Qualquer aplicativo UDP ou TCP pode ter suporte. Para cargas de trabalho que exigem processamento de solicitação por HTTP ou manipulação de cargas de camada de aplicativo, como análise de URLs HTTP, você deve usar um balanceador de carga de camada 7, como o [Gateway de aplicativo](https://azure.microsoft.com/services/application-gateway).
  * Como Load Balancer não interage com a carga de TCP e fornece o descarregamento de TLS, você pode criar cenários criptografados de ponta a ponta. O uso de Load Balancer ganha grande escala horizontal para aplicativos TLS encerrando a conexão TLS na própria VM. Por exemplo, a capacidade de criação de chaves de sessão TLS é limitada apenas pelo tipo e pelo número de VMs que você adiciona ao pool de back-ends. Se você precisar de descarregamento de SSL, tratamento de camada de aplicativo ou desejar delegar o gerenciamento de certificados para o Azure, use o [Gateway de aplicativo](https://azure.microsoft.com/services/application-gateway) do balanceador de carga de camada 7 do Azure em vez disso.

* **Reconfiguração automática**

  Quando aumenta ou reduz instâncias verticalmente, o Balanceador de Carga reconfigura-se a si próprio instantaneamente. Adicionar ou remover VMs do pool de back-end reconfigura o Load Balancer sem operações adicionais no recurso Load Balancer.

* **Sondas de estado de funcionamento**

  Para determinar a integridade das instâncias no pool de back-end, Load Balancer usa as investigações de integridade que você definir. Quando uma sonda não consegue responder, o Balanceador de Carga deixa de enviar ligações novas para as instâncias em mau estado de funcionamento. Uma falha de investigação não afeta as conexões existentes. A conexão continua até que o aplicativo encerre o fluxo, um tempo limite de ociosidade ocorra ou a VM seja desligada.

  Load Balancer fornece tipos diferentes de investigação de integridade para pontos de extremidade TCP, HTTP e HTTPS. Para obter mais informações, consulte [tipos de investigação](load-balancer-custom-probe-overview.md#types).

  Ao usar os serviços de nuvem clássicos, é permitido um tipo adicional: [agente convidado](load-balancer-custom-probe-overview.md#guestagent). Um agente convidado deve ser considerado uma investigação de integridade do último recurso. A Microsoft não os recomenda se outras opções estiverem disponíveis.

* **Ligações de saída (SNAT)**

  Todos os fluxos de saída de endereços IP privados dentro de sua rede virtual para endereços IP públicos na Internet podem ser convertidos em um endereço IP de front-end da Load Balancer. Quando um front-end público está vinculado a uma VM de back-end por meio de uma regra de balanceamento de carga, o Azure converte as conexões de saída para o endereço IP público de front-end. Essa configuração tem as seguintes vantagens:

  * Atualização fácil e recuperação de desastre de serviços, pois o front-end pode ser mapeado dinamicamente para outra instância do serviço.
  * Gerenciamento de ACL (lista de controle de acesso) mais fácil. As ACLs expressas como IPs de front-end não mudam à medida que os serviços são expandidos ou reduzidos ou reimplantados. A tradução de conexões de saída para um número menor de endereços IP do que computadores reduz a carga da implementação de listas de destinatários seguros.

  Para obter mais informações, consulte [conexões de saída no Azure](load-balancer-outbound-connections.md).

O Standard Load Balancer tem recursos adicionais específicos do SKU além desses conceitos básicos, conforme descrito abaixo.

## <a name="skus"></a> Comparação de SKUs do Balanceador de Carga

O Load Balancer dá suporte a SKUs Basic e Standard. Essas SKUs diferem em escala de cenário, recursos e preços. Qualquer cenário possível com Load Balancer básica pode ser criado com Standard Load Balancer. As APIs para ambos os SKUs são semelhantes e são invocadas por meio da especificação de uma SKU. A API para oferecer suporte a SKUs para Load Balancer e o IP público está disponível a partir da API de `2017-08-01`. Ambos os SKUs compartilham a mesma API e estrutura geral.

A configuração completa do cenário pode variar ligeiramente dependendo do SKU. A documentação do Balanceador de Carga indica se um artigo se aplica apenas a um SKU específico. Para comparar e compreender as diferenças, veja a tabela seguinte. Para obter mais informações, consulte [visão geral do Azure Standard Load Balancer](load-balancer-standard-overview.md).

>[!NOTE]
> Os designs novos devem adotar o Balanceador de Carga Standard.

As VMs autónomas, os conjuntos de disponibilidade e os conjuntos de dimensionamento de máquinas virtuais só podem ser ligados a um SKU, nunca a ambos. Load Balancer e o SKU do endereço IP público devem corresponder quando você os usa com endereços IP públicos. Load Balancer e SKUs de IP público não são mutáveis.

A prática recomendada é especificar explicitamente as SKUs. Nesta fase, estamos a manter as alterações obrigatórias no mínimo. Se um SKU não for especificado, o padrão será a versão de API `2017-08-01` do SKU básico.

>[!IMPORTANT]
>Standard Load Balancer é um novo produto de Load Balancer. É basicamente um superconjunto de Load Balancer básicas, mas há diferenças importantes entre os dois produtos. Qualquer cenário completo possível no Balanceador de Carga Básico também pode ser criado com o Balanceador de Carga Standard. Se você já estiver acostumado a Load Balancer básica, compare-o com Standard Load Balancer para entender as alterações mais recentes em seu comportamento.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Para obter mais informações, consulte [limites do balanceador de carga](https://aka.ms/lblimits). Relativamente aos detalhes do Balanceador de Carga Standard, veja a [descrição geral](load-balancer-standard-overview.md), os [preços](https://aka.ms/lbpricing) e o [SLA](https://aka.ms/lbsla).

## <a name="concepts"></a>Conceitos

### <a name = "publicloadbalancer"></a>Balanceador de Carga Público

Um Load Balancer público mapeia o endereço IP público e a porta do tráfego de entrada para o endereço IP privado e a porta da VM. Load Balancer mapeia o tráfego o contrário para o tráfego de resposta da VM. Você pode distribuir tipos específicos de tráfego entre várias VMs ou serviços aplicando regras de balanceamento de carga. Por exemplo, pode distribuir a carga do tráfego de pedidos da Web entre múltiplos servidores Web.

>[!NOTE]
>Você pode implementar apenas um Load Balancer público e um Load Balancer interno por conjunto de disponibilidade.

A figura a seguir mostra um ponto de extremidade com balanceamento de carga para o tráfego da Web que é compartilhado entre três VMs para a porta pública e TCP 80. Estas três VMs estão num conjunto com carga balanceada.

![Exemplo de balanceador de carga público](./media/load-balancer-overview/IC727496.png)

*Figura: equilibrando o tráfego da Web usando um Load Balancer público*

Os clientes da Internet enviam solicitações da página da Web para o endereço IP público de um aplicativo de rede na porta TCP 80. Azure Load Balancer distribui as solicitações entre as três VMs no conjunto de balanceamento de carga. Para obter mais informações sobre algoritmos de Load Balancer, consulte [recursos fundamentais do Load Balancer](load-balancer-overview.md##fundamental-load-balancer-features).

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

Consulte [criar um Load Balancer básico](quickstart-create-basic-load-balancer-portal.md) para começar a usar um Load Balancer: criar um, criar VMs com uma extensão personalizada do IIS instalada e balancear a carga do aplicativo Web entre as VMs.
