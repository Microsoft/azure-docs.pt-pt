---
title: Configurar sempre no grupo de disponibilidade com DH2i DxEnterprise em execução em Máquinas Virtuais Azure baseadas em Linux
description: Use DH2i DxEnterprise como gestor de cluster para alcançar uma alta disponibilidade com um grupo de disponibilidade no SQL Server em Máquinas Virtuais Linux Azure
ms.date: 03/04/2021
ms.service: virtual-machines-linux
ms.topic: tutorial
author: amvin87
ms.author: amitkh
ms.reviewer: vanto
ms.openlocfilehash: 07752eb5c7f18a8952c43e77afed78b06432aca6
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568541"
---
# <a name="tutorial---setup-a-three-node-always-on-availability-group-with-dh2i-dxenterprise-running-on-linux-based-azure-virtual-machines"></a>Tutorial - Configurar um grupo de três nós sempre em disponibilidade com DH2i DxEnterprise em execução em Máquinas Virtuais Azure baseadas em Linux

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este tutorial explica como configurar o SQL Server Always On availability group com DH2i DxEnterprise a funcionar em Máquinas Virtuais Azure (VMs) baseadas em Linux. 

Para obter mais informações sobre a DxEnterprise, consulte [DH2i DxEnterprise](https://dh2i.com/dxenterprise-availability-groups/).

> [!NOTE]
> A Microsoft suporta o movimento de dados, o grupo de disponibilidade e os componentes do SQL Server. Contacte a DH2i para apoio relacionado com a documentação do cluster DH2i DxEnterprise, para a gestão do cluster e do quórum.
 

Este tutorial consiste nos seguintes passos:

> [!div class="checklist"]
> * Instale o SQL Server em todas as máquinas virtuais Azure (VMs) que farão parte do grupo de disponibilidade.
> * Instale dxEnterprise em todos os VMs e configuure o cluster DxEnterprise.
> * Crie os anfitriões virtuais para fornecer suporte de failover e alta disponibilidade, adicione um grupo de disponibilidade e base de dados ao grupo de disponibilidade.
> * Criar o balançador de carga azure interno para o ouvinte do grupo Disponibilidade (opcional).
> * Efetuar uma falha manual ou automática.

Neste tutorial, vamos criar um cluster DxEnterprise usando [a DxAdmin Client UI](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/). Opcionalmente, também pode configurar o cluster utilizando a interface de linha de comando [DxCLI.](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/) Por exemplo, usamos quatro VMs. Três desses VMs estão a correr Ubuntu 18.04, e fazem parte do aglomerado de três nós. O quarto VM está a executar o Windows 10 com a ferramenta DxAdmin para gerir e configurar o cluster.

## <a name="prerequisites"></a>Pré-requisitos

- Crie quatro VMs em Azure. Siga o [Quickstart: Crie uma máquina virtual Linux em artigo do portal Azure](../../../virtual-machines/linux/quick-create-portal.md) para criar máquinas virtuais baseadas em Linux. Da mesma forma, para criar a máquina virtual baseada no Windows, siga o [Quickstart: Crie uma máquina virtual Windows no artigo do portal Azure.](../../../virtual-machines/windows/quick-create-portal.md)
- Instale .NET 3.1 em todos os VMs baseados em Linux que vão fazer parte do cluster. Siga as instruções [aqui](/dotnet/core/install/linux) documentadas com base no sistema operativo Linux que escolher.
- Será necessária uma licença DxEnterprise válida com funcionalidades de gestão do grupo de disponibilidade ativadas. Para mais informações, consulte [o DxEnterprise Free Trial](https://dh2i.com/trial/) sobre como obter um teste gratuito.

## <a name="install-sql-server-on-all-the-azure-vms-that-will-be-part-of-the-availability-group"></a>Instale o SQL Server em todos os VMs Azure que farão parte do grupo de disponibilidade

Neste tutorial, estamos a criar um cluster baseado em três nós linux que executa o grupo de disponibilidade. Siga a documentação para [a instalação do SQL Server no Linux](/sql/linux/sql-server-linux-overview#install) com base na escolha da sua plataforma Linux. Recomendamos também que instale as [ferramentas SQL Server](/sql/linux/sql-server-linux-setup-tools) para este tutorial.
 
> [!NOTE]
> Certifique-se de que o Sistema Linux OS que escolhe é uma distribuição comum que é suportada tanto pela [DH2i DxEnterprise (Ver Secção de Requisitos mínimos do sistema)](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) como pelo [Microsoft SQL Server](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms).
>
> Neste exemplo, utilizamos o Ubuntu 18.04, que é suportado tanto pelo DH2i DxEnterprise como pelo Microsoft SQL Server.

Para este tutorial, não vamos instalar o SQL Server no Windows VM, uma vez que este nó não vai fazer parte do cluster, e é usado apenas para gerir o cluster usando o DxAdmin.

Depois de completar este passo, deverá ter ferramentas SQL Server e [SQL Server](/sql/linux/sql-server-linux-setup-tools) (opcionalmente) instaladas nos três VMs baseados em Linux que participarão no grupo de disponibilidade.
 
## <a name="install-dxenterprise-on-all-the-vms-and-configure-the-cluster"></a>Instale dxEnterprise em todos os VMs e configure o cluster

Neste passo, vamos instalar DH2i DxEnterprise para Linux nos três LMs Linux. A tabela a seguir descreve o papel que cada servidor desempenha no cluster:

| Número de VMs | Dh2i DxEnterprise papel | Função de réplica de grupo de disponibilidade do Microsoft SQL Server |
|--|--|--|
| 1 | Nó de cluster - baseado em Linux | Primário |
| 1 | Nó de cluster - baseado em Linux | Secundário - Compromisso sincronizado |
| 1 | Nó de cluster - baseado em Linux | Secundário - Compromisso sincronizado |
| 1 | Cliente DxAdmin | ND |


Para instalar dxEnterprise nos três nós baseados em Linux, siga a documentação DH2i DxEnterprise com base no sistema operativo Linux que escolher. Instale dxEnterprise utilizando qualquer um dos métodos listados abaixo.

- **Ubuntu**
    - [Guia de início rápido de instalação de repo](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-ubuntu-installation-quick-start-guide/)
    - [Guia de início rápido de extensão](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Guia de início rápido de imagem de marketplace](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)
- **RHEL**
    - [Guia de início rápido de instalação de repo](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-rhel-centos-installation-quick-start-guide/)
    - [Guia de início rápido de extensão](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Guia de início rápido de imagem de marketplace](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)

Para instalar apenas a ferramenta do cliente DxAdmin no Windows VM, siga o [Guia de Arranque Rápido do Cliente DxAdmin UI](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/).

Após este passo, deverá ter o cluster DxEnterprise criado nos VMs Linux e o cliente DxAdmin instalado na máquina do Cliente Windows. 

> [!NOTE]
> Também pode criar um cluster de três nós onde um dos nós é adicionado como *modo apenas de configuração*, como descrito [aqui](/sql/database-engine/availability-groups/windows/availability-modes-always-on-availability-groups#SupportedAvModes) para permitir a falha automática. 

## <a name="create-the-virtual-hosts-to-provide-failover-support-and-high-availability"></a>Crie os anfitriões virtuais para fornecer suporte de failover e alta disponibilidade

Neste passo, vamos criar um grupo virtual de anfitriões, disponibilidade, e depois adicionar uma base de dados, tudo usando a DxAdmin UI.   

> [!NOTE]
> Durante este passo, as instâncias do SQL Server serão reiniciadas para ativar Always On. 

Ligue-se à máquina cliente Do Windows que executa a DxAdmin para ligar ao cluster criado no degrau acima. Siga os passos documentados nos [Grupos de Disponibilidade MSSQL com a DxAdmin](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-mssql-availability-groups-with-dxadmin-quick-start-guide/) para ativar a Always On e criar o grupo virtual de anfitriões e disponibilidade. 

> [!TIP]
> Antes de adicionar as bases de dados, certifique-se de que a base de dados é criada e monitorizada na instância primária do SQL Server.  

## <a name="create-the-internal-azure-load-balancer-for-listener-optional"></a>Criar o esquilibrador de carga azure interno para o ouvinte (opcional)

Neste passo opcional, pode criar e configurar o equilibrador Azure Load que detém os endereços IP para os ouvintes do grupo de disponibilidade. Para obter mais informações sobre o Balançador de Carga [Azure, consulte o Balançador de Carga Azure](../../../load-balancer/load-balancer-overview.md). Para configurar o equilibrador de carga Azure e o ouvinte do grupo de disponibilidade utilizando o DxAdmin, siga o Guia de Arranque Rápido do Balancer de Carga DxEnterprise [Azure](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-load-balancer-quick-start-guide/).

Após este passo, deverá ter um ouvinte de grupo de disponibilidade criado e mapeado para o equilibrador de carga Azure Interno.

## <a name="test-manual-or-automatic-failover"></a>Teste manual ou falha automática

Para o teste automático de failover, pode avançar e derrubar a réplica primária (desligar a máquina virtual do portal Azure). Isto irá replicar a indisponibilidade súbita do nó primário. O comportamento esperado é:
- O cluster manager promove uma das réplicas secundárias do grupo de disponibilidade para o primário.
- A réplica primária falhada une-se automaticamente ao cluster depois de ser re-volta. O gestor do cluster promove-o para a réplica secundária.

 
Também pode efetuar uma falha manual seguindo os passos abaixo mencionados:

1. Ligue-se ao cluster via DxAdmin   
1. Expandir o anfitrião virtual para o grupo de disponibilidade
1. Clique com o botão direito no nó-alvo/réplica secundária e selecione **Iniciar a hospedagem no Membro** para iniciar a falha 

Para mais informações sobre mais operações dentro da DxEnterprise, aceda ao [Guia DxEnterprise de administração](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) e [ao DxEnterprise DxCLI Guide](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [Grupos de Disponibilidade no Linux](/sql/linux/sql-server-linux-availability-group-overview)
- [Quickstart: Criar máquina virtual Linux no portal Azure](../../../virtual-machines/linux/quick-create-portal.md)
- [Início Rápido: Criar uma máquina virtual do Windows no portal do Azure](../../../virtual-machines/windows/quick-create-portal.md)
- [Plataformas suportadas para SQL Server 2019 no Linux](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms)