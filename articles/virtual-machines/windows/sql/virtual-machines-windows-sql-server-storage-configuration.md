---
title: Configuração de armazenamento para VMs SQL Server | Microsoft Docs
description: Este tópico descreve como o Azure configura o armazenamento para SQL Server VMs durante o provisionamento (modelo de implantação do Gerenciador de recursos). Ele também explica como você pode configurar o armazenamento para suas VMs SQL Server existentes.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/05/2017
ms.author: mathoma
ms.openlocfilehash: e28478d31a674d742870344b33eac6b84c3ae584
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70123847"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Configuração de armazenamento para VMs SQL Server

Quando você configura uma imagem de máquina virtual SQL Server no Azure, o portal ajuda a automatizar sua configuração de armazenamento. Isso inclui a anexação de armazenamento à VM, tornando esse armazenamento acessível para SQL Server e configurá-lo para otimizar para seus requisitos de desempenho específicos.

Este tópico explica como o Azure configura o armazenamento para suas VMs SQL Servers durante o provisionamento e para VMs existentes. Essa configuração se baseia nas [práticas recomendadas de desempenho](virtual-machines-windows-sql-performance.md) para VMs do Azure em execução SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para usar as definições de configuração de armazenamento automatizado, sua máquina virtual requer as seguintes características:

