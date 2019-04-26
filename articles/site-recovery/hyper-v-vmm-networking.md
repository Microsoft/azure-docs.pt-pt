---
title: Configurar para ligar a um site secundário no local após a ativação pós-falha com o Azure Site Recovery de endereçamento IP | Documentos da Microsoft
description: Descreve como configurar o endereçamento de IP para ligar a VMs num site secundário no local após a recuperação após desastre e de ativação pós-falha com o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: c93f80ba93e84c93ddf58b46b4bbcf98c53478d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60482357"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>Configurar a ligação a um site secundário no local após a ativação pós-falha de endereçamento IP

Depois de efetuar a ativação pós-falha de VMs de Hyper-V em clouds do System Center Virtual Machine Manager (VMM) para um site secundário, tem de ser capaz de ligar a VMs de réplica. Este artigo ajuda-o a fazê-lo. 

## <a name="connection-options"></a>Opções de ligação

Após a ativação pós-falha, existem duas formas de lidar com endereçamento IP para VMs de réplica: 

- **Manter o mesmo endereço IP após a ativação pós-falha**: Neste cenário, a VM replicada tem o mesmo endereço IP da VM principal. Isso simplifica a rede relacionado com problemas após a ativação pós-falha, mas requer algum trabalho de infraestrutura.
- **Utilizar um endereço IP diferente após a ativação pós-falha**: Neste cenário a VM obtém um novo endereço IP após a ativação pós-falha. 
 

## <a name="retain-the-ip-address"></a>Manter o endereço IP

Se pretende manter os endereços IP a partir do site primário, após a ativação pós-falha para o site secundário, pode:

- Implemente uma sub-rede Stretch entre a primária e os sites secundários.
- Execute uma ativação pós-falha de sub-rede completa dos principais para site secundário. Terá de atualizar as rotas para indicar a nova localização dos endereços IP.


### <a name="deploy-a-stretched-subnet"></a>Implementar uma sub-rede Stretch

Numa configuração de Stretch, a sub-rede está disponível em simultâneo em sites primários e secundários. Numa sub-rede Stretch, quando move uma máquina e a respetiva configuração de endereço IP (camada 3) para o site secundário, a rede automaticamente encaminha o tráfego para a nova localização. 

- De uma perspectiva de (camada de ligação de dados) de camada 2, terá de equipamento de rede que pode gerir uma VLAN Stretch.
- Por alongamento a VLAN, o domínio de falha potenciais estende para ambos os sites. Isso se torna um ponto único de falha. Embora seja pouco provável, neste cenário que poderá não conseguir isolar um incidente, como um storm de difusão. 


### <a name="fail-over-a-subnet"></a>Efetuar a ativação pós-falha de uma sub-rede

Pode efetuar a ativação ao longo de toda a rede para obter os benefícios da sub-rede Stretch, sem alongamento, na verdade, ele. Nesta solução, uma sub-rede está disponível no site de origem ou destino, mas não em ambos simultaneamente.

- Para manter o espaço de endereços IP em caso de uma ativação pós-falha, por meio de programação pode fazer com que a infraestrutura de router para mover as sub-redes de um site para outro.
- Quando ocorre uma ativação pós-falha, sub-redes mover-se com as respetivas VMs associadas.
- A principal desvantagem dessa abordagem é que, em caso de falha, precisa mover toda a rede.

#### <a name="example"></a>Exemplo

Eis um exemplo de ativação pós-falha de sub-rede completa. 

- Antes da ativação pós-falha, o site primário tem aplicações em execução na sub-rede 192.168.1.0/24.
- Durante a ativação pós-falha, todas as VMs nesta sub-rede com ativação pós-falha para o site secundário e mantém os seus endereços IP. 
- Rotas entre todos os sites tenham de ser modificados para refletir o fato de que todas as VMs na sub-rede 192.168.1.0/24 agora foram movidas para o site secundário.

Os gráficos seguintes ilustram as sub-redes antes e após a ativação pós-falha.


**Antes da ativação pós-falha**

![Antes da ativação pós-falha](./media/hyper-v-vmm-networking/network-design2.png)

**Após a ativação pós-falha**

![Após a ativação pós-falha](./media/hyper-v-vmm-networking/network-design3.png)

Após a ativação pós-falha, o Site Recovery aloca um endereço IP para cada interface de rede na VM. O endereço é alocado a partir do conjunto de endereços IP estático na rede relevante, para cada instância VM.

- Se o conjunto de endereços IP no site secundário é o mesmo que o site de origem, o Site Recovery aloca o mesmo endereço IP (da VM de origem), para a VM de réplica. O endereço IP é reservado no VMM, mas ele não está definido como o endereço IP de ativação pós-falha do anfitrião de Hyper-V. O endereço IP de ativação pós-falha num anfitrião Hyper-v é definido apenas antes da ativação pós-falha.
- Se o mesmo endereço IP não estiver disponível, o Site Recovery aloca outro endereço IP disponível do conjunto.
- Se as VMs usam DHCP, a recuperação de Site não gere os endereços IP. Tem de verificar que o servidor DHCP no site secundário pode alocar endereços do mesmo intervalo de que o site de origem.

### <a name="validate-the-ip-address"></a>Validar o endereço IP

Depois de ativar a proteção para uma VM, pode utilizar após o script de exemplo para verificar o endereço atribuído à VM. Este endereço IP é definido como o endereço IP de ativação pós-falha e atribuído à VM no momento da ativação pós-falha:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="use-a-different-ip-address"></a>Utilizar um endereço IP diferente

Neste cenário, os endereços IP das VMs com ativação pós-falha são alterados. A desvantagem dessa solução é a manutenção necessária.  Entradas DNS e a cache poderão ter de ser atualizado. Isso pode resultar em tempo de inatividade, que pode ser atenuado da seguinte forma:

- Utilize valores de TTL baixas para aplicativos de intranet.
- Utilize o seguinte script num plano de recuperação do Site Recovery, para uma atualização atempada do servidor DNS. O script não é necessário se utilizar o registo de DNS dinâmico.

    ```
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>Exemplo 

Neste exemplo temos diferentes endereços IP em todos os sites primários e secundários, e há um site de terceiro que aplicações alojadas no principal ou recuperação de site pode ser acedido.

- Antes da ativação pós-falha, as aplicações são 192.168.1.0/24 sub-rede alojada no site primário.
- Após a ativação pós-falha, as aplicações são configuradas na sub-rede 172.16.1.0/24 no site secundário.
- Todos os três sites podem aceder a si.
- Após a ativação pós-falha, aplicações serão restauradas na sub-rede de recuperação.
- Neste cenário não é necessário fazer a ativação pós-falha toda a rede e sem alterações são necessárias para reconfigurar as rotas de rede ou VPN. A ativação pós-falha e algumas atualizações DNS, certifique-se de que as aplicações permanecem acessíveis.
- Se o DNS está configurado para permitir atualizações dinâmicas, em seguida, as VMs serão registram-se com o novo endereço IP, quando for iniciado após a ativação pós-falha.

**Antes da ativação pós-falha**

![Endereço IP diferente - antes da ativação pós-falha](./media/hyper-v-vmm-networking/network-design10.png)

**Após a ativação pós-falha**

![Endereço IP diferente - após a ativação pós-falha](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>Passos Seguintes

[Executar uma ativação pós-falha](hyper-v-vmm-failover-failback.md)

