---
title: Revisão do estatuto de emprego de importação/exportação do Azure Import /Export - v1 ! Microsoft Docs
description: Saiba como utilizar os ficheiros de registo criados quando a produção ou a produção de trabalho foi executada para ver o estado do trabalho de importação/exportação.
author: twooley
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/26/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 5ab0dd7f0e0ed90c205c37ddbb7ea3a9fca74ae9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85512266"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Rever o estado do trabalho de importação/exportação do Azure com ficheiros de registo de cópias
Quando os processos de serviço de importação/exportação do Microsoft Azure conduzem associados a uma empresa de importação ou exportação, escreve ficheiros de registo de cópia na conta de armazenamento para ou para onde está a importar ou exportar bolhas. O ficheiro de registo contém um estado detalhado sobre cada ficheiro que foi importado ou exportado. O URL de cada ficheiro de registo de cópia é devolvido quando consulta o estado de um trabalho concluído; ver [Get Job](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get) para mais informações.  

## <a name="example-urls"></a>URLs de exemplo

Seguem-se urls exemplo para ficheiros de registo de cópias para uma tarefa de importação com duas unidades:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 Consulte o formato de ficheiro de registo de [serviço de importação/exportação](../storage-import-export-file-format-log.md) para o formato de registos de cópias e a lista completa de códigos de estado.  

## <a name="next-steps"></a>Próximos passos

 * [Criação da ferramenta de importação/exportação Azure](storage-import-export-tool-setup-v1.md)   
 * [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Reparação de uma tarefa de importação](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Reparação de uma tarefa de exportação](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Resolver problemas da Ferramenta de Importação /Exportação do Azure (Troubleshooting the Azure Import/Export Tool)](storage-import-export-tool-troubleshooting-v1.md)
