---
title: FaQ migração do servidor migratório Azure
description: Obtenha respostas a perguntas comuns sobre a utilização da Migração do Servidor Migratório Azure migrate para migrar máquinas.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 273ef746e685afcf9f3654963dd9c6bd5b855b24
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927523"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migração do servidor migratório Azure Migrador: Questões comuns

Este artigo responde a perguntas comuns sobre a ferramenta Migração do Servidor Azure. Se tiver outras perguntas, verifique estes recursos:

- [Perguntas gerais](resources-faq.md) sobre Azure Migrate
- Perguntas sobre o [aparelho Azure Migrate](common-questions-appliance.md)
- Perguntas sobre [descoberta, avaliação e visualização da dependência](common-questions-discovery-assessment.md)
- Obter perguntas respondidas no [fórum Migratório Azure](https://aka.ms/AzureMigrateForum)

## <a name="how-does-agentless-vmware-replication-work"></a>Como funciona a replicação vMware sem agente?

O método de replicação sem agente para VMware utiliza instantâneos VMware e VMware Changed Block Tracking (CBT).

Eis o processo:

1. Quando se começa a replicar, está programado um ciclo inicial de replicação. No ciclo inicial, é tirada uma imagem instantânea do VM. O instantâneo é utilizado para replicar os VMs VMDKs (discos). 
2. Após o ciclo inicial de replicação terminar, os ciclos de replicação delta são programados periodicamente.
    - Durante a replicação delta, é tirada uma imagem instantânea e os blocos de dados que mudaram desde o ciclo de replicação anterior são replicados.
    - VMware CBT é usado para determinar blocos que mudaram desde o último ciclo.
    - A frequência dos ciclos de replicação periódica é gerida automaticamente pela Azure Migrate e depende de quantos outros VMs e discos estão a replicar-se simultaneamente a partir da mesma loja de dados. Em condições ideais, a replicação eventualmente converge para um ciclo por hora para cada VM.

Quando se migra, está programado um ciclo de replicação a pedido para a máquina capturar quaisquer dados restantes. Para garantir zero perda de dados e consistência da aplicação, pode optar por desligar a máquina durante a migração.

## <a name="why-isnt-resynchronization-exposed"></a>Por que a ressincronização não é exposta?

Durante a migração sem agente, em todos os ciclos delta, a diferença entre o instantâneo atual e o instantâneo previamente tirado é escrita. É sempre a diferença entre instantâneos, dados dobráveis. Se um sector específico for escrito *N* vezes entre instantâneos, apenas a última escrita precisa de ser transferida porque estamos interessados apenas na última sincronização. O processo é diferente da replicação baseada no agente, durante a qual rastreamos e aplicamos cada escrita. Neste processo, cada ciclo delta é uma ressincronização. Então, nenhuma opção de ressincronização exposta. Se os discos alguma vez não estiverem sincronizados por causa de uma falha, é fixado no próximo ciclo. 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Como é que a taxa de agitação afeta a replicação sem agente?

Como a replicação sem agente dobra-se nos dados, o padrão de *churn* é mais importante do que a taxa de *churn*. Quando um ficheiro é escrito uma e outra vez, a taxa não tem muito impacto. No entanto, um padrão em que todos os outros sectores são escritos causa uma grande agitação no próximo ciclo. Como minimizamos a quantidade de dados que transferimos, permitimos que os dados dobrem o máximo possível antes de agendarmos o próximo ciclo.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Com que frequência está programado um ciclo de replicação?

A fórmula para agendar o próximo ciclo de replicação é (tempo de ciclo anterior / 2) ou uma hora, o que for maior.

Por exemplo, se um VM demorar quatro horas para um ciclo delta, o próximo ciclo está agendado em duas horas, e não na próxima hora. O processo é diferente imediatamente após a replicação inicial, quando o primeiro ciclo delta é agendado imediatamente.

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>Como é que a replicação sem agente afeta os servidores VMware?

A replicação sem agente resulta em algum impacto de desempenho nos anfitriões VMware vCenter Server e VMware ESXi. Como a replicação sem agente usa instantâneos, consome IOPS no armazenamento, pelo que é necessária alguma largura de banda de armazenamento IOPS. Não recomendamos a utilização de replicação sem agente se tiver restrições no armazenamento ou iOPs no seu ambiente.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Posso fazer migração sem agente de VMs UEFI para a Azure Gen 2?

Não. Utilize a Recuperação do Sítio Azure para migrar estes VMs para os VMs Da Gen 2 Azure. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>Posso fixar VMs nas Zonas de Disponibilidade Azure quando migrar?

Não. As Zonas de Disponibilidade Azure não são suportadas para a migração do Azure Migrate.

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>Que protocolo de transporte o Azure Migrate usa durante a replicação?

A Azure Migrate utiliza o protocolo do Dispositivo de Bloqueio de Rede (NBD) com encriptação SSL.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Qual é a versão vCenter Server mínima necessária para a migração?

Deve ter pelo menos vCenter Server 5.5 e vSphere ESXi versão anfitriã 5.5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Os clientes podem migrar os seus VMs para discos não geridos?

Não. A Azure Migrate apoia a migração apenas para discos geridos (Standard HDD, Premium SSD).

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>Quantos VMs posso replicar de uma vez usando migração sem agente?

Atualmente, pode migrar 100 VMs por exemplo de vCenter Server simultaneamente. Migrar em lotes de 10 VMs.
 
## <a name="next-steps"></a>Passos seguintes

Leia a visão geral do [Azure Migrate.](migrate-services-overview.md)
