---
title: Configurar endereço IP após falha para um site secundário com recuperação do site Azure
description: Descreve como configurar o endereço IP para a ligação aos VMs num local secundário após recuperação de desastres e falha com a Recuperação do Local de Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 43942c20a353ff69383f3e721679e4c95ab9d230
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87495948"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>Configurar endereço IP para ligar a um site secundário no local após falha

Depois de ter falhado sobre as nuvens De Hiper-V VMs no System Center Virtual Machine Manager (VMM) para um site secundário, precisa de ser capaz de se ligar aos VMs de réplica. Este artigo ajuda-o a fazer isto. 

## <a name="connection-options"></a>Opções de conexão

Após o failover, existem algumas formas de lidar com o endereço IP para réplicas de VMs: 

- **Conservar o mesmo endereço IP após falha:** Neste cenário, o VM replicado tem o mesmo endereço IP que o VM primário. Isto simplifica as questões relacionadas com a rede após o fracasso, mas requer alguns trabalhos de infraestrutura.
- **Utilize um endereço IP diferente após a falha**: Neste cenário, o VM obtém um novo endereço IP após falha. 
 

## <a name="retain-the-ip-address"></a>Reter o endereço IP

Se pretender reter os endereços IP do local primário, depois de ter falhado no local secundário, pode:

- Desloque uma sub-rede estendida entre os locais primários e secundários.
- Execute uma falha total da sub-rede do local primário para o secundário. É necessário atualizar as rotas para indicar a nova localização dos endereços IP.


### <a name="deploy-a-stretched-subnet"></a>Implementar uma sub-rede estendida

Numa configuração estendida, a sub-rede está disponível simultaneamente nos locais primários e secundários. Numa sub-rede estendida, quando move uma máquina e a sua configuração de endereço IP (Camada 3) para o site secundário, a rede encaminha automaticamente o tráfego para a nova localização. 

- Do ponto de vista da Camada 2 (camada de ligação de dados), precisa de equipamento de networking que possa gerir um VLAN esticado.
- Ao esticar o VLAN, o domínio potencial de avaria estende-se a ambos os locais. Isto torna-se um único ponto de falha. Embora improvável, em tal cenário, você pode não ser capaz de isolar um incidente como uma tempestade de transmissão. 


### <a name="fail-over-a-subnet"></a>Falha sobre uma sub-rede

Pode falhar em toda a sub-rede para obter os benefícios da sub-rede estendida, sem realmente esticá-la. Nesta solução, uma sub-rede está disponível na fonte ou no local alvo, mas não em ambos simultaneamente.

- Para manter o espaço de endereço IP em caso de falha, pode organizar programáticamente a infraestrutura do router para mover sub-redes de um site para outro.
- Quando ocorre uma falha, as sub-redes movem-se com os seus VMs associados.
- A principal desvantagem desta abordagem é que, em caso de falha, tem que mover toda a sub-rede.

#### <a name="example"></a>Exemplo

Aqui está um exemplo de falha total da sub-rede. 

- Antes do failover, o site primário tem aplicações em execução na sub-rede 192.168.1.0/24.
- Durante o failover, todos os VMs desta sub-rede são falhados no site secundário e conservam os seus endereços IP. 
- As rotas entre todos os sítios devem ser modificadas para refletir o facto de todos os VMs na sub-rede 192.168.1.0/24 terem agora sido transferidos para o local secundário.

Os gráficos que se seguem ilustram as sub-redes antes e depois do failover.


**Antes do failover**

![Diagrama mostrando as sub-redes antes do failover.](./media/hyper-v-vmm-networking/network-design2.png)

**Depois do failover**

![Diagrama mostrando as sub-redes após a falha.](./media/hyper-v-vmm-networking/network-design3.png)

Após o failover, a Recuperação do Site atribui um endereço IP para cada interface de rede no VM. O endereço é atribuído a partir do conjunto de endereços IP estático na rede relevante, para cada instância VM.

- Se o conjunto de endereços IP no site secundário for o mesmo que no site de origem, a Recuperação do Site atribui o mesmo endereço IP (da origem VM), à réplica VM. O endereço IP está reservado em VMM, mas não está definido como o endereço IP de falha no anfitrião Hyper-V. O endereço IP de falha num anfitrião Hyper-v é definido pouco antes do failover.
- Se o mesmo endereço IP não estiver disponível, a Recuperação do Site atribui outro endereço IP disponível a partir da piscina.
- Se os VMs utilizarem DHCP, a Recuperação do Site não gere os endereços IP. É necessário verificar se o servidor DHCP no site secundário pode alocar endereços a partir da mesma gama que o site de origem.

### <a name="validate-the-ip-address"></a>Validar o endereço IP

Depois de ativar a proteção de um VM, pode utilizar o seguinte script de amostra para verificar o endereço atribuído ao VM. Este endereço IP é definido como o endereço IP de failover, e atribuído ao VM no momento do failover:

```powershell
$vm = Get-SCVirtualMachine -Name <VM_NAME>
$na = $vm[0].VirtualNetworkAdapters>
$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
$ip.address
```

## <a name="use-a-different-ip-address"></a>Use um endereço IP diferente

Neste cenário, os endereços IP dos VMs que falham são alterados. A desvantagem desta solução é a manutenção necessária.  As entradas de DNS e cache podem ter de ser atualizadas. Isto pode resultar em tempo de inatividade, que pode ser atenuado da seguinte forma:

- Utilize valores TTL baixos para aplicações intranet.
- Utilize o seguinte script num plano de recuperação do site, para uma atualização atemea do servidor DNS. Não precisa do script se utilizar o registo de DNS dinâmico.

    ```powershell
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

Neste exemplo, temos diferentes endereços IP em sites primários e secundários, e há um terceiro site a partir do qual as aplicações hospedadas no site principal ou de recuperação podem ser acedidas.

- Antes do failover, as aplicações são hospedadas na sub-rede 192.168.1.0/24 no site principal.
- Após o failover, as aplicações são configuradas na sub-rede 172.16.1.0/24 no site secundário.
- Os três sites podem aceder uns aos outros.
- Após o failover, as aplicações serão restauradas na sub-rede de recuperação.
- Neste cenário não há necessidade de falhar em toda a sub-rede, e não são necessárias alterações para reconfigurar as rotas VPN ou rede. O failover, e algumas atualizações dns, garantem que as aplicações permanecem acessíveis.
- Se o DNS estiver configurado para permitir atualizações dinâmicas, os VMs registar-se-ão usando o novo endereço IP, quando começarem após o failover.

**Antes do failover**

![Diagrama mostrando diferentes endereços IP antes do failover.](./media/hyper-v-vmm-networking/network-design10.png)

**Depois do failover**

![Diagrama mostrando diferentes endereços IP após a falha.](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>Passos seguintes

[Executar uma ativação pós-falha](hyper-v-vmm-failover-failback.md)

