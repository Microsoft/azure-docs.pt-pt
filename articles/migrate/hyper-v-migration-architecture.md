---
title: Como funciona a migração Hyper-V em Azure Migrate?
description: Conheça a migração Hyper-V com a Migração Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 8bca88fc63a7fc04a22d2a68adbe59259b07f50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185884"
---
# <a name="how-does-hyper-v-replication-work"></a>Como funciona a replicação do Hyper-V?

Este artigo fornece uma visão geral da arquitetura e dos processos utilizados quando migra VMs Hiper-V com a ferramenta migração do servidor de migração do servidor migratório Azure Migrate.

[A Azure Migrate](migrate-services-overview.md) fornece um centro central para acompanhar a descoberta, avaliação e migração das suas aplicações e cargas de trabalho no local, e VMs de nuvem privada/pública, para O Azure. O hub fornece ferramentas azure migrate para avaliação e migração, bem como ofertas de fornecedores de software independentes de terceiros (ISV).

## <a name="agentless-migration"></a>Migração sem agente

A ferramenta de migração do servidor de migração do servidor migratório Azure fornece replicação sem agente para VMs hiper-V no local, utilizando um fluxo de trabalho de migração otimizado para Hyper-V. Instala um agente de software apenas em anfitriões Hiper-V ou nós de cluster. Nada precisa de ser instalado em VMs Hiper-V.

## <a name="server-migration-and-azure-site-recovery"></a>Migração do servidor e recuperação do site azure

A Migração do Servidor Migratório Azure migrate é uma ferramenta para migrar no local de trabalho, e VMs baseados em nuvem, para Azure. A Recuperação do Site é uma ferramenta de recuperação de desastres. As ferramentas partilham alguns componentes tecnológicos comuns usados para a replicação de dados, mas servem diferentes propósitos. 


## <a name="architectural-components"></a>Componentes da arquitetura

![Arquitetura](./media/hyper-v-replication-architecture/architecture.png)



**Componente** | **Implantação** | 
--- | --- 
**Fornecedor de replicação** | O fornecedor de recuperação de sites do Microsoft Azure está instalado em anfitriões Hyper-V e registado na Migração do Servidor de Migração azure.<br/> O fornecedor orquestra a replicação para VMs hiper-V.
**Agente de Serviços de Recuperação** | O agente do Serviço de Recuperação do Microsoft Azure trata da replicação de dados. Trabalha com o fornecedor para replicar dados de VMs Hiper-V para Azure.<br/> Os dados replicados são enviados para uma conta de armazenamento na sua subscrição Azure. A ferramenta De migração do servidor processa os dados replicados e aplica-os a réplicas de discos na subscrição. Os discos de réplica são usados para criar os VMs Azure quando se migra.

- Os componentes são instalados por um único ficheiro de configuração, descarregado da Migração do Servidor Migratório Migratório Migratório Migratório Azure migratório no portal.
- O fornecedor e o aparelho utilizam ligações DE saída HTTPS porta 443 para comunicar com a Migração do Servidor Migratório Migratório Migratório Migratório Migratório Migratório Migratório Azure Migrate.
- As comunicações do fornecedor e do agente são seguras e encriptadas.


## <a name="replication-process"></a>Processo de replicação

1. Quando ativa a replicação de um VM Hiper-V, começa a replicação inicial.
2. Uma foto de VM Hiper-V é tirada.
3. Os VHDs no VM são replicados um a um, até serem todos copiados para o Azure. O tempo inicial de replicação depende do tamanho vm e da largura de banda da rede.
4. As alterações do disco que ocorrem durante a replicação inicial são rastreadas utilizando réplica hiper-V e armazenadas em ficheiros de registo (ficheiros hrl).
    - Os ficheiros de registo estão na mesma pasta que os discos.
    - Cada disco tem um ficheiro HRL associado que é enviado para o armazenamento secundário.
    - Os ficheiros de instantâneo e de registo consomem recursos do disco quando a replicação inicial está em curso.
4. Após os acabamentos iniciais da replicação, o instantâneo VM é eliminado e a replicação delta começa.
5. As alterações incrementais do disco são rastreadas em ficheiros hrl. Os registos de replicação são periodicamente enviados para uma conta de armazenamento Azure pelo agente dos Serviços de Recuperação.


## <a name="performance-and-scaling"></a>Desempenho e dimensionamento

O desempenho da replicação para Hyper-V é influenciado por fatores que incluem o tamanho VM, a taxa de mudança de dados (churn) dos VMs, o espaço disponível no anfitrião Hyper-V para armazenamento de ficheiros de registo, a largura de banda de upload para dados de replicação e armazenamento de alvos em Azure.

- Se estiver a replicar várias máquinas ao mesmo tempo, utilize o Planejador de Implantação de Recuperação de [Sítios Azure](../site-recovery/hyper-v-deployment-planner-overview.md) para hyper-V, para ajudar a otimizar a replicação.
- Planeie a sua replicação Hyper-V e distribua a replicação sobre as contas de armazenamento do Azure, de acordo com a capacidade.

### <a name="control-upload-throughput"></a>Controlo de carregamento de carregamento

Pode limitar a quantidade de largura de banda utilizada para fazer o upload de dados para o Azure em cada hospedeiro Hyper-V. Cuidado. Se definir os valores demasiado baixos, irá afetar negativamente a replicação e atrasar a migração.


1. Inscreva-se no nó de hospedeiro hiper-V ou cluster.
2. Executar **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**, para abrir o Snap-in de Backup Do Windows Azure.
3. No snap-in, selecione **Change Properties**.
4. Em **Throttling**, selecione Ativar o estrangulamento da largura de banda da **Internet para operações de backup**. Estabeleça os limites para o trabalho e o horário de trabalho. As gamas válidas são de 512 Kbps a 1.023 Mbps.
I

### <a name="influence-upload-efficiency"></a>Influenciar a eficiência do upload

Se tiver largura de banda sobressalente para replicação e quiser aumentar os uploads, pode aumentar o número de fios atribuídos para a tarefa de upload, da seguinte forma:

1. Abra o registo com Regedit.
2. Navegue para HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. Aumente o valor para o número de fios utilizados para o upload de dados para cada VM replicante. O valor padrão é de 4 e o valor máximo é 32. 




## <a name="next-steps"></a>Passos seguintes

Experimente [a migração Hyper-V](tutorial-migrate-hyper-v.md) usando a migração do servidor migratório azure migrate.
