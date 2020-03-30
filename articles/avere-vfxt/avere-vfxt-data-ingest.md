---
title: Movendo dados para Avere vFXT para Azure
description: Como adicionar dados a um novo volume de armazenamento para utilização com o Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: c2a38b20fff789faf370e3161a92a31ed5f04c57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153723"
---
# <a name="moving-data-to-the-vfxt-cluster---parallel-data-ingest"></a>Mover dados para o cluster vFXT - Ingerir dados paralelos

Depois de criar um novo cluster vFXT, a sua primeira tarefa pode ser mover dados para um novo volume de armazenamento em Azure. No entanto, se o seu método habitual de movimentação de dados estiver a emitir um simples comando de cópia de um cliente, provavelmente verá um desempenho de cópia lento. A cópia com fios únicos não é uma boa opção para copiar dados para o armazenamento traseiro do cluster Avere vFXT.

Como o Cluster Avere vFXT para Azure é uma cache de vários clientes escalável, a forma mais rápida e eficiente de copiar dados para ele é com vários clientes. Esta técnica paraleliza a ingestão dos ficheiros e objetos.

![Diagrama mostrando movimento de dados multi-cliente, multi-threaded: Na parte superior esquerda, um ícone para armazenamento de hardware no local tem várias setas provenientes dele. As setas apontam para quatro máquinas de clientes. De cada máquina cliente três setas apontam para o Avere vFXT. Do Avere vFXT, várias setas apontam para o armazenamento blob.](media/avere-vfxt-parallel-ingest.png)

Os ``cp`` ``copy`` comandos ou que são comumente usados para transferir dados de um sistema de armazenamento para outro são processos de roscar uninor que copiam apenas um ficheiro de cada vez. Isto significa que o servidor de ficheiros está a ingerir apenas um ficheiro de cada vez - o que é um desperdício dos recursos do cluster.

Este artigo explica estratégias para criar um sistema multi-cliente de cópia de ficheiros multi-threaded para mover dados para o cluster Avere vFXT. Explica conceitos de transferência de ficheiros e pontos de decisão que podem ser usados para copiar dados eficientes usando vários clientes e comandos de cópia simples.

Também explica alguns serviços que podem ajudar. O ``msrsync`` utilitário pode ser utilizado para automatizar parcialmente o processo de ``rsync`` divisão de um conjunto de dados em baldes e utilizando comandos. O ``parallelcp`` script é outro utilitário que lê o diretório de origem e emite comandos de cópia automaticamente. Além ``rsync`` disso, a ferramenta pode ser usada em duas fases para fornecer uma cópia mais rápida que ainda fornece consistência de dados.

Clique no link para saltar para uma secção:

