---
title: Como utilizar o Azure Storage para backup do SQL Server e restaurar Microsoft Docs
description: Saiba como fazer o back up SQL Server para o Azure Storage. Explica os benefícios de fazer backup das bases de dados SQL para o Azure Storage.
services: virtual-machines-windows
documentationcenter: ''
author: MikeRayMSFT
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.openlocfilehash: c59fc03e36b738f7eaa91fcd13dda1cf0f6497a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91272808"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Use o armazenamento Azure para a cópia de segurança do SQL Server e restaure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

A partir do SQL Server 2012 SP1 CU2, pode agora escrever backups do SQL Server diretamente para o armazenamento do Azure Blob. Pode utilizar esta funcionalidade para fazer o back up e restaurar a partir do armazenamento do Azure Blob e de uma base de dados do SQL Server. O backup para a nuvem oferece benefícios de disponibilidade, armazenamento geo-replicado ilimitado fora do local, e facilidade de migração de dados de e para a nuvem. Pode emitir declarações backup ou RESTORE utilizando Transact-SQL ou SMO.

## <a name="overview"></a>Descrição geral
SQL Server 2016 introduz novas capacidades; pode utilizar [a cópia de segurança instantânea para](https://msdn.microsoft.com/library/mt169363.aspx) realizar cópias de segurança quase instantâneas e restauros incrivelmente rápidos.

Este tópico explica por que pode optar por utilizar o Azure Storage para cópias de segurança do SQL Server e, em seguida, descreve os componentes envolvidos. Pode utilizar os recursos fornecidos no final do artigo para aceder a walk-throughs e informações adicionais para começar a utilizar este serviço com as suas cópias de segurança do SQL Server.

## <a name="benefits-of-using-azure-blob-storage-for-sql-server-backups"></a>Benefícios da utilização do armazenamento Azure Blob para backups do SQL Server
Existem vários desafios que enfrenta ao fazer backup do SQL Server. Estes desafios incluem gestão de armazenamento, risco de falha de armazenamento, acesso ao armazenamento fora do local e configuração de hardware. Muitos destes desafios são abordados utilizando o armazenamento Azure Blob para backups do SQL Server. Considere os seguintes benefícios:

* **Facilidade de utilização**: Armazenar as suas cópias de segurança em blobs Azure pode ser uma opção conveniente, flexível e de fácil acesso fora do local. A criação de armazenamento fora do local para as cópias de segurança do SQL Server pode ser tão fácil como modificar os scripts/empregos existentes para utilizar a sintaxe **backup TO URL.** O armazenamento fora do local deve normalmente estar longe o suficiente do local da base de dados de produção para evitar um único desastre que possa ter impacto tanto nos locais de base de dados fora do local como na base de dados de produção. Ao optar por [geo-replicar as suas bolhas Azure,](../../../storage/common/storage-redundancy.md)tem uma camada extra de proteção em caso de desastre que possa afetar toda a região.
* **Arquivo de reserva**: O armazenamento Azure Blob oferece uma melhor alternativa à opção de fita frequentemente usada para arquivar cópias de segurança. O armazenamento de fitas pode exigir transporte físico para uma instalação fora do local e medidas para proteger os meios de comunicação. Armazenar as suas cópias de segurança no armazenamento Azure Blob proporciona uma opção instantânea, altamente disponível e uma opção de arquivamento durável.
* **Hardware gerido**: Não existe sobrecarga de gestão de hardware com serviços Azure. Os serviços Azure gerem o hardware e fornecem geo-replicação para redundância e proteção contra falhas de hardware.
* **Armazenamento ilimitado**: Ao permitir uma cópia de segurança direta para as bolhas Azure, tem acesso a armazenamento praticamente ilimitado. Alternativamente, o backup até um disco de máquina virtual Azure tem limites baseados no tamanho da máquina. Existe um limite para o número de discos que pode anexar a uma máquina virtual Azure para cópias de segurança. Este limite é de 16 discos para um caso extra grande e menos para casos menores.
* **Disponibilidade de cópia de segurança**: As cópias de segurança armazenadas em blobs Azure estão disponíveis em qualquer lugar e a qualquer momento e podem ser facilmente acedidas para restauros a uma instância do SQL Server, sem a necessidade de anexar/desmontar ou descarregar e ligar o VHD.
* **Custo**: Pague apenas pelo serviço utilizado. Pode ser rentável como uma opção de arquivo off-site e backup. Consulte a [calculadora de preços da Azure](https://go.microsoft.com/fwlink/?LinkId=277060 "Calculadora de preços")e o [artigo Azure Pricing](https://go.microsoft.com/fwlink/?LinkId=277059 "Artigo de preços") para obter mais informações.
* **Imagens de armazenamento**: Quando os ficheiros de base de dados são armazenados numa bolha Azure e estiver a utilizar o SQL Server 2016, pode utilizar [cópias de segurança instantâneas](https://msdn.microsoft.com/library/mt169363.aspx) para realizar cópias de segurança quase instantâneas e restauros incrivelmente rápidos.

Para obter mais detalhes, consulte [a Cópia de Segurança do Servidor SQL e restaure com o armazenamento Azure Blob](https://go.microsoft.com/fwlink/?LinkId=271617).

As duas secções seguintes introduzem o armazenamento Azure Blob, incluindo os componentes necessários do SQL Server. É importante compreender os componentes e a sua interação para utilizar com sucesso a cópia de segurança e restaurar a partir do armazenamento da Azure Blob.

## <a name="azure-blob-storage-components"></a>Componentes de armazenamento Azure Blob
Os seguintes componentes Azure são utilizados quando se baseia no armazenamento da Azure Blob.

| Componente | Descrição |
| --- | --- |
| **Conta de armazenamento** |A conta de armazenamento é o ponto de partida para todos os serviços de armazenamento. Para aceder ao armazenamento do Azure Blob, primeiro crie uma conta de Armazenamento Azure. Para obter mais informações sobre o armazenamento da Azure Blob, consulte [como utilizar o armazenamento Azure Blob](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/). |
| **Container** (Contentor) |Um recipiente fornece um agrupamento de um conjunto de bolhas, e pode armazenar um número ilimitado de Blobs. Para escrever uma cópia de segurança do SQL Server para o armazenamento do Azure Blob, deve ter pelo menos o recipiente de raiz criado. |
| **Blob** |Um arquivo de qualquer tipo e tamanho. As bolhas são endereçadas utilizando o seguinte formato URL: **https://[conta de armazenamento].blob.core.windows.net/[contentor]/[blob]**. Para obter mais informações sobre as manchas de página, consulte [Understanding Block e Page Blobs](https://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>Componentes do Servidor SQL
Os seguintes componentes do SQL Server são utilizados quando se baseia no armazenamento do Azure Blob.

| Componente | Descrição |
| --- | --- |
| **URL** |Um URL especifica um identificador de recursos uniforme (URI) para um ficheiro de backup único. O URL é utilizado para fornecer a localização e o nome do ficheiro de backup do SQL Server. O URL deve apontar para uma bolha real, não apenas um recipiente. Se a bolha não existir, é criada. Se for especificada uma bolha existente, o BACKUP falha, a menos que a opção > COM FORMATO seja especificada. Segue-se um exemplo do URL que especificaria no comando BACKUP: **http[s][storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**. HTTPS é recomendado, mas não é necessário. |
| **Credencial** |As informações necessárias para ligar e autenticar ao armazenamento da Azure Blob são armazenadas como credenciais. Para que o SQL Server escreva cópias de segurança para uma Mancha Azure ou seja restaurada a partir dele, deve ser criada uma credencial SQL Server. Para obter mais informações, consulte [a Credencial do Servidor SQL](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> O SQL Server 2016 foi atualizado para suportar bolhas de blocos. Consulte [Tutorial: Utilizar o armazenamento do Microsoft Azure Blob com bases de dados SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx) para obter mais detalhes.
> 
> 

## <a name="next-steps"></a>Passos seguintes
1. Crie uma conta Azure se ainda não tiver uma. Se estiver a avaliar o Azure, considere o [teste gratuito.](https://azure.microsoft.com/free/)
2. Em seguida, passe por um dos seguintes tutoriais que o acompanham através da criação de uma conta de armazenamento e realização de um restauro.
   
   * **SQL Server 2014**: [Tutorial: SQL Server 2014 Backup e Restaurar para o armazenamento do Microsoft Azure Blob](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **SQL Server 2016**: [Tutorial: Utilização do armazenamento do Microsoft Azure Blob com bases de dados SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)
3. Reveja documentação adicional a partir de [Backup e Restauro do SQL Server com o armazenamento microsoft Azure Blob](https://msdn.microsoft.com/library/jj919148.aspx).

Se tiver algum problema, reveja o tópico [SQL Server Backup para URL Best Practices e Troubleshooting](https://msdn.microsoft.com/library/jj919149.aspx).

Para outras opções de backup e restauro do SQL Server, consulte [Backup e Restore for SQL Server em Azure Virtual Machines](backup-restore.md).

