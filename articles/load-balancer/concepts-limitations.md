---
title: Componentes e Limitações
titleSuffix: Azure Load Balancer
description: Visão geral dos componentes e limitações do Equilíbrio de Carga Azure.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer components and limitations and how it will affect my environment.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/14/2020
ms.author: allensu
ms.openlocfilehash: aab6a4de7be57df1f691861533a4528a0bcae571
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241683"
---
# <a name="load-balancer-components-and-limitations"></a>Componentes e limitações do Balancer de Carga
O Azure Load Balancer contém vários componentes chave para o seu funcionamento.  Estes componentes podem ser configurados na sua subscrição através do portal Azure, Azure CLI ou Azure PowerShell.  

## <a name="load-balancer-components"></a>Componentes do Balancer de Carga

* **Configurações IP frontend**: O endereço IP do equilibrador de carga. É o ponto de contacto dos clientes. Estes endereços podem ser: 

    - **[Endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)**
    - **[Endereço IP privado](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)**

* **Piscina de backend**: O grupo de máquinas virtuais ou instâncias no Conjunto de Escala de Máquina virtual que vão servir o pedido de entrada. Para escalar os custos de forma eficaz para satisfazer os elevados volumes de tráfego que chegam, as diretrizes de computação geralmente recomendam adicionar mais instâncias à piscina de backend. O Balancer de carga reconfigura-se instantaneamente através da reconfiguração automática quando escala as instâncias para cima ou para baixo. A adição ou remoção de VMs da piscina de backend reconfigura o Balancer de Carga sem operações adicionais. O âmbito da piscina de backend é qualquer máquina virtual na rede virtual. Uma piscina de backend pode ter até 1000 instâncias de backend ou configurações IP.
Os Equilibradores básicos de carga têm um âmbito limitado (conjunto de disponibilidade) só podem escalar até 300 configurações IP. Para obter mais informações sobre os limites, consulte [os limites do Balancer de Carga](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). Ao considerar como projetar o seu backend pool, você pode projetar para o menor número de recursos individuais de reserva para otimizar ainda mais a duração das operações de gestão. Não há diferença no desempenho ou escala do avião de dados.
* **Sondas**de **[saúde](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** : Uma sonda de saúde é usada para determinar a saúde dos casos na piscina de backend. Pode definir o limiar pouco saudável para as suas sondas de saúde. Quando uma sonda não consegue responder, o Balanceador de Carga deixa de enviar ligações novas para as instâncias em mau estado de funcionamento. Uma falha na sonda não afeta as ligações existentes. 
    
    A ligação continua até à aplicação: 
    - Acaba com o fluxo
    - O tempo limite de marcha lenta ocorre
    - O VM fecha

    O Balancer de Carga fornece diferentes tipos de sonda de saúde para pontos finais:
    - TCP
    - HTTP
    - HTTPS (sonda HTTP com invólucro de segurança de camada de transporte (TLS)
     
     O Equilíbrio de Carga Básico não suporta sondas HTTPS. Além disso, o Equilíbrio de Carga Básico terminará todas as ligações TCP (incluindo ligações estabelecidas). 
    Para mais informações, consulte [os tipos de sonda](load-balancer-custom-probe-overview.md#types).

* **Regras de equilíbrio de carga**: As regras de equilíbrio de carga são as que dizem ao Equilibrador de Carga o que tem de ser feito quando. 
* **Regras**nat de entrada : Uma regra nat de entrada reencaminha o tráfego de um porta específico de um endereço IP frontal específico para um porto específico de uma instância específica de backend dentro da rede virtual. **[O encaminhamento](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** por porta é feito pela mesma distribuição baseada em hash como o equilíbrio de carga. Os cenários comuns para esta capacidade são o Protocolo de Ambiente de Trabalho Remoto (RDP) ou sessões de Secure Shell (SSH) para instâncias VM individuais dentro de uma Rede Virtual Azure. Pode mapear vários pontos finais internos para portas no mesmo endereço IP frontal. Pode utilizar os endereços IP frontais para administrar remotamente os seus VMs sem uma caixa de salto adicional.
* **Regras de saída**: Uma regra de **[saída](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)** configura a tradução de endereços de rede (NAT) para todas as máquinas virtuais ou instâncias identificadas pelo conjunto de backend do seu Balancer de Carga Padrão a traduzir para a extremidade frontal.
O Equilíbrio de Carga Básico não suporta regras de saída.
![Azure Load Balancer](./media/load-balancer-overview/load-balancer-overview.png)

## <a name = "load-balancer-concepts"></a>Conceitos de Balancer de Carga

O Balanceador de Carga proporciona as seguintes capacidades básicas para as aplicações TCP e UDP:

* **Algoritmo de equilíbrio de carga**: Com o Azure Load Balancer, pode criar uma regra de equilíbrio de carga para distribuir o tráfego que chega à extremidade frontal para instâncias de piscina de backend. Load Balancer usa um algoritmo de hashing para distribuição de fluxos de entrada (não bytes) e reescreve os cabeçalhos dos fluxos para apoiar as instâncias da piscina. Um servidor está disponível para receber novos fluxos quando uma sonda de saúde indica um ponto final saudável.
Por predefinição, o Balancer load usa um hash de 5-tuple. 

   O hash inclui: 

   - **Endereço IP de origem**
   - **Porta-fonte**
   - **Endereço IP de destino**
   - **Porto de destino**
   - **Número de protocolo IP para mapas de fluxos para servidores disponíveis** 

Pode criar afinidade com um endereço IP de origem utilizando um hash de 2 ou 3 tuple para uma determinada regra. Todos os pacotes do mesmo fluxo de pacotes são entregues na mesma instância, atrás do front-end com carga balanceada. Quando o cliente inicia um novo fluxo a partir da mesma fonte IP, a porta de origem é alterada. Como resultado, o hash de 5-tuple pode fazer com que o tráfego vá para um ponto final diferente.
Para mais informações, consulte Configure o modo de [distribuição para O Equilíbrio de Carga Azure](./load-balancer-distribution-mode.md). 

A imagem seguinte apresenta a distribuição baseada em hashes:

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer-distribution.svg" width="512" title="Distribuição baseada em hashes">
</p>

  *Figura: distribuição baseada em hashes*

* **Independência e transparência**da aplicação : O Balancer load não interage diretamente com tCP ou UDP ou com a camada de aplicação. Qualquer cenário de aplicação TCP ou UDP pode ser suportado. O Balancer de Carga não termina ou origina fluxos, interage com a carga útil do fluxo, ou fornece qualquer função de gateway da camada de aplicação. Os apertos de mão protocolares ocorrem sempre diretamente entre o cliente e a instância de piscina de fundo. Uma resposta a um fluxo de entrada é sempre uma resposta de uma máquina virtual. Quando o fluxo chega à máquina virtual, o endereço IP de origem original também é preservado.
  * Cada ponto final só recebe uma resposta de uma VM. Por exemplo, ocorre sempre um aperto de mão TCP entre o cliente e o VM traseiro selecionado. Uma resposta a um pedido para uma frente é uma resposta gerada por um VM de back-end. Quando valida com sucesso a conectividade para uma extremidade frontal, está a validar a conectividade de ponta a ponta a pelo menos uma máquina virtual de ponta a ponta.
  * As cargas de aplicação são transparentes para o Balancer load. Qualquer aplicação UDP ou TCP pode ser suportada.
  * Como o Load Balancer não interage com a carga útil do TCP e fornece a descarga de TLS, pode construir cenários encriptados de ponta a ponta. A utilização do Balancer de Carga ganha uma grande escala para aplicações TLS, terminando a ligação TLS no próprio VM. Por exemplo, a sua capacidade de chave de sessão TLS é limitada apenas pelo tipo e número de VMs que adiciona à piscina de back-end.

* **Ligações de saída**: Todos os fluxos de saída de endereços IP privados dentro da sua rede virtual para endereços IP públicos na internet podem ser traduzidos para um endereço IP frontend do Balancer de Carga. Quando uma extremidade frontal pública é ligada a um VM de backend através de uma regra de equilíbrio de carga, O Azure traduz ligações de saída para o endereço IP frontal público. Esta configuração tem as seguintes vantagens:
  * Fácil atualização e recuperação de desastres de serviços, porque a parte frontal pode ser mapeada dinamicamente para outra instância do serviço.
  * Gestão da lista de controlo de acesso (ACL) mais fácil. Os ACLs expressos como IPs frontais não mudam à medida que os serviços escalam para cima ou para baixo ou são redistribuídos. Traduzir as ligações de saída a um número menor de endereços IP do que as máquinas reduz o fardo da implementação de listas de destinatários seguros.

  O Standard Load Balancer utiliza um [algoritmo SNAT robusto, escalável e previsível.](load-balancer-outbound-connections.md#snat) Estes são os princípios-chave a recordar ao trabalhar com o Standard Load Balancer:

    - as regras de equilíbrio de carga inferem a forma como o SNAT é programado. As regras de equilíbrio de carga são específicas do protocolo. O SNAT é específico do protocolo e a configuração deve refletir isso em vez de criar um efeito colateral.

    - **Múltiplas frentes** Quando existem várias extremidades dianteiras disponíveis, todas as extremidades dianteiras são usadas e cada extremidade frontal multiplica o número de portas SNAT disponíveis. Se você quer mais portas SNAT porque você está esperando ou já está experimentando uma alta procura de conexões de saída, você também pode adicionar inventário incremental de porta SNAT configurando frontends adicionais, regras e piscinas de backend para a mesma máquina virtual recursos.

    - **Controlo que frontal é usado para saída** Pode escolher e controlar se não desejar que uma determinada extremidade frontal seja utilizada para ligações de saída. Se pretender limitar as ligações de saída a penas originárias de um endereço IP frontal específico, pode desativar opcionalmente o SNAT de saída na regra que expressa o mapeamento de saída.

    - **Os** cenários de saída da conectividade de controlo são explícitos e a conectividade de saída não existe até que tenha sido especificada. O Balancer de Carga Standard existe no contexto da rede virtual.  Uma rede virtual é uma rede privada isolada.  A menos que exista uma associação com um endereço IP público, a conectividade pública não é permitida.  Pode chegar aos [Pontos Finais](../virtual-network/virtual-network-service-endpoints-overview.md) do Serviço VNet porque estão dentro e local para a sua rede virtual.  Se quiser estabelecer conectividade de saída para um destino fora da sua rede virtual, tem duas opções:
        - atribuir um endereço IP público Standard SKU como um endereço IP público de nível de instância para o recurso virtual da máquina ou
        - coloque o recurso virtual da máquina na piscina de backend de um equilibrador de carga padrão público.

        Ambos permitirão a conectividade de saída da rede virtual para fora da rede virtual. 

        Se tiver _apenas_ um Balancer de Carga Padrão interno associado ao pool de backend em que o seu recurso virtual está localizado, a sua máquina virtual só pode alcançar recursos de rede virtuais e [pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md)de serviço VNet .  Pode seguir os passos descritos no parágrafo anterior para criar conectividade de saída.

        A conectividade de saída de um recurso de máquina virtual não associado às SKUs padrão permanece como antes.

        Reveja [a discussão detalhada das Ligações de Saída](load-balancer-outbound-connections.md).

* **Zonas de Disponibilidade**: O Standard Load Balancer suporta capacidades adicionais em regiões onde estão disponíveis zonas de disponibilidade. Estas funcionalidades são incrementais para todos os saldos standard de carga.  Configurações de Zonas de Disponibilidade estão disponíveis para ambos os tipos, equilibrantes de carga padrão públicos e internos.
 Uma extremidade frontal redundante sobrevive à falha da zona e é servida por infraestruturas dedicadas em todas as zonas simultaneamente. 
Além disso, pode garantir uma frontend a uma zona específica. Um frontend zonal partilha o destino com a respetiva zona e é servido apenas por infraestruturas dedicadas numa única zona.
O equilíbrio de carga transversal está disponível para a piscina de backend, e qualquer recurso virtual de máquina numa rede virtual pode fazer parte de uma piscina de backend.
O Equilíbrio de Carga Básico não suporta zonas.
Reveja [a discussão detalhada sobre as capacidades relacionadas](load-balancer-standard-availability-zones.md) com as Zonas de Disponibilidade e a visão geral das [Zonas de Disponibilidade](../availability-zones/az-overview.md) para obter mais informações.

* **Portas HA**: Pode configurar regras de equilíbrio de carga para tornar a sua escala de aplicação e ser altamente fiável. Quando utilizar uma regra de equilíbrio de carga saqueada por Portas HA, o Standard Load Balancer fornecerá um equilíbrio de carga por cauda lúfluo em cada porta efémera de um endereço IP frontal do Standard Load Balancer interno.  A funcionalidade é útil para outros cenários em que é impraticável ou indesejável especificar portas individuais. Uma regra de equilíbrio de carga ha portas permite-lhe criar cenários n+1 ativos ativos ativos ou ativos ativos para eletrodomésticos virtuais de rede e qualquer aplicação, que requer grandes gamas de portas de entrada.  Uma sonda de saúde pode ser usada para determinar quais os backends que devem receber novos fluxos.  Pode utilizar um Grupo de Segurança de Rede para imitar um cenário de alcance portuário. O Equilíbrio de Carga Básico não suporta portas HA.
Revisão [detalhada da discussão sobre os Portos ha](load-balancer-ha-ports-overview.md)
>[!IMPORTANT]
> Se estiver a planear utilizar um Aparelho Virtual rede, consulte o seu fornecedor para obter orientações sobre se o seu produto foi testado com portas HA e siga as suas orientações específicas para a implementação. 

* **Múltiplas extremidades dianteiras**: Load Balancer suporta várias regras com várias extremidades dianteiras.  O Standard Load Balancer expande-o para cenários de saída.  Os cenários de saída são essencialmente o inverso de uma regra de equilíbrio de carga de entrada.  A regra de equilíbrio de carga de entrada também cria um associado para ligações de saída. O Standard Load Balancer utiliza todas as extremidades dianteiras associadas a um recurso de máquina virtual através de uma regra de equilíbrio de carga.  Além disso, um parâmetro na regra de equilíbrio de carga e permite suprimir uma regra de equilíbrio de carga para efeitos de conectividade de saída, que permite a seleção de frontendas específicas, incluindo nenhuma.

Para comparação, o Basic Load Balancer seleciona uma única extremidade frontal aleatoriamente e não há capacidade de controlar qual foi selecionada.
## <a name="load-balancer-types"></a>Tipos de balanceadores de carga

### <a name = "publicloadbalancer"></a>Balanceador de Carga Público

Um Balancer de Carga pública mapeia o endereço IP público e o porto de tráfego de entrada para o endereço IP privado e porto do VM. O Load Balancer mapeia o tráfego ao contrário para o tráfego de resposta do VM. Pode distribuir tipos específicos de tráfego em vários VMs ou serviços aplicando regras de equilíbrio de carga. Por exemplo, pode distribuir a carga do tráfego de pedidos da Web entre múltiplos servidores Web.

>[!NOTE]
>Só pode implementar um Balancer de Carga pública e um Balancer de Carga interna por conjunto de disponibilidade.

O valor seguinte mostra um ponto final equilibrado em carga para o tráfego web que é partilhado entre três VMs para o público e porta TCP 80. Estas três VMs estão num conjunto com carga balanceada.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer-http.svg" width="256" title="Equilibrador de carga pública">
</p>

*Figura: Equilibrar o tráfego web utilizando um equilibrador de carga pública*

Os clientes da Internet enviam pedidos de página web para o endereço IP público de uma aplicação web na porta 80 do TCP. O Azure Load Balancer distribui os pedidos pelos três VMs no conjunto equilibrado em carga. Para obter mais informações sobre algoritmos de Balancer load, consulte [os conceitos load Balancer](concepts-limitations.md#load-balancer-concepts).

O Azure Load Balancer distribui o tráfego da rede de forma igual entre várias instâncias vm por padrão. Também pode configurar a afinidade de sessão. Para mais informações, consulte Configure o modo de [distribuição para O Equilíbrio de Carga Azure](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Balanceador de Carga Interno

Um equilibrador de carga interno direciona o tráfego apenas para recursos que estão dentro de uma rede virtual ou que usam uma VPN para aceder à infraestrutura Azure, em contraste com um equilibrador de carga pública. A infraestrutura Azure restringe o acesso aos endereços IP frontais equilibrados em carga de uma rede virtual. Os endereços IP front-end e as redes virtuais nunca são diretamente expostos a um ponto final da Internet. As aplicações de linha de negócio internas são executadas no Azure e acedidas de dentro do Azure ou a partir de recursos no local.

Os balanceadores de carga internos permitem os seguintes tipos de balanceador de carga:

* **Dentro de uma rede virtual**: O equilíbrio de carga dos VMs na rede virtual para um conjunto de VMs que estão na mesma rede virtual.
* **Para uma rede virtual transversal**: O equilíbrio de carga dos computadores no local para um conjunto de VMs que se encontram na mesma rede virtual.
* **Para aplicações de vários níveis**: Equilibre a carga para aplicações multi-camadas viradas para a Internet, onde os níveis de back-end não estejam virados para a Internet. Os níveis de back-end requerem o equilíbrio da carga de tráfego a partir do nível virado para a Internet. Veja a próxima figura.
* **Para aplicações de linha de negócio**: balanceamento de carga para aplicações de linha de negócio que estão alojadas no Azure sem hardware ou software adicional de balanceador de carga. Este cenário inclui servidores no local que estão no conjunto de computadores cujo tráfego é equilibrado.


<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" width="256" title="Equilibrador de carga pública">
</p>

*Figura: Equilibrar aplicações a vários níveis utilizando equilibrantes de carga públicos e internos*

## <a name="skus"></a> Comparação de SKUs do Balanceador de Carga

O equilibrador de carga suporta as SKUs Básicas e Standard. Estas SKUs diferem na escala de cenários, funcionalidades e preços. Qualquer cenário que seja possível com o Equilíbrio de Carga Básico pode ser criado com o Balancer de Carga Padrão. As APIs para ambas as SKUs são semelhantes e são invocadas através da especificação de um SKU. A API para apoiar as SKUs para o equilibrador de carga e o IP público está disponível a partir do `2017-08-01` API. Ambas as SKUs partilham a mesma API geral e estrutura.

A configuração completa do cenário pode diferir ligeiramente dependendo do SKU. A documentação do equilibrador de carga chama quando um artigo se aplica apenas a um SKU específico. Para comparar e compreender as diferenças, veja a tabela seguinte. Para mais informações, consulte a visão geral do Equilíbrio de [Carga Padrão Azure](load-balancer-standard-overview.md).

>[!NOTE]
> A Microsoft recomenda o Balancer de Carga Padrão.
As VMs autónomas, os conjuntos de disponibilidade e os conjuntos de dimensionamento de máquinas virtuais só podem ser ligados a um SKU, nunca a ambos. Load Balancer e o endereço IP público SKU devem coincidir quando os utilizar com endereços IP públicos. O Balancer de Carga e as IP SKUs públicas não são mutáveis.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Para obter mais informações, consulte [os limites do equilíbrio de carga](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). Relativamente aos detalhes do Balanceador de Carga Standard, veja a [descrição geral](load-balancer-standard-overview.md), os [preços](https://aka.ms/lbpricing) e o [SLA](https://aka.ms/lbsla).

## <a name = "limitations"></a>Limitações

- As SKUs não são mutáveis. Não pode alterar o SKU de um recurso existente.
- Um recurso de máquina virtual autónomo, recurso definido de disponibilidade ou recurso conjunto de escala de máquina virtual pode fazer referência a um SKU, nunca ambos.
- Uma regra do Balancer de Carga não pode abranger duas redes virtuais.  Os frontends e as respetivas instâncias de backend relacionadas devem estar localizados na mesma rede virtual.  
- [As operações de subscrição de movimentos](../azure-resource-manager/management/move-resource-group-and-subscription.md) não são suportadas para recursos IP Standard LB e Público.
- As Funções dos Web Worker sem um VNet e outros serviços da plataforma Microsoft podem ser acessíveis a partir de instâncias por trás apenas de um Balancer de Carga Padrão interno. Não deve confiar nisto, uma vez que o próprio serviço ou a plataforma subjacente podem mudar sem aviso prévio. Deve sempre assumir que precisa de criar uma conectividade de [saída](load-balancer-outbound-connections.md) explicitamente, se desejar, quando utilizar apenas um Balancer de Carga Padrão interno.

- O Balancer de Carga fornece o equilíbrio de carga e o encaminhamento da porta para protocolos específicos de TCP ou UDP. As regras de equilíbrio de carga e as regras de entrada na NAT apoiam a TCP e a UDP, mas não outros protocolos IP, incluindo o ICMP.

  O Balancer de Carga não termina, responde ou interage de outra forma com a carga útil de um fluxo UDP ou TCP. Não é um representante. A validação bem sucedida da conectividade com uma extremidade frontal deve ter lugar em banda com o mesmo protocolo utilizado numa regra de equilíbrio de carga ou na nat de entrada. Pelo menos uma das suas máquinas virtuais deve gerar uma resposta para um cliente ver uma resposta de uma frente.

  Não receber uma resposta na banda da extremidade frontal do Balancer de carga indica que nenhuma máquina virtual poderia responder. Nada pode interagir com uma extremidade frontal load Balancer sem uma máquina virtual capaz de responder. Este princípio aplica-se igualmente às ligações de saída em que o SNAT de máscara portuária só é suportado para tCP e UDP. Quaisquer outros protocolos IP, incluindo o ICMP, falham. Designar um endereço IP público de nível de exemplo para mitigar esta questão. Para mais informações, consulte [Understanding SNAT e PAT](load-balancer-outbound-connections.md#snat).

- Os Equilibradores de Carga Interna não traduzem ligações originais de saída para a parte frontal de um Balancer interno de carga porque ambos estão no espaço de endereço IP privado. Os Balanceadores de Carga Pública fornecem [ligações](load-balancer-outbound-connections.md) de saída de endereços IP privados dentro da rede virtual para endereços IP públicos. Para os Equilibradores internos de carga, esta abordagem evita a possível exaustão da porta SNAT dentro de um espaço de endereço IP interno único, onde a tradução não é necessária.

  Um efeito colateral é que se um fluxo de saída de um VM na piscina traseira tentar um fluxo para a extremidade dianteira do Balancer de Carga interna na sua piscina _e_ for mapeado de volta para si mesmo, as duas pernas do fluxo não combinam. Como não combinam, o fluxo falha. O fluxo tem sucesso se o fluxo não mapear de volta para o mesmo VM na piscina traseira que criou o fluxo para a parte frontal.

  Quando o fluxo se mapeia de volta para si mesmo, o fluxo de saída parece ter origem do VM para a extremidade frontal e o fluxo de entrada correspondente parece ter origem do VM para si mesmo. Do ponto de vista do sistema operativo convidado, as partes de entrada e saída do mesmo fluxo não coincidem com a máquina virtual. A pilha de TCP não reconhecerá estas metades do mesmo fluxo que fazer parte do mesmo fluxo. A fonte e o destino não coincidem. Quando o fluxo mapeia qualquer outro VM na piscina traseira, as metades do fluxo correspondem e o VM pode responder ao fluxo.

  O sintoma para este cenário são os tempos de ligação intermitentes quando o fluxo volta ao mesmo backend que originou o fluxo. As sobras comuns incluem a inserção de uma camada proxy por trás do Balancer de Carga interna e a utilização de regras de estilo de retorno de servidor direto (DSR). Para mais informações, consulte [as extremidades dianteiras múltiplas para o Equilíbrio de Carga Azure](load-balancer-multivip-overview.md).

  Pode combinar um Balancer de Carga interno com qualquer representante de terceiros ou utilizar o Gateway de [Aplicação](../application-gateway/application-gateway-introduction.md) interna para cenários de procuração com HTTP/HTTPS. Embora possa utilizar um Balancer de Carga pública para mitigar esta questão, o cenário resultante é propenso à exaustão de [SNAT](load-balancer-outbound-connections.md#snat). Evite esta segunda abordagem a menos que cuidadosamente gerida.

- Em geral, o reencaminhamento de fragmentos de IP não é suportado em regras de equilíbrio de carga. A fragmentação ip dos pacotes UDP e TCP não é suportada em regras de equilíbrio de carga. As regras de equilíbrio de carga das portas de alta disponibilidade podem ser utilizadas para encaminhar os fragmentos ip existentes. Para mais informações, consulte a visão geral das portas de [alta disponibilidade.](load-balancer-ha-ports-overview.md)

## <a name="next-steps"></a>Passos seguintes

- Consulte [Criar um Balancer de Carga Padrão público](quickstart-load-balancer-standard-public-portal.md) para começar com a utilização de um Balancer de Carga: criar um, criar VMs com uma extensão IIS personalizada instalada e carregar o equilíbrio da aplicação web entre os VMs.
- Saiba mais sobre o Equilíbrio de [Carga Azure.](load-balancer-overview.md)
- Saiba mais sobre a utilização de Zonas de [Equilíbrio de Carga Padrão e De Disponibilidade.](load-balancer-standard-availability-zones.md)
- Saiba mais sobre sondas de [saúde.](load-balancer-custom-probe-overview.md)
- Saiba mais sobre diagnósticos de [balanceadores de carga padrão.](load-balancer-standard-diagnostics.md)
- Aprenda a utilizar o [Balancer de Carga para ligações de saída](load-balancer-outbound-connections.md).
- Conheça [as Regras de Saída](load-balancer-outbound-rules-overview.md).
- Saiba mais sobre [o Reset TCP em Idle](load-balancer-tcp-reset.md).
- Saiba mais sobre o [Balancer de Carga Standard com regras](load-balancer-ha-ports-overview.md)de equilíbrio de carga ha ports .
- Aprenda a utilizar o [Balancer de Carga com extremidades múltiplas](load-balancer-multivip-overview.md).
- Saiba mais sobre grupos de [segurança de rede.](../virtual-network/security-overview.md)
