---
title: O que é o Balanceador de carga Standard do Azure?
titlesuffix: Azure Load Balancer
description: Descrição geral das funcionalidades do Balanceador de carga Standard do Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/28/2019
ms.author: kumud
ms.openlocfilehash: 266630cb7c9601af69073a6c9beb7d7ada9b8034
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65957479"
---
# <a name="azure-standard-load-balancer-overview"></a>Descrição geral do Balanceador de carga Standard do Azure

O Balanceador de carga do Azure permite-lhe dimensionar as suas aplicações e criar disponibilidade elevada para os seus serviços. Balanceador de carga pode ser utilizado para cenários de entrada e de saída e fornece um débito elevado, de baixa latência e aumenta até milhões de fluxos para todas as aplicações TCP e UDP. 

Este artigo se concentra no balanceador de carga Standard.  Para obter uma descrição geral mais geral para o Balanceador de carga do Azure, reveja [descrição geral do Balanceador de carga](load-balancer-overview.md) também.

## <a name="what-is-standard-load-balancer"></a>O que é o Balanceador de Carga Standard?

Balanceador de carga Standard é um novo produto de Balanceador de carga para aplicativos de todos os TCP e UDP com um conjunto ao longo do Balanceador de carga básico de recursos expandidos e mais granulares.  Embora existam muitas semelhanças, é importante familiarizar-se com as diferenças, conforme descrito neste artigo.

Pode utilizar o Balanceador de carga Standard como um balanceador de carga interno ou público. E uma máquina virtual pode estar conectada a um público e um recurso de Balanceador de carga interno.

Funções do recurso de Balanceador de carga sempre são expressos como um front-end, uma regra, uma sonda de estado de funcionamento e uma definição de conjunto de back-end.  Um recurso pode conter várias regras. Pode colocar as máquinas virtuais para o pool de back-end, especificando o conjunto de back-end do recurso NIC da máquina virtual.  Este parâmetro é transmitido o perfil de rede e expandido ao utilizar conjuntos de dimensionamento de máquina virtual.

Um aspecto-chave é o âmbito da rede virtual para o recurso.  Embora o Balanceador de carga básico existir dentro do escopo de um conjunto de disponibilidade, um balanceador de carga Standard está totalmente integrado com o âmbito de uma rede virtual e todos os conceitos de rede virtual aplicam-se.

Recursos do Balanceador de carga são objetos nos quais pode expressar como o Azure deve programar a sua infraestrutura de multi-inquilino para alcançar o cenário que deseja criar.  Não existe nenhuma relação direta entre recursos do Balanceador de carga e a infraestrutura real; criar um balanceador de carga não cria uma instância, capacidade está sempre disponível e existem não arranque ou dimensionamento atrasos a serem considerados. 

>[!NOTE]
> O Azure fornece um conjunto de soluções para os seus cenários de balanceamento de carga totalmente gerido.  Se estiver à procura de encerramento de TLS ("descarga de SSL") ou por processamento de camada de aplicativo de pedidos HTTP/HTTPS, reveja [Gateway de aplicação](../application-gateway/application-gateway-introduction.md).  Se pretender balanceamento de carga de DNS global, reveja [Gestor de Tráfego](../traffic-manager/traffic-manager-overview.md).  Seus cenários de ponto a ponto podem beneficiar de combinar essas soluções, conforme necessário.

## <a name="why-use-standard-load-balancer"></a>Porquê utilizar o Balanceador de carga Standard?

O Balanceador de Carga Standard permite-lhe dimensionar as suas aplicações e criar uma elevada disponibilidade para implementações de pequena escala em arquiteturas com várias zonas grandes e complexas.

Reveja a tabela abaixo para uma visão geral das diferenças entre o Balanceador de carga Standard e o Balanceador de carga básico:

