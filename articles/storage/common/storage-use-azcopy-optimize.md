---
title: Otimizar o desempenho do AzCopy v10 com a Azure Storage | Microsoft Docs
description: Este artigo ajuda-o a otimizar o desempenho do AzCopy v10 com o Azure Storage.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: b60fc4b1fc20c455c2c409f544a8af16f1dbf8d1
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509136"
---
# <a name="optimize-the-performance-of-azcopy-with-azure-storage"></a>Otimizar o desempenho da AzCopy com o Azure Storage

O AzCopy é um utilitário da linha de comando que pode utilizar para copiar blobs ou ficheiros de/para uma conta de armazenamento. Este artigo ajuda-o a otimizar o desempenho.

> [!NOTE]
> Se procura conteúdo para ajudá-lo a começar com a AzCopy, consulte [Get start with AzCopy](storage-use-azcopy-v10.md)

Você pode comparar o desempenho e, em seguida, usar comandos e variáveis ambientais para encontrar uma troca ideal entre desempenho e consumo de recursos.

## <a name="run-benchmark-tests"></a>Executar testes de referência

Pode executar um teste de referência de desempenho em recipientes de bolhas específicas ou ações de ficheiros para visualizar estatísticas gerais de desempenho e identificar estrangulamentos de desempenho. Pode executar o teste carregando ou descarregando dados de teste gerados. 

Utilize o seguinte comando para executar um teste de referência de desempenho.

**Syntax**

`azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'`

**Exemplo**

