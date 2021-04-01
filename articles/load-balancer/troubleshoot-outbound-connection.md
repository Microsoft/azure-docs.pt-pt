---
title: Ligações de saída de resolução de problemas no Equilibrador de Carga Azure
description: Resoluções para problemas comuns com conectividade de saída através do Balançador de Carga Azure.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 05/7/2020
ms.author: errobin
ms.openlocfilehash: 516576f4e005cc9fe2303945ecb1a13489908a5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94696358"
---
# <a name="troubleshooting-outbound-connections-failures"></a><a name="obconnecttsg"></a> Falhas nas ligações de saída de resolução de problemas

Este artigo destina-se a fornecer resoluções para problemas comuns que podem ocorrer com ligações de saída de um Balançador de Carga Azure. A maioria dos problemas com a conectividade de saída que os clientes experimentam devem-se à exaustão da porta SNAT e aos intervalos de ligação que levam a pacotes abandonados. Este artigo fornece medidas para atenuar cada uma destas questões.

## <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a> Gestão da exaustão portuária do SNAT (PAT)
[As portas efémeras utilizadas](load-balancer-outbound-connections.md) para [pat](load-balancer-outbound-connections.md) são um recurso exaustivo, conforme descrito em [VM autónomo sem endereço IP público](load-balancer-outbound-connections.md) e [VM equilibrado em carga sem endereço IP público](load-balancer-outbound-connections.md). Pode monitorizar a sua utilização de portas efémeras e comparar com a sua alocação atual para determinar o risco de ou confirmar a exaustão do SNAT utilizando [este](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) guia.

