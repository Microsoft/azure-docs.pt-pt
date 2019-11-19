---
title: Ingestão de dados do cache HPC do Azure – script de cópia paralela
description: Como usar um script de cópia paralela para mover dados para um destino de armazenamento de BLOBs no cache HPC do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 90e05ad3d42b1009b631630fe476669a9f418d33
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166890"
---
# <a name="azure-hpc-cache-data-ingest---parallel-copy-script-method"></a>Ingestão de dados do cache HPC do Azure – método de script de cópia paralela

Este artigo fornece instruções para criar o script de ``parallelcp`` e usá-lo para mover dados para um contêiner de armazenamento de BLOBs para uso com o cache do HPC do Azure.

Para saber mais sobre como mover dados para o armazenamento de BLOBs para o cache do Azure HPC, leia [mover dados para o armazenamento de BLOBs do Azure](hpc-cache-ingest.md).

## <a name="create-the-parallelcp-script"></a>Criar o script parallelcp

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

## <a name="parallel-copy-example"></a>Exemplo de cópia paralela

Este exemplo usa o script de cópia paralela para compilar ``glibc`` usando arquivos de origem no cache do HPC do Azure.

Os arquivos de origem são armazenados em cache no ponto de montagem do cache HPC do Azure e os arquivos de objeto são armazenados no disco rígido local.

Este exemplo usa o script de cópia paralela com a opção ``-j`` e ``make`` para obter paralelização.

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
