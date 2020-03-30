---
title: Revisão do estado do emprego na importação/exportação de Azure - v1 [ Microsoft Docs
description: Saiba como utilizar os ficheiros de registo criados quando o emprego de importação ou exportação foi executado para ver o estado do trabalho de importação/exportação.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/26/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 33e6ecca0eb4ca05b1f6bf53cdb3bd4ac8c8d8a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978441"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Revisão do estado de trabalho de importação/exportação do Azure com ficheiros de registo de cópias
Quando os processos de serviço de importação/exportação do Microsoft Azure conduzem associados a um trabalho de importação ou exportação, escreve ficheiros de registo de cópias na conta de armazenamento de ou a partir do qual está a importar ou exportar bolhas. O ficheiro de registo contém um estado detalhado sobre cada ficheiro que foi importado ou exportado. O URL de cada ficheiro de registo de cópias é devolvido quando se consulta o estado de um trabalho concluído; ver [Get Job](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get) para mais informações.  

## <a name="example-urls"></a>Exemplo URLs

Seguem-se os URLs de cópia para um trabalho de importação com duas unidades:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 Consulte o formato de ficheiro de registo do [serviço de importação/exportação](../storage-import-export-file-format-log.md) para o formato de registos de cópias e a lista completa de códigos de estado.  

## <a name="next-steps"></a>Passos seguintes

 * [Configuração da ferramenta de importação/exportação Azure](storage-import-export-tool-setup-v1.md)   
 * [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Reparação de uma tarefa de importação](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Reparação de uma tarefa de exportação](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Resolver problemas da Ferramenta de Importação /Exportação do Azure](storage-import-export-tool-troubleshooting-v1.md)
