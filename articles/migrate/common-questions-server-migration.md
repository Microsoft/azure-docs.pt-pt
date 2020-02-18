---
title: Perguntas comuns sobre migração do servidor migratório azure migrate
description: Obtenha respostas a perguntas comuns sobre máquinas migratórias com migração do servidor migratório Azure
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 0c967027457b925b45ea19d994cfadfdbd0b8ab3
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425838"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migração do servidor migratório Azure Migrador: Questões comuns

Este artigo responde a perguntas comuns sobre a ferramenta migração de migração Azure Migrate:Server. Se tiver mais perguntas depois de ler este artigo, reveja estes artigos:

- [Perguntas gerais](resources-faq.md) sobre Azure Migrate.
- [Perguntas](common-questions-appliance.md) sobre o aparelho Azure Migrate.
- [Perguntas](common-questions-discovery-assessment.md) sobre a descoberta, avaliação e visualização da dependência.
- Poste perguntas sobre o [fórum Azure Migrate](https://aka.ms/AzureMigrateForum).


## <a name="how-does-agentless-vmware-replication-work"></a>Como funciona a replicação vMware sem agente?

O método de replicação sem agente para VMware utiliza instantâneos VMware e VMware Changed Block Tracking (CBT).

1. Quando se começa a replicar, está programado um ciclo inicial de replicação. No ciclo inicial, é tirada uma imagem instantânea do VM. Este instantâneo é utilizado para replicar os VMs VMDKs (discos). 
2. Após o ciclo inicial de replicação terminar, os ciclos de replicação delta são programados periodicamente.
    - Durante a replicação delta, é tirada uma imagem instantânea e os blocos de dados que mudaram desde o ciclo de replicação anterior são replicados.
    - VMware CBT é usado para determinar blocos que mudaram desde o último ciclo.
    - A frequência dos ciclos de replicação periódica é gerida automaticamente pelo Azure Migrate, dependendo de quantos outros VMs/discos estão a replicar-se simultaneamente a partir da mesma loja de dados. Em condições ideais, a replicação eventualmente converge para um ciclo por hora para cada VM.

Quando se migra, está programado um ciclo de replicação a pedido para a máquina, para capturar quaisquer dados restantes. Pode optar por desligar a máquina durante a migração, para garantir zero perdas de dados e consistência da aplicação.

## <a name="why-isnt-resynchronization-exposed"></a>Por que a ressincronização não é exposta?

Durante a migração sem agente, em todos os ciclos delta a diferença entre o instantâneo atual e o instantâneo previamente tirado é escrita. Uma vez que é sempre a diferença entre instantâneos, dados dobráveis. Para que, se um determinado sector for escrito N vezes entre instantâneos, apenas a última escrita precisa de ser transferida, uma vez que estamos interessados apenas na última sincronização. Isto difere da replicação baseada no agente, onde rastreamos e aplicamos cada escrita. Isto significa que cada ciclo delta é uma resincronização. Portanto, não há nenhuma opção de ressincronização exposta. Se alguma vez os discos não estiverem sincronizados por causa de uma falha, é fixado no próximo ciclo. 

## <a name="how-does-churn-rate-impact-agentless-replication"></a>Como é que a taxa de impacto da taxa de impacto é uma replicação agente?

Uma vez que a replicação sem agente dobra a data, o padrão de churn é mais importante que a taxa de churn. Quando um ficheiro é escrito uma e outra vez, a taxa não tem muito impacto. No entanto, um padrão em que todos os outros sectores são escritos causa uma grande agitação no próximo ciclo. Uma vez que minimizamos a quantidade de dados que transferimos, permitimos que os dados dobrem o máximo possível antes de agendar o próximo ciclo.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Com que frequência está programado um ciclo de replicação?

A fórmula para agendar o próximo ciclo de replicação: (Tempo de ciclo anterior / 2) ou 1 hora, o que for maior.

Por exemplo, se um VM demorar quatro horas para um ciclo delta, o próximo ciclo está agendado em duas horas, e não na próxima hora. Isto difere imediatamente após a replicação inicial, onde o primeiro ciclo delta é programado imediatamente.

## <a name="how-does-agentless-replication-impact-vmware-servers"></a>Como é que a replicação sem agente afeta os servidores VMware?

Existe algum impacto no desempenho nos anfitriões vCenter Server/ESXi. Uma vez que a replicação sem agente usa instantâneos, consome IOPs no armazenamento, e é necessária alguma largura de banda de armazenamento IOPS. Não recomendamos a utilização de replicação sem agente se houver restrições no armazenamento/IOPs no seu ambiente.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Posso fazer migração sem agente de VMs UEFI para a Azure Gen 2?

Não. Utilize a Recuperação do Sítio Azure para migrar estes VMs para os VMs Da Gen 2 Azure. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>Posso fixar VMs nas Zonas de Disponibilidade Azure quando migrar?

Não, as Zonas de Disponibilidade Azure não são suportadas.

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>Que protocolo de transporte é usado pela Azure Migrate durante a replicação?

A Azure Migrate utiliza o protocolo do Dispositivo de Bloqueio de Rede (NBD) com encriptação SSL.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Qual é a versão vCenter Server mínima necessária para a migração?

Precisa de ter pelo menos vCenter Server 5.5 e VMware vSphere ESXi versão anfitriã 5.5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Os clientes podem migrar os seus VMs para discos não geridos?

Não. A Azure Migrate apoia a migração apenas para discos geridos (HDD padrão, SSD premium).

## <a name="how-many-vms-can-i-replicate-together-with-agentless-migration"></a>Quantos VMs posso replicar juntamente com a migração sem agente?

Atualmente, pode migrar 100 VMs por vCenter Server simultaneamente. Migrar em lotes de 10 VMs.
 