```azcopy
azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

> [!TIP]
> Este exemplo encerra argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

Este comando executa uma referência de desempenho enviando dados de teste para um destino especificado. Os dados do teste são gerados na memória, enviados para o destino e depois eliminados do destino após a conclusão do teste. Pode especificar quantos ficheiros gerar e qual o tamanho que pretende que sejam utilizando parâmetros de comando opcionais.

Se preferir realizar este teste descarregando dados, desfie o `mode` parâmetro para `download` . Para obter documentos de referência detalhados, consulte [a referência de azcopia](storage-ref-azcopy-bench.md). 

## <a name="optimize-for-large-numbers-of-small-files"></a>Otimizar para um grande número de pequenos ficheiros

A produção pode diminuir ao transferir pequenos ficheiros, especialmente quando se transfere um grande número deles. Para maximizar o desempenho, reduza o tamanho de cada trabalho. Para operações de descarregamento e upload, aumente a concordância, diminua a atividade de log e desligue funcionalidades que incorram em elevados custos de desempenho.

#### <a name="reduce-the-size-of-each-job"></a>Reduzir o tamanho de cada trabalho

Para obter um desempenho ótimo, certifique-se de que cada emprego transfere menos de 10 milhões de ficheiros. Os postos de trabalho que transferem mais de 50 milhões de ficheiros podem ter um desempenho fraco porque o mecanismo de rastreio de emprego da AzCopy incorre numa quantidade significativa de despesas gerais. Para reduzir as despesas gerais, considere dividir os grandes empregos em pequenos. 

Uma forma de reduzir o tamanho de um trabalho é limitar o número de ficheiros afetados por um trabalho. Podes usar parâmetros de comando para o fazeres. Por exemplo, um trabalho pode copiar apenas um subconjunto de diretórios utilizando o `include path` parâmetro como parte do comando de cópia da [azcopia.](storage-ref-azcopy-copy.md) 

Utilize o `include-pattern` parâmetro para copiar ficheiros que tenham uma extensão específica (por exemplo: `*.pdf` ). Num trabalho separado, use o `exclude-pattern` parâmetro para copiar todos os ficheiros que não tenham `*.pdf` extensão. Consulte [ficheiros específicos do Upload](storage-use-azcopy-blobs-upload.md#upload-specific-files) e [Descarregue blobs específicos](storage-use-azcopy-blobs-download.md#download-specific-blobs) por exemplo.

Depois de decidir como dividir grandes empregos em trabalhos mais pequenos, considere gerir empregos em mais do que uma Máquina Virtual (VM).

#### <a name="increase-concurrency"></a>Aumentar a concordância

Se estiver a carregar ou a descarregar ficheiros, utilize a `AZCOPY_CONCURRENCY_VALUE` variável ambiente para aumentar o número de pedidos simultâneos que podem ocorrer na sua máquina. Desente esta variável o mais alto possível sem comprometer o desempenho da sua máquina. Para saber mais sobre esta variável, veja o [Número de pedidos simultâneos](#increase-the-number-of-concurrent-requests) nesta secção de pedidos.

Se estiver a copiar bolhas entre contas de armazenamento, considere definir o valor da `AZCOPY_CONCURRENCY_VALUE` variável ambiental para um valor superior a `1000` . Pode definir esta variável alta porque a AzCopy utiliza APIs de servidor a servidor, por isso os dados são copiados diretamente entre servidores de armazenamento e não utilizam o poder de processamento da sua máquina.  

#### <a name="decrease-the-number-of-logs-generated"></a>Diminuir o número de registos gerados

Pode melhorar o desempenho reduzindo o número de entradas de registo que a AzCopy cria à medida que completa uma operação. Por padrão, a AzCopy regista toda a atividade relacionada com uma operação. Para obter um desempenho ótimo, considere definir o `log-level` parâmetro da sua cópia, sincronizar ou remover o comando para `ERROR` . Assim, a AzCopy regista apenas erros. Por predefinição, o nível de registo de valor é definido para `INFO` . 

#### <a name="turn-off-length-checking"></a>Desligue a verificação do comprimento 

Se estiver a carregar ou a descarregar ficheiros, considere definir os `--check-length` comandos da sua cópia e sincronizar para `false` . Isto impede que a AzCopy verifique o comprimento de um ficheiro após uma transferência. Por predefinição, o AzCopy verifica o comprimento para garantir que os ficheiros de origem e destino coincidem após a conclusão de uma transferência. A AzCopy executa esta verificação após cada transferência de ficheiro. Este cheque pode degradar o desempenho quando os trabalhos transferem um grande número de pequenos ficheiros. 

#### <a name="turn-on-concurrent-local-scanning-linux"></a>Ligue a digitalização local simultânea (Linux)

As verificações de ficheiros em alguns sistemas Linux não executam rápido o suficiente para saturar todas as ligações paralelas da rede. Nestes casos, pode definir `AZCOPY_CONCURRENT_SCAN` o `true` . 

## <a name="increase-the-number-of-concurrent-requests"></a>Aumentar o número de pedidos simultâneos

Pode aumentar a produção definindo a `AZCOPY_CONCURRENCY_VALUE` variável ambiente. Esta variável especifica o número de pedidos simultâneos que podem ocorrer.  

Se o seu computador tiver menos de 5 CPUs, então o valor desta variável está definido para `32` . Caso contrário, o valor predefinido é igual a 16 vezes o número de CPUs. O valor predefinido máximo desta variável `3000` é, mas pode definir manualmente este valor mais ou menos. 

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **macOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Utilize o `azcopy env` para verificar o valor atual desta variável. Se o valor estiver em branco, então pode ler qual o valor que está a ser utilizado olhando para o início de qualquer ficheiro de registo AzCopy. O valor selecionado, e a razão pela qual foi selecionado, são comunicados lá.

Antes de definir esta variável, recomendamos que faça um teste de referência. O processo de teste de referência reportará o valor de concordância recomendado. Em alternativa, se as condições da sua rede e as cargas de trabalho variarem, desa um ponto de variação desta variável para a palavra `AUTO` em vez de um número específico. Isso fará com que a AzCopy execute sempre o mesmo processo de afinação automática que utiliza em testes de referência.

## <a name="limit-the-throughput-data-rate"></a>Limitar a taxa de dados de produção

Pode utilizar a `cap-mbps` bandeira nos seus comandos para colocar um teto na taxa de dados de produção. Por exemplo, o seguinte comando retoma um trabalho e caps de produção para `10` megabits (Mb) por segundo. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

## <a name="optimize-memory-use"></a>Otimizar o uso da memória

Desfita a `AZCOPY_BUFFER_GB` variável ambiente para especificar a quantidade máxima da memória do sistema que pretende que o AzCopy utilize para tamponar ao descarregar e carregar ficheiros. Expresse este valor em gigabytes (GB).

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **macOS** | `export AZCOPY_BUFFER_GB=<value>` |

> [!NOTE]
> O rastreio de trabalho incorre sempre em sobrecargas adicionais no uso da memória. O valor varia em função do número de transferências num trabalho. Os tampão são o maior componente do uso da memória. Pode ajudar a controlar a sobrecarga utilizando `AZCOPY_BUFFER_GB` aproximadamente os seus requisitos, mas não existe nenhuma bandeira disponível para limitar rigorosamente o uso geral da memória.

## <a name="optimize-file-synchronization"></a>Otimizar a sincronização de ficheiros

O comando [de sincronização](storage-ref-azcopy-sync.md) identifica todos os ficheiros no destino e, em seguida, compara os nomes dos ficheiros e os últimos cartões de tempo modificados antes do início da operação de sincronização. Se tiver um grande número de ficheiros, então pode melhorar o desempenho eliminando este processamento frontal. 

Para isso, use o comando [de cópia azcopia](storage-ref-azcopy-copy.md) e coloque a `--overwrite` bandeira para `ifSourceNewer` . O AzCopy comparará os ficheiros à medida que são copiados sem realizar quaisquer exames e comparações frontais. Isto fornece uma vantagem de desempenho em casos em que há um grande número de ficheiros para comparar.

O comando [de cópia azcopia](storage-ref-azcopy-copy.md) não apaga ficheiros do destino, por isso, se pretender apagar ficheiros no destino quando já não existirem na fonte, utilize o comando de sincronização de [azcopia](storage-ref-azcopy-sync.md) com a `--delete-destination` bandeira definida para um valor de ou `true` `prompt` .

## <a name="see-also"></a>Ver também

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)