---
title: Referência rápida para comandos de tarefa de importação de ferramenta de importação/exportação do Azure - v1 | Documentos da Microsoft
description: Referência de comandos de ferramenta importar/exportar do Azure para comandos da tarefa de importação usados com freqüência. Refere-se para a v1 da ferramenta importar/exportar.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 44ee23a510bc15d5cb52a338be1652180b922db9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478441"
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Referência rápida para comandos utilizados com frequência para tarefas de importação
Esta seção fornece que uma referência rápida para alguns comandos de usados com freqüência. Para utilização detalhada, consulte [preparar os discos rígidos para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md).  

## <a name="prepare-a-hard-drive-when-data-has-already-been-copied-to-the-hard-drive"></a>Preparar um disco rígido quando os dados já foram copiados para o disco rígido
 O seguinte comando prepara o disco rígido quando os dados já foi copiados para o mesmo, mas ainda não tem sido criptografados com BitLocker:  
  
```  
  WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /t:d /encrypt /srcdir:d:\movies\drama /dstdir:movies/drama/ /skipwrite
```    

## <a name="copy-a-single-directory-to-a-hard-drive"></a>Copiar um único diretório num disco rígido  
 O comando seguinte copia um diretório de origem única para um disco rígido que ainda não tenha sido criptografado com BitLocker:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
## <a name="copy-two-directories-to-a-hard-drive"></a>Copie os dois diretórios num disco rígido  
 Para copiar os dois diretórios de origem para uma unidade, utilize os seguintes comandos:  
  
 O primeiro comando Especifica o diretório de registo, a chave de conta de armazenamento, a letra de unidade de destino, `format/encrypt` requisitos e os parâmetros comuns:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
 O segundo comando Especifica o ficheiro de diário, um novo ID de sessão e as localizações de origem e de destino:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:music /srcdir:d:\Music /dstdir:entertainment/music/  
```  
  
## <a name="copy-a-large-file-to-a-hard-drive-in-a-second-copy-session"></a>Copiar um arquivo grande para um disco rígido numa segunda sessão de cópia  
 O seguinte comando copia um arquivo grande único para um disco rígido que foi preparado numa sessão de cópia anterior:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:dvd /srcfile:d:\dvd\favoritemovie.vhd /dstblob:dvd/favoritemovie.vhd  
```  
  
## <a name="next-steps"></a>Passos Seguintes

* [Fluxo de trabalho de amostra para preparar unidades de disco rígido para uma tarefa de importação](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
