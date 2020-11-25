---
title: Como funciona a migração hyper-V em Azure Migrate?
description: Conheça a migração do Hiper-V com a Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 390a8a49e9a47ee5e6845d85fe4fe02f514708e8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013620"
---
# <a name="how-does-hyper-v-replication-work"></a>Como funciona a replicação do Hiper-V?

Este artigo fornece uma visão geral da arquitetura e processos utilizados quando migra hiper-VMs com a ferramenta de migração do servidor Azure Migrate.

[A Azure Migrate](migrate-services-overview.md) fornece um centro central para acompanhar a descoberta, avaliação e migração das suas aplicações e cargas de trabalho no local, e VMs de nuvem privada/pública, para Azure. O hub fornece ferramentas Azure Migrate para avaliação e migração, bem como ofertas de fornecedores de software independentes de terceiros (ISV).

## <a name="agentless-migration"></a>Migração sem agentes

A ferramenta de migração do servidor Azure Migrate fornece replicação sem agentes para hiper-VMs no local, usando um fluxo de trabalho de migração otimizado para Hiper-V. Instala um agente de software apenas em anfitriões Hiper-V ou nós de cluster. Nada precisa de ser instalado em VMs Hiper-V.

## <a name="server-migration-and-azure-site-recovery"></a>Migração de servidores e recuperação do site Azure

A migração do servidor Azure Migrate é uma ferramenta para migrar cargas de trabalho no local, e VMs baseados na nuvem, para Azure. A Recuperação do Local é uma ferramenta de recuperação de desastres. As ferramentas partilham alguns componentes tecnológicos comuns usados para a replicação de dados, mas servem propósitos diferentes. 


## <a name="architectural-components"></a>Componentes da arquitetura

![O diagrama mostra uma rede Source Hyper-V com um canal de dados H T P S para o Microsoft Azure, com detalhes explicados numa tabela.](./media/hyper-v-replication-architecture/architecture.png)



**Componente** | **Implementação** | 
--- | --- 
**Fornecedor de replicação** | O provedor de recuperação do site do Microsoft Azure está instalado em anfitriões Hiper-V e registado na Migração do Servidor migratório Azure.<br/> O fornecedor orquestra a replicação para VMs Hiper-V.
**Agente dos Serviços de Recuperação** | O agente do Serviço de Recuperação do Microsoft Azure trata da replicação de dados. Trabalha com o fornecedor para replicar dados de Hiper-VMs a Azure.<br/> Os dados replicados são enviados para uma conta de armazenamento na sua subscrição Azure. A ferramenta de migração do servidor processa os dados replicados e aplica-os a discos de réplica na subscrição. Os discos de réplica são usados para criar os VMs Azure quando migra.

- Os componentes são instalados por um único ficheiro de configuração, descarregado a partir da Migração do Servidor Azure Migrate no portal.
- O fornecedor e o aparelho utilizam ligações https de saída 443 para comunicar com a Migração do Servidor Azure Migrate.
- As comunicações do fornecedor e do agente são seguras e encriptadas.


## <a name="replication-process"></a>Processo de replicação

1. Quando ativa a replicação de um Hiper-VM, inicia-se a replicação inicial.
2. É tirada uma foto de VM Hiper-V.
3. Os VHDs nos VM são replicados um a um, até que todos sejam copiados para OZure. O tempo de replicação inicial depende do tamanho do VM e da largura de banda da rede.
4. As alterações de disco que ocorrem durante a replicação inicial são rastreadas utilizando a réplica Hyper-V e armazenadas em ficheiros de registo (ficheiros hrl).
    - Os ficheiros de registo estão na mesma pasta que os discos.
    - Cada disco tem um ficheiro HRL associado que é enviado para armazenamento secundário.
    - Os ficheiros de instantâneo e de registo consomem recursos do disco quando a replicação inicial está em curso.
4. Após o final da replicação inicial, o instantâneo VM é eliminado e a replicação delta começa.
5. As alterações incrementais do disco são rastreadas em ficheiros HRL. Os registos de replicação são carregados periodicamente para uma conta de armazenamento Azure pelo agente dos Serviços de Recuperação.


## <a name="performance-and-scaling"></a>Desempenho e dimensionamento

O desempenho de replicação do Hyper-V é influenciado por fatores que incluem o tamanho VM, a taxa de alteração de dados (churn) dos VMs, espaço disponível no anfitrião Hyper-V para armazenamento de ficheiros de log, upload da largura de banda para dados de replicação e armazenamento de alvo em Azure.

- Se estiver a replicar várias máquinas ao mesmo tempo, utilize o Planejador de [Implementação de Recuperação do Local Azure](../site-recovery/hyper-v-deployment-planner-overview.md) para hiper-V, para ajudar a otimizar a replicação.
- Planeie a sua replicação Hyper-V e distribua a replicação sobre as contas de armazenamento Azure, de acordo com a capacidade.

### <a name="control-upload-throughput"></a>Produção de upload de controlo

Pode limitar a quantidade de largura de banda usada para enviar dados para Azure em cada anfitrião Hiper-V. Cuidado. Se definir os valores demasiado baixos, irá afetar negativamente a replicação e atrasar a migração.


1. Inscreva-se no hospedeiro Hiper-V ou no nó de cluster.
2. Executar **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**, para abrir o snap-in MMC de backup do Windows Azure.
3. No snap-in, selecione **Change Properties**.
4. Em **Throttling**, selecione **Ativar o estrangulamento da largura de banda da Internet para operações de backup**. Definir os limites para o horário de trabalho e não-trabalho. Os intervalos válidos são de 512 Kbps a 1.023 Mbps.
I

### <a name="influence-upload-efficiency"></a>Eficiência do upload de influência

Se tiver largura de banda sobressalente para replicação e quiser aumentar os uploads, pode aumentar o número de linhas atribuídas para a tarefa de upload, da seguinte forma:

1. Abra o registo com Regedit.
2. Navegue para HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM chave
3. Aumente o valor para o número de fios utilizados para o upload de dados para cada VM replicante. O valor predefinido é 4 e o valor máximo é de 32. 




## <a name="next-steps"></a>Passos seguintes

Experimente a [migração hyper-V](tutorial-migrate-hyper-v.md) usando a migração do servidor Azure Migrate.
