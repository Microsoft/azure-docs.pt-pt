---
title: Decidindo quando usar BLOBs do Azure, arquivos do Azure ou discos do Azure
description: Saiba mais sobre as diferentes maneiras de armazenar e acessar dados no Azure para ajudá-lo a decidir qual tecnologia usar.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4b1a42e25a6d8c7b4a3c24dffcb858ffe63dd10b
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671036"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Decidindo quando usar BLOBs do Azure, arquivos do Azure ou discos do Azure

O Microsoft Azure fornece vários recursos no armazenamento do Azure para armazenar e acessar seus dados na nuvem. Este artigo aborda os arquivos, BLOBs e discos do Azure e foi projetado para ajudá-lo a escolher entre esses recursos.

## <a name="scenarios"></a>Cenários

A tabela a seguir compara arquivos, BLOBs e discos e mostra cenários de exemplo apropriados para cada um.

| Funcionalidade | Descrição | Quando utilizar |
|--------------|-------------|-------------|
| **Ficheiros do Azure** | Fornece uma interface SMB, bibliotecas de cliente e uma [interface REST](/rest/api/storageservices/file-service-rest-api) que permite o acesso de qualquer lugar a arquivos armazenados. | Você deseja "aumentar e deslocar" um aplicativo para a nuvem que já usa as APIs do sistema de arquivos nativo para compartilhar dados entre ele e outros aplicativos em execução no Azure.<br/><br/>Você deseja armazenar ferramentas de desenvolvimento e depuração que precisam ser acessadas de várias máquinas virtuais. |
| **BLOBs do Azure** | Fornece bibliotecas de cliente e uma [interface REST](/rest/api/storageservices/blob-service-rest-api) que permite que dados não estruturados sejam armazenados e acessados em grande escala em blobs de blocos.<br/><br/>Também dá suporte a [Azure data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) para soluções de análise de Big data empresarial. | Você deseja que seu aplicativo dê suporte a cenários de acesso aleatório e streaming.<br/><br/>Você deseja ser capaz de acessar dados de aplicativos de qualquer lugar.<br/><br/>Você deseja criar um data Lake corporativo no Azure e executar Big Data Analytics. |
| **Discos do Azure** | Fornece bibliotecas de cliente e uma [interface REST](/rest/api/compute/manageddisks/disks/disks-rest-api) que permite que os dados sejam armazenados de forma persistente e acessados de um disco rígido virtual anexado. | Você deseja migrar e deslocar os aplicativos que usam APIs do sistema de arquivos nativo para ler e gravar dados em discos persistentes.<br/><br/>Você deseja armazenar dados que não precisam ser acessados de fora da máquina virtual à qual o disco está anexado. |


## <a name="next-steps"></a>Passos seguintes

Ao tomar decisões sobre como seus dados são armazenados e acessados, você também deve considerar os custos envolvidos. Para obter mais informações, consulte [preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).
  
Alguns recursos SMB não são aplicáveis à nuvem. Para obter mais informações, consulte [recursos sem suporte no serviço de arquivos do Azure](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).
 
Para obter mais informações sobre BLOBs do Azure, consulte nosso artigo [o que é o armazenamento de BLOBs do Azure?](../blobs/storage-blobs-overview.md).

Para obter mais informações sobre Armazenamento em Disco, consulte nossa [introdução aos Managed disks](../../virtual-machines/windows/managed-disks-overview.md).

Para obter mais informações sobre os arquivos do Azure, consulte nosso artigo [sobre como planejar uma implantação de arquivos do Azure](../files/storage-files-planning.md).