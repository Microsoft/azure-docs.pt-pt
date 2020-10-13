---
title: Gerir a disponibilidade de VMs
description: Saiba como utilizar várias máquinas virtuais para garantir uma elevada disponibilidade para as suas aplicações em Azure
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cynthn
ms.openlocfilehash: fe89b58f71b14b211863fd46ba523e8c866764f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361794"
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>Gerir a disponibilidade de máquinas virtuais Linux

Aprenda formas de configurar e gerir várias máquinas virtuais para garantir uma elevada disponibilidade para a sua aplicação Linux em Azure. 


## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Compreender os Reinícios da VM - manutenção vs. período de indisponibilidade
Existem três cenários que podem levar a que a máquina virtual em Azure seja afetada: manutenção de hardware não planeada, tempo de paragem inesperado e manutenção planeada.

* Um **Evento de Manutenção de Hardware Não Planeada** ocorre quando a plataforma Azure prevê que o hardware ou um componente da plataforma associado a uma máquina física está prestes a falhar. Quando a plataforma prevê uma falha, emite um evento de manutenção de hardware não planeada para reduzir o impacto sobre as máquinas virtuais alojadas nesse hardware. O Azure usa a tecnologia [Live Migration](./linux/maintenance-and-updates.md) para migrar as Máquinas Virtuais do hardware falhado para uma máquina física saudável. A Migração em Direto é uma operação que visa conservar a VM e que apenas coloca a Máquina Virtual em pausa durante um curto espaço de tempo. A memória, os ficheiros abertos e as ligações de rede são mantidos, mas pode ocorrer uma redução do desempenho antes e/ou depois do evento. Nos casos em que não é possível utilizar a Migração em Direto, a VM sofre um Período de Indisponibilidade Inesperado, conforme descrito abaixo.


