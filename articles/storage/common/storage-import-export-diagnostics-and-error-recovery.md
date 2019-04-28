---
title: Recuperação de diagnósticos e erros para tarefas de importação/exportação do Azure | Documentos da Microsoft
description: Saiba como ativar o registo de tarefas do serviço importar/exportar do Azure Microsoft verboso.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 306b94fbe23e0ae92dcd59f7a87b7bb58ef7c3b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478815"
---
# <a name="diagnostics-and-error-recovery-for-azure-importexport-jobs"></a>Recuperação de diagnósticos e erros para tarefas de importação/exportação do Azure
Para cada unidade de processamento, o serviço importar/exportar do Azure cria um registo de erros na conta de armazenamento associados. Também pode ativar o registo verboso, definindo a `LogLevel` propriedade para `Verbose` ao chamar os [colocar tarefa](/rest/api/storageimportexport/jobs) ou [propriedades da tarefa de atualização](/rest/api/storageimportexport/jobs) operações.

 Por predefinição, os registos são escritos para um contentor com o nome `waimportexport`. Pode especificar um nome diferente, definindo a `DiagnosticsPath` propriedade ao chamar o `Put Job` ou `Update Job Properties` operações. Os registos são armazenados como blobs de blocos com a seguinte convenção de nomenclatura: `waies/jobname_driveid_timestamp_logtype.xml`.

 Pode obter o URI dos registos de uma tarefa ao chamar o [Get Job de](/rest/api/storageimportexport/jobs) operação. O URI para o registo verboso que é devolvido na `VerboseLogUri` propriedade para cada unidade, enquanto o URI para o log de erro é retornado no `ErrorLogUri` propriedade.

Pode utilizar os dados de registo para identificar os seguintes problemas.

## <a name="drive-errors"></a>Erros de unidade

Os seguintes itens são classificados como erros de unidade:

-   Erros no acessar ou ler o ficheiro de manifesto

-   Chaves do BitLocker incorretas

-   Erros de leitura/escrita da unidade

## <a name="blob-errors"></a>Erros de blob

Os seguintes itens são classificados como erros de blob:

-   Blob incorreta ou em conflito ou nomes

-   Ficheiros em falta

-   Blob não foi encontrado

-   Ficheiros truncados (os arquivos no disco são menores do que o especificado no manifesto de)

-   Arquivo corrompido conteúdo (para tarefas de importação, detectada com um erro de correspondência de soma de verificação MD5)

-   Ficheiros de metadados e propriedades do blob danificada (detetados com um erro de correspondência de soma de verificação MD5)

-   Esquema incorreto para as propriedades do blob e/ou arquivos de metadados

Pode haver casos em que algumas partes de uma tarefa de importação ou exportação não forem concluídas com êxito, enquanto o trabalho inteiro ainda é concluída. Neste caso, pode carregar ou transferir as partes ausentes dos dados pela rede, ou pode criar uma nova tarefa para transferir os dados. Consulte a [referência da ferramenta importar/exportar do Azure](storage-import-export-tool-how-to-v1.md) para aprender a reparar os dados pela rede.

## <a name="next-steps"></a>Passos Seguintes

* [Utilizar a API de REST do serviço de importação/exportação](storage-import-export-using-the-rest-api.md)
