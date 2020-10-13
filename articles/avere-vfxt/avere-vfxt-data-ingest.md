---
title: Dados de mudança para Avere vFXT para Azure
description: Como adicionar dados a um novo volume de armazenamento para uso com o Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: 76bbe60397ebb01aed5694d933b3067f778a4c21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85505601"
---
# <a name="moving-data-to-the-vfxt-cluster---parallel-data-ingest"></a>Mover dados para o cluster vFXT - Ingestão de dados paralelos

Depois de criar um novo cluster vFXT, a sua primeira tarefa poderá ser mover dados para um novo volume de armazenamento em Azure. No entanto, se o seu método habitual de movimentação de dados estiver a emitir um simples comando de cópia de um cliente, provavelmente verá um desempenho de cópia lenta. A cópia de um único roscado não é uma boa opção para copiar dados para o armazenamento de back-end do cluster Avere vFXT.

Como o cluster Avere vFXT para Azure é uma cache multi-cliente escalável, a forma mais rápida e eficiente de copiar dados para ele é com vários clientes. Esta técnica paraleliza a ingestão dos ficheiros e objetos.

![Diagrama que mostra movimento de dados multi-clientes e multi-threaded: Na parte superior esquerda, um ícone para armazenamento de hardware no local tem várias setas provenientes dele. As setas apontam para quatro máquinas de clientes. De cada máquina cliente três setas apontam para o Avere vFXT. Do Avere vFXT, várias setas apontam para o armazenamento blob.](media/avere-vfxt-parallel-ingest.png)

Os ``cp`` ``copy`` comandos ou comandos que são normalmente utilizados para a transferência de dados de um sistema de armazenamento para outro são processos de rosca única que copiam apenas um ficheiro de cada vez. Isto significa que o servidor de ficheiros está a ingerir apenas um ficheiro de cada vez - o que é um desperdício dos recursos do cluster.

Este artigo explica estratégias para a criação de um sistema multi-cliente de cópia de ficheiros multi-threaded para mover dados para o cluster Avere vFXT. Explica conceitos de transferência de ficheiros e pontos de decisão que podem ser usados para copiar dados eficientes usando vários clientes e comandos de cópia simples.

Também explica alguns utilitários que podem ajudar. O ``msrsync`` utilitário pode ser usado para automatizar parcialmente o processo de dividir um conjunto de dados em baldes e usar ``rsync`` comandos. O ``parallelcp`` script é outro utilitário que lê o diretório de origem e emite comandos de cópia automaticamente. Além disso, a ``rsync`` ferramenta pode ser usada em duas fases para fornecer uma cópia mais rápida que ainda fornece consistência de dados.

Clique no link para saltar para uma secção:

