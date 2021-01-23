---
title: Migrar uma base de dados do SQL Server para o SQL Server numa máquina virtual | Microsoft Docs
description: Saiba como migrar uma base de dados de utilizador no local para o SQL Server numa máquina virtual Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.subservice: migration
ms.topic: how-to
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f6e9009040d2d02702f8a71c352716491d07d1f7
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704309"
---
# <a name="migrate-a-sql-server-database-to-sql-server-on-an-azure-virtual-machine"></a>Migrar uma base de dados do SQL Server para o SQL Server numa máquina virtual Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Existem várias formas de migrar uma base de dados de utilizadores do SQL Server no local para o SQL Server numa máquina virtual Azure (VM). Este artigo discutirá brevemente vários métodos e recomendará o melhor método para vários cenários.


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > O SQL Server 2008 e o SQL Server 2008 R2 aproximam-se [do fim do seu ciclo de vida de suporte](https://www.microsoft.com/sql-server/sql-server-2008) para casos no local. Para estender o suporte, pode migrar a sua instância SQL Server para um Azure VM ou comprar Atualizações de Segurança Alargadas para mantê-lo no local. Para obter mais informações, consulte [o suporte para O SQL Server 2008 e 2008 R2 com Azure](sql-server-2008-extend-end-of-support.md)

## <a name="what-are-the-primary-migration-methods"></a>Quais são os métodos primários de migração?

Os principais métodos de migração são:

* Execute uma cópia de segurança no local utilizando a compressão e, em seguida, copie manualmente o ficheiro de cópia de segurança no Azure VM.
* Execute uma cópia de segurança para URL e, em seguida, restabeleça no Azure VM a partir do URL.
* Retire os ficheiros de dados e registos, copie-os para o armazenamento do Azure Blob e, em seguida, anexe-os ao SQL Server no Azure VM a partir do URL.
* Converta a máquina física no local para um Hiper-V VHD, faça o upload para o armazenamento de Azure Blob e, em seguida, implemente-o como novo VM utilizando VHD carregado.
* Envie o disco rígido utilizando o Serviço de Importação/Exportação de Janelas.
* Se tiver uma implementação do Grupo AlwaysOn Availability no local, utilize o [Add Azure Replica Wizard](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability) para criar uma réplica em Azure, failover e apontar os utilizadores para a instância da base de dados Azure.
* Utilize a [replicação transacional](/sql/relational-databases/replication/transactional/transactional-replication) do SQL Server para configurar a instância do Servidor SQL Azure como assinante, desativar a replicação e apontar os utilizadores para a instância da base de dados Azure.

> [!TIP]
> Também pode utilizar estas mesmas técnicas para mover bases de dados entre VMs do SQL Server em Azure. Por exemplo, não existe uma forma suportada de atualizar um VM de imagem de galeria SQL Server de uma versão/edição para outra. Neste caso, deverá criar um novo SQL Server VM com a nova versão/edição e, em seguida, utilizar uma das técnicas de migração deste artigo para mover as suas bases de dados. 

## <a name="choose-a-migration-method"></a>Escolha um método de migração

Para obter o melhor desempenho da transferência de dados, migrar os ficheiros da base de dados para o VM Azure utilizando um ficheiro de cópia de segurança comprimido.

Para minimizar o tempo de inatividade durante o processo de migração da base de dados, utilize a opção AlwaysOn ou a opção de replicação transacional.

Se não for possível utilizar os métodos acima referidos, migrar manualmente a sua base de dados. Geralmente, começa-se com uma cópia de segurança da base de dados, segue-a com uma cópia da cópia da base de dados para o Azure e, em seguida, restaura-se a base de dados. Também pode copiar os ficheiros da base de dados em Azure e depois anexá-los. Existem vários métodos através dos quais pode realizar este processo manual de migração de uma base de dados para um VM Azure.

> [!NOTE]
> Quando fizer o upgrade para o SQL Server 2014 ou SQL Server 2016 a partir de versões mais antigas do SQL Server, deve considerar se são necessárias alterações. Recomendamos que aborde todas as dependências de funcionalidades não suportadas pela nova versão do SQL Server como parte do seu projeto de migração. Para obter mais informações sobre as edições e cenários suportados, consulte [Upgrade para SQL Server](/sql/database-engine/install-windows/upgrade-sql-server).

O quadro que se segue lista cada um dos métodos primários de migração e discute quando a utilização de cada método é mais adequada.

| Método | Versão da base de dados de origem | Versão da base de dados de destino | Restrição do tamanho do tamanho da cópia de segurança da base de dados de | Notas |
| --- | --- | --- | --- | --- |
| [Execute uma cópia de segurança no local utilizando a compressão e copie manualmente o ficheiro de backup na máquina virtual Azure](#back-up-and-restore) |SQL Server 2005 ou maior |SQL Server 2005 ou maior |[Limite de armazenamento Azure VM](../../../index.yml) | Esta técnica é simples e bem testada para mover bases de dados através de máquinas. |
| [Execute uma cópia de segurança para URL e restaure na máquina virtual Azure a partir do URL](#backup-to-url-and-restore-from-url) |SQL Server 2012 SP1 CU2 ou superior | SQL Server 2012 SP1 CU2 ou superior | < 12.8 TB para SQL Server 2016, caso contrário < 1 TB | Este método é apenas mais uma forma de mover o ficheiro de backup para o VM usando o armazenamento Azure. |
| [Desprender e, em seguida, copiar os ficheiros de dados e registos para o armazenamento do Azure Blob e, em seguida, anexar ao SQL Server em Azure virtual machine a partir de URL](#detach-and-attach-from-a-url) | SQL Server 2005 ou maior |SQL Server 2014 ou maior | [Limite de armazenamento Azure VM](../../../index.yml) | Utilize este método quando planeia [armazenar estes ficheiros utilizando o serviço de armazenamento Azure Blob](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) e anexá-los ao SQL Server em execução num VM Azure, particularmente com bases de dados muito grandes |
| [Converter na máquina no local para VHDs Hiper-V, fazer upload para o armazenamento do Azure Blob e, em seguida, implementar uma nova máquina virtual usando VHD carregado](#convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm) |SQL Server 2005 ou maior |SQL Server 2005 ou maior |[Limite de armazenamento Azure VM](../../../index.yml) |Utilize ao [aproximar a sua própria licença SQL Server,](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md)ao migrar uma base de dados que irá executar numa versão mais antiga do SQL Server, ou quando migrar sistemas e bases de dados de utilizadores em conjunto como parte da migração da base de dados dependente de outras bases de dados de utilizadores e/ou bases de dados do sistema. |
| [Disco rígido de navio utilizando serviço de importação/exportação de janelas](#ship-a-hard-drive) |SQL Server 2005 ou maior |SQL Server 2005 ou maior |[Limite de armazenamento Azure VM](../../../index.yml) |Utilize o [Serviço de Importação/Exportação do Windows](../../../import-export/storage-import-export-service.md) quando o método de cópia manual for demasiado lento, tal como com bases de dados muito grandes |
| [Utilize o Assistente de Réplica Add Azure](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability) |SQL Server 2012 ou maior |SQL Server 2012 ou maior |[Limite de armazenamento Azure VM](../../../index.yml) |Minimiza o tempo de inatividade, use quando tiver uma implantação Always On no local |
| [Use a replicação transacional do SQL Server](/sql/relational-databases/replication/transactional/transactional-replication) |SQL Server 2005 ou maior |SQL Server 2005 ou maior |[Limite de armazenamento Azure VM](../../../index.yml) |Use quando precisar de minimizar o tempo de inatividade e não tenha uma implantação Always On no local |

## <a name="back-up-and-restore"></a>Criar cópias de segurança e restauro

Faça o backup da sua base de dados com compressão, copie a cópia de segurança para o VM e, em seguida, restaure a base de dados. Se o seu ficheiro de cópia de segurança for superior a 1 TB, tem de criar um conjunto às riscas porque o tamanho máximo de um disco VM é de 1 TB. Utilize as seguintes etapas gerais para migrar uma base de dados do utilizador utilizando este método manual:

1. Execute uma cópia de segurança completa da base de dados para um local no local.
2. Criar ou carregar uma máquina virtual com a versão desejada do SQL Server.
3. Configurar conectividade com base nos seus requisitos. Consulte [a Ligação a uma máquina virtual do servidor SQL em Azure (Gestor de Recursos)](ways-to-connect-to-sql.md).
4. Copie os ficheiros de cópias de segurança para o seu VM utilizando o ambiente de trabalho remoto, o Windows Explorer ou o comando de cópia a partir de uma solicitação de comando.

## <a name="backup-to-url-and-restore-from-url"></a>Backup para URL e Restaurar a partir de URL

Em vez de fazer backup num ficheiro local, pode utilizar [backup para URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) e, em seguida, restaurar de URL para o VM. O SQL Server 2016 suporta conjuntos de backup às riscas. São recomendados para o desempenho e são obrigados a exceder os limites de tamanho por bolha. Para bases de dados muito grandes, recomenda-se a utilização do Serviço de [Importação/Exportação](../../../import-export/storage-import-export-service.md) do Windows.

## <a name="detach-and-attach-from-a-url"></a>Separar e anexar de um URL

Desprete a sua base de dados e registar ficheiros e transfira-os para [o armazenamento da Azure Blob](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure). Em seguida, anexe a base de dados do URL no seu Azure VM. Utilize este método se quiser que os ficheiros de base de dados físicos residam no armazenamento blob, o que pode ser útil para bases de dados muito grandes. Utilize as seguintes etapas gerais para migrar uma base de dados do utilizador utilizando este método manual:

1. Retire os ficheiros da base de dados da base de dados no local.
2. Copie os ficheiros de base de dados separados para o armazenamento Azure Blob utilizando o [utilitário de linha de comando AZCopy](../../../storage/common/storage-use-azcopy-v10.md).
3. Anexar os ficheiros de base de dados do URL Azure à instância do SQL Server no Azure VM.

## <a name="convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm"></a>Converta-se em VM, faça upload para um URL e implemente como um novo VM

Utilize este método para migrar todas as bases de dados do sistema e dos utilizadores numa instância do SQL Server no local para uma máquina virtual Azure. Utilize os seguintes passos gerais para migrar uma instância do SqL Server inteira utilizando este método manual:

1. Converter máquinas físicas ou virtuais em VHDs Hiper-V.
2. Faça o upload dos ficheiros VHD para o Azure Storage utilizando o [cmdlet Add-AzureVHD](/previous-versions/azure/dn495173(v=azure.100)).
3. Implemente uma nova máquina virtual utilizando o VHD carregado.

> [!NOTE]
> Para migrar uma aplicação inteira, considere a utilização da [Recuperação do Local de Azure].](../../../site-recovery/site-recovery-overview.md)

## <a name="ship-a-hard-drive"></a>Envie um disco rígido

Utilize o [método do Windows Import/Export Service](../../../import-export/storage-import-export-service.md) para transferir grandes quantidades de dados de ficheiros para o armazenamento do Azure Blob em situações em que o upload da rede é proibitivamente caro ou não é viável. Com este serviço, envia um ou mais discos rígidos contendo esses dados para um centro de dados Azure onde os seus dados serão enviados para a sua conta de armazenamento.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, consulte [o SQL Server na visão geral das máquinas virtuais Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).

> [!TIP]
> Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](frequently-asked-questions-faq.md).

Para obter instruções sobre a criação do SQL Server numa Máquina Virtual Azure a partir de uma imagem capturada, consulte [Dicas & Truques sobre a 'clonagem' de máquinas virtuais Azure SQL a partir de imagens capturadas](/archive/blogs/psssql/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images) no blog CSS SQL Server Engineers.