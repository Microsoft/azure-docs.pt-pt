---
title: Perguntas comuns sobre o aparelho Azure Migrate
description: Obtenha respostas a perguntas comuns sobre o aparelho Azure Migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 3bb066b08447a951665e629da5ebcb75714b9f1e
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425360"
---
# <a name="azure-migrate-appliance-common-questions"></a>Aparelho migratório Azure: Questões comuns

Este artigo responde a perguntas comuns sobre o aparelho Azure Migrate. Se tiver outras perguntas, reveja estes artigos:

- [Perguntas gerais](resources-faq.md) sobre Azure Migrate.
- [Perguntas](common-questions-discovery-assessment.md) sobre a descoberta, avaliação e visualização da dependência.
- [Perguntas](common-questions-server-migration.md) sobre migração de servidores.
- Responda a perguntas no [fórum Azure Migrate.](https://aka.ms/AzureMigrateForum) 


## <a name="what-is-the-azure-migrate-appliance"></a>O que é o aparelho Azure Migrate?

O aparelho Azure Migrate é um aparelho leve utilizado pela ferramenta De avaliação do servidor Azure Migrate:Server para descobrir e avaliar servidores no local. O aparelho também é utilizado pela ferramenta Migração Azure Migrate:Server, para migração sem agentes de VMware VMware no local. 

- O aparelho é implantado no local como VM ou máquina física.
- O aparelho descobre máquinas no local e envia continuamente metadados de máquinas e dados de desempenho para o Azure Migrate.
- A descoberta do aparelho é sem agente. Nada está instalado em máquinas descobertas.

[Saiba mais](migrate-appliance.md) sobre o aparelho.

## <a name="how-does-the-appliance-connect-to-azure"></a>Como é que o aparelho se liga ao Azure?

O aparelho pode ligar-se através da internet ou utilizar o Azure ExpressRoute com o público/microsoft a espreitar.

## <a name="does-appliance-analysis-impact-performance"></a>A análise do aparelho tem impacto no desempenho?

Os perfis do aparelho Azure Migrate no local para medir continuamente os dados de desempenho. Este perfil de perfis quase não tem máquinas de perfil de impacto de desempenho.

### <a name="can-i-harden-the-appliance-vm"></a>Posso endurecer o vm do aparelho?

Quando criar o VM do aparelho utilizando o modelo descarregado, pode adicionar componentes (por exemplo, um antivírus) ao modelo, desde que deixe no lugar as regras de comunicação e firewall necessárias para o aparelho Migratório Azure.


## <a name="what-network-connectivity-is-needed"></a>Que conectividade de rede é necessária?

Reveja as informações sobre a conectividade da rede:
- Avaliação vMware utilizando o aparelho Migratório Azure: [URL](migrate-appliance.md#url-access) e requisitos de acesso à [porta.](migrate-support-matrix-vmware.md#port-access)
- Migração sem agente VMware utilizando o aparelho Azure Migrate: [URL](migrate-appliance.md#url-access) e requisitos de acesso à [porta.](migrate-support-matrix-vmware-migration.md#agentless-ports)
- Avaliação de hiper-V utilizando o aparelho Migratório Azure: [URL](migrate-appliance.md#url-access) e requisitos de acesso à [porta.](migrate-support-matrix-hyper-v.md#port-access)


## <a name="what-data-does-the-appliance-collect"></a>Que dados o aparelho recolhe?

Reveja os dados recolhidos:

- [VMware](migrate-appliance.md#collected-performance-data-vmware) VM dados de desempenho vM e [metadados](migrate-appliance.md#collected-metadata-vmware).
- Dados de [desempenho](migrate-appliance.md#collected-performance-data-hyper-v) de VM hiper-V e [metadados.](migrate-appliance.md#collected-metadata-hyper-v)


## <a name="how-is-data-stored"></a>Como são armazenados os dados?

Os dados recolhidos pelo aparelho Azure Migrate são armazenados na localização Azure onde criou o projeto Azure Migrate. 

- Os dados são armazenados de forma segura numa subscrição da Microsoft e são eliminados quando elimina o projeto Azure Migrate.
- Se utilizar a visualização da [dependência,](concepts-dependency-visualization.md)os dados recolhidos são armazenados nos Estados Unidos, num espaço de trabalho log Analytics criado na subscrição do Azure. Estes dados são eliminados quando elimina o espaço de trabalho do Log Analytics na sua subscrição.

## <a name="how-much-data-is-uploaded-in-continuous-profiling"></a>Quantos dados são carregados em perfis contínuos?

O volume de dados enviados para a Migração Azure depende de vários parâmetros. Para lhe dar uma ideia, um projeto Azure Migrate com 10 máquinas (cada uma com um disco e um NIC) envia cerca de 50 MB por dia. Este valor é aproximado e altera-se, com base no número de pontos de dados para os NICs e discos. O aumento dos dados enviados não é linear se houver um aumento no número de máquinas, NICs ou discos.

## <a name="is-data-encrypted-at-restin-transit"></a>Os dados estão encriptados em repouso/em trânsito?

Sim, para ambos.

- Os metadados são enviados de forma segura para o serviço Azure Migrate através da internet, via HTTPS.
- Os metadados são armazenados numa base de dados [da Azure Cosmos,](../cosmos-db/database-encryption-at-rest.md) e no [armazenamento do Azure Blob,](../storage/common/storage-service-encryption.md)numa subscrição da Microsoft. Os metadados estão encriptados em repouso.
- Os dados para análise de dependência também são encriptados em trânsito (HTTPS seguro). Está armazenado num espaço de trabalho de Log Analytics na sua subscrição. Também está encriptado em repouso.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Como é que o aparelho se liga ao VCenter Server?

1. O aparelho liga-se ao VCenter Server (porta 443), utilizando as credenciais fornecidas quando configurao o aparelho.
2. O aparelho utiliza o VMware PowerCLI para consultar o VCenter Server, para recolher metadados sobre os VMs geridos pelo VCenter Server.
3. O aparelho recolhe dados de configuração sobre VMs (núcleos, memória, discos, NICs) e o histórico de desempenho de cada VM durante o mês passado.
4. Os metadados recolhidos são enviados para o Azure Migrate: Server Assessment (através da internet via HTTPS) para avaliação.

## <a name="can-i-connect-the-appliance-to-multiple-vcenter-servers"></a>Posso ligar o aparelho a vários servidores vCenter?

Não. Há um mapeamento de um para um entre um aparelho e o VCenter Server. Para descobrir VMs em várias instâncias do VCenter Server, é necessário implementar vários aparelhos.

## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Quantas VMs ou servidores posso descobrir com um dispositivo?

Pode descobrir até 10.000 VMware VMs, até 5.000 VMs Hiper-V e até 250 servidores físicos com um único aparelho. Se tiver mais máquinas no seu ambiente no local, leia sobre a escala [hyper-V,](scale-hyper-v-assessment.md) [VMware](scale-vmware-assessment.md) e avaliação [física.](scale-physical-assessment.md)

## <a name="can-i-delete-an-appliance"></a>Posso apagar um aparelho?

Atualmente, a apagar um aparelho do projeto não é suportada.

- A única forma de apagar o aparelho é eliminar o grupo de recursos que contém o projeto Azure Migrate associado ao aparelho.
- No entanto, a eliminação do grupo de recursos também eliminará outros aparelhos registados, o inventário descoberto, avaliações e todos os outros componentes do Azure associados ao projeto no grupo de recursos.


## <a name="can-i-use-the-appliance-with-a-different-subscriptionproject"></a>Posso usar o aparelho com uma subscrição/projeto diferente?

Depois de utilizar o aparelho para iniciar a descoberta, não é possível reconfigurá-lo com uma assinatura Azure diferente, nem usá-lo num projeto azure migrate diferente. Também não é possível descobrir VMs num servidor vCenter diferente. Instale um novo aparelho para estas tarefas.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Posso montar o aparelho num VM Azure?
Não. Isto não é apoiado atualmente. 

## <a name="can-i-discover-on-an-esxi-host"></a>Posso descobrir num hospedeiro esxi?
Não. Precisa de um vCenter Server para descobrir VMware VMs.

## <a name="how-do-i-update-the-appliance"></a>Como atualizo o aparelho?

Por defeito, o aparelho e os seus agentes instalados são atualizados automaticamente. O aparelho verifica as atualizações uma vez a cada 24 horas. Se o processo de atualização falhar, haverá uma nova tentativa. Atualizações automáticas apenas atualizam os agentes do aparelho e do aparelho. O sistema operativo não está atualizado. Utilize as Atualizações do Microsoft para manter o sistema operativo atualizado.

## <a name="can-i-check-agent-health"></a>Posso verificar a saúde do agente?

No portal, vá na página de **saúde** do Agente na ferramenta de avaliação do servidor ou migração do servidor. Aí, pode verificar o estado de ligação entre os agentes de descoberta e avaliação do aparelho e o Azure.

## <a name="next-steps"></a>Passos seguintes
Leia a visão geral do [Azure Migrate.](migrate-services-overview.md)
