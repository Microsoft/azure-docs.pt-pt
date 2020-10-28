---
title: Configuração de armazenamento para VMs do servidor SQL Microsoft Docs
description: Este tópico descreve como o Azure configura o armazenamento para VMs do servidor SQL durante o fornecimento (modelo de implementação do Gestor de Recursos Azure). Também explica como pode configurar o armazenamento para os VMs do seu servidor SQL existente.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: ebeee228d8c936732465359dfa264d822cbecb1e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793080"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Configuração de armazenamento das VMs do SQL Server
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ao configurar uma imagem virtual do SQL Server (VM) em Azure, o portal Azure ajuda a automatizar a sua configuração de armazenamento. Isto inclui a anexação do armazenamento ao VM, tornando esse armazenamento acessível ao SQL Server, e configurando-o para otimizar os seus requisitos de desempenho específicos.

Este tópico explica como o Azure configura o armazenamento para os seus VMs do servidor SQL, tanto durante o fornecimento como para os VMs existentes. Esta configuração baseia-se nas [melhores práticas](performance-guidelines-best-practices.md) de desempenho para VMs Azure que executam o SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar as definições de configuração de armazenamento automatizado, a sua máquina virtual requer as seguintes características:

* A provisionada com uma [imagem de galeria sql server](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo).
* Utiliza o [modelo de implementação do Gestor de Recursos.](../../../azure-resource-manager/management/deployment-models.md)
* Utiliza [SSDs premium.](../../../virtual-machines/disks-types.md)

## <a name="new-vms"></a>Novos VMs

As secções seguintes descrevem como configurar o armazenamento para novas máquinas virtuais SQL Server.

### <a name="azure-portal"></a>Portal do Azure

Ao forer um VM Azure utilizando uma imagem de galeria SQL Server, selecione alterar a **configuração** no separador Definições do **Servidor SQL** para abrir a página de Configuração de Armazenamento Otimizada de Desempenho. Pode deixar os valores por defeito ou modificar o tipo de configuração do disco que melhor se adequa às suas necessidades com base na sua carga de trabalho. 

![Configuração de armazenamento VM do servidor SQL durante o provisionamento](./media/storage-configuration/sql-vm-storage-configuration-provisioning.png)