* Provisionado com uma imagem da [Galeria de SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* Usa o [modelo de implantação do Gerenciador de recursos](../../../azure-resource-manager/resource-manager-deployment-model.md).
* Usa [SSDs Premium](../disks-types.md).

## <a name="new-vms"></a>Novas VMs

As seções a seguir descrevem como configurar o armazenamento para novas máquinas virtuais SQL Server.

### <a name="azure-portal"></a>Portal do Azure

Ao provisionar uma VM do Azure usando uma imagem da Galeria de SQL Server, você pode optar por configurar automaticamente o armazenamento para sua nova VM. Especifique o tamanho do armazenamento, os limites de desempenho e o tipo de carga de trabalho. A captura de tela a seguir mostra a folha de configuração de armazenamento usada durante o provisionamento de VM do SQL.

![SQL Server a configuração de armazenamento de VM durante o provisionamento](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Com base em suas escolhas, o Azure executa as seguintes tarefas de configuração de armazenamento depois de criar a VM:

* Cria e anexa o SSDs Premium à máquina virtual.
* Configura os discos de dados para serem acessíveis para SQL Server.
* Configura os discos de dados em um pool de armazenamento com base no tamanho especificado e nos requisitos de desempenho (IOPS e taxa de transferência).
* Associa o pool de armazenamento a uma nova unidade na máquina virtual.
* Otimiza essa nova unidade com base no tipo de carga de trabalho especificado (data warehouse, processamento transacional ou geral).

Para obter mais detalhes sobre como o Azure configura as configurações de armazenamento, consulte a [seção configuração de armazenamento](#storage-configuration). Para obter uma explicação completa de como criar uma VM SQL Server no portal do Azure, consulte [o tutorial de provisionamento](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Modelos de gerenciamento de recursos

Se você usar os seguintes modelos do Resource Manager, dois discos de dados Premium serão anexados por padrão, sem nenhuma configuração de pool de armazenamento. No entanto, você pode personalizar esses modelos para alterar o número de discos de dados Premium anexados à máquina virtual.

* [Criar VM com backup automatizado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Criar VM com aplicação de patch automatizada](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Criar VM com integração do AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>VMs existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para VMs SQL Server existentes, você pode modificar algumas configurações de armazenamento no portal do Azure. Abra o [recurso de máquinas virtuais do SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)e selecione **visão geral**. A página Visão geral do SQL Server mostra o uso de armazenamento atual de sua VM. Todas as unidades existentes na VM são exibidas neste gráfico. Para cada unidade, o espaço de armazenamento é exibido em quatro seções:

* Dados do SQL
* Registo do SQL
* Outro (armazenamento não SQL)
* Disponível

Para modificar as configurações de armazenamento, selecione **definir** em **configurações**. 

![Configurar o armazenamento para a VM de SQL Server existente](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

As opções de configuração que você vê variam dependendo se você já usou esse recurso antes. Ao usar o pela primeira vez, você pode especificar seus requisitos de armazenamento para uma nova unidade. Se você já usou esse recurso para criar uma unidade, poderá optar por estender o armazenamento dessa unidade.

### <a name="use-for-the-first-time"></a>Usar pela primeira vez

Se for a primeira vez que você usa esse recurso, você pode especificar o tamanho do armazenamento e os limites de desempenho para uma nova unidade. Essa experiência é semelhante ao que você veria no tempo de provisionamento. A principal diferença é que você não tem permissão para especificar o tipo de carga de trabalho. Essa restrição impede a interrupção de qualquer configuração de SQL Server existente na máquina virtual.

O Azure cria uma nova unidade com base em suas especificações. Nesse cenário, o Azure executa as seguintes tarefas de configuração de armazenamento:

* Cria e anexa discos de dados de armazenamento Premium à máquina virtual.
* Configura os discos de dados para serem acessíveis para SQL Server.
* Configura os discos de dados em um pool de armazenamento com base no tamanho especificado e nos requisitos de desempenho (IOPS e taxa de transferência).
* Associa o pool de armazenamento a uma nova unidade na máquina virtual.

Para obter mais detalhes sobre como o Azure configura as configurações de armazenamento, consulte a [seção configuração de armazenamento](#storage-configuration).

### <a name="add-a-new-drive"></a>Adicionar uma nova unidade

Se você já tiver configurado o armazenamento em sua VM SQL Server, a expansão do armazenamento traz duas novas opções. A primeira opção é adicionar uma nova unidade, que pode aumentar o nível de desempenho de sua VM.

No entanto, depois de adicionar a unidade, você deve executar algumas configurações manuais adicionais para alcançar o aumento de desempenho.

### <a name="extend-the-drive"></a>Estender a unidade

A outra opção para expandir o armazenamento é estender a unidade existente. Essa opção aumenta o armazenamento disponível para sua unidade, mas não aumenta o desempenho. Com os pools de armazenamento, você não pode alterar o número de colunas depois que o pool de armazenamento é criado. O número de colunas determina o número de gravações paralelas, que podem ser distribuídas entre os discos de dados. Portanto, todos os discos de dados adicionados não podem aumentar o desempenho. Eles só podem fornecer mais armazenamento para os dados que estão sendo gravados. Essa limitação também significa que, ao estender a unidade, o número de colunas determina o número mínimo de discos de dados que você pode adicionar. Portanto, se você criar um pool de armazenamento com quatro discos de dados, o número de colunas também será quatro. Sempre que você estender o armazenamento, deverá adicionar pelo menos quatro discos de dados.

![Estender uma unidade para uma VM do SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Configuração do armazenamento

Esta seção fornece uma referência para as alterações de configuração de armazenamento que o Azure executa automaticamente durante o provisionamento ou configuração de VM do SQL no portal do Azure.

* Se você tiver selecionado menos de dois TBs de armazenamento para sua VM, o Azure não criará um pool de armazenamento.
* Se você tiver selecionado pelo menos dois TBs de armazenamento para sua VM, o Azure configurará um pool de armazenamento. A próxima seção deste tópico fornece os detalhes da configuração do pool de armazenamento.
* A configuração de armazenamento automático sempre usa discos de dados p30 de [SSDs Premium](../disks-types.md) . Consequentemente, há um mapeamento 1:1 entre o número selecionado de terabytes e o número de discos de dados anexados à sua VM.

Para obter informações sobre preços, consulte a página [preços de armazenamento](https://azure.microsoft.com/pricing/details/storage) na guia **armazenamento em disco** .

### <a name="creation-of-the-storage-pool"></a>Criação do pool de armazenamento

O Azure usa as seguintes configurações para criar o pool de armazenamento em VMs SQL Server.

| Definição | Value |
| --- | --- |
| Tamanho da distribuição |256 KB (data warehousing); 64 KB (transacional) |
| Tamanhos de disco |1 TB cada |
| Cache |Leitura |
| Tamanho da alocação |tamanho da unidade de alocação NTFS de 64 KB |
| Inicialização instantânea de arquivo |Enabled |
| Bloquear páginas na memória |Enabled |
| Recuperação |Recuperação simples (sem resiliência) |
| Número de colunas |Número de discos de dados<sup>1</sup> |
| Local do TempDB |Armazenados em discos de dados<sup>2</sup> |

<sup>1</sup> depois que o pool de armazenamento é criado, você não pode alterar o número de colunas no pool de armazenamento.

<sup>2</sup> essa configuração se aplica somente à primeira unidade que você cria usando o recurso de configuração de armazenamento.

## <a name="workload-optimization-settings"></a>Configurações de otimização da carga de trabalho

A tabela a seguir descreve as três opções de tipo de carga de trabalho disponíveis e suas otimizações correspondentes:

| Tipo de carga de trabalho | Descrição | Otimizações |
| --- | --- | --- |
| **Geral** |Configuração padrão que dá suporte à maioria das cargas de trabalho |Nenhum |
| **Processamento transacional** |Otimiza o armazenamento para cargas de trabalho OLTP tradicionais do banco de dados |Sinalizador de rastreamento 1117<br/>Sinalizador de rastreamento 1118 |
| **Armazém de dados** |Otimiza o armazenamento para cargas de trabalho de análise e relatórios |Sinalizador de rastreamento 610<br/>Sinalizador de rastreamento 1117 |

> [!NOTE]
> Você só pode especificar o tipo de carga de trabalho ao provisionar uma máquina virtual SQL selecionando-a na etapa de configuração de armazenamento.

## <a name="next-steps"></a>Passos Seguintes

Para outros tópicos relacionados à execução de SQL Server em VMs do Azure, consulte [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
