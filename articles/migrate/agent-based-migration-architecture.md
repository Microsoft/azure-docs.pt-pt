---
title: Migração baseada em agente na migração do servidor migratório de Azure
description: Fornece uma visão geral da migração VMware VMware baseada em agentes em Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 1389b03d87beb24cba50fec0a782de8b7f935557
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753897"
---
# <a name="agent-based-migration-architecture"></a>Arquitetura de migração baseada em agentes

Este artigo fornece uma visão geral da arquitetura e processos usados para a replicação baseada em agente de VMware VMs com a ferramenta [Azure Migrate: Server Migration.](migrate-services-overview.md#azure-migrate-server-assessment-tool)

Utilizando Azure Migrate: Migração de servidores, pode replicar VMware VMs com algumas opções:

- Migrar VMs usando replicação baseada em agente, como descrito neste artigo.
- Migrar VMware VMware usando replicação sem agente. Isto migra VMs sem necessidade de instalar nada neles.

Saiba mais sobre [a seleção e comparação dos](server-migrate-overview.md) métodos de migração para VMware VMs. 


## <a name="agent-based-migration"></a>Migração baseada em agente

A migração baseada em agentes é usada para migrar vMware vMware no local e servidores físicos para Azure. Também pode ser usado para migrar outros servidores virtualizados no local, bem como VMs de nuvem privada e pública, incluindo instâncias AWS, e VMs GCP. A migração baseada em agentes em Azure Migrate utiliza alguma funcionalidade de backend do serviço de Recuperação do [Local Azure.](../site-recovery/site-recovery-overview.md)


## <a name="architectural-components"></a>Componentes da arquitetura

O diagrama ilustra os componentes envolvidos na migração baseada em agentes.

![O diagrama mostra os componentes para a migração baseada em agentes, que são explicados numa tabela.](./media/agent-based-replication-architecture/architecture.png)

A tabela resume os componentes utilizados para a migração baseada em agentes.

**Componente** | **Detalhes** | **Instalação**
--- | --- | ---
**Aparelho de replicação** | O aparelho de replicação (servidor de configuração/servidor de processo) é uma máquina no local que funciona como uma ponte entre o ambiente no local e a migração do servidor. O aparelho descobre o inventário da máquina no local, para que a Migração do Servidor possa orquestrar a replicação e a migração. O aparelho tem dois componentes:<br/><br/> **Servidor de configuração**: Liga-se à migração do servidor e coordena a replicação.<br/> **Servidor de** processo : Trata da replicação de dados. O servidor de processo recebe dados da máquina, comprime e encripta-os e envia para o Azure. Em Azure, a Migração do Servidor escreve os dados para discos geridos. | Por predefinição, o servidor de processo é instalado juntamente com o servidor de configuração no aparelho de replicação.
**Serviço de Mobilidade** | O serviço mobility é um agente instalado em cada máquina que pretende replicar e migrar. Envia dados de replicação da máquina para o servidor de processo. | Os ficheiros de instalação para diferentes versões do serviço mobility estão localizados no aparelho de replicação. Descarrega e instala o agente de que necessita, de acordo com o sistema operativo e a versão da máquina que pretende replicar.

## <a name="mobility-service-installation"></a>Instalação do serviço de Mobilidade

Pode implantar o Serviço de Mobilidade utilizando os seguintes métodos:

- **Instalação push**: O serviço mobility é instalado pelo servidor de processos quando ativa a proteção de uma máquina. 
- **Instale manualmente**: Pode instalar manualmente o serviço de Mobilidade em cada máquina através de UI ou de um pedido de comando.

O serviço mobility comunica com o aparelho de replicação e máquinas replicadas. Se tiver software antivírus em funcionamento no aparelho de replicação, servidores de processo ou máquinas que estão a ser replicadas, as seguintes pastas devem ser excluídas da digitalização:


- C:\Ficheiros de programa\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Ficheiros de programas (x86)\Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agente (em máquinas Windows com o serviço de Mobilidade instalado)

## <a name="replication-process"></a>Processo de replicação

1. Quando ativa a replicação de uma máquina, começa a replicação inicial para Azure.
2. Durante a replicação inicial, o serviço Mobility lê dados dos discos da máquina e envia-os para o servidor de processo.
3. Estes dados são utilizados para semear uma cópia do disco na sua assinatura Azure. 
4. Após o final da replicação inicial, começa a replicação das alterações delta para Azure. A replicação é de nível de bloco, e quase contínua.
4. O serviço mobility interceta para a memória do disco, integrando-se com o subsistema de armazenamento do sistema operativo. Este método evita operações de E/S de disco na máquina de replicação, para replicação incremental. 
5. As alterações rastreadas para uma máquina são enviadas para o servidor de processo na porta de entrada HTTPS 9443. Esta porta pode ser modificada. O servidor de processo comprime-o e encripta-o e envia-o para o Azure. 

## <a name="ports"></a>Portas

**Dispositivo** | **Ligação**
--- | --- 
**Máquinas de replicação** | O serviço de Mobilidade em funcionamento em VMs comunica com o aparelho de replicação no local na porta HTTPS 443 entrada, para gestão de replicação.<br/><br/> As máquinas enviam dados de replicação para o servidor de processo na porta HTTPS 9443. Esta porta pode ser modificada.
**Aparelho de replicação** | O aparelho de replicação orquestra a replicação com o Azure sobre a porta HTTPS 443.
**Servidor de processo** | O servidor de processo recebe dados de replicação, otimiza-os e encripta-os e envia-os para o armazenamento Azure sobre a porta 443 de saída.


## <a name="performance-and-scaling"></a>Desempenho e dimensionamento

Por predefinição, implementa um único aparelho de replicação que executa tanto o servidor de configuração como o servidor de processo. Se estiveres apenas a replicar algumas máquinas, esta implantação é suficiente. No entanto, se estiver a replicar e a migrar centenas de máquinas, um único servidor de processo pode não ser capaz de lidar com todo o tráfego de replicação. Neste caso, pode implementar servidores de processos adicionais de escala.

### <a name="plan-vmware-deployment"></a>Implementação do Plano VMware

Se estiver a replicar VMware VMs, pode utilizar o [Planejador de Implementação de Recuperação de Sítios para VMware,](../site-recovery/site-recovery-deployment-planner.md)para ajudar a determinar os requisitos de desempenho, incluindo a taxa de alteração diária de dados e os servidores de processo de que necessita.

### <a name="replication-appliance-capacity"></a>Capacidade do aparelho de replicação

Utilize os valores desta tabela para descobrir se precisa de um servidor de processo adicional na sua implementação.

- Se a sua taxa de variação diária (taxa de churn) for superior a 2 TB, implemente um servidor de processo adicional.
- Se estiver a replicar mais de 200 máquinas, implante um aparelho de replicação adicional.

**CPU** | **Memória** | **Caching de dados espaciais gratuitos** | **Taxa de churn** | **Limites de replicação**
--- | --- | --- | --- | ---
8 vCPUs (2 tomadas * 4 núcleos \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB ou menos | < 100 máquinas 
12 vCPUs (2 tomadas * 6 núcleos \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB a 1 TB | 100-150 máquinas.
16 vCPUs (2 tomadas * 8 núcleos \@ 2,5 GHz) | 32 G1 |  1 TB | 1 TB a 2 TB | 151-200 máquinas.

### <a name="sizing-scale-out-process-servers"></a>Servidores de processo de dimensionamento dimensionais

Se precisar de implementar um servidor de processo de escala, utilize esta tabela para descobrir o tamanho do servidor.

**Servidor de processo** | **Espaço gratuito para caching de dados** | **Taxa de churn** | **Limites de replicação**
--- | --- | --- | --- 
4 vCPUs (2 tomadas * 2 núcleos \@ 2,5 GHz), memória de 8 GB | 300 GB | 250 GB ou menos | Até 85 máquinas 
8 vCPUs (2 tomadas * 4 núcleos \@ 2,5 GHz), memória de 12 GB | 600 GB | 251 GB a 1 TB    | 86-150 máquinas.
12 vCPUs (2 tomadas * 6 núcleos \@ 2,5 GHz), memória de 24 GB | 1 TB | 1-2 TB | 151-225 máquinas.

## <a name="throttle-upload-bandwidth"></a>O acelerador carrega a largura de banda.

O tráfego VMware que se replica no Azure passa por um servidor de processo específico. Pode limitar a produção de upload, estrangulando a largura de banda nas máquinas que estão a funcionar como servidores de processo. Pode influenciar a largura de banda utilizando esta chave de registo:

- O valor de registo HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM especifica o número de fios que são utilizados para transferência de dados (replicação inicial ou delta) de um disco. Um valor mais elevado aumenta a largura de banda da rede que é usada para a replicação. O valor predefinido é quatro. O valor máximo é de 32. Monitorize o tráfego para otimizar o valor.
- Além disso, pode acelerar a largura de banda na máquina do servidor de processo da seguinte forma:

    1. Na máquina do servidor de processo, abra o encaixe MMC de backup Azure. Há um atalho no ambiente de trabalho ou na pasta C:\Program Files\Microsoft Azure Recovery Services Agent\bin. 
    2. No snap-in, selecione **Change Properties**.
    3. Em **Throttling**, selecione **Ativar o estrangulamento da largura de banda da Internet para operações de backup**. Definir os limites para o horário de trabalho e não-trabalho. Os intervalos válidos são de 512 Kbps a 1.023 Mbps.


## <a name="next-steps"></a>Passos seguintes

Experimente [a migração baseada em agentes](tutorial-migrate-vmware-agent.md) para [VMware](tutorial-migrate-vmware-agent.md) ou [servidores físicos](tutorial-migrate-physical-virtual-machines.md).
