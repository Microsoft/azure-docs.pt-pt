---
title: Arquitetura de migração baseada em agente na migração do servidor de migrar do Azure
description: Fornece uma descrição geral da migração de VM do VMware com base em agente com o Azure Migrate migração do servidor.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 21c779587842c976ba93d7fa592a91ee714bc55c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811157"
---
# <a name="agent-based-migration-architecture"></a>Arquitetura de migração baseada em agente

Este artigo fornece uma descrição geral da arquitetura e os processos utilizados para a replicação baseada em agente com a ferramenta de migração do servidor de migrar do Azure.

[O Azure Migrate](migrate-services-overview.md) fornece um concentrador central para controlar a deteção, avaliação e migração de aplicações no local e cargas de trabalho e instâncias de VM do AWS/GCP, para o Azure. O hub fornece ferramentas do Azure Migrate para avaliação e migração, bem como ofertas de (ISV fornecedor) de terceiros independentes de software.

## <a name="agent-based-replication"></a>Replicação baseada em agente

Replicação baseada em agente na replicação do Azure Migrate servidor ferramenta é utilizada para migrar no local VMs de VMware e servidores físicos para o Azure. Também pode ser utilizado para migrar outros servidores no local virtualizado, bem como a nuvem privada e pública, as VMs, incluindo instâncias do AWS e GCP VMs.

Para a migração do VMware, a ferramenta de migração do servidor de migrar do Azure oferece duas opções:

- Migração usando a replicação baseada em agente, conforme descrito neste artigo.
- Replicação sem agente, para migrar VMs sem precisar instalar nada nos mesmos.

Saiba mais sobre [selecionar um método de migração do VMware](server-migrate-overview.md).

## <a name="server-migration-and-azure-site-recovery"></a>Migração do servidor e a recuperação de Site do Azure

Migração de servidor migrar do Azure é uma ferramenta de migração no local e cargas de trabalho de cloud pública para o Azure. Está otimizado para a migração. Site Recovery é uma ferramenta de recuperação após desastre. Migração do servidor e recuperação de sites do Azure partilham alguns componentes comuns de tecnologia utilizadas para a replicação de dados, mas servem objetivos diferentes.

## <a name="architectural-components"></a>Componentes da arquitetura

![Arquitetura](./media/agent-based-replication-architecture/architecture.png)

A tabela resume os componentes utilizados para a migração baseada em agente.

**Componente** | **Detalhes** | **Instalação**
--- | --- | ---
**Aplicação de replicação** | A aplicação de replicação (servidor de configuração) é uma máquina no local que age como uma ponte entre o ambiente no local e a ferramenta de migração do servidor de migrar do Azure. A aplicação da Deteta o inventário VM no local, para que a migração do servidor do Azure pode orquestrar a replicação e a migração. A aplicação tem dois componentes:<br/><br/> **Servidor de configuração**: Liga-se a migração do servidor de migrar do Azure e coordena a replicação.<br/> **Servidor de processos**: Processa a replicação de dados. Ele recebe dados da VM, compacta e criptografa- e envia para a subscrição do Azure. Aqui, a migração do servidor grava os dados para os managed disks. | Por predefinição, o servidor de processos está instalado, juntamente com o servidor de configuração na aplicação da replicação.
**Serviço de mobilidade** | O serviço de mobilidade é um agente instalado em cada máquina que pretende replicar e migrar. Envia dados de replicação da máquina para o servidor de processos. Um número de diferentes agentes de serviço de mobilidade estão disponíveis. | Ficheiros de instalação para o serviço de mobilidade estão localizados na aplicação da replicação. Transfira e instale o agente que tem, de acordo com o sistema operativo e a versão da máquina que pretende replicar.

### <a name="mobility-service-installation"></a>Instalação do serviço de Mobilidade

Pode implementar o serviço de mobilidade através dos seguintes métodos:

- **Instalação push**: O serviço de mobilidade está instalado pelo servidor de processos quando ativa a proteção para uma máquina. 
- **Instalar manualmente**: Pode instalar o serviço de mobilidade manualmente em cada máquina através da interface do Usuário ou linha de comandos.

O serviço de mobilidade comunica com a aplicação de replicação e replicados máquinas. Se tiver software antivírus em execução na aplicação de replicação, servidores de processos ou máquinas a ser replicadas, as seguintes pastas devem ser excluídas da verificação:


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86) \Microsoft do Azure Site Recovery
- C:\ProgramData\ASR\agent (em máquinas do Windows com o serviço de mobilidade instalado)

## <a name="replication-process"></a>Processo de replicação

1. Quando ativa a replicação para uma VM, começa a replicação inicial para o Azure.
2. Durante a replicação inicial, o serviço de mobilidade leia os dados de discos da máquina e envia-os para o servidor de processos.
3. Estes dados são utilizados para efetuar o seeding uma cópia do disco na sua subscrição do Azure. 
4. Depois de concluída a replicação inicial, começa a replicação das alterações delta para o Azure. A replicação é o nível de bloco e quase contínua.
4. As gravações de interceptações de serviço de mobilidade na memória de disco da VM, com a integração com o subsistema de armazenamento do sistema operativo. Este método evita operações de e/s de disco na máquina de replicação para a replicação incremental. 
5. Alterações registadas relativas a uma máquina são enviadas para o servidor de processos na porta HTTPS 9443 entradas. Esta porta pode ser modificada. O servidor de processos compacta e criptografa- e envia-os para o Azure. 

