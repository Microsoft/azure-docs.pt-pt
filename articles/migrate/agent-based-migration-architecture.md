---
title: Migração baseada em agente no Azure migrar migração de servidor
description: Fornece uma visão geral da migração de VM do VMware baseada em agente com a migração de servidor de migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: a8477b4c10ccbc76f36eed4d64ac12e8bb648a28
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186090"
---
# <a name="agent-based-migration-architecture"></a>Arquitetura de migração baseada em agentes

Este artigo fornece uma visão geral da arquitetura e dos processos usados para a replicação baseada em agente com a ferramenta de migração de servidor de migrações para Azure.

As [migrações para Azure](migrate-services-overview.md) fornecem um hub central para acompanhar a descoberta, avaliação e migração de seus aplicativos e cargas de trabalho locais e instâncias de VM AWS/GCP para o Azure. O Hub fornece ferramentas de migração do Azure para avaliação e migração, bem como ofertas de ISVs (fornecedores independentes de software) de terceiros.

## <a name="agent-based-replication"></a>Replicação baseada em agente

A replicação baseada em agente na ferramenta de replicação de servidor de migrações para Azure é usada para migrar VMs VMware locais e servidores físicos para o Azure. Ele também pode ser usado para migrar outros servidores virtualizados locais, bem como VMs de nuvem pública e privada, incluindo instâncias AWS e VMs GCP.

Para a migração do VMware, a ferramenta de migração de servidor de migrações para Azure oferece algumas opções:

- Migração usando a replicação baseada em agente, conforme descrito neste artigo.
- A replicação sem agente, para migrar as VMs não precisar instalar nada nelas.

Saiba mais sobre como [selecionar um método de migração para VMware](server-migrate-overview.md).

## <a name="server-migration-and-azure-site-recovery"></a>Migração e Azure Site Recovery do servidor

A migração de servidor de migrações para Azure é uma ferramenta para migrar cargas de trabalho locais e de nuvem pública para o Azure. Ele é otimizado para migração. Site Recovery é uma ferramenta de recuperação de desastre. A migração e o Site Recovery do Azure Server compartilham alguns componentes de tecnologia comuns usados para replicação de dados, mas atendem a finalidades diferentes.

## <a name="architectural-components"></a>Componentes da arquitetura

![Arquitetura](./media/agent-based-replication-architecture/architecture.png)

A tabela resume os componentes usados para a migração baseada em agente.

**Componente** | **Detalhes** | **Instalação**
--- | --- | ---
**Dispositivo de replicação** | O dispositivo de replicação (servidor de configuração) é um computador local que atua como uma ponte entre o ambiente local e a ferramenta de migração de servidor de migrações para Azure. O dispositivo descobre o inventário de VM local, para que a migração do servidor do Azure possa orquestrar a replicação e a migração. O dispositivo tem dois componentes:<br/><br/> **Servidor de configuração**: conecta-se à migração do servidor de migrações para Azure e coordena a replicação.<br/> **Servidor de processo**: lida com a replicação de dados. Ele recebe dados da VM, compacta e criptografa-os e envia para a assinatura do Azure. Lá, a migração do servidor grava os dados em discos gerenciados. | Por padrão, o servidor de processo é instalado junto com o servidor de configuração no dispositivo de replicação.
**Serviço de mobilidade** | O serviço de mobilidade é um agente instalado em cada computador que você deseja replicar e migrar. Ele envia dados de replicação do computador para o servidor de processo. Há vários agentes de serviço de mobilidade diferentes disponíveis. | Os arquivos de instalação do serviço de mobilidade estão localizados no dispositivo de replicação. Você baixa e instala o agente de que precisa, de acordo com o sistema operacional e a versão do computador que você deseja replicar.

### <a name="mobility-service-installation"></a>Instalação do serviço de Mobilidade

Você pode implantar o serviço de mobilidade usando os seguintes métodos:

- **Instalação por push**: o serviço de mobilidade é instalado pelo servidor de processo quando você habilita a proteção para um computador. 
- **Instalar manualmente**: você pode instalar o serviço de mobilidade manualmente em cada computador por meio da interface do usuário ou do prompt de comando.

O serviço de mobilidade se comunica com o dispositivo de replicação e com máquinas replicadas. Se você tiver um software antivírus em execução no dispositivo de replicação, servidores de processo ou máquinas que estão sendo replicadas, as seguintes pastas deverão ser excluídas da verificação:


- C:\Arquivos de Programas\microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- Azure Site Recovery C:\ProgramData\Microsoft
- C:\Arquivos de programas (x86) \Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (em computadores Windows com o serviço de mobilidade instalado)

## <a name="replication-process"></a>Processo de replicação

1. Quando você habilita a replicação para uma VM, a replicação inicial para o Azure começa.
2. Durante a replicação inicial, o serviço de mobilidade lê os dados dos discos de computador e os envia para o servidor de processo.
3. Esses dados são usados para propagar uma cópia do disco na sua assinatura do Azure. 
4. Após a conclusão da replicação inicial, a replicação de alterações delta para o Azure é iniciada. A replicação é de nível de bloco e quase contínua.
4. O serviço de mobilidade intercepta gravações na memória do disco da VM, integrando-se ao subsistema de armazenamento do sistema operacional. Esse método evita operações de e/s de disco no computador de replicação para replicação incremental. 
5. As alterações controladas para um computador são enviadas para o servidor de processo na porta HTTPS 9443 de entrada. Essa porta pode ser modificada. O servidor de processo compacta e criptografa-o e o envia para o Azure. 

