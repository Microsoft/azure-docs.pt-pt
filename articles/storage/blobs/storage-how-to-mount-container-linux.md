---
title: Como montar o armazenamento da Blob Azure como um sistema de ficheiros no Linux [ Microsoft Docs
description: Monte um recipiente de armazenamento Azure Blob com FUSE em Linux
author: rishabpoh
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 2/1/2019
ms.author: ripohane
ms.reviewer: dineshm
ms.openlocfilehash: a0a03df59bc6ecffcb4f0a701616297f2da78fdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061417"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Como montar o armazenamento blob como um sistema de ficheiros com blobfuse

## <a name="overview"></a>Descrição geral
[Blobfuse](https://github.com/Azure/azure-storage-fuse) é um controlador virtual do sistema de ficheiros para o armazenamento de Azure Blob. A Blobfuse permite-lhe aceder aos dados de blob de bloco existentes na sua conta de armazenamento através do sistema de ficheiros Linux. A Blobfuse utiliza o esquema de diretório virtual com o '/' de corte dianteiro como delimitador.  

Este guia mostra-lhe como usar blobfuse, e montar um recipiente de armazenamento Blob em Linux e aceder a dados. Para saber mais sobre blobfuse, leia os detalhes no [repositório blobfuse](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> A Blobfuse não garante o cumprimento 100% POSIX, uma vez que simplesmente traduz pedidos em [APIs de REPOUSO Blob](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api). Por exemplo, as operações de renome são atómicas em POSIX, mas não em blobfuse.
> Para obter uma lista completa das diferenças entre um sistema de ficheiros nativo e blobfuse, visite [o repositório de código fonte blobfuse](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Instale blobfuse no Linux
Os binários blobfuse estão disponíveis nos [repositórios](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software) de software da Microsoft para o Linux para distribuição ubuntu e RHEL. Para instalar blobfuse nessas distribuições, configure um dos repositórios da lista. Também pode construir os binários a partir do código fonte seguindo as etapas de instalação do [Armazenamento Azure](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source) se não houver binários disponíveis para a sua distribuição.

Blobfuse suporta instalação em Ubuntu 14.04, 16.04 e 18.04. Execute este comando para se certificar de que tem uma dessas versões implementadas:
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>Configure o repositório do pacote da Microsoft
Configure o [Repositório de Pacotes Linux para produtos Microsoft](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Como exemplo, numa distribuição enterprise Linux 6:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Da mesma forma, `.../rhel/7/...` altere o URL para apontar para uma distribuição Enterprise Linux 7.

Outro exemplo sobre uma distribuição Ubuntu 14.04:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Da mesma forma, `.../ubuntu/16.04/...` altere o URL para ou `.../ubuntu/18.04/...` para fazer referência a outra versão Ubuntu.

### <a name="install-blobfuse"></a>Instale blobfuse

Numa distribuição Ubuntu/Debian:
```bash
sudo apt-get install blobfuse
```

Numa distribuição enterprise Linux:
```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Preparar para a montagem
A Blobfuse proporciona um desempenho nativo, exigindo um caminho temporário no sistema de ficheiros para tampão e cache quaisquer ficheiros abertos. Para este percurso temporário, escolha o disco mais performante ou utilize um ramdisk para melhor desempenho. 

> [!NOTE]
> A Blobfuse armazena todos os conteúdos de ficheiros abertos no caminho temporário. Certifique-se de ter espaço suficiente para acomodar todos os ficheiros abertos. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Opcional) Use um ramdisk para o caminho temporário
O exemplo seguinte cria um ramdisk de 16 GB e um diretório para blobfuse. Escolha o tamanho com base nas suas necessidades. Este ramdisk permite que o blobfuse abra ficheiros até 16 GB de tamanho. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>Use um SSD como um caminho temporário
Em Azure, pode utilizar os discos efémeros (SSD) disponíveis nos seus VMs para fornecer um tampão de baixa latência para blobfuse. Nas distribuições de Ubuntu, este disco efémero é montado em '/mnt'. Nas distribuições Red Hat e CentOS, o disco é montado em '/mnt/recurso/'.

Certifique-se de que o utilizador tem acesso ao caminho temporário:
```bash
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Configure as credenciais da sua conta de armazenamento
A Blobfuse exige que as suas credenciais sejam armazenadas num ficheiro de texto no seguinte formato: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```
O `accountName` é o prefixo da sua conta de armazenamento - não o URL completo.

Criar este ficheiro utilizando:

```
touch ~/fuse_connection.cfg
```

Depois de ter criado e editado este ficheiro, certifique-se de restringir o acesso para que nenhum outro utilizador possa lê-lo.
```bash
chmod 600 fuse_connection.cfg
```

> [!NOTE]
> Se criou o ficheiro de configuração no `dos2unix` Windows, certifique-se de que corre para desinfetar e converter o ficheiro para o formato Unix. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Criar um diretório vazio para montagem
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Montar

> [!NOTE]
> Para obter uma lista completa de opções de montagem, verifique [o repositório blobfuse](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Para montar blobfuse, execute o seguinte comando com o utilizador. Este comando coloca o recipiente especificado em '//path/to/fuse_connection.cfg' no local '/mycontainer'.

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Deverá agora ter acesso às suas bolhas de bloco através do sistema de ficheiros regularAP. O utilizador que monta o diretório é a única pessoa que pode acessá-lo, por padrão, que assegura o acesso. Para permitir o acesso a todos os ```-o allow_other```utilizadores, pode montar através da opção . 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Passos seguintes

* [Página inicial de Blobfuse](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Reportar problemas blobfuse](https://github.com/Azure/azure-storage-fuse/issues) 

