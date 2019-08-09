---
title: Como montar o armazenamento de BLOBs do Azure como um sistema de arquivos no Linux | Microsoft Docs
description: Montar um contêiner de armazenamento de BLOBs do Azure com fusível no Linux
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 2/1/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 88002999baacf38b4afd40b574686457c48546e4
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845019"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Como montar o armazenamento de BLOBs como um sistema de arquivos com o blobfuse

## <a name="overview"></a>Descrição geral
[Blobfuse](https://github.com/Azure/azure-storage-fuse) é um driver de sistema de arquivos virtual para o armazenamento de BLOBs do Azure. O Blobfuse permite que você acesse seus dados de blob de blocos existentes em sua conta de armazenamento por meio do sistema de arquivos do Linux. Blobfuse usa o esquema de diretório virtual com a barra "/" como um delimitador.  

Este guia mostra como usar o blobfuse e montar um contêiner de armazenamento de BLOBs no Linux e acessar dados. Para saber mais sobre o blobfuse, leia os detalhes no [repositório do blobfuse](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> O Blobfuse não garante a conformidade de 100% POSIX, pois simplesmente converte solicitações em [APIs REST de blob](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api). Por exemplo, as operações de renomeação são atômicas em POSIX, mas não em blobfuse.
> Para obter uma lista completa das diferenças entre um sistema de arquivos nativo e o blobfuse, visite [o repositório de código-fonte do blobfuse](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Instalar o blobfuse no Linux
Os binários do Blobfuse estão disponíveis nos repositórios de [software da Microsoft para](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software) as distribuições do Linux para Ubuntu e RHEL. Para instalar o blobfuse nessas distribuições, configure um dos repositórios da lista. Você também pode criar os binários do código-fonte seguindo as [etapas de instalação do armazenamento do Azure](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source) se não houver binários disponíveis para sua distribuição.

O Blobfuse dá suporte à instalação no Ubuntu 14, 4, 16, 4 e 18, 4. Execute este comando para certificar-se de que você tem uma dessas versões implantadas:
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>Configurar o repositório de pacotes da Microsoft
Configure o [repositório de pacotes do Linux para produtos da Microsoft](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Por exemplo, em uma distribuição Enterprise Linux 6:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Da mesma forma, altere a `.../rhel/7/...` URL para para apontar para uma distribuição Enterprise Linux 7.

Outro exemplo em uma distribuição do Ubuntu 14, 4:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Da mesma forma, altere a `.../ubuntu/16.04/...` URL `.../ubuntu/18.04/...` para ou para fazer referência a outra versão do Ubuntu.

### <a name="install-blobfuse"></a>Instalar o blobfuse

Em uma distribuição do Ubuntu/Debian:
```bash
sudo apt-get install blobfuse
```

Em uma distribuição Enterprise Linux:
```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Preparar para montar
O Blobfuse fornece desempenho nativo, exigindo um caminho temporário no sistema de arquivos para armazenar em buffer e armazenar em cache os arquivos abertos. Para esse caminho temporário, escolha o disco de melhor desempenho ou use um ramdisk para obter o máximo de performance. 

> [!NOTE]
> O Blobfuse armazena todo o conteúdo do arquivo aberto no caminho temporário. Certifique-se de ter espaço suficiente para acomodar todos os arquivos abertos. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>Adicional Usar um ramdisk para o caminho temporário
O exemplo a seguir cria um ramdisk de 16 GB e um diretório para blobfuse. Escolha o tamanho com base nas suas necessidades. Esse ramdisk permite que o blobfuse Abra arquivos de até 16 GB de tamanho. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>Usar um SSD como um caminho temporário
No Azure, você pode usar os discos efêmeras (SSD) disponíveis em suas VMs para fornecer um buffer de baixa latência para blobfuse. Em distribuições do Ubuntu, esse disco efêmero é montado em '/mnt '. Nas distribuições Red Hat e CentOS, o disco é montado em '/mnt/Resource/'.

Verifique se o usuário tem acesso ao caminho temporário:
```bash
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Configurar suas credenciais de conta de armazenamento
O Blobfuse exige que suas credenciais sejam armazenadas em um arquivo de texto no seguinte formato: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```
O `accountName` é o prefixo para sua conta de armazenamento, não a URL completa.

Crie este arquivo usando:

```
touch ~/fuse_connection.cfg
```

Depois de criar e editar esse arquivo, certifique-se de restringir o acesso para que nenhum outro usuário possa lê-lo.
```bash
chmod 600 fuse_connection.cfg
```

> [!NOTE]
> Se você tiver criado o arquivo de configuração no Windows, certifique-se `dos2unix` de executar para limpar e converter o arquivo para o formato UNIX. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Criar um diretório vazio para montagem
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Montar

> [!NOTE]
> Para obter uma lista completa de opções de montagem, verifique [o repositório blobfuse](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Para montar o blobfuse, execute o comando a seguir com o usuário. Este comando monta o contêiner especificado em '/path/to/fuse_connection.cfg ' no local '/MyContainer '.

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Agora você deve ter acesso aos seus blobs de blocos por meio das APIs regulares do sistema de arquivos. O usuário que monta o diretório é a única pessoa que pode acessá-lo, por padrão, que protege o acesso. Para permitir o acesso a todos os usuários, você pode montar por ```-o allow_other```meio da opção. 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Passos Seguintes

* [home page Blobfuse](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Relatar problemas de blobfuse](https://github.com/Azure/azure-storage-fuse/issues) 

