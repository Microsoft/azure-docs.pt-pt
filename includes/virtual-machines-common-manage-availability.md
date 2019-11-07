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
ms.openlocfilehash: 7c884d3c7102fc47f6efad86d9fe3704afd0edcf
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73591202"
---
## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Compreender os Reinícios da VM - manutenção vs. período de indisponibilidade
Há três cenários que podem levar à máquina virtual no Azure ser afetada: manutenção de hardware não planejada, tempo de inatividade inesperado e manutenção planejada.

* Um **Evento de Manutenção de Hardware Não Planeada** ocorre quando a plataforma Azure prevê que o hardware ou um componente da plataforma associado a uma máquina física está prestes a falhar. Quando a plataforma prevê uma falha, emite um evento de manutenção de hardware não planeada para reduzir o impacto sobre as máquinas virtuais alojadas nesse hardware. O Azure usa a tecnologia [migração ao vivo](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) para migrar as máquinas virtuais do hardware com falha para um computador físico íntegro. A Migração em Direto é uma operação que visa conservar a VM e que apenas coloca a Máquina Virtual em pausa durante um curto espaço de tempo. A memória, os ficheiros abertos e as ligações de rede são mantidos, mas pode ocorrer uma redução do desempenho antes e/ou depois do evento. Nos casos em que não é possível utilizar a Migração em Direto, a VM sofre um Período de Indisponibilidade Inesperado, conforme descrito abaixo.


