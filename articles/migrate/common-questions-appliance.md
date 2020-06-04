---
title: Azure Migrate eletrodoméstico FAQ
description: Obtenha respostas a perguntas comuns sobre o aparelho Azure Migrate.
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: 14ce04e992cdbf121bcb1518b6a95c09ec421872
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84324424"
---
# <a name="azure-migrate-appliance-common-questions"></a>Aparelho Azure Migrate: Questões comuns

Este artigo responde a perguntas comuns sobre o aparelho Azure Migrate. Se tiver outras questões, verifique estes recursos:

- [Perguntas gerais](resources-faq.md) sobre Azure Migrate
- Perguntas sobre [descoberta, avaliação e visualização de dependência](common-questions-discovery-assessment.md)
- Perguntas sobre [migração de servidores](common-questions-server-migration.md)
- Obtenha perguntas respondidas no [fórum Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="what-is-the-azure-migrate-appliance"></a>O que é o aparelho Azure Migrate?

O aparelho Azure Migrate é um aparelho leve que a ferramenta Azure Migrate: Ferramenta de avaliação do servidor utiliza para descobrir e avaliar servidores no local. A ferramenta Azure Migrate: Server Migration também utiliza o aparelho para migração sem agentes de VMware VMware no local.

Aqui está mais informações sobre o aparelho Azure Migrate:

- O aparelho é colocado no local como VM ou máquina física.
- O aparelho descobre máquinas no local e envia continuamente metadados e dados de desempenho da máquina para a Azure Migrate.
- A descoberta do aparelho é sem agente. Nada está instalado em máquinas descobertas.

[Saiba mais](migrate-appliance.md) sobre o aparelho.

## <a name="how-can-i-deploy-the-appliance"></a>Como posso colocar o aparelho?

O aparelho pode ser acionado da seguinte forma:

- Utilizando um modelo para VMware VMs e Hiper-VMs (modelo OVA para VMware ou VHD para Hiper-V).
- Se não quiser utilizar um modelo, ou se estiver no Governo Azure, pode utilizar o aparelho para VMware ou Hyper-V utilizando um script PowerShell.
- Para servidores físicos, utilize sempre o aparelho utilizando um script.


## <a name="how-does-the-appliance-connect-to-azure"></a>Como é que o aparelho se liga ao Azure?

O aparelho pode ligar-se através da internet ou utilizando o Azure ExpressRoute com o olhar público/Microsoft.

## <a name="does-appliance-analysis-affect-performance"></a>A análise do aparelho afeta o desempenho?

O aparelho Azure Migrate perfis no local máquinas continuamente para medir os dados de desempenho. Este perfil não tem quase nenhum impacto no desempenho em máquinas com perfil.

## <a name="can-i-harden-the-appliance-vm"></a>Posso endurecer o aparelho VM?

Quando utilizar o modelo descarregado para criar o VM do aparelho, pode adicionar componentes (antivírus, por exemplo) ao modelo se deixar em vigor as regras de comunicação e firewall que são necessárias para o aparelho Azure Migrate.

## <a name="what-network-connectivity-is-required"></a>Que conectividade de rede é necessária?


O aparelho precisa de acesso aos URLs Azure. [Reveja](migrate-appliance.md#url-access) a lista de URL.

## <a name="what-data-does-the-appliance-collect"></a>Que dados o aparelho recolhe?

Consulte os seguintes artigos para obter informações sobre os dados que o aparelho Azure Migrate recolhe em VMs:

- **VMware VM:** [Rever os](migrate-appliance.md#collected-data---vmware) dados recolhidos. [
- **Hiper-V VM:** [Rever os](migrate-appliance.md#collected-data---hyper-v) dados recolhidos.

## <a name="how-is-data-stored"></a>Como são armazenados os dados?

Os dados recolhidos pelo aparelho Azure Migrate estão armazenados no local Azure onde criou o projeto Azure Migrate.

Aqui estão mais informações sobre como os dados são armazenados:

- Os dados recolhidos são armazenados de forma segura no CosmosDB numa subscrição da Microsoft. Os dados são eliminados quando elimina o projeto Azure Migrate. O armazenamento é manuseado pela Azure Migrate. Não é possível escolher especificamente uma conta de armazenamento para os dados recolhidos.
- Se utilizar [a visualização de dependência,](concepts-dependency-visualization.md)os dados recolhidos são armazenados nos Estados Unidos num espaço de trabalho Azure Log Analytics criado na sua subscrição Azure. Os dados são eliminados quando elimina o espaço de trabalho Do Log Analytics na sua subscrição.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Quantos dados são carregados durante o perfil contínuo?

O volume de dados enviados para a Azure Migrate depende de vários parâmetros. Como exemplo, um projeto Azure Migrate que tem 10 máquinas (cada uma com um disco e um NIC) envia aproximadamente 50 MB de dados por dia. Este valor é aproximado; o valor real varia dependendo do número de pontos de dados para os discos e NICs. Se o número de máquinas, discos ou NICs aumentar, o aumento dos dados enviados não é linear.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>Os dados são encriptados em repouso e em trânsito?

Sim, para ambos:

- Os metadados são enviados de forma segura para o serviço Azure Migrate através da internet através de HTTPS.
- Os metadados são armazenados numa base de [dados Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) e no [armazenamento Azure Blob](../storage/common/storage-service-encryption.md) numa subscrição da Microsoft. Os metadados são encriptados em repouso para armazenamento.
- Os dados para análise de dependência também são encriptados em trânsito (por HTTPS seguro). Está guardado num espaço de trabalho do Log Analytics na sua subscrição. Os dados são encriptados em repouso para análise de dependência.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Como é que o aparelho se liga ao servidor vCenter?

Estes passos descrevem como o aparelho se conecta ao VMware vCenter Server:

1. O aparelho liga-se ao servidor vCenter (porta 443) utilizando as credenciais fornecidas quando configurar o aparelho.
2. O aparelho utiliza o VMware PowerCLI para consultar o vCenter Server para recolher metadados sobre os VMs que são geridos pelo vCenter Server.
3. O aparelho recolhe dados de configuração sobre VMs (núcleos, memória, discos, NICs) e o histórico de desempenho de cada VM durante o mês passado.
4. Os metadados recolhidos são enviados para o Azure Migrate: Ferramenta de avaliação do servidor (através da internet via HTTPS) para avaliação.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>O aparelho Azure Migrate pode ligar-se a vários servidores vCenter?

Não. Há um mapeamento de um para um entre um [aparelho Azure Migrate](migrate-appliance.md) e vCenter Server. Para descobrir VMs em várias instâncias do VCenter Server, tem de implantar vários aparelhos. 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Um projeto Azure Migrate pode ter vários aparelhos?
Um projeto pode ter vários aparelhos ligados a ele. No entanto, um aparelho só pode ser associado a um projeto. 

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>O aparelho Azure Migrate/aparelho de replicação pode ligar-se ao mesmo vCenter?
Yes. Pode adicionar tanto o aparelho Azure Migrate (utilizado para avaliação e migração de VMware sem agente) como o aparelho de replicação (utilizado para a migração baseada em agentes de VMware VMs) ao mesmo servidor vCenter.


## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Quantos VMs ou servidores posso descobrir com um aparelho?

Pode descobrir até 10.000 VMware VMs, até 5.000 VMs Hiper-V e até 250 servidores físicos com um único aparelho. Se tiver mais máquinas no seu ambiente no local, leia sobre [o escalonamento de uma avaliação de Hiper-V,](scale-hyper-v-assessment.md) [escalonando uma avaliação de VMware](scale-vmware-assessment.md)e [escalando uma avaliação do servidor físico](scale-physical-assessment.md).

## <a name="can-i-delete-an-appliance"></a>Posso apagar um aparelho?

Atualmente, a eliminação de um aparelho do projeto não é suportada.

A única forma de eliminar o aparelho é eliminar o grupo de recursos que contém o projeto Azure Migrate que está associado ao aparelho.

No entanto, a eliminação do grupo de recursos também elimina outros aparelhos registados, o inventário descoberto, avaliações e todos os outros componentes Azure no grupo de recursos que estão associados ao projeto.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Posso utilizar o aparelho com uma subscrição ou projeto diferente?

Depois de utilizar o aparelho para iniciar a descoberta, não pode reconfigurar o aparelho para utilizar com uma subscrição Azure diferente, e não pode usá-lo num projeto diferente da Azure Migrate. Também não é possível descobrir VMs numa instância diferente do vCenter Server. Crie um novo aparelho para estas tarefas.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Posso montar o aparelho num Azure VM?

Não. Atualmente, esta opção não é suportada. 

## <a name="can-i-discover-on-an-esxi-host"></a>Posso descobrir num hospedeiro ESXi?

Não. Para descobrir VMware VMs, tem de ter o vCenter Server.

## <a name="how-do-i-update-the-appliance"></a>Como atualizei o aparelho?

Por predefinição, o aparelho e os seus agentes instalados são atualizados automaticamente. O aparelho verifica as atualizações a cada 24 horas. As atualizações que falham são novamente experimentadas. 

Apenas o aparelho e os agentes do aparelho são atualizados através destas atualizações automáticas. O sistema operativo não é atualizado pelas atualizações automáticas da Azure Migrate. Utilize atualizações do Windows para manter o sistema operativo atualizado.

## <a name="can-i-check-agent-health"></a>Posso verificar a saúde do agente?

Yes. No portal, vá à página **de saúde do Agente** para o Azure Migrate: Avaliação do Servidor ou Azure Migrate: Ferramenta de migração do servidor. Lá, pode verificar o estado de ligação entre a Azure e os agentes de descoberta e avaliação do aparelho.

## <a name="next-steps"></a>Próximos passos

Leia a visão geral do [Azure Migrate](migrate-services-overview.md).
