---
title: Como utilizar o armazenamento Azure para a cópia de segurança do SQL Server e restaurar Microsoft Docs
description: Aprenda a apoiar o SQL Server para o Armazenamento Azure. Explica os benefícios de apoiar as bases de dados SQL para o Armazenamento Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MikeRayMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mikeray
ms.openlocfilehash: dc7d1140014b3d8aca327c54139b743e3740f5f6
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84049128"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Utilizar o Armazenamento do Azure para o Restauro e a Cópia de Segurança do SQL Server
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Começando pelo SQL Server 2012 SP1 CU2, já pode escrever cópias de segurança do SQL Server diretamente para o serviço de armazenamento Azure Blob. Pode utilizar esta funcionalidade para fazer o apoio e restaurar do serviço Azure Blob com uma base de dados do SQL Server no local ou uma base de dados do SQL Server numa máquina virtual Azure. O backup para a nuvem oferece benefícios de disponibilidade, armazenamento fora do local geo-replicado ilimitado e facilidade de migração de dados de e para a nuvem. Pode emitir declarações de BACKUP ou RESTAURO utilizando a Transact-SQL ou SMO.


## <a name="overview"></a>Descrição geral
O SQL Server 2016 introduz novas capacidades; pode utilizar [cópias de segurança instantâneas](https://msdn.microsoft.com/library/mt169363.aspx) para executar cópias de segurança quase instantâneas e restauros incrivelmente rápidos.

Este tópico explica por que razão pode optar por utilizar o armazenamento Azure para cópias de segurança SQL e, em seguida, descreve os componentes envolvidos. Pode utilizar os recursos fornecidos no final do artigo para aceder a walkthroughs e informações adicionais para começar a utilizar este serviço com as suas cópias de segurança do SQL Server.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Benefícios da utilização do serviço Azure Blob para backups do servidor SQL
Existem vários desafios que enfrenta ao apoiar o SQL Server. Estes desafios incluem gestão de armazenamento, risco de falha de armazenamento, acesso ao armazenamento fora do local e configuração de hardware. Muitos destes desafios são abordados utilizando o serviço de loja Azure Blob para backups do SQL Server. Considere os seguintes benefícios:

* **Facilidade de utilização**: Armazenar as suas cópias de segurança em blobs Azure pode ser uma opção conveniente, flexível e fácil de aceder fora do local. Criar armazenamento fora do local para as cópias de segurança do SQL Server pode ser tão fácil como modificar os seus scripts/empregos existentes para usar a sintaxe **DE URL BACKUP TO.** O armazenamento fora do local deve normalmente estar longe o suficiente da localização da base de dados de produção para evitar uma única catástrofe que possa afetar tanto os locais de base de dados fora do local como a base de dados de produção. Ao optar por [geo-replicar as suas bolhas Azure,](../../../storage/common/storage-redundancy.md)tem uma camada extra de proteção em caso de um desastre que possa afetar toda a região.
* **Arquivo de cópia**de segurança : O serviço de armazenamento Azure Blob oferece uma melhor alternativa à opção de fita frequentemente usada para arquivar cópias de segurança. O armazenamento de fitas pode requerer transporte físico para uma instalação fora do local e medidas para proteger os meios de comunicação. Armazenar as suas cópias de segurança no Armazenamento Azure Blob fornece uma opção instantânea, altamente disponível e uma opção de arquivamento durável.
* **Hardware gerido**: Não há nenhuma sobrecarga de gestão de hardware com serviços Azure. Os serviços Azure gerem o hardware e fornecem geo-replicação para redundância e proteção contra falhas de hardware.
* **Armazenamento ilimitado**: Ao permitir uma cópia de segurança direta às bolhas Azure, tem acesso a armazenamento praticamente ilimitado. Alternativamente, o backup de um disco de máquina virtual Azure tem limites baseados no tamanho da máquina. Há um limite para o número de discos que pode ligar a uma máquina virtual Azure para cópias de segurança. Este limite é de 16 discos para uma instância extra grande e menos para casos menores.
* **Disponibilidade de backup:** As cópias de segurança armazenadas em blobs Azure estão disponíveis em qualquer lugar e a qualquer momento e podem ser facilmente acedidas para restauros a um Servidor SQL no local ou a outro Servidor SQL a funcionar numa Máquina Virtual Azure, sem a necessidade de anexar/desprender ou desconectar ou transferir e prender o VHD.
* **Custo**: Pague apenas pelo serviço utilizado. Pode ser rentável como uma opção de arquivo off-site e backup. Consulte a calculadora de [preços Azure](https://go.microsoft.com/fwlink/?LinkId=277060 "Calculadora de preços")e o [artigo de Preços Do Azure](https://go.microsoft.com/fwlink/?LinkId=277059 "Artigo de preços") para mais informações.
* **Snapshots de armazenamento**: Quando os ficheiros de base de dados são armazenados numa bolha Azure e estiver a utilizar o SQL Server 2016, pode utilizar [cópias de segurança instantâneas](https://msdn.microsoft.com/library/mt169363.aspx) para executar cópias de segurança quase instantâneas e restauros incrivelmente rápidos.

Para mais detalhes, consulte [a Cópia de Segurança do Servidor SQL e restaure com o Serviço de Armazenamento Azure Blob](https://go.microsoft.com/fwlink/?LinkId=271617).

As duas secções seguintes introduzem o serviço de armazenamento Azure Blob, incluindo os componentes sQL Server necessários. É importante compreender os componentes e a sua interação para utilizar com sucesso a cópia de segurança e restauro do serviço de armazenamento Azure Blob.

## <a name="azure-blob-storage-service-components"></a>Componentes do serviço de armazenamento Azure Blob
Os seguintes componentes Azure são utilizados no apoio ao serviço de armazenamento Azure Blob.

| Componente | Descrição |
| --- | --- |
| **Conta de Armazenamento** |A conta de armazenamento é o ponto de partida para todos os serviços de armazenamento. Para aceder a um serviço de armazenamento Azure Blob, crie primeiro uma conta de Armazenamento Azure. Para mais informações sobre o serviço de armazenamento Azure Blob, consulte [Como utilizar o Serviço de Armazenamento Azure Blob](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Contentor** |Um recipiente fornece um agrupamento de um conjunto de bolhas, e pode armazenar um número ilimitado de Blobs. Para escrever uma cópia de segurança do SQL Server para um serviço Azure Blob, deve ter pelo menos o recipiente de raiz criado. |
| **Rio Blob** |Um ficheiro de qualquer tipo e tamanho. As bolhas são endereçadas utilizando o seguinte formato URL: **https://[conta de armazenamento].blob.core.windows.net/[container]/[blob]**. Para mais informações sobre a página Blobs, consulte [Understanding Block e Page Blobs](https://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>Componentes do servidor SQL
Os seguintes componentes do SQL Server são utilizados no apoio ao serviço de armazenamento Azure Blob.

| Componente | Descrição |
| --- | --- |
| **URL** |Um URL especifica um identificador de recursos uniformes (URI) a um ficheiro de reserva único. O URL é utilizado para fornecer a localização e o nome do ficheiro de backup do SQL Server. O URL deve apontar para uma bolha real, não apenas para um recipiente. Se a bolha não existir, é criada. Se for especificada uma bolha existente, a CÓPIA de Segurança falha, a menos que seja especificada a opção > WITH FORMAT. O seguinte é um exemplo do URL que especificaria no comando BACKUP: **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**. HTTPS é recomendado, mas não é necessário. |
| **Credencial** |A informação necessária para ligar e autenticar o serviço de armazenamento Azure Blob é armazenada como credencial.  Para que o SQL Server escreva cópias de segurança a um Azure Blob ou restaure a partir dele, deve ser criada uma credencial do Servidor SQL. Para mais informações, consulte [a Credencial do Servidor SQL](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> O Sql Server 2016 foi atualizado para suportar bolhas de blocos. Consulte [tutorial: Utilizando o serviço de armazenamento Microsoft Azure Blob com bases de dados SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx) para mais detalhes.
> 
> 

## <a name="next-steps"></a>Próximos passos
1. Crie uma conta Azure se ainda não tiver uma. Se está a avaliar o Azure, considere o [julgamento gratuito.](https://azure.microsoft.com/free/)
2. Em seguida, passe por um dos seguintes tutoriais que o percorrem através da criação de uma conta de armazenamento e realização de um restauro.
   
   * **SQL Server 2014**: [Tutorial: SQL Server 2014 Backup and Restore to Microsoft Azure Blob Storage Service](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **SQL Server 2016**: Tutorial: Utilizar o serviço de [armazenamento Microsoft Azure Blob com bases de dados SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)
3. Reveja a documentação adicional a partir da Cópia de [Segurança do Servidor SQL e restaure com o Microsoft Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148.aspx).

Se tiver algum problema, reveja o tópico [SQL Server Backup para URL Best Practices e Troubleshooting](https://msdn.microsoft.com/library/jj919149.aspx).

Para outras opções de backup do Servidor SQL e restaurar, consulte [backup e restauro para o Servidor SQL em Máquinas Virtuais Azure](backup-restore.md).

