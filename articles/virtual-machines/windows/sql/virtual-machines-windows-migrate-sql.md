---
title: Migrar uma base de dados do SQL Server para o SQL Server num VM [ Microsoft Docs
description: Saiba como migrar uma base de dados de utilizadores no local para o SQL Server numa máquina virtual Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646868"
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Migrar uma base de dados do SQL Server para o SQL Server numa VM do Azure

Existem vários métodos para migrar uma base de dados de utilizadores do SQL Server no local para o SQL Server num VM Azure. Este artigo discutirá brevemente vários métodos e recomendará o melhor método para vários cenários.


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > O SQL Server 2008 e o SQL Server 2008 R2 estão a aproximar-se [do fim do seu ciclo de vida de suporte](https://www.microsoft.com/sql-server/sql-server-2008) para casos no local. Para estender o suporte, pode migrar a sua instância SQL Server para um VM Azure ou comprar Atualizações de Segurança Estendidas para mantê-lo no local. Para mais informações, consulte [Extend suporte para SQL Server 2008 e 2008 R2 com Azure](virtual-machines-windows-sql-server-2008-eos-extend-support.md)

## <a name="what-are-the-primary-migration-methods"></a>Quais são os principais métodos de migração?
Os principais métodos de migração são:

* Execute a cópia de segurança no local utilizando a compressão e copie manualmente o ficheiro de cópia de segurança na máquina virtual Azure
* Execute uma cópia de segurança para URL e restaure na máquina virtual Azure a partir do URL
* Desaperte e, em seguida, copie os dados e os ficheiros de registo para o armazenamento de blob Azure e, em seguida, prenda-o ao Servidor SQL em Azure VM a partir de URL
* Converta a máquina física no local para Hyper-V VHD, carregue para o armazenamento Azure Blob e, em seguida, implante como novo VM usando VHD carregado
* Unidade rígida de navio utilizando windows import/serviço de exportação
* Se tiver uma implementação do Grupo AlwaysOn Availability no local, utilize o Assistente de [Réplica Add Azure](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) para criar uma réplica em Azure e, em seguida, falhar, apontando os utilizadores para a instância de base de dados Azure
* Utilize [a replicação transacional](https://msdn.microsoft.com/library/ms151176.aspx) do SQL Server para configurar a instância do Servidor Azure SQL como assinante e, em seguida, desativar a replicação, apontando os utilizadores para a instância de base de dados Azure

> [!TIP]
> Também pode utilizar estas mesmas técnicas para mover bases de dados entre VMs do Servidor SQL em Azure. Por exemplo, não existe uma forma suportada de atualizar um VM de imagem de imagem de galeria SQL Server de uma versão/edição para outra. Neste caso, deverá criar um novo VM SQL Server com a nova versão/edição e, em seguida, utilizar uma das técnicas de migração neste artigo para mover as suas bases de dados. 

## <a name="choosing-your-migration-method"></a>Escolher o seu método de migração
Para um ótimo desempenho de transferência de dados, emigra os ficheiros da base de dados para o VM Azure utilizando um ficheiro de cópia de segurança comprimido.

Para minimizar o tempo de inatividade durante o processo de migração da base de dados, utilize a opção AlwaysOn ou a opção de replicação transacional.

Se não for possível utilizar os métodos acima referidos, emigra manualmente a sua base de dados. Utilizando este método, geralmente começará com uma cópia de cópia da base de dados em Azure e, em seguida, executará uma cópia de cópia da base de dados em Azure e, em seguida, executará uma restauração de base de dados. Também pode copiar os ficheiros da base de dados em Azure e, em seguida, anexá-los. Existem vários métodos através dos quais pode realizar este processo manual de migração de uma base de dados para um VM Azure.

> [!NOTE]
> Quando faz o upgrade para o SQL Server 2014 ou SQL Server 2016 a partir de versões mais antigas do SQL Server, deve considerar se as alterações são necessárias. Recomendamos que aborde todas as dependências de funcionalidades não suportadas pela nova versão do SQL Server como parte do seu projeto de migração. Para obter mais informações sobre as edições e cenários suportados, consulte [upgrade para SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

A tabela que se segue enumera cada um dos métodos primários de migração e discute quando a utilização de cada método é mais adequada.

| Método | Versão da base de dados de origem | Versão da base de dados de destino | Restrição de tamanho de backup de base de dados de origem | Notas |
| --- | --- | --- | --- | --- |
| [Execute a cópia de segurança no local utilizando a compressão e copie manualmente o ficheiro de cópia de segurança na máquina virtual Azure](#backup-and-restore) |SQL Server 2005 ou superior |SQL Server 2005 ou superior |[Limite de armazenamento Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | Esta é uma técnica muito simples e bem testada para mover bases de dados através de máquinas. |
| [Execute uma cópia de segurança para URL e restaure na máquina virtual Azure a partir do URL](#backup-to-url-and-restore) |SQL Server 2012 SP1 CU2 ou superior |SQL Server 2012 SP1 CU2 ou superior |< 12.8 TB para O Servidor SQL 2016, caso contrário < 1 TB | Este método é apenas mais uma forma de mover o ficheiro de reserva para o VM utilizando o armazenamento Azure. |
| [Desaperte e, em seguida, copie os dados e os ficheiros de registo para o armazenamento de blob Azure e, em seguida, prenda-o ao Servidor SQL na máquina virtual Azure a partir de URL](#detach-and-attach-from-url) |SQL Server 2005 ou superior |SQL Server 2014 ou superior |[Limite de armazenamento Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Utilize este método quando planeia armazenar estes ficheiros utilizando o serviço de [armazenamento Azure Blob](https://msdn.microsoft.com/library/dn385720.aspx) e anexe-os ao SQL Server em execução num VM Azure, particularmente com bases de dados muito grandes |
| [Converta a máquina no local em Hiper-V VHDs, carregue para o armazenamento Azure Blob e, em seguida, implante uma nova máquina virtual usando VHD carregado](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQL Server 2005 ou superior |SQL Server 2005 ou superior |[Limite de armazenamento Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Utilize ao [juntar a sua própria licença SQL Server,](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)ao migrar uma base de dados que irá executar numa versão mais antiga do SQL Server, ou quando migrar sistema e bases de dados de utilizadores em conjunto como parte da migração de bases de dados dependentes de outras bases de dados de utilizadores e/ou bases de dados do sistema. |
| [Unidade rígida de navio utilizando windows import/serviço de exportação](#ship-hard-drive) |SQL Server 2005 ou superior |SQL Server 2005 ou superior |[Limite de armazenamento Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Utilize o [Serviço de Importação/Exportação do Windows](../../../storage/common/storage-import-export-service.md) quando o método de cópia manual for demasiado lento, como por exemplo, com bases de dados muito grandes |
| [Utilize o Assistente de Réplica Add Azure](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQL Server 2012 ou superior |SQL Server 2012 ou superior |[Limite de armazenamento Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Minimiza o tempo de inatividade, use quando tiver uma implantação sempre no local |
| [Utilize a replicação transacional do Servidor SQL](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 ou superior |SQL Server 2005 ou superior |[Limite de armazenamento Azure VM](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Utilize quando necessitar de minimizar o tempo de inatividade e não tenha uma implantação sempre no local |

## <a name="backup-and-restore"></a>Cópia de segurança e restauro
Volte a colocar a base de dados com compressão, copie a cópia da cópia para o VM e, em seguida, restaure a base de dados. Se o seu ficheiro de reserva for superior a 1 TB, deve riscas-la porque o tamanho máximo de um disco VM é de 1 TB. Utilize os seguintes passos gerais para migrar uma base de dados do utilizador utilizando este método manual:

1. Execute uma cópia de segurança completa da base de dados para um local no local.
2. Crie ou carregue uma máquina virtual com a versão do Servidor SQL desejada.
3. Configurar a conectividade com base nos seus requisitos. Consulte [a Ligação a uma máquina virtual do Servidor SQL no Azure (Gestor de Recursos)](virtual-machines-windows-sql-connect.md).
4. Copie os seus ficheiros de cópia para o seu VM utilizando o ambiente de trabalho remoto, o Windows Explorer ou o comando de cópia a partir de um pedido de comando.

## <a name="backup-to-url-and-restore"></a>Backup para URL e restaurar
Em vez de fazer backup a um ficheiro local, pode utilizar a cópia de [segurança para URL](https://msdn.microsoft.com/library/dn435916.aspx) e, em seguida, restaurar do URL para o VM. Com o SQL Server 2016, os conjuntos de backup às riscas são suportados, são recomendados para o desempenho, e necessários para exceder os limites de tamanho por bolha. Para bases de dados muito grandes, recomenda-se a utilização do Serviço de [Importação/Exportação](../../../storage/common/storage-import-export-service.md) do Windows.

## <a name="detach-and-attach-from-url"></a>Desprender e anexar a partir de URL
Desaperte a sua base de dados e faça log files e transfira-os para [o armazenamento do Blob Azure](https://msdn.microsoft.com/library/dn385720.aspx). Em seguida, fixe a base de dados do URL no seu VM Azure. Utilize isto se quiser que os ficheiros de base de dados físicas residam no armazenamento blob. Isto pode ser útil para bases de dados muito grandes. Utilize os seguintes passos gerais para migrar uma base de dados do utilizador utilizando este método manual:

1. Retire os ficheiros da base de dados da instância de base de dados no local.
2. Copie os ficheiros de base de dados destacados no armazenamento de blob Azure utilizando o utilitário da [linha de comando AZCopy](../../../storage/common/storage-use-azcopy.md).
3. Fixe os ficheiros da base de dados do URL Azure à instância do Servidor SQL no VM Azure.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Converter em VM e carregar para URL e implementar como nova VM
Utilize este método para migrar todas as bases de dados do sistema e do utilizador numa instância do SQL Server no local para a máquina virtual Azure. Utilize os seguintes passos gerais para migrar uma instância inteira do SQL Server utilizando este método manual:

1. Converta máquinas físicas ou virtuais em VHDs Hiper-V.
2. Carregue os ficheiros VHD para o Armazenamento Azure utilizando o [cmdlet Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3. Implante uma nova máquina virtual utilizando o VHD carregado.

> [!NOTE]
> Para migrar uma aplicação inteira, considere usar a Recuperação do [Site Azure].](../../../site-recovery/site-recovery-overview.md)

## <a name="ship-hard-drive"></a>Unidade rígido de navio
Utilize o método do Serviço de [Importação/Exportação do Windows](../../../storage/common/storage-import-export-service.md) para transferir grandes quantidades de dados de ficheiros para o armazenamento da Blob Azure em situações em que o upload da rede é proibitivamente caro ou não é viável. Com este serviço, envia um ou mais discos rígidos contendo esses dados para um centro de dados Azure, onde os seus dados serão enviados para a sua conta de armazenamento.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a execução do Servidor SQL em Máquinas Virtuais Azure, consulte [o Servidor SQL na visão geral das Máquinas Virtuais Azure](virtual-machines-windows-sql-server-iaas-overview.md).

> [!TIP]
> Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](virtual-machines-windows-sql-server-iaas-faq.md).

Para obter instruções sobre a criação de uma Máquina Virtual do Servidor Azure SQL a partir de uma imagem capturada, consulte [Dicas & Truques sobre máquinas virtuais Azure SQL 'clonagem' a partir de imagens capturadas](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) no blog CSS SQL Server Engineers.