* **Um Tempo de Inatividade Inesperado** é quando o hardware ou a infraestrutura física da máquina virtual falham inesperadamente. Isto pode incluir falhas de rede locais, falhas de disco locais ou outras falhas no nível da cremalheira. Quando detetada, a plataforma Azure migra automaticamente (cura) a sua máquina virtual para uma máquina física saudável no mesmo datacenter. Durante o procedimento de recuperação, as máquinas virtuais sofrem um período de indisponibilidade (reinício) e, em alguns casos, a perda da unidade temporária. O SO anexado e os discos de dados são sempre preservados.

  As máquinas virtuais também podem experimentar tempo de inatividade no caso improvável de uma paragem ou desastre que afeta todo um datacenter, ou mesmo uma região inteira. Para estes cenários, o Azure oferece opções de proteção, incluindo [zonas de disponibilidade](../availability-zones/az-overview.md) e [regiões emparelhadas.](regions.md#region-pairs)

* **Os eventos de Manutenção Planeados** são atualizações periódicas feitas pela Microsoft para a plataforma Azure subjacente para melhorar a fiabilidade, desempenho e segurança globais da infraestrutura da plataforma em que as suas máquinas virtuais funcionam. A maioria destas atualizações são realizadas sem qualquer impacto nas suas Máquinas Virtuais ou Serviços de Nuvem (ver [Manutenção que não requer um reboot).](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot) Ainda que a plataforma Azure tente utilizar a Manutenção de Conservação de VMs em todas as ocasiões possíveis, há algumas situações raras em que estas atualizações precisam de um reinício da máquina virtual para aplicar as atualizações necessárias à infraestrutura subjacente. Neste caso, pode realizar a Manutenção Planeada do Azure com a operação de Reimplementação da Manutenção dando início à manutenção das respetivas VMs no intervalo de tempo adequado. Para obter mais informações, consulte [Manutenção Planeada para Máquinas Virtuais](maintenance-and-updates.md).


Para reduzir o impacto do período de indisponibilidade devido a um ou mais destes eventos, recomendamos as seguintes melhores práticas de elevada disponibilidade para as máquinas virtuais:

* Utilize zonas de Availabiilty para proteger contra falhas no datacenter
* Configurar várias máquinas virtuais num conjunto de disponibilidade para redundância
* Utilizar discos geridos para VMs num conjunto de disponibilidade
* Use eventos programados para responder proativamente aos eventos com impacto em VM
* Configurar cada camada da aplicação em conjuntos de disponibilidade separados
* Combine um equilibrador de carga com zonas de disponibilidade ou conjuntos
* Utilize zonas de disponibilidade para proteger contra falhas no nível do datacenter

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Utilize zonas de disponibilidade para proteger contra falhas no nível do datacenter

[As zonas de disponibilidade](../availability-zones/az-overview.md) expandem o nível de controlo que tem para manter a disponibilidade das aplicações e dados nos seus VMs. As Zonas de Disponibilidade são localizações físicas exclusivas numa região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, existem no mínimo três zonas distintas em todas as regiões habilititdas. A separação física das Zonas de Disponibilidade dentro de uma região protege as aplicações e os dados contra falhas do datacenter. Os serviços redundantes de zona replicam as suas aplicações e dados através das Zonas de Disponibilidade para proteger de pontos de falha únicos.

Uma Zona de Disponibilidade numa região de Azure é uma combinação de um domínio de **falha** e um **domínio de atualização.** Por exemplo, se criar três ou mais VMs em três zonas numa região de Azure, os seus VMs são efetivamente distribuídos por três domínios de avaria e três domínios de atualização. A plataforma Azure reconhece esta distribuição através de domínios de atualização para garantir que os VMs em diferentes zonas não sejam atualizados ao mesmo tempo.

Com Zonas de Disponibilidade, o Azure oferece à indústria o melhor SLA de 99,99% de VM uptime. Ao arquiteto as suas soluções para usar VMs replicados em zonas, pode proteger as suas aplicações e dados da perda de um datacenter. Se uma zona estiver comprometida, então aplicações replicadas e dados estão instantaneamente disponíveis noutra zona.

![Zonas de disponibilidade](./media/virtual-machines-common-manage-availability/three-zones-per-region.png)

Saiba mais sobre a implementação de um [Windows](./windows/create-powershell-availability-zone.md) ou [Linux](./linux/create-cli-availability-zone.md) VM numa Zona de Disponibilidade.

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Configurar várias máquinas virtuais num conjunto de disponibilidade para redundância
Os conjuntos de disponibilidade são outra configuração do datacenter para fornecer redundância e disponibilidade de VM. Esta configuração dentro de um datacenter garante que durante um evento de manutenção planeada ou não planeada, pelo menos uma máquina virtual está disponível e cumpre o Azure SLA de 99,95%. Para obter mais informações, veja [SLA para Máquinas Virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Uma máquina virtual de um único caso, num conjunto de disponibilidade por si só, deve utilizar O SSD Premium ou Disco Ultra para todos os discos do sistema operativo e discos de dados, a fim de se qualificar para o SLA para conectividade máquina virtual de pelo menos 99,9%. 
> 
> Uma máquina virtual de um único caso com um SSD Standard terá um SLA de pelo menos 99,5%, enquanto uma máquina virtual de instância única com um HDD Standard terá um SLA de pelo menos 95%.  Ver [SLA para máquinas virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/)

Cada máquina virtual no seu conjunto de disponibilidade é atribuída um **domínio de atualização** e um **domínio de avaria** pela plataforma Azure subjacente. Para um conjunto de disponibilidade especificado, cinco domínios de atualização não configuráveis pelo utilizador são atribuídos por predefinição (as implementações do Resource Manager podem então ser aumentadas para fornecer até 20 domínios de atualização), para indicar os grupos de máquinas virtuais e o hardware físico subjacente que podem ser reiniciados ao mesmo tempo. Quando são configuradas mais de cinco máquinas virtuais num conjunto de disponibilidade único, a sexta máquina virtual é colocada no mesmo domínio de atualização da primeira máquina virtual, a sétima no mesmo domínio de atualização da segunda máquina virtual, e assim sucessivamente. A ordem dos domínios de atualização que estão a ser reiniciados não pode continuar sequencialmente durante a manutenção planeada, sendo que apenas um domínio de atualização é reiniciado de cada vez. Um domínio de atualização reiniciado dispõe de 30 minutos para realizar a recuperação antes de a manutenção ser iniciada num domínio de atualização diferente.

Os domínios de falha definem o grupo de máquinas virtuais que partilham a mesma origem de energia e o mesmo comutador de rede física. Por predefinição, as máquinas virtuais configuradas no seu conjunto de disponibilidade estão separadas através de até três domínios de falha para implementações do Resource Manager (dois domínios de falha para o Clássico). Embora a colocação de máquinas virtuais num conjunto de disponibilidade não proteja a sua aplicação de falhas específicas do sistema operativo ou da aplicação, limita o impacto de potenciais falhas de hardware físico, indisponibilidade de rede ou falhas de energia.

<!--Image reference-->
   ![Desenho conceptual da configuração do domínio de falha e do domínio de atualização](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Utilizar discos geridos para VMs num conjunto de disponibilidade
Se está atualmente a utilizar VMs com discos não geridos, recomendamos vivamente que dissimulada de discos não geridos para [Linux](./linux/convert-unmanaged-to-managed-disks.md) e [Windows](./windows/convert-unmanaged-to-managed-disks.md).

Os [discos geridos](./managed-disks-overview.md) proporcionam uma melhor fiabilidade para os Conjuntos de Disponibilidade ao garantir que os discos das VMs num Conjunto de Disponibilidade estão suficientemente isolados uns dos outros a fim de evitar pontos de falha únicos. Fá-lo colocando automaticamente os discos em diferentes domínios de falha de armazenamento (clusters de armazenamento) e alinhando-os com o domínio de falha VM. Se um domínio de falha de armazenamento falhar devido a falha de hardware ou software, apenas a instância VM com discos no domínio da falha de armazenamento falha.
![Discos geridos FDs](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> O número de domínios de falhas para os conjuntos de disponibilidade geridos varia consoante a região, dois ou três por região. Pode ver o domínio de falha para cada região executando os seguintes scripts.

```azurepowershell-interactive
Get-AzComputeResourceSku | where{$_.ResourceType -eq 'availabilitySets' -and $_.Name -eq 'Aligned'}
```

```azurecli-interactive 
az vm list-skus --resource-type availabilitySets --query '[?name==`Aligned`].{Location:locationInfo[0].location, MaximumFaultDomainCount:capabilities[0].value}' -o Table
```

> [!NOTE]
> Em certas circunstâncias, dois VMs no mesmo conjunto de disponibilidade podem partilhar um domínio de falha. Pode confirmar um domínio de avaria partilhada indo para o seu conjunto de disponibilidade e verificando a coluna **'Domínio de avaria'.** Um domínio de avaria partilhada pode ser causado pela sequência de conclusão quando implementou os VMs:
> 1. Desdobre o primeiro VM.
> 1. Parar/negociar o primeiro VM.
> 1. Implante o segundo VM.
>
> Nestas circunstâncias, o disco de SO do segundo VM pode ser criado no mesmo domínio de avaria que o primeiro VM, pelo que os dois VMs estarão no mesmo domínio de avaria. Para evitar este problema, recomendamos que não pare/transloja VMs entre implementações.

Se planeia utilizar VMs com discos não geridos, siga abaixo as melhores práticas para contas de Armazenamento onde discos rígidos virtuais (VHDs) de VMs são armazenados como [bolhas](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs)de página .

1. **Manter todos os discos (SO e dados) associados a uma VM na mesma conta de armazenamento**
2. **Reveja os [limites](../storage/blobs/scalability-targets-premium-page-blobs.md) do número de discos não geridos numa conta de Armazenamento Azure** antes de adicionar mais VHDs a uma conta de armazenamento
3. **Utilize uma conta de armazenamento separada para cada VM num Conjunto de Disponibilidade.** Não partilhe Contas de armazenamento com várias VMs no mesmo Conjunto de Disponibilidade. É aceitável que os VMs em diferentes Conjuntos de Disponibilidade partilhem contas de armazenamento se as melhores práticas forem seguidas ![ por FDs de discos não geridos](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>Use eventos programados para responder proativamente aos eventos com impacto em VM

Quando subscreve [eventos agendados,](./linux/scheduled-events.md)o seu VM é notificado sobre os próximos eventos de manutenção que podem ter impacto no seu VM. Quando os eventos programados estiverem ativados, a sua máquina virtual recebe um mínimo de tempo antes da atividade de manutenção ser executada. Por exemplo, as atualizações do Host OS que podem ter impacto no seu VM são feitas em fila como eventos que especificam o impacto, bem como um momento em que a manutenção será realizada se não forem tomadas medidas. Os eventos de agenda também são feitos quando o Azure deteta uma falha de hardware iminente que pode afetar o seu VM, o que lhe permite decidir quando a cura deve ser realizada. Os clientes podem usar o evento para executar tarefas antes da manutenção, como o estado de poupança, falhando no secundário, e assim por diante. Depois de completar a sua lógica para lidar graciosamente com o evento de manutenção, pode aprovar o evento agendado pendente para permitir que a plataforma prossiga com a manutenção.


## <a name="combine-a-load-balancer-with-availability-zones-or-sets"></a>Combine um equilibrador de carga com zonas de disponibilidade ou conjuntos
Combine o Balançador de [Carga Azure](../load-balancer/load-balancer-overview.md) com uma zona de disponibilidade ou definido para obter a maior resiliência da aplicação. O Balanceador de Carga do Azure distribui tráfego entre várias máquinas virtuais. Para as nossas máquinas virtuais de camada Standard, o Balanceador de Carga do Azure está incluído. Nem todas as camadas de máquina virtual incluem o Balanceador de Carga do Azure. Para obter mais informações sobre a carga que equilibra as suas máquinas virtuais, consulte **máquinas virtuais de equilíbrio de carga** para [Linux](linux/tutorial-load-balancer.md) ou [Windows](windows/tutorial-load-balancer.md).

Se o balanceador de carga não estiver configurado para balancear tráfego em várias máquinas virtuais, então qualquer evento de manutenção planeada afeta apenas a máquina virtual que serve o tráfego, provocando uma indisponibilidade na camada da aplicação. Colocar várias máquinas virtuais da mesma camada no mesmo balanceador de carga e conjunto de disponibilidade permite que o tráfego seja servido continuamente por, pelo menos, uma instância.

Para obter um tutorial sobre como carregar o equilíbrio entre zonas de disponibilidade, consulte [Tutorial: Balance VMs de carga em zonas de disponibilidade com um Balanceador de Carga Padrão utilizando o portal Azure](../load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal.md).


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a carga equilibrando as suas máquinas virtuais, consulte [load Balanceing virtual machines](../load-balancer/load-balancer-overview.md).
