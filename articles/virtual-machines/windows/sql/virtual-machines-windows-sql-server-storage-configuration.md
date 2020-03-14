---
title: Configuração de armazenamento para VMs do Servidor SQL  Microsoft Docs
description: Este tópico descreve como o Azure configura o armazenamento para VMs do Servidor SQL durante o provisionamento (modelo de implementação do Gestor de Recursos). Também explica como pode configurar o armazenamento para os vMs do Servidor SQL existentes.
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
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 9d8fce0772f13c6e009b2441ecd85779a7622c5c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243201"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Configuração de armazenamento para VMs do Servidor SQL

Quando configura uma imagem virtual do SQL Server em Azure, o Portal ajuda a automatizar a sua configuração de armazenamento. Isto inclui a fixação do armazenamento ao VM, tornando esse armazenamento acessível ao SQL Server, e configurando-o para otimizar para os seus requisitos de desempenho específicos.

Este tópico explica como o Azure configura o armazenamento para os seus VMs do Servidor SQL, tanto durante o fornecimento como para os VMs existentes. Esta configuração baseia-se nas [melhores práticas](virtual-machines-windows-sql-performance.md) de desempenho para Os VMs Azure que executam o SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar as definições de configuração de armazenamento automatizada, a sua máquina virtual requer as seguintes características:

