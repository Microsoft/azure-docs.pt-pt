---
title: Azure HPC Cache data ingestão - script de cópia paralelo
description: Como utilizar um script de cópia paralelo para mover dados para um alvo de armazenamento blob em Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: ff7b15a36c5ef19a1fa6ffdca7697dd6ba97c29f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87092359"
---
# <a name="azure-hpc-cache-data-ingest---parallel-copy-script-method"></a>Azure HPC Cache data ingestão - método de script de cópia paralelo

Este artigo dá instruções para criar o ``parallelcp`` script e usá-lo para mover dados para um recipiente de armazenamento Blob para uso com cache Azure HPC.

Para saber mais sobre a mudança de dados para o armazenamento blob para o seu Cache Azure HPC, leia [os dados do Move para o armazenamento Azure Blob](hpc-cache-ingest.md).

## <a name="create-the-parallelcp-script"></a>Crie o script parallelcp

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

## <a name="parallel-copy-example"></a>Exemplo de cópia paralela

Este exemplo utiliza o script de cópia paralelo para compilar ``glibc`` utilizando ficheiros de origem na Cache Azure HPC.

Os ficheiros de origem estão em cache no ponto de montagem da Cache Azure HPC e os ficheiros de objetos são armazenados no disco rígido local.

Este exemplo utiliza o script de cópia paralelo com a opção ``-j`` e ``make`` para ganhar paralelização.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/cache1 hpccache
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
