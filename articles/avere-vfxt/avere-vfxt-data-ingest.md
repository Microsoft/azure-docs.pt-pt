---
title: Movendo dados para o avere vFXT para o Azure
description: Como adicionar dados a um novo volume de armazenamento para uso com o avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: c2a38b20fff789faf370e3161a92a31ed5f04c57
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76153723"
---
# <a name="moving-data-to-the-vfxt-cluster---parallel-data-ingest"></a>Movendo dados para o cluster vFXT-ingestão de dados paralelos

Depois de criar um novo cluster vFXT, sua primeira tarefa pode ser mover dados para um novo volume de armazenamento no Azure. No entanto, se o método habitual de mover dados estiver emitindo um comando de cópia simples de um cliente, você provavelmente verá um desempenho de cópia lento. A cópia de thread único não é uma boa opção para copiar dados para o armazenamento de back-end do cluster avere vFXT.

Como o avere vFXT para o cluster do Azure é um cache de vários clientes escalonáveis, a maneira mais rápida e eficiente de copiar dados para ele é com vários clientes. Essa técnica paralelize a ingestão dos arquivos e objetos.

![Diagrama mostrando a movimentação de dados multithreads com vários clientes: na parte superior esquerda, um ícone para o armazenamento de hardware local tem várias setas provenientes dele. As setas apontam para quatro computadores cliente. De cada computador cliente, três setas apontam para o avere vFXT. Do avere vFXT, várias setas apontam para o armazenamento de BLOBs.](media/avere-vfxt-parallel-ingest.png)

Os comandos ``cp`` ou ``copy`` que normalmente são usados para transferir dados de um sistema de armazenamento para outro são processos de thread único que copiam apenas um arquivo por vez. Isso significa que o servidor de arquivos está ingerindo apenas um arquivo por vez, o que é um desperdício dos recursos do cluster.

Este artigo explica estratégias para criar um sistema de cópia de arquivos multifuncional multilocação para mover dados para o cluster avere vFXT. Ele explica os conceitos de transferência de arquivos e os pontos de decisão que podem ser usados para a cópia eficiente de dados usando vários clientes e comandos de cópia simples.

Ele também explica alguns utilitários que podem ajudar. O utilitário ``msrsync`` pode ser usado para automatizar parcialmente o processo de dividir um conjunto de um DataSet em buckets e usar comandos ``rsync``. O script ``parallelcp`` é outro utilitário que lê o diretório de origem e emite comandos de cópia automaticamente. Além disso, a ferramenta de ``rsync`` pode ser usada em duas fases para fornecer uma cópia mais rápida que ainda fornece consistência de dados.

Clique no link para ir para uma seção:

