---
title: Migração baseada em agente na migração do servidor migratório azure migrate
description: Fornece uma visão geral da migração VMware VM baseada em agente em Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: raynew
ms.openlocfilehash: d345d707cbf58f48466c3bd830d93250d13397c6
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425867"
---
# <a name="agent-based-migration-architecture"></a>Arquitetura de migração baseada em agentes

Este artigo fornece uma visão geral da arquitetura e processos utilizados para a replicação baseada em agentes de VMware VMs com a ferramenta [Migração De Servidores Azure.](migrate-services-overview.md#azure-migrate-server-assessment-tool)

Utilizando o Azure Migrate: Migração do Servidor, pode replicar VMware VMs com algumas opções:

- Migram VMs usando replicação baseada em agente, como descrito neste artigo.
- VMs migradores usando replicação sem agente. Isto migra vMs sem necessidade de instalar nada neles.

Saiba mais sobre [a seleção e comparação](server-migrate-overview.md) de métodos de migração para VMware VMs. 


## <a name="agent-based-migration"></a>Migração baseada em agentes

A migração baseada em agentes é usada para migrar no local VMware VMs e servidores físicos para O Azure. Também pode ser usado para migrar outros servidores virtualizados no local, bem como VMs de nuvem privada e pública, incluindo instâncias AWS, e VMs GCP. A migração baseada em agentes no Azure Migrate utiliza alguma funcionalidade de backend do serviço de recuperação de [sites Azure.](../site-recovery/site-recovery-overview.md)


## <a name="architectural-components"></a>Componentes da arquitetura

O diagrama ilustra os componentes envolvidos na migração baseada em agentes.

![Arquitetura](./media/agent-based-replication-architecture/architecture.png)

A tabela resume os componentes utilizados para a migração baseada no agente.

**Componente** | **Detalhes** | **Instalação**
--- | --- | ---
**Aparelho de replicação** | O aparelho de replicação (servidor de configuração/servidor de processos) é uma máquina no local que funciona como uma ponte entre o ambiente no local, e a Migração do Servidor. O aparelho descobre o inventário da máquina no local, para que a Migração do Servidor possa orquestrar a replicação e a migração. O aparelho tem dois componentes:<br/><br/> **Servidor de configuração**: Conecta-se à Migração do Servidor e coordena a replicação.<br/> **Servidor de processos**: Trata da replicação de dados. O servidor de processos recebe dados da máquina, comprime e encripta-os e envia para o Azure. Em Azure, server Migration escreve os dados para discos geridos. | Por defeito, o servidor de processo sou instalado juntamente com o servidor de configuração do aparelho de replicação.
**Serviço de mobilidade** | O serviço mobility é um agente instalado em cada máquina que pretende replicar e migrar. Envia dados de replicação da máquina para o servidor de processos. | Os ficheiros de instalação para diferentes versões do serviço mobility estão localizados no aparelho de replicação. Faça o download e instala o agente de que necessita, de acordo com o sistema operativo e a versão da máquina que pretende replicar.

## <a name="mobility-service-installation"></a>Instalação do serviço de Mobilidade

Você pode implantar o serviço de mobilidade usando os seguintes métodos:

- **Instalação de impulso**: O serviço de Mobilidade é instalado pelo servidor de processo quando ativa a proteção de uma máquina. 
- **Instale manualmente:** Pode instalar manualmente o serviço mobility em cada máquina através de UI ou pedido de comando.

O serviço de Mobilidade comunica com o aparelho de replicação e máquinas replicadas. Se tiver software antivírus em execução no aparelho de replicação, servidores de processos ou máquinas a serem replicadas, as seguintes pastas devem ser excluídas da digitalização:


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86)\Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (em máquinas Windows com o serviço de Mobilidade instalado)

## <a name="replication-process"></a>Processo de replicação

1. Quando ativa a replicação de uma máquina, começa a replicação inicial para Azure.
2. Durante a replicação inicial, o serviço de Mobilidade lê os dados dos discos da máquina e envia-os para o servidor de processos.
3. Estes dados são utilizados para semear uma cópia do disco na sua subscrição Azure. 
4. Após os acabamentos iniciais da replicação, começa a replicação das alterações delta para Azure. A replicação é de nível de bloco, e quase contínua.
4. O serviço de Mobilidade interceta escritos para memória de disco, integrando-se com o subsistema de armazenamento do sistema operativo. Este método evita as operações de I/S do disco na máquina de replicação, para replicação incremental. 
5. As alterações rastreadas para uma máquina são enviadas para o servidor de processo na porta de entrada HTTPS 9443. Esta porta pode ser modificada. O servidor de processos comprime e encripta-o e envia-o para o Azure. 

## <a name="ports"></a>Portas

