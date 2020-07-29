---
title: Perguntas comuns sobre migração de servidores Azure Migrate
description: Obtenha respostas para perguntas comuns sobre a utilização da migração do servidor Azure Migrate para migrar máquinas.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: af40aecaa1614542074cf87ce95eb81492233bdc
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321229"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migração do servidor Azure Migrate: Questões comuns

Este artigo responde a perguntas comuns sobre a ferramenta Azure Migrate: Server Migration. Se tiver outras questões, verifique estes recursos:

- [Perguntas gerais](resources-faq.md) sobre Azure Migrate
- Perguntas sobre o [aparelho Azure Migrate](common-questions-appliance.md)
- Perguntas sobre [descoberta, avaliação e visualização de dependência](common-questions-discovery-assessment.md)
- Obtenha perguntas respondidas no [fórum Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Que geografias são apoiadas para a migração com Azure Migrate?

Reveja as regiões suportadas em [clouds públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e do [Azure Government](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="how-does-agentless-vmware-replication-work"></a>Como funciona a replicação de VMware sem agente?

O método de replicação sem agente para VMware utiliza instantâneos VMware e VMware Change Block Tracking (CBT).

Segue-se o processo:

1. Quando se inicia a replicação, está programado um ciclo inicial de replicação. No ciclo inicial, é tirada uma imagem do VM. O instantâneo é utilizado para replicar os VMs VMDKs (discos). 
2. Após o fim do ciclo de replicação inicial, os ciclos de replicação delta são programados periodicamente.
    - Durante a replicação delta, um instantâneo é tirado, e blocos de dados que mudaram desde o ciclo de replicação anterior são replicados.
    - VMware CBT é usado para determinar blocos que mudaram desde o último ciclo.
    - A frequência dos ciclos periódicos de replicação é gerida automaticamente pela Azure Migrate e depende de quantos outros VMs e discos estão simultaneamente a replicar-se a partir da mesma datastore. Em condições ideais, a replicação eventualmente converge para um ciclo por hora para cada VM.

Quando migrar, está programado um ciclo de replicação a pedido para a máquina capturar quaisquer dados restantes. Para garantir a perda de dados zero e a consistência da aplicação, pode optar por desligar a máquina durante a migração.

## <a name="why-isnt-resynchronization-exposed"></a>Por que a ressincronização não está exposta?

Durante a migração sem agente, em cada ciclo delta, a diferença entre o instantâneo atual e o instantâneo anteriormente tirado é escrito. É sempre a diferença entre instantâneos, dados dobráveis. Se um sector específico for escrito *N* vezes entre instantâneos, apenas a última escrita precisa de ser transferida porque estamos interessados apenas na última sincronização. O processo é diferente da replicação baseada em agente, durante a qual rastreamos e aplicamos cada escrita. Neste processo, cada ciclo delta é uma ressincronização. Então, nenhuma opção de ressincronização exposta. Se os discos não estiverem sincronizados por causa de uma falha, é fixado no ciclo seguinte. 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Como é que a taxa de churn afeta a replicação sem agente?

Como a replicação sem agente se dobra nos dados, o *padrão de churn* é mais importante do que a *taxa de churn*. Quando um ficheiro é escrito uma e outra vez, a taxa não tem muito impacto. No entanto, um padrão em que todos os outros sectores são escritos causa uma grande agitação no ciclo seguinte. Como minimizamos a quantidade de dados que transferimos, permitimos que os dados dobrem o máximo possível antes de agendarmos o próximo ciclo.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Com que frequência é programado um ciclo de replicação?

A fórmula para agendar o próximo ciclo de replicação é (tempo de ciclo / 2) ou uma hora, o que for maior.

Por exemplo, se um VM demorar quatro horas para um ciclo delta, o próximo ciclo é programado em duas horas, e não na próxima hora. O processo é diferente imediatamente após a replicação inicial, quando o primeiro ciclo delta é programado imediatamente.

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>Como é que a replicação sem agente afeta os servidores VMware?

A replicação sem agente resulta em algum impacto de desempenho nos anfitriões VMware vCenter Server e VMware ESXi. Como a replicação sem agente utiliza instantâneos, consome IOPS no armazenamento, pelo que é necessária uma largura de banda de armazenamento IOPS. Não recomendamos a utilização de replicação sem agente se tiver restrições no armazenamento ou IOPs no seu ambiente.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Posso fazer a migração sem agente de VMs da UEFI para a Azure Gen 2?

Não. Utilize a Recuperação do Local azul para migrar estes VMs para os VMs Gen 2 Azure. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>Posso fixar VMs em Zonas de Disponibilidade Azure quando migrar?

Não. As Zonas de Disponibilidade Azure não são suportadas pela migração do Azure Migrate.

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>Que protocolo de transporte a Azure Migrate utiliza durante a replicação?

A Azure Migrate utiliza o protocolo dispositivo de bloqueio de rede (NBD) com encriptação TLS.

## <a name="how-is-the-data-transmitted-from-on-prem-environment-to-azure-is-it-encrypted-before-transmission"></a>Como é transmitidos os dados do ambiente on-prem para o Azure? Está encriptado antes da transmissão? 
O aparelho Azure Migrate no caso de replicação sem agente comprime dados e encripta antes de ser carregado. Os dados são transmitidos através de um canal de comunicação seguro através de https e utilizam o TLS 1.2 ou mais tarde. Além disso, o Azure Storage encripta automaticamente os seus dados quando este é persistido na nuvem (encriptação em repouso).  

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Qual é a versão mínima do vCenter Server necessária para a migração?

Deve ter pelo menos o vCenter Server 5.5 e vSphere ESXi versão 5.5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Os clientes podem migrar os seus VMs para discos não geridos?

Não. A Azure Migrate suporta a migração apenas para discos geridos (Standard HDD, Premium SSD).

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>Quantos VMs posso replicar ao mesmo tempo usando migração sem agentes?

Atualmente, pode migrar 300 VMs por exemplo do vCenter Server simultaneamente. Migrar em lotes de 10 VMs.

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Como posso acelerar a replicação na utilização do aparelho Azure Migrate para a replicação de VMware sem agente?  

Pode acelerar usando a NetQosPolicy. Por exemplo:

O AppNamePrefix para usar na NetQosPolicy é "GatewayWindowsService.exe". Pode criar uma política sobre o aparelho Azure Migrate para acelerar o tráfego de replicação do aparelho, criando uma política como esta:
 
New-NetQosPolicy -Nome "ThrottleReplication" -AppPathNameMatchCondition "GatewayWindowsService.exe" -ThrottleRateActionBitsPerSecond 1MB

## <a name="can-i-migrate-vms-that-are-already-being-replicated-to-azure"></a>Posso migrar VMs que já estão a ser replicados para o Azure? 

Se os VMs já estiverem a ser replicados para Azure por outros meios, não é possível migrar essas máquinas como VMs com Azure Migrate Server Migration. Como uma solução alternativa, você pode tratar os VMs como servidores físicos, e migrar-los de acordo com a [migração de servidores físicos suportados](migrate-support-matrix-physical-migration.md).

## <a name="when-do-i-migrate-machines-as-physical-servers"></a>Quando migrarei máquinas como servidores físicos?

As máquinas migratórias tratando-as como servidores físicos são úteis em vários cenários:

- Quando se está a migrar para os servidores físicos.
- Se estiver a migrar VMs virtualizados por plataformas como xen, KVM.
- Para migrar VMs Hiper-V ou VMware, se por alguma razão não conseguir utilizar o processo de migração padrão para a migração [de Hiper-V](tutorial-migrate-hyper-v.md)ou [VMware.](server-migrate-overview.md) Por exemplo, se não estiver a executar vCenter VMware e estiver a utilizar apenas os anfitriões ESXi.
- Para migrar VMs que estão atualmente em execução em nuvens privadas para Azure
- Se quiser migrar VMs em nuvens públicas como a Amazon Web Services (AWS) ou a Google Cloud Platform (GCP), para o Azure.

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliance"></a>Implementei dois (ou mais) aparelhos para descobrir VMs no meu servidor vCenter. No entanto, quando tento migrar os VMs, só vejo VMs correspondentes a um dos aparelhos.

Se houver vários aparelhos configurados, é necessário que não haja sobreposição entre os VMs nas contas vCenter fornecidas. Uma descoberta com tal sobreposição é um cenário não apoiado.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>Preciso de VMware vCenter para migrar VMware VMs?
Para [migrar VMware VMs](server-migrate-overview.md) utilizando a migração baseada em agentes VMware ou sem agentes, os anfitriões ESXi nos quais estão localizados os VMs devem ser geridos pelo vCenter Server. Se não tiver vCenter Server, pode migrar VMware VMs migrando-os como servidores físicos. [Saiba mais](migrate-support-matrix-physical-migration.md).
 
## <a name="next-steps"></a>Passos seguintes

Leia a visão geral do [Azure Migrate](migrate-services-overview.md).
