---
title: Revisão do estatuto de emprego de importação/exportação do Azure Import /Export - v1 ! Microsoft Docs
description: Saiba como utilizar os ficheiros de registo criados pela empresa de importação ou exportação para ver o estado do trabalho.
author: twooley
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/26/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: c9093af25e621b3ab9a60d9894f0a875340fda45
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88525674"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Rever o estado do trabalho de importação/exportação do Azure com ficheiros de registo de cópias
Quando o serviço Microsoft Azure Import/Export processa as unidades associadas a uma tarefa de importação ou exportação, escreve ficheiros de registo de cópia na conta de armazenamento que usou para importar ou exportar bolhas. O ficheiro de registo contém um estado detalhado sobre cada ficheiro que foi importado ou exportado. O serviço devolve o URL de cada ficheiro de registo de cópia quando consulta o estado de um trabalho concluído. Para mais informações, consulte [Get Job](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get).  

## <a name="example-urls"></a>URLs de exemplo

Seguem-se urls exemplo para ficheiros de registo de cópias para uma tarefa de importação com duas unidades:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 Consulte o formato de ficheiro de registo de [serviço de importação/exportação](../storage-import-export-file-format-log.md) para o formato de registos de cópias e a lista completa de códigos de estado.  

## <a name="next-steps"></a>Passos seguintes

 * [Criação da ferramenta de importação/exportação Azure](storage-import-export-tool-setup-v1.md)   
 * [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Reparação de uma tarefa de importação](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Reparação de uma tarefa de exportação](../storage-import-export-tool-repairing-an-export-job-v1.md)