**Dispositivo** | **Conexão**
--- | --- 
**Máquinas de replicação** | O serviço de Mobilidade em funcionamento em VMs comunica com o aparelho de replicação no local na porta HTTPS 443 entrada, para gestão de replicação.<br/><br/> As máquinas enviam dados de replicação para o servidor de processo na entrada da porta HTTPS 9443. Esta porta pode ser modificada.
**Aparelho de replicação** | O aparelho de replicação orquestra a replicação com Azure sobre a saída da porta HTTPS 443.
**Servidor de processos** | O servidor de processos recebe dados de replicação, otimiza e encripta-os, e envia-os para o armazenamento Azure sobre a saída da porta 443.


## <a name="performance-and-scaling"></a>Desempenho e dimensionamento

Por predefinição, implementa um único aparelho de replicação que executa tanto o servidor de configuração como o servidor de processo. Se estiver apenas a replicar algumas máquinas, esta implantação é suficiente. No entanto, se estiver a replicar e a migrar centenas de máquinas, um único servidor de processos pode não ser capaz de lidar com todo o tráfego de replicação. Neste caso, pode implementar servidores de processo seletos adicionais.

### <a name="plan-vmware-deployment"></a>Implementação de VMware de plano

Se estiver a replicar VMs VMware, pode utilizar o Planificador de Implementação de Recuperação do [Site para VMware,](../site-recovery/site-recovery-deployment-planner.md)para ajudar a determinar os requisitos de desempenho, incluindo a taxa diária de alteração de dados e os servidores de processo de que necessita.

### <a name="replication-appliance-capacity"></a>Capacidade do aparelho de replicação

Utilize os valores nesta tabela para descobrir se precisa de um servidor de processo adicional na sua implementação.

- Se a sua taxa de variação diária (taxa de churn) for superior a 2 TB, implemente um servidor de processo adicional.
- Se estiver a replicar mais de 200 máquinas, implante um aparelho de replicação adicional.

**CPU** | **Memória** | **Cache de dados espaciais gratuitos** | **Taxa de churn** | **Limites de replicação**
--- | --- | --- | --- | ---
8 vCPUs (2 tomadas * 4 núcleos \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB ou menos | E 100 máquinas 
12 vCPUs (2 tomadas * 6 núcleos \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB a 1 TB | 100-150 máquinas.
16 vCPUs (2 tomadas * 8 núcleos \@ 2,5 GHz) | 32 G1 |  1 TB | 1 TB a 2 TB | 151-200 máquinas.

### <a name="sizing-scale-out-process-servers"></a>Dimensionamento de servidores de processos de escala

Se precisar de implementar um servidor de processo sem escalas, utilize esta tabela para descobrir o tamanho do servidor.

**Servidor de processos** | **Espaço livre para recolha de dados** | **Taxa de churn** | **Limites de replicação**
--- | --- | --- | --- 
4 vCPUs (2 tomadas * 2 núcleos \@ 2,5 GHz), memória de 8-GB | 300 GB | 250 GB ou menos | Até 85 máquinas 
8 vCPUs (2 tomadas * 4 núcleos \@ 2,5 GHz), memória de 12 GB | 600 GB | 251 GB a 1 TB    | 86-150 máquinas.
12 vCPUs (2 tomadas * 6 núcleos \@ 2,5 GHz), memória de 24-GB | 1 TB | 1-2 TB | 151-225 máquinas.

## <a name="throttle-upload-bandwidth"></a>Largura de banda de carregamento de aceleração.

O tráfego vMware que se replica para o Azure passa por um servidor de processo específico. Pode limitar a entrada de carregamento acelerando a largura de banda nas máquinas que estão a funcionar como servidores de processos. Pode influenciar a largura de banda utilizando esta chave de registo:

- O HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft\Microsoft\Microsoft Azure Backup\Replication\UploadThreadsPerVM valor de registo especifica o número de fios que são usados para transferência de dados (replicação inicial ou delta) de um disco. Um valor mais elevado aumenta a largura de banda da rede que é usada para a replicação. O valor padrão é quatro. O valor máximo é 32. Monitorize o tráfego para otimizar o valor.
- Além disso, pode acelerar a largura de banda na máquina do servidor de processos da seguinte forma:

    1. Na máquina do servidor de processos, abra o encaixe de MMC de backup Azure. Há um atalho no ambiente de trabalho ou na pasta C:\Program Files\Microsoft Azure Recovery Services Agent\bin. 
    2. No snap-in, selecione **Change Properties**.
    3. Em **Throttling**, selecione Ativar o estrangulamento da largura de banda da **Internet para operações de backup**. Estabeleça os limites para o trabalho e o horário de trabalho. As gamas válidas são de 512 Kbps a 1.023 Mbps.


## <a name="next-steps"></a>Passos seguintes

Experimente a [migração baseada em agentes](tutorial-migrate-vmware-agent.md) para [VMware](tutorial-migrate-vmware-agent.md) ou [servidores físicos](tutorial-migrate-physical-virtual-machines.md).
