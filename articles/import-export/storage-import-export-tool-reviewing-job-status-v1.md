---
title: Revisão do estatuto de emprego de importação/exportação da Azure - v1 | Microsoft Docs
description: Saiba como utilizar os ficheiros de registo criados pela empresa de importação ou exportação para ver o estado do trabalho.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: f634ceb60ae78d4d825c73bd2e98da2fb951b374
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98706702"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Rever o estado do trabalho de importação/exportação do Azure com ficheiros de registo de cópias
Quando o serviço Microsoft Azure Import/Export processa as unidades associadas a uma tarefa de importação ou exportação, escreve ficheiros de registo de cópia na conta de armazenamento que usou para importar ou exportar bolhas. O ficheiro de registo contém um estado detalhado sobre cada ficheiro que foi importado ou exportado. O serviço devolve o URL de cada ficheiro de registo de cópia quando consulta o estado de um trabalho concluído. Para mais informações, consulte [Get Job](/rest/api/storageimportexport/Jobs/Get).  

## <a name="example-urls"></a>URLs de exemplo

Seguem-se urls exemplo para ficheiros de registo de cópias para uma tarefa de importação com duas unidades:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 Consulte o formato de ficheiro de registo de [serviço de importação/exportação](/previous-versions/azure/storage/common/storage-import-export-file-format-log) para o formato de registos de cópias e a lista completa de códigos de estado.  

## <a name="next-steps"></a>Passos seguintes

 * [Criação da ferramenta de importação/exportação Azure](storage-import-export-tool-setup-v1.md)   
 * [Preparar as unidades de disco rígido para uma tarefa de importação](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)   
 * [Reparação de uma tarefa de importação](./storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Reparação de uma tarefa de exportação](./storage-import-export-tool-repairing-an-export-job-v1.md)