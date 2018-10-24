---
title: Como montar o armazenamento de Blobs do Azure como um sistema de ficheiros no Linux | Documentos da Microsoft
description: Um contentor de armazenamento de Blobs do Azure com FUSE de montagem no Linux
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 10/11/2018
ms.author: seguler
ms.openlocfilehash: 50378fd7739567b0cc56066168ddd33c3ea14141
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957059"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Como montar o armazenamento de BLOBs como um sistema de ficheiros com blobfuse

## <a name="overview"></a>Descrição geral
[Blobfuse](https://github.com/Azure/azure-storage-fuse) é um driver de sistema de arquivos virtual para armazenamento de Blobs do Azure, que permite-lhe aceder aos seus dados de BLOBs de bloco existente na sua conta de armazenamento através do sistema de ficheiros do Linux. Armazenamento de Blobs do Azure é um serviço de armazenamento de objeto e, portanto, não tem um espaço de nomes hierárquico. Blobfuse fornece este espaço de nomes com o esquema do diretório virtual com o uso de barra "/" como um delimitador.  

Este guia mostra-lhe como utilizar blobfuse e montar um contentor de armazenamento de BLOBs no Linux e aceda aos dados. Para saber mais sobre blobfuse, leia os detalhes na [o repositório de blobfuse](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> Blobfuse não garante a conformidade de POSIX 100% à medida que ele simplesmente traduz os pedidos para [APIs de REST do Blob](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api). Por exemplo, as operações de mudança de nome são atómicas em POSIX, mas não em blobfuse.
> Para obter uma lista completa das diferenças entre um sistema de arquivos nativos e blobfuse, visite [o repositório de código de origem blobfuse](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Instalar blobfuse no Linux
Binários de Blobfuse estão disponíveis na [os repositórios de software da Microsoft para Linux](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software) distribuições de Ubuntu e RHEL. Para instalar o blobfuse nessas distribuições, configure um dos repositórios da lista. Também pode criar os binários do código-fonte seguindo os passos de instalação [aqui](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source) se não houver nenhum binários disponíveis para a sua distribuição.

### <a name="configure-the-microsoft-package-repository"></a>Configurar o repositório do pacote Microsoft
Configurar o [repositório de pacotes do Linux para produtos Microsoft](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Por exemplo, numa distribuição do Enterprise Linux 6:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Da mesma forma, altere o url para `.../rhel/7/...` para apontar para uma distribuição do Enterprise Linux 7.

Outro exemplo num 14.04 Ubuntu:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Da mesma forma, altere o url para `.../ubuntu/16.04/...` para apontar para uma distribuição do Ubuntu 16.04.

### <a name="install-blobfuse"></a>Instalar blobfuse

Numa distribuição Ubuntu/Debian:
```bash
sudo apt-get install blobfuse
```

Numa distribuição Linux empresarial:
```bash
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Preparar para a montagem
Blobfuse requer um caminho temporário no sistema de arquivos da memória intermédia e armazenar em cache todos os ficheiros abertos, que ajuda a proporciona um desempenho nativo semelhantes. Para este caminho temporário, selecione o disco de elevado desempenho a maioria dos ou utilizar um ramdisk para um melhor desempenho. 

> [!NOTE]
> Blobfuse armazena todo o conteúdo do arquivo aberto no caminho temporário. Lembre-se de que tem espaço suficiente para acomodar abrir todos os ficheiros. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Opcional) Utilizar um ramdisk para o caminho temporário
O exemplo seguinte cria um ramdisk de 16 GB, bem como criar um diretório para blobfuse. Escolha o tamanho com base nas suas necessidades. Este ramdisk permite blobfuse para abrir ficheiros até 16 GB de tamanho. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-for-temporary-path"></a>Utilizar um SSD para o caminho temporário
No Azure, pode utilizar os discos efémeros (SSD) disponíveis nas suas VMs para fornecer uma memória intermédia de baixa latência para blobfuse. Distribuições do Ubuntu, este disco efêmero está montado em ' / mnt ", ao passo que este está montado em ' / mnt/recurso /" em distribuições Red Hat e CentOS.

Certifique-se de que o utilizador tem acesso ao caminho temporário:
```bash
sudo mkdir /mnt/resource/blobfusetmp
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Configurar as credenciais de conta de armazenamento
Blobfuse requer as suas credenciais para ser armazenado num arquivo de texto no seguinte formato: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```

Depois de criar este ficheiro, certifique-se restringir o acesso para que nenhum outro utilizador pode lê-lo.
```bash
chmod 700 fuse_connection.cfg
```

> [!NOTE]
> Se tiver criado o ficheiro de configuração no Windows, certifique-se executar `dos2unix` para limpar e converter para o formato Unix. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Crie um diretório vazio para a montagem
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Montagem

> [!NOTE]
> Para obter uma lista completa das opções de montagem, consulte [o repositório de blobfuse](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Por ordem para a montagem blobfuse, execute o seguinte comando com o utilizador. Este comando monta o contentor especificado em "/ path/to/fuse_connection.cfg' para a localização ' / mycontainer".

```bash
blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Agora, deve ter acesso para blobs de blocos através de APIs do sistema de ficheiros normais. Tenha em atenção que o diretório montado só pode ser acedido pelo utilizador montar, que protege o acesso. Se pretender permitir o acesso a todos os utilizadores, poderá montar através da opção ```-o allow_other```. 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Passos Seguintes

* [Home page do Blobfuse](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Problemas de blobfuse de relatório](https://github.com/Azure/azure-storage-fuse/issues) 

