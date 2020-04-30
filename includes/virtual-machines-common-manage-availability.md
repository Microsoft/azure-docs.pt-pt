---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ba21dfc900145ceeacab6c363e5de84b830282b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82109637"
---
## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Compreender os Reinícios da VM - manutenção vs. período de indisponibilidade
Existem três cenários que podem levar a que a máquina virtual em Azure seja impactada: manutenção de hardware não planeada, tempo de inatividade inesperado e manutenção planeada.

* Um **Evento de Manutenção de Hardware Não Planeada** ocorre quando a plataforma Azure prevê que o hardware ou um componente da plataforma associado a uma máquina física está prestes a falhar. Quando a plataforma prevê uma falha, emite um evento de manutenção de hardware não planeada para reduzir o impacto sobre as máquinas virtuais alojadas nesse hardware. O Azure usa a tecnologia [Live Migration](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) para migrar as Máquinas Virtuais do hardware falhado para uma máquina física saudável. A Migração em Direto é uma operação que visa conservar a VM e que apenas coloca a Máquina Virtual em pausa durante um curto espaço de tempo. A memória, os ficheiros abertos e as ligações de rede são mantidos, mas pode ocorrer uma redução do desempenho antes e/ou depois do evento. Nos casos em que não é possível utilizar a Migração em Direto, a VM sofre um Período de Indisponibilidade Inesperado, conforme descrito abaixo.


* **Um tempo de inatividade inesperado** é quando o hardware ou a infraestrutura física para a máquina virtual falha inesperadamente. Isto pode incluir falhas de rede locais, falhas no disco local ou outras falhas de nível de rack. Quando detetada, a plataforma Azure migra automaticamente (cura) a sua máquina virtual para uma máquina física saudável no mesmo centro de dados. Durante o procedimento de recuperação, as máquinas virtuais sofrem um período de indisponibilidade (reinício) e, em alguns casos, a perda da unidade temporária. O SO anexado e os discos de dados são sempre preservados.

  As máquinas virtuais também podem experimentar o tempo de inatividade no caso improvável de uma paragem ou desastre que afeta todo um datacenter, ou mesmo uma região inteira. Para estes cenários, o Azure oferece opções de proteção, incluindo [zonas de disponibilidade](../articles/availability-zones/az-overview.md) e [regiões emparelhadas.](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions)

