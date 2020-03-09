---
title: FaQ do aparelho migratório Azure
description: Obtenha respostas a perguntas comuns sobre o aparelho Azure Migrate.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 99f7fc7db79785f99b96e6076607e434e43e605f
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927315"
---
# <a name="azure-migrate-appliance-common-questions"></a>Aparelho migratório Azure: Questões comuns

Este artigo responde a perguntas comuns sobre o aparelho Azure Migrate. Se tiver outras perguntas, verifique estes recursos:

- [Perguntas gerais](resources-faq.md) sobre Azure Migrate
- Perguntas sobre [descoberta, avaliação e visualização da dependência](common-questions-discovery-assessment.md)
- Perguntas sobre [migração de servidores](common-questions-server-migration.md)
- Obter perguntas respondidas no [fórum Migratório Azure](https://aka.ms/AzureMigrateForum)

## <a name="what-is-the-azure-migrate-appliance"></a>O que é o aparelho Azure Migrate?

O aparelho Azure Migrate é um aparelho leve que a ferramenta Azure Migrate: Server Assessment utiliza para descobrir e avaliar servidores no local. A ferramenta Azure Migrate: Server Migration também utiliza o aparelho para a migração sem agentes de VMware VMware no local.

Aqui está mais informações sobre o aparelho Azure Migrate:

- O aparelho é implantado no local como VM ou máquina física.
- O aparelho descobre máquinas no local e envia continuamente metadados de máquinas e dados de desempenho para o Azure Migrate.
- A descoberta do aparelho é sem agente. Nada está instalado em máquinas descobertas.

[Saiba mais](migrate-appliance.md) sobre o aparelho.

## <a name="how-does-the-appliance-connect-to-azure"></a>Como é que o aparelho se liga ao Azure?

O aparelho pode ligar-se através da internet ou utilizando o Azure ExpressRoute com o público/microsoft peering.

## <a name="does-appliance-analysis-affect-performance"></a>A análise do aparelho afeta o desempenho?

Os perfis do aparelho Azure Migrate no local para medir continuamente os dados de desempenho. Este perfil quase não tem impacto no desempenho nas máquinas perfiladas.

## <a name="can-i-harden-the-appliance-vm"></a>Posso endurecer o vm do aparelho?

Quando utilizar o modelo descarregado para criar o VM do aparelho, pode adicionar componentes (antivírus, por exemplo) ao modelo se deixar no lugar as regras de comunicação e firewall que são necessárias para o aparelho Azure Migrate.

## <a name="what-network-connectivity-is-required"></a>Que conectividade de rede é necessária?

Consulte os seguintes artigos para obter informações sobre os requisitos de conectividade da rede para o aparelho Azure Migrate:

- **Avaliação vMware**: [Acesso url](migrate-appliance.md#url-access) e [acesso à porta](migrate-support-matrix-vmware.md#port-access)
- **Migração sem agente VMware**: [Acesso url](migrate-appliance.md#url-access) e [acesso à porta](migrate-support-matrix-vmware-migration.md#agentless-ports)
- **Avaliação de hiper-V:** [Acesso url](migrate-appliance.md#url-access) e [acesso à porta](migrate-support-matrix-hyper-v.md#port-access)

## <a name="what-data-does-the-appliance-collect"></a>Que dados o aparelho recolhe?

Consulte os seguintes artigos para obter informações sobre os dados que o aparelho Azure Migrate recolhe em VMs:

- **VMware VM**: [dados de desempenho](migrate-appliance.md#collected-performance-data-vmware) e [metadados](migrate-appliance.md#collected-metadata-vmware)
- **Hyper-V VM**: [dados de desempenho](migrate-appliance.md#collected-performance-data-hyper-v) e [metadados](migrate-appliance.md#collected-metadata-hyper-v)

## <a name="how-is-data-stored"></a>Como são armazenados os dados?

Os dados recolhidos pelo aparelho Azure Migrate estão armazenados na localização Azure onde criou o projeto Azure Migrate.

Aqui está mais informações sobre como os dados são armazenados:

- Os dados são armazenados de forma segura numa subscrição da Microsoft e são eliminados quando elimina o projeto Azure Migrate.
- Se utilizar a visualização da [dependência,](concepts-dependency-visualization.md)os dados recolhidos são armazenados nos Estados Unidos num espaço de trabalho Azure Log Analytics criado na sua subscrição Azure. Os dados são eliminados quando elimina o espaço de trabalho do Log Analytics na sua subscrição.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Quantos dados são enviados durante o perfil contínuo?

O volume de dados enviados para a Migração Azure depende de vários parâmetros. Como exemplo, um projeto Azure Migrate que tem 10 máquinas (cada uma com um disco e um NIC) envia aproximadamente 50 MB de dados por dia. Este valor é aproximado; o valor real varia consoante o número de pontos de dados dos discos e NICs. Se o número de máquinas, discos ou NICs aumentar, o aumento dos dados enviados não é linear.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>Os dados estão encriptados em repouso e em trânsito?

Sim, para ambos:

- Os metadados são enviados de forma segura para o serviço Azure Migrate através da internet via HTTPS.
- Os metadados são armazenados numa base de dados [da Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) e no [armazenamento do Azure Blob](../storage/common/storage-service-encryption.md) numa subscrição da Microsoft. Os metadados estão encriptados em repouso para armazenamento.
- Os dados para análise de dependência também são encriptados em trânsito (por HTTPS seguro). Está armazenado num espaço de trabalho de Log Analytics na sua subscrição. Os dados são encriptados em repouso para análise de dependência.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Como é que o aparelho se liga ao VCenter Server?

Estas etapas descrevem como o aparelho se liga ao VMware vCenter Server:

1. O aparelho liga-se ao VCenter Server (porta 443) utilizando as credenciais fornecidas quando configurao o aparelho.
2. O aparelho utiliza o VMware PowerCLI para consultar o VCenter Server para recolher metadados sobre os VMs que são geridos pelo VCenter Server.
3. O aparelho recolhe dados de configuração sobre VMs (núcleos, memória, discos, NICs) e o histórico de desempenho de cada VM durante o mês passado.
4. Os metadados recolhidos são enviados para a ferramenta De avaliação do servidor Azure (através da internet via HTTPS) para avaliação.

## <a name="can-i-connect-the-appliance-to-multiple-instances-of-vcenter-server"></a>Posso ligar o aparelho a várias instâncias do VCenter Server?

Não. Há um mapeamento de um para um entre um aparelho e o VCenter Server. Para descobrir VMs em várias instâncias do VCenter Server, deve implementar vários aparelhos.

## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Quantos VMs ou servidores posso descobrir com um aparelho?

Pode descobrir até 10.000 VMware VMs, até 5.000 VMs Hiper-V e até 250 servidores físicos com um único aparelho. Se tiver mais máquinas no seu ambiente no local, leia sobre [a escala de uma avaliação Hyper-V,](scale-hyper-v-assessment.md) [escalonando uma avaliação vMware](scale-vmware-assessment.md)e [escalando uma avaliação do servidor físico](scale-physical-assessment.md).

## <a name="can-i-delete-an-appliance"></a>Posso apagar um aparelho?

Atualmente, a apagar um aparelho do projeto não é suportada.

A única forma de apagar o aparelho é eliminar o grupo de recursos que contém o projeto Azure Migrate que está associado ao aparelho.

No entanto, a eliminação do grupo de recursos também elimina outros aparelhos registados, o inventário descoberto, avaliações e todos os outros componentes do Azure no grupo de recursos que estão associados ao projeto.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Posso usar o aparelho com uma subscrição ou projeto diferente?

Depois de utilizar o aparelho para iniciar a descoberta, não é possível reconfigurar o aparelho para utilizar com uma assinatura Azure diferente, e não pode utilizá-lo num projeto azure migrate diferente. Também não é possível descobrir VMs numa instância diferente do VCenter Server. Instale um novo aparelho para estas tarefas.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Posso montar o aparelho num VM Azure?

Não. Atualmente, esta opção não é suportada. 

## <a name="can-i-discover-on-an-esxi-host"></a>Posso descobrir num hospedeiro esxi?

Não. Para descobrir VMware VMs, você deve ter vCenter Server.

## <a name="how-do-i-update-the-appliance"></a>Como atualizo o aparelho?

Por defeito, o aparelho e os seus agentes instalados são atualizados automaticamente. O aparelho verifica as atualizações a cada 24 horas. As atualizações que falham são novamente tentadas. 

Apenas o aparelho e os agentes do aparelho são atualizados através destas atualizações automáticas. O sistema operativo não é atualizado pelas atualizações automáticas da Azure Migrate. Utilize as Atualizações do Windows para manter o sistema operativo atualizado.

## <a name="can-i-check-agent-health"></a>Posso verificar a saúde do agente?

Sim. No portal, vá a página de **saúde** do Agente para o Azure Migrate: Server Assessment ou Azure Migrate: Server Migration tool. Aí, pode verificar o estado de ligação entre o Azure e os agentes de descoberta e avaliação do aparelho.

## <a name="next-steps"></a>Passos seguintes

Leia a visão geral do [Azure Migrate.](migrate-services-overview.md)
