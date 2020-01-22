---
title: Perguntas comuns sobre o dispositivo de migrações para Azure
description: Obtenha respostas para perguntas comuns sobre o dispositivo de migrações para Azure
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: c9727287160f996059e9c1fb2d1fb5aec5900ab5
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76292337"
---
# <a name="azure-migrate-appliance-common-questions"></a>Dispositivo de migrações para Azure: perguntas comuns

Este artigo responde a perguntas comuns sobre o dispositivo migrações para Azure. Se você tiver outras consultas depois de ler este artigo, poste-as no [Fórum de migrações para Azure](https://aka.ms/AzureMigrateForum). Se você tiver outras dúvidas, leia estes artigos:

- [Perguntas gerais](resources-faq.md) sobre as migrações para Azure.
- [Perguntas](common-questions-discovery-assessment.md) sobre a descoberta, avaliação e visualização de dependência.


## <a name="what-is-the-azure-migrate-appliance"></a>O que é o dispositivo de migrações para Azure?

O dispositivo de migrações para Azure é um dispositivo leve usado pela ferramenta migrações para Azure: Server Assessment para descobrir e avaliar servidores locais e usado pela migração do Azure: ferramenta de migração de servidor para migrar sem agente de VMs VMware locais. 

O dispositivo é implantado localmente como uma VM ou uma máquina física. O dispositivo descobre máquinas locais e envia continuamente metadados de máquina e dados de desempenho para migrações para Azure. A descoberta de dispositivo é sem agente. Nada é instalado em computadores descobertos. [Saiba mais](migrate-appliance.md) sobre o dispositivo.

## <a name="how-does-the-appliance-connect-to-azure"></a>Como o dispositivo se conecta ao Azure?

A conexão pode ser pela Internet ou usar o Azure ExpressRoute com emparelhamento público/Microsoft.

## <a name="does-appliance-analysis-impact-performance"></a>A análise do dispositivo afeta o desempenho?

O dispositivo de migrações para Azure cria perfis de máquinas locais continuamente para medir os dados de desempenho da VM. Essa criação de perfil não tem impacto sobre o desempenho nos hosts Hyper-V/ESXi ou no VMware vCenter Server.

### <a name="can-i-harden-the-appliance-vm"></a>Posso proteger a VM do dispositivo?

Ao criar a VM do dispositivo usando o modelo baixado, você pode adicionar componentes (por exemplo, um antivírus) ao modelo, desde que você deixe as regras de comunicação e firewall necessárias para o dispositivo de migrações para Azure no estado em que se encontram.


## <a name="what-network-connectivity-is-needed"></a>Qual é a conectividade de rede necessária?

Examine o seguinte:
- Avaliação do VMware usando o dispositivo de migrações para Azure: requisitos de acesso de [URL](migrate-appliance.md#url-access) e [porta](migrate-support-matrix-vmware.md#port-access) .
- Migração sem agente do VMware usando o dispositivo de migrações para Azure: requisitos de acesso de [URL](migrate-appliance.md#url-access) e [porta](migrate-support-matrix-vmware-migration.md#agentless-ports) .
- Avaliação do Hyper-V usando o dispositivo de migrações para Azure: requisitos de acesso de [URL](migrate-appliance.md#url-access) e [porta](migrate-support-matrix-hyper-v.md#port-access) .


## <a name="what-data-does-the-appliance-collect"></a>Quais dados o dispositivo coleta?

Examine os dados coletados:

- [Metadados](migrate-appliance.md#collected-metadata-vmware)e [dados de desempenho](migrate-appliance.md#collected-performance-data-vmware) de VM do VMware.
- [Metadados](migrate-appliance.md#collected-metadata-hyper-v)e dados de [desempenho](migrate-appliance.md#collected-performance-data-hyper-v) da VM do Hyper-V.


## <a name="how-is-data-stored"></a>Como são armazenados os dados?

Os dados coletados pelo dispositivo migrações para Azure são armazenados no local do Azure que você escolhe ao criar o projeto de migração. 

- Os dados são armazenados com segurança em uma assinatura da Microsoft e são excluídos quando você exclui o projeto de migrações para Azure.
- Se você usar a [visualização de dependência](concepts-dependency-visualization.md), os dados coletados serão armazenados no Estados Unidos, em um espaço de trabalho log Analytics criado na assinatura do Azure. Esses dados são excluídos quando você exclui o espaço de trabalho Log Analytics em sua assinatura.

## <a name="how-much-data-is-uploaded-in-continuous-profiling"></a>Qual a quantidade de dados carregada na criação de perfil contínua?

O volume de dados enviados para migrações para Azure depende de vários parâmetros. Para dar uma noção do volume, um projeto de migrações para Azure com 10 computadores (cada um com um disco e uma NIC) envia cerca de 50 MB por dia. Esse valor é aproximado e muda com base no número de pontos de dados para as NICs e os discos. O aumento nos dados enviados é não linear se houver um aumento no número de computadores, NICs ou discos.

## <a name="is-data-encrypted-at-restin-transit"></a>Os dados são criptografados em repouso/em trânsito?

Sim, para ambos.

- Os metadados são enviados com segurança para o serviço de migrações para Azure pela Internet, via HTTPS.
- Os metadados são armazenados em um banco de dados [Cosmos do Azure](../cosmos-db/database-encryption-at-rest.md) e no [armazenamento de BLOBs do Azure](../storage/common/storage-service-encryption.md), em uma assinatura da Microsoft. Os metadados são criptografados em repouso.
- Os dados para análise de dependência também são criptografados em trânsito (HTTPS seguro). Ele é armazenado em um espaço de trabalho Log Analytics em sua assinatura. Ele também é criptografado em repouso.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Como o dispositivo se conecta ao vCenter Server?

1. O dispositivo se conecta ao vCenter Server (porta 443), usando as credenciais fornecidas quando você configura o dispositivo.
2. O dispositivo usa o VMware PowerCLI para consultar vCenter Server, a fim de coletar metadados sobre as VMs gerenciadas pelo vCenter Server.
3. O dispositivo coleta dados de configuração sobre VMs (núcleos, memória, discos, NICs) e o histórico de desempenho de cada VM no último mês.
4. Os metadados coletados são ent para migrações para Azure: avaliação de servidor (pela Internet via HTTPS) para avaliação.

## <a name="can-i-connect-the-appliance-to-multiple-vcenter-servers"></a>Posso conectar o dispositivo a vários servidores vCenter?

Não. Há um mapeamento de um para um entre um dispositivo e vCenter Server. Para descobrir VMs em várias instâncias de vCenter Server, você precisa implantar vários dispositivos.

### <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Quantas VMs ou servidores posso descobrir com um dispositivo?

Você pode descobrir até 10.000 VMs VMware, até 5.000 VMs Hyper-V e até 250 servidores com um único dispositivo. Se você tiver mais computadores no seu ambiente local, leia sobre dimensionamento do [Hyper-V](scale-hyper-v-assessment.md), [VMware](scale-vmware-assessment.md) e avaliação [física](scale-physical-assessment.md) .

## <a name="can-i-delete-an-appliance"></a>Posso excluir um dispositivo?

Atualmente, não há suporte para a exclusão do dispositivo do projeto.

- A única maneira de excluir o dispositivo é excluir o grupo de recursos que contém o projeto de migrações para Azure associado ao dispositivo.
- No entanto, a exclusão do grupo de recursos também excluirá outros dispositivos registrados, o inventário descoberto, as avaliações e todos os outros componentes do Azure associados ao projeto no grupo de recursos.


## <a name="can-i-use-the-appliance-with-a-different-subscriptionproject"></a>Posso usar o dispositivo com uma assinatura/projeto diferente?

Depois de usar o dispositivo para iniciar a descoberta, você não pode reconfigurá-la com uma assinatura do Azure diferente ou em um projeto de migração do Azure diferente. Você também não pode descobrir VMs em um vCenter Server diferente. Configure um novo dispositivo para essas tarefas.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Posso configurar o dispositivo em uma VM do Azure?
Não tem suporte no momento. 

## <a name="can-i-discover-on-an-esxi-host"></a>Posso descobrir em um host ESXi?
Não é necessário um vCenter Server para descobrir VMs VMware.

## <a name="how-do-i-update-the-appliance"></a>Como fazer atualizar o dispositivo?

Por padrão, o dispositivo e seus agentes instalados são atualizados automaticamente. O dispositivo verifica se há atualizações uma vez a cada 24 horas. Se houver falhas durante o processo de atualização, haverá um processo de repetição. As atualizações automáticas somente atualizam os agentes do dispositivo e do dispositivo. O sistema operacional não está atualizado. Use as atualizações da Microsoft para manter o sistema operacional atualizado.

## <a name="can-i-check-agent-health"></a>Posso verificar a integridade do agente?

No portal, acesse a página de **integridade do agente** na ferramenta de avaliação do servidor ou de migração do servidor. Lá, você pode verificar o status da conexão entre os agentes de descoberta e avaliação no dispositivo e no Azure.

## <a name="next-steps"></a>Passos seguintes
Leia a [visão geral de migrações para Azure](migrate-services-overview.md).
