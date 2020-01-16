---
title: O que é o Azure Standard Load Balancer?
titleSuffix: Azure Load Balancer
description: Com este roteiro de aprendizagem, comece com uma visão geral dos recursos do Azure Standard Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: allensu
ms.openlocfilehash: b4db0746f64bfde04f09b589ac6beb6dc74d13f2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978670"
---
# <a name="azure-standard-load-balancer-overview"></a>Visão geral do Azure Standard Load Balancer

Azure Load Balancer permite que você dimensione seus aplicativos e crie alta disponibilidade para seus serviços. Load Balancer pode ser usado para cenários de entrada, bem como de saída e fornece baixa latência, alta taxa de transferência e escala para milhões de fluxos para todos os aplicativos TCP e UDP. 

Este artigo se concentra em Standard Load Balancer.  Para obter uma visão geral mais geral do Azure Load Balancer, examine [Load Balancer visão geral](load-balancer-overview.md) também.

## <a name="what-is-standard-load-balancer"></a>O que é o Balanceador de Carga Standard?

Standard Load Balancer é um novo produto de Load Balancer para todos os aplicativos TCP e UDP com um conjunto de recursos expandido e mais granular sobre Load Balancer básico.  Embora haja muitas semelhanças, é importante se familiarizar com as diferenças, conforme descrito neste artigo.

Você pode usar Standard Load Balancer como um Load Balancer público ou interno. E uma máquina virtual pode ser conectada a um recurso de Load Balancer público e um interno.

As funções do recurso de Load Balancer são sempre expressas como front-end, uma regra, uma investigação de integridade e uma definição de pool de back-end.  Um recurso pode conter várias regras. Você pode posicionar máquinas virtuais no pool de back-end especificando o pool de back-end do recurso NIC da máquina virtual.  Esse parâmetro é passado pelo perfil de rede e expandido ao usar conjuntos de dimensionamento de máquinas virtuais.

Um aspecto fundamental é o escopo da rede virtual para o recurso.  Embora o Load Balancer básico exista no escopo de um conjunto de disponibilidade, um Standard Load Balancer é totalmente integrado ao escopo de uma rede virtual e todos os conceitos de rede virtual se aplicam.

Load Balancer recursos são objetos nos quais você pode expressar como o Azure deve programar sua infraestrutura multilocatário para obter o cenário que você deseja criar.  Não há nenhuma relação direta entre Load Balancer recursos e a infraestrutura real; a criação de uma Load Balancer não cria uma instância, a capacidade está sempre disponível e não há atrasos de inicialização ou de dimensionamento a serem considerados. 

### <a name="backend"></a>Pool de back-end

Standard Load Balancer pools de back-end expandem para qualquer recurso de máquina virtual em uma rede virtual.  Ele pode conter até 1000 instâncias de back-end.  Uma instância de back-end é uma configuração de IP, que é uma propriedade de um recurso NIC.

O pool de back-end pode conter máquinas virtuais autônomas, conjuntos de disponibilidade ou conjuntos de dimensionamento de máquinas virtuais.  Você também pode misturar recursos no pool de back-end. Você pode combinar até 150 recursos no pool de back-end por Load Balancer recurso.

Ao considerar como projetar seu pool de back-end, você pode criar o número mínimo de recursos de pool de back-end individuais para otimizar ainda mais a duração das operações de gerenciamento.  Não há nenhuma diferença no desempenho ou na escala do plano de dados.

### <a name="probes"></a>Investigações de integridade
  
