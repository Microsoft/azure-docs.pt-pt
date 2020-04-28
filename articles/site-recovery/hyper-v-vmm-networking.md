---
title: Configurar endereço IP após falha num local secundário com recuperação do site Azure
description: Descreve como configurar endereços IP para ligar a VMs em um local secundário no local após recuperação de desastres e falha com a Recuperação do Site Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: a61f7ff69e648262eb721eb61a98b09dbbee924c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73961433"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>Configurar endereço IP para ligar a um local secundário no local após falha

Depois de falhar sobre os VMs Hiper-V nas nuvens de Gestor de Máquinas Virtuais do System Center (VMM) para um local secundário, precisa de ser capaz de se ligar aos VMs réplicas. Este artigo ajuda-o a fazer isto. 

## <a name="connection-options"></a>Opções de ligação

Após a falha, existem algumas formas de lidar com endereços IP para vMs réplicas: 

- **Mantenha o mesmo endereço IP após a falha**: Neste cenário, o VM replicado tem o mesmo endereço IP que o VM primário. Isto simplifica as questões relacionadas com a rede após o fracasso, mas requer alguns trabalhos de infraestrutura.
- **Utilize um endereço IP diferente após a falha**: Neste cenário, o VM recebe um novo endereço IP após a falha. 
 

## <a name="retain-the-ip-address"></a>Reter o endereço IP

Se pretender reter os endereços IP do local principal, depois de falhar no local secundário, pode:

- Implementar uma sub-rede estendida entre os locais primários e secundários.
- Execute uma falha de sub-rede completa do local primário para o secundário. É necessário atualizar as rotas para indicar a nova localização dos endereços IP.


### <a name="deploy-a-stretched-subnet"></a>Implementar uma subrede estendida

Numa configuração esticada, a sub-rede está disponível simultaneamente nos locais primários e secundários. Numa sub-rede estendida, quando se desloca uma máquina e a sua configuração de endereço IP (Camada 3) para o local secundário, a rede encaminha automaticamente o tráfego para o novo local. 

- Do ponto de vista da Camada 2 (camada de ligação de dados), é necessário um equipamento de networking que possa gerir um VLAN esticado.
- Ao esticar o VLAN, o domínio de falha potencial estende-se a ambos os locais. Isto torna-se um único ponto de fracasso. Embora improvável, num cenário destes, talvez não seja capaz de isolar um incidente como uma tempestade de transmissão. 


### <a name="fail-over-a-subnet"></a>Falhar sobre uma sub-rede

Pode falhar em toda a subnet para obter os benefícios da sub-rede estendida, sem realmente esticá-la. Nesta solução, uma sub-rede está disponível na fonte ou local-alvo, mas não em ambos simultaneamente.

- Para manter o espaço de endereço IP em caso de falha, pode programar programáticamente a infraestrutura do router para mover subredes de um site para outro.
- Quando ocorre uma falha, as subredes movem-se com os seus VMs associados.
- A principal desvantagem desta abordagem é que, em caso de falha, tem de mover toda a subnet.

#### <a name="example"></a>Exemplo

Aqui está um exemplo de falha completa da sub-rede. 

- Antes do failover, o local principal tem aplicações em execução na subnet192.168.1.0/24.
- Durante a failover, todos os VMs desta sub-rede são falhados no site secundário e mantêm os seus endereços IP. 
- As rotas entre todos os locais devem ser modificadas para refletir o facto de que todos os VMs da subneta 192.168.1.0/24 já se deslocaram para o local secundário.

Os gráficos seguintes ilustram as subredes antes e depois da falha.


**Antes do fracasso**

![Antes do fracasso](./media/hyper-v-vmm-networking/network-design2.png)

**Depois do fracasso**

![Depois do fracasso](./media/hyper-v-vmm-networking/network-design3.png)

Após a falha, a Recuperação do Site atribui um endereço IP para cada interface de rede no VM. O endereço é atribuído a partir do conjunto de endereços IP estático sático na rede relevante, para cada instância VM.

- Se o conjunto de endereços IP no local secundário for o mesmo que no site de origem, a Recuperação do Site atribui o mesmo endereço IP (da fonte VM), à réplica VM. O endereço IP está reservado em VMM, mas não é definido como o endereço IP failover no hospedeiro Hyper-V. O endereço IP failover em um hospedeiro Hyper-v é definido pouco antes da falha.
- Se o mesmo endereço IP não estiver disponível, a Recuperação do Site atribui outro endereço IP disponível da piscina.
- Se os VMs utilizarem dHCP, a Recuperação do Site não gere os endereços IP. Tem de verificar se o servidor DHCP no site secundário pode atribuir endereços da mesma gama que o site de origem.

### <a name="validate-the-ip-address"></a>Validar o endereço IP

Depois de ativar a proteção de um VM, pode utilizar o seguinte script de amostra para verificar o endereço atribuído ao VM. Este endereço IP é definido como o endereço IP failover, e atribuído ao VM no momento da falha:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="use-a-different-ip-address"></a>Use um endereço IP diferente

Neste cenário, os endereços IP dos VMs que falham são alterados. A desvantagem desta solução é a manutenção necessária.  As entradas de DNS e cache podem ter de ser atualizadas. Isto pode resultar em tempo de inatividade, que pode ser atenuado da seguinte forma:

- Utilize valores TTL baixos para aplicações intranet.
- Utilize o seguinte script num plano de recuperação de recuperação do site, para uma atualização oportuna do servidor DNS. Não precisa do guião se utilizar o registo dNS dinâmico.

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

Neste exemplo temos diferentes endereços IP em sites primários e secundários, e há um terceiro site a partir do qual as aplicações hospedadas no local principal ou de recuperação podem ser acedidas.

- Antes do failover, as aplicações são hospedadas subnet 192.168.1.0/24 no site principal.
- Após o failover, as aplicações são configuradas na subnet172.16.1.0/24 no site secundário.
- Os três sites podem aceder uns aos outros.
- Após a falha, as aplicações serão restauradas na subnet de recuperação.
- Neste cenário não há necessidade de falhar em toda a subnet, e não são necessárias alterações para reconfigurar as rotas VPN ou rede. O failover, e algumas atualizações dNS, garantem que as aplicações permanecem acessíveis.
- Se o DNS estiver configurado para permitir atualizações dinâmicas, os VMs registar-se-ão utilizando o novo endereço IP, quando começarem após a falha.

**Antes do fracasso**

![Endereço IP diferente - antes de failover](./media/hyper-v-vmm-networking/network-design10.png)

**Depois do fracasso**

![Endereço IP diferente - após falha](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>Passos seguintes

[Executar uma ativação pós-falha](hyper-v-vmm-failover-failback.md)

