---
title: Perguntas comuns sobre migração do servidor migratório azure migrate
description: Obtenha respostas a perguntas comuns sobre migração do servidor migratório de migração do Azure Migrate
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: bae3447f0fada18de5473e1ef1a1c1d431535f63
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77067386"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migração do servidor migratório Azure Migrador: Questões comuns

Este artigo responde a perguntas comuns sobre o Azure Migrate: Server Migration. Se tiver mais dúvidas depois de ler este artigo, publique-as no [fórum Azure Migrate](https://aka.ms/AzureMigrateForum). Se você tiver outras dúvidas, leia estes artigos:

- [Perguntas gerais](resources-faq.md) sobre Azure Migrate.
- [Perguntas](common-questions-appliance.md) sobre o aparelho Azure Migrate.
- [Perguntas](common-questions-discovery-assessment.md) sobre a descoberta, avaliação e visualização da dependência.


## <a name="how-does-agentless-vmware-replication-work"></a>Como funciona a replicação vMware sem agente?

O método de replicação sem agente para VMware utiliza instantâneos VMware e vMware alterou o rastreio de blocos (CBT). Está programado um ciclo inicial de replicação quando o utilizador iniciar a replicação. No ciclo inicial de replicação, é tirado um instantâneo do VM, e este instantâneo é usado para replicar os VMs VMDKs (discos). Após o ciclo de replicação inicial ser concluído, os ciclos de replicação delta são programados periodicamente. No ciclo de replicação delta, é tirada uma imagem instantânea e os blocos de dados que mudaram desde o ciclo de replicação anterior são replicados. O rastreio de blocos alterado vMware é usado para determinar blocos que mudaram desde o último ciclo.
A frequência dos ciclos de replicação periódica é gerida automaticamente pelo serviço, dependendo de quantos outros VMs/discos estão a replicar simultaneamente a mesma loja de dados e em condições ideais eventualmente convergirá para 1 ciclo por hora por VM.

Quando se migra, está programado um ciclo de replicação a pedido para o VM capturar quaisquer dados restantes. Pode optar por desligar o VM como parte da migração para garantir zero perdas de dados e consistência da aplicação.

## <a name="why-is-the-resynchronization-option-not-exposed-in-agentless-stack"></a>Porque é que a opção de ressincronização não está exposta numa pilha sem agente?

Em pilha sem agente, em cada ciclo delta, transferimos a difusão entre o instantâneo atual e o instantâneo anterior que tínhamos tirado. Uma vez que é sempre uma difusão entre instantâneos, isto dá a vantagem de dobrar os dados (isto é, se um determinado sector estiver escrito 'n' vezes entre os instantâneos, só precisamos de transferir a última escrita, uma vez que estamos interessados apenas na última sincronização). Isto é diferente da pilha baseada em agente em que rastreamos cada escrevo e aplicamo-los. Isto significa que cada ciclo delta é uma resincronização. Portanto, não há nenhuma opção de ressincronização exposta. 

Se alguma vez os discos estiverem dessincronizados devido a uma falha, é fixado no ciclo seguinte. 

## <a name="what-is-the-impact-of-churn-rate-if-i-use-agentless-replication"></a>Qual é o impacto da taxa de agitação se eu usar replicação sem agente?

Uma vez que a pilha depende da dobra dos dados, mais do que a taxa de churn, o padrão de churn é o que importa nesta pilha. Um padrão em que um ficheiro está a ser escrito uma e outra vez não tem muito impacto. No entanto, um padrão em que todos os outros sectores são escritos causará uma grande agitação no próximo ciclo. Uma vez que minimizamos a quantidade de dados que transferimos, permitimos que os dados dobrem o máximo possível antes de agendar o próximo ciclo.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Com que frequência está programado um ciclo de replicação?

A fórmula para agendar o próximo ciclo de replicação é esta: (Tempo de ciclo anterior / 2) ou 1 hora o que for maior. Por exemplo, se um VM demorou quatro horas para um ciclo delta, marcaremos o seu próximo ciclo em 2 horas e não na próxima hora. Isto é diferente quando o ciclo é imediatamente após o IR, onde programamos imediatamente o primeiro ciclo delta.

## <a name="what-is-the-impact-on-performance-of-vcenter-server-or-esxi-host-while-using-agentless-replication"></a>Qual é o impacto no desempenho do vCenter Server ou do hospedeiro ESXi enquanto utiliza uma replicação sem agente?

Uma vez que a replicação sem agente usa instantâneos, haverá consumo de IOPs no armazenamento, e os clientes precisarão de algum espaço para iOPs no armazenamento. Não recomendamos a utilização desta pilha em ambiente restrito de armazenamento/IOPs.

## <a name="does-agentless-migration-stack-support-migration-of-uefi-vms-to-azure-gen-2-vms"></a>A pilha de migração sem agente apoia a migração de VMs UEFI para Os VMs da Gen 2?

Não, você deve usar azure site recovery para migrar estes VMs para VMs Gen 2 Azure. 

## <a name="can-i-pin-my-vms-to-azure-availability-zones-when-i-migrate"></a>Posso fixar os meus VMs nas Zonas de Disponibilidade Azure quando migrar?

Não, o suporte para as Zonas de Disponibilidade Azure não está lá.

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>Que protocolo de transporte é usado pela Azure Migrate durante a replicação?

A Azure Migrate utiliza o protocolo do Dispositivo de Bloqueio de Rede (NBD) com encriptação SSL.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Qual é a versão vCenter Server mínima necessária para a migração?

Precisa de ter pelo menos vCenter Server 5.5 e VMware vSphere ESXi versão anfitriã 5.5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Os clientes podem migrar os seus VMs para discos não geridos?

Não. A Azure Migrate apoia a migração apenas para discos geridos (HDD padrão, SSD premium).

## <a name="how-many-vms-can-replicate-simultaneously-using-agentless-vmware-stack"></a>Quantos VMs podem replicar simultaneamente usando uma pilha de VMware sem agente?

Atualmente, os clientes podem migrar 100 VMs por vCenter Server simultaneamente. Isto pode ser feito em lotes de 10 VMs.