* [Exemplo de cópia manual](#manual-copy-example) - Uma explicação completa usando comandos de cópia
* [Exemplo de rsync de duas fases](#use-a-two-phase-rsync-process)
* [Exemplo parcialmente automatizado (msrsync)](#use-the-msrsync-utility)
* [Exemplo de cópia paralela](#use-the-parallel-copy-script)

## <a name="data-ingestor-vm-template"></a>Modelo vm ingestor de dados

Um modelo de Gestor de Recursos está disponível no GitHub para criar automaticamente um VM com as ferramentas paralelas de ingestão de dados mencionadas neste artigo.

![diagrama mostrando várias setas cada uma a partir de armazenamento de blob, armazenamento de hardware e fontes de arquivo Azure. As setas apontam para um "data ingestor vm" e, a partir daí, várias setas apontam para o Avere vFXT](media/avere-vfxt-ingestor-vm.png)

O VM ingestor de dados faz parte de um tutorial onde o vM recém-criado monta o cluster Avere vFXT e descarrega o seu script de bootstrap do cluster. Leia [Bootstrap um VM ingestor](https://github.com/Azure/Avere/blob/master/docs/data_ingestor.md) de dados para mais detalhes.

## <a name="strategic-planning"></a>Planeamento estratégico

Ao conceber uma estratégia para copiar dados em paralelo, deve compreender as trocas em tamanho de ficheiro, contagem de ficheiros e profundidade de diretório.

* Quando os ficheiros são pequenos, a métrica de interesse é de ficheiros por segundo.
* Quando os ficheiros são grandes (10MiBi ou mais), a métrica de interesse é bytes por segundo.

Cada processo de cópia tem uma taxa de entrada e uma taxa transferida por ficheiros, que pode ser medida cronometrando o comprimento do comando de cópia e fatorizando o tamanho do ficheiro e a contagem de ficheiros. Explicar como medir as tarifas está fora do âmbito deste documento, mas é importante perceber se vai lidar com ficheiros pequenos ou grandes.

## <a name="manual-copy-example"></a>Exemplo de cópia manual

Pode criar manualmente uma cópia multi-threaded num cliente executando mais do que um comando de cópia ao mesmo tempo no fundo contra conjuntos predefinidos de ficheiros ou caminhos.

O comando Linux/UNIX ``cp`` ``-p`` inclui o argumento para preservar a propriedade e os metadados mtime. Adicionar este argumento aos comandos abaixo é opcional. (Adicionar o argumento aumenta o número de chamadas de sistema de ficheiros enviadas do cliente para o sistema de ficheiros de destino para modificação de metadados.)

Este exemplo simples copia dois ficheiros em paralelo:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Depois de emitir este `jobs` comando, o comando mostrará que dois fios estão a funcionar.

### <a name="predictable-filename-structure"></a>Estrutura previsível do nome de ficheiro

Se os seus nomes de ficheiros forem previsíveis, pode utilizar expressões para criar fios de cópia paralelos.

Por exemplo, se o seu diretório contiver 1000 ficheiros que são numerados sequencialmente de `0001` a, `1000`pode utilizar as seguintes expressões para criar dez fios paralelos que cada cópia de 100 ficheiros:

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

### <a name="unknown-filename-structure"></a>Estrutura de nome de ficheiro desconhecido

Se a sua estrutura de nomeação de ficheiros não for previsível, pode agrupar ficheiros com nomes de diretório.

Este exemplo recolhe diretórios ``cp`` inteiros para enviar para comandos executados como tarefas de fundo:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

Após a recolha dos ficheiros, pode executar comandos de cópia paralelos para copiar recursivamente os subdiretórios e todos os seus conteúdos:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

### <a name="when-to-add-mount-points"></a>Quando adicionar pontos de montagem

Depois de ter fios paralelos suficientes contra um único ponto de montagem do sistema de ficheiros de destino, haverá um ponto em que adicionar mais fios não dá mais energia. (A entrada será medida em ficheiros/segundo ou bytes/segundo, dependendo do seu tipo de dados.) Ou pior, o excesso de rosca pode, por vezes, causar uma degradação da entrada.

Quando isso acontecer, pode adicionar pontos de montagem do lado do cliente a outros endereços IP do cluster vFXT, utilizando o mesmo caminho de montagem do sistema de ficheiros remoto:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

A adição de pontos de montagem do lado do `/mnt/destination[1-3]` cliente permite-lhe desviar comandos de cópia adicionais para os pontos de montagem adicionais, alcançando mais paralelismo.

Por exemplo, se os seus ficheiros forem muito grandes, poderá definir os comandos de cópia para utilizar caminhos de destino distintos, enviando mais comandos em paralelo a partir do cliente que executa a cópia.

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

No exemplo acima, os três pontos de montagem de destino estão a ser alvo pelos processos de cópia de ficheiros do cliente.

### <a name="when-to-add-clients"></a>Quando adicionar clientes

Por último, quando tiver atingido as capacidades do cliente, adicionar mais fios de cópia ou pontos de montagem adicionais não produzirá quaisquer ficheiros/sec adicionais ou aumentos de bytes/seg. Nessa situação, pode implantar outro cliente com o mesmo conjunto de pontos de montagem que estarão a executar os seus próprios conjuntos de processos de cópia de ficheiros.

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

### <a name="create-file-manifests"></a>Criar manifestos de ficheiros

Depois de compreender as abordagens acima (múltiplos fios de cópia por destino, múltiplos destinos por cliente, múltiplos clientes por sistema de ficheiros de origem acessível à rede), considere esta recomendação: Construa manifestos de ficheiros e, em seguida, use-os com cópia comandos através de vários clientes.

Este cenário utiliza ``find`` o comando UNIX para criar manifestos de ficheiros ou diretórios:

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

Redirecione este resultado para um ficheiro:`find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Em seguida, pode iterar através do manifesto, usando comandos BASH para contar ficheiros e determinar os tamanhos dos subdiretórios:

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

Por último, tem de elaborar os comandos de cópia de ficheiros reais para os clientes.

Se tiver quatro clientes, use este comando:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Se tem cinco clientes, use algo assim:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

E por seis... Extrapolar, se necessário.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

Obterá ficheiros *N* resultantes, um para cada um dos seus clientes *N* que tem os nomes `find` de caminho para os diretórios de nível 4 obtidos como parte da saída do comando.

Utilize cada ficheiro para construir o comando de cópia:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

O acima irá dar-lhe ficheiros *N,* cada um com um comando de cópia por linha, que pode ser executado como um roteiro BASH no cliente.

O objetivo é executar vários fios destes scripts simultaneamente por cliente em paralelo em vários clientes.

## <a name="use-a-two-phase-rsync-process"></a>Use um processo de rsync de duas fases

O ``rsync`` utilitário padrão não funciona bem para a população de armazenamento em nuvem através do sistema Avere vFXT para o sistema Azure porque gera um grande número de operações de criação de ficheiros e renomeação para garantir a integridade dos dados. No entanto, pode ``--inplace`` utilizar ``rsync`` com segurança a opção para ignorar o procedimento de cópia mais cuidadoso se o seguir com uma segunda execução que verifica a integridade do ficheiro.

Uma ``rsync`` operação de cópia padrão cria um ficheiro temporário e enche-o com dados. Se a transferência de dados for concluída com sucesso, o ficheiro temporário é renomeado para o nome de ficheiro original. Este método garante a consistência mesmo que os ficheiros sejam acedidos durante a cópia. Mas este método gera mais operações de escrita, o que retarda o movimento de ficheiros através da cache.

A ``--inplace`` opção escreve o novo ficheiro diretamente na sua localização final. Não é garantido que os ficheiros sejam consistentes durante a transferência, mas isso não é importante se estiver a preparar um sistema de armazenamento para ser utilizado mais tarde.

A ``rsync`` segunda operação serve como uma verificação de consistência na primeira operação. Como os ficheiros já foram copiados, a segunda fase é uma análise rápida para garantir que os ficheiros do destino correspondam aos ficheiros da fonte. Se algum ficheiro não corresponder, são recopiadas.

Pode emitir ambas as fases em conjunto num comando:

```bash
rsync -azh --inplace <source> <destination> && rsync -azh <source> <destination>
```

Este método é um método simples e eficaz para conjuntos de dados até ao número de ficheiros que o gestor interno de diretório pode lidar. (Isto é tipicamente 200 milhões de ficheiros para um cluster de 3 nós, 500 milhões de ficheiros para um cluster de seis nós, e assim por diante.)

## <a name="use-the-msrsync-utility"></a>Use a utilidade msrsync

A ``msrsync`` ferramenta também pode ser usada para mover dados para um ficheiro de núcleo de back-end para o cluster Avere. Esta ferramenta foi concebida para otimizar o ``rsync`` uso da largura de banda executando vários processos paralelos. Está disponível no GitHub em <https://github.com/jbd/msrsync>.

``msrsync``separa o diretório de origem em "baldes" separados e, em seguida, executa processos individuais ``rsync`` em cada balde.

Os testes preliminares utilizando um VM de quatro núcleos mostraram a melhor eficiência ao utilizar 64 processos. Use ``msrsync`` a ``-p`` opção para definir o número de processos para 64.

Também pode usar ``--inplace`` o ``msrsync`` argumento com comandos. Se utilizar esta opção, considere executar um segundo comando (como com [rsync](#use-a-two-phase-rsync-process), descrito acima) para garantir a integridade dos dados.

``msrsync``só pode escrever de e para os volumes locais. A fonte e o destino devem ser acessíveis como montados locais na rede virtual do cluster.

Para ``msrsync`` utilizar para povoar um volume de nuvem Azure com um cluster Avere, siga estas instruções:

1. Instalar ``msrsync`` e os seus pré-requisitos (rsync e Python 2.6 ou posterior)
1. Determine o número total de ficheiros e diretórios a copiar.

   Por exemplo, utilize o ``prime.py`` utilitário Avere com ```prime.py --directory /path/to/some/directory``` argumentos <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>(disponíveis através do download de url).

   Se não ``prime.py``estiver a utilizar, pode calcular o ``find`` número de itens com a ferramenta GNU da seguinte forma:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Divida o número de itens em 64 para determinar o número de itens por processo. Utilize este número ``-f`` com a opção de definir o tamanho dos baldes quando executar o comando.

1. Emita o ``msrsync`` comando para copiar ficheiros:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Se ``--inplace``utilizar, adicione uma segunda execução sem a opção de verificar se os dados estão corretamente copiados:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH> && msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Por exemplo, este comando foi concebido para mover 11.000 ficheiros em 64 processos de /teste/repositório de origem para /mnt/vfxt/repositório:

   ``msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository && msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository``

## <a name="use-the-parallel-copy-script"></a>Use o roteiro de cópia paralela

O ``parallelcp`` script também pode ser útil para mover dados para o armazenamento de back-end do seu cluster vFXT.

O script abaixo irá `parallelcp`adicionar o executável . (Este script foi concebido para Ubuntu; se ``parallel`` utilizar outra distribuição, tem de instalar separadamente.)

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

Este exemplo utiliza o script ``glibc`` de cópia paralela para compilar utilizando ficheiros de origem do cluster Avere.
<!-- xxx what is stored where? what is 'the avere cluster mount point'? xxx -->

Os ficheiros de origem são armazenados no ponto de montagem do cluster Avere, e os ficheiros do objeto são armazenados no disco rígido local.

Este script usa script de cópia paralela acima. A ``-j`` opção ``parallelcp`` é ``make`` usada com e para ganhar paralelização.

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
