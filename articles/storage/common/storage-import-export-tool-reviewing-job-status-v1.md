---
title: Examinando o status do trabalho de importação/exportação do Azure-v1 | Microsoft Docs
description: Saiba como usar os arquivos de log criados quando o trabalho de importação ou exportação foi executado para ver o status do trabalho de importação/exportação.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/26/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 075af43796d3ca3dfef4b48f8f98f20903af3308
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978973"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Examinando o status do trabalho de importação/exportação do Azure com arquivos de log de cópia
Quando o serviço de Importação/Exportação do Microsoft Azure processa unidades associadas a um trabalho de importação ou exportação, ele grava arquivos de log de cópia na conta de armazenamento para ou da qual você está importando ou exportando BLOBs. O arquivo de log contém o status detalhado sobre cada arquivo que foi importado ou exportado. A URL para cada arquivo de log de cópia é retornada quando você consulta o status de um trabalho concluído; consulte [obter trabalho](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get) para obter mais informações.  

## <a name="example-urls"></a>URLs de exemplo

Veja a seguir as URLs de exemplo para arquivos de log de cópia para um trabalho de importação com duas unidades:  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 Consulte [formato do arquivo de log do serviço de importação/exportação](../storage-import-export-file-format-log.md) para o formato dos logs de cópia e a lista completa de códigos de status.  
  
## <a name="next-steps"></a>Passos seguintes
 
 * [Configurando a ferramenta de importação/exportação do Azure](storage-import-export-tool-setup-v1.md)   
 * [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Reparação de uma tarefa de importação](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Reparação de uma tarefa de exportação](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Resolver problemas da Ferramenta de Importação /Exportação do Azure (Troubleshooting the Azure Import/Export Tool)](storage-import-export-tool-troubleshooting-v1.md)
