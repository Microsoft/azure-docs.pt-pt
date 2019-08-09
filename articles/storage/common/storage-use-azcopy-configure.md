---
title: Configurar, otimizar e solucionar problemas do AzCopy com o armazenamento do Azure | Microsoft Docs
description: Configurar, otimizar e solucionar problemas do AzCopy.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 8a96c5b2d39967c8ee82f48e880bac9270a58c36
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68844797"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurar, otimizar e solucionar problemas do AzCopy

AzCopy é um utilitário de linha de comando que você pode usar para copiar BLOBs ou arquivos de ou para uma conta de armazenamento. Este artigo ajuda você a executar tarefas de configuração avançadas e ajuda a solucionar problemas que podem surgir ao usar o AzCopy.

> [!NOTE]
> Se você estiver procurando conteúdo para ajudá-lo a começar a usar o AzCopy, consulte qualquer um dos seguintes artigos:
> - [Introdução ao AzCopy](storage-use-azcopy-v10.md)
> - [Transferir dados com o armazenamento de BLOBs e AzCopy](storage-use-azcopy-blobs.md)
> - [Transferir dados com o AzCopy e o armazenamento de arquivos](storage-use-azcopy-files.md)
> - [Transferir dados com os buckets AzCopy e Amazon S3](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Definir configurações de proxy

Para definir as configurações de proxy para AzCopy, defina `https_proxy` a variável de ambiente. Se você executar o AzCopy no Windows, o AzCopy detectará automaticamente as configurações de proxy, de modo que você não precisa usar essa configuração no Windows. Se você optar por usar essa configuração no Windows, ela substituirá a detecção automática.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | Em um prompt de comando, use:`set https_proxy=<proxy IP>:<proxy port>`<br> No PowerShell, use:`$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

Atualmente, o AzCopy não dá suporte a proxies que exigem autenticação com NTLM ou Kerberos.

## <a name="optimize-throughput"></a>Otimizar taxa de transferência

Você pode usar o `cap-mbps` sinalizador para inserir um teto na taxa de dados de taxa de transferência. Por exemplo, o comando a seguir Caps taxa `10` de transferência para megabits (MB) por segundo.

```azcopy
azcopy cap-mbps 10
```

A taxa de transferência pode diminuir ao transferir arquivos pequenos. Você pode aumentar a taxa de transferência definindo a `AZCOPY_CONCURRENCY_VALUE` variável de ambiente. Essa variável especifica o número de solicitações simultâneas que podem ocorrer.  Se o computador tiver menos de 5 CPUs, o valor dessa variável será definido como `32`. Caso contrário, o valor padrão é igual a 16 multiplicado pelo número de CPUs. O valor padrão máximo dessa variável é `300`, mas você pode definir manualmente esse valor como maior ou menor.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Use o `azcopy env` para verificar o valor atual dessa variável.  Se o valor estiver em branco, a `AZCOPY_CONCURRENCY_VALUE` variável será definida como o valor padrão de `300`.

## <a name="change-the-location-of-the-log-files"></a>Alterar o local dos arquivos de log

Por padrão, os arquivos de log estão localizados `%USERPROFILE\\.azcopy` no diretório no Windows ou `$HOME\\.azcopy` no diretório no Mac e no Linux. Você pode alterar esse local se precisar usando esses comandos.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Use o `azcopy env` para verificar o valor atual dessa variável. Se o valor estiver em branco, os logs serão gravados no local padrão.

## <a name="change-the-default-log-level"></a>Alterar o nível de log padrão

Por padrão, o nível de log AzCopy é `INFO`definido como. Se você quiser reduzir o detalhamento de log para economizar espaço em disco, substitua essa configuração usando a ``--log-level`` opção. 

Os níveis de log disponíveis `DEBUG`são `INFO`: `WARNING`, `ERROR`, `PANIC`,, `FATAL`e.

## <a name="troubleshoot-issues"></a>Resolver problemas

O AzCopy cria arquivos de log e de plano para cada trabalho. Você pode usar os logs para investigar e solucionar problemas em potencial. 

Os logs conterão o status de falha (`UPLOADFAILED`, `COPYFAILED`, e `DOWNLOADFAILED`), o caminho completo e o motivo da falha.

Por padrão, os arquivos de log e de plano estão localizados `%USERPROFILE\\.azcopy` no diretório no Windows `$HOME\\.azcopy` ou no diretório no Mac e no Linux.

> [!IMPORTANT]
> Ao enviar uma solicitação para Suporte da Microsoft (ou solucionar o problema que envolve terceiros), compartilhe a versão redação do comando que você deseja executar. Isso garante que a SAS não seja compartilhada acidentalmente com ninguém. Você pode encontrar a versão redação no início do arquivo de log.

### <a name="review-the-logs-for-errors"></a>Examinar os logs em busca de erros

O comando a seguir receberá todos os `UPLOADFAILED` erros com o `04dc9ca9-158f-7945-5933-564021086c79` status do log:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Exibir e retomar trabalhos

Cada operação de transferência criará um trabalho do AzCopy. Use o seguinte comando para exibir o histórico de trabalhos:

```
azcopy jobs list
```

Para exibir as estatísticas de trabalho, use o seguinte comando:

```
azcopy jobs show <job-id>
```

Para filtrar as transferências por status, use o seguinte comando:

```
azcopy jobs show <job-id> --with-status=Failed
```

Use o comando a seguir para retomar um trabalho com falha/cancelado. Esse comando usa seu identificador junto com o token SAS, pois ele não é persistente por motivos de segurança:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

Quando você reinicia um trabalho, o AzCopy examina o arquivo de plano de trabalho. O arquivo de plano lista todos os arquivos que foram identificados para processamento quando o trabalho foi criado pela primeira vez. Quando você retomar um trabalho, o AzCopy tentará transferir todos os arquivos listados no arquivo de plano que ainda não foram transferidos.