>[!NOTE]
> Os designs novos devem adotar o Balanceador de Carga Standard. 

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Revisão [limites de serviço para o Balanceador de carga](https://aka.ms/lblimits), bem como [preços](https://aka.ms/lbpricing), e [SLA](https://aka.ms/lbsla).


### <a name="backend"></a>Conjunto back-end

Expanda conjuntos de back-end de Balanceador de carga Standard a qualquer recurso de máquina virtual numa rede virtual.  Pode conter até 1000 instâncias de back-end.  Uma instância de back-end é uma configuração de IP, o que é uma propriedade de um recurso NIC.

O conjunto de back-end pode conter máquinas virtuais autónomas, conjuntos de disponibilidade ou conjuntos de dimensionamento de máquina virtual.  Também pode misturar os recursos no conjunto de back-end. Pode combinar até 150 recursos no conjunto de back-end por recurso de Balanceador de carga.

Ao considerar como estruturar o seu conjunto de back-end, pode criar para o menor número de recursos do agrupamento de back-end individuais para otimizar ainda mais a duração das operações de gestão.  Não existe nenhuma diferença no desempenho do plano de dados ou a escala.

### <a name="probes"></a>Sondas de estado de funcionamento
  
Balanceador de carga Standard adiciona suporte para [sondas de estado de funcionamento do HTTPS](load-balancer-custom-probe-overview.md#httpprobe) (sonda HTTP com o wrapper de Transport Layer Security (TLS)) com precisão a monitorizar as aplicações de HTTPS.  

Além disso, quando o conjunto de back-end toda [sondas para baixo](load-balancer-custom-probe-overview.md#probedown), Balanceador de carga Standard permite das conexões TCP estabelecidas tudo continuar. (O Balanceador de carga básico irá terminar todas as conexões TCP para todas as instâncias).

Revisão [sondas de estado de funcionamento do Balanceador de carga](load-balancer-custom-probe-overview.md) para obter detalhes.

### <a name="az"></a>As zonas de disponibilidade

>[!IMPORTANT]
>Revisão [zonas de disponibilidade](../availability-zones/az-overview.md) para tópicos relacionados, incluindo quaisquer informações específicas da região.

Balanceador de carga Standard suporta capacidades adicionais nas regiões onde as zonas de disponibilidade estão disponíveis.  Estas funcionalidades são incrementais para todos os Balanceador de carga Standard oferece.  Configurações de zonas de disponibilidade estão disponíveis para o Balanceador de carga Standard público e internos.

Front-ends não zonal tornam-se com redundância de zona por predefinição, quando implementado numa região com zonas de disponibilidade.   Um front-end com redundância de zona sobrevive a falha de zona e é atendido por infraestrutura dedicada em todas as zonas em simultâneo. 

Além disso, pode garantir um front-end para uma zona específica. Um front-end zonal partilha de destino com a zona respectiva e é servido apenas pela infraestrutura dedicada numa única zona.

Balanceamento de carga entre zonas está disponível para o conjunto de back-end e qualquer recurso de máquina virtual numa vnet pode fazer parte de um conjunto de back-end.

Revisão [análise detalhada das zonas de disponibilidade relacionados com capacidades](load-balancer-standard-availability-zones.md).

### <a name="diagnostics"></a> Diagnóstico

Balanceador de carga Standard fornece métricas multidimensionais através do Azure Monitor.  Estas métricas podem ser filtradas, agrupadas e divididas para uma dimensão especificada.  Eles fornecem informações atual e históricos sobre desempenho e estado de funcionamento do seu serviço.  Estado de funcionamento do recurso também é suportado.  Segue-se uma breve descrição geral de diagnóstico suportado:

| Métrica | Descrição |
| --- | --- |
| Disponibilidade de VIP | Balanceador de carga Standard exercita continuamente o caminho de dados de dentro de uma região para o Balanceador de carga front-end para a pilha SDN, que oferece suporte a sua VM. Enquanto permanecerem instâncias de bom estado de funcionamento, a medição segue o mesmo caminho que o tráfego com balanceamento de carga da sua aplicação. O caminho de dados que é utilizado pelos clientes também é validado. A medição é invisível para seu aplicativo e não interfere com outras operações.|
| Disponibilidade do DIP | Balanceador de carga Standard utiliza um serviço que monitoriza o estado de funcionamento do seu aplicativo do ponto de extremidade, de acordo com as definições de configuração de pesquisa de estado de funcionamento distribuído. Esta métrica fornece um agregado ou por ponto final-vista filtrada de cada ponto de extremidade de instância individual no balanceador de carga do agrupamento.  Pode ver como o Balanceador de carga visualiza o estado de funcionamento da sua aplicação, conforme indicado pela sua configuração de sonda de estado de funcionamento.
| Pacotes SYN | Balanceador de carga Standard não terminar ligações de TCP ou interagir com os fluxos de pacotes TCP ou UDP. Fluxos e seus handshakes são sempre entre a origem e a instância VM. Para resolver melhor seus cenários de protocolo TCP, pode fazer uso de SYN contadores de pacotes para compreender quantos ligação de TCP são feitas tentativas. A métrica relata o número de pacotes de TCP SYN que foram recebidos.|
| Ligações SNAT | Balanceador de carga Standard relata o número de fluxos de saída que são masqueraded para o endereço IP público front-end. Portas SNAT são um recurso exhaustible. Esta métrica pode dar uma indicação de quanto seu aplicativo depende SNAT para os fluxos de originados de saída.  Contadores para fluxos de SNAT de saída com êxito e falhados são reportadas e podem ser usados para solucionar problemas e compreender o estado de funcionamento dos seus fluxos de saída.|
| Contadores de byte | Relatórios dos dados processados por front-end do Balanceador de carga Standard.|
| Contadores de pacotes | Os relatórios do Balanceador de carga Standard pacotes processados por front-end.|

Revisão [detalhadas a discussão de diagnóstico de Balanceador de carga Standard](load-balancer-standard-diagnostics.md).

### <a name="haports"></a>HA portas

Balanceador de carga Standard oferece suporte a um novo tipo de regra.  

Pode configurar regras de balanceamento de carga para fazer o dimensionamento do aplicativo e altamente confiáveis. Quando utiliza uma regra de balanceamento de carga para portas HA, o Balanceador de carga Standard fornecerá por cada porta efêmera do endereço IP de front-end de um interno Balanceador de carga Standard de balanceamento de carga de fluxo.  A funcionalidade é útil para outros cenários onde é impraticável ou indesejado para especificar portas individuais.

Uma regra de balanceamento de carga para portas HA permite-lhe criar ativa-passiva ou ativa-ativa n + 1 cenários para dispositivos de rede Virtual e de qualquer aplicativo, o que requer grandes intervalos de portas de entrada.  Uma sonda de estado de funcionamento pode ser utilizada para determinar quais back-ends devem receber novos fluxos.  Pode utilizar um grupo de segurança de rede para emular um cenário de intervalo de porta.

>[!IMPORTANT]
> Se estiver a planear utilizar uma aplicação de rede Virtual, contacte o seu fornecedor para obter orientações sobre se o seu produto foi testado com portas HA e siga suas orientações específicas para implementação. 

Revisão [detalhadas a discussão das portas HA](load-balancer-ha-ports-overview.md).

### <a name="securebydefault"></a>Seguro por padrão

Balanceador de carga Standard é totalmente integrado para a rede virtual.  A rede virtual é uma rede privada, fechada.  Uma vez que os balanceadores de carga Standard e endereços IP públicos Standard foram concebidos para permitir que esta rede virtual ser acessado de fora da rede virtual, esses recursos agora padrão fechado, a menos que abri-los. Isso significa que os grupos de segurança de rede (NSGs) agora são utilizados para conceder explicitamente e tráfego permitido de lista de permissões.  Pode criar o seu Datacenter virtual inteiro e decidir através do NSG, o que e quando devem estar disponível.  Se não tiver um NSG numa sub-rede ou NIC do seu recurso de máquina virtual, o tráfego não tem permissão para aceder este recurso.

Para saber mais sobre NSGs e como aplicá-las para o seu cenário, consulte [grupos de segurança de rede](../virtual-network/security-overview.md).

### <a name="outbound"></a> Ligações de saída

Load Balancer suporta cenários de entrada e saídos.  Balanceador de carga Standard é significativamente diferente de Balanceador de carga básico em relação a ligações de saída.

Tradução de endereços de rede de origem (SNAT) é utilizado para mapear internos, privados endereços IP na sua rede virtual para endereços IP públicos no front-ends do Balanceador de carga.

Balanceador de carga Standard apresenta um novo algoritmo para um [algoritmo SNAT mais robusto, escalável e previsível](load-balancer-outbound-connections.md#snat) e capacidade de novas ativa, remove ambiguidade e configurações explícitas de forças lado, em vez disso, efeitos. Estas alterações são necessárias para permitir novas funcionalidades a surgir. 

Estes são os princípios importantes a lembrar ao trabalhar com o Balanceador de carga Standard:

- a conclusão de uma regra impulsiona o recurso do Balanceador de carga.  toda a programação do Azure é derivada da respetiva configuração.
- Quando vários front-ends estão disponíveis, todos os de front-ends são utilizados e cada front-end multiplica o número de portas SNAT disponíveis
- Pode escolher e controlar se não pretender para um front-end específico a ser utilizado para ligações de saída.
- cenários de saída são explícitos e conectividade de saída não existir até que foi especificado.
- regras de balanceamento de carga inferir como SNAT programado. Regras de balanceamento de carga são específico de protocolo. SNAT é específica de protocolo e a configuração deverá refletir isso em vez de criar um efeito colateral.

#### <a name="multiple-frontends"></a>Vários front-ends
Se quiser mais portas SNAT porque está esperando ou está já a experienciar uma procura elevada de ligações de saída, também pode adicionar inventário de porta SNAT incremental através da configuração de front-ends adicionais, regras e conjuntos de back-end para a mesma máquina virtual recursos.

#### <a name="control-which-frontend-is-used-for-outbound"></a>Controle qual front-end é utilizado para a saída
Se quiser restringir ligações de saída para apenas ser originado de um endereço IP de front-end específicas, opcionalmente, pode desativar SNAT de saída na regra que expresse o mapeamento de saída.

#### <a name="control-outbound-connectivity"></a>Conectividade de saída do controle
Balanceador de carga Standard existe dentro do contexto da rede virtual.  Uma rede virtual é uma rede privada isolada.  A menos que exista uma associação com um endereço IP público, pública conectividade não é permitida.  Pode entrar [pontos finais de serviço de VNet](../virtual-network/virtual-network-service-endpoints-overview.md) porque se encontra dentro do e local à sua rede virtual.  Se quiser estabelecer conectividade de saída para um destino fora da sua rede virtual, tem duas opções:
- atribuir um endereço IP público de SKU padrão como um endereço IP público de nível de instância para o recurso de máquina virtual ou
- coloca o recurso de máquina virtual no conjunto de back-end de um balanceador de carga Standard público.

Ambos permitirá que a conectividade de saída da rede virtual para fora da rede virtual. 

Se _apenas_ tem um balanceador de carga de padrão interno, associado com o conjunto de back-end em que o recurso de máquina virtual se encontra, sua máquina virtual só pode aceder a recursos de rede virtual e [serviço de VNet Pontos de extremidade](../virtual-network/virtual-network-service-endpoints-overview.md).  Pode seguir os passos descritos no parágrafo anterior para criar a conectividade de saída.

Conectividade de saída de um recurso de máquina virtual não associado a SKUs padrão permanece como antes.

Revisão [detalhadas discussão sobre as ligações de saída](load-balancer-outbound-connections.md).

### <a name="multife"></a>Vários front-ends
Load Balancer suporta várias regras com vários front-ends.  Balanceador de carga Standard expande isso para cenários de saída.  Cenários de saída são essencialmente o inverso de uma regra de balanceamento de carga de entrada.  A regra de balanceamento de carga de entrada também cria associado para ligações de saída. Balanceador de carga Standard utiliza front-ends todos os associados a um recurso de máquina virtual através de uma regra de balanceamento de carga.  Além disso, um parâmetro na regra de balanceamento de carga e permite-lhe suprimir uma regra de balanceamento de carga para fins de conectividade de saída, que permite a seleção de front-ends específica incluindo none.

Para comparação, Balanceador de carga básico seleciona um front-end único aleatoriamente e está disponível nenhuma capacidade para controlar qual deles tiver sido selecionada.

Revisão [detalhadas discussão sobre as ligações de saída](load-balancer-outbound-connections.md).

### <a name="operations"></a> Operações de gestão

Recursos do Balanceador de carga padrão existem numa inteiramente nova plataforma de infraestrutura.  Isto permite operações de gestão mais rápidas para SKUs padrão e tempos de conclusão são, normalmente, menos de 30 segundos por recurso de SKU padrão.  Como os conjuntos de back-end aumentam de tamanho, a duração necessária para o back-end conjunto de alterações também aumento.

Pode modificar os recursos do Balanceador de carga Standard e mover um endereço IP público Standard de uma máquina virtual para outro a muito mais rápido.

## <a name="migration-between-skus"></a>Migração entre SKUs

SKUs não são mutáveis. Siga os passos nesta secção para mover de um recurso de SKU para outro.

>[!IMPORTANT]
>Reveja este documento em sua totalidade para compreender as diferenças entre SKUs e examinei cuidadosamente seu cenário.  Terá de fazer alterações adicionais para alinhar o seu cenário.

### <a name="migrate-from-basic-to-standard-sku"></a>Migrar do básico para o Standard SKU

1. Crie um novo recurso padrão (Balanceador de carga e IPs públicos, conforme necessário). Recriar suas regras e definições de pesquisa.  Se estava a utilizar uma sonda TCP para 443/tcp anteriormente, pondere alterar este protocolo de sonda para uma pesquisa HTTPS e adicione um caminho.

2. Criar um novo ou Atualize o NSG existente no NIC ou uma sub-rede para o tráfego com balanceamento de carga de lista aprovada, pesquisa, bem como qualquer outro tráfego que pretende permitir.

3. Remova os recursos de SKU básico (Balanceador de carga e IPs públicos, conforme aplicável) todas as instâncias VM. Certifique-se de que também remover todas as instâncias VM de um conjunto de disponibilidade.

4. Anexe todas as instâncias VM para os novos recursos de SKU padrão.

### <a name="migrate-from-standard-to-basic-sku"></a>Migrar do plano padrão para o SKU básico

1. Crie um novo recurso básico (Balanceador de carga e IPs públicos, conforme necessário). Recriar suas regras e definições de pesquisa.  Altere uma sonda HTTPS para uma sonda TCP para 443/tcp. 

2. Remova os recursos de Standard SKU (Balanceador de carga e IPs públicos, conforme aplicável) todas as instâncias VM. Certifique-se de que também remover todas as instâncias VM de um conjunto de disponibilidade.

3. Anexe todas as instâncias VM para os novos recursos de SKU básico.

>[!IMPORTANT]
>
>Existem limitações relativamente à utilização dos SKUs básico e Standard.
>
>HA portas e os diagnósticos do Standard SKU só estão disponíveis na Standard SKU. Não é possível migrar de Standard SKU para o SKU básico e também manter esses recursos.
>
>SKU padrão e básico têm um número de diferenças, conforme descrito neste artigo.  Certifique-se de compreender e preparar para os mesmos.
>
>Correspondência das SKUs tem de ser utilizada para os recursos do Balanceador de carga e o IP público. Não pode ter uma mistura de recursos de SKU básico e recursos de SKU padrão. Não é possível anexar máquinas virtuais autónomas, máquinas virtuais num recurso de conjunto de disponibilidade ou uma máquina virtual dos recursos do conjunto de dimensionamento para ambos os SKUs em simultâneo.

## <a name="region-availability"></a>Disponibilidade de região

Balanceador de carga Standard está atualmente disponível em todas as regiões de cloud pública.

## <a name="sla"></a>SLA

Balanceadores de carga Standard estão disponíveis com um SLA de 99,99%.  Reveja os [SLA de Balanceador de carga Standard](https://aka.ms/lbsla) para obter detalhes.

## <a name="pricing"></a>Preços

Utilização de Balanceador de carga Standard é cobrada.

- Número de configurado regras de balanceamento de carga e de saída (regras NAT de entrada não contam para o número total de regras)
- Quantidade de dados processados entrada e saída irrespective de regra. 

Para obter as informações de preços do Balanceador de Carga Standard, aceda à página [Preços de Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="limitations"></a>Limitações

- SKUs não são mutáveis. Não pode alterar o SKU de um recurso existente.
- Conjunto de disponibilidade de um recurso de máquina virtual autónoma, recursos ou recurso de conjunto de dimensionamento de máquina virtual pode fazer referência a um SKU, não ambos.
- Uma regra de Balanceador de carga não pode abranger duas redes virtuais.  Front-ends e suas instâncias de back-end relacionada tem de estar localizadas na mesma rede virtual.  
- [Mover as operações de subscrição](../azure-resource-manager/resource-group-move-resources.md) não são suportadas para recursos de LB de SKU padrão e o PIP.
- Funções de trabalho de Web sem uma VNet e a outros serviços de plataforma da Microsoft pode ser acessíveis quando apenas um Standard Balanceador de carga interno é utilizado devido a um efeito colateral de como os serviços de pré-VNet e outra plataforma dos serviços de função. Tem não contar com isso como respetivos do serviço em si ou subjacentes plataforma pode alteradas sem aviso prévio. Deve sempre partem do princípio de que precisa para criar [conectividade de saída](load-balancer-outbound-connections.md) explicitamente se assim o desejar quando utilizar um Standard Balanceador de carga interno apenas.
- O Balanceador de Carga é um produto TCP ou UDP para balanceamento de carga e encaminhamento de portas para estes dois protocolos IP específicos.  As regras de balanceamento de carga e as regras NAT de entrada são suportadas para TCP e UDP, mas não para os outros protocolos IP, incluindo o ICMP. O Balanceador de Carga não termina, não responde nem interage com o payload dos fluxos UDP ou TCP. Não é um proxy. Validação com êxito de conectividade para um front-end tem de efetuar lugar em banda com o mesmo protocolo usado numa entrada ou balanceamento regra NAT de carga (TCP ou UDP) _e_ , pelo menos, uma das suas máquinas virtuais tem de gerar uma resposta para um cliente Para ver uma resposta de um front-end.  Não receber uma resposta em banda do front-end de Balanceador de carga indica que nenhuma máquina virtual foram capaz de responder.  Não é possível interagir com um balanceador de carga front-end sem uma máquina virtual capaz de responder.  Isto também se aplica às ligações de saída, em que o [SNAT de máscara de rede](load-balancer-outbound-connections.md#snat) só é suportado para TCP e UDP; qualquer outro protocolo IP, incluindo ICMP, falhará.  Para mitigar o problema, atribua um endereço IP público ao nível da instância.
- Ao contrário dos balanceadores de carga público que fornecem [ligações de saída](load-balancer-outbound-connections.md) durante a transição de endereços IP privados dentro da rede virtual para endereços IP públicos, balanceadores de carga interno não traduzir saída teve origem ligações para o front-end de um balanceador de carga interno como ambos estão no espaço de endereços IP privados.  Isso evita a possibilidade de esgotamento de SNAT dentro exclusivo espaço de endereços IP interno onde a tradução não é necessária.  O efeito é que, se um fluxo de saída de uma VM no conjunto de back-end tenta um fluxo para o front-end de Balanceador de carga interno no qual conjunto reside _e_ é mapeado para ele mesmo, as duas primeiras partes do fluxo não coincidem e o fluxo irá falhar .  Se o fluxo não mapeado para a mesma VM no conjunto de back-end que criou o fluxo para o front-end, o fluxo será concluída com êxito.   Quando o fluxo é mapeado para ele mesmo o fluxo de saída é apresentada se originar de VM para o front-end e o fluxo de entrada correspondente é exibido originar da VM para si próprio. Do ponto de vista do SO convidado, as partes de entrada e saída do mesmo fluxo não correspondem dentro da máquina virtual. A pilha TCP não reconhecerá essas partes do fluxo como fazendo parte do mesmo fluxo, pois a origem e o destino não correspondem.  Quando o fluxo é mapeado para qualquer outra VM no conjunto de back-end, corresponderão ao metades do fluxo e a VM pode responder com êxito para o fluxo.  O sintoma para este cenário é o tempo limite das conexões intermitentes. Existem várias soluções comuns para obtenção de forma fiável neste cenário (com origem fluxos a partir de um conjunto de back-end para back-end conjuntos de Balanceador de carga interno respectivos front-end) que incluem a inserção de um proxy de terceiros por trás de carga interno Balanceador ou [usando regras de estilo DSR](load-balancer-multivip-overview.md).  Embora possa utilizar um balanceador de carga público para mitigar o problema, o cenário resultante é propenso a [esgotamento de SNAT](load-balancer-outbound-connections.md#snat) e deve ser evitado, salvo se for gerido cuidadosamente.

## <a name="next-steps"></a>Passos Seguintes

- Aprenda a usar [Balanceador de carga Standard e zonas de disponibilidade](load-balancer-standard-availability-zones.md).
- Saiba mais sobre [sondas de estado de funcionamento](load-balancer-custom-probe-overview.md).
- Saiba mais sobre [zonas de disponibilidade](../availability-zones/az-overview.md).
- Saiba mais sobre [diagnósticos do Balanceador de carga Standard](load-balancer-standard-diagnostics.md).
- Saiba mais sobre [suportado métricas multidimensionais](../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) para obter um diagnóstico no [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md).
- Aprenda a usar [Balanceador de carga para ligações de saída](load-balancer-outbound-connections.md).
- Saiba mais sobre [regras de saída](load-balancer-outbound-rules-overview.md).
- Saiba mais sobre [TCP repor ociosos](load-balancer-tcp-reset.md).
- Saiba mais sobre [Balanceador de carga Standard com regras de balanceamento de carga de portas HA](load-balancer-ha-ports-overview.md).
- Aprenda a usar [Balanceador de carga com vários front-ends](load-balancer-multivip-overview.md).
- Saiba mais sobre [redes virtuais](../virtual-network/virtual-networks-overview.md).
- Saiba mais sobre [grupos de segurança de rede](../virtual-network/security-overview.md).
- Saiba mais sobre [pontos finais de serviço de VNet](../virtual-network/virtual-network-service-endpoints-overview.md).
- Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) no Azure.
- Saiba mais sobre [Balanceador de carga](load-balancer-overview.md).
