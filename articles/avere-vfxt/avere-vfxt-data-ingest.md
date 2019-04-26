---
title: Mover dados para Avere vFXT para o Azure
description: Como adicionar dados a um novo volume de armazenamento para utilização com o vFXT Avere para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: a3d6cb745c782d2a7166208f2a8dd1202a330b15
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60410125"
---
# <a name="moving-data-to-the-vfxt-cluster---parallel-data-ingest"></a>Mover dados para o cluster de vFXT - paralelas de dados de ingestão 

Depois de criar um novo cluster de vFXT, sua primeira tarefa pode ser mover dados para o novo volume de armazenamento. No entanto, se o seu método usual de mover dados está a emitir um comando de cópia simples de um cliente, provavelmente verá um desempenho de cópia lenta. Cópia de thread único não é uma boa opção para copiar dados para o armazenamento de back-end do cluster de vFXT Avere.

Uma vez que o cluster de vFXT Avere é uma cache de cliente multi dimensionável, é a forma mais rápida e mais eficiente para copiar dados para ele com vários clientes. Essa técnica processa a ingestão de ficheiros e objetos.

![Diagrama que mostra o movimento de dados de vários clientes, com múltiplos threads: Na parte superior esquerda, um ícone para o armazenamento de hardware no local tem setas vários provenientes do mesmo. As setas apontam para quatro máquinas de cliente. De cada computador cliente três setas pontos em direção a vFXT Avere. Com Avere vFXT, setas vários apontam-se ao armazenamento de Blobs.](media/avere-vfxt-parallel-ingest.png) 

O ``cp`` ou ``copy`` comandos comumente usadas para a utilizar para transferir dados do sistema de um armazenamento para outra são processos de thread único de mensagens em fila que copie apenas um ficheiro de cada vez. Isso significa que o servidor de ficheiros está a ingerir apenas um ficheiro de cada vez - o que é um desperdício de recursos do cluster.

Este artigo explica as estratégias para a criação de um ficheiro de vários cliente e com múltiplos threads copiar o sistema para mover dados para o cluster de vFXT Avere. Explica os conceitos de transferência de ficheiros e os pontos de decisão que podem ser utilizados para cópia de dados eficientes com vários clientes e comandos de cópia simples.

Também explica alguns utilitários que podem ajudar. O ``msrsync`` utilitário pode ser utilizado para parcialmente automatizar o processo de divisão de um conjunto de dados em buckets e utilizar comandos do rsync. O ``parallelcp`` script é outro utilitário que lê o diretório de origem e problemas de copiar os comandos automaticamente.  

Clique na ligação para saltar para uma secção:

