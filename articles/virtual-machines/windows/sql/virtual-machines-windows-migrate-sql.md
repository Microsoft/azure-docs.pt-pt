---
title: Migrar um banco de dados SQL Server para SQL Server em uma VM | Microsoft Docs
description: Saiba mais sobre como migrar um banco de dados de usuário local para SQL Server em uma máquina virtual do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.topic: article
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c8314b04c05e2ecba2715b807171b5c1a2fa988a
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646868"
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Migrar uma base de dados do SQL Server para o SQL Server numa VM do Azure

Há vários métodos para migrar um banco de dados de usuário local SQL Server para SQL Server em uma VM do Azure. Este artigo abordará brevemente vários métodos e recomendará o melhor método para vários cenários.


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > SQL Server 2008 e SQL Server 2008 R2 estão se aproximando do [fim do ciclo de vida de suporte](https://www.microsoft.com/sql-server/sql-server-2008) para instâncias locais. Para estender o suporte, você pode migrar sua instância de SQL Server para uma VM do Azure ou comprar atualizações de segurança estendidas para mantê-las no local. Para obter mais informações, consulte [estender o suporte para SQL Server 2008 e 2008 R2 com o Azure](virtual-machines-windows-sql-server-2008-eos-extend-support.md)

## <a name="what-are-the-primary-migration-methods"></a>Quais são os principais métodos de migração?
Os métodos de migração primários são:

* Executar o backup local usando a compactação e copiar manualmente o arquivo de backup para a máquina virtual do Azure
* Executar um backup para URL e restaurar para a máquina virtual do Azure a partir da URL
* Desanexar e copiar os arquivos de dados e de log para o armazenamento de BLOBs do Azure e, em seguida, anexar a SQL Server na VM do Azure da URL
* Converter computador físico local em VHD do Hyper-V, carregar no armazenamento de BLOBs do Azure e implantar como nova VM usando VHD carregado
* Enviar disco rígido usando o serviço de importação/exportação do Windows
* Se você tiver uma implantação de grupo de disponibilidade AlwaysOn local, use o [Assistente para adicionar réplica do Azure](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) para criar uma réplica no Azure e, em seguida, fazer failover, apontando os usuários para a instância de banco de dados do Azure
* Use SQL Server [replicação transacional](https://msdn.microsoft.com/library/ms151176.aspx) para configurar a instância do SQL Server do Azure como um assinante e, em seguida, desabilitar a replicação, apontando os usuários para a instância do banco de dados do Azure

> [!TIP]
> Você também pode usar essas mesmas técnicas para mover bancos de dados entre SQL Server VMs no Azure. Por exemplo, não há nenhuma maneira com suporte para atualizar uma VM de imagem de galeria SQL Server de uma versão/edição para outra. Nesse caso, você deve criar uma nova VM SQL Server com a nova versão/edição e, em seguida, usar uma das técnicas de migração neste artigo para mover seus bancos de dados. 

## <a name="choosing-your-migration-method"></a>Escolhendo o método de migração
Para obter o melhor desempenho de transferência de dados, migre os arquivos de banco de dado para a VM do Azure usando um arquivo de backup compactado.

Para minimizar o tempo de inatividade durante o processo de migração de banco de dados, use a opção AlwaysOn ou a opção de replicação transacional.

Se não for possível usar os métodos acima, migre manualmente seu banco de dados. Usando esse método, geralmente você começa com um backup de banco de dados seguido por uma cópia do backup de banco de dados no Azure e, em seguida, executa uma restauração de banco de dados. Você também pode copiar os próprios arquivos do banco de dados no Azure e, em seguida, anexá-los. Há vários métodos pelos quais você pode realizar esse processo manual de migração de um banco de dados para uma VM do Azure.

> [!NOTE]
> Ao atualizar para SQL Server 2014 ou SQL Server 2016 de versões anteriores do SQL Server, você deve considerar se as alterações são necessárias. É recomendável que você resolva todas as dependências em recursos sem suporte na nova versão do SQL Server como parte do seu projeto de migração. Para obter mais informações sobre as edições e os cenários com suporte, consulte [atualizar para SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

A tabela a seguir lista cada um dos principais métodos de migração e discute quando o uso de cada método é mais apropriado.

| Método | Versão do banco de dados de origem | Versão do banco de dados de destino | Restrição de tamanho de backup do banco de dados de origem | Notas |
| --- | --- | --- | --- | --- |
| [Executar o backup local usando a compactação e copiar manualmente o arquivo de backup para a máquina virtual do Azure](#backup-and-restore) |SQL Server 2005 ou superior |SQL Server 2005 ou superior |[Limite de armazenamento de VM do Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | Essa é uma técnica muito simples e bem testada para mover bancos de dados entre computadores. |
| [Executar um backup para URL e restaurar para a máquina virtual do Azure a partir da URL](#backup-to-url-and-restore) |SQL Server 2012 SP1 CU2 ou superior |SQL Server 2012 SP1 CU2 ou superior |< 12,8 TB para SQL Server 2016, caso contrário < 1 TB | Esse método é apenas outra maneira de mover o arquivo de backup para a VM usando o armazenamento do Azure. |
| [Desanexar e copiar os arquivos de dados e de log para o armazenamento de BLOBs do Azure e, em seguida, anexar a SQL Server na máquina virtual do Azure da URL](#detach-and-attach-from-url) |SQL Server 2005 ou superior |SQL Server 2014 ou superior |[Limite de armazenamento de VM do Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Use esse método ao planejar [armazenar esses arquivos usando o serviço de armazenamento de BLOBs do Azure](https://msdn.microsoft.com/library/dn385720.aspx) e anexá-los ao SQL Server em execução em uma VM do Azure, especialmente com bancos de dados muito grandes |
| [Converter o computador local em VHDs do Hyper-V, carregar no armazenamento de BLOBs do Azure e, em seguida, implantar uma nova máquina virtual usando VHD carregado](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQL Server 2005 ou superior |SQL Server 2005 ou superior |[Limite de armazenamento de VM do Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Use ao [trazer sua própria licença de SQL Server](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md), ao migrar um banco de dados que será executado em uma versão mais antiga do SQL Server, ou ao migrar o sistema e os bancos de dados de usuário juntos como parte da migração do banco de dados dependente de outros bancos de dados de usuário e/ou bancos de dados do sistema. |
| [Enviar disco rígido usando o serviço de importação/exportação do Windows](#ship-hard-drive) |SQL Server 2005 ou superior |SQL Server 2005 ou superior |[Limite de armazenamento de VM do Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Use o [serviço de importação/exportação do Windows](../../../storage/common/storage-import-export-service.md) quando o método de cópia manual for muito lento, como com bancos de dados muito grandes |
| [Usar o assistente para adicionar réplica do Azure](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQL Server 2012 ou superior |SQL Server 2012 ou superior |[Limite de armazenamento de VM do Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Minimiza o tempo de inatividade, use quando você tiver uma implantação Always On local |
| [Usar SQL Server replicação transacional](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 ou superior |SQL Server 2005 ou superior |[Limite de armazenamento de VM do Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Use quando precisar minimizar o tempo de inatividade e não tiver uma implantação Always On local |

## <a name="backup-and-restore"></a>Cópia de segurança e restauro
Faça backup de seu banco de dados com compactação, copie o backup para a VM e, em seguida, restaure o banco de dados. Se o arquivo de backup tiver mais de 1 TB, você deverá distribuí-lo porque o tamanho máximo de um disco de VM é de 1 TB. Use as seguintes etapas gerais para migrar um banco de dados de usuário usando este método manual:

1. Execute um backup de banco de dados completo em um local.
2. Crie ou carregue uma máquina virtual com a versão do SQL Server desejado.
3. Configure a conectividade com base em seus requisitos. Consulte [conectar-se a uma máquina Virtual SQL Server no Azure (Gerenciador de recursos)](virtual-machines-windows-sql-connect.md).
4. Copie os arquivos de backup para sua VM usando a área de trabalho remota, o Windows Explorer ou o comando de cópia de um prompt de comando.

## <a name="backup-to-url-and-restore"></a>Backup para URL e restauração
Em vez de fazer backup em um arquivo local, você pode usar o [backup para URL](https://msdn.microsoft.com/library/dn435916.aspx) e, em seguida, restaurar da URL para a VM. Com SQL Server 2016, os conjuntos de backup distribuídos têm suporte, são recomendados para desempenho e precisam exceder os limites de tamanho por blob. Para bancos de dados muito grandes, recomenda-se o uso do [serviço de importação/exportação do Windows](../../../storage/common/storage-import-export-service.md) .

## <a name="detach-and-attach-from-url"></a>Desanexar e anexar da URL
Desanexe seu banco de dados e arquivos de log e transfira-os para o [armazenamento de BLOBs do Azure](https://msdn.microsoft.com/library/dn385720.aspx). Em seguida, anexe o banco de dados da URL na sua VM do Azure. Use esta se desejar que os arquivos de banco de dados físicos residam no armazenamento de BLOBs. Isso pode ser útil para bancos de dados muito grandes. Use as seguintes etapas gerais para migrar um banco de dados de usuário usando este método manual:

1. Desanexe os arquivos de banco de dados da instância do banco de dados local.
2. Copie os arquivos de banco de dados desanexados no armazenamento de BLOBs do Azure usando o [Utilitário de linha de comando AZCopy](../../../storage/common/storage-use-azcopy.md).
3. Anexe os arquivos de banco de dados da URL do Azure para a instância de SQL Server na VM do Azure.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Converter em VM e carregar para URL e implementar como nova VM
Use este método para migrar todos os bancos de dados do sistema e do usuário em uma instância do SQL Server local para uma máquina virtual do Azure. Use as seguintes etapas gerais para migrar uma instância de SQL Server inteira usando este método manual:

1. Converta máquinas físicas ou virtuais em VHDs do Hyper-V.
2. Carregue os arquivos VHD no armazenamento do Azure usando o [cmdlet Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3. Implante uma nova máquina virtual usando o VHD carregado.

> [!NOTE]
> Para migrar um aplicativo inteiro, considere o uso de [Azure site Recovery](../../../site-recovery/site-recovery-overview.md)].

## <a name="ship-hard-drive"></a>Enviar disco rígido
Use o [método de serviço de importação/exportação do Windows](../../../storage/common/storage-import-export-service.md) para transferir grandes quantidades de dados de arquivo para o armazenamento de BLOBs do Azure em situações em que o carregamento pela rede é extremamente caro ou não é viável. Com esse serviço, você envia um ou mais discos rígidos contendo esses dados para uma data center do Azure, onde seus dados serão carregados para sua conta de armazenamento.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como executar SQL Server em máquinas virtuais do Azure, consulte a [visão geral de SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

> [!TIP]
> Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](virtual-machines-windows-sql-server-iaas-faq.md).

Para obter instruções sobre como criar uma máquina virtual do Azure SQL Server de uma imagem capturada, confira [dicas & truques sobre como clonar máquinas virtuais do Azure SQL de imagens capturadas](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) no blog de engenheiros de SQL Server do CSS.