* Aprovisionado com uma imagem de [galeria SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* Utiliza o modelo de [implementação do Gestor](../../../azure-resource-manager/management/deployment-models.md)de Recursos.
* Utiliza [SSDs premium.](../disks-types.md)

## <a name="new-vms"></a>Novos VMs

As seguintes secções descrevem como configurar o armazenamento para novas máquinas virtuais Do Servidor SQL.

### <a name="azure-portal"></a>Portal do Azure

Ao fornecer um VM Azure utilizando uma imagem de galeria SQL Server, selecione a **configuração De alterar** no **separador Definições do Servidor SQL** para abrir a página de Configuração de Armazenamento Otimizada de Desempenho. Pode deixar os valores em padrão ou modificar o tipo de configuração do disco que melhor se adequa às suas necessidades com base na sua carga de trabalho. 

![Configuração de armazenamento VM do servidor SQL durante o provisionamento](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Selecione o tipo de carga de trabalho que está a implementar o seu Servidor SQL para obter **a otimização**do Armazenamento . Com a opção de otimização **geral,** por padrão terá um disco de dados com 5000 IOPS max, e utilizará esta mesma unidade para os seus dados, registo de transações e armazenamento TempDB. A seleção do **processamento transacional** (OLTP) ou **do armazenamento** de dados criará um disco separado para dados, um disco separado para o registo de transações e utilizará o SSD local para tempDB. Não existem diferenças de armazenamento entre o **processamento transacional** e o armazenamento de **dados,** mas altera a configuração das [riscas e traça bandeiras.](#workload-optimization-settings) Escolher o armazenamento premium define o cache para *ReadOnly* para a unidade de dados, e *Nenhum* para a unidade de log de acordo com as melhores práticas de desempenho do [SQL Server VM](virtual-machines-windows-sql-performance.md). 

![Configuração de armazenamento VM do servidor SQL durante o provisionamento](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

A configuração do disco é completamente personalizável para que possa configurar a topologia de armazenamento, o tipo de disco e os IOPs de que necessita para a carga de trabalho vM do Seu Servidor SQL. Você também tem a capacidade de usar UltraSSD (pré-visualização) como uma opção para o **tipo de Disco** se o seu SQL Server VM estiver numa das regiões apoiadas (Leste US 2, SouthEast Asia e North Europe) e você ativou discos ultra para a sua [subscrição](/azure/virtual-machines/windows/disks-enable-ultra-ssd).  

Além disso, tem a capacidade de definir o cache para os discos. Os VMs Azure têm uma tecnologia de cache multi-nível chamada [Blob Cache](/azure/virtual-machines/windows/premium-storage-performance#disk-caching) quando usada com [Discos Premium](/azure/virtual-machines/windows/disks-types#premium-ssd). Blob Cache usa uma combinação da RAM máquina virtual e SSD local para o cache. 

O caching de disco para SSD Premium pode ser *ReadOnly*, *ReadWrite* ou *None*. 

- O caching *ReadOnly* é altamente benéfico para os ficheiros de dados do SQL Server que são armazenados no Armazenamento Premium. *ReadOnly* caching traz baixa latência lida, IOPS de leitura alta, e a entrada como, leituras são realizadas a partir de cache, que os dentro da memória VM e SSD local. Estas leituras são muito mais rápidas do que as leituras do disco de dados, que é do armazenamento de blob Azure. O armazenamento premium não conta as leituras servidas da cache para o iOPS do disco e a entrada. Portanto, o seu aplicável é capaz de obter uma maior produção total de formigas IOPS. 
- *Nenhuma* configuração de cache deve ser utilizada para os discos que hospedam o ficheiro SQL Server Log, uma vez que o ficheiro de registo é escrito sequencialmente e não beneficia do cache *do ReadOnly.* 
- *ReadWrite* caching não deve ser usado para hospedar ficheiros SQL Server, uma vez que o SQL Server não suporta a consistência dos dados com a cache *ReadWrite.* Escreve que a capacidade de resíduos da cache e das latenciências de blob *ReadOnly* aumenta ligeiramente se os escritos passarem pelas camadas de cache *de blob ReadOnly.* 


   > [!TIP]
   > Certifique-se de que a sua configuração de armazenamento corresponde às limitações impostas pelo tamanho vm selecionado. A escolha de parâmetros de armazenamento que excedam a tampa de desempenho do tamanho vM resultará em erro: `The desired performance might not be reached due to the maximum virtual machine disk performance cap.`. Ou diminua os IOPs alterando o tipo de disco ou aumente a limitação da tampa de desempenho aumentando o tamanho do VM. 


Com base nas suas escolhas, o Azure executa as seguintes tarefas de configuração de armazenamento após a criação do VM:

* Cria e liga SSDs premium à máquina virtual.
* Configura os discos de dados para serem acessíveis ao Servidor SQL.
* Refigura os discos de dados num conjunto de armazenamento com base nos requisitos especificados de tamanho e desempenho (IOPS e entrada).
* Associa a piscina de armazenamento com uma nova unidade na máquina virtual.
* Otimiza esta nova unidade com base no seu tipo de carga de trabalho especificado (armazenamento de dados, processamento transacional ou geral).

Para mais detalhes sobre como o Azure configura as definições de armazenamento, consulte a secção de [configuração](#storage-configuration)de Armazenamento . Para uma passagem completa de como criar um VM SQL Server no portal Azure, consulte [o tutorial de provisionamento](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Modelos de Gestão de Recursos

Se utilizar os seguintes modelos do Gestor de Recursos, dois discos de dados premium são anexados por padrão, sem configuração do pool de armazenamento. No entanto, pode personalizar estes modelos para alterar o número de discos de dados premium que estão ligados à máquina virtual.

* [Criar VM com cópia de segurança automatizada](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Criar VM com Patching Automatizado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Criar VM com integração AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>Modelo de início rápido

Pode utilizar o seguinte modelo de arranque rápido para implementar um VM do Servidor SQL utilizando a otimização do armazenamento. 

* [Criar VM com otimização de armazenamento](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [Criar VM usando UltraSSD](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>VMs existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para vMs de servidor SQL existentes, pode modificar algumas definições de armazenamento no portal Azure. Abra o seu recurso de [máquinas virtuais SQL,](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)e selecione **visão geral**. A página de visão geral do Servidor SQL mostra o uso atual do armazenamento do seu VM. Todos os discos que existem no seu VM são exibidos neste gráfico. Para cada unidade, o espaço de armazenamento exibe em quatro secções:

* Dados SQL
* Registo SQL
* Outros (armazenamento não SQL)
* Disponível

Para modificar as definições de armazenamento, **selecione Configurar** em **Definições**. 

![Configure armazenamento para VM servidor SQL existente](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Pode modificar as definições do disco para as unidades configuradas durante o processo de criação de VM do Servidor SQL. Selecionar a **unidade extend** abre a página de modificação de unidade, permitindo-lhe alterar o tipo de disco, bem como adicionar discos adicionais. 

![Configure armazenamento para VM servidor SQL existente](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="storage-configuration"></a>Configuração do armazenamento

Esta secção fornece uma referência para as alterações de configuração de armazenamento que o Azure executa automaticamente durante o fornecimento ou configuração de VM SQL no portal Azure.

* O Azure configura um armazém a partir do armazenamento selecionado a partir do seu VM. A próxima secção deste tópico fornece detalhes sobre a configuração do pool de armazenamento.
* A configuração automática de armazenamento utiliza sempre discos de dados [SSDs](../disks-types.md) P30 premium. Consequentemente, existe um mapeamento 1:1 entre o seu número selecionado de Terabytes e o número de discos de dados ligados ao seu VM.

Para obter informações sobre preços, consulte a página de preços de [armazenamento](https://azure.microsoft.com/pricing/details/storage) no **separador De armazenamento** do disco.

### <a name="creation-of-the-storage-pool"></a>Criação da piscina de armazenamento

O Azure utiliza as seguintes definições para criar o pool de armazenamento em VMs do Servidor SQL.

| Definição | Valor |
| --- | --- |
| Tamanho da risca |256 KB (Armazenagem de dados); 64 KB (Transacional) |
| Tamanhos do disco |1 TB cada |
| Cache |Leitura |
| Tamanho da atribuição |Tamanho da unidade de atribuição de NTFS de 64 KB |
| Recuperação | Recuperação simples (sem resiliência) |
| Número de colunas |Número de discos de dados até<sup>81</sup> |


<sup>1</sup> Após a criação do depósito, não é possível alterar o número de colunas no depósito.


## <a name="workload-optimization-settings"></a>Definições de otimização da carga de trabalho

O quadro seguinte descreve as três opções do tipo de carga de trabalho disponíveis e as respetivas otimizações:

| Tipo de carga de trabalho | Descrição | Otimizações |
| --- | --- | --- |
| **Geral** |Definição padrão que suporta a maioria das cargas de trabalho |Nenhum |
| **Processamento transacional** |Otimiza o armazenamento para cargas de trabalho tradicionais oLTP |Trace Flag 1117<br/>Trace Flag 1118 |
| **Armazenagem de dados** |Otimiza o armazenamento para cargas de trabalho analíticas e de reporte |Trace Flag 610<br/>Trace Flag 1117 |

> [!NOTE]
> Só pode especificar o tipo de carga de trabalho quando fornecer uma máquina virtual SQL selecionando-a no passo de configuração de armazenamento.

## <a name="next-steps"></a>Passos seguintes

Para outros tópicos relacionados com a execução do Servidor SQL em VMs Azure, consulte [o Servidor SQL em Máquinas Virtuais Azure](virtual-machines-windows-sql-server-iaas-overview.md).