Standard Load Balancer adiciona suporte para [investigações de integridade https](load-balancer-custom-probe-overview.md#httpprobe) (investigação http com wrapper TLS) para monitorar com precisão seus aplicativos HTTPS.  

Além disso, quando todo o pool de back-end é [investigado](load-balancer-custom-probe-overview.md#probedown), Standard Load Balancer permite que todas as conexões TCP estabelecidas continuem. (Basic Load Balancer encerrará todas as conexões TCP para todas as instâncias).

Examine [Load Balancer investigações de integridade](load-balancer-custom-probe-overview.md) para obter detalhes.

### <a name="az"></a>Zonas de Disponibilidade

>[!IMPORTANT]
>Examine [zonas de disponibilidade](../availability-zones/az-overview.md) para obter tópicos relacionados, incluindo qualquer informação específica da região.

O Standard Load Balancer dá suporte a recursos adicionais em regiões em que Zonas de Disponibilidade estão disponíveis.  Esses recursos são incrementais para todos os Standard Load Balancer fornece.  Zonas de Disponibilidade configurações estão disponíveis para Standard Load Balancer públicas e internos.

Os front-ends não zonais tornam-se redundantes por zona por padrão quando implantados em uma região com Zonas de Disponibilidade.   Um front-end com redundância de zona sobreviver à falha de zona e é servido pela infraestrutura dedicada em todas as zonas simultaneamente. 

Além disso, você pode garantir um front-end para uma zona específica. Um front-end zonal compartilha destino com a respectiva zona e é servido apenas pela infraestrutura dedicada em uma única zona.

O balanceamento de carga entre zonas está disponível para o pool de back-end e qualquer recurso de máquina virtual em uma vnet pode fazer parte de um pool de back-end.

Examine a [discussão detalhada de recursos relacionados ao zonas de disponibilidade](load-balancer-standard-availability-zones.md).

### <a name="diagnostics"></a>Diagnostics

Standard Load Balancer fornece métricas multidimensionais por meio de Azure Monitor.  Essas métricas podem ser filtradas, agrupadas e divididas para uma determinada dimensão.  Eles fornecem informações atuais e históricas sobre o desempenho e a integridade do seu serviço.  Também há suporte para Resource Health.  Veja a seguir uma breve visão geral do diagnóstico com suporte:

| Métrica | Descrição |
| --- | --- |
| Disponibilidade de VIP | Standard Load Balancer exercita continuamente o caminho de dados de dentro de uma região para a Load Balancer front-end até a pilha SDN que dá suporte à sua VM. Desde que as instâncias íntegras permaneçam, a medida segue o mesmo caminho que o tráfego com balanceamento de carga do aplicativo. O caminho de dados usado por seus clientes também é validado. A medida é invisível para seu aplicativo e não interfere em outras operações.|
| Disponibilidade de DIP | Standard Load Balancer usa um serviço de investigação de integridade distribuído que monitora a integridade do ponto de extremidade do aplicativo de acordo com suas definições de configuração. Essa métrica fornece uma exibição de agregação ou por ponto de extremidade filtrada de cada ponto de extremidade de instância individual no pool de Load Balancer.  Você pode ver como Load Balancer exibe a integridade do seu aplicativo conforme indicado pela configuração da investigação de integridade.
| Pacotes SYN | Standard Load Balancer não encerra as conexões TCP ou interage com os fluxos de pacotes TCP ou UDP. Os fluxos e seus Handshakes sempre estão entre a origem e a instância de VM. Para solucionar melhor os cenários de protocolo TCP, você pode fazer uso de contadores de pacotes SYN para entender quantas tentativas de conexão TCP são feitas. A métrica relata o número de pacotes TCP SYN que foram recebidos.|
| Conexões SNAT | Standard Load Balancer relata o número de fluxos de saída mascarados para o front-end de endereço IP público. As portas SNAT são um recurso esse esgotável. Essa métrica pode dar uma indicação de quanto seu aplicativo está contando com SNAT para fluxos originados de saída.  Os contadores de fluxos SNAT de saída bem-sucedidos e com falha são relatados e podem ser usados para solucionar problemas e entender a integridade dos fluxos de saída.|
| Contadores de bytes | Standard Load Balancer relata os dados processados por front-end.|
| Contadores de pacotes | Standard Load Balancer relata os pacotes processados por front-end.|

Examine a [discussão detalhada sobre o diagnóstico de Standard Load Balancer](load-balancer-standard-diagnostics.md).

### <a name="haports"></a>Portas de alta disponibilidade

Standard Load Balancer dá suporte a um novo tipo de regra.  

Você pode configurar regras de balanceamento de carga para tornar seu aplicativo em escala e ser altamente confiável. Quando você usa uma regra de balanceamento de carga de portas de alta disponibilidade, Standard Load Balancer fornecerá o balanceamento de carga por fluxo em cada porta efêmera de um endereço IP de front-end interno de Standard Load Balancer.  O recurso é útil para outros cenários em que é impraticável ou indesejável especificar portas individuais.

Uma regra de balanceamento de carga de portas de alta disponibilidade permite criar cenários ativo-passivo ou ativo-ativo n + 1 para soluções de virtualização de rede e qualquer aplicativo, o que exige grandes intervalos de portas de entrada.  Uma investigação de integridade pode ser usada para determinar quais back-ends devem receber novos fluxos.  Você pode usar um grupo de segurança de rede para emular um cenário de intervalo de portas.

>[!IMPORTANT]
> Se você estiver planejando usar uma solução de virtualização de rede, consulte seu fornecedor para obter orientação sobre se o produto foi testado com portas de alta disponibilidade e siga suas diretrizes específicas para a implementação. 

Examine a [discussão detalhada de portas de alta disponibilidade](load-balancer-ha-ports-overview.md).

### <a name="securebydefault"></a>Seguro por padrão

Standard Load Balancer é totalmente integrado à rede virtual.  A rede virtual é uma rede privada e fechada.  Como os balanceadores de carga padrão e os endereços IP públicos padrão são projetados para permitir que essa rede virtual seja acessada de fora da rede virtual, esses recursos agora são padrão fechados, a menos que você os abra. Isso significa que os NSGs (grupos de segurança de rede) agora são usados para permitir explicitamente e o tráfego permitido pela lista de permissões.  Você pode criar seu data center virtual inteiro e decidir por NSG o que e quando ele deve estar disponível.  Se você não tiver um NSG em uma sub-rede ou NIC de seu recurso de máquina virtual, o tráfego não terá permissão para acessar esse recurso.

Para saber mais sobre o NSGs e como aplicá-los ao seu cenário, consulte [grupos de segurança de rede](../virtual-network/security-overview.md).

### <a name="outbound"></a>Conexões de saída

O Load Balancer dá suporte a cenários de entrada e saída.  Standard Load Balancer é significativamente diferente de Load Balancer básica em relação às conexões de saída.

O SNAT (conversão de endereços de rede de origem) é usado para mapear endereços IP privados e internos em sua rede virtual para endereços IP públicos em Load Balancer front-ends.

Standard Load Balancer introduz um novo algoritmo para um [algoritmo de SNAT mais robusto, escalonável e previsível](load-balancer-outbound-connections.md#snat) e permite novas capacidades, remove a ambiguidade e força configurações explícitas em vez de efeitos colaterais. Essas alterações são necessárias para permitir que novos recursos surjam. 

Estes são os principais princípios a serem lembrados ao trabalhar com Standard Load Balancer:

- a conclusão de uma regra orienta o recurso de Load Balancer.  toda a programação do Azure deriva de sua configuração.
- Quando vários front-ends estão disponíveis, todos os front-ends são usados e cada front-end multiplica o número de portas SNAT disponíveis
- Você pode escolher e controlar se não desejar que um front-end específico seja usado para conexões de saída.
- os cenários de saída são explícitos e a conectividade de saída não existe até que tenha sido especificado.
- regras de balanceamento de carga inferem como o SNAT é programado. As regras de balanceamento de carga são específicas de protocolo. SNAT é específico de protocolo e a configuração deve refletir isso em vez de criar um efeito colateral.

#### <a name="multiple-frontends"></a>Vários front-ends
Se você quiser mais portas SNAT porque está esperando ou já está tendo uma alta demanda de conexões de saída, também poderá adicionar o inventário de porta SNAT incremental Configurando front-ends adicionais, regras e pools de back-end para a mesma máquina virtual os.

#### <a name="control-which-frontend-is-used-for-outbound"></a>Controlar qual front-end é usado para saída
Se você quiser restringir as conexões de saída para serem originadas apenas de um endereço IP de front-end específico, você pode opcionalmente desabilitar o SNAT de saída na regra que expressa o mapeamento de saída.

#### <a name="control-outbound-connectivity"></a>Controlar a conectividade de saída
Standard Load Balancer existe no contexto da rede virtual.  Uma rede virtual é uma rede isolada e privada.  A menos que haja uma associação com um endereço IP público, a conectividade pública não é permitida.  Você pode acessar os [pontos de extremidade do serviço VNet](../virtual-network/virtual-network-service-endpoints-overview.md) porque eles estão dentro de e local em sua rede virtual.  Se você quiser estabelecer a conectividade de saída para um destino fora da sua rede virtual, terá duas opções:
- atribuir um endereço IP público de SKU padrão como um endereço IP público em nível de instância para o recurso de máquina virtual ou
- Coloque o recurso de máquina virtual no pool de back-end de um Standard Load Balancer público.

Ambos permitirão a conectividade de saída da rede virtual para fora da rede virtual. 

Se você tiver _apenas_ um Standard Load balancer interno associado ao pool de back-end no qual o recurso de máquina virtual está localizado, sua máquina virtual só poderá acessar os recursos de rede virtual e os [pontos de extremidade de serviço de VNet](../virtual-network/virtual-network-service-endpoints-overview.md).  Você pode seguir as etapas descritas no parágrafo anterior para criar a conectividade de saída.

A conectividade de saída de um recurso de máquina virtual não associado a SKUs padrão permanece como antes.

Examine a [discussão detalhada de conexões de saída](load-balancer-outbound-connections.md).

### <a name="multife"></a>Vários front-ends
Load Balancer dá suporte a várias regras com vários front-ends.  Standard Load Balancer expande isso para cenários de saída.  Os cenários de saída são essencialmente o inverso de uma regra de balanceamento de carga de entrada.  A regra de balanceamento de carga de entrada também cria um associado para conexões de saída. Standard Load Balancer usa todos os front-ends associados a um recurso de máquina virtual por meio de uma regra de balanceamento de carga.  Além disso, um parâmetro na regra de balanceamento de carga e permite suprimir uma regra de balanceamento de carga para fins de conectividade de saída, o que permite a seleção de front-ends específicos, incluindo nenhum.

Para comparação, o Load Balancer básico seleciona um único front-end aleatoriamente e não há capacidade de controlar qual deles foi selecionado.

Examine a [discussão detalhada de conexões de saída](load-balancer-outbound-connections.md).

### <a name="operations"></a>Operações de gerenciamento

Standard Load Balancer recursos existem em uma plataforma de infraestrutura totalmente nova.  Isso permite que operações de gerenciamento mais rápidas para SKUs padrão e tempos de conclusão normalmente sejam menos de 30 segundos por recurso de SKU Standard.  À medida que os pools de back-end aumentam, a duração necessária para as alterações do pool de back-end também aumenta

Você pode modificar Standard Load Balancer recursos e mover um endereço IP público padrão de uma máquina virtual para outra muito mais rápida.

## <a name="migration-between-skus"></a>Migração entre SKUs

Os SKUs não são mutáveis. Siga as etapas nesta seção para mover de um SKU de recurso para outro.

>[!IMPORTANT]
>Revise este documento em sua totalidade para entender as diferenças entre os SKUs e ter examinado cuidadosamente seu cenário.  Talvez seja necessário fazer alterações adicionais para alinhar seu cenário.

### <a name="migrate-from-basic-to-standard-sku"></a>Migrar do SKU básico para o Standard

1. Crie um novo recurso padrão (Load Balancer e IPs públicos, conforme necessário). Recrie suas regras e definições de investigação.  Se você estava usando uma investigação TCP para 443/TCP anteriormente, considere alterar esse protocolo de investigação para uma investigação HTTPS e adicionar um caminho.

2. Criar um novo ou Atualize o NSG existente no NIC ou uma sub-rede para o tráfego com balanceamento de carga de lista aprovada, pesquisa, bem como qualquer outro tráfego que pretende permitir.

3. Remova os recursos básicos de SKU (Load Balancer e IPs públicos, conforme aplicável) de todas as instâncias de VM. Certifique-se também de remover todas as instâncias de VM de um conjunto de disponibilidade.

4. Anexe todas as instâncias de VM aos novos recursos de SKU padrão.

>[!IMPORTANT]
>
>Os SKUs correspondentes devem ser usados para Load Balancer e recursos IP públicos. Você não pode ter uma mistura de recursos de SKU básicos e recursos de SKU padrão. Não é possível anexar máquinas virtuais autónomas, máquinas virtuais num recurso de conjunto de disponibilidade ou uma máquina virtual dos recursos do conjunto de dimensionamento para ambos os SKUs em simultâneo.

## <a name="region-availability"></a>Disponibilidade regional

O Standard Load Balancer está disponível atualmente em todas as regiões do Azure.

## <a name="sla"></a>SLA 

Os balanceadores de carga padrão estão disponíveis com um SLA de 99,99%.  Examine o [SLA de Standard Load Balancer](https://aka.ms/lbsla) para obter detalhes. 

## <a name="pricing"></a>Preços 

O uso de Standard Load Balancer é cobrado. 

- Número de regras de balanceamento de carga e de saída configuradas (as regras de NAT de entrada não são contadas em relação ao número total de regras) 
- Quantidade de dados processados de entrada e saída, independentemente da regra.

Para obter as informações de preços do Balanceador de Carga Standard, aceda à página [Preços de Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="limitations"></a>Limitações

- Os SKUs não são mutáveis. Você não pode alterar a SKU de um recurso existente.
- Um recurso de máquina virtual autônoma, recurso de conjunto de disponibilidade ou recurso de conjunto de dimensionamento de máquinas virtuais pode referenciar um SKU, nunca ambos.
- Uma regra de Load Balancer não pode abranger duas redes virtuais.  Os front-ends e suas instâncias de back-end relacionadas devem estar localizados na mesma rede virtual.  
- [As operações de movimentação de assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md) não têm suporte para recursos SKU lb e Pip padrão.
- As funções de Web Worker sem uma VNet e outros serviços de plataforma da Microsoft podem ser acessíveis de instâncias por trás de apenas um Standard Load Balancer interno devido a um efeito colateral de como os serviços de VNet e outros serviços de plataforma funcionam. Você não deve confiar nele como o próprio serviço ou a plataforma subjacente pode ser alterada sem aviso prévio. Você deve sempre supor que precisará criar a [conectividade de saída](load-balancer-outbound-connections.md) explicitamente, se desejado, ao usar apenas um Standard Load balancer interno.
- O Balanceador de Carga é um produto TCP ou UDP para balanceamento de carga e encaminhamento de portas para estes dois protocolos IP específicos.  As regras de balanceamento de carga e as regras NAT de entrada são suportadas para TCP e UDP, mas não para os outros protocolos IP, incluindo o ICMP. O Balanceador de Carga não termina, não responde nem interage com o payload dos fluxos UDP ou TCP. Não é um proxy. A validação bem-sucedida da conectividade com um front-end deve ocorrer em banda com o mesmo protocolo usado em um balanceamento de carga ou em uma regra NAT de entrada (TCP ou UDP) _e_ pelo menos uma de suas máquinas virtuais deve gerar uma resposta para que um cliente Veja uma resposta de um front-end.  Não receber uma resposta em banda do front-end Load Balancer indica que nenhuma máquina virtual foi capaz de responder.  Não é possível interagir com um front-end Load Balancer sem uma máquina virtual capaz de responder.  Isto também se aplica às ligações de saída, em que o [SNAT de máscara de rede](load-balancer-outbound-connections.md#snat) só é suportado para TCP e UDP; qualquer outro protocolo IP, incluindo ICMP, falhará.  Para mitigar o problema, atribua um endereço IP público ao nível da instância.
- Ao contrário dos balanceadores de carga públicos que fornecem [conexões de saída](load-balancer-outbound-connections.md) ao fazer a transição de endereços IP privados dentro da rede virtual para endereços IP públicos, os balanceadores de carga internos não convertem conexões originadas de saída para o front-end de um Load balancer interno, pois ambos estão no espaço de endereço IP privado.  Isso evita o potencial de esgotamento de SNAT dentro do espaço de endereço IP interno exclusivo em que a conversão não é necessária.  O efeito colateral é que, se um fluxo de saída de uma VM no pool de back-end tentar um fluxo para o front-end da Load Balancer interna em que o pool reside _e_ for mapeado de volta para si mesmo, ambas as pernas do fluxo não corresponderão e o fluxo falhará.  Se o fluxo não foi mapeado de volta para a mesma VM no pool de back-end que criou o fluxo para o front-end, o fluxo terá sucesso.   Quando o fluxo mapeia de volta para si mesmo, o fluxo de saída parece originar da VM para o front-end e o fluxo de entrada correspondente parece originar-se da VM para si mesmo. Do ponto de vista do SO convidado, as partes de entrada e saída do mesmo fluxo não correspondem dentro da máquina virtual. A pilha TCP não reconhecerá essas partes do fluxo como fazendo parte do mesmo fluxo, pois a origem e o destino não correspondem.  Quando o fluxo é mapeado para qualquer outra VM no pool de back-end, as metades do fluxo serão correspondidas e a VM poderá responder com êxito ao fluxo.  O sintoma para esse cenário é o tempo limite de conexão intermitente. Há várias soluções alternativas comuns para alcançar esse cenário de forma confiável (originando fluxos de um pool de back-end para os pools de back-ends respectivos Load Balancer front-end) que incluem a inserção de um proxy de terceiros atrás do Load Balancer interno ou o [uso de regras de estilo DSR](load-balancer-multivip-overview.md).  Embora possa utilizar um balanceador de carga público para mitigar o problema, o cenário resultante é propenso a [esgotamento de SNAT](load-balancer-outbound-connections.md#snat) e deve ser evitado, salvo se for gerido cuidadosamente.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [Azure Load Balancer](load-balancer-overview.md).
- Saiba mais sobre como usar [Standard Load Balancer e zonas de disponibilidade](load-balancer-standard-availability-zones.md).
- Saiba mais sobre [investigações de integridade](load-balancer-custom-probe-overview.md).
- Saiba mais sobre o [diagnóstico de Standard Load Balancer](load-balancer-standard-diagnostics.md).
- Saiba mais sobre as [métricas multidimensionais com suporte](../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) para diagnósticos no [Azure monitor](../monitoring-and-diagnostics/monitoring-overview.md).
- Aprenda a usar [Balanceador de carga para ligações de saída](load-balancer-outbound-connections.md).
- Saiba mais sobre [as regras de saída](load-balancer-outbound-rules-overview.md).
- Saiba mais sobre a [redefinição de TCP em ociosidade](load-balancer-tcp-reset.md).
- Saiba mais sobre [Standard Load Balancer com regras de balanceamento de carga de portas de alta disponibilidade](load-balancer-ha-ports-overview.md).
- Saiba mais sobre como usar [Load Balancer com vários front-ends](load-balancer-multivip-overview.md).
- Saiba mais sobre [grupos de segurança de rede](../virtual-network/security-overview.md).
