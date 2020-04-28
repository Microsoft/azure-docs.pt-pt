---
title: Ingerir dados azure HPC Cache - roteiro de cópia paralela
description: Como usar um roteiro de cópia paralela para mover dados para um alvo de armazenamento Blob em Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 90e05ad3d42b1009b631630fe476669a9f418d33
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74166890"
---
# <a name="azure-hpc-cache-data-ingest---parallel-copy-script-method"></a>Ingerir dados azure HPC Cache - método de script de cópia paralela

Este artigo dá instruções ``parallelcp`` para criar o script e usá-lo para mover dados para um recipiente de armazenamento Blob para utilização com cache Azure HPC.

Para saber mais sobre a mudança de dados para o armazenamento Blob para o seu Cache Azure HPC, leia [os dados do Move para o armazenamento da Blob Azure](hpc-cache-ingest.md).

## <a name="create-the-parallelcp-script"></a>Criar o roteiro parallelcp

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

## <a name="parallel-copy-example"></a>Exemplo de cópia paralela

Este exemplo utiliza o script ``glibc`` de cópia paralela para compilar utilizando ficheiros de origem no Cache Azure HPC.

Os ficheiros de origem estão em cache no ponto de montagem azure HPC Cache, e os ficheiros do objeto são armazenados no disco rígido local.

Este exemplo utiliza o roteiro ``-j`` de ``make`` cópia paralela com a opção e para ganhar paralelização.

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