* [Exemplo de cópia manual](#manual-copy-example) -uma explicação completa usando comandos de cópia
* [Exemplo de rsync de duas fases](#use-a-two-phase-rsync-process)
* [Exemplo parcialmente automatizado (msrsync)](#use-the-msrsync-utility)
* [Exemplo de cópia paralela](#use-the-parallel-copy-script)

## <a name="data-ingestor-vm-template"></a>Modelo de VM de ingestão de dados

Um modelo do Resource Manager está disponível no GitHub para criar automaticamente uma VM com as ferramentas de ingestão de dados paralelas mencionadas neste artigo.

![diagrama mostrando várias setas cada do armazenamento de BLOBs, armazenamento de hardware e fontes de arquivo do Azure. As setas apontam para uma "VM de ingestão de dados" e, a partir daí, várias setas apontam para avere vFXT](media/avere-vfxt-ingestor-vm.png)

A VM de ingestão de dados faz parte de um tutorial em que a VM recém-criada monta o cluster avere vFXT e baixa seu script de bootstrap do cluster. Leia [inicializar uma VM de ingestão de dados](https://github.com/Azure/Avere/blob/master/docs/data_ingestor.md) para obter detalhes.

## <a name="strategic-planning"></a>Planejamento estratégico

Ao criar uma estratégia para copiar dados em paralelo, você deve entender as compensações no tamanho do arquivo, contagem de arquivos e profundidade de diretório.

* Quando os arquivos são pequenos, a métrica de interesse é de arquivos por segundo.
* Quando os arquivos são grandes (10MiBi ou superior), a métrica de interesse é de bytes por segundo.

Cada processo de cópia tem uma taxa de produtividade e uma taxa de transferência de arquivos, que pode ser medida por cronometrar o comprimento do comando de cópia e fatorar o tamanho do arquivo e a contagem de arquivos. Explicar como medir as tarifas está fora do escopo deste documento, mas é importante entender se você estará lidando com arquivos pequenos ou grandes.

## <a name="manual-copy-example"></a>Exemplo de cópia manual

Você pode criar manualmente uma cópia multi-threaded em um cliente executando mais de um comando de cópia de uma vez em segundo plano em relação a conjuntos predefinidos de arquivos ou caminhos.

O comando ``cp`` do Linux/UNIX inclui o argumento ``-p`` para preservar a propriedade e os metadados mtime. A adição deste argumento aos comandos abaixo é opcional. (Adicionar o argumento aumenta o número de chamadas do sistema de arquivos enviadas do cliente para o sistema de arquivos de destino para a modificação de metadados.)

Este exemplo simples copia dois arquivos em paralelo:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Depois de emitir esse comando, o comando `jobs` mostrará que dois threads estão em execução.

### <a name="predictable-filename-structure"></a>Estrutura de nome de arquivo previsível

Se os nomes de seus forem previsíveis, você poderá usar expressões para criar threads de cópia paralela.

Por exemplo, se seu diretório contiver 1000 arquivos que são numerados sequencialmente de `0001` para `1000`, você poderá usar as expressões a seguir para criar dez threads paralelos que cada cópia 100 arquivos:

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination1/ & \
cp /mnt/source/file2* /mnt/destination1/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination1/ & \
cp /mnt/source/file5* /mnt/destination1/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination1/ & \
cp /mnt/source/file8* /mnt/destination1/ & \
cp /mnt/source/file9* /mnt/destination1/
```

### <a name="unknown-filename-structure"></a>Estrutura de nome de arquivo desconhecida

Se a estrutura de nomeação de arquivos não for previsível, você poderá agrupar arquivos por nomes de diretório.

Este exemplo coleta diretórios inteiros para enviar para ``cp`` comandos executados como tarefas em segundo plano:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

Depois que os arquivos são coletados, você pode executar comandos de cópia paralela para copiar recursivamente os subdiretórios e todo o seu conteúdo:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

### <a name="when-to-add-mount-points"></a>Quando adicionar pontos de montagem

Depois que você tiver threads paralelos suficientes indo em um único ponto de montagem do sistema de arquivos de destino, haverá um ponto em que a adição de mais threads não oferece mais taxa de transferência. (A taxa de transferência será medida em arquivos/segundo ou em bytes/segundo, dependendo do tipo de dados.) Ou pior, o excesso de threads pode, às vezes, causar uma degradação da taxa de transferência.

Quando isso acontece, você pode adicionar pontos de montagem do lado do cliente a outros endereços IP do cluster vFXT, usando o mesmo caminho de montagem do sistema de arquivos remoto:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

A adição de pontos de montagem do lado do cliente permite bifurcar comandos de cópia adicionais para os pontos de montagem `/mnt/destination[1-3]` adicionais, alcançando mais paralelismo.

Por exemplo, se os arquivos forem muito grandes, você poderá definir os comandos de cópia para usar caminhos de destino distintos, enviando mais comandos em paralelo do cliente que executa a cópia.

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination2/ & \
cp /mnt/source/file2* /mnt/destination3/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination2/ & \
cp /mnt/source/file5* /mnt/destination3/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination2/ & \
cp /mnt/source/file8* /mnt/destination3/ & \
```

No exemplo acima, todos os três pontos de montagem de destino estão sendo direcionados pelos processos de cópia de arquivo do cliente.

### <a name="when-to-add-clients"></a>Quando adicionar clientes

Por fim, quando você tiver atingido os recursos do cliente, adicionar mais threads de cópia ou pontos de montagem adicionais não produzirá nenhum arquivo adicional/s ou bytes/s aumenta. Nessa situação, você pode implantar outro cliente com o mesmo conjunto de pontos de montagem que executará seus próprios conjuntos de processos de cópia de arquivos.

Exemplo:

```bash
Client1: cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
Client1: cp -R /mnt/source/dir2/dir2a /mnt/destination/dir2/ &
Client1: cp -R /mnt/source/dir3/dir3a /mnt/destination/dir3/ &

Client2: cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
Client2: cp -R /mnt/source/dir2/dir2b /mnt/destination/dir2/ &
Client2: cp -R /mnt/source/dir3/dir3b /mnt/destination/dir3/ &

Client3: cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ &
Client3: cp -R /mnt/source/dir2/dir2c /mnt/destination/dir2/ &
Client3: cp -R /mnt/source/dir3/dir3c /mnt/destination/dir3/ &

Client4: cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
Client4: cp -R /mnt/source/dir2/dir2d /mnt/destination/dir2/ &
Client4: cp -R /mnt/source/dir3/dir3d /mnt/destination/dir3/ &
```

### <a name="create-file-manifests"></a>Criar manifestos de arquivo

Depois de entender as abordagens acima (vários threads de cópia por destino, vários destinos por cliente, vários clientes por sistema de arquivos de origem acessível por rede), considere esta recomendação: Compilar manifestos de arquivo e usá-los com cópia comandos em vários clientes.

Esse cenário usa o comando UNIX ``find`` para criar manifestos de arquivos ou diretórios:

```bash
user@build:/mnt/source > find . -mindepth 4 -maxdepth 4 -type d
./atj5b55c53be6-01/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-01/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-01/support/trace/rolling
./atj5b55c53be6-03/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-03/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-03/support/trace/rolling
./atj5b55c53be6-02/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-02/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-02/support/trace/rolling
```

Redirecionar o resultado para um arquivo: `find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Em seguida, você pode iterar por meio do manifesto, usando comandos BASH para contar arquivos e determinar os tamanhos dos subdiretórios:

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l` `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
7      16K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:12:19UTC
8      83K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:17:17UTC
575    7.7M    ./atj5b5ab44b7f-02/support/cores/armada_main.2000.1531980253.gsi
33     4.4G    ./atj5b5ab44b7f-02/support/trace/rolling
281    6.6M    ./atj5b5ab44b7f-01/support/gsi/2018-07-18T00:07:03EDT
15     182K    ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-18T05:01:00UTC
244    17M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-19T01:01:01UTC
299    31M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T01:01:00UTC
256    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T21:59:41UTC_partial
889    7.7M    ./atj5b5ab44b7f-01/support/gsi/2018-07-20T21:59:41UTC
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
11     248K    ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:12:19UTC
11     88K     ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:17:17UTC
645    11M     ./atj5b5ab44b7f-01/support/cores/armada_main.2019.1531980253.gsi
33     4.0G    ./atj5b5ab44b7f-01/support/trace/rolling
244    2.1M    ./atj5b5ab44b7f-03/support/gsi/2018-07-18T00:07:03EDT
9      158K    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.3M    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T01:01:00UTC
131    12M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    8.4M    ./atj5b5ab44b7f-03/support/gsi/2018-07-20T21:59:41UTC
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_vfxt_catchup
7      159K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:12:19UTC
7      157K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:17:17UTC
576    12M     ./atj5b5ab44b7f-03/support/cores/armada_main.2013.1531980253.gsi
33     2.8G    ./atj5b5ab44b7f-03/support/trace/rolling
```

Por fim, você deve criar os comandos de cópia de arquivo reais para os clientes.

Se você tiver quatro clientes, use este comando:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Se você tiver cinco clientes, use algo assim:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

E por seis... Extrapolar conforme necessário.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

Você receberá *N* arquivos resultantes, um para cada um dos seus *n* clientes que têm os nomes de caminho para os diretórios de nível quatro obtidos como parte da saída do comando `find`.

Use cada arquivo para criar o comando de cópia:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

O acima fornecerá a você *N* arquivos, cada um com um comando de cópia por linha, que pode ser executado como um script de bash no cliente.

O objetivo é executar vários threads desses scripts simultaneamente por cliente em paralelo em vários clientes.

## <a name="use-a-two-phase-rsync-process"></a>Usar um processo rsync de duas fases

O utilitário de ``rsync`` padrão não funciona bem para popular o armazenamento em nuvem por meio do avere vFXT para o sistema do Azure porque ele gera um grande número de operações de criação e renomeação de arquivo para garantir a integridade dos dados. No entanto, você pode usar com segurança a opção ``--inplace`` com ``rsync`` para ignorar o procedimento de cópia mais cuidadoso se você seguir isso com uma segunda execução que verifica a integridade do arquivo.

Uma operação de cópia de ``rsync`` padrão cria um arquivo temporário e o preenche com os dados. Se a transferência de dados for concluída com êxito, o arquivo temporário será renomeado para o nome de arquivo original. Esse método garante a consistência, mesmo que os arquivos sejam acessados durante a cópia. Mas esse método gera mais operações de gravação, o que reduz a movimentação de arquivos por meio do cache.

A opção ``--inplace`` grava o novo arquivo diretamente em seu local final. Não há garantia de que os arquivos sejam consistentes durante a transferência, mas isso não é importante se você estiver esficando um sistema de armazenamento para uso posterior.

A segunda ``rsync`` operação serve como uma verificação de consistência na primeira operação. Como os arquivos já foram copiados, a segunda fase é uma verificação rápida para garantir que os arquivos no destino correspondam aos arquivos na origem. Se os arquivos não corresponderem, eles serão copiados novamente.

Você pode emitir ambas as fases em um único comando:

```bash
rsync -azh --inplace <source> <destination> && rsync -azh <source> <destination>
```

Esse método é um método simples e econômico para conjuntos de data até o número de arquivos que o Gerenciador de diretório interno pode manipular. (Normalmente, são arquivos 200 milhões para um cluster de três nós, 500 milhões arquivos para um cluster de seis nós e assim por diante.)

## <a name="use-the-msrsync-utility"></a>Usar o utilitário msrsync

A ferramenta de ``msrsync`` também pode ser usada para mover dados para um Filer principal de back-end para o cluster avere. Essa ferramenta foi projetada para otimizar o uso da largura de banda executando vários processos paralelos de ``rsync``. Ele está disponível no GitHub em <https://github.com/jbd/msrsync>.

``msrsync`` divide o diretório de origem em "buckets" separados e, em seguida, executa os processos de ``rsync`` individuais em cada Bucket.

Os testes preliminares usando uma VM de quatro núcleos mostraram a melhor eficiência ao usar os processos 64. Use a opção ``msrsync`` ``-p`` para definir o número de processos como 64.

Você também pode usar o argumento ``--inplace`` com comandos ``msrsync``. Se você usar essa opção, considere a execução de um segundo comando (como com [rsync](#use-a-two-phase-rsync-process), descrito acima) para garantir a integridade dos dados.

``msrsync`` só pode gravar de e para volumes locais. A origem e o destino devem estar acessíveis como montagens locais na rede virtual do cluster.

Para usar ``msrsync`` para popular um volume de nuvem do Azure com um cluster avere, siga estas instruções:

1. Instalar ``msrsync`` e seus pré-requisitos (rsync e Python 2,6 ou posterior)
1. Determine o número total de arquivos e diretórios a serem copiados.

   Por exemplo, use o utilitário avere ``prime.py`` com argumentos ```prime.py --directory /path/to/some/directory``` (disponível baixando a URL <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>).

   Se não estiver usando ``prime.py``, você poderá calcular o número de itens com a ferramenta GNU ``find`` da seguinte maneira:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Divida o número de itens por 64 para determinar o número de itens por processo. Use esse número com a opção ``-f`` para definir o tamanho dos buckets ao executar o comando.

1. Emita o comando ``msrsync`` para copiar arquivos:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Se estiver usando ``--inplace``, adicione uma segunda execução sem a opção para verificar se os dados foram copiados corretamente:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH> && msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Por exemplo, este comando foi projetado para mover arquivos 11.000 em 64 processos de/Test/Source-Repository para/mnt/vfxt/Repository:

   ``msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository && msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository``

## <a name="use-the-parallel-copy-script"></a>Usar o script de cópia paralela

O script de ``parallelcp`` também pode ser útil para mover dados para o armazenamento de back-end do cluster vFXT.

O script a seguir adicionará o `parallelcp`executável. (Esse script foi criado para o Ubuntu; se estiver usando outra distribuição, você deverá instalar o ``parallel`` separadamente.)

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM
#!/bin/bash

display_usage() {
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n"
}

if [  \$# -le 1 ] ; then
    display_usage
    exit 1
fi

if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then
    display_usage
    exit 0
fi

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

### <a name="parallel-copy-example"></a>Exemplo de cópia paralela

Este exemplo usa o script de cópia paralela para compilar ``glibc`` usando arquivos de origem do cluster avere.
<!-- xxx what is stored where? what is 'the avere cluster mount point'? xxx -->

Os arquivos de origem são armazenados no ponto de montagem do cluster avere e os arquivos de objeto são armazenados na unidade de disco rígido local.

Esse script usa script de cópia paralela acima. A opção ``-j`` é usada com ``parallelcp`` e ``make`` para obter paralelização.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/node1 avere
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
