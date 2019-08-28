---
title: Como usar o armazenamento do Azure para SQL Server Backup e restauração | Microsoft Docs
description: Saiba como fazer backup de SQL Server no armazenamento do Azure. Explica os benefícios de fazer backup de bancos de dados SQL no armazenamento do Azure.
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
ms.openlocfilehash: cb19dc7262721e672bd3f54b32db9188dad7fee0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101882"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Usar o armazenamento do Azure para SQL Server Backup e restauração
## <a name="overview"></a>Descrição geral
A partir do SQL Server 2012 SP1 CU2, agora você pode gravar SQL Server backups diretamente no serviço de armazenamento de BLOBs do Azure. Você pode usar essa funcionalidade para fazer backup e restauração do serviço blob do Azure com um banco de dados SQL Server local ou um banco de dados SQL Server em uma máquina virtual do Azure. O backup na nuvem oferece benefícios de disponibilidade, armazenamento fora do local de replicação geográfica ilimitada e facilidade de migração de dados de e para a nuvem. Você pode emitir instruções de BACKUP ou restauração usando Transact-SQL ou SMO.

SQL Server 2016 apresenta novos recursos; Você pode usar o [backup de instantâneo de arquivo](https://msdn.microsoft.com/library/mt169363.aspx) para executar backups quase instantâneos e restaurações incrivelmente rápidas.

Este tópico explica por que você pode optar por usar o armazenamento do Azure para backups do SQL e, em seguida, descreve os componentes envolvidos. Você pode usar os recursos fornecidos no final do artigo para acessar orientações e informações adicionais para começar a usar esse serviço com os backups do SQL Server.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Benefícios de usar o serviço blob do Azure para backups SQL Server
Há vários desafios que você enfrenta ao fazer backup de SQL Server. Esses desafios incluem o gerenciamento de armazenamento, o risco de falha de armazenamento, o acesso ao armazenamento fora do local e a configuração de hardware. Muitos desses desafios são abordados usando o serviço de repositório de blob do Azure para backups SQL Server. Considere os seguintes benefícios:

* **Facilidade de uso**: O armazenamento de seus backups em BLOBs do Azure pode ser uma opção de acesso fácil, flexível e simples de acessar. A criação de armazenamento externo para seus backups SQL Server pode ser tão fácil quanto modificar seus scripts/trabalhos existentes para usar a sintaxe de **backup para URL** . O armazenamento fora do local normalmente deve ser muito suficiente no local do banco de dados de produção para evitar um único desastre que possa afetar os locais do banco de dados fora do local e de produção. Ao optar por [replicar geograficamente seus BLOBs do Azure](../../../storage/common/storage-redundancy.md), você tem uma camada extra de proteção em caso de desastre que pode afetar toda a região.
* **Arquivo morto de backup**: O serviço de armazenamento de BLOBs do Azure oferece uma alternativa melhor à opção de fita usada com frequência para arquivar backups. O armazenamento em fita pode exigir transporte físico para uma instalação externa e medidas para proteger a mídia. Armazenar os backups no armazenamento de BLOBs do Azure fornece uma opção de arquivamento instantânea, altamente disponível e durável.
* **Hardware gerenciado**: Não há sobrecarga de gerenciamento de hardware com os serviços do Azure. Os serviços do Azure gerenciam o hardware e fornecem replicação geográfica para redundância e proteção contra falhas de hardware.
* **Armazenamento ilimitado**: Ao habilitar um backup direto para BLOBs do Azure, você tem acesso a armazenamento praticamente ilimitado. Como alternativa, o backup em um disco de máquina virtual do Azure tem limites com base no tamanho da máquina. Há um limite para o número de discos que você pode anexar a uma máquina virtual do Azure para backups. Esse limite é de 16 discos para uma instância grande extra e menos para instâncias menores.
* **Disponibilidade do backup**: Os backups armazenados em BLOBs do Azure estão disponíveis em qualquer lugar e a qualquer momento e podem ser acessados facilmente para restaurações em um SQL Server local ou em outro SQL Server em execução em uma máquina virtual do Azure, sem a necessidade de anexar/desanexar/desanexação de banco de dados ou baixar e anexando o VHD.
* **Custo**: Pague apenas pelo serviço que é usado. Pode ser econômico como uma opção de arquivamento fora do local e de backup. Consulte a [calculadora]de preços do Azure(https://go.microsoft.com/fwlink/?LinkId=277060 "calculadora de preços")e o artigo de preços [do Azure](https://go.microsoft.com/fwlink/?LinkId=277059 "") para obter mais informações.
* **Instantâneos de armazenamento**: Quando os arquivos de banco de dados são armazenados em um blob do Azure e você está usando SQL Server 2016, você pode usar o [backup de instantâneo de arquivo](https://msdn.microsoft.com/library/mt169363.aspx) para executar backups quase instantâneos e restaurações incrivelmente rápidas.

Para obter mais detalhes, consulte [SQL Server Backup e restauração com o serviço de armazenamento de BLOBs do Azure](https://go.microsoft.com/fwlink/?LinkId=271617).

As duas seções a seguir introduzem o serviço de armazenamento de BLOBs do Azure, incluindo os componentes de SQL Server necessários. É importante entender os componentes e sua interação para usar com êxito o backup e a restauração do serviço de armazenamento de BLOBs do Azure.

## <a name="azure-blob-storage-service-components"></a>Componentes do serviço de armazenamento de BLOBs do Azure
Os componentes do Azure a seguir são usados ao fazer backup para o serviço de armazenamento de BLOBs do Azure.

| Componente | Descrição |
| --- | --- |
| **Storage Account** |A conta de armazenamento é o ponto de partida para todos os serviços de armazenamento. Para acessar um serviço de armazenamento de BLOBs do Azure, primeiro crie uma conta de armazenamento do Azure. Para obter mais informações sobre o serviço de armazenamento de BLOBs do Azure, consulte [como usar o serviço de armazenamento de BLOBs do Azure](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Contentor** |Um contêiner fornece um agrupamento de um conjunto de BLOBs e pode armazenar um número ilimitado de BLOBs. Para gravar um backup de SQL Server em um serviço blob do Azure, você deve ter pelo menos o contêiner raiz criado. |
| **Blob** |Um arquivo de qualquer tipo e tamanho. Os BLOBs são endereçáveis usando o seguinte formato de URL: **https://[conta de armazenamento]. blob. Core. Windows. net/[Container]/[blob]** . Para obter mais informações sobre blobs de página, consulte [noções básicas sobre blobs de bloco e de página](https://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>Componentes do SQL Server
Os seguintes SQL Server componentes são usados ao fazer backup para o serviço de armazenamento de BLOBs do Azure.

| Componente | Descrição |
| --- | --- |
| **URL** |Uma URL especifica um URI (Uniform Resource Identifier) para um arquivo de backup exclusivo. A URL é usada para fornecer o local e o nome do arquivo de backup SQL Server. A URL deve apontar para um blob real, não apenas para um contêiner. Se o BLOB não existir, ele será criado. Se um blob existente for especificado, o BACKUP falhará, a menos que a opção > WITH FORMAT seja especificada. Veja a seguir um exemplo da URL que você especificaria no comando de BACKUP: **http [s]://[storageAccount]. blob. Core. Windows. net/[Container]/[filename. bak]** . O HTTPS é recomendado, mas não é necessário. |
| **Credencial** |As informações necessárias para se conectar e autenticar no serviço de armazenamento de BLOBs do Azure são armazenadas como uma credencial.  Para SQL Server gravar backups em um blob do Azure ou restaurá-lo, uma credencial de SQL Server deve ser criada. Para obter mais informações, consulte [SQL Server Credential](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> O SQL Server 2016 foi atualizado para dar suporte a blobs de blocos. Consulte [o tutorial: Usando o serviço de armazenamento de BLOBs Microsoft Azure com bancos](https://msdn.microsoft.com/library/dn466438.aspx) de dados SQL Server 2016 para obter mais detalhes.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
1. Crie uma conta do Azure se você ainda não tiver uma. Se você estiver avaliando o Azure, considere a [avaliação gratuita](https://azure.microsoft.com/free/).
2. Em seguida, siga um dos tutoriais a seguir para orientá-lo na criação de uma conta de armazenamento e na realização de uma restauração.
   
   * **SQL Server 2014**: [Destina SQL Server Backup e restauração do 2014 para Microsoft Azure serviço](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx)de armazenamento de BLOBs.
   * **SQL Server 2016**: [Destina Usando o serviço de armazenamento de BLOBs Microsoft Azure com bancos de dados SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)
3. Examine a documentação adicional que começa com [SQL Server Backup e restauração com Microsoft Azure serviço de armazenamento de BLOBs](https://msdn.microsoft.com/library/jj919148.aspx).

Se você tiver problemas, examine o tópico [SQL Server práticas recomendadas de backup para URL e solução de problemas](https://msdn.microsoft.com/library/jj919149.aspx).

Para outras SQL Server opções de backup e restauração, consulte [backup e restauração para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).

