---
title: Configurar o endereçamento IP para se conectar a um site secundário local após o failover com Azure Site Recovery
description: Descreve como configurar o endereçamento IP para se conectar a VMs em um site secundário local após a recuperação de desastres e o failover com o Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: f158c6b71bb53d6b683577401e625e24808eb7eb
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813693"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>Configurar o endereçamento IP para se conectar a um site secundário local após o failover

Depois de realizar o failover de VMs do Hyper-V em nuvens System Center Virtual Machine Manager (VMM) para um site secundário, você precisará ser capaz de se conectar às VMs de réplica. Este artigo ajuda você a fazer isso. 

## <a name="connection-options"></a>Opções de conexão

Após o failover, há duas maneiras de lidar com endereçamento IP para VMs de réplica: 

- **Manter o mesmo endereço IP após o failover**: Nesse cenário, a VM replicada tem o mesmo endereço IP que a VM primária. Isso simplifica os problemas relacionados à rede após o failover, mas requer algum trabalho de infraestrutura.
- **Usar um endereço IP diferente após o failover**: Nesse cenário, a VM Obtém um novo endereço IP após o failover. 
 

## <a name="retain-the-ip-address"></a>Manter o endereço IP

Se desejar manter os endereços IP do site primário, após o failover para o site secundário, você poderá:

- Implante uma sub-rede ampliada entre os sites primário e secundário.
- Execute um failover de sub-rede completo do site primário para o secundário. Você precisa atualizar as rotas para indicar o novo local dos endereços IP.


### <a name="deploy-a-stretched-subnet"></a>Implantar uma sub-rede ampliada

Em uma configuração ampliada, a sub-rede está disponível simultaneamente nos sites primário e secundário. Em uma sub-rede ampliada, quando você move um computador e sua configuração de endereço IP (camada 3) para o site secundário, a rede roteia automaticamente o tráfego para o novo local. 

- De uma perspectiva de camada 2 (camada de link de dados), você precisa de um equipamento de rede que possa gerenciar uma VLAN ampliada.
- Ao estender a VLAN, o domínio de falha potencial se estende a ambos os sites. Isso se torna um ponto único de falha. Embora seja improvável, nesse cenário, talvez você não consiga isolar um incidente, como um Storm de difusão. 


### <a name="fail-over-a-subnet"></a>Fazer failover de uma sub-rede

Você pode fazer failover de toda a sub-rede para obter os benefícios da sub-rede ampliada, sem realmente estendê-la. Nesta solução, uma sub-rede está disponível no site de origem ou de destino, mas não em ambos simultaneamente.

- Para manter o espaço de endereço IP no caso de um failover, você pode organizar de forma programática a infraestrutura do roteador para mover sub-redes de um site para outro.
- Quando ocorre um failover, as sub-redes se movem com suas VMs associadas.
- A principal desvantagem dessa abordagem é que, em caso de falha, você precisa mover toda a sub-rede.

#### <a name="example"></a>Exemplo

Aqui está um exemplo de failover de sub-rede completo. 

- Antes do failover, o site primário tem aplicativos em execução na sub-rede 192.168.1.0/24.
- Durante o failover, todas as VMs nessa sub-rede são transferidas para o site secundário e mantêm seus endereços IP. 
- As rotas entre todos os sites precisam ser modificadas para refletir o fato de que todas as VMs na sub-rede 192.168.1.0/24 agora foram movidas para o site secundário.

Os gráficos a seguir ilustram as sub-redes antes e após o failover.


**Antes do failover**

![Antes do failover](./media/hyper-v-vmm-networking/network-design2.png)

**Após o failover**

![Após o failover](./media/hyper-v-vmm-networking/network-design3.png)

Após o failover, Site Recovery aloca um endereço IP para cada interface de rede na VM. O endereço é alocado do pool de endereços IP estáticos na rede relevante, para cada instância de VM.

- Se o pool de endereços IP no site secundário for o mesmo que no site de origem, Site Recovery alocará o mesmo endereço IP (da VM de origem) para a VM de réplica. O endereço IP é reservado no VMM, mas não é definido como o endereço IP de failover no host Hyper-V. O endereço IP de failover em um host Hyper-v é definido logo antes do failover.
- Se o mesmo endereço IP não estiver disponível, Site Recovery alocará outro endereço IP disponível do pool.
- Se as VMs usarem DHCP, o Site Recovery não gerenciará os endereços IP. Você precisa verificar se o servidor DHCP no site secundário pode alocar endereços do mesmo intervalo que o site de origem.

### <a name="validate-the-ip-address"></a>Validar o endereço IP

Depois de habilitar a proteção para uma VM, você pode usar o seguinte script de exemplo para verificar o endereço atribuído à VM. Esse endereço IP é definido como o endereço IP de failover e atribuído à VM no momento do failover:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="use-a-different-ip-address"></a>Usar um endereço IP diferente

Nesse cenário, os endereços IP das VMs que fazem failover são alterados. A desvantagem dessa solução é a manutenção necessária.  As entradas de cache e DNS podem precisar ser atualizadas. Isso pode resultar em tempo de inatividade, que pode ser mitigado da seguinte maneira:

- Use valores TTL baixos para aplicativos de intranet.
- Use o script a seguir em um plano de recuperação Site Recovery, para uma atualização oportuna do servidor DNS. Você não precisará do script se usar o registro de DNS dinâmico.

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

Neste exemplo, temos endereços IP diferentes em sites primários e secundários, e há um terceiro site do qual os aplicativos hospedados no site primário ou de recuperação podem ser acessados.

- Antes do failover, os aplicativos são hospedados na sub-rede 192.168.1.0/24 no site primário.
- Após o failover, os aplicativos são configurados na sub-rede 172.16.1.0/24 no site secundário.
- Todos os três sites podem acessar um ao outro.
- Após o failover, os aplicativos serão restaurados na sub-rede de recuperação.
- Nesse cenário, não há necessidade de fazer failover de toda a sub-rede e nenhuma alteração é necessária para reconfigurar as rotas VPN ou de rede. O failover e algumas atualizações de DNS garantem que os aplicativos permaneçam acessíveis.
- Se o DNS estiver configurado para permitir atualizações dinâmicas, as VMs se registrarão usando o novo endereço IP quando iniciarem após o failover.

**Antes do failover**

![Endereço IP diferente-antes do failover](./media/hyper-v-vmm-networking/network-design10.png)

**Após o failover**

![Endereço IP diferente-após o failover](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>Passos Seguintes

[Executar um failover](hyper-v-vmm-failover-failback.md)

