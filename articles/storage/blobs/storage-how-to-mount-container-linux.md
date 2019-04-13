---
title: Como montar o armazenamento de Blobs do Azure como um sistema de ficheiros no Linux | Documentos da Microsoft
description: Um contentor de armazenamento de Blobs do Azure com FUSE de montagem no Linux
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 2/1/2019
ms.author: seguler
ms.openlocfilehash: eadf52afd115eb1cb642082cea4b9f338bd44914
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521658"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Como montar o armazenamento de BLOBs como um sistema de ficheiros com blobfuse

## <a name="overview"></a>Descrição geral
[Blobfuse](https://github.com/Azure/azure-storage-fuse) é um driver de sistema de arquivos virtual para o armazenamento de Blobs do Azure. Blobfuse permite-lhe aceder aos seus dados de BLOBs de bloco existente na sua conta de armazenamento através do sistema de ficheiros do Linux. Armazenamento de Blobs do Azure é um serviço de armazenamento de objeto e não tem um espaço de nomes hierárquico. Blobfuse fornece este espaço de nomes com o esquema do diretório virtual com a barra "/" como um delimitador.  

Este guia mostra-lhe como utilizar blobfuse e montar um contentor de armazenamento de BLOBs no Linux e aceda aos dados. Para saber mais sobre blobfuse, leia os detalhes na [o repositório de blobfuse](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> Blobfuse não garante a conformidade de POSIX 100% à medida que ele simplesmente traduz os pedidos para [APIs de REST do Blob](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api). Por exemplo, as operações de mudança de nome são atómicas em POSIX, mas não em blobfuse.
> Para obter uma lista completa das diferenças entre um sistema de arquivos nativos e blobfuse, visite [o repositório de código de origem blobfuse](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Instalar blobfuse no Linux
Binários de Blobfuse estão disponíveis na [os repositórios de software da Microsoft para Linux](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software) distribuições de Ubuntu e RHEL. Para instalar blobfuse nessas distribuições, configure um dos repositórios da lista. Também pode criar os binários do seguinte código de origem do [passos de instalação do armazenamento do Azure](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source) se não houver nenhum binários disponíveis para a sua distribuição.

Blobfuse suporta a instalação no Ubuntu 14.04 e 18.04 16.04. Execute este comando para se certificar de que tem um dessas versões implementadas:
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>Configurar o repositório do pacote Microsoft
Configurar o [repositório de pacotes do Linux para produtos Microsoft](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Por exemplo, numa distribuição do Enterprise Linux 6:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Da mesma forma, altere o URL para `.../rhel/7/...` para apontar para uma distribuição do Enterprise Linux 7.

Outro exemplo numa distribuição do Ubuntu 14.04:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Da mesma forma, altere o URL para `.../ubuntu/16.04/...` ou `.../ubuntu/18.04/...` para fazer referência a outra versão do Ubuntu.

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
Blobfuse proporciona um desempenho nativo semelhante ao exigir que um caminho temporário no sistema de arquivos da memória intermédia e armazenar em cache todos os ficheiros abertos. Para este caminho temporário, selecione o disco de elevado desempenho a maioria dos ou utilizar um ramdisk para um melhor desempenho. 

> [!NOTE]
> Blobfuse armazena todo o conteúdo do arquivo aberto no caminho temporário. Lembre-se de que tem espaço suficiente para acomodar abrir todos os ficheiros. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Opcional) Utilizar um ramdisk para o caminho temporário
O exemplo seguinte cria um ramdisk de 16 GB e um diretório para blobfuse. Escolha o tamanho com base nas suas necessidades. Este ramdisk permite blobfuse para abrir ficheiros até 16 GB de tamanho. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>Utilizar um SSD como um caminho temporário
No Azure, pode utilizar os discos efémeros (SSD) disponíveis nas suas VMs para fornecer uma memória intermédia de baixa latência para blobfuse. Distribuições do Ubuntu, este disco efêmero está montado em ' / mnt ". Distribuições do Red Hat e CentOS, o disco está montado em ' / mnt/recurso / ".

Certifique-se de que o utilizador tem acesso ao caminho temporário:
```bash
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Configurar as credenciais de conta de armazenamento
Blobfuse requer as suas credenciais para ser armazenado num arquivo de texto no seguinte formato: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```
O `accountName` é o prefixo para a sua conta de armazenamento - não é o URL completo.

Crie este ficheiro com:

```
touch ~/fuse_connection.cfg
```

Depois de criado e editasse este ficheiro, certifique-se restringir o acesso para que nenhum outro usuário pode lê-lo.
```bash
chmod 600 fuse_connection.cfg
```

> [!NOTE]
> Se tiver criado o ficheiro de configuração no Windows, certifique-se executar `dos2unix` para limpar e converter o ficheiro em formato Unix. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Crie um diretório vazio para a montagem
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Montar

> [!NOTE]
> Para obter uma lista completa das opções de montagem, consulte [o repositório de blobfuse](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

A montagem blobfuse, execute o seguinte comando com o utilizador. Este comando monta o contentor especificado em "/ path/to/fuse_connection.cfg' para a localização ' / mycontainer".

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Agora, deve ter acesso para blobs de blocos através de APIs do sistema de ficheiros normais. O utilizador que monta o diretório é a única pessoa que pode aceder aos mesmos, por predefinição, o que protege o acesso. Para permitir o acesso a todos os utilizadores, pode montá-através da opção ```-o allow_other```. 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Passos Seguintes

* [Home page do Blobfuse](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Problemas de blobfuse de relatório](https://github.com/Azure/azure-storage-fuse/issues) 

