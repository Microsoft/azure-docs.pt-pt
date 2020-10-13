---
title: Migrar disco de log para ultra disco
description: Aprenda a migrar o seu SQL Server no disco de log da Azure Virtual Machine (VM) para um Azure Ultradisk para tirar partido do alto desempenho e da baixa latência.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 4ddafd9fbeda1752a782085244597aea3ccbdd2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91271907"
---
# <a name="migrate-log-disk-to-ultra-disk"></a>Migrar disco de log para ultra disco
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Os discos ultra Azure oferecem alta produção, iops elevado e armazenamento consistentemente baixo de disco de latência para SQL Server em Azure Virtual Machine (VM). 

Este artigo ensina-o a migrar o disco de log para um ultra SSD para tirar partido dos benefícios de desempenho oferecidos pelos discos ultra. 

## <a name="back-up-database"></a>Base de dados de back-up

Complete uma cópia de [segurança completa](backup-restore.md) da sua base de dados. 

## <a name="attach-disk"></a>Anexar disco

Fixe o Ultra SSD à sua máquina virtual depois de ter ativado a compatibilidade ultradisk no VM. 

O disco ultra é suportado num subconjunto de tamanhos e regiões VM. Antes de prosseguir, valide que o seu VM se encontra numa região, zona e tamanho que suporta o disco ultra. Pode [determinar e validar o tamanho e a região de VM](../../../virtual-machines/disks-enable-ultra-ssd.md#determine-vm-size-and-region-availability) utilizando o Azure CLI ou o PowerShell. 

### <a name="enable-compatibility"></a>Permitir compatibilidade

Para permitir a compatibilidade, siga estes passos:

1. Vá à sua máquina virtual no [portal Azure](https://portal.azure.com/). 
1. Parar/negociar a máquina virtual. 
1. Selecione **Discos** em **Definições** e, em seguida, selecione **Definições adicionais**. 

   :::image type="content" source="media/storage-migrate-to-ultradisk/additional-disks-settings-azure-portal.png" alt-text="Selecione definições adicionais para Discos em Definições no portal Azure":::

1. Selecione **Sim** para Ativar a **compatibilidade ultra disco**. 

   :::image type="content" source="../../../virtual-machines/media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png" alt-text="Selecione definições adicionais para Discos em Definições no portal Azure":::

1. Selecione **Guardar**. 



### <a name="attach-disk"></a>Anexar disco

Utilize o portal Azure para anexar um disco ultra à sua máquina virtual. Para mais detalhes, consulte [anexar um disco ultra](../../../virtual-machines/disks-enable-ultra-ssd.md#attach-an-ultra-disk).

Uma vez que o disco esteja ligado, inicie o seu VM mais uma vez utilizando o portal Azure. 



## <a name="format-disk"></a>Disco de formato

Ligue-se à sua máquina virtual e formate o seu disco ultra.  

Para formatar o seu disco ultra, siga estes passos:

1. Ligue-se ao seu VM utilizando o Protocolo de Ambiente de Trabalho Remoto (RDP).
1. Utilize [a Gestão do Disco](/windows-server/storage/disk-management/overview-of-disk-management) para formatar e dividir o seu disco ultra recém-ligado. 


## <a name="use-disk-for-log"></a>Use o disco para o registo

Configure o SqL Server para utilizar a nova unidade de registo. Pode fazê-lo utilizando o Transact-SQL (T-SQL) ou o SQL Server Management Studio (SSMS). A conta utilizada para a conta de serviço SQL Server deve ter o controlo total da localização do novo ficheiro de registo. 

### <a name="configure-permissions"></a>Configurar permissões

1. Verifique a conta de serviço utilizada pelo SQL Server. Pode fazê-lo utilizando o Sql Server Configuration Manager ou Services.msc.
1. Navegue para o seu novo disco. 
1. Crie uma pasta (ou várias pastas) para ser utilizada para o seu ficheiro de registo. 
1. Clique com o botão direito na pasta e selecione **Propriedades**.
1. No separador **Segurança,** conceder acesso total ao controlo total da conta de serviço sql Server. 
1. Selecione **OK**  para guardar as suas definições. 
1. Repita isto para todas as pastas de nível de raiz onde planeia ter dados SQL. 

### <a name="use-new-log-drive"></a>Use nova unidade de log 

Após a concessão da permissão, utilize o Transact-SQL (T-SQL) ou o SQL Server Management Studio (SSMS) para desvincular a base de dados e mover os ficheiros de registo existentes para a nova localização.

   > [!CAUTION]
   > A desvinculação da base de dados irá desligá-la, fechar as ligações e reverter quaisquer transações que estejam a bordo. Proceda com precaução e durante uma janela de manutenção de tempo de intervalo. 



# <a name="transact-sql-t-sql"></a>[Transact-SQL (T-SQL)](#tab/tsql)

Utilize o T-SQL para mover os ficheiros existentes para um novo local:

1. Ligue à sua base de dados no SQL Server Management Studio e abra uma nova janela **de consulta.** 
1. Obtenha os ficheiros e locais existentes:

   ```sql
   USE AdventureWorks
   GO

   sp_helpfile
   GO
   ```

1. Desprender a base de dados: 

   ```sql
   USE master
   GO

   sp_detach_db 'AdventureWorks'
   GO
   ```

1. Utilize o explorador de ficheiros para mover o ficheiro de registo para a nova localização no disco ultra. 

1. Anexar a base de dados, especificando as novas localizações dos ficheiros: 

   ```sql
    sp_attach_db 'AdventureWorks'
   'E:\Fixed_FG\AdventureWorks.mdf',
   'E:\Fixed_FG\AdventureWorks_2.ndf',
   'F:\New_Log\AdventureWorks_log.ldf'
   GO
   ```

Neste momento, a base de dados surge online com o registo no novo local. 



# <a name="sql-server-management-studio-ssms"></a>[SQL Server Management Studio (SSMS)](#tab/ssms)

Utilize o SSMS para mover os ficheiros existentes para um novo local:

1. Ligue à sua base de dados no SQL Server Management Studio (SSMS). 
1. Clique no botão direito na base de dados, selecione **Propriedades** e, em seguida, selecione **Ficheiros**. 
1. Observe o caminho dos ficheiros existentes. 
1. Selecione **OK** para fechar a caixa de diálogo. 
1. Clique com o botão direito na base de **dados,** selecione Tasks  >  **Desconect**. 
1. Siga o assistente para desprender a base de dados. 
1. Utilize o File Explorer para mover manualmente o ficheiro de registo para a nova localização.
1. Anexar a base de dados no SQL Server Management Studio
   1. Clique com o botão direito **bases de dados** no Explorador de **Objetos** e **selecione a base de dados Attach**. 
   1. Utilizando a caixa de diálogo, adicione cada ficheiro, incluindo o ficheiro de registo na sua nova localização. 
   1. Selecione **OK** para anexar a base de dados. 

Neste momento, a base de dados surge online com o registo no novo local.

---


## <a name="next-steps"></a>Passos seguintes

Reveja as [melhores práticas de desempenho](performance-guidelines-best-practices.md) para configurações adicionais para melhorar o desempenho. 

Para uma visão geral do SQL Server em Azure Virtual Machines, consulte os seguintes artigos:

- [Visão geral do SQL Server em VMs windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Visão geral do SQL Server em Linux VMs](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