* [Exemplo de cópia manual](#manual-copy-example) – uma explicação completa sobre utilizar comandos de cópia
* [Exemplo de parcialmente automatizada (msrsync)](#use-the-msrsync-utility-to-populate-cloud-volumes) 
* [Exemplo de cópia paralela](#use-the-parallel-copy-script)

## <a name="data-ingestor-vm-template"></a>Modelo de VM de ingestor de dados

Um modelo do Resource Manager está disponível no GitHub para criar automaticamente uma VM com as ferramentas de ingestão de dados paralelos mencionadas neste artigo. 

![diagrama mostra vários setas do armazenamento de BLOBs, armazenamento de hardware e as origens de ficheiros do Azure. As setas apontam para uma "vm de ingestor de dados" e a partir daí, setas vários apontam para o vFXT Avere](media/avere-vfxt-ingestor-vm.png)

A VM de ingestor de dados é parte de um tutorial em que a VM recém-criada monta o cluster de vFXT Avere e transfere o seu script de arranque de configuração do cluster. Leia [inicializar um ingestor de dados VM](https://github.com/Azure/Avere/blob/master/docs/data_ingestor.md) para obter detalhes.

## <a name="strategic-planning"></a>Planejamento estratégico

Ao criar uma estratégia para copiar dados em paralelo, deve compreender as compensações no tamanho do ficheiro, a contagem de ficheiros e a profundidade de diretório.

* Quando os ficheiros estão pequeno, a métrica de interesse é arquivos por segundo.
* Quando os arquivos são grandes (10MiBi ou superior), a métrica de interesse é bytes por segundo.

Cada processo de cópia tem uma taxa de débito e a taxa de ficheiros transferidos que pode ser medida pelo comprimento do comando de cópia exceder o tempo e cálculo do tamanho de ficheiro e a contagem de ficheiros. Explicar como medir as taxas de está fora do escopo deste documento, mas é fundamental para compreender se lidará com arquivos pequenos ou grandes.

## <a name="manual-copy-example"></a>Exemplo de cópia manual 

Pode criar manualmente uma cópia com múltiplos threads num cliente executando mais de um comando de cópia ao mesmo tempo em segundo plano em relação a conjuntos predefinidos de ficheiros ou caminhos.

Linux/UNIX ``cp`` comando inclui o argumento ``-p`` para preservar a propriedade e mtime metadados. Adicionar este argumento para os comandos abaixo é opcional. (Adicionar o argumento aumenta o número de chamadas de sistema de ficheiros enviados do cliente para o sistema de ficheiros de destino para modificação de metadados.)

Neste exemplo simples copia dois ficheiros em paralelo:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Depois de emitir este comando, o `jobs` comando mostrará se dois threads estão em execução.

### <a name="predictable-filename-structure"></a>Estrutura de nome de ficheiro previsível 

Se os seus nomes de ficheiros forem previsíveis, pode utilizar expressões criar threads paralelos de cópia. 

Por exemplo, se o seu diretório contém ficheiros de 1000 que são numerados em seqüência da `0001` para `1000`, pode usar as expressões seguintes para criar threads paralelos dez que copia cada 100 ficheiros:

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

### <a name="unknown-filename-structure"></a>Estrutura de nome de arquivo desconhecido

Se a estrutura de nomenclatura de ficheiro não for previsível, pode agrupar ficheiros por nomes de diretório. 

Neste exemplo recolhe todos diretórios para enviar para ``cp`` comandos são executados como tarefas em segundo plano:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

Depois dos ficheiros são recolhidos, pode executar cópia paralela comandos recursivamente copiar os subdiretórios e todos os seus conteúdos:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

### <a name="when-to-add-mount-points"></a>Quando adicionar pontos de montagem

Depois de ter suficiente threads paralelos vai contra um ponto de montagem do sistema de ficheiros de destino único, haverá um ponto onde adicionando mais threads não lhe dá mais débito. (Débito irá ser medido em ficheiros por segundo ou bytes/segundo, consoante o tipo de dados.) Ou, pior ainda, excesso de threading às vezes pode causar uma degradação de débito.  

Quando isto acontecer, pode adicionar pontos de montagem do lado do cliente para outros endereços IP de cluster vFXT, usando o mesmo caminho de montagem do sistema de ficheiros remoto:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

Adicionar pontos de montagem do lado do cliente permite que dividir os comandos de cópia adicionais para adicional `/mnt/destination[1-3]` pontos, obter ainda mais o paralelismo de montagem.  

Por exemplo, se os ficheiros forem muito grandes, pode definir os comandos de cópia para utilizar caminhos de destino distintos, enviando mais comandos em paralelo do cliente a efetuar a cópia.

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

No exemplo acima, todos os pontos de montagem de três de destino que está a ser visados pelos processos de cópia de ficheiros do cliente.

### <a name="when-to-add-clients"></a>Quando adicionar clientes

Por último, quando atingiu capacidades do cliente, adicionando mais threads de cópia ou pontos de montagem adicionais não resultará em quaisquer arquivos/s adicionais ou aumenta de bytes/seg. Nessa situação, pode implementar outro cliente com o mesmo conjunto de pontos de montagem que irá executar seus próprios conjuntos de processos de cópia de ficheiros. 

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

### <a name="create-file-manifests"></a>Criar ficheiro de manifestos

Depois de compreender as abordagens acima (vários cópia-threads por destino, vários destinos por cliente, vários clientes por sistema de ficheiros de origem acessível através da rede), considere esta recomendação: Criar ficheiro de manifestos e, em seguida, utilizá-los com os comandos de cópia por vários clientes.

Este cenário utiliza o UNIX ``find`` comando para criar os manifestos de arquivos ou diretórios:

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

Redirecione o resultado para um ficheiro: `find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Em seguida, pode iterar o manifesto, utilizar comandos do BASH para contagem de ficheiros e determinar os tamanhos dos subdiretórios:

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l`    `du -sh ${i}`"; done
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

Por último, deve trabalhar os comandos de cópia de arquivo real para os clientes.  

Se tiver quatro clientes, use este comando:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Se tiver cinco clientes, use algo como:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

E para seis... Utilize para tirar conclusões conforme necessário.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

Obterá *N* arquivos resultantes, um para cada um dos seus *N* clientes que tem os nomes de caminho para os diretórios de quarto nível obtidos como parte da saída do `find` comando. 

Utilize cada ficheiro para criar o comando de cópia:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

O procedimento acima irá dar-lhe *N* ficheiros, cada um com um comando de cópia por linha, que pode ser executado como um script BASH no cliente. 

O objetivo é executar vários threads esses scripts em simultâneo por cliente em paralelo em vários clientes.

## <a name="use-the-msrsync-utility-to-populate-cloud-volumes"></a>Utilizar o utilitário de msrsync para preencher os volumes de cloud

O ``msrsync`` ferramenta também pode ser utilizada para mover dados para um filtro de núcleos de back-end para o cluster Avere. Esta ferramenta foi concebida para otimizar a utilização de largura de banda ao executar várias paralelo ``rsync`` processos. Está disponível a partir do GitHub em https://github.com/jbd/msrsync.

``msrsync`` Divide o diretório de origem em separado "registos" e, em seguida, executa individuais ``rsync`` processos em cada registo.

Testes preliminares, utilizar uma VM de quatro núcleos mostraram a melhor eficiência quando utilizar os processos de 64. Utilize o ``msrsync`` opção ``-p`` para definir o número de processos para 64.

Tenha em atenção que ``msrsync`` pode gravar apenas de e para volumes locais. A origem e de destino tem de ser acessíveis como monta local na rede de virtual do cluster.

Para utilizar msrsync para preencher um volume de cloud do Azure com um cluster de Avere, siga estas instruções:

1. Instalar msrsync e seus pré-requisitos (rsync e Python 2.6 ou posterior)
1. Determine o número total de ficheiros e diretórios a serem copiados.

   Por exemplo, utilize o utilitário de Avere ``prime.py`` com argumentos ```prime.py --directory /path/to/some/directory``` (disponível por baixar o url https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py).

   Se não utilizar ``prime.py``, é possível calcular o número de itens com a Gnu ``find`` ferramenta da seguinte forma:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Divida o número de itens por 64 para determinar o número de itens por processo. Utilize este número com o ``-f`` opção para definir o tamanho dos registos ao executar o comando.

1. Emita o comando msrsync para copiar ficheiros:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Por exemplo, este comando foi concebido para mover 11,000 ficheiros em 64 processos de /test/source-repository para /mnt/vfxt/repository:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository``

## <a name="use-the-parallel-copy-script"></a>Utilize o script de cópia paralela

O ``parallelcp`` script também pode ser útil para mover dados para o armazenamento de back-end do seu cluster vFXT. 

O script abaixo irá adicionar o executável `parallelcp`. (Este script foi concebido para Ubuntu; se usar a distribuição de outro, tem de instalar ``parallel`` separadamente.)

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

Este exemplo utiliza o script de cópia paralela para compilar ``glibc`` usando arquivos de origem do Avere cluster. 
<!-- xxx what is stored where? what is 'the avere cluster mount point'? xxx -->

Os ficheiros de origem são armazenados no ponto de montagem do cluster de Avere e os ficheiros de objeto são armazenados no disco rígido local.

Este script utiliza o script de cópia paralela acima. A opção ``-j`` é utilizado com ``parallelcp`` e ``make`` para obter a paralelização.

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

