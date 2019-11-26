---
title: Provision virtual machine with Azure portal
description: This how-to guide describes your options for creating Windows SQL Server 2017 virtual machines in the Azure portal.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 28f00db3b604534be5ff9cee79c0aacc41f066b5
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74464145"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>How to provision a Windows SQL Server virtual machine in the Azure portal

This guide provides details on the different options available when you create a Windows SQL Server virtual machine in the Azure portal. This article covers more configuration options than the [SQL Server VM quickstart](quickstart-sql-vm-create-portal.md), which goes more through one possible provisioning task. 

Use this guide to create your own SQL Server VM. Or, use it as a reference for the available options in the Azure portal.

> [!TIP]
> Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](virtual-machines-windows-sql-server-iaas-faq.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a id="select"></a> SQL Server virtual machine gallery images

When you create a SQL Server virtual machine, you can select one of several pre-configured images from the virtual machine gallery. The following steps demonstrate how to select one of the SQL Server 2017 images.

1. Select **Azure SQL** in the left-hand menu of the Azure portal. If **Azure SQL** is not in the list, select **All services**, then type Azure SQL in the search box. (Optional) Select the star next to **Azure SQL** to favorite it and add it as an item in the left-hand navigation. 
1. Select **+ Add** to open the **Select SQL deployment option** page. You can view additional information by selecting **Show details**. 
1. Type `2017` in the SQL Server image search box on the **SQL virtual machines** tile, and then select **Free SQL Server License: SQL Server 2017 Developer on Windows Server 2016** from the drop-down. 


   ![Select SQL VM image](media/virtual-machines-windows-portal-sql-server-provision/select-sql-vm-image-portal.png)

   > [!TIP]
   > The Developer edition is used in this walkthrough because it is a full-featured, free edition of SQL Server for development testing. Apenas paga pelo custo de execução da VM. However, you are free to choose any of the images to use in this walkthrough. For a description of available images, see the [SQL Server Windows Virtual Machines overview](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

   > [!TIP]
   > Licensing costs for SQL Server are incorporated into the per-second pricing of the VM you create and varies by edition and cores. However, SQL Server Developer edition is free for development/testing (not production), and SQL Express is free for lightweight workloads (less than 1 GB of memory, less than 10 GB of storage). You can also bring-your-own-license (BYOL) and pay only for the VM. Aos nomes dessas imagem é adicionado o prefixo {BYOL}. 
   >
   > Para obter mais informações sobre estas opções, consulte [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços de VMs do Azure do SQL Server)](virtual-machines-windows-sql-server-pricing-guidance.md).


1. Selecione **Criar**.


## <a name="1-configure-basic-settings"></a>1. Configure basic settings


On the **Basics** tab, provide the following information:

* Under **Project Details**, make sure the correct subscription is selected. 
*  In the **Resource group** section, either select an existing resource group from the list or choose **Create new** to create a new resource group. Um grupo de recursos é uma coleção de recursos relacionados no Azure (máquinas virtuais, contas do Storage, redes virtuais, etc.). 

    ![Subscrição](media/quickstart-sql-vm-create-portal/basics-project-details.png)

  > [!NOTE]
  > Utilizar um novo grupo de recursos é útil se estiver apenas a testar ou a saber mais sobre implementações do SQL Server no Azure. Depois de terminar o teste, elimine o grupo de recursos para eliminar automaticamente a VM e todos os recursos associados a esse grupo de recursos. Para mais informações sobre grupos de recursos, consulte o artigo [Descrição Geral do Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md).


* Under **Instance details**:
    1. Enter a unique **Virtual machine name**.  
    1. Choose a location for your **Region**. 
    1. For the purpose of this guide, leave **Availability options** set to _No infrastructure redundancy required_. To find out more information about availability options, see [Availability](../../windows/availability.md). 
    1. In the **Image** list, select _Free SQL Server License: SQL Server 2017 Developer on Windows Server 2016_.  
    1. Choose to **Change size** for the **Size** of the virtual machine and select the **A2 Basic** offering. Be sure to clean up your resources once you're done with them to prevent any unexpected charges. Relativamente a cargas de trabalho de produção, veja os tamanhos e as configurações de máquinas recomendados em [Performance best practices for SQL Server in Azure Virtual Machine](virtual-machines-windows-sql-performance.md) (Melhores práticas de desempenho para o SQL Server nas Máquinas Virtuais do Azure).

    ![Instance details](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

> [!IMPORTANT]
> O custo mensal estimado apresentado na janela **Escolher um tamanho** não inclui os custos de licenciamento do SQL Server. This estimate is the cost of the VM alone. For the Express and Developer editions of SQL Server, this estimate is the total estimated cost. Quanto a outras edições, veja a [página de preços das Máquinas Virtuais do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) e selecione a edição de destino do SQL Server. Also see the [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) and [Sizes for virtual machines](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Under **Administrator account**, provide a username and a password. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Conta de administrador](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

* Under **Inbound port rules**, choose **Allow selected ports** and then select **RDP (3389)** from the drop-down. 

   ![Regras da porta de entrada](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Configure optional features

### <a name="disks"></a>Discos

On the **Disks** tab, configure your disk options. 

* Under **OS disk type**, select the type of disk you want for your OS from the drop-down. Premium is recommended for production systems but is not available for a Basic VM. To utilize Premium SSD, change the virtual machine size. 
* Under **Advanced**, select **Yes** under use **Managed Disks**.

   > [!NOTE]
   > A Microsoft recomenda o Managed Disks para o SQL Server. O Managed Disks processa o armazenamento em segundo plano. Além disso, se houver máquinas virtuais com Managed Disks no mesmo conjunto de disponibilidade, o Azure distribui os recursos de armazenamento para fornecer a redundância adequada. For more information, see [Azure Managed Disks Overview](../managed-disks-overview.md). Para obter informações específicas sobre os discos geridos em conjuntos de disponibilidade, veja [Use managed disks for VMs in availability set](../manage-availability.md) (Utilizar discos geridos em VMs num conjunto de disponibilidade).

![SQL VM Disk settings](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Funcionamento em Rede

On the **Networking** tab, configure your networking options. 

* Create a new **virtual network**, or use an existing vNet for your SQL Server VM. Designate a **Subnet** as well. 

* Under **NIC network security group**, select either a basic security group, or the advanced security group. Choosing the basic option allows you to select inbound ports for the SQL Server VM (the same values that were configured on the **Basic** tab). Selecting the advanced option allows you to choose an existing network security group, or create a new one. 

* You can make other changes to network settings, or keep the default values.

![SQL VM Networking settings](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Monitorização

On the **Monitoring** tab, configure monitoring and autoshutdown. 

* Azure enables **Boot diagnostics** by default with the same storage account designated for the VM. You can change these settings here, as well as enabling **OS guest diagnostics**. 
* You can enable **System assigned managed identity** and **autoshutdown** on this tab as well. 

![SQL VM management settings](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Configure SQL Server settings

On the **SQL Server settings** tab, configure specific settings and optimizations for SQL Server. The settings that you can configure for SQL Server include the following:

| Definição |
| --- |
| [Conetividade](#connectivity) |
| [Autenticação](#authentication) |
| [Integração do Cofre de Chaves do Azure](#azure-key-vault-integration) |
| [Configuração do armazenamento](#storage-configuration) |
| [Aplicação de Patches Automatizada](#automated-patching) |
| [Cópia de Segurança Automatizada](#automated-backup) |
| [Machine Learning Services](#machine-learning-services) |


### <a name="connectivity"></a>Ligação

Em **Conectividade do SQL**, especifique o tipo de acesso que pretende para a instância do SQL Server nesta VM. For the purposes of this walkthrough, select **Public (internet)** to allow connections to SQL Server from machines or services on the internet. With this option selected, Azure automatically configures the firewall and the network security group to allow traffic on the port selected.

> [!TIP]
> Por predefinição, o SQL Server escuta numa porta bem conhecida, a **1433**. Para maior segurança, altere a porta na caixa de diálogo anterior para escutar numa porta não predefinida, como 1401. If you change the port, you must connect using that port from any client tools, such as SSMS.

![SQL VM Security](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-security.png)

Para ligar ao SQL Server através da Internet, também tem de ativar a Autenticação do SQL Server, que se descreve na secção seguinte.

Se preferir não ativar ligações para o Motor de Base de dados através da Internet, escolha uma das seguintes opções:

* **Local (no interior da VM)** para permitir ligações ao SQL Server, apenas a partir de dentro da VM.
* **Privada (na Virtual Network)** para permitir ligações ao SQL Server a partir de máquinas ou serviços na mesma rede virtual.

Em geral, melhore a segurança, selecionando a conectividade mais restritiva que permite o seu cenário. Mas todas as opções têm capacidade de segurança através das regras do Grupo de Segurança de Rede e Autenticação do SQL/Windows. Pode editar o Grupo de Segurança de Rede após a VM ter sido criada. Para obter mais informações, veja [Security Considerations for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-security.md) (Considerações de segurança para o SQL Server em Máquinas Virtuais do Azure).



### <a name="authentication"></a>Autenticação

If you require SQL Server Authentication, click **Enable** under **SQL authentication** on the **SQL Server settings** tab.

![Autenticação do SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

> [!NOTE]
> If you plan to access SQL Server over the internet (the Public connectivity option), you must enable SQL authentication here. O acesso público ao SQL Server requer a utilização da Autenticação do SQL.

Se ativar a Autenticação do SQL Server, especifique um **Nome de início de sessão** e uma **Palavra-passe**. This login name is configured as a SQL Server Authentication login and member of the **sysadmin** fixed server role. Para obter mais informações sobre Modos de Autenticação, veja [Choose an Authentication Mode](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode) (Escolher um Modo de Autenticação).

Se não ativar a Autenticação do SQL Server, em seguida, pode utilizar a conta de Administrador local na VM para ligar à instância do SQL Server.


### <a name="azure-key-vault-integration"></a>Integração do Cofre de Chaves do Azure

To store security secrets in Azure for encryption, select **SQL Server settings**, and scroll down to  **Azure key vault integration**. Select **Enable** and fill in the requested information. 

![Integração do Cofre de Chaves do Azure](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-akv.png)

A tabela seguinte lista os parâmetros necessários para configurar a Integração do Cofre de Chaves do Azure.

| PARÂMETRO | DESCRIÇÃO | EXEMPLO |
| --- | --- | --- |
| **URL do Cofre de Chaves** |A localização do cofre de chaves. |https:\//contosokeyvault.vault.azure.net/ |
| **Nome principal** |Nome principal do serviço Azure Active Directory. Este nome também é referido como o ID de Cliente. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Segredo principal** |Segredo principal do serviço Azure Active Directory. Este segredo também é referido como o Segredo do Cliente. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Nome da credencial** |**Nome da credencial**: a Integração AKV cria uma credencial dentro do SQL Server, permitindo que a VM tenha acesso ao cofre de chaves. Escolha um nome para esta credencial. |mycred1 |

Para obter mais informações, consulte o artigo [Configurar a Integração do Cofre de Chaves do Azure para o SQL Server em VMs do Azure](virtual-machines-windows-ps-sql-keyvault.md).

### <a name="storage-configuration"></a>Configuração do armazenamento

On the **SQL Server settings** tab, under **Storage configuration**, select **Change configuration** to open the Performance Optimized Storage Configuration page and specify the storage requirements.

![SQL VM Storage configuration](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Em **Armazenamento otimizado para**, selecione uma das seguintes opções:

* **Geral** é a predefinição e suporta a maioria das cargas de trabalho.
* **Transactional processing** optimizes the storage for traditional database OLTP workloads.
* O **Armazenamento de dados** otimiza o armazenamento para cargas de trabalho analíticas e de relatórios.

![SQL VM Storage configuration](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

You can choose to leave the values at default, or you can manually change the storage topology to suit your IOPS needs. For more information, see [storage configuration](virtual-machines-windows-sql-server-storage-configuration.md). 

### <a name="sql-server-license"></a>SQL Server License
If you're a Software Assurance customer, you can utilize the [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) to bring your own SQL Server license and save on resources. 

![SQL VM License](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Aplicação de patches automatizada

A **Aplicação de patches automatizada** está ativada por predefinição. A Aplicação de patches automatizada permite ao Azure automaticamente aplicar o patch do SQL Server e o sistema operativo. Especifique um dia da semana, a hora e a duração de uma janela de manutenção. O Azure executa a aplicação de patches nesta janela de manutenção. A agenda da janela de manutenção utiliza a região da VM para a hora. Se não pretender que o Azure aplique automaticamente o patch do SQL Server e o sistema operativo, clique em **Desativar**.  

![SQL VM automated patching](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-automated-patching.png)

Para obter mais informações, consulte o artigo [Aplicação de Patches Automatizada para o SQL Server nas Virtual Machines do Azure](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Cópia de segurança automatizada

Ative as cópias de segurança de bases de dados automáticas em **Cópia de segurança automatizada**. A cópia de segurança automatizada está desativada por predefinição.

Quando ativa a cópia de segurança automatizada do SQL, pode configurar as seguintes definições:

* Período de retenção (dias) para cópias de segurança
* Conta do Storage a utilizar para as cópias de segurança
* Opção de encriptação e palavra-passe para as cópias de segurança
* Realizar cópia de segurança das bases de dados do sistema
* Configurar agenda da cópia de segurança

Para encriptar a cópia de segurança, clique em **Ativar**. Em seguida, especifique a **Palavra-passe**. O Azure cria um certificado para encriptar as cópias de segurança e utiliza a palavra-passe especificada para proteger o certificado. By default the schedule is set automatically, but you can create a manual schedule by selecting **Manual**. 

![SQL VM automated backups](media/virtual-machines-windows-portal-sql-server-provision/automated-backup.png)

Para obter mais informações, consulte o artigo [Cópia de Segurança Automatizada para o SQL Server nas Virtual Machines do Azure](virtual-machines-windows-sql-automated-backup.md).


### <a name="machine-learning-services"></a>Serviços do Machine Learning

You have the option to enable [Machine Learning Services](/sql/advanced-analytics/). This option enables you to use machine learning with Python and R in SQL Server 2017. Select **Enable** on the **SQL Server Settings** window.


## <a name="4-review--create"></a>4. Review + create

On the **Review + create** tab, review the summary, and select  **Create** to create SQL Server, resource group, and resources specified for this VM.

Pode monitorizar a implementação a partir do portal do Azure. O botão **Notificações** na parte superior do ecrã mostra o estado básico da implementação.

> [!NOTE]
> Para lhe fornecer uma ideia dos tempos da implementação, implementei uma VM do SQL para a região EUA Leste com as predefinições. Esta implementação de teste demorou aproximadamente 12 minutos a concluir. Mas poderá ter uma implementação mais lenta ou mais rápida com base na sua região e nas definições selecionadas.

## <a id="remotedesktop"></a> Abrir a VM com o Ambiente de Trabalho Remoto

Utilize os seguintes passos para ligar à máquina virtual do SQL Server com o Ambiente de Trabalho Remoto:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Depois de ligar à máquina virtual do SQL Server, pode iniciar o SQL Server Management Studio e estabelecer ligação à Autenticação do Windows com as suas credenciais de administrador local. Se ativou a Autenticação do SQL Server, também pode ligar com Autenticação do SQL Server com o início de sessão do SQL e a palavra-passe que configurou durante o aprovisionamento.

O acesso ao computador permite-lhe alterar diretamente as definições do SQL Server e da máquina com base nos seus requisitos. Por exemplo, pode configurar as definições da firewall ou alterar as definições de configuração do SQL Server.

## <a id="connect"></a> Ligar ao SQL Server remotamente

In this walkthrough, you selected **Public** access for the virtual machine and **SQL Server Authentication**. Estas definições configuraram automaticamente a máquina virtual para permitir ligações ao SQL Server a partir de qualquer cliente através da Internet (partindo do princípio de que tem o início de sessão SQL correto).

> [!NOTE]
> Se não selecionou Público durante o aprovisionamento, pode alterar as definições de conectividade SQL através do portal após o aprovisionamento. Para obter mais informações, veja o artigo [Alterar as definições de conectividade SQL](virtual-machines-windows-sql-connect.md#change).

The following sections show how to connect over the internet to your SQL Server VM instance.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > This example uses the common port 1433. However, this value will need to be modified if a different port (such as 1401) was specified during the deployment of the SQL Server VM. 


## <a name="next-steps"></a>Passos seguintes

Para outras informações sobre como utilizar o SQL Server no Azure, consulte o artigo [SQL Server em Virtual Machines do Azure](virtual-machines-windows-sql-server-iaas-overview.md) e as [Perguntas Mais Frequentes](virtual-machines-windows-sql-server-iaas-faq.md).
