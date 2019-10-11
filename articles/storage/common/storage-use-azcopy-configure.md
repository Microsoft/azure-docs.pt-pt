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
ms.openlocfilehash: 42d2dae148b83687ff06d4ed321a881bcb9e7ae0
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273935"
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

Para definir as configurações de proxy para AzCopy, defina a variável de ambiente `https_proxy`. Se você executar o AzCopy no Windows, o AzCopy detectará automaticamente as configurações de proxy, de modo que você não precisa usar essa configuração no Windows. Se você optar por usar essa configuração no Windows, ela substituirá a detecção automática.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | Em um prompt de comando, use: `set https_proxy=<proxy IP>:<proxy port>`<br> No PowerShell, use: `$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

Atualmente, o AzCopy não dá suporte a proxies que exigem autenticação com NTLM ou Kerberos.

## <a name="optimize-performance"></a>Otimizar o desempenho

Você pode obter o desempenho do benchmark e, em seguida, usar comandos e variáveis de ambiente para encontrar uma compensação ideal entre o consumo de recursos e o desempenho.

### <a name="run-benchmark-tests"></a>Executar testes de benchmark

Você pode executar um teste de benchmark de desempenho em contêineres de blob específicos para exibir estatísticas gerais de desempenho e para identificar afunilamentos de desempenho. 

> [!NOTE]
> Na versão atual, esse recurso está disponível apenas para contêineres de armazenamento de BLOBs.

Use o comando a seguir para executar um teste de benchmark de desempenho.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy bench 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Exemplo** | `azcopy bench 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/'` |

Esse comando executa um parâmetro de comparação de desempenho carregando dados de teste para um destino especificado. Os dados de teste são gerados na memória, carregados no destino e, em seguida, excluídos do destino após a conclusão do teste. Você pode especificar quantos arquivos serão gerados e qual tamanho você gostaria que eles estivéssemos usando parâmetros de comando opcionais.

Para exibir as diretrizes de ajuda detalhadas para este comando, digite `azcopy bench -h` e pressione a tecla ENTER.

### <a name="optimize-throughput"></a>Otimizar taxa de transferência

Você pode usar o sinalizador `cap-mbps` para inserir um teto na taxa de dados de produtividade. Por exemplo, o comando a seguir Caps taxa de transferência para `10` megabits (MB) por segundo.

```azcopy
azcopy cap-mbps 10
```

A taxa de transferência pode diminuir ao transferir arquivos pequenos. Você pode aumentar a taxa de transferência definindo a variável de ambiente `AZCOPY_CONCURRENCY_VALUE`. Essa variável especifica o número de solicitações simultâneas que podem ocorrer.  

Se o computador tiver menos de 5 CPUs, o valor dessa variável será definido como `32`. Caso contrário, o valor padrão é igual a 16 multiplicado pelo número de CPUs. O valor padrão máximo dessa variável é `3000`, mas você pode definir manualmente esse valor como maior ou menor. 

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Use o `azcopy env` para verificar o valor atual dessa variável. Se o valor estiver em branco, você poderá ler qual valor está sendo usado observando o início de qualquer arquivo de log do AzCopy. O valor selecionado e o motivo pelo qual ele foi selecionado são relatados lá.

Antes de definir essa variável, recomendamos que você execute um teste de parâmetro de comparação. O processo de teste de benchmark relatará o valor de simultaneidade recomendado. Como alternativa, se as suas condições de rede e suas cargas variam, defina essa variável para a palavra `AUTO` em vez de para um número específico. Isso fará com que o AzCopy sempre execute o mesmo processo de ajuste automático que ele usa nos testes de parâmetro de comparação.

### <a name="optimize-memory-use"></a>Otimizar o uso de memória

Defina a variável de ambiente `AZCOPY_BUFFER_GB` para especificar a quantidade máxima de memória do sistema que você deseja que o AzCopy use ao baixar e carregar arquivos.
Expresse esse valor em gigabytes (GB).

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **MacOS** | `export AZCOPY_BUFFER_GB=<value>` |

## <a name="troubleshoot-issues"></a>Resolver problemas

O AzCopy cria arquivos de log e de plano para cada trabalho. Você pode usar os logs para investigar e solucionar problemas em potencial. 

Os logs conterão o status de falha (`UPLOADFAILED`, `COPYFAILED` e `DOWNLOADFAILED`), o caminho completo e o motivo da falha.

Por padrão, os arquivos de log e de plano estão localizados no diretório `%USERPROFILE$\.azcopy` no Windows ou no diretório `$HOME$\.azcopy` no Mac e no Linux, mas você pode alterar esse local, se desejar.

> [!IMPORTANT]
> Ao enviar uma solicitação para Suporte da Microsoft (ou solucionar o problema que envolve terceiros), compartilhe a versão redação do comando que você deseja executar. Isso garante que a SAS não seja compartilhada acidentalmente com ninguém. Você pode encontrar a versão redação no início do arquivo de log.

### <a name="review-the-logs-for-errors"></a>Examinar os logs em busca de erros

O comando a seguir obterá todos os erros com o status `UPLOADFAILED` do log `04dc9ca9-158f-7945-5933-564021086c79`:

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

## <a name="change-the-location-of-the-plan-and-log-files"></a>Alterar o local do plano e dos arquivos de log

Por padrão, os arquivos de plano e de log estão localizados no diretório `%USERPROFILE$\.azcopy` no Windows ou no diretório `$HOME$\.azcopy` no Mac e no Linux. Você pode alterar esse local.

### <a name="change-the-location-of-plan-files"></a>Alterar o local dos arquivos de plano

Use qualquer um desses comandos.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Use o `azcopy env` para verificar o valor atual dessa variável. Se o valor estiver em branco, os arquivos de plano serão gravados no local padrão.

### <a name="change-the-location-of-log-files"></a>Alterar o local dos arquivos de log

Use qualquer um desses comandos.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Use o `azcopy env` para verificar o valor atual dessa variável. Se o valor estiver em branco, os logs serão gravados no local padrão.

## <a name="change-the-default-log-level"></a>Alterar o nível de log padrão

Por padrão, o nível de log AzCopy é definido como `INFO`. Se você quiser reduzir o detalhamento de log para economizar espaço em disco, substitua essa configuração usando a opção ``--log-level``. 

Os níveis de log disponíveis são: `NONE`, `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC` e `FATAL`.

## <a name="remove-plan-and-log-files"></a>Remover arquivos de plano e de log

Se você quiser remover todos os arquivos de plano e de log do computador local para economizar espaço em disco, use o comando `azcopy jobs clean`.

Para remover o plano e os arquivos de log associados a apenas um trabalho, use `azcopy jobs rm <job-id>`. Substitua o espaço reservado `<job-id>` neste exemplo pela ID do trabalho.