* **Os eventos de manutenção planeados** são atualizações periódicas feitas pela Microsoft para a plataforma Azure subjacente para melhorar a fiabilidade geral, desempenho e segurança da infraestrutura da plataforma em que as suas máquinas virtuais funcionam. A maior parte destas atualizações são realizadas sem exercer qualquer impacto nas suas Máquinas Virtuais ou Serviços Cloud (consulte [Manutenção de Conservação de VMs](https://docs.microsoft.com/azure/virtual-machines/windows/preserving-maintenance)). Ainda que a plataforma Azure tente utilizar a Manutenção de Conservação de VMs em todas as ocasiões possíveis, há algumas situações raras em que estas atualizações precisam de um reinício da máquina virtual para aplicar as atualizações necessárias à infraestrutura subjacente. Neste caso, pode realizar a Manutenção Planeada do Azure com a operação de Reimplementação da Manutenção dando início à manutenção das respetivas VMs no intervalo de tempo adequado. Para obter mais informações, consulte [Manutenção Planeada para Máquinas Virtuais](https://docs.microsoft.com/azure/virtual-machines/windows/planned-maintenance/).


Para reduzir o impacto do período de indisponibilidade devido a um ou mais destes eventos, recomendamos as seguintes melhores práticas de elevada disponibilidade para as máquinas virtuais:

* [Configurar várias máquinas virtuais num conjunto de disponibilidade para redundância]
* [Utilizar discos geridos para VMs num conjunto de disponibilidade]
* [Use eventos programados para responder proativamente a eventos de impacto vm](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)
* [Configurar cada camada da aplicação em conjuntos de disponibilidade separados]
* [Combinar um Balanceador de Carga com conjuntos de disponibilidade]
* [Utilize zonas de disponibilidade para proteger contra falhas de nível de datacenter]

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Utilize zonas de disponibilidade para proteger contra falhas de nível de datacenter

[As zonas](../articles/availability-zones/az-overview.md) de disponibilidade expandem o nível de controlo que tem para manter a disponibilidade das aplicações e dados nos seus VMs. As Zonas de Disponibilidade são localizações físicas exclusivas numa região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, existem no mínimo três zonas separadas em todas as regiões habilitadas. A separação física das Zonas de Disponibilidade dentro de uma região protege aplicações e dados de falhas no datacenter. Os serviços redundantes em zonas replicam as suas aplicações e dados através das Zonas de Disponibilidade para proteger contra pontos únicos de falha.

Uma Zona de Disponibilidade numa região do Azure é uma combinação de um domínio de **falha** e um domínio de **atualização.** Por exemplo, se criar três ou mais VMs em três zonas de uma região do Azure, os seus VMs são efetivamente distribuídos por três domínios de falha e três domínios de atualização. A plataforma Azure reconhece esta distribuição através de domínios de atualização para garantir que os VMs em diferentes zonas não são atualizados ao mesmo tempo.

Com Zonas de Disponibilidade, o Azure oferece à indústria o melhor tempo de uptime De SLA de 99,99% vM. Ao arquitetar as suas soluções para utilizar VMs replicados em zonas, pode proteger as suas aplicações e dados da perda de um datacenter. Se uma zona estiver comprometida, as aplicações e os dados replicados estão instantaneamente disponíveis noutra zona.

![Zonas de disponibilidade](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Saiba mais sobre a implementação de um [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) ou [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) VM numa Zona de Disponibilidade.

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Configurar várias máquinas virtuais num conjunto de disponibilidade para redundância
Os conjuntos de disponibilidade são outra configuração do datacenter para fornecer redundância e disponibilidade vM. Esta configuração dentro de um datacenter garante que durante um evento de manutenção planeado ou não planeado, pelo menos uma máquina virtual está disponível e cumpre o Azure SLA de 99,95%. Para obter mais informações, veja [SLA para Máquinas Virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Uma única máquina virtual numa única instância definida por si mesma deve utilizar Premium SSD ou Ultra Disk para todos os discos e discos de dados do sistema operativo, de modo a se qualificar para o SLA para conectividade Máquina Virtual de pelo menos 99,9%.

A cada máquina virtual no seu conjunto de disponibilidade é atribuído um **domínio de atualização** e um **domínio de falha** pela plataforma Azure subjacente. Para um conjunto de disponibilidade especificado, cinco domínios de atualização não configuráveis pelo utilizador são atribuídos por predefinição (as implementações do Resource Manager podem então ser aumentadas para fornecer até 20 domínios de atualização), para indicar os grupos de máquinas virtuais e o hardware físico subjacente que podem ser reiniciados ao mesmo tempo. Quando são configuradas mais de cinco máquinas virtuais num conjunto de disponibilidade único, a sexta máquina virtual é colocada no mesmo domínio de atualização da primeira máquina virtual, a sétima no mesmo domínio de atualização da segunda máquina virtual, e assim sucessivamente. A ordem dos domínios de atualização que estão a ser reiniciados não pode continuar sequencialmente durante a manutenção planeada, sendo que apenas um domínio de atualização é reiniciado de cada vez. Um domínio de atualização reiniciado dispõe de 30 minutos para realizar a recuperação antes de a manutenção ser iniciada num domínio de atualização diferente.

Os domínios de falha definem o grupo de máquinas virtuais que partilham a mesma origem de energia e o mesmo comutador de rede física. Por predefinição, as máquinas virtuais configuradas no seu conjunto de disponibilidade estão separadas através de até três domínios de falha para implementações do Resource Manager (dois domínios de falha para o Clássico). Embora a colocação de máquinas virtuais num conjunto de disponibilidade não proteja a sua aplicação de falhas específicas do sistema operativo ou da aplicação, limita o impacto de potenciais falhas de hardware físico, indisponibilidade de rede ou falhas de energia.

<!--Image reference-->
   ![Desenho conceptual da configuração do domínio de falha e do domínio de atualização](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Utilizar discos geridos para VMs num conjunto de disponibilidade
Se, neste momento, utiliza VMs com discos não geridos, recomendamos vivamente que [converta as VMs no Conjunto de Disponibilidade de modo a utilizarem Discos Geridos](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md).

Os [discos geridos](../articles/virtual-machines/windows/managed-disks-overview.md) proporcionam uma melhor fiabilidade para os Conjuntos de Disponibilidade ao garantir que os discos das VMs num Conjunto de Disponibilidade estão suficientemente isolados uns dos outros a fim de evitar pontos de falha únicos. Fá-lo colocando automaticamente os discos em diferentes domínios de falha de armazenamento (clusters de armazenamento) e alinhando-os com o domínio de falha VM. Se um domínio de falha de armazenamento falhar devido a falha de hardware ou software, apenas a instância VM com discos no domínio da falha de armazenamento falha.
![DF de discos geridos](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> O número de domínios de falhas para os conjuntos de disponibilidade geridos varia consoante a região, dois ou três por região. Pode ver o domínio de avaria para cada região executando os seguintes scripts.

```azurepowershell-interactive
Get-AzComputeResourceSku | where{$_.ResourceType -eq 'availabilitySets' -and $_.Name -eq 'Aligned'}
```

```azurecli-interactive 
az vm list-skus --resource-type availabilitySets --query '[?name==`Aligned`].{Location:locationInfo[0].location, MaximumFaultDomainCount:capabilities[0].value}' -o Table
```

> [!NOTE]
> Em certas circunstâncias, 2 VMs no mesmo Conjunto de Disponibilidade podem partilhar o mesmo FaultDomain. Isto pode ser confirmado indo para o seu conjunto de disponibilidade e verificando a coluna **De domínio de falha.**
> Isto pode ser causado rumado da seguinte sequência durante a implementação dos VMs:
> - Implementar o 1º VM
> - Parar/Dealocar o 1º VM
> - Implementar o 2º VM Nestas circunstâncias, o Disco OS da 2ª VM pode ser criado no mesmo Domínio de Falha que o 1º VM, e assim o 2º VM também aterrará no mesmo FaultDomain. 
> Para evitar este problema, recomenda-se não parar/desalojar os VMs entre implementações.

Se planeia utilizar VMs com discos não geridos, siga abaixo as melhores práticas para contas de Armazenamento onde discos rígidos virtuais (VHDs) de VMs são armazenados como bolhas de [página](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs).

1. **Manter todos os discos (SO e dados) associados a uma VM na mesma conta de armazenamento**
2. **Reveja os [limites](../articles/storage/blobs/scalability-targets-premium-page-blobs.md) do número de discos não geridos numa conta** de Armazenamento Azure antes de adicionar mais VHDs a uma conta de armazenamento
3. **Utilize uma conta de armazenamento separada para cada VM num Conjunto de Disponibilidade.** Não partilhe Contas de armazenamento com várias VMs no mesmo Conjunto de Disponibilidade. É aceitável que os VMs em diferentes Conjuntos de Disponibilidade ![partilhem contas de armazenamento se forem seguidas as melhores práticas, os FDs não geridos](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>Use eventos programados para responder proativamente a eventos de impacto vm

Quando subscreve [eventos agendados,](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)o seu VM é notificado sobre os próximos eventos de manutenção que podem afetar o seu VM. Quando os eventos programados estiverem ativados, a sua máquina virtual recebe um tempo mínimo antes da atividade de manutenção ser realizada. Por exemplo, as atualizações do Os do anfitrião que podem afetar o seu VM são feitas em fila como eventos que especificam o impacto, bem como um momento em que a manutenção será realizada se não forem tomadas medidas. Os eventos de agendamento também estão em fila quando o Azure detetar uma falha de hardware iminente que pode afetar o seu VM, o que lhe permite decidir quando deve ser realizada a cicatrização. Os clientes podem usar o evento para executar tarefas antes da manutenção, como o estado de poupança, falhando no secundário, e assim por diante. Depois de completar a sua lógica para lidar graciosamente com o evento de manutenção, pode aprovar o evento agendado pendente para permitir que a plataforma prossiga com a manutenção.

## <a name="configure-each-application-tier-into-separate-availability-zones-or-availability-sets"></a>Configure cada nível de aplicação em zonas de disponibilidade separadas ou conjuntos de disponibilidade
Se as suas máquinas virtuais forem quase idênticas e servirem o mesmo propósito para a sua aplicação, recomendamos que configure uma zona de disponibilidade ou um conjunto de disponibilidade para cada nível da sua aplicação.  Se colocar dois níveis diferentes na mesma zona de disponibilidade ou conjunto, todas as máquinas virtuais no mesmo nível de aplicação podem ser reiniciadas de uma só vez. Ao configurar pelo menos duas máquinas virtuais numa zona de disponibilidade ou definidas para cada nível, garante que pelo menos uma máquina virtual em cada nível está disponível.

Por exemplo, você poderia colocar todas as máquinas virtuais na parte frontal da sua aplicação executando IIS, Apache e Nginx em uma única zona de disponibilidade ou conjunto. Certifique-se de que apenas as máquinas virtuais frontais são colocadas na mesma zona de disponibilidade ou definidas. Da mesma forma, certifique-se de que apenas as máquinas virtuais de nível de dados são colocadas na sua própria zona de disponibilidade ou configuradas, como as suas máquinas virtuais SQL Server replicadas, ou as suas máquinas virtuais MySQL.

<!--Image reference-->
   ![Camadas da aplicação](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-zones-or-sets"></a>Combine um equilibrador de carga com zonas de disponibilidade ou conjuntos
Combine o Equilíbrio de [Carga Azure](../articles/load-balancer/load-balancer-overview.md) com uma zona de disponibilidade ou definido para obter a maior resiliência de aplicação. O Balanceador de Carga do Azure distribui tráfego entre várias máquinas virtuais. Para as nossas máquinas virtuais de camada Standard, o Balanceador de Carga do Azure está incluído. Nem todas as camadas de máquina virtual incluem o Balanceador de Carga do Azure. Para mais informações sobre o balanceamento de carga de máquinas virtuais, veja [Balanceamento de Carga de máquinas virtuais](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Se o balanceador de carga não estiver configurado para balancear tráfego em várias máquinas virtuais, então qualquer evento de manutenção planeada afeta apenas a máquina virtual que serve o tráfego, provocando uma indisponibilidade na camada da aplicação. Colocar várias máquinas virtuais da mesma camada no mesmo balanceador de carga e conjunto de disponibilidade permite que o tráfego seja servido continuamente por, pelo menos, uma instância.

Para um tutorial sobre como carregar o equilíbrio em zonas de disponibilidade, consulte VMs de balanço de [carga em todas as zonas de disponibilidade utilizando o Azure CLI](../articles/load-balancer/load-balancer-standard-public-zone-redundant-cli.md).


<!-- Link references -->
[Configurar várias máquinas virtuais num conjunto de disponibilidade para redundância]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Configurar cada camada da aplicação em conjuntos de disponibilidade separados]: #configure-each-application-tier-into-separate-availability-zones-or-availability-sets
[Combinar um Balanceador de Carga com conjuntos de disponibilidade]: #combine-a-load-balancer-with-availability-zones-or-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Utilizar discos geridos para VMs num conjunto de disponibilidade]: #use-managed-disks-for-vms-in-an-availability-set
[Utilize zonas de disponibilidade para proteger contra falhas de nível de datacenter]: #use-availability-zones-to-protect-from-datacenter-level-failures
