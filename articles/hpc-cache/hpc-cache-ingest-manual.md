---
title: Azure HPC Cache dados ingerir - cópia manual
description: Como usar comandos da CP para mover dados para um alvo de armazenamento Blob em Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: f96a0fa264124f9d050e667b003d98579da63b77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87092342"
---
# <a name="azure-hpc-cache-data-ingest---manual-copy-method"></a>Azure HPC Cache data ingestão - método de cópia manual

Este artigo dá instruções detalhadas para copiar manualmente dados para um recipiente de armazenamento Blob para utilização com cache Azure HPC. Utiliza operações paralelas multi roscadas para otimizar a velocidade da cópia.

Para saber mais sobre a mudança de dados para o armazenamento blob para o seu Cache Azure HPC, leia [os dados do Move para o armazenamento Azure Blob](hpc-cache-ingest.md).

## <a name="simple-copy-example"></a>Exemplo de cópia simples

Pode criar manualmente uma cópia multi-roscadas num cliente executando mais do que um comando de cópia ao mesmo tempo em segundo plano contra conjuntos de ficheiros ou caminhos predefinidos.

O comando Linux/UNIX ``cp`` inclui o argumento para preservar a propriedade e os ``-p`` metadados mtime. Adicionar este argumento aos comandos abaixo é opcional. (Adicionar o argumento aumenta o número de chamadas do sistema de ficheiros enviadas do cliente para o sistema de ficheiros de destino para modificação de metadados.)

Este exemplo simples copia dois ficheiros em paralelo:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Após a emissão deste comando, o `jobs` comando mostrará que dois fios estão em funcionamento.

## <a name="copy-data-with-predictable-file-names"></a>Copiar dados com nomes de ficheiros previsíveis

Se os nomes dos seus ficheiros forem previsíveis, pode utilizar expressões para criar fios de cópia paralelos.

Por exemplo, se o seu diretório contiver 1000 ficheiros numerados sequencialmente de `0001` , pode utilizar as `1000` seguintes expressões para criar 10 fios paralelos que cada cópia de 100 ficheiros:

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

## <a name="copy-data-with-unstructured-file-names"></a>Copiar dados com nomes de ficheiros não estruturados

Se a estrutura de nomeação do seu ficheiro não for previsível, pode agrupar ficheiros por nomes de diretório.

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

## <a name="when-to-add-mount-points"></a>Quando adicionar pontos de montagem

Depois de ter fios paralelos suficientes contra um único ponto de montagem do sistema de ficheiros de destino, haverá um ponto em que adicionar mais fios não dá mais produção. (A produção será medida em ficheiros/segundo ou bytes/segundo, dependendo do seu tipo de dados.) Ou pior, o excesso de rosca pode, por vezes, causar uma degradação da produção.

Quando isto acontece, pode adicionar pontos de montagem do lado do cliente a outros endereços de montagem de cache Azure HPC, utilizando o mesmo caminho de montagem do sistema de ficheiros remotos:

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

## <a name="when-to-add-clients"></a>Quando adicionar clientes

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

## <a name="create-file-manifests"></a>Criar manifestos de ficheiros

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
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
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
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
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
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_hpccache_catchup
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