* [Exemplo de cópia manual](#manual-copy-example) - Uma explicação completa usando comandos de cópia
* [Exemplo rsync em duas fases](#use-a-two-phase-rsync-process)
* [Exemplo parcialmente automatizado (msrsync)](#use-the-msrsync-utility)
* [Exemplo de cópia paralela](#use-the-parallel-copy-script)

## <a name="data-ingestor-vm-template"></a>Modelo VM do ingestor de dados

Um modelo de Gestor de Recursos está disponível no GitHub para criar automaticamente um VM com as ferramentas paralelas de ingestão de dados mencionadas neste artigo.

![Diagrama mostrando várias setas cada uma a partir de armazenamento de bolhas, armazenamento de hardware e fontes de ficheiros Azure. As setas apontam para um "data ingestor vm" e a partir daí, várias setas apontam para o Avere vFXT](media/avere-vfxt-ingestor-vm.png)

O data ingestor VM faz parte de um tutorial onde o recém-criado VM monta o cluster Avere vFXT e descarrega o seu script de botas do cluster. Leia [Bootstrap um dados ingeridos VM](https://github.com/Azure/Avere/blob/master/docs/data_ingestor.md) para obter mais detalhes.

## <a name="strategic-planning"></a>Planeamento estratégico

Ao desenhar uma estratégia para copiar dados em paralelo, deve entender as trocas no tamanho do ficheiro, contagem de ficheiros e profundidade de diretório.

* Quando os ficheiros são pequenos, a métrica de juros são ficheiros por segundo.
* Quando os ficheiros são grandes (10MiBi ou superior), a métrica de interesse é bytes por segundo.

Cada processo de cópia tem uma taxa de produção e uma taxa de transferência de ficheiros, que pode ser medida através do tempo do comando da cópia e factoring do tamanho do ficheiro e contagem de ficheiros. Explicar como medir as taxas está fora do âmbito deste documento, mas é importante perceber se vai lidar com ficheiros pequenos ou grandes.

## <a name="manual-copy-example"></a>Exemplo de cópia manual

Pode criar manualmente uma cópia multi-roscadas num cliente executando mais do que um comando de cópia ao mesmo tempo em segundo plano contra conjuntos de ficheiros ou caminhos predefinidos.

O comando Linux/UNIX ``cp`` inclui o argumento para preservar a propriedade e os ``-p`` metadados mtime. Adicionar este argumento aos comandos abaixo é opcional. (Adicionar o argumento aumenta o número de chamadas de sistema de ficheiros enviadas do cliente para o sistema de ficheiros de destino para modificação de metadados.)

Este exemplo simples copia dois ficheiros em paralelo:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Após a emissão deste comando, o `jobs` comando mostrará que dois fios estão em funcionamento.

### <a name="predictable-filename-structure"></a>Estrutura previsível do nome de ficheiro

Se os seus ficheiros forem previsíveis, pode utilizar expressões para criar fios de cópia paralelos.

Por exemplo, se o seu diretório contiver 1000 ficheiros numerados sequencialmente de `0001` , pode utilizar as `1000` seguintes expressões para criar dez fios paralelos que cada cópia de 100 ficheiros:

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

Se a sua estrutura de nomeação de ficheiros não for previsível, pode agrupar ficheiros por nomes de diretório.

Este exemplo recolhe diretórios inteiros para enviar para ``cp`` comandos executados como tarefas de fundo:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

Após a recolha dos ficheiros, pode executar comandos de cópia paralelos para copiar novamente as subdiretórios e todo o seu conteúdo:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

### <a name="when-to-add-mount-points"></a>Quando adicionar pontos de montagem

Depois de ter fios paralelos suficientes contra um único ponto de montagem do sistema de ficheiros de destino, haverá um ponto em que adicionar mais fios não dá mais produção. (A produção será medida em ficheiros/segundo ou bytes/segundo, dependendo do seu tipo de dados.) Ou pior, o excesso de rosca pode, por vezes, causar uma degradação da produção.

Quando isto acontece, pode adicionar pontos de montagem do lado do cliente a outros endereços IP do cluster vFXT, utilizando o mesmo caminho de montagem do sistema de ficheiros remoto:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

A adição de pontos de montagem do lado do cliente permite-lhe desviar comandos de cópia adicionais para os pontos de `/mnt/destination[1-3]` montagem adicionais, conseguindo um paralelismo adicional.

Por exemplo, se os seus ficheiros forem muito grandes, poderá definir os comandos de cópia para utilizar caminhos de destino distintos, enviando mais comandos em paralelo com o cliente que executa a cópia.

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

Por último, quando tiver atingido as capacidades do cliente, adicionar mais fios de cópia ou pontos de montagem adicionais não produzirá quaisquer ficheiros/seg adicionais ou aumentos de bytes/seg. Nessa situação, pode implantar outro cliente com o mesmo conjunto de pontos de montagem que estarão a executar os seus próprios conjuntos de processos de cópia de ficheiros.

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

Depois de compreender as abordagens acima (múltiplos fios de cópia por destino, múltiplos destinos por cliente, vários clientes por sistema de ficheiros de origem acessível à rede), considere esta recomendação: Construa manifestos de ficheiros e, em seguida, use-os com comandos de cópia em vários clientes.

Este cenário utiliza o comando UNIX ``find`` para criar manifestos de ficheiros ou diretórios:

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

Redirecione este resultado para um ficheiro: `find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Em seguida, pode iterar através do manifesto, usando comandos BASH para contar ficheiros e determinar os tamanhos das subdiretivas:

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

Por último, deve elaborar os comandos de cópia de ficheiros reais para os clientes.

Se tiver quatro clientes, utilize este comando:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Se tiver cinco clientes, use algo assim:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

E por seis... Extrapolar conforme necessário.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

Você *receberá* ficheiros N resultantes, um para cada um dos seus clientes *N* que tem os nomes do caminho para os diretórios de nível quatro obtidos como parte da saída do `find` comando.

Utilize cada ficheiro para construir o comando de cópia:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

O acima vai dar-lhe ficheiros *N,* cada um com um comando de cópia por linha, que pode ser executado como um script BASH no cliente.

O objetivo é executar vários fios destes scripts simultaneamente por cliente em paralelo em vários clientes.

## <a name="use-a-two-phase-rsync-process"></a>Use um processo rsync de duas fases

O utilitário padrão ``rsync`` não funciona bem para povoar o armazenamento em nuvem através do sistema Avere vFXT para Azure, porque gera um grande número de operações de criação de ficheiros e renomeação para garantir a integridade dos dados. No entanto, pode utilizar com segurança a ``--inplace`` opção ``rsync`` para saltar o procedimento de cópia mais cuidadoso se o seguir com uma segunda execução que verifica a integridade do ficheiro.

Uma operação de cópia padrão ``rsync`` cria um ficheiro temporário e preenche-o com dados. Se a transferência de dados for concluída com sucesso, o ficheiro temporário será renomeado para o nome de ficheiro original. Este método garante a consistência mesmo que os ficheiros sejam acedidos durante a cópia. Mas este método gera mais operações de escrita, o que retarda o movimento do ficheiro através da cache.

A opção ``--inplace`` escreve o novo ficheiro diretamente na sua localização final. Os ficheiros não são garantidos para serem consistentes durante a transferência, mas isso não é importante se estiver a preparar um sistema de armazenamento para ser utilizado mais tarde.

A segunda ``rsync`` operação serve como uma verificação de consistência na primeira operação. Como os ficheiros já foram copiados, a segunda fase é uma verificação rápida para garantir que os ficheiros do destino correspondam aos ficheiros da fonte. Se os ficheiros não corresponderem, são recopiados.

Pode emitir ambas as fases juntas num só comando:

```bash
rsync -azh --inplace <source> <destination> && rsync -azh <source> <destination>
```

Este método é um método simples e eficaz no tempo para conjuntos de dados até ao número de ficheiros que o gestor de diretório interno pode tratar. (Isto é tipicamente 200 milhões de ficheiros para um cluster de 3 nós, 500 milhões de ficheiros para um cluster de seis nós, e assim por diante.)

## <a name="use-the-msrsync-utility"></a>Use o utilitário msrsync

A ``msrsync`` ferramenta também pode ser usada para mover dados para um ficheiro de núcleo de back-end para o cluster Avere. Esta ferramenta foi concebida para otimizar o uso da largura de banda executando ``rsync`` vários processos paralelos. Está disponível no GitHub em <https://github.com/jbd/msrsync> .

``msrsync`` rompe o diretório de origem em "baldes" separados e, em seguida, executa processos individuais ``rsync`` em cada balde.

Os testes preliminares utilizando um VM de quatro núcleos mostraram a melhor eficiência quando utilizaram 64 processos. Utilize a ``msrsync`` opção ``-p`` para definir o número de processos para 64.

Também pode usar o ``--inplace`` argumento com ``msrsync`` comandos. Se utilizar esta opção, considere executar um segundo comando (como com [o rsync](#use-a-two-phase-rsync-process), descrito acima) para garantir a integridade dos dados.

``msrsync`` só pode escrever de e para os volumes locais. A fonte e o destino devem ser acessíveis como suportes locais na rede virtual do cluster.

Para utilizar ``msrsync`` para preencher um volume de nuvem azul com um cluster Avere, siga estas instruções:

1. Instale ``msrsync`` e os seus pré-requisitos (rsync e Python 2.6 ou posterior)
1. Determine o número total de ficheiros e diretórios a serem copiados.

   Por exemplo, utilize o utilitário Avere ``prime.py`` com argumentos ```prime.py --directory /path/to/some/directory``` (disponíveis por download de <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py> url).

   Se não ``prime.py`` utilizar, pode calcular o número de itens com a ferramenta GNU ``find`` da seguinte forma:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Divida o número de itens por 64 para determinar o número de itens por processo. Utilize este número com a ``-f`` opção de definir o tamanho dos baldes quando executar o comando.

1. Emita o ``msrsync`` comando para copiar ficheiros:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Se ``--inplace`` utilizar, adicione uma segunda execução sem a opção de verificar se os dados estão corretamente copiados:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH> && msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Por exemplo, este comando foi concebido para mover 11.000 ficheiros em 64 processos de /teste/repositório de fontes para /mnt/vfxt/repositório:

   ``msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository && msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository``

## <a name="use-the-parallel-copy-script"></a>Use o script de cópia paralelo

O ``parallelcp`` script também pode ser útil para mover dados para o armazenamento de back-end do seu cluster vFXT.

O roteiro abaixo irá adicionar o executável `parallelcp` . (Este script foi concebido para Ubuntu; se utilizar outra distribuição, deve instalar-se ``parallel`` separadamente.)

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

Este exemplo utiliza o script de cópia paralelo para compilar ``glibc`` utilizando ficheiros de origem do cluster Avere.
<!-- xxx what is stored where? what is 'the avere cluster mount point'? xxx -->

Os ficheiros de origem são armazenados no ponto de montagem do cluster Avere, e os ficheiros de objetos são armazenados no disco rígido local.

Este script usa script de cópia paralelo acima. A opção ``-j`` é usada com e para ganhar ``parallelcp`` ``make`` paralelização.

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
