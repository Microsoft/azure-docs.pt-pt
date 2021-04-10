---
title: Configure o armazenamento para VMs do servidor SQL | Microsoft Docs
description: Este tópico descreve como o Azure configura o armazenamento para VMs do servidor SQL durante o fornecimento (modelo de implementação do Gestor de Recursos Azure). Também explica como pode configurar o armazenamento para os VMs do seu servidor SQL existente.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 982bd9239c5e95c9b7af09b5f54c5a09067ca7c6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565431"
---
# <a name="configure-storage-for-sql-server-vms"></a>Configure o armazenamento para VMs do servidor SQL
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo ensina-lhe como configurar o seu armazenamento para o seu SQL Server em Azure Virtual Machines (VMs).

Os VMs do Servidor SQL implantados através de imagens de mercado seguem automaticamente [as melhores práticas](performance-guidelines-best-practices-storage.md) de armazenamento predefinidas que podem ser modificadas durante a implementação. Algumas destas definições podem ser alteradas após a implementação. 


## <a name="prerequisites"></a>Pré-requisitos

Para utilizar as definições de configuração de armazenamento automatizado, a sua máquina virtual requer as seguintes características:

* A provisionada com uma [imagem de galeria SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) ou registada com a [extensão SQL IaaS]().
* Utiliza o [modelo de implementação do Gestor de Recursos.](../../../azure-resource-manager/management/deployment-models.md)
* Utiliza [SSDs premium.](../../../virtual-machines/disks-types.md)

## <a name="new-vms"></a>Novos VMs

As secções seguintes descrevem como configurar o armazenamento para novas máquinas virtuais SQL Server.

### <a name="azure-portal"></a>Portal do Azure

Ao forer um VM Azure utilizando uma imagem de galeria SQL Server, selecione alterar a **configuração** no separador Definições do **Servidor SQL** para abrir a página de Configuração de Armazenamento Otimizada de Desempenho. Pode deixar os valores por defeito ou modificar o tipo de configuração do disco que melhor se adequa às suas necessidades com base na sua carga de trabalho. 

![Screenshot que realça o separador de definições do SQL Server e a opção de configuração Change.](./media/storage-configuration/sql-vm-storage-configuration-provisioning.png)

Selecione o tipo de carga de trabalho para a qual está a implementar o seu Servidor SQL sob **a otimização do Armazenamento**. Com a opção de otimização **geral,** por padrão terá um disco de dados com 5000 IOPS máximo, e utilizará esta mesma unidade para o seu registo de dados, registo de transações e armazenamento tempDB. 