* **Um tempo de inatividade inesperado** é quando o hardware ou a infraestrutura física da máquina virtual falha inesperadamente. Isso pode incluir falhas de rede local, falhas no disco local ou outras falhas no nível do rack. Quando detectada, a plataforma do Azure migra automaticamente (repara) sua máquina virtual para um computador físico íntegro no mesmo datacenter. Durante o procedimento de recuperação, as máquinas virtuais sofrem um período de indisponibilidade (reinício) e, em alguns casos, a perda da unidade temporária. O SO anexado e os discos de dados são sempre preservados.

  As máquinas virtuais também podem ter tempo de inatividade no caso improvável de uma interrupção ou desastre que afete um datacenter inteiro ou até mesmo uma região inteira. Para esses cenários, o Azure fornece opções de proteção, incluindo [zonas de disponibilidade](../articles/availability-zones/az-overview.md) e [regiões emparelhadas](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

* Os **eventos de Manutenção Planeada** são atualizações periódicas levadas a cabo pela Microsoft na plataforma Azure subjacente para melhorar a fiabilidade, o desempenho e a segurança gerais da infraestrutura da plataforma em que as suas máquinas virtuais são executadas. A maior parte destas atualizações são realizadas sem exercer qualquer impacto nas suas Máquinas Virtuais ou Serviços Cloud (consulte [Manutenção de Conservação de VMs](https://docs.microsoft.com/azure/virtual-machines/windows/preserving-maintenance)). Ainda que a plataforma Azure tente utilizar a Manutenção de Conservação de VMs em todas as ocasiões possíveis, há algumas situações raras em que estas atualizações precisam de um reinício da máquina virtual para aplicar as atualizações necessárias à infraestrutura subjacente. Neste caso, pode realizar a Manutenção Planeada do Azure com a operação de Reimplementação da Manutenção dando início à manutenção das respetivas VMs no intervalo de tempo adequado. Para obter mais informações, consulte [Manutenção Planeada para Máquinas Virtuais](https://docs.microsoft.com/azure/virtual-machines/windows/planned-maintenance/).


Para reduzir o impacto do período de indisponibilidade devido a um ou mais destes eventos, recomendamos as seguintes melhores práticas de elevada disponibilidade para as máquinas virtuais:

* [Configurar várias máquinas virtuais num conjunto de disponibilidade para redundância]
* [Utilizar discos geridos para VMs num conjunto de disponibilidade]
* [Usar eventos agendados para responder proativamente a eventos que afetam a VM](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)
* [Configurar cada camada da aplicação em conjuntos de disponibilidade separados]
* [Combinar um Balanceador de Carga com conjuntos de disponibilidade]
* [Usar zonas de disponibilidade para proteger contra falhas no nível do datacenter]

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Usar zonas de disponibilidade para proteger contra falhas no nível do datacenter

As [zonas de disponibilidade](../articles/availability-zones/az-overview.md) expandem o nível de controle que você precisa para manter a disponibilidade dos aplicativos e dos dados em suas VMs. As Zonas de Disponibilidade são localizações físicas exclusivas numa região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. A separação física de Zonas de Disponibilidade dentro de uma região protege aplicativos e dados de falhas do datacenter. Os serviços com redundância de zona replicam seus aplicativos e dados em Zonas de Disponibilidade para proteger contra pontos únicos de falha.

Uma zona de disponibilidade em uma região do Azure é uma combinação de um **domínio de falha** e um **domínio de atualização**. Por exemplo, se você criar três ou mais VMs em três zonas em uma região do Azure, suas VMs serão efetivamente distribuídas entre três domínios de falha e três domínios de atualização. A plataforma Azure reconhece essa distribuição entre domínios de atualização para garantir que as VMs em diferentes zonas não sejam atualizadas ao mesmo tempo.

Com o Zonas de Disponibilidade, o Azure oferece um SLA de tempo de atividade de VM melhor do setor 99,99%. Ao arquitetar suas soluções para usar VMs replicadas em zonas, você pode proteger seus aplicativos e dados contra a perda de um datacenter. Se uma zona for comprometida, os aplicativos e dados replicados estarão instantaneamente disponíveis em outra zona.

![Zonas de disponibilidade](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Saiba mais sobre como implantar uma VM do [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) ou [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) em uma zona de disponibilidade.

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Configurar várias máquinas virtuais num conjunto de disponibilidade para redundância
Os conjuntos de disponibilidade são outra configuração de datacenter para fornecer redundância e disponibilidade de VM. Essa configuração em um datacenter garante que, durante um evento de manutenção planejada ou não planejada, pelo menos uma máquina virtual esteja disponível e atenda ao SLA de 99,95% do Azure. Para obter mais informações, veja [SLA para Máquinas Virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Evite deixar isolada uma máquina virtual de instância única num conjunto de disponibilidade. As VMs nesta configuração não se qualificam para uma garantia de SLA e enfrentam tempo de inatividade durante eventos de manutenção planejada do Azure, exceto quando uma única VM está usando o [SSDs Premium do Azure](../articles/virtual-machines/windows/disks-types.md#premium-ssd). Para VMs únicas usando SSDs Premium, o SLA do Azure se aplica.

A cada máquina virtual no seu conjunto de disponibilidade é atribuído um **domínio de atualização** e um **domínio de falha** pela plataforma Azure subjacente. Para um conjunto de disponibilidade especificado, cinco domínios de atualização não configuráveis pelo utilizador são atribuídos por predefinição (as implementações do Resource Manager podem então ser aumentadas para fornecer até 20 domínios de atualização), para indicar os grupos de máquinas virtuais e o hardware físico subjacente que podem ser reiniciados ao mesmo tempo. Quando são configuradas mais de cinco máquinas virtuais num conjunto de disponibilidade único, a sexta máquina virtual é colocada no mesmo domínio de atualização da primeira máquina virtual, a sétima no mesmo domínio de atualização da segunda máquina virtual, e assim sucessivamente. A ordem dos domínios de atualização que estão a ser reiniciados não pode continuar sequencialmente durante a manutenção planeada, sendo que apenas um domínio de atualização é reiniciado de cada vez. Um domínio de atualização reiniciado dispõe de 30 minutos para realizar a recuperação antes de a manutenção ser iniciada num domínio de atualização diferente.

Os domínios de falha definem o grupo de máquinas virtuais que partilham a mesma origem de energia e o mesmo comutador de rede física. Por predefinição, as máquinas virtuais configuradas no seu conjunto de disponibilidade estão separadas através de até três domínios de falha para implementações do Resource Manager (dois domínios de falha para o Clássico). Embora a colocação de máquinas virtuais num conjunto de disponibilidade não proteja a sua aplicação de falhas específicas do sistema operativo ou da aplicação, limita o impacto de potenciais falhas de hardware físico, indisponibilidade de rede ou falhas de energia.

<!--Image reference-->
   ![desenho conceitual do domínio de atualização e da configuração de domínio de falha](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Utilizar discos geridos para VMs num conjunto de disponibilidade
Se, neste momento, utiliza VMs com discos não geridos, recomendamos vivamente que [converta as VMs no Conjunto de Disponibilidade de modo a utilizarem Discos Geridos](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md).

Os [discos geridos](../articles/virtual-machines/windows/managed-disks-overview.md) proporcionam uma melhor fiabilidade para os Conjuntos de Disponibilidade ao garantir que os discos das VMs num Conjunto de Disponibilidade estão suficientemente isolados uns dos outros a fim de evitar pontos de falha únicos. Ele faz isso colocando automaticamente os discos em diferentes domínios de falha de armazenamento (clusters de armazenamento) e alinhando-os com o domínio de falha da VM. Se um domínio de falha de armazenamento falhar devido a uma falha de hardware ou de software, somente a instância de VM com discos no domínio de falha de armazenamento falhará.
![FDs Managed disks](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> O número de domínios de falhas para os conjuntos de disponibilidade geridos varia consoante a região, dois ou três por região. A tabela seguinte mostra o número por região

[!INCLUDE [managed-disks-common-fault-domain-region-list](managed-disks-common-fault-domain-region-list.md)]

> Observação: em determinadas circunstâncias, pode acontecer que 2 VMs que fazem parte do mesmo Availabilityset compartilhem o mesmo FaultDomain. Isso pode ser confirmado entrando em seu Availabilityset e marcando a coluna "domínio de falha".
> Esse comportamento pode ser observado quando a seguinte sequência ocorreu durante a implantação das VMs:
> - Implantar a 1ª VM
> - Parar/desalocar a 1ª VM
> - Implantar a 2ª VM nessas circunstâncias, o disco do sistema operacional da 2ª VM pode ser criado no mesmo domínio de falha que a primeira VM e, portanto, a 2ª VM também irá para o mesmo FaultDomain. 
> Para evitar esse problema, é recomendável não parar/desalocar a VM entre suas implantações.

Se você planeja usar VMs com discos não gerenciados, siga as práticas recomendadas abaixo para contas de armazenamento em que os VHDs (discos rígidos virtuais) das VMs são armazenados como [blobs de páginas](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs).

1. **Manter todos os discos (SO e dados) associados a uma VM na mesma conta de armazenamento**
2. **Rever os [limites](../articles/storage/common/storage-scalability-targets.md) do número de discos não geridos numa Conta de armazenamento**  antes de adicionar mais VHDs a uma conta de armazenamento
3. **Utilize uma conta de armazenamento separada para cada VM num Conjunto de Disponibilidade.** Não partilhe Contas de armazenamento com várias VMs no mesmo Conjunto de Disponibilidade. É aceitável que as VMs em diferentes conjuntos de disponibilidade compartilhem contas de armazenamento, se as práticas recomendadas acima forem seguidas ![discos não gerenciados FDs](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>Usar eventos agendados para responder proativamente a eventos que afetam a VM

Quando você se inscreve em [eventos agendados](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events), sua VM é notificada sobre eventos de manutenção futuros que podem afetar sua VM. Quando os eventos agendados são habilitados, sua máquina virtual recebe um período mínimo de tempo antes que a atividade de manutenção seja executada. Por exemplo, as atualizações do sistema operacional host que podem afetar sua VM são enfileiradas como eventos que especificam o impacto, bem como uma hora em que a manutenção será executada se nenhuma ação for executada. Os eventos de agendamento também são enfileirados quando o Azure detecta uma falha de hardware iminente que pode afetar sua VM, o que permite que você decida quando o reparo deve ser executado. Os clientes podem usar o evento para executar tarefas antes da manutenção, como salvar o estado, fazer failover para o secundário e assim por diante. Depois de concluir sua lógica para manipular o evento de manutenção normalmente, você pode aprovar o evento de agendamento pendente para permitir que a plataforma continue com a manutenção.

## <a name="configure-each-application-tier-into-separate-availability-zones-or-availability-sets"></a>Configurar cada camada de aplicativo em zonas de disponibilidade ou conjuntos de disponibilidade separados
Se suas máquinas virtuais forem praticamente idênticas e atenderem à mesma finalidade para seu aplicativo, recomendamos que você configure uma zona de disponibilidade ou um conjunto de disponibilidade para cada camada do seu aplicativo.  Se você posicionar duas camadas diferentes na mesma zona ou conjunto de disponibilidade, todas as máquinas virtuais na mesma camada de aplicativo poderão ser reinicializadas ao mesmo tempo. Ao configurar pelo menos duas máquinas virtuais em uma zona de disponibilidade ou definidas para cada camada, você garante que pelo menos uma máquina virtual em cada camada esteja disponível.

Por exemplo, você pode colocar todas as máquinas virtuais no front-end de seu aplicativo executando IIS, Apache e Nginx em uma única zona de disponibilidade ou conjunto. Certifique-se de que apenas as máquinas virtuais de front-end sejam colocadas na mesma zona ou conjunto de disponibilidade. Da mesma forma, verifique se apenas as máquinas virtuais da camada de dados são colocadas em sua própria zona de disponibilidade ou conjunto, como suas máquinas virtuais SQL Server replicadas ou suas máquinas virtuais do MySQL.

<!--Image reference-->
   ![camadas de aplicativo](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-zones-or-sets"></a>Combinar um balanceador de carga com zonas de disponibilidade ou conjuntos
Combine o [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) com uma zona de disponibilidade ou defina para obter a maior resiliência do aplicativo. O Balanceador de Carga do Azure distribui tráfego entre várias máquinas virtuais. Para as nossas máquinas virtuais de camada Standard, o Balanceador de Carga do Azure está incluído. Nem todas as camadas de máquina virtual incluem o Balanceador de Carga do Azure. Para mais informações sobre o balanceamento de carga de máquinas virtuais, veja [Balanceamento de Carga de máquinas virtuais](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Se o balanceador de carga não estiver configurado para balancear tráfego em várias máquinas virtuais, então qualquer evento de manutenção planeada afeta apenas a máquina virtual que serve o tráfego, provocando uma indisponibilidade na camada da aplicação. Colocar várias máquinas virtuais da mesma camada no mesmo balanceador de carga e conjunto de disponibilidade permite que o tráfego seja servido continuamente por, pelo menos, uma instância.

Para obter um tutorial sobre como balancear a carga entre zonas de disponibilidade, consulte [balancear carga de VMs em todas as zonas de disponibilidade usando o CLI do Azure](../articles/load-balancer/load-balancer-standard-public-zone-redundant-cli.md).


<!-- Link references -->
[Configurar várias máquinas virtuais num conjunto de disponibilidade para redundância]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Configurar cada camada da aplicação em conjuntos de disponibilidade separados]: #configure-each-application-tier-into-separate-availability-zones-or-availability-sets
[Combinar um Balanceador de Carga com conjuntos de disponibilidade]: #combine-a-load-balancer-with-availability-zones-or-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Utilizar discos geridos para VMs num conjunto de disponibilidade]: #use-managed-disks-for-vms-in-an-availability-set
[Usar zonas de disponibilidade para proteger contra falhas no nível do datacenter]: #use-availability-zones-to-protect-from-datacenter-level-failures