Se sabe que está a iniciar muitas ligações TCP ou UDP de saída para o mesmo endereço IP de destino e porta, e observa ligações de saída falhadas ou é aconselhado por suporte a que está a esgotar as portas SNAT [(portas efémeras](load-balancer-outbound-connections.md#preallocatedports) pré-locadas utilizadas pela [PAT),](load-balancer-outbound-connections.md)tem várias opções gerais de mitigação. Reveja estas opções e decida o que está disponível e o melhor para o seu cenário. É possível que um ou mais possam ajudar a gerir este cenário.

Se tiver dificuldade em compreender o comportamento de ligação de saída, pode utilizar estatísticas de pilha de IP (netstat). Ou pode ser útil observar comportamentos de conexão usando capturas de pacotes. Pode realizar estas capturas de pacotes no so convidado da sua instância ou utilizar [o Network Watcher para a captura de pacotes.](../network-watcher/network-watcher-packet-capture-manage-portal.md) 

## <a name="manually-allocate-snat-ports-to-maximize-snat-ports-per-vm"></a><a name ="manualsnat"></a>Aloque manualmente portas SNAT para maximizar portas SNAT por VM
Tal como definido nas [portas pré-locadas,](load-balancer-outbound-connections.md#preallocatedports)o balançador de carga irá automaticamente alocar portas com base no número de VMs no backend. Por padrão, isto é feito de forma conservadora para garantir a escalabilidade. Se souber o número máximo de VMs que terá no backend, pode atribuir manualmente portas SNAT em cada regra de saída. Por exemplo, se souber que terá um máximo de 10 VMs, pode alocar 6.400 portas SNAT por VM em vez das 1.024 predefinidos. 

## <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>Modificar a aplicação para reutilizar ligações 
Pode reduzir a procura de portas efémeras que são utilizadas para o SNAT reutilizando ligações na sua aplicação. A reutilização de ligação é especialmente relevante para protocolos como HTTP/1.1, onde a reutilização de ligação é o padrão. E outros protocolos que utilizam HTTP como seu transporte (por exemplo, REST) podem beneficiar por sua vez. 

A reutilização é sempre melhor do que as ligações individuais e atómicas de TCP para cada pedido. A reutilização resulta em transações TCP mais eficazes e muito eficientes.

## <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>Modifique a aplicação para utilizar o pooling de ligação
Pode utilizar um sistema de agrupamento de ligação na sua aplicação, onde os pedidos são distribuídos internamente por um conjunto fixo de ligações (cada reutilização sempre que possível). Este regime limita o número de portos efémeros em uso e cria um ambiente mais previsível. Este regime também pode aumentar o rendimento dos pedidos, permitindo múltiplas operações simultâneas quando uma única ligação está a bloquear a resposta de uma operação.  

O agrupamento de ligação pode já existir dentro da estrutura que está a utilizar para desenvolver a sua aplicação ou as definições de configuração para a sua aplicação. Pode combinar o pooling de ligação com a reutilização de ligação. Os seus múltiplos pedidos consomem então um número fixo e previsível de portas para o mesmo endereço IP de destino e porto. Os pedidos beneficiam igualmente de uma utilização eficiente das transações TCP, reduzindo a latência e a utilização dos recursos. As transações de UDP também podem beneficiar, uma vez que a gestão do número de fluxos de UDP pode, por sua vez, evitar condições de escape e gerir a utilização do porto SNAT.

## <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>Modifique a aplicação para usar lógica de repetição menos agressiva
Quando [as portas efémeras pré-locadas utilizadas](load-balancer-outbound-connections.md#preallocatedports) para [o PAT](load-balancer-outbound-connections.md) estão esgotadas ou ocorrem falhas de aplicação, as retrações de força agressiva ou bruta sem decomposição e lógica de recuo fazem com que ocorram ou persistam. Pode reduzir a procura de portas efémeras utilizando uma lógica de reluto menos agressiva. 

As portas efémeras têm um tempo limite de 4 minutos (não ajustável). Se as retraçãos forem demasiado agressivas, a exaustão não tem oportunidade de se limpar por si própria. Portanto, considerando como- e com que frequência - as suas transações de retréis de aplicação é uma parte crítica do projeto.

## <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>Atribuir um IP público a cada VM
A atribuição de um endereço IP público altera o seu cenário de [IP público para um VM](load-balancer-outbound-connections.md). Todas as portas efémeras do IP público que são utilizadas para cada VM estão disponíveis para o VM. (Ao contrário dos cenários em que as portas efémeras de um IP público são partilhadas com todos os VMs associados à respetiva piscina de backend.) Existem compensações a ter em conta, tais como o custo adicional dos endereços IP públicos e o impacto potencial da filtragem de um grande número de endereços IP individuais.

>[!NOTE] 
>Esta opção não está disponível para funções de trabalhador web.

## <a name="use-multiple-frontends"></a><a name="multifesnat"></a>Use vários frontends
Ao utilizar o Balanceador de Carga Padrão público, atribui [vários endereços IP frontend para ligações de saída](load-balancer-outbound-connections.md) e [multiplica o número de portas SNAT disponíveis](load-balancer-outbound-connections.md#preallocatedports).  Crie uma configuração IP frontal, regra e piscina de backend para ativar a programação do SNAT para o IP público do frontend.  A regra não precisa de funcionar e uma sonda de saúde não precisa de ter sucesso.  Se utilizar vários frontends para entrada também (em vez de apenas para saída), deve utilizar bem as sondas de saúde personalizadas para garantir a fiabilidade.

>[!NOTE]
>Na maioria dos casos, a exaustão dos portos SNAT é um sinal de mau design.  Certifique-se de que compreende por que razão está a esgotar as portas antes de utilizar mais frontends para adicionar portas SNAT.  Pode estar a mascarar um problema que pode levar ao fracasso mais tarde.

## <a name="scale-out"></a><a name="scaleout"></a>Escala para fora
[As portas pré-locadas](load-balancer-outbound-connections.md#preallocatedports) são atribuídas com base no tamanho da piscina de backend e agrupadas em camadas para minimizar a perturbação quando algumas portas têm de ser realojadas para acomodar o próximo nível de tamanho da piscina de backend maior.  Pode ter a opção de aumentar a utilização da porta SNAT para um dado frontend, escalando a sua piscina de backend para o tamanho máximo para um determinado nível.  Tendo em conta que a atribuição por porta padrão é necessária para que a aplicação se escalone eficientemente sem risco de exaustão SNAT.

Por exemplo, duas máquinas virtuais no pool de backend teriam 1024 portas SNAT disponíveis por configuração IP, permitindo um total de 2048 portas SNAT para a implantação.  Se a implantação fosse aumentada para 50 máquinas virtuais, embora o número de portas pré-locadas permaneça constante por máquina virtual, um total de 51.200 (50 x 1024) portas SNAT podem ser utilizadas pela implantação.  Se desejar aumentar a sua implantação, verifique o número de [portas pré-locadas](load-balancer-outbound-connections.md#preallocatedports) por nível para se certificar de que molda a sua escala ao máximo para o respetivo nível.  No exemplo anterior, se tivesse optado por escalar para 51 em vez de 50 instâncias, iria progredir para o nível seguinte e acabaria com menos portas SNAT por VM, bem como no total.

Se você escalar para o próximo nível maior do tamanho da piscina de backend, há potencial para algumas das suas ligações de saída para o tempo de saída se as portas atribuídas tiverem que ser realojadas.  Se estiver a utilizar apenas algumas das suas portas SNAT, a escala através do próximo tamanho maior da piscina é inconsequente.  Metade das portas existentes serão realojadas cada vez que se deslocar para o próximo nível da piscina de backend.  Se não quiser que isto ocorra, tem de moldar a sua implantação para o tamanho do nível.  Ou certifique-se de que a sua aplicação pode detetar e tentar novamente se necessário.  Os keepalivos TCP podem ajudar a detetar quando as portas SNAT já não funcionam devido à reafectação.

## <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>Use keepalives para redefinir o tempo limite de saída
As ligações de saída têm um tempo limite de 4 minutos. Este tempo limite é ajustável através [das regras de saída.](outbound-rules.md) Também pode utilizar o transporte (por exemplo, keepalives TCP) ou os keepalives da camada de aplicação para refrescar um fluxo inativo e repor este tempo de inatividade, se necessário.  

Ao utilizar as keepalives TCP, é suficiente para os ativar de um lado da ligação. Por exemplo, basta ativar-lhes no lado do servidor apenas para repor o temporizador inativo do fluxo e não é necessário para ambos os lados iniciarem as keepalives TCP iniciadas.  Existem conceitos semelhantes para a camada de aplicação, incluindo configurações de servidor de clientes de base de dados.  Verifique o lado do servidor para saber quais as opções existentes para manter a aplicação específica.

## <a name="next-steps"></a>Passos Seguintes
Procuramos sempre melhorar a experiência dos nossos clientes. Se estiver a ter problemas com conectividade de saída que não estejam listados ou resolvidos por este artigo, envie feedback através do GitHub através da parte inferior desta página e abordaremos o seu feedback o mais rapidamente possível.