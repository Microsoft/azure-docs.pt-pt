---
title: Resolver problemas do Balanceador de Carga do Azure
description: Saiba como resolver problemas conhecidos com o Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: bfe2b21a86f2ce4b4630ba69cde87796fd367f4b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98029203"
---
# <a name="troubleshoot-azure-load-balancer-backend-traffic-responses"></a>Resolução de problemas Azure Load Balancer backend respostas de tráfego

Esta página fornece informações de resolução de problemas para perguntas do Balancer de Carga Azure.

## <a name="vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Os VMs por trás do Balanceador de Carga não estão a responder ao tráfego na porta de dados configurada

Se um pool de backend VM for listado como saudável e responder às sondas de saúde, mas ainda não participar no Equilíbrio de Carga, ou não estiver a responder ao tráfego de dados, pode ser devido a qualquer uma das seguintes razões:
* Load Balancer Backend pool VM não está a ouvir na porta de dados
* O grupo de segurança da rede está a bloquear a porta na piscina de backend balancer de carga VM  
* Aceder ao Equilibrador de Carga a partir do mesmo VM e NIC
* Aceder ao frontend do Balançador de Carga de Internet a partir do pool de backend balancer de carga participante VM

## <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Causa 1: Load Balancer pool VM não está a ouvir na porta de dados

Se um VM não responder ao tráfego de dados, pode ser porque ou a porta-alvo não está aberta no VM participante, ou, o VM não está a ouvir nessa porta. 

**Validação e resolução**

1. Faça login no VM de backend. 
2. Abra uma pronta de comando e execute o seguinte comando para validar há uma aplicação ouvindo na porta de dados:  
            netstat -um 
3. Se o porto não estiver listado com Estado "LISTENING", configuure a porta de escuta adequada 
4. Se a porta estiver marcada como Escuta, verifique a aplicação-alvo na porta para eventuais problemas.

## <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Causa 2: O grupo de segurança da rede está a bloquear a porta na piscina de backend balancer de carga VM  

Se um ou mais grupos de segurança de rede configurados na sub-rede ou no VM estiverem a bloquear o IP ou a porta de origem, então o VM não consegue responder.

Para o balançador de carga pública, o endereço IP dos clientes da Internet será utilizado para a comunicação entre os clientes e os VMs de backend de reequilibradores de carga. Certifique-se de que o endereço IP dos clientes é permitido no grupo de segurança de rede da VM.

1. Listar os grupos de segurança da rede configurados no VM backend. Para obter mais informações, consulte [Gerir grupos de segurança de rede](../virtual-network/manage-network-security-group.md)
1. Da lista de grupos de segurança da rede, verifique se:
    - o tráfego de entrada ou saída na porta de dados tem interferência. 
    - a **Negar Todas as** regras do grupo de segurança da rede sobre o NIC do VM ou a sub-rede que tem uma prioridade maior que a regra padrão que permite as sondas e o tráfego do Balancer de Carga (os grupos de segurança da rede devem permitir o Load Balancer IP de 168.63.129.16, ou seja, a porta da sonda)
1. Se alguma das regras estiver a bloquear o tráfego, remova e reconfigure essas regras para permitir o tráfego de dados.  
1. Teste se o VM já começou a responder às sondas de saúde.

## <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Causa 3: Aceder ao Balanceador de Carga a partir da mesma interface VM e Rede 

Se a sua aplicação hospedada no VM de backend de um Balancer de Carga estiver a tentar aceder a outra aplicação hospedada no mesmo VM de backend sobre a mesma Interface de Rede, é um cenário não suportado e falhará. 

**Resolução** Pode resolver esta questão através de um dos seguintes métodos:
* Configurar vMs separados de backend pool por aplicação. 
* Configure a aplicação em VMs NIC duplos para que cada aplicação estivesse a utilizar a sua própria interface de Rede e endereço IP. 

## <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Causa 4: Aceder ao frontend interno do balançador de carga a partir da piscina de backend balancer participante VM

Se um Balancer de Carga interno estiver configurado dentro de um VNet, e um dos VMs de backend do participante estiver a tentar aceder ao frontend interno do Balancer de Carga, podem ocorrer falhas quando o fluxo é mapeado para o VM originário. Este cenário não é suportado.

**Resolução** Existem várias formas de desbloquear este cenário, incluindo o uso de um representante. Avaliar o Gateway de Aplicações ou outros proxies de terceiros (por exemplo, nginx ou haproxy). Para mais informações sobre o Gateway de Aplicações, consulte [a visão geral do Gateway de aplicações](../application-gateway/overview.md)

**Detalhes** Os Balançadores de Carga Interna não traduzem ligações originadas de saída para a extremidade frontal de um Balanceador de Carga interno porque ambos estão no espaço de endereço IP privado. Os Balançadores de Carga Pública fornecem ligações de saída de [endereços](load-balancer-outbound-connections.md) IP privados dentro da rede virtual para endereços IP públicos. Para os equilibradores de carga internos, esta abordagem evita a exaustão potencial da porta SNAT dentro de um espaço de endereço IP interno único, onde a tradução não é necessária.

Um efeito colateral é que se um fluxo de saída de um VM na piscina traseira tenta um fluxo para a extremidade frontal do Balancer de Carga interno na sua piscina _e_ é mapeado de volta para si mesmo, as duas pernas do fluxo não correspondem. Como não combinam, o fluxo falha. O fluxo é bem sucedido se o fluxo não mapear de volta para o mesmo VM na piscina traseira que criou o fluxo para a extremidade dianteira.

Quando o fluxo volta para si mesmo, o fluxo de saída parece originar-se do VM para a extremidade frontal e o fluxo de entrada correspondente parece originar-se do VM para si mesmo. Do ponto de vista do sistema operativo convidado, as partes de entrada e saída do mesmo fluxo não correspondem dentro da máquina virtual. A pilha de TCP não reconhecerá estas metades do mesmo fluxo que fazer parte do mesmo fluxo. A fonte e o destino não coincidem. Quando o fluxo mapeia para qualquer outro VM na piscina traseira, as metades do fluxo correspondem e o VM pode responder ao fluxo.

O sintoma para este cenário é intervalos intermitentes de ligação quando o fluxo volta ao mesmo backend que originou o fluxo. As soluções comuns incluem a inserção de uma camada de procuração por trás do balanceador de carga interno e a utilização de regras de estilo Direct Server Return (DSR). Para obter mais informações, consulte [Multiple Frontends for Azure Load Balancer](load-balancer-multivip-overview.md).

Pode combinar um Balancer de Carga interno com qualquer procuração de terceiros ou utilizar o Gateway de [Aplicação](../application-gateway/overview.md) interno para cenários de procuração com HTTP/HTTPS. Embora possa utilizar um Balancer de Carga público para mitigar esta questão, o cenário resultante é propenso à [exaustão do SNAT](load-balancer-outbound-connections.md). Evite esta segunda abordagem, a menos que seja cuidadosamente gerida.

## <a name="next-steps"></a>Passos seguintes

Se os passos anteriores não resolverem o problema, abra um [bilhete de apoio](https://azure.microsoft.com/support/options/).