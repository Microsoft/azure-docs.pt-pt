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
ms.openlocfilehash: f57c2cacca9bb3e4526ec6261b8aa0ff6c18448a
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546712"
---
## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Compreender os Reinícios da VM - manutenção vs. período de indisponibilidade
Existem três cenários que podem conduzir a máquina virtual no Azure a ser afetado: manutenção de hardware não planeada, o período de indisponibilidade inesperado e a manutenção planeada.

* Um **Evento de Manutenção de Hardware Não Planeada** ocorre quando a plataforma Azure prevê que o hardware ou um componente da plataforma associado a uma máquina física está prestes a falhar. Quando a plataforma prevê uma falha, emite um evento de manutenção de hardware não planeada para reduzir o impacto sobre as máquinas virtuais alojadas nesse hardware. O Azure utiliza [migração em direto](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) tecnologia para migrar as máquinas virtuais do hardware com falhas para uma máquina física em bom estado. A Migração em Direto é uma operação que visa conservar a VM e que apenas coloca a Máquina Virtual em pausa durante um curto espaço de tempo. A memória, os ficheiros abertos e as ligações de rede são mantidos, mas pode ocorrer uma redução do desempenho antes e/ou depois do evento. Nos casos em que não é possível utilizar a Migração em Direto, a VM sofre um Período de Indisponibilidade Inesperado, conforme descrito abaixo.


* **Um período de indisponibilidade inesperado** é quando o hardware ou a infraestrutura física para a máquina virtual está a falhar inesperadamente. Isto pode incluir falhas de rede local, falhas de disco local ou outras falhas estruturais. Quando detectado, migra automaticamente a plataforma do Azure (recuperação) a máquina virtual para uma máquina física em bom estado no mesmo datacenter. Durante o procedimento de recuperação, as máquinas virtuais sofrem um período de indisponibilidade (reinício) e, em alguns casos, a perda da unidade temporária. O SO anexado e os discos de dados são sempre preservados. 

  Máquinas virtuais também podem sofrer períodos de inatividade na improvável eventualidade de uma indisponibilidade ou desastre que afete todo o datacenter ou até mesmo uma região inteira. Nestes cenários, o Azure oferece opções de proteção incluindo [zonas de disponibilidade](../articles/availability-zones/az-overview.md) e [regiões emparelhadas](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

* Os **eventos de Manutenção Planeada** são atualizações periódicas levadas a cabo pela Microsoft na plataforma Azure subjacente para melhorar a fiabilidade, o desempenho e a segurança gerais da infraestrutura da plataforma em que as suas máquinas virtuais são executadas. A maior parte destas atualizações são realizadas sem exercer qualquer impacto nas suas Máquinas Virtuais ou Serviços Cloud (consulte [Manutenção de Conservação de VMs](https://docs.microsoft.com/azure/virtual-machines/windows/preserving-maintenance)). Ainda que a plataforma Azure tente utilizar a Manutenção de Conservação de VMs em todas as ocasiões possíveis, há algumas situações raras em que estas atualizações precisam de um reinício da máquina virtual para aplicar as atualizações necessárias à infraestrutura subjacente. Neste caso, pode realizar a Manutenção Planeada do Azure com a operação de Reimplementação da Manutenção dando início à manutenção das respetivas VMs no intervalo de tempo adequado. Para obter mais informações, consulte [Manutenção Planeada para Máquinas Virtuais](https://docs.microsoft.com/azure/virtual-machines/windows/planned-maintenance/).


Para reduzir o impacto do período de indisponibilidade devido a um ou mais destes eventos, recomendamos as seguintes melhores práticas de elevada disponibilidade para as máquinas virtuais:

* [Configurar várias máquinas virtuais num conjunto de disponibilidade para redundância]
* [Utilizar discos geridos para VMs num conjunto de disponibilidade]
* [Usar eventos agendados proativamente a resposta para afetar os eventos VM](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)
* [Configurar cada camada da aplicação em conjuntos de disponibilidade separados]
* [Combinar um Balanceador de Carga com conjuntos de disponibilidade]
* [Utilizar zonas de disponibilidade para proteger de falhas de nível do datacenter]

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Configurar várias máquinas virtuais num conjunto de disponibilidade para redundância
Para fornecer redundância à aplicação, é recomendável agrupar duas ou mais máquinas virtuais num conjunto de disponibilidade. Esta configuração num Data Center assegura que durante a um evento de manutenção planeada ou, pelo menos uma máquina virtual está disponível e que cumpre 99,95% do SLA do Azure. Para obter mais informações, veja [SLA para Máquinas Virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Evite deixar isolada uma máquina virtual de instância única num conjunto de disponibilidade. As VMs nesta configuração não se qualificar para uma garantia de SLA e enfrentam o período de indisponibilidade durante eventos de manutenção planeada do Azure, exceto quando está a utilizar uma única VM [premium do Azure SSDs](../articles/virtual-machines/windows/disks-types.md#premium-ssd). Para VMs únicas que utilizam o SSD premium, aplica-se o SLA do Azure.

A cada máquina virtual no seu conjunto de disponibilidade é atribuído um **domínio de atualização** e um **domínio de falha** pela plataforma Azure subjacente. Para um conjunto de disponibilidade especificado, cinco domínios de atualização não configuráveis pelo utilizador são atribuídos por predefinição (as implementações do Resource Manager podem então ser aumentadas para fornecer até 20 domínios de atualização), para indicar os grupos de máquinas virtuais e o hardware físico subjacente que podem ser reiniciados ao mesmo tempo. Quando são configuradas mais de cinco máquinas virtuais num conjunto de disponibilidade único, a sexta máquina virtual é colocada no mesmo domínio de atualização da primeira máquina virtual, a sétima no mesmo domínio de atualização da segunda máquina virtual, e assim sucessivamente. A ordem dos domínios de atualização que estão a ser reiniciados não pode continuar sequencialmente durante a manutenção planeada, sendo que apenas um domínio de atualização é reiniciado de cada vez. Um domínio de atualização reiniciado dispõe de 30 minutos para realizar a recuperação antes de a manutenção ser iniciada num domínio de atualização diferente.

Os domínios de falha definem o grupo de máquinas virtuais que partilham a mesma origem de energia e o mesmo comutador de rede física. Por predefinição, as máquinas virtuais configuradas no seu conjunto de disponibilidade estão separadas através de até três domínios de falha para implementações do Resource Manager (dois domínios de falha para o Clássico). Embora a colocação de máquinas virtuais num conjunto de disponibilidade não proteja a sua aplicação de falhas específicas do sistema operativo ou da aplicação, limita o impacto de potenciais falhas de hardware físico, indisponibilidade de rede ou falhas de energia.

<!--Image reference-->
   ![Desenho conceptual da configuração de domínio de falha e do domínio de atualização](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Utilizar discos geridos para VMs num conjunto de disponibilidade
Se, neste momento, utiliza VMs com discos não geridos, recomendamos vivamente que [converta as VMs no Conjunto de Disponibilidade de modo a utilizarem Discos Geridos](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md).

Os [discos geridos](../articles/virtual-machines/windows/managed-disks-overview.md) proporcionam uma melhor fiabilidade para os Conjuntos de Disponibilidade ao garantir que os discos das VMs num Conjunto de Disponibilidade estão suficientemente isolados uns dos outros a fim de evitar pontos de falha únicos. Ele faz isso, os discos são colocados automaticamente em domínios de falha de armazenamento diferente (clusters de armazenamento) e alinhá-los com o domínio de falhas da VM. Se a um domínio de falha de armazenamento falhar devido uma falha de hardware ou software, apenas a instância VM com discos em que o domínio de falhas de armazenamento falha.
![Domínios de falha de discos geridos](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> O número de domínios de falhas para os conjuntos de disponibilidade geridos varia consoante a região, dois ou três por região. A tabela seguinte mostra o número por região

[!INCLUDE [managed-disks-common-fault-domain-region-list](managed-disks-common-fault-domain-region-list.md)]

Se planeja usar VMs com discos não geridos, siga melhores práticas para contas de armazenamento onde os discos rígidos virtuais (VHDs) das VMs são armazenados como abaixo [blobs de páginas](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs).

1. **Manter todos os discos (SO e dados) associados a uma VM na mesma conta de armazenamento**
2. **Rever os [limites](../articles/storage/common/storage-scalability-targets.md) do número de discos não geridos numa Conta de armazenamento**  antes de adicionar mais VHDs a uma conta de armazenamento
3. **Utilize uma conta de armazenamento separada para cada VM num Conjunto de Disponibilidade.** Não partilhe Contas de armazenamento com várias VMs no mesmo Conjunto de Disponibilidade. É aceitável que as VMs em diferentes conjuntos de disponibilidade partilhem contas de armazenamento se melhores práticas anteriores são seguidos ![domínios de falha de discos não geridos](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>Usar eventos agendados para reagir de forma proativa para afetar os eventos VM

Quando subscreve [eventos agendados](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events), a VM é notificada sobre eventos de manutenção futura, que podem afetar a sua VM. Quando os eventos agendados estão ativados, sua máquina virtual é atribuída uma quantidade mínima de tempo antes que seja efetuada a atividade de manutenção. Por exemplo, as atualizações de SO de Host que podem afetar a sua VM são enfileiradas como eventos que especificam o impacto, bem como um tempo em que a manutenção será executada se foi efetuada nenhuma ação. Agenda de eventos também são colocados quando o Azure Deteta a falha de hardware iminente que pode afetar a sua VM, o que lhe permite decidir quando a recuperação deve ser efetuada. Os clientes podem usar o evento para efetuar tarefas antes da manutenção, bem como a gravação de estado, efetuar a ativação pós-falha para o secundário e assim por diante. Depois de concluir sua lógica para simplesmente manipulando o evento de manutenção, pode aprovar o evento agendado pendente para permitir que a plataforma continuar com a manutenção.

## <a name="configure-each-application-tier-into-separate-availability-sets"></a>Configurar cada camada da aplicação em conjuntos de disponibilidade separados
Se as máquinas virtuais forem todas praticamente idênticas e servirem o mesmo fim para a sua aplicação, recomendamos que configure um conjunto de disponibilidade para cada camada da aplicação.  Se colocar duas camadas diferentes no mesmo conjunto de disponibilidade, todas as máquinas virtuais da mesma camada de aplicação podem ser reiniciadas ao mesmo tempo. Ao configurar, pelo menos, duas máquinas virtuais num conjunto de disponibilidade para cada camada, garante que, pelo menos, uma máquina virtual em cada camada está disponível.

Por exemplo, poderia colocar todas as máquinas virtuais no front-end da sua aplicação com o IIS, Apache e Nginx num único conjunto de disponibilidade. Certifique-se de que apenas as máquinas virtuais front-end são colocadas no mesmo conjunto de disponibilidade. Da mesma forma, certifique-se de que apenas as máquinas virtuais de camadas de dados são colocadas no seu próprio conjunto de disponibilidade, como as máquinas virtuais replicadas do SQL Server ou as máquinas virtuais MySQL.

<!--Image reference-->
   ![Camadas da aplicação](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-sets"></a>Combinar um balanceador de carga com conjuntos de disponibilidade
Combine o [Balanceador de Carga do Azure](../articles/load-balancer/load-balancer-overview.md) com um conjunto de disponibilidade para obter a máxima resiliência da aplicação. O Balanceador de Carga do Azure distribui tráfego entre várias máquinas virtuais. Para as nossas máquinas virtuais de camada Standard, o Balanceador de Carga do Azure está incluído. Nem todas as camadas de máquina virtual incluem o Balanceador de Carga do Azure. Para mais informações sobre o balanceamento de carga de máquinas virtuais, veja [Balanceamento de Carga de máquinas virtuais](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Se o balanceador de carga não estiver configurado para balancear tráfego em várias máquinas virtuais, então qualquer evento de manutenção planeada afeta apenas a máquina virtual que serve o tráfego, provocando uma indisponibilidade na camada da aplicação. Colocar várias máquinas virtuais da mesma camada no mesmo balanceador de carga e conjunto de disponibilidade permite que o tráfego seja servido continuamente por, pelo menos, uma instância.

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Utilizar zonas de disponibilidade para proteger de falhas de nível do datacenter

[As zonas de disponibilidade](../articles/availability-zones/az-overview.md), define uma alternativa à disponibilidade, expanda o nível de controle que precisa manter a disponibilidade das aplicações e dados nas suas VMs. Uma Zona de Disponibilidade é uma zona separada fisicamente numa região do Azure. Existem três zonas de disponibilidade por região do Azure suportada. Cada zona de disponibilidade tem um distintos de energia origem, rede e arrefecimento e é logicamente separados de outras zonas de disponibilidade na região do Azure. Criando suas soluções para utilizar as VMs replicadas em zonas, pode proteger as aplicações e dados contra a perda de um datacenter. Se uma zona for comprometida, em seguida, replicados de aplicações e dados são imediatamente disponíveis noutra zona. 

![Zonas de disponibilidade](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Saiba mais sobre como implementar um [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) ou [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) VM numa zona de disponibilidade.


<!-- Link references -->
[Configurar várias máquinas virtuais num conjunto de disponibilidade para redundância]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Configurar cada camada da aplicação em conjuntos de disponibilidade separados]: #configure-each-application-tier-into-separate-availability-sets
[Combinar um Balanceador de Carga com conjuntos de disponibilidade]: #combine-a-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Utilizar discos geridos para VMs num conjunto de disponibilidade]: #use-managed-disks-for-vms-in-an-availability-set
[Utilizar zonas de disponibilidade para proteger de falhas de nível do datacenter]: #use-availability-zones-to-protect-from-datacenter-level-failures
