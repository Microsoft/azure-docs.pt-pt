---
title: Perguntas comuns sobre migração do servidor migratório azure migrate
description: Obtenha respostas a perguntas comuns sobre a utilização da Migração do Servidor Migratório Azure migrate para migrar máquinas.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 0cfe23b4e544040fc3ab69796988ca34b1bdcdbf
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744330"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migração do servidor migratório Azure Migrador: Questões comuns

Este artigo responde a perguntas comuns sobre a ferramenta Migração do Servidor Azure. Se tiver outras perguntas, verifique estes recursos:

- [Perguntas gerais](resources-faq.md) sobre Azure Migrate
- Perguntas sobre o [aparelho Azure Migrate](common-questions-appliance.md)
- Perguntas sobre [descoberta, avaliação e visualização da dependência](common-questions-discovery-assessment.md)
- Obter perguntas respondidas no [fórum Migratório Azure](https://aka.ms/AzureMigrateForum)

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Que geografias são apoiadas para a migração com a Migração Azure?

Reveja as geografias apoiadas para as nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais.](migrate-support-matrix.md#supported-geographies-azure-government)

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

A Azure Migrate utiliza o protocolo do Dispositivo de Bloqueio de Rede (NBD) com encriptação TLS.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Qual é a versão vCenter Server mínima necessária para a migração?

Deve ter pelo menos vCenter Server 5.5 e vSphere ESXi versão anfitriã 5.5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Os clientes podem migrar os seus VMs para discos não geridos?

Não. A Azure Migrate apoia a migração apenas para discos geridos (Standard HDD, Premium SSD).

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>Quantos VMs posso replicar de uma vez usando migração sem agente?

Atualmente, pode migrar 100 VMs por exemplo de vCenter Server simultaneamente. Migrar em lotes de 10 VMs.

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Como é que eu estrangulo a replicação na utilização do aparelho Azure Migrate para uma replicação vmware sem agente?  

Pode acelerar utilizando a NetQosPolicy. Por exemplo:

O AppNamePrefix a utilizar na NetQosPolicy é "GatewayWindowsService.exe". Poderia criar uma política sobre o aparelho Azure Migrate para acelerar o tráfego de replicação do aparelho, criando uma política como esta:
 
New-NetQosPolicy -Name "ThrottleReplication" -AppPathNameMatchCondition "GatewayWindowsService.exe" -ThrottleRateActionBitsPerSecond 1MB

## <a name="can-i-migrate-vms-that-are-already-being-replicated-to-azure"></a>Posso migrar VMs que já estão sendo replicados para Azure? 

Se os VMs já estão a ser replicados para o Azure por outros meios, não podemigrar essas máquinas como VMs com migração de servidores migratórios Azure. Como uma supressão, pode tratar os VMs como servidores físicos e emigrar de acordo com a [migração de servidores físicos suportados](migrate-support-matrix-physical-migration.md).

## <a name="when-do-i-migrate-machines-as-physical-servers"></a>Quando migrarei máquinas como servidores físicos?

As máquinas migratórias, tratando-as como servidores físicos, são úteis em vários cenários:

- Quando se está a migrar para o local, servidores físicos.
- Se estiver a migrar VMs virtualizados por plataformas como xen, KVM.
- Para migrar VMs Hiper-V ou VMware, se por alguma razão você não puder usar o processo de migração padrão para a migração [hyper-V](tutorial-migrate-hyper-v.md)ou [VMware.](server-migrate-overview.md) Por exemplo, se não estiver a executar vCenter VMware vCenter, e estiver a utilizar apenas os anfitriões ESXi.
- Para migrar VMs que estão atualmente em execução em nuvens privadas para Azure
- Se quiser migrar VMs em nuvens públicas como Amazon Web Services (AWS) ou Google Cloud Platform (GCP), para O Azure.

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliance"></a>Desloquei dois (ou mais) aparelhos para descobrir VMs no meu vCenter Server. No entanto, quando tento migrar os VMs, só vejo VMs correspondentes a um dos aparelhos.

Embora este possa ser um caso de bom uso, não o apoiamos atualmente. A colocação de dois (ou mais) aparelhos para descobrir o mesmo conjunto de VMs causa um problema de serviço em que a propriedade vM continua a alternar entre os dois aparelhos. Esta é a razão pela qual vês vMs a aparecer e a desaparecer. Nesses casos, para resolver o problema, deve eliminar um aparelho e fazer uma atualização dura.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>Preciso de VMware vCenter para migrar VMware VMware VMs?
Para [migrar VMware VMs](server-migrate-overview.md) usando migração baseada em vMware ou sem agente, os anfitriões ESXi em que os VMs estão localizados devem ser geridos pelo VCenter Server. Se não tiver vCenter Server, pode migrar VMware VMware VMware migrando-os como servidores físicos. [Saiba mais](migrate-support-matrix-physical-migration.md).
 
## <a name="next-steps"></a>Passos seguintes

Leia a visão geral do [Azure Migrate.](migrate-services-overview.md)
