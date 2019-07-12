---
title: Como funciona a migração do Hyper-V com migração do servidor de migrar do Azure? | Microsoft Docs
description: Fornece uma descrição geral da migração do Hyper-V na migração do servidor de migrar do Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 9148e76a9f2abd369ae595422d785a347e58dfab
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811716"
---
# <a name="how-does-hyper-v-replication-work"></a>Como funciona a replicação de Hyper-V?

Este artigo fornece uma descrição geral da arquitetura e processos utilizados para migrar VMs de Hyper-V com a ferramenta de migração do servidor de migrar do Azure.

[O Azure Migrate](migrate-services-overview.md) fornece um concentrador central para controlar a deteção, avaliação e migração de aplicações no local e cargas de trabalho e pública/privada na cloud VMs, para o Azure. O hub fornece ferramentas do Azure Migrate para avaliação e migração, bem como ofertas de (ISV fornecedor) de terceiros independentes de software.

## <a name="agentless-migration"></a>Migração sem agente

A ferramenta de migração do servidor de migrar do Azure fornece replicação sem agente para VMs de Hyper-V no local, com um fluxo de trabalho de migração otimizada para o Hyper-V. Instalar um agente de software apenas em anfitriões Hyper-V ou nós de cluster. Nada tem de ser instalado em VMs de Hyper-V.

## <a name="server-migration-and-azure-site-recovery"></a>Migração do servidor e a recuperação de Site do Azure

Migração de servidor migrar do Azure é uma ferramenta para migrar cargas de trabalho no local e VMs com base na cloud, para o Azure. Site Recovery é uma ferramenta de recuperação após desastre. As ferramentas de partilham alguns componentes comuns de tecnologia utilizadas para a replicação de dados, mas servem objetivos diferentes. 


## <a name="architectural-components"></a>Componentes da arquitetura

![Arquitetura](./media/hyper-v-replication-architecture/architecture.png)



**Componente** | **Implementação** | 
--- | --- 
**Fornecedor de replicação** | O fornecedor do Microsoft Azure Site Recovery é instalado em anfitriões Hyper-V e registado com a migração de servidor de migração do Azure.<br/> O fornecedor orquestra a replicação para as VMs de Hyper-V.
**Serviços de recuperação** | O agente do serviço de recuperação do Microsoft Azure processa os replicação de dados. Ele funciona com o fornecedor para replicar dados a partir de VMs de Hyper-V para o Azure.<br/> Os dados replicados são carregados para uma conta de armazenamento na sua subscrição do Azure. A migração do servidor ferramenta os processos dos dados replicados e a aplica em discos de réplica na subscrição. Os discos de réplica são utilizados para criar as VMs do Azure quando migrar.

- Componentes são instalados por um ficheiro de configuração única, transferido a partir do Azure Migrate migração do servidor no portal.
- O fornecedor e a aplicação utilizam ligações HTTPS de saída porta 443 para comunicar com o Azure Migrate migração do servidor.
- As comunicações do fornecedor e agente são seguras e encriptadas.


## <a name="replication-process"></a>Processo de replicação

1. Quando ativa a replicação para uma VM de Hyper-V, começa a replicação inicial.
2. É criado um instantâneo de VM de Hyper-V.
3. VHDs na VM são replicado um por um, até estarem todos copiados para o Azure. Tempo de replicação inicial depende do tamanho da VM e largura de banda de rede.
4. As alterações de disco que ocorrem durante a replicação inicial são controladas através da réplica do Hyper-V e armazenados nos ficheiros de registo (hrl ficheiros).
    - Ficheiros de registo estão na mesma pasta que os discos.
    - Cada disco tem um ficheiro de hrl associado que é enviado para o armazenamento secundário.
    - Os ficheiros de instantâneo e de registo consomem recursos do disco quando a replicação inicial está em curso.
4. Depois de concluída a replicação inicial, o instantâneo VM é eliminado e começa a replicação de diferenças.
5. Alterações de disco incrementais são controladas em arquivos de hrl. Os registos de replicação estão carregados periodicamente para uma conta de armazenamento do Azure, o agente dos serviços de recuperação.


## <a name="performance-and-scaling"></a>Desempenho e dimensionamento

Desempenho de replicação de Hyper-V é influenciado por fatores que incluem o tamanho da VM, a taxa de alteração (renovação) de dados das VMs, o espaço disponível no anfitrião Hyper-V para o armazenamento de ficheiros de registo, largura de banda de carregamento de dados de replicação e armazenamento de destino no Azure.

- Se estiver a replicar várias máquinas ao mesmo tempo, utilize o [do Azure Site Recovery Deployment Planner](../site-recovery/hyper-v-deployment-planner-overview.md) para Hyper-V, para ajudar a otimizar os replicação.
- Planear a sua replicação de Hyper-V e distribuir os replicação através de contas de armazenamento do Azure, em conformidade com a capacidade.

### <a name="control-upload-throughput"></a>Débito de carregamento de controlo

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

Experimente [migração do Hyper-V](tutorial-migrate-hyper-v.md) utilizando a migração de servidor de migrar do Azure.