Selecione o tipo de carga de trabalho para a qual está a implementar o seu Servidor SQL sob **a otimização do Armazenamento** . Com a opção de otimização **geral,** por padrão terá um disco de dados com 5000 IOPS máximo, e utilizará esta mesma unidade para o seu registo de dados, registo de transações e armazenamento tempDB. Selecionar o **processamento transacional** (OLTP) ou o armazenamento de dados criará um disco separado para **dados,** um disco separado para o registo de transações e utilizará sSD local para tempDB. Não existem diferenças de armazenamento entre **o processamento transacional** e o armazenamento **de dados,** mas altera a [configuração das suas listras e traça as bandeiras.](#workload-optimization-settings) A escolha do armazenamento premium define o caching para *ReadOnly* para a unidade de dados, e *nenhuma* para a unidade de registo de acordo com as [melhores práticas de desempenho do SQL Server VM](performance-guidelines-best-practices.md). 

![Configuração de armazenamento VM do servidor SQL durante o provisionamento](./media/storage-configuration/sql-vm-storage-configuration.png)

A configuração do disco é completamente personalizável para que possa configurar a topologia de armazenamento, o tipo de disco e os IOPs de que necessita para a carga de trabalho VM do seu SQL Server VM. Você também tem a capacidade de usar o UltraSSD (pré-visualização) como uma opção para o **tipo de disco** se o seu SQL Server VM estiver numa das regiões suportadas (Leste DOS EUA 2, Ásia do Sul e Norte da Europa) e tiver ativado discos ultra para a sua [subscrição.](../../../virtual-machines/disks-enable-ultra-ssd.md)  

Além disso, tem a capacidade de definir o cache para os discos. Os VMs Azure têm uma tecnologia de cache multi-nível chamada [Blob Cache](../../../virtual-machines/premium-storage-performance.md#disk-caching) quando usado com [Discos Premium](../../../virtual-machines/disks-types.md#premium-ssd). Blob Cache usa uma combinação da RAM máquina virtual e SSD local para cache. 

O cache do disco para SSD Premium pode ser *LidoOnly* , *ReadWrite* ou *Nenhum* . 

- *O caching ReadOnly* é altamente benéfico para os ficheiros de dados do SQL Server que são armazenados no Armazenamento Premium. *ReadOnly* caching traz baixa latência de leitura, IOPS de alta leitura, e produção como, leituras são realizadas a partir de cache, que está dentro da memória VM e SSD local. Estas leituras são muito mais rápidas do que as leituras do disco de dados, que é do armazenamento de Azure Blob. O armazenamento premium não conta as leituras servidas de cache para o disco IOPS e produção. Portanto, o seu aplicável é capaz de alcançar um total mais elevado de IOPS e produção. 
- *Nenhuma* configuração de cache deve ser utilizada para os discos que hospedam o ficheiro SQL Server Log, uma vez que o ficheiro de registo é escrito sequencialmente e não beneficia do cache *ReadOnly.* 
- O cache *ReadWrite* não deve ser utilizado para hospedar ficheiros SQL Server, uma vez que o SQL Server não suporta a consistência dos dados com a cache *ReadWrite.* Escreve a capacidade de resíduos da cache *de bolhas ReadOnly* e as latências aumentam ligeiramente se as escritas passarem pelas camadas de cache de bolhas *ReadOnly.* 


   > [!TIP]
   > Certifique-se de que a sua configuração de armazenamento corresponde às limitações impostas pelo tamanho VM selecionado. A escolha de parâmetros de armazenamento que excedam a tampa de desempenho do tamanho VM resultará em erro: `The desired performance might not be reached due to the maximum virtual machine disk performance cap.` . Diminua os IOPs alterando o tipo de disco ou aumentando a limitação da tampa de desempenho aumentando o tamanho do VM. 


Com base nas suas escolhas, a Azure executa as seguintes tarefas de configuração de armazenamento após a criação do VM:

* Cria e liga SSDs premium à máquina virtual.
* Configura os discos de dados para serem acessíveis ao SQL Server.
* Configura os discos de dados num conjunto de armazenamento com base nos requisitos de tamanho e desempenho especificados (IOPS e produção).
* Associa a piscina de armazenamento a uma nova unidade na máquina virtual.
* Otimiza esta nova unidade com base no seu tipo de carga de trabalho especificada (armazenamento de dados, processamento transacional ou geral).

Para mais detalhes sobre como o Azure configura as definições de armazenamento, consulte a [secção de configuração de armazenamento](#storage-configuration). Para uma análise completa de como criar um SQL Server VM no portal Azure, consulte [o tutorial de provisionamento](../../../azure-sql/virtual-machines/windows/create-sql-vm-portal.md).

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

Para os VMs do servidor SQL existentes, pode modificar algumas definições de armazenamento no portal Azure. Abra o [seu recurso de máquinas virtuais SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)e selecione **visão geral** . A página sql Server Overview mostra o uso atual do armazenamento do seu VM. Todas as unidades existentes no seu VM são apresentadas neste gráfico. Para cada unidade, o espaço de armazenamento apresenta quatro secções:

* Dados SQL
* Log SQL
* Outros (armazenamento não-SQL)
* Disponível

Para modificar as definições de armazenamento, **selecione Configurações de** **Configurações** . 

![Configure armazenamento para o VM do servidor SQL existente](./media/storage-configuration/sql-vm-storage-configuration-existing.png)

Pode modificar as definições do disco para as unidades que foram configuradas durante o processo de criação do SQL Server VM. Selecionar **A unidade de extensão** abre a página de modificação de unidade, permitindo-lhe alterar o tipo de disco, bem como adicionar discos adicionais. 

![Configure armazenamento para o VM do servidor SQL existente](./media/storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="storage-configuration"></a>Configuração do armazenamento

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


## <a name="workload-optimization-settings"></a>Definições de otimização da carga de trabalho

O quadro a seguir descreve as três opções de tipo de carga de trabalho disponíveis e as respetivas otimizações:

| Tipo de carga de trabalho | Descrição | Otimizações |
| --- | --- | --- |
| **Geral** |Definição padrão que suporta a maioria das cargas de trabalho |Nenhum |
| **Processamento transacional** |Otimiza o armazenamento para as cargas de trabalho tradicionais da base de dados OLTP |Bandeira de traço 1117<br/>Bandeira de traço 1118 |
| **Armazenagem de dados** |Otimiza o armazenamento para cargas de trabalho analíticas e reportando |Bandeira de traço 610<br/>Bandeira de traço 1117 |

> [!NOTE]
> Só é possível especificar o tipo de carga de trabalho quando forte uma máquina virtual SQL Server selecionando-a no passo de configuração de armazenamento.

## <a name="next-steps"></a>Passos seguintes

Para outros tópicos relacionados com a execução do SQL Server em VMs Azure, consulte [o SQL Server em Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).