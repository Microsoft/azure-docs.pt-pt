---
title: Revisão do Estado de tarefa de importar/exportar do Azure - v1 | Documentos da Microsoft
description: Saiba como utilizar os ficheiros de registo criados quando a tarefa de importação ou exportação foi executada para ver o estado da tarefa de importação/exportação.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/26/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 306e3ccf19ba8db2de01e4b20a52707215a4a040
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320711"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Revisão do Estado da tarefa de importar/exportar do Azure com ficheiros de registo de cópia
Quando o serviço de importação/exportação do Microsoft Azure processa unidades associadas a uma tarefa de importação ou exportação, escreve copiar ficheiros de registo para a conta de armazenamento para ou a partir do qual está a importar ou exportar blobs. O ficheiro de registo contém o estado detalhado sobre cada ficheiro que foi importado ou exportado. O URL para cada ficheiro de registo de cópia é devolvido ao consultar o estado de uma tarefa concluída; ver [Get Job de](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get) para obter mais informações.  

## <a name="example-urls"></a>URLs de exemplo

Seguem-se os URLs de exemplo para ficheiros de registo de cópia de uma tarefa de importação com duas unidades:  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 Ver [serviço importar/exportar formato de ficheiro de registo](../storage-import-export-file-format-log.md) para o formato dos registos de cópia e a lista completa dos códigos de estado.  
  
## <a name="next-steps"></a>Passos Seguintes
 
 * [Configurar a ferramenta de importação/exportação do Azure](storage-import-export-tool-setup-v1.md)   
 * [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Reparação de uma tarefa de importação](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Reparação de uma tarefa de exportação](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Resolver problemas da Ferramenta de Importação /Exportação do Azure (Troubleshooting the Azure Import/Export Tool)](storage-import-export-tool-troubleshooting-v1.md)
