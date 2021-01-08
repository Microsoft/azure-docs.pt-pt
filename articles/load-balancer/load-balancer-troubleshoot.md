---
title: Resolução de problemas comuns Azure Load Balancer
description: Saiba como resolver problemas comuns com o Azure Load Balancer.
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
ms.openlocfilehash: cddaf1bde84d7e60eb59bd4c58c65fa889e06ae3
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028816"
---
# <a name="troubleshoot-azure-load-balancer"></a>Resolver problemas do Balanceador de Carga do Azure

Esta página fornece informações de resolução de problemas para perguntas básicas e padrão comum do Balançador de Carga. Para obter mais informações sobre o Balanceador de Carga Padrão, consulte [a visão geral do Balancer de Carga Padrão](load-balancer-standard-diagnostics.md).

Quando a conectividade do Balanceador de Carga não está disponível, os sintomas mais comuns são os seguintes:

- Os VMs por trás do Balanceador de Carga não estão a responder a sondas de saúde 
- Os VMs por trás do Balanceador de Carga não estão a responder ao tráfego na porta configurada

Quando os clientes externos para os VMs de backend passarem pelo equilibrador de carga, o endereço IP dos clientes será utilizado para a comunicação. Certifique-se de que o endereço IP dos clientes é adicionado à lista de autorizações da NSG.

## <a name="no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>Sem conectividade de saída dos Balanceadores de Carga Internos Padrão (ILB)

**Validação e resolução**

Os ILBs standard são **seguros por defeito**. Os ILBs básicos permitiram a ligação à internet através de um endereço IP público *oculto.* Isto não é recomendado para cargas de trabalho de produção, uma vez que o endereço IP não é estático nem bloqueado através de NSGs que possui. Se mudou recentemente de um ILB Básico para um ILB padrão, deve criar um IP público explicitamente através da configuração [outbound,](egress-only.md) que bloqueia o IP através de NSGs. Também pode utilizar um [NAT Gateway](../virtual-network/nat-overview.md) na sua sub-rede.

## <a name="cant-change-backend-port-for-existing-lb-rule-of-a-load-balancer-that-has-virtual-machine-scale-set-deployed-in-the-backend-pool"></a>Não é possível alterar a porta de backend para a regra LB existente de um equilibrador de carga que tenha a balança de máquina virtual implantada na piscina de backend.

### <a name="cause-the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-virtual-machine-scale-set"></a>Causa: A porta de backend não pode ser modificada para uma regra de equilíbrio de carga que é usada por uma sonda de saúde para o balanceador de carga referenciado por conjunto de balanças de máquinas virtuais

**Resolução** Para alterar a porta, pode remover a sonda de saúde atualizando o conjunto de escala de máquina virtual, atualizar a porta e, em seguida, configurar novamente a sonda de saúde.

## <a name="small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>O pequeno tráfego continua a passar pelo equilibrador de carga depois de retirar os VMs do pool de backend do equilibrador de carga.

### <a name="cause-vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>Causa: Os VM removidos da piscina de backend não devem mais receber tráfego. A pequena quantidade de tráfego de rede pode estar relacionada com armazenamento, DNS e outras funções dentro de Azure.

Para verificar, pode efetuar um rastreio de rede. O FQDN utilizado para as suas contas de armazenamento blob está listado nas propriedades de cada conta de armazenamento.  A partir de uma máquina virtual dentro da sua subscrição Azure, pode realizar nslookup para determinar o IP Azure atribuído a essa conta de armazenamento.

## <a name="additional-network-captures"></a>Capturas adicionais de rede

Se decidir abrir um caso de apoio, recolhê as seguintes informações para uma resolução mais rápida. Escolha um único VM de backend para realizar os seguintes testes:

- Utilize o ping do PS de um dos VMs de backend dentro do VNet para testar a resposta da porta da sonda (exemplo: ping 10.0.0.0.4:3389) e resultados recorde. 
- Se não for recebida qualquer resposta nestes testes de ping, faça um rastreio de Netsh simultâneo no VM de backend e no VM de teste VNet enquanto corre PsPing, então pare o rastreio de Netsh.

## <a name="load-balancer-in-failed-state"></a>Balanceador de carga em estado falhado

**Resolução**

- Assim que identificar o recurso que está em estado de falha, vá ao [Azure Resource Explorer](https://resources.azure.com/) e identifique o recurso neste estado.
- Atualize o toggle no canto superior direito para Ler/Escrever.
- Clique em Editar para obter o recurso em estado falhado.
- Clique em PUT seguido por GET para garantir que o estado de provisionamento foi atualizado para Sucesso.
- Em seguida, pode prosseguir com outras ações, uma vez que o recurso está fora do estado falhado.

## <a name="next-steps"></a>Passos seguintes

Se os passos anteriores não resolverem o problema, abra um [bilhete de apoio](https://azure.microsoft.com/support/options/).
