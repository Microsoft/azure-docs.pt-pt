---
title: Encontre erros & retomar os trabalhos com registos em AzCopy (Azure Storage) | Microsoft Docs
description: Aprenda a usar registos para diagnosticar erros e retomar os trabalhos que são pausados usando ficheiros de plano.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: d3b956803e9a796c49288f90873e88c3b69f1c7b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502900"
---
# <a name="find-errors-and-resume-jobs-by-using-log-and-plan-files-in-azcopy"></a>Encontre erros e retome os trabalhos utilizando ficheiros de registo e plano no AzCopy

O AzCopy é um utilitário da linha de comando que pode utilizar para copiar blobs ou ficheiros de/para uma conta de armazenamento. Este artigo ajuda-o a usar registos para diagnosticar erros e, em seguida, usar ficheiros de plano para retomar os trabalhos. Este artigo também mostra como configurar registos e planear ficheiros alterando o seu nível de verbosidade e a localização padrão onde estão armazenados.

> [!NOTE]
> Se procura conteúdo para ajudá-lo a começar com a AzCopy, consulte [Get start with AzCopy](storage-use-azcopy-v10.md).

## <a name="log-and-plan-files"></a>Registar e planear ficheiros

A AzCopy cria *ficheiros de registo* e *plano* para cada trabalho. Pode utilizar estes registos para investigar e resolver problemas potenciais. 

Os registos contêm o estado de falha `UPLOADFAILED` `COPYFAILED` (, e `DOWNLOADFAILED` ) o caminho completo, e a razão da falha.

Por predefinição, os ficheiros de registo e plano estão localizados `%USERPROFILE%\.azcopy` no diretório do Windows ou `$HOME$\.azcopy` no diretório no Mac e linux, mas pode alterar essa localização. 

O erro relevante não é necessariamente o primeiro erro que aparece no ficheiro. Para erros como erros de rede, intervalos de tempo e erros do Server Busy, o AzCopy voltará a tentar até 20 vezes e normalmente o processo de relemisão terá sucesso.  O primeiro erro que vê pode ser algo inofensivo que foi novamente julgado com sucesso.  Assim, em vez de olhar para o primeiro erro no ficheiro, procure os erros que estão `UPLOADFAILED` `COPYFAILED` próximos , ou `DOWNLOADFAILED` . 

> [!IMPORTANT]
> Ao submeter um pedido ao Microsoft Support (ou resolver problemas do problema envolvendo terceiros), partilhe a versão redigida do comando que pretende executar. Isto garante que o SAS não é partilhado acidentalmente com ninguém. Pode encontrar a versão redigida no início do ficheiro de registo.

## <a name="review-the-logs-for-errors"></a>Reveja os registos de erros

O seguinte comando obterá todos os erros com `UPLOADFAILED` o estado do `04dc9ca9-158f-7945-5933-564021086c79` registo:

**Janelas (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

## <a name="view-and-resume-jobs"></a>Ver e retomar empregos

Cada operação de transferência criará um trabalho AzCopy. Use o seguinte comando para ver a história dos empregos:

```
azcopy jobs list
```

Para ver as estatísticas de trabalho, utilize o seguinte comando:

```
azcopy jobs show <job-id>
```

Para filtrar as transferências por estado, utilize o seguinte comando:

```
azcopy jobs show <job-id> --with-status=Failed
```

Utilize o seguinte comando para retomar um trabalho falhado/cancelado. Este comando usa o seu identificador juntamente com o token SAS, uma vez que não é persistente por razões de segurança:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> Incluir argumentos de caminho, como o símbolo SAS com cotações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

Quando retomas um trabalho, a AzCopy olha para o ficheiro do plano de trabalho. O ficheiro do plano lista todos os ficheiros que foram identificados para processamento quando o trabalho foi criado pela primeira vez. Quando retomar um trabalho, a AzCopy tentará transferir todos os ficheiros que estão listados no ficheiro do plano que ainda não foram transferidos.

## <a name="change-the-location-of-plan-files"></a>Alterar a localização dos ficheiros do plano

Use qualquer um destes comandos.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | PowerShell:`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> Numa utilização imediata de comando:: `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **macOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Utilize o `azcopy env` para verificar o valor atual desta variável. Se o valor estiver em branco, os ficheiros do plano são escritos para a localização predefinida.

## <a name="change-the-location-of-log-files"></a>Alterar a localização dos ficheiros de registo

Use qualquer um destes comandos.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | PowerShell:`$env:AZCOPY_LOG_LOCATION="<value>"` <br> Numa utilização imediata de comando:: `set AZCOPY_LOG_LOCATION=<value>`|
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **macOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Utilize o `azcopy env` para verificar o valor atual desta variável. Se o valor estiver em branco, os registos são escritos para a localização predefinido.

## <a name="change-the-default-log-level"></a>Alterar o nível de registo predefinido

Por predefinição, o nível de registo AzCopy está definido para `INFO` . Se quiser reduzir a verbosidade do log para economizar espaço no disco, substitua esta definição utilizando a ``--log-level`` opção. 

Os níveis de registo disponíveis são: `NONE` , , , , , , e `DEBUG` `INFO` `WARNING` `ERROR` `PANIC` `FATAL` .

## <a name="remove-plan-and-log-files"></a>Remover plano e registar ficheiros

Se pretender remover todos os ficheiros de planos e registos da sua máquina local para economizar espaço no disco, utilize o `azcopy jobs clean` comando.

Para remover o plano e registar ficheiros associados a apenas um trabalho, utilize `azcopy jobs rm <job-id>` . Substitua o `<job-id>` espaço reservado neste exemplo pelo ID de trabalho do trabalho.

## <a name="see-also"></a>Ver também

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
