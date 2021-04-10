---
title: Configurar, otimizar e resolver problemas AzCopy com Azure Storage | Microsoft Docs
description: Configurar, otimizar e resolver problemas AzCopy com Azure Storage. Altere a localização ou remova os ficheiros do plano e do registo. Altere o nível de registo predefinido.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: ad9e5665204dbd3f99f83af3578b1996814d6fa0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728848"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurar, otimizar e resolver problemas AzCopy

O AzCopy é um utilitário da linha de comando que pode utilizar para copiar blobs ou ficheiros de/para uma conta de armazenamento. Este artigo ajuda a executar tarefas de configuração avançadas e a resolver problemas que podem surgir à medida que utiliza o AzCopy.

> [!NOTE]
> Se procura conteúdo para ajudá-lo a começar com a AzCopy, consulte qualquer um dos seguintes artigos:
> - [Introdução ao AzCopy](storage-use-azcopy-v10.md)
> - [Transferir dados com AzCopy e armazenamento de bolhas](./storage-use-azcopy-v10.md#transfer-data)
> - [Transferir dados com o AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)
> - [Transfira dados com baldes AzCopy e Amazon S3](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Configurar definições de proxy

Para configurar as definições de procuração para AzCopy, defina a `HTTPS_PROXY` variável ambiental. Se executar o AzCopy no Windows, o AzCopy detetará automaticamente as definições de proxy, para que não tenha de utilizar esta definição no Windows. Se optar por utilizar esta definição no Windows, sobrepor-se-á à deteção automática.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | Numa utilização rápida de comando: `set HTTPS_PROXY=<proxy IP>:<proxy port>`<br> Na utilização do PowerShell: `$env:HTTPS_PROXY="<proxy IP>:<proxy port>"`|
| **Linux** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |
| **macOS** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |

Atualmente, a AzCopy não suporta proxies que requerem autenticação com NTLM ou Kerberos.

### <a name="bypassing-a-proxy"></a>Contornar um representante ###

Se estiver a executar a AzCopy no Windows e quiser dizer-lhe para não utilizar _nenhum_ representante (em vez de detetar automaticamente as definições) utilize estes comandos. Com estas definições, a AzCopy não procurará nem tentará utilizar qualquer procuração.

| Sistema operativo | Ambiente | Comandos  |
|--------|-----------|----------|
| **Windows** | Pedido de comando (CMD) | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

Noutros sistemas operativos, basta deixar a HTTPS_PROXY variável desaparada se não pretender utilizar nenhum representante.

## <a name="optimize-performance"></a>Otimizar o desempenho

Você pode comparar o desempenho e, em seguida, usar comandos e variáveis ambientais para encontrar uma troca ideal entre desempenho e consumo de recursos.

Esta secção ajuda-o a executar estas tarefas de otimização:

> [!div class="checklist"]
> * Executar testes de referência
> * Otimizar a produção
> * Otimizar o uso da memória 
> * Otimizar a sincronização de ficheiros

### <a name="run-benchmark-tests"></a>Executar testes de referência

Pode executar um teste de referência de desempenho em recipientes de bolhas específicas ou ações de ficheiros para visualizar estatísticas gerais de desempenho e para estrangulamentos de desempenho de identidade. Pode executar o teste carregando ou descarregando dados de teste gerados. 

Utilize o seguinte comando para executar um teste de referência de desempenho.

| Sintaxe / exemplo  |  Código |
|--------|-----------|
| **Syntax** | `azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Exemplo** | `azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> Este exemplo encerra argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

Este comando executa uma referência de desempenho enviando dados de teste para um destino especificado. Os dados do teste são gerados na memória, enviados para o destino e depois eliminados do destino após a conclusão do teste. Pode especificar quantos ficheiros gerar e qual o tamanho que pretende que sejam utilizando parâmetros de comando opcionais.

Se preferir realizar este teste descarregando dados, desfie o `mode` parâmetro para `download` . Para obter documentos de referência detalhados, consulte [a referência de azcopia](storage-ref-azcopy-bench.md). 

### <a name="optimize-throughput"></a>Otimizar a produção

Pode utilizar a `cap-mbps` bandeira nos seus comandos para colocar um teto na taxa de dados de produção. Por exemplo, o seguinte comando retoma um trabalho e caps de produção para `10` megabits (Mb) por segundo. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

O débito pode diminuir ao transferir ficheiros pequenos. Pode aumentar a produção definindo a `AZCOPY_CONCURRENCY_VALUE` variável ambiente. Esta variável especifica o número de pedidos simultâneos que podem ocorrer.  

Se o seu computador tiver menos de 5 CPUs, então o valor desta variável está definido para `32` . Caso contrário, o valor predefinido é igual a 16 vezes o número de CPUs. O valor predefinido máximo desta variável `3000` é, mas pode definir manualmente este valor mais ou menos. 

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **macOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Utilize o `azcopy env` para verificar o valor atual desta variável. Se o valor estiver em branco, então pode ler qual o valor que está a ser utilizado olhando para o início de qualquer ficheiro de registo AzCopy. O valor selecionado, e a razão pela qual foi selecionado, são comunicados lá.

Antes de definir esta variável, recomendamos que faça um teste de referência. O processo de teste de referência reportará o valor de concordância recomendado. Em alternativa, se as condições da sua rede e as cargas de trabalho variarem, desa um ponto de variação desta variável para a palavra `AUTO` em vez de um número específico. Isso fará com que a AzCopy execute sempre o mesmo processo de afinação automática que utiliza em testes de referência.

### <a name="optimize-memory-use"></a>Otimizar o uso da memória

Desfita a `AZCOPY_BUFFER_GB` variável ambiente para especificar a quantidade máxima da memória do sistema que pretende que o AzCopy utilize para tamponar ao descarregar e carregar ficheiros. Expresse este valor em gigabytes (GB).

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **macOS** | `export AZCOPY_BUFFER_GB=<value>` |

> [!NOTE]
> O rastreio de trabalho incorre sempre em sobrecargas adicionais no uso da memória. O valor varia em função do número de transferências num trabalho. Os tampão são o maior componente do uso da memória. Pode ajudar a controlar a sobrecarga utilizando `AZCOPY_BUFFER_GB` aproximadamente os seus requisitos, mas não existe nenhuma bandeira para limitar o uso geral da memória.

### <a name="optimize-file-synchronization"></a>Otimizar a sincronização de ficheiros

O comando [de sincronização](storage-ref-azcopy-sync.md) identifica todos os ficheiros no destino e, em seguida, compara os nomes dos ficheiros e os últimos cartões de tempo modificados antes do início da operação de sincronização. Se tiver um grande número de ficheiros, então pode melhorar o desempenho eliminando este processamento frontal. 

Para isso, use o comando [de cópia azcopia](storage-ref-azcopy-copy.md) e coloque a `--overwrite` bandeira para `ifSourceNewer` . O AzCopy comparará os ficheiros à medida que são copiados sem realizar quaisquer exames e comparações frontais. Isto fornece uma vantagem de desempenho em casos em que há um grande número de ficheiros para comparar.

O comando [de cópia azcopia](storage-ref-azcopy-copy.md) não apaga ficheiros do destino, por isso, se pretender apagar ficheiros no destino quando já não existirem na fonte, utilize o comando de sincronização de [azcopia](storage-ref-azcopy-sync.md) com a `--delete-destination` bandeira definida para um valor de ou `true` `prompt` . 

## <a name="troubleshoot-issues"></a>Resolver problemas

O AzCopy cria ficheiros de registo e de plano para cada trabalho. Pode utilizar os registos para investigar e resolver os possíveis problemas. 

Os registos contêm o estado de falha `UPLOADFAILED` `COPYFAILED` (, e `DOWNLOADFAILED` ) o caminho completo, e a razão da falha.

Por predefinição, os ficheiros de registo e plano estão localizados `%USERPROFILE%\.azcopy` no diretório do Windows ou `$HOME$\.azcopy` no diretório no Mac e linux, mas pode alterar essa localização se quiser.

O erro relevante não é necessariamente o primeiro erro que aparece no ficheiro. Para erros como erros de rede, intervalos de tempo e erros do Server Busy, o AzCopy voltará a tentar até 20 vezes e normalmente o processo de relemisão terá sucesso.  O primeiro erro que vê pode ser algo inofensivo que foi novamente julgado com sucesso.  Assim, em vez de olhar para o primeiro erro no ficheiro, procure os erros que estão `UPLOADFAILED` `COPYFAILED` próximos , ou `DOWNLOADFAILED` . 

> [!IMPORTANT]
> Ao submeter um pedido ao Microsoft Support (ou resolver problemas do problema envolvendo terceiros), partilhe a versão redigida do comando que pretende executar. Isto garante que o SAS não é partilhado acidentalmente com ninguém. Pode encontrar a versão redigida no início do ficheiro de registo.

### <a name="review-the-logs-for-errors"></a>Reveja os registos de erros

O seguinte comando obterá todos os erros com `UPLOADFAILED` o estado do `04dc9ca9-158f-7945-5933-564021086c79` registo:

**Janelas (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Ver e retomar empregos

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

## <a name="change-the-location-of-the-plan-and-log-files"></a>Alterar a localização do plano e registar ficheiros

Por predefinição, os ficheiros de plano e de registo estão localizados `%USERPROFILE%\.azcopy` no diretório do Windows ou no `$HOME/.azcopy` diretório de Mac e Linux. Pode mudar este local.

### <a name="change-the-location-of-plan-files"></a>Alterar a localização dos ficheiros do plano

Use qualquer um destes comandos.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | PowerShell:`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> Numa utilização imediata de comando:: `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **macOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Utilize o `azcopy env` para verificar o valor atual desta variável. Se o valor estiver em branco, os ficheiros do plano são escritos para a localização predefinida.

### <a name="change-the-location-of-log-files"></a>Alterar a localização dos ficheiros de registo

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

Para remover o plano e registar ficheiros associados a apenas um trabalho, utilize `azcopy jobs rm <job-id>` . Substitua o `<job-id>` espaço reservado neste exemplo pelo id de trabalho do trabalho.