Selecionar o **processamento transacional** (OLTP) ou o armazenamento de dados criará um disco separado para **dados,** um disco separado para o registo de transações e utilizará sSD local para tempDB. Não existem diferenças de armazenamento entre **o processamento transacional** e o armazenamento **de dados,** mas altera a [configuração das suas listras e traça as bandeiras.](#workload-optimization-settings) A escolha do armazenamento premium define o caching para *ReadOnly* para a unidade de dados, e *nenhuma* para a unidade de registo de acordo com as [melhores práticas de desempenho do SQL Server VM](performance-guidelines-best-practices.md). 

![Configuração de armazenamento VM do servidor SQL durante o provisionamento](./media/storage-configuration/sql-vm-storage-configuration.png)

A configuração do disco é completamente personalizável para que possa configurar a topologia de armazenamento, o tipo de disco e os IOPs de que necessita para a carga de trabalho VM do seu SQL Server VM. Você também tem a capacidade de usar o UltraSSD (pré-visualização) como uma opção para o **tipo de disco** se o seu SQL Server VM estiver numa das regiões suportadas (Leste DOS EUA 2, Ásia do Sul e Norte da Europa) e tiver ativado discos ultra para a sua [subscrição.](../../../virtual-machines/disks-enable-ultra-ssd.md)  

Além disso, tem a capacidade de definir o cache para os discos. Os VMs Azure têm uma tecnologia de cache multi-nível chamada [Blob Cache](../../../virtual-machines/premium-storage-performance.md#disk-caching) quando usado com [Discos Premium](../../../virtual-machines/disks-types.md#premium-ssd). Blob Cache usa uma combinação da RAM máquina virtual e SSD local para cache. 

O cache do disco para SSD Premium pode ser *LidoOnly*, *ReadWrite* ou *Nenhum*. 

- *O caching ReadOnly* é altamente benéfico para os ficheiros de dados do SQL Server que são armazenados no Armazenamento Premium. *ReadOnly* caching traz baixa latência de leitura, IOPS de alta leitura, e produção como, leituras são realizadas a partir de cache, que está dentro da memória VM e SSD local. Estas leituras são muito mais rápidas do que as leituras do disco de dados, que é do armazenamento de Azure Blob. O armazenamento premium não conta as leituras servidas de cache para o disco IOPS e produção. Portanto, o seu aplicável é capaz de alcançar um total mais elevado de IOPS e produção. 
- *Nenhuma* configuração de cache deve ser utilizada para os discos que hospedam o ficheiro SQL Server Log, uma vez que o ficheiro de registo é escrito sequencialmente e não beneficia do cache *ReadOnly.* 
- O cache *ReadWrite* não deve ser utilizado para hospedar ficheiros SQL Server, uma vez que o SQL Server não suporta a consistência dos dados com a cache *ReadWrite.* Escreve a capacidade de resíduos da cache *de bolhas ReadOnly* e as latências aumentam ligeiramente se as escritas passarem pelas camadas de cache de bolhas *ReadOnly.* 


   > [!TIP]
   > Certifique-se de que a sua configuração de armazenamento corresponde às limitações impostas pelo tamanho VM selecionado. A escolha de parâmetros de armazenamento que excedam a tampa de desempenho do tamanho de VM resultará em aviso: `The desired performance might not be reached due to the maximum virtual machine disk performance cap` . Diminua os IOPs alterando o tipo de disco ou aumentando a limitação da tampa de desempenho aumentando o tamanho do VM. Isto não vai parar de provisões. 


Com base nas suas escolhas, a Azure executa as seguintes tarefas de configuração de armazenamento após a criação do VM:

* Cria e liga SSDs Premium à máquina virtual.
* Configura os discos de dados para serem acessíveis ao SQL Server.
* Configura os discos de dados num conjunto de armazenamento com base nos requisitos de tamanho e desempenho especificados (IOPS e produção).
* Associa a piscina de armazenamento a uma nova unidade na máquina virtual.
* Otimiza esta nova unidade com base no seu tipo de carga de trabalho especificada (armazenamento de dados, processamento transacional ou geral).

Para uma análise completa de como criar um SQL Server VM no portal Azure, consulte [o tutorial de provisionamento](../../../azure-sql/virtual-machines/windows/create-sql-vm-portal.md).

### <a name="resource-manager-templates"></a>Modelos do Resource Manager

Se utilizar os seguintes modelos de Gestor de Recursos, dois discos de dados premium são anexados por padrão, sem configuração de piscina de armazenamento. No entanto, é possível personalizar estes modelos para alterar o número de discos de dados premium que estão ligados à máquina virtual.

* [Criar VM com cópia de segurança automatizada](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Criar VM com Patching Automatizado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Criar VM com Integração AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>Modelo de início rápido

Pode utilizar o seguinte modelo de arranque rápido para implementar um SQL Server VM utilizando a otimização de armazenamento. 

* [Criar VM com otimização de armazenamento](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [Criar VM usando UltraSSD](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>VMs existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para os VMs do servidor SQL existentes, pode modificar algumas definições de armazenamento no portal Azure. Abra o [seu recurso de máquinas virtuais SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)e selecione **visão geral**. A página sql Server Overview mostra o uso atual do armazenamento do seu VM. Todas as unidades existentes no seu VM são apresentadas neste gráfico. Para cada unidade, o espaço de armazenamento apresenta quatro secções:

* Dados SQL
* Log SQL
* Outros (armazenamento não-SQL)
* Disponível

Para modificar as definições de armazenamento, **selecione Configurações de** **Configurações**. 

![Screenshot que realça a opção Configurar e a secção de Utilização de Armazenamento.](./media/storage-configuration/sql-vm-storage-configuration-existing.png)

Pode modificar as definições do disco para as unidades que foram configuradas durante o processo de criação do SQL Server VM. Selecionar **A unidade de extensão** abre a página de modificação de unidade, permitindo-lhe alterar o tipo de disco, bem como adicionar discos adicionais. 

![Configure armazenamento para o VM do servidor SQL existente](./media/storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="automated-changes"></a>Alterações automatizadas

Esta secção fornece uma referência para as alterações de configuração de armazenamento que o Azure executa automaticamente durante o fornecimento ou configuração do SQL Server VM no portal Azure.

* O Azure configura uma piscina de armazenamento a partir do armazenamento selecionado a partir do seu VM. A próxima secção deste tópico fornece detalhes sobre a configuração da piscina de armazenamento.
* A configuração automática de armazenamento utiliza sempre discos [de dados SSDs](../../../virtual-machines/disks-types.md) P30 premium. Consequentemente, existe um mapeamento de 1:1 entre o número selecionado de Terabytes e o número de discos de dados ligados ao seu VM.

Para obter informações sobre preços, consulte a página [de preços de Armazenamento](https://azure.microsoft.com/pricing/details/storage) no **separador Armazenamento de Discos.**

### <a name="creation-of-the-storage-pool"></a>Criação da piscina de armazenamento

O Azure utiliza as seguintes definições para criar a piscina de armazenamento em VMs do SQL Server.

| Definição | Valor |
| --- | --- |
| Tamanho de listra |256 KB (armazenagem de dados); 64 KB (Transacional) |
| Tamanhos de disco |1 TB cada |
| Cache |Ler |
| Tamanho da atribuição |64 KB NTFS tamanho da unidade de atribuição |
| Recuperação | Recuperação simples (sem resiliência) |
| Número de colunas |Número de discos de dados até 8<sup>1</sup> |


<sup>1</sup> Após a criação da piscina de armazenamento, não é possível alterar o número de colunas na piscina de armazenamento.


### <a name="workload-optimization-settings"></a>Definições de otimização da carga de trabalho

O quadro a seguir descreve as três opções de tipo de carga de trabalho disponíveis e as respetivas otimizações:

| Tipo de carga de trabalho | Description | Otimizações |
| --- | --- | --- |
| **Geral** |Definição padrão que suporta a maioria das cargas de trabalho |Nenhum |
| **Processamento transacional** |Otimiza o armazenamento para as cargas de trabalho tradicionais da base de dados OLTP |Bandeira de traço 1117<br/>Bandeira de traço 1118 |
| **Armazenagem de dados** |Otimiza o armazenamento para cargas de trabalho analíticas e reportando |Bandeira de traço 610<br/>Bandeira de traço 1117 |

> [!NOTE]
> Só é possível especificar o tipo de carga de trabalho quando forte uma máquina virtual SQL Server selecionando-a no passo de configuração de armazenamento.

## <a name="enable-caching"></a>Enable caching 

Mude a política de cache ao nível do disco. Pode fazê-lo utilizando o portal Azure, [o PowerShell](/powershell/module/az.compute/set-azvmdatadisk)ou o [Azure CLI](/cli/azure/vm/disk). 

Para alterar a sua política de caching no portal Azure, siga estes passos:

1. Pare o seu serviço SQL Server. 
1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 
1. Navegue para a sua máquina virtual, **selecione Discos** em **Definições**. 
   
   ![Screenshot mostrando a lâmina de configuração do disco VM no portal Azure.](./media/storage-configuration/disk-in-portal.png)

1. Escolha a política de cache apropriada para o seu disco a partir do drop-down. 

   ![Screenshot mostrando a configuração da política de cache do disco no portal Azure.](./media/storage-configuration/azure-disk-config.png)

1. Após a alteração entrar em vigor, reinicie o SQL Server VM e inicie o serviço SQL Server. 


## <a name="enable-write-accelerator"></a>Ativar o acelerador de escrita

Write Acceleration é uma funcionalidade de disco que só está disponível para as Máquinas Virtuais série M (VMs). O objetivo da aceleração da escrita é melhorar a latência de I/O de escritas contra o Azure Premium Storage quando você precisa de um dígito único de latência I/O devido a cargas de trabalho críticas oLTP de missão de alto volume ou ambientes de armazém de dados. 

Pare toda a atividade do SQL Server e desligue o serviço SQL Server antes de escriminar a sua política de aceleração de escrita. 

Se os discos estiverem listrados, ative a Write Acceleration para cada disco individualmente e o seu VM Azure deve ser desligado antes de escoar quaisquer alterações. 

Para ativar a Aceleração da Escrita utilizando o portal Azure, siga estes passos:

1. Pare o seu serviço SQL Server. Se os discos estiverem às riscas, desligue a máquina virtual. 
1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 
1. Navegue para a sua máquina virtual, **selecione Discos** em **Definições**. 
   
   ![Screenshot mostrando a lâmina de configuração do disco VM no portal Azure.](./media/storage-configuration/disk-in-portal.png)

1. Escolha a opção de cache com **o Acelerador de Escrita** para o seu disco a partir do drop-down. 

   ![Screenshot mostrando a política de cache do acelerador de escrita.](./media/storage-configuration/write-accelerator.png)

1. Após a alteração entrar em vigor, inicie o serviço de máquina virtual e SQL Server. 

## <a name="disk-striping"></a>Striping de disco

Para obter mais produção, pode adicionar discos de dados adicionais e utilizar a desmontagem do disco. Para determinar o número de discos de dados, analise a produção e largura de banda necessárias para os seus ficheiros de dados do SQL Server, incluindo o registo e a temperatura. Os limites de produção e largura de banda variam de acordo com o tamanho de VM. Para saber mais, consulte o [tamanho do VM](../../../virtual-machines/sizes.md)


* Para windows 8/Windows Server 2012 ou posterior, utilize [os Espaços de Armazenamento](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) com as seguintes orientações:

  1. Desloca (tamanho das listras) a 64 KB (65.536 bytes) para evitar o impacto do desempenho devido ao desalinhamento da partição. Isto tem de ser definido com o PowerShell.

  2. Definir contagem de colunas = número de discos físicos. Utilize o PowerShell ao configurar mais de 8 discos (não o Server Manager UI).

Por exemplo, o seguinte PowerShell cria uma nova piscina de armazenamento com o tamanho interleave a 64 KB e o número de colunas iguais à quantidade de disco físico na piscina de armazenamento:

  ```powershell
  $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
  
  New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
      -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
      -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
      –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
      -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
      -AllocationUnitSize 65536 -Confirm:$false 
  ```

  * Para o Windows 2008 R2 ou mais cedo, pode utilizar discos dinâmicos (volumes listrados de OS) e o tamanho das listras é sempre de 64 KB. Esta opção é depreciada a partir do Windows 8/Windows Server 2012. Para obter informações, consulte a declaração de suporte no [Serviço de Disco Virtual que está em transição para a API de Gestão de Armazenamento do Windows](https://docs.microsoft.com/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api).
 
  * Se estiver a utilizar [espaços de armazenamento diretos (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) com [instâncias de cluster de failover do servidor SQL,](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure)tem de configurar uma única piscina. Embora possam ser criados volumes diferentes nesse único pool, todos eles partilharão as mesmas características, como a mesma política de caching.
 
  * Determine o número de discos associados à sua piscina de armazenamento com base nas suas expectativas de carga. Tenha em mente que diferentes tamanhos de VM permitem diferentes números de discos de dados anexados. Para obter mais informações, consulte [tamanhos para máquinas virtuais.](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json)


## <a name="next-steps"></a>Passos seguintes

Para outros tópicos relacionados com a execução do SQL Server em VMs Azure, consulte [o SQL Server em Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).
