---
title: Configuração do armazenamento para VMs do SQL Server | Documentos da Microsoft
description: Este tópico descreve como o Azure configura armazenamento para VMs do SQL Server, durante o aprovisionamento (modelo de implementação do Resource Manager). Também explica como configurar o armazenamento para as VMs do SQL Server existente.
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: craigg
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/05/2017
ms.author: ninarn
ms.openlocfilehash: 360ffb3d2c682d6bd2344cb3ae95447ff3df278d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076829"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Configuração do armazenamento para VMs do SQL Server

Ao configurar uma imagem de máquina virtual do SQL Server no Azure, o Portal de ajuda para automatizar a configuração de armazenamento. Isto inclui a anexar o armazenamento para a VM, disponibilizando que o armazenamento para o SQL Server e configurá-lo a otimizar para os seus requisitos de desempenho específicos.

Este tópico explica como o Azure configura armazenamento para as suas VMs do SQL Server durante o aprovisionamento e de VMs existentes. Esta configuração se baseia a [melhores práticas de desempenho](virtual-machines-windows-sql-performance.md) para VMs do Azure com o SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar as definições de configuração de armazenamento automática, a sua máquina virtual requer as seguintes características:

* Aprovisionado com um [imagem da galeria do SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* Utiliza a [modelo de implementação do Resource Manager](../../../azure-resource-manager/resource-manager-deployment-model.md).
* Utiliza [premium SSDs](../disks-types.md).

## <a name="new-vms"></a>Novas VMs

As secções seguintes descrevem como configurar o armazenamento para novas máquinas virtuais do SQL Server.

### <a name="azure-portal"></a>Portal do Azure

Quando aprovisionar uma VM do Azure com uma imagem de galeria do SQL Server, pode optar por configurar automaticamente o armazenamento para a sua nova VM. Especifique o tamanho de armazenamento, a limites de desempenho e o tipo de carga de trabalho. Captura de ecrã seguinte mostra o painel de configuração de armazenamento utilizado durante a VM do SQL Server de aprovisionamento.

![Configuração do armazenamento VM do SQL Server durante o aprovisionamento](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Com base em suas opções, o Azure efetua as seguintes tarefas de configuração de armazenamento depois de criar a VM:

* Cria e anexa o SSDs de premium para a máquina virtual.
* Configura os discos de dados a estar acessíveis ao SQL Server.
* Configura os discos de dados num agrupamento de armazenamento com base nos tamanho e o desempenho (IOPS e débito) requisitos especificados.
* Associa o agrupamento de armazenamento com uma nova unidade na máquina virtual.
* Otimiza esta nova unidade com base no seu tipo de carga de trabalho especificado (armazenamento de dados, processamento transacional ou gerais).

Para obter mais detalhes sobre como o Azure configura as definições de armazenamento, consulte a [seção de configuração de armazenamento](#storage-configuration). Para obter instruções completas de como criar uma VM do SQL Server no portal do Azure, consulte [do tutorial de aprovisionamento](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Modelos de gerir recursos

Se utilizar os seguintes modelos do Resource Manager, dois discos de dados premium estão ligados por padrão, sem qualquer configuração de agrupamento de armazenamento. No entanto, pode personalizar estes modelos para alterar o número de discos de dados de premium que estão ligados à máquina virtual.

* [Criar VM com a cópia de segurança automatizada](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Criar VM com a aplicação de patches automatizada](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Criar VM com a integração AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>VMs existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para VMs existentes do SQL Server, pode modificar algumas definições de armazenamento no portal do Azure. Abra sua [recurso de máquinas virtuais do SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource)e selecione **descrição geral**. A página de visão geral do SQL Server mostra a utilização do armazenamento atual da sua VM. Todas as unidades que existem na sua VM são apresentadas neste gráfico. Para cada unidade, o espaço de armazenamento é apresentado em quatro seções:

* Dados do SQL
* Registo SQL
* Outro (armazenamento de não-SQL)
* Disponível

Para modificar as definições de armazenamento, selecione **configurar** sob **definições**. 

![Configurar o armazenamento de VM de SQL Server existente](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

As opções de configuração que vê que varia consoante esteja utilizou esta funcionalidade antes. Quando utilizar pela primeira vez, pode especificar os requisitos de armazenamento para uma nova unidade. Se tiver utilizado anteriormente esta funcionalidade para criar uma unidade, pode optar por expandir o armazenamento dessa unidade.

### <a name="use-for-the-first-time"></a>Utilizar pela primeira vez

Se for a primeira vez que utilizar esta funcionalidade, pode especificar os limites de tamanho e o desempenho de armazenamento para uma nova unidade. Esta experiência é semelhante ao que veria em tempo de aprovisionamento. A principal diferença é que não tem permissão para especificar o tipo de carga de trabalho. Esta limitação impede interromper quaisquer configurações existentes do SQL Server na máquina virtual.

O Azure cria uma nova unidade com base em suas especificações. Neste cenário, o Azure efetua as seguintes tarefas de configuração de armazenamento:

* Cria e anexa os discos de dados de armazenamento premium para a máquina virtual.
* Configura os discos de dados a estar acessíveis ao SQL Server.
* Configura os discos de dados num agrupamento de armazenamento com base nos tamanho e o desempenho (IOPS e débito) requisitos especificados.
* Associa o agrupamento de armazenamento com uma nova unidade na máquina virtual.

Para obter mais detalhes sobre como o Azure configura as definições de armazenamento, consulte a [seção de configuração de armazenamento](#storage-configuration).

### <a name="add-a-new-drive"></a>Adicionar uma nova unidade

Se já tiver configurado o armazenamento na sua VM do SQL Server, expandir o armazenamento apresenta duas novas opções. A primeira opção é adicionar uma nova unidade, o que pode aumentar o nível de desempenho da sua VM.

No entanto, depois de adicionar a unidade, é necessário executar alguma configuração extra manual para alcançar o aumento de desempenho.

### <a name="extend-the-drive"></a>Expandir a unidade

A outra opção para expandir o armazenamento é estender o disco existente. Esta opção aumenta o armazenamento disponível para o seu disco, mas isso não aumentar o desempenho. Com agrupamentos de armazenamento, não é possível alterar o número de colunas depois de criar o agrupamento de armazenamento. O número de colunas determina o número de gravações paralelas, que podem ser distribuídos entre os discos de dados. Por conseguinte, os discos de dados adicionados não é possível aumentar o desempenho. Apenas podem fornecer mais armazenamento para os dados que estão sendo gravados. Esta limitação também significa que, quando expandir a unidade, o número de colunas determina o número mínimo de discos de dados que podem ser adicionados. Portanto, se criar um agrupamento de armazenamento com discos de dados de quatro, o número de colunas também é quatro. Sempre que expande o armazenamento, tem de adicionar, pelo menos, quatro discos de dados.

![Expandir uma unidade para uma VM do SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Configuração do armazenamento

Esta secção fornece uma referência para as alterações de configuração de armazenamento que o Azure efetua automaticamente durante o aprovisionamento da VM SQL ou de configuração no portal do Azure.

* Se selecionar menos de dois TB de armazenamento para a sua VM, o Azure não cria um agrupamento de armazenamento.
* Se tiver selecionado, pelo menos, dois TB de armazenamento para a sua VM, o Azure configura um agrupamento de armazenamento. A secção seguinte deste tópico fornece os detalhes da configuração do agrupamento de armazenamento.
* Configuração de armazenamento automática sempre utiliza [premium SSDs](../disks-types.md) P30 discos de dados. Conseqüentemente, há um mapeamento 1:1 entre o número selecionado de Terabytes e o número de discos de dados ligados à sua VM.

Para obter informações sobre preços, consulte a [preços de armazenamento](https://azure.microsoft.com/pricing/details/storage) página no **armazenamento de disco** separador.

### <a name="creation-of-the-storage-pool"></a>Criação do agrupamento de armazenamento

O Azure utiliza as seguintes definições para criar o agrupamento de armazenamento em VMs do SQL Server.

| Definição | Value |
| --- | --- |
| Tamanho do stripe |256 KB (armazenamento de dados); 64 KB (transacional) |
| Tamanhos de disco |1 TB |
| Cache |Leitura |
| Tamanho de alocação |Tamanho da unidade de alocação NTFS 64 KB |
| Inicialização instantânea de arquivo |Enabled |
| Bloquear páginas na memória |Enabled |
| Recuperação |Recuperação simples (sem resiliência) |
| Número de colunas |Número de discos de dados<sup>1</sup> |
| Localização de TempDB |Armazenados em discos de dados<sup>2</sup> |

<sup>1</sup> depois de criar o agrupamento de armazenamento, não é possível alterar o número de colunas no agrupamento de armazenamento.

<sup>2</sup> esta definição aplica-se apenas a primeira unidade de criar a utilizar a funcionalidade de configuração de armazenamento.

## <a name="workload-optimization-settings"></a>Definições de otimização de carga de trabalho

A tabela seguinte descreve as opções de tipo de carga de trabalho de três disponíveis e respetivos otimizações correspondentes:

| Tipo de carga de trabalho | Descrição | Otimizações |
| --- | --- | --- |
| **Geral** |Configuração padrão, que suporta a maioria das cargas de trabalho |Nenhuma |
| **Processamento de transações** |Otimiza o armazenamento para cargas de trabalho OLTP de bases de dados tradicionais |Trace Flag 1117<br/>Trace Flag 1118 |
| **Armazém de dados** |Otimiza o armazenamento para cargas de trabalho analíticas e de relatórios |Trace Flag 610<br/>Trace Flag 1117 |

> [!NOTE]
> Só pode especificar o tipo de carga de trabalho quando aprovisionar uma máquina virtual do SQL ao selecioná-la no passo de configuração de armazenamento.

## <a name="next-steps"></a>Passos Seguintes

Para outros tópicos relacionados ao executar o SQL Server em VMs do Azure, consulte [SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).
