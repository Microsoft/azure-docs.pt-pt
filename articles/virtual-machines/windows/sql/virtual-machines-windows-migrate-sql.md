---
title: Migrar uma base de dados do SQL Server para o SQL Server numa VM | Documentos da Microsoft
description: Saiba mais sobre como migrar uma base de dados de utilizador no local para o SQL Server numa máquina virtual do Azure.
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
ms.devlang: na
ms.topic: article
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 95acda60935e82b226a1a0e860b5fa8effb8e47e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60325703"
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Migrar uma base de dados do SQL Server para o SQL Server numa VM do Azure

Existem vários métodos para migrar uma base de dados de utilizador de SQL Server no local para o SQL Server numa VM do Azure. Este artigo resumidamente discutir diversos métodos e recomendar o melhor método para vários cenários.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="what-are-the-primary-migration-methods"></a>Quais são os métodos de migração primária?
Os métodos de migração primária são:

* Fazer cópia de segurança no local com a compactação e copie manualmente o ficheiro de cópia de segurança para a máquina virtual do Azure
* Efetuar uma cópia de segurança para URL e o restauro na máquina virtual do Azure partir do URL
* Desanexar e, em seguida, copie os ficheiros de dados e de registo para o armazenamento de Blobs do Azure e, em seguida, anexar ao SQL Server na VM do Azure a partir do URL
* Converter máquina física no local para o Hyper-V VHD, carregar para o armazenamento de Blobs do Azure e, em seguida, implementar como nova VM com a carregar o VHD
* Envie o disco rígido usando o serviço de importação/exportação do Windows
* Se tiver uma implementação de grupo de Disponibilidade AlwaysOn no local, utilize o [Assistente para adicionar Azure réplica](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) para criar uma réplica no Azure e, em seguida, a ativação pós-falha, os utilizadores a apontar para a instância de base de dados do Azure
* Utilizar o SQL Server [replicação transacional](https://msdn.microsoft.com/library/ms151176.aspx) para configurar a instância de servidor SQL do Azure como um subscritor e, em seguida, desative a replicação, os utilizadores a apontar para a instância de base de dados do Azure

> [!TIP]
> Também pode utilizar essas mesmas técnicas para mover bases de dados entre VMs do SQL Server no Azure. Por exemplo, não é possível suportado para atualizar uma VM de imagem da galeria do SQL Server de uma versão/edição para outra. Neste caso, deve criar uma nova VM do SQL Server com a nova versão/edição e, em seguida, utilize uma das técnicas de migração neste artigo para mover as bases de dados. 

## <a name="choosing-your-migration-method"></a>Escolher o método de migração
Para um desempenho de transferência de dados ideal, migre os ficheiros de base de dados na VM do Azure através de um ficheiro de cópia de segurança comprimido.

Para minimizar o período de indisponibilidade durante o processo de migração de base de dados, utilize a opção AlwaysOn ou a opção de replicação transacional.

Se não for possível utilizar os métodos acima, migre manualmente a base de dados. Usando esse método, geralmente começam com uma cópia de segurança de base de dados, seguida de uma cópia da cópia de segurança da base de dados para o Azure e, em seguida, efetue um restauro de base de dados. Pode também copiar os próprios arquivos de banco de dados para o Azure e, em seguida, anexe-os. Existem vários métodos que pode realizar este processo manual da migração de uma base de dados numa VM do Azure.

> [!NOTE]
> Ao atualizar para o SQL Server 2014 ou SQL Server 2016 a partir de versões anteriores do SQL Server, deve considerar se forem necessárias alterações. Recomendamos que abordar todas as dependências de funcionalidades não suportadas pela nova versão do SQL Server como parte do seu projeto de migração. Para obter mais informações sobre os cenários e edições suportadas, consulte [atualizar para o SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

A tabela seguinte apresenta uma lista de cada um dos métodos de migração primária e discute quando a utilização de cada método é mais adequada.

| Método | Versão da base de dados de origem | Versão da base de dados de destino | Restrição de tamanho de cópia de segurança da base de dados de origem | Notas |
| --- | --- | --- | --- | --- |
| [Fazer cópia de segurança no local com a compactação e copie manualmente o ficheiro de cópia de segurança para a máquina virtual do Azure](#backup-and-restore) |SQL Server 2005 ou superior |SQL Server 2005 ou superior |[Limite de armazenamento VM do Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Essa é uma técnica muito simple e bem testada para mover bases de dados em máquinas. |
| [Efetuar uma cópia de segurança para URL e o restauro na máquina virtual do Azure partir do URL](#backup-to-url-and-restore) |CU2 do SQL Server 2012 SP1 ou superior |CU2 do SQL Server 2012 SP1 ou superior |< 12.8 TB para o SQL Server 2016, caso contrário, < 1 TB | Esse método é apenas outra forma de mover o ficheiro de cópia de segurança para a VM com o armazenamento do Azure. |
| [Desanexar e, em seguida, copie os ficheiros de dados e de registo para o armazenamento de Blobs do Azure e, em seguida, anexar ao SQL Server na máquina virtual do Azure a partir do URL](#detach-and-attach-from-url) |SQL Server 2005 ou superior |SQL Server 2014 ou superior |[Limite de armazenamento VM do Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Utilize este método quando pretender [armazenar estes ficheiros ao utilizar o serviço de armazenamento de Blobs do Azure](https://msdn.microsoft.com/library/dn385720.aspx) e anexe-os para o SQL Server em execução numa VM do Azure, particularmente com bases de dados muito grandes |
| [Converter máquina no local para VHDs de Hyper-V, carregar para o armazenamento de Blobs do Azure e, em seguida, implementar uma nova máquina virtual com VHD carregado](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQL Server 2005 ou superior |SQL Server 2005 ou superior |[Limite de armazenamento VM do Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Utilizar quando [trazer a sua própria licença do SQL Server](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md), ao migrar uma base de dados que serão executados numa versão mais antiga do SQL Server ou após a migração de bases de dados do sistema e do usuário em conjunto como parte da migração da base de dados depende de outros bases de dados de utilizador e/ou bases de dados do sistema. |
| [Envie o disco rígido usando o serviço de importação/exportação do Windows](#ship-hard-drive) |SQL Server 2005 ou superior |SQL Server 2005 ou superior |[Limite de armazenamento VM do Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Utilize o [serviço de importação/exportação do Windows](../../../storage/common/storage-import-export-service.md) quando o método de cópia manual é muito lento, tal como com bancos de dados muito grandes |
| [Utilize o Assistente de réplica do Azure para adicionar](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQL Server 2012 ou superior |SQL Server 2012 ou superior |[Limite de armazenamento VM do Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Minimiza o tempo de inatividade, utilização quando tiver uma implementação em Always On no local |
| [Utilizar a replicação transacional do SQL Server](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 ou superior |SQL Server 2005 ou superior |[Limite de armazenamento VM do Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Utilize quando precisa minimizar o tempo de inatividade e não tiver uma implementação de local Always On |

## <a name="backup-and-restore"></a>Cópia de segurança e restauro
Cópia de segurança de base de dados com a compressão, copiar a cópia de segurança para a VM e, em seguida, restaure a base de dados. Se o ficheiro de cópia de segurança for superior a 1 TB, tem de o stripe porque o tamanho máximo de um disco VM é 1 TB. Utilize os seguintes passos gerais para migrar uma através deste método manual da base de dados do utilizador:

1. Efetue uma cópia de segurança completa da base de dados para uma localização no local.
2. Criar ou carregar uma máquina virtual com a versão do SQL Server pretendido.
3. Configure a conectividade com base nos seus requisitos. Ver [ligar a uma Máquina Virtual do SQL Server no Azure (Resource Manager)](virtual-machines-windows-sql-connect.md).
4. Copie os ficheiro ou ficheiros de cópia de segurança para a VM com o ambiente de trabalho remoto, no Windows Explorer ou o comando de cópia de um prompt de comando.

## <a name="backup-to-url-and-restore"></a>Cópia de segurança e restauro do URL
Em vez de backup num arquivo local, pode utilizar o [cópia de segurança para URL](https://msdn.microsoft.com/library/dn435916.aspx) e, em seguida, restaurar a partir do URL para a VM. Com o SQL Server 2016, repartidos conjuntos de cópia de segurança são suportados, são recomendados para desempenho e necessário para exceder os limites de tamanho por blob. Bases de dados muito grandes, a utilização do [serviço de importação/exportação do Windows](../../../storage/common/storage-import-export-service.md) é recomendado.

## <a name="detach-and-attach-from-url"></a>Desanexar e anexar a partir do URL
Desligue os ficheiros de registo e base de dados e transferi-las para [armazenamento de Blobs do Azure](https://msdn.microsoft.com/library/dn385720.aspx). Em seguida, anexe a base de dados da URL na sua VM do Azure. Utilize esta opção se pretender que os ficheiros de base de dados física resida no armazenamento de Blobs. Isso pode ser útil para bases de dados muito grandes. Utilize os seguintes passos gerais para migrar uma através deste método manual da base de dados do utilizador:

1. Desligar os ficheiros de base de dados da instância de base de dados no local.
2. Copie os ficheiros de base de dados desanexada para o armazenamento de Blobs do Azure com o [utilitário de linha de comandos do AZCopy](../../../storage/common/storage-use-azcopy.md).
3. Anexe os ficheiros de base de dados a partir do URL do Azure para a instância do SQL Server numa VM do Azure.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Converter em VM e carregar para URL e implementar como nova VM
Utilize este método para migrar todos os sistema e do usuário bases de dados numa instância do SQL Server no local para a máquina virtual do Azure. Utilize os seguintes passos gerais para migrar uma toda instância de SQL Server através deste método manual:

1. Converta máquinas físicas ou virtuais em VHDs de Hyper-V.
2. Carregar ficheiros VHD para o armazenamento do Azure com o [cmdlet Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3. Implemente uma nova máquina virtual com o VHD carregado.

> [!NOTE]
> Para migrar um aplicativo inteiro, considere usar [do Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)].

## <a name="ship-hard-drive"></a>Envie o disco rígido
Utilize o [método de serviço de importação/exportação do Windows](../../../storage/common/storage-import-export-service.md) para transferir grandes quantidades de dados de ficheiro para o armazenamento de Blobs do Azure em situações em que o carregamento através da rede está proibitivamente cara ou não é viável. Com este serviço, enviar um ou mais unidades de disco rígido que contém dados para um centro de dados do Azure, onde os dados serão carregados para a sua conta de armazenamento.

## <a name="next-steps"></a>Próximos Passos
Para obter mais informações sobre a execução do SQL Server em máquinas de virtuais do Azure, consulte [SQL Server em Descrição geral de máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

> [!TIP]
> Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](virtual-machines-windows-sql-server-iaas-faq.md).

Para obter instruções sobre como criar uma Máquina Virtual do Azure SQL Server a partir de uma imagem capturada, consulte [dicas e truques sobre "clonar" máquinas de virtuais de SQL do Azure a partir de imagens capturadas](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) no blogue de engenheiros do CSS SQL Server.