## <a name="ports"></a>Portas

**Vice** | **ligação**
--- | --- 
VMs | O serviço de mobilidade em execução nas VMs se comunica com o dispositivo de replicação local na porta HTTPS 443 de entrada para o gerenciamento de replicação.<br/><br/> As VMs enviam dados de replicação para o servidor de processo (em execução no dispositivo de replicação por padrão) na porta HTTPS 9443 de entrada. Essa porta pode ser modificada.
Dispositivo de replicação | O dispositivo de replicação orquestra a replicação com o Azure sobre a porta HTTPS 443 de saída.
Servidor de processo | O servidor de processo recebe dados de replicação, otimiza-os e criptografa-os e envia-os para o armazenamento do Azure pela porta 443 de saída.


## <a name="performance-and-scaling"></a>Desempenho e dimensionamento

Por padrão, você implanta um único dispositivo de replicação que executa o servidor de configuração e o servidor de processo. Se você estiver replicando apenas alguns computadores, essa implantação será suficiente. No entanto, se você estiver replicando e migrando centenas de computadores, um único servidor de processo poderá não ser capaz de lidar com todo o tráfego de replicação. Nesse caso, você pode implantar servidores de processo adicionais e de escala horizontal.

### <a name="site-recovery-deployment-planner-for-vmware"></a>Site Recovery Planejador de Implantações para VMware

Se você estiver replicando VMs VMware, poderá usar o [Site Recovery planejador de implantações](../site-recovery/site-recovery-deployment-planner.md) para VMware para ajudar a determinar os requisitos de desempenho, incluindo a taxa diária de alteração de dados e os servidores de processo necessários.

### <a name="replication-appliance-capacity"></a>Capacidade do dispositivo de replicação

Os valores nesta tabela podem ser usados para descobrir se você precisa de um servidor de processo adicional em sua implantação.

- Se sua taxa de alteração diária (taxa de rotatividade) tiver mais de 2 TB, implante um servidor de processo adicional.
- Se você estiver replicando mais de 200 computadores, implante um dispositivo de replicação adicional.

**CPU** | **Memória** | **Espaço livre para cache de dados** | **Taxa de rotatividade** | **Limites de replicação**
--- | --- | --- | --- | ---
8 vCPUs (2 soquetes * 4 núcleos \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB ou menos | < computadores 100 
12 vCPUs (2 soquetes * 6 núcleos \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB a 1 TB | 100-150 computadores.
16 vCPUs (2 soquetes * 8 núcleos \@ 2,5 GHz) | 32 G1 |  1 TB | 1 TB a 2 TB | 151-200 computadores.

### <a name="scale-out-process-server-sizing"></a>Dimensionamento do servidor de processo de expansão

Se você precisar implantar um servidor de processo de expansão, essa tabela poderá ajudá-lo a descobrir o dimensionamento do servidor.

**Servidor de processos** | **Espaço livre para cache de dados** | **Taxa de rotatividade** | **Limites de replicação**
--- | --- | --- | --- 
4 vCPUs (2 soquetes * 2 núcleos \@ 2,5 GHz), 8 GB de memória | 300 GB | 250 GB ou menos | Até 85 computadores 
8 vCPUs (2 soquetes * 4 núcleos \@ 2,5 GHz), 12 GB de memória | 600 GB | 251 GB a 1 TB    | 86-150 computadores.
12 vCPUs (2 soquetes * 6 núcleos \@ 2,5 GHz), 24 GB de memória | 1 TB | 1-2 TB | 151-225 computadores.

## <a name="control-upload-throughput"></a>Taxa de transferência de upload de controle


 O tráfego do VMware que Replica para o Azure passa por um servidor de processo específico. Você pode limitar a taxa de transferência de upload, limitando a largura de banda nos computadores que estão sendo executados como servidores de processo. Você pode influenciar a largura de banda usando essa chave do registro:

- O valor do registro HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Windows do Backup\Replication\UploadThreadsPerVM do Azure especifica o número de threads usados para transferência de dados (replicação inicial ou Delta) de um disco. Um valor mais alto aumenta a largura de banda de rede usada para replicação. O valor padrão é quatro. O valor máximo é 32. Monitorize o tráfego para otimizar o valor.
- Além disso, você pode limitar a largura de banda na máquina do servidor de processo da seguinte maneira:

    1. No computador do servidor de processo, abra o snap-in MMC do backup do Azure. Há um atalho na área de trabalho ou na pasta C:\Program Files\Microsoft Azure Recovery Services Agent\bin. 
    2. No snap-in, selecione **alterar propriedades**.
    3. Em **limitação**, selecione **habilitar limitação de uso de largura de banda da Internet para operações de backup**. Defina os limites de horas de trabalho e de folga. Intervalos válidos são de 512 kbps a 1.023 Mbps.


## <a name="next-steps"></a>Passos seguintes

Experimente a [migração de VM VMware](tutorial-migrate-vmware-agent.md) baseada em agente usando a migração de servidor de migrações para Azure.
