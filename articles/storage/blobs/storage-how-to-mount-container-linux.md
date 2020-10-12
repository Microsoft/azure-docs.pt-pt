---
title: Como montar o armazenamento do Azure Blob como um sistema de ficheiros no Linux Microsoft Docs
description: Saiba como montar um recipiente de armazenamento Azure Blob com blobfuse, um controlador de sistema de ficheiros virtual no Linux.
author: rishabpoh
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.date: 2/1/2019
ms.author: ripohane
ms.reviewer: dineshm
ms.openlocfilehash: 297595c6c4a9c82c3d0293f2cea2db66ea9ca54a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89180410"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Como montar o armazenamento Blob como um sistema de ficheiros com blobfuse

## <a name="overview"></a>Descrição geral
[Blobfuse](https://github.com/Azure/azure-storage-fuse) é um controlador de sistema de ficheiros virtual para o armazenamento Azure Blob. O Blobfuse permite-lhe aceder aos dados de blob existentes na sua conta de armazenamento através do sistema de ficheiros Linux. Blobfuse usa o esquema de diretório virtual com o "/" de corte avançado como um delimiter.  

Este guia mostra-lhe como usar blobfuse, e montar um recipiente de armazenamento Blob no Linux e dados de acesso. Para saber mais sobre blobfuse, leia os detalhes no [repositório blobfuse.](https://github.com/Azure/azure-storage-fuse)

> [!WARNING]
> A Blobfuse não garante o cumprimento 100% posix, uma vez que simplesmente traduz pedidos em [APIs Blob REST](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api). Por exemplo, as operações de renome são atómicas em POSIX, mas não em blobfuse.
> Para obter uma lista completa de diferenças entre um sistema de ficheiros nativo e blobfuse, visite [o repositório de código fonte blobfuse](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Instale blobfuse no Linux
Os binários Blobfuse estão disponíveis nos [repositórios de software da Microsoft para a Linux](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software) para distribuição Ubuntu e RHEL. Para instalar blobfuse nessas distribuições, configuure um dos repositórios da lista. Também pode construir os binários a partir do código fonte seguindo os passos de instalação do [Azure Storage](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source) se não houver binários disponíveis para a sua distribuição.

Blobfuse suporta instalação em Ubuntu 14.04, 16.04 e 18.04. Executar este comando para se certificar de que tem uma dessas versões implantadas:
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>Configure o repositório do pacote da Microsoft
Configure o [Repositório de Pacotes Linux para produtos microsoft](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Como exemplo, numa distribuição da Enterprise Linux 6:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Da mesma forma, altere o URL `.../rhel/7/...` para apontar para uma distribuição Da Enterprise Linux 7.

Outro exemplo sobre uma distribuição Ubuntu 14.04:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Da mesma forma, altere o URL para `.../ubuntu/16.04/...` ou para referência outra versão `.../ubuntu/18.04/...` Ubuntu.

### <a name="install-blobfuse"></a>Instalar blobfuse

Numa distribuição Ubuntu/Debian:
```bash
sudo apt-get install blobfuse
```

Numa distribuição da Enterprise Linux:
```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Preparar para montagem
O Blobfuse fornece um desempenho nativo, exigindo um caminho temporário no sistema de ficheiros para tamponar e cache de quaisquer ficheiros abertos. Para este percurso temporário, escolha o disco mais performante ou use um ramdisk para melhor desempenho. 

> [!NOTE]
> Blobfuse armazena todos os conteúdos de ficheiros abertos no caminho temporário. Certifique-se de ter espaço suficiente para acomodar todos os ficheiros abertos. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Opcional) Use um ramdisk para o caminho temporário
O exemplo a seguir cria um ramdisk de 16 GB e um diretório para blobfuse. Escolha o tamanho com base nas suas necessidades. Este ramdisk permite que o blobfuse abra ficheiros até 16 GB de tamanho. 
```bash
sudo mkdir /mnt/ramdisk
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>Use um SSD como um caminho temporário
Em Azure, pode utilizar os discos efémeros (SSD) disponíveis nos seus VMs para fornecer um tampão de baixa latência para o blobfuse. Nas distribuições de Ubuntu, este disco efémero é montado em '/mnt'. Nas distribuições de Chapéu Vermelho e CentOS, o disco é montado em '/mnt/recurso/'.

Certifique-se de que o seu utilizador tem acesso ao caminho temporário:
```bash
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Configure as credenciais da sua conta de armazenamento
Blobfuse exige que as suas credenciais sejam armazenadas num ficheiro de texto no seguinte formato: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```
Este `accountName` é o prefixo para a sua conta de armazenamento - não o URL completo.

Crie este ficheiro utilizando:

```
touch ~/fuse_connection.cfg
```

Uma vez criado e editado este ficheiro, certifique-se de restringir o acesso para que nenhum outro utilizadores possa lê-lo.
```bash
chmod 600 fuse_connection.cfg
```

> [!NOTE]
> Se tiver criado o ficheiro de configuração no Windows, certifique-se de que corre `dos2unix` para higienizar e converter o ficheiro para o formato Unix. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Crie um diretório vazio para montagem
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Montar

> [!NOTE]
> Para obter uma lista completa das opções de montagem, verifique [o repositório blobfuse](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Para montar blobfuse, executar o seguinte comando com o seu utilizador. Este comando monta o recipiente especificado em '/caminho/para/fuse_connection.cfg' no local '/mycontainer'.

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Deverá agora ter acesso às suas bolhas de bloco através das APIs do sistema de ficheiros regulares. O utilizador que monta o diretório é a única pessoa que pode aceder ao mesmo, por padrão, o que garante o acesso. Para permitir o acesso a todos os utilizadores, pode montar através da opção ```-o allow_other``` . 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Passos seguintes

* [Página inicial blobfuse](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Reportar problemas blobfuse](https://github.com/Azure/azure-storage-fuse/issues) 

