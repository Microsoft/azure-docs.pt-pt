---
title: Configurar, otimizar e resolver problemas relacionados com o AzCopy com o armazenamento do Azure | Documentos da Microsoft
description: Configurar, otimizar e solucionar problemas de AzCopy.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 1a67846889b43d582a7a7d477a33f0e2168fd760
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147856"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurar, otimizar e resolver problemas relacionados com o AzCopy

O AzCopy é um utilitário de linha de comandos que pode utilizar para copiar blobs ou ficheiros de ou para uma conta de armazenamento. Este artigo ajuda-o para executar tarefas de configuração avançada e ajuda-o a resolver problemas que podem surgir como utilizar o AzCopy.

> [!NOTE]
> Se estiver à procura de conteúdos que ajudem a se familiarizar com o AzCopy, veja qualquer um dos seguintes artigos:
> - [Introdução ao AzCopy](storage-use-azcopy-v10.md)
> - [Transferir dados com AzCopy e armazenamento de BLOBs](storage-use-azcopy-blobs.md)
> - [Transferir dados com AzCopy e o ficheiro de armazenamento](storage-use-azcopy-files.md)
> - [Transferir dados com AzCopy e o Amazon S3 registos](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Configurar definições de proxy

Para configurar as definições de proxy para o AzCopy, defina o `https_proxy` variável de ambiente.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | Numa linha de comandos, utilize: `set https_proxy=<proxy IP>:<proxy port>`<br> Na utilização do PowerShell: `$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

Atualmente, o AzCopy não suporta proxies exigem a autenticação com NTLM ou Kerberos.

## <a name="optimize-throughput"></a>Otimizar o débito

Definir o `AZCOPY_CONCURRENCY_VALUE` variável de ambiente para configurar o número de pedidos simultâneos e para controlar o consumo de recursos e desempenho de taxa de transferência. Se o seu computador tiver menos de 5 CPUs, então o valor da variável é definido como `32`. Caso contrário, o valor predefinido é igual a 16 multiplicada pelo número de CPUs. O valor máximo predefinido desta variável é `300`, mas pode definir manualmente este valor superior ou inferior.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Utilize o `azcopy env` para verificar o valor atual da variável.  Se o valor está em branco, o `AZCOPY_CONCURRENCY_VALUE` variável é definida como o valor predefinido de `300`.

## <a name="change-the-location-of-the-log-files"></a>Alterar a localização dos ficheiros de registo

Por predefinição, os ficheiros de registo estão localizados no `%USERPROFILE\\.azcopy` diretório no Windows ou no `$HOME\\.azcopy` diretório em Mac e Linux. Pode alterar esta localização se for necessário, utilizando estes comandos.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Utilize o `azcopy env` para verificar o valor atual da variável. Se o valor está em branco, em seguida, os registos são escritos para a localização predefinida.

## <a name="change-the-default-log-level"></a>Alterar o nível de registo predefinido

Por predefinição, o nível de registo do AzCopy estiver definido como `INFO`. Se gostaria de reduzir a verbosidade de registo para poupar espaço em disco, substituir esta definição utilizando o ``--log-level`` opção. 

Os níveis de registo disponíveis são: `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC`, e `FATAL`.

## <a name="troubleshoot-issues"></a>Resolver problemas

O AzCopy cria ficheiros de registo e o plano para cada tarefa. Pode utilizar os registos para investigar e resolver quaisquer problemas potenciais. 

Os registos irão conter o estado de falha (`UPLOADFAILED`, `COPYFAILED`, e `DOWNLOADFAILED`), o caminho completo e o motivo da falha.

Por predefinição, os ficheiros de registo e de plano estão localizados no `%USERPROFILE\\.azcopy` em Windows ou `$HOME\\.azcopy` diretório em Mac e Linux.

> [!IMPORTANT]
> Ao submeter um pedido para Support da Microsoft (ou resolver o problema que envolvem qualquer terceiro), partilhar a versão eliminada do comando que deseja executar. Isto garante que a SAS acidentalmente não serem partilhada com qualquer pessoa. Pode encontrar a versão eliminada no início do ficheiro de registo.

### <a name="review-the-logs-for-errors"></a>Rever os registos de erros

O seguinte comando irá obter todos os erros com `UPLOADFAILED` estado a partir do `04dc9ca9-158f-7945-5933-564021086c79` registo:

**Windows**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Ver e retomar tarefas

Cada operação de transferência irá criar uma tarefa de AzCopy. Utilize o seguinte comando para ver o histórico de tarefas:

```
azcopy jobs list
```

Para ver as estatísticas de tarefa, utilize o seguinte comando:

```
azcopy jobs show <job-id>
```

Para filtrar as transferências por Estado, utilize o seguinte comando:

```
azcopy jobs show <job-id> --with-status=Failed
```

Utilize o seguinte comando para retomar uma tarefa falha/cancelado. Este comando utiliza o seu identificador juntamente com o token SAS, pois não é persistente por motivos de segurança:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

Quando retomar uma tarefa, o AzCopy analisa o ficheiro de plano de tarefa. O ficheiro de plano apresenta uma lista de todos os ficheiros que foram identificados para o processamento quando a tarefa foi criada pela primeira vez. Quando retomar uma tarefa, irá tentar AzCopy transferir todos os ficheiros que estão listados no arquivo do plano que já não foram transferido.