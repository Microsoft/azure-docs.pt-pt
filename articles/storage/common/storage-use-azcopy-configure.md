---
title: Configure, otimize e problemas AzCopy com armazenamento Azure [ Microsoft Docs
description: Configure, otimize e desame o AzCopy.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/28/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: d2cb40d7510e46539db46bdb61ec2d64c0fd1ec7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526500"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configure, otimize e problemas AzCopy

O AzCopy é um utilitário de linha de comando que pode utilizar para copiar bolhas ou ficheiros de ou para uma conta de armazenamento. Este artigo ajuda-o a executar tarefas de configuração avançadas e ajuda-o a resolver problemas que podem surgir à medida que utiliza o AzCopy.

> [!NOTE]
> Se procura conteúdo para o ajudar a começar com a AzCopy, consulte qualquer um dos seguintes artigos:
> - [Introdução ao AzCopy](storage-use-azcopy-v10.md)
> - [Transferir dados com armazenamento azCopy e blob](storage-use-azcopy-blobs.md)
> - [Transferir dados com a AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)
> - [Transferir dados com baldes AzCopy e Amazon S3](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Configurar definições de proxy

Para configurar as definições de procuração para AzCopy, detete a variável ambiente. `https_proxy` Se executar o AzCopy no Windows, o AzCopy deteta automaticamente as definições de procuração, pelo que não tem de utilizar esta definição no Windows. Se optar por utilizar esta definição no Windows, irá sobrepor-se à deteção automática.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | Numa utilização rápida de comando:`set https_proxy=<proxy IP>:<proxy port>`<br> Na utilização da PowerShell:`$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

Atualmente, a AzCopy não suporta proxies que requerem autenticação com NTLM ou Kerberos.

## <a name="optimize-performance"></a>Otimizar o desempenho

Você pode comparar o desempenho, e depois usar comandos e variáveis ambientais para encontrar uma troca ideal entre desempenho e consumo de recursos.

Esta secção ajuda-o a executar estas tarefas de otimização:

> [!div class="checklist"]
> * Executar testes de referência
> * Otimizar a entrada
> * Otimizar o uso da memória 
> * Otimizar a sincronização de ficheiros

### <a name="run-benchmark-tests"></a>Executar testes de referência

Pode realizar um teste de benchmark de desempenho em recipientes de blob específicos para ver estatísticas gerais de desempenho e estrangulamentos de desempenho de identidade. 

> [!NOTE]
> No lançamento atual, esta funcionalidade está disponível apenas para recipientes de armazenamento Blob.

Utilize o seguinte comando para realizar um teste de referência de desempenho.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy bench 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Exemplo** | `azcopy bench 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> Este exemplo encerra argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as conchas de comando, exceto na Shell de Comando do Windows (cmd.exe). Se estiver a utilizar uma Shell de Comando windows (cmd.exe), encerre argumentos de caminho com citações duplas (") em vez de citações únicas (').

Este comando executa uma referência de desempenho, enviando dados de teste para um destino especificado. Os dados do teste são gerados na memória, enviados para o destino, e depois eliminados do destino após a conclusão do teste. Pode especificar quantos ficheiros gerar e qual o tamanho que gostaria que fossem utilizando parâmetros de comando opcionais.

Para obter documentos de referência detalhados, consulte [o banco de azcopy](storage-ref-azcopy-bench.md).

Para ver a orientação detalhada `azcopy bench -h` da ajuda para este comando, digite e, em seguida, prima a tecla ENTER.

### <a name="optimize-throughput"></a>Otimizar a entrada

Pode utilizar `cap-mbps` a bandeira nos seus comandos para colocar um teto na taxa de dados de entrada. Por exemplo, o comando seguinte retoma um `10` trabalho e caps de entrada para megabits (MB) por segundo. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

A entrada pode diminuir ao transferir pequenos ficheiros. Pode aumentar a entrada definindo `AZCOPY_CONCURRENCY_VALUE` a variável ambiental. Esta variável especifica o número de pedidos simultâneos que podem ocorrer.  

Se o seu computador tiver menos de 5 CPUs, `32`então o valor desta variável está definido para . Caso contrário, o valor predefinido é igual a 16 multiplicados pelo número de CPUs. O valor padrão máximo `3000`desta variável é, mas pode definir manualmente este valor mais alto ou inferior. 

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Utilize `azcopy env` o para verificar o valor atual desta variável. Se o valor estiver em branco, pode ler qual o valor que está a ser utilizado olhando para o início de qualquer ficheiro de registo AzCopy. O valor selecionado, e a razão pela qual foi selecionado, são relatados lá.

Antes de definir esta variável, recomendamos que faça um teste de referência. O processo de teste de referência reportará o valor da moeda recomendada. Em alternativa, se as condições de rede e `AUTO` as cargas variam, defina esta variável na palavra em vez de num número específico. Isso fará com que a AzCopy execute sempre o mesmo processo de afinação automática que utiliza em testes de referência.

### <a name="optimize-memory-use"></a>Otimizar o uso da memória

Detete a variável ambiental `AZCOPY_BUFFER_GB` para especificar a quantidade máxima da memória do seu sistema que pretende que o AzCopy utilize ao descarregar e carregar ficheiros.
Expresse este valor em gigabytes (GB).

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **MacOS** | `export AZCOPY_BUFFER_GB=<value>` |

### <a name="optimize-file-synchronization"></a>Otimizar a sincronização de ficheiros

O comando [de sincronização](storage-ref-azcopy-sync.md) identifica todos os ficheiros no destino e, em seguida, compara os nomes dos ficheiros e os últimos selos de tempo modificados antes de iniciar a operação de sincronização. Se tiver um grande número de ficheiros, então pode melhorar o desempenho eliminando este processamento frontal. 

Para o conseguir, utilize o comando de cópia `--overwrite` de `ifSourceNewer` [azcópia](storage-ref-azcopy-copy.md) e coloque a bandeira em . O AzCopy irá comparar ficheiros à medida que são copiados sem realizar quaisquer digitalizações e comparações frontais. Isto fornece uma vantagem de desempenho nos casos em que há um grande número de ficheiros para comparar.

O comando de cópia azcopy não apaga ficheiros do destino, por isso, se pretender eliminar ficheiros no destino quando `--delete-destination` já não existam `true` na `prompt`fonte, utilize o comando de [sincronização azcopy](storage-ref-azcopy-sync.md) com a bandeira definida para um valor de ou . [azcopy copy](storage-ref-azcopy-copy.md) 

## <a name="troubleshoot-issues"></a>Resolver problemas

A ZCopy cria ficheiros de registo e plano para cada trabalho. Pode usar os registos para investigar e resolver potenciais problemas. 

Os registos conterão o`UPLOADFAILED`estado `COPYFAILED`de `DOWNLOADFAILED`falha , e o caminho completo, e a razão da falha.

Por predefinição, os ficheiros `%USERPROFILE%\.azcopy` de log e `$HOME$\.azcopy` de plano estão localizados no diretório no Windows ou no diretório no Mac e no Linux, mas pode alterar esse local se quiser.

O erro relevante não é necessariamente o primeiro erro que aparece no ficheiro. Para erros como erros de rede, intervalos de tempo e erros de Server Busy, o AzCopy irá voltar a tentar até 20 vezes e normalmente o processo de retry é bem sucedido.  O primeiro erro que vêpode ser algo inofensivo que foi julgado com sucesso.  Então, em vez de olhar para o primeiro erro `UPLOADFAILED`no `COPYFAILED`ficheiro, procure os erros que estão perto , ou `DOWNLOADFAILED`. 

> [!IMPORTANT]
> Ao submeter um pedido ao Microsoft Support (ou resolver problemas com o problema envolvendo terceiros), partilhe a versão redigida do comando que pretende executar. Isto garante que a SAS não é partilhada acidentalmente com ninguém. Pode encontrar a versão redigida no início do ficheiro de registo.

### <a name="review-the-logs-for-errors"></a>Reveja os registos de erros

O seguinte comando obterá `UPLOADFAILED` todos `04dc9ca9-158f-7945-5933-564021086c79` os erros com o estado do registo:

**Janelas (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Ver e retomar os empregos

Cada operação de transferência criará um trabalho AzCopy. Utilize o seguinte comando para ver a história dos empregos:

```
azcopy jobs list
```

Para visualizar as estatísticas de emprego, utilize o seguinte comando:

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
> Encerre argumentos de caminho, como o símbolo SAS com citações únicas ('). Utilize aspas únicas em todas as conchas de comando, exceto na Shell de Comando do Windows (cmd.exe). Se estiver a utilizar uma Shell de Comando windows (cmd.exe), encerre argumentos de caminho com citações duplas (") em vez de citações únicas (').

Quando retoma um trabalho, a AzCopy olha para o ficheiro do plano de trabalho. O ficheiro do plano lista todos os ficheiros que foram identificados para processamento quando o trabalho foi criado pela primeira vez. Quando retomar um trabalho, a AzCopy tentará transferir todos os ficheiros listados no ficheiro do plano que ainda não foram transferidos.

## <a name="change-the-location-of-the-plan-and-log-files"></a>Alterar a localização do plano e registar ficheiros

Por predefinição, os ficheiros `%USERPROFILE%\.azcopy` de planoe de `$HOME$\.azcopy` registo estão localizados no diretório no Windows ou no diretório em Mac e Linux. Pode mudar este local.

### <a name="change-the-location-of-plan-files"></a>Alterar a localização dos ficheiros do plano

Use qualquer um destes comandos.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Utilize `azcopy env` o para verificar o valor atual desta variável. Se o valor estiver em branco, os ficheiros de plano são escritos para a localização predefinida.

### <a name="change-the-location-of-log-files"></a>Alterar a localização dos ficheiros de registo

Use qualquer um destes comandos.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Utilize `azcopy env` o para verificar o valor atual desta variável. Se o valor estiver em branco, os registos são escritos para a localização predefinida.

## <a name="change-the-default-log-level"></a>Alterar o nível de registo padrão

Por predefinição, o nível `INFO`de registo AzCopy está definido para . Se quiser reduzir a verbosidade do registo para economizar espaço no ``--log-level`` disco, sobrepor esta definição utilizando a opção. 

Os níveis de `NONE`registo `DEBUG` `INFO`disponíveis `WARNING` `ERROR`são: , , , e `PANIC` `FATAL`.

## <a name="remove-plan-and-log-files"></a>Remover ficheiros de plano e log

Se pretender remover todos os ficheiros de planos e registos da sua máquina local para guardar espaço em disco, utilize o `azcopy jobs clean` comando.

Para remover o plano e registar ficheiros `azcopy jobs rm <job-id>`associados apenas a um trabalho, utilize . Substitua `<job-id>` o espaço reservado neste exemplo pela identificação do trabalho.