## <a name="ports"></a>Portas

**dispositivo** | **ligação**
--- | --- 
VMs | O serviço de mobilidade em execução em VMs comunica com a aplicação de replicação no local na porta HTTPS 443 entrada, para a gestão da replicação.<br/><br/> VMs enviam os dados de replicação para o servidor de processos (em execução na aplicação da replicação por predefinição) na porta HTTPS 9443 entrada. Esta porta pode ser modificada.
Aplicação de replicação | A aplicação de replicação orquestra a replicação com o Azure através da porta HTTPS 443 de saída.
Servidor de processos | O servidor de processos recebe dados de replicação, otimiza e criptografa- e envia-os para o armazenamento do Azure através da porta 443 saída.


## <a name="performance-and-scaling"></a>Desempenho e dimensionamento

Por predefinição, vai implementar uma aplicação de replicação único que executa o servidor de configuração e o servidor de processos. Se estiver a replicar apenas algumas máquinas, esta implementação é suficiente. No entanto, se estiver a replicar e migrar centenas de máquinas, um servidor de processos único pode não ser capaz de lidar com todo o tráfego de replicação. Neste caso, pode implementar servidores de processos adicionais, de escalamento horizontal.

### <a name="site-recovery-deployment-planner-for-vmware"></a>Site Recovery Deployment Planner de VMware

Se estiver a replicar VMs de VMware, pode utilizar o [Site Recovery Deployment Planner](../site-recovery/site-recovery-deployment-planner.md) do VMware para o ajudar a determinar requisitos de desempenho, incluindo os dados diários de alterar taxa e os servidores de processo, precisa.

### <a name="replication-appliance-capacity"></a>Capacidade da aplicação de replicação

Os valores nesta tabela podem ser utilizados para descobrir se precisa de um servidor de processos adicionais na sua implementação.

- Se a sua taxa de alteração diária (taxa de abandono) é a mais de 2 TB, implemente um servidor de processos adicionais.
- Se estiver a replicar mais de 200 máquinas, implemente uma aplicação de replicação.

**CPU** | **Memória** | **Espaço livre para a cache de dados** | **Taxa de abandono** | **Limites de replicação**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 núcleos \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB ou menos | < 100 máquinas 
12 vCPUs (2 sockets * 6 núcleos \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB a 1 TB | 100 150 máquinas.
16 vCPUs (2 sockets * 8 núcleos \@ 2,5 GHz) | 32 G1 |  1 TB | 1 TB para 2 TB | 151 200 máquinas.

### <a name="scale-out-process-server-sizing"></a>Dimensionamento do servidor de processo de escalamento horizontal

Se precisar de implementar um servidor de processos de escalamento horizontal, essa tabela pode ajudá-lo a descobrir o dimensionamento do servidor.

**Servidor de processos** | **Espaço livre para a cache de dados** | **Taxa de abandono** | **Limites de replicação**
--- | --- | --- | --- 
4 vCPUs (2 sockets * 2 núcleos \@ 2.5ghz), 8 GB de memória | 300 GB | 250 GB ou menos | Até 85 máquinas 
8 vCPUs (2 sockets * 4 núcleos \@ 2.5ghz), 12 GB de memória | 600 GB | 251 GB a 1 TB    | 86 150 máquinas.
12 vCPUs (2 sockets * 6 núcleos \@ 2.5ghz), 24 GB de memória | 1 TB | 1-2 TB | máquinas de 151 225.

## <a name="control-upload-throughput"></a>Débito de carregamento de controlo

Pode limitar a quantidade de largura de banda utilizada para carregar dados para o Azure em cada anfitrião de Hyper-V. Tenha cuidado. Se definir os valores muito baixos, que ele afetará negativamente a replicação e a migração de atraso.


1. Inicie sessão para o nó de anfitrião ou cluster Hyper-V.
2. Execute **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**, para abrir o snap-in MMC de cópia de segurança do Windows Azure.
3. No snap-in, selecione **alterar propriedades**.
4. Na **limitação**, selecione **ativar a limitação para operações de cópia de segurança de utilização de largura de banda de internet**. Defina os limites para o trabalho e de descanso. Intervalos válidos são entre 512 Kbps 1,023 Mbps.
I

### <a name="influence-upload-efficiency"></a>Eficiência de carregamento de influência

Se o ter largura de banda livre para a replicação e desejar para carregamentos de aumentar, pode aumentar o número de threads alocados para a tarefa de carregamento, da seguinte forma:

1. Abra o registo com Regedit.
2. Navegue até à chave HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. Aumente o valor para o número de threads utilizados para carregamento de dados para cada VM da replicação. O valor predefinido é 4 e o valor máximo é 32. 

## <a name="next-steps"></a>Passos Seguintes

Experimente com base em agente [migração de VM de VMware](tutorial-migrate-vmware-agent.md) utilizando a migração de servidor de migrar do Azure.
