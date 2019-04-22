---
title: Copiar ou mover dados para o armazenamento do Azure com AzCopy no Linux | Documentos da Microsoft
description: Utilize o AzCopy no Linux utilitário para mover ou copiar dados de ou para BLOBs e ficheiros de conteúdo. Copiar dados para o armazenamento do Azure de arquivos locais ou copiar dados em ou entre contas de armazenamento. Migre facilmente os dados ao armazenamento do Azure.
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 04/26/2018
ms.author: seguler
ms.subservice: common
ms.openlocfilehash: 0f87645537576f49ee04b823341acf8853798f88
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58882230"
---
# <a name="transfer-data-with-azcopy-on-linux"></a>Transferir dados com AzCopy no Linux

O AzCopy é um utilitário de linha de comandos concebido para copiar dados de/para armazenamento de Blobs do Microsoft Azure e o arquivo, usando comandos concebidos para otimizar o desempenho. Pode copiar dados entre um sistema de ficheiros e uma conta de armazenamento ou entre contas de armazenamento.  

Existem duas versões do AzCopy que pode baixar. AzCopy no Linux destina-se plataformas Linux, oferecendo opções da linha de comandos de estilo POSIX. [AzCopy no Windows](../storage-use-azcopy.md) oferece opções de linha de comandos de estilo do Windows. Este artigo aborda o AzCopy no Linux. 

> [!NOTE]  
> As dependências do .NET Core a partir da versão do AzCopy 7.2, são empacotadas com o pacote de AzCopy. Se usar a versão de 7,2 ou mais tarde, já não terá de instalar o .NET Core como um pré-requisito.

## <a name="download-and-install-azcopy"></a>Baixe e instale o AzCopy

### <a name="installation-on-linux"></a>Instalação no Linux

> [!NOTE]
> Poderá ter de instalar as dependências do .NET Core 2.1 realçadas desta [artigo de pré-requisitos do .NET Core](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) dependendo de sua distribuição. 
>
> Para as distribuições do RHEL 7, instale dependências ICU e libunwind: ```yum install -y libunwind icu```

Instalar o AzCopy no Linux (v7.2 ou posterior) é tão fácil quanto um pacote de tar a extrair e executar o script de instalação. 

**Distribuições baseadas no RHEL 6**: [ligação de transferência](https://aka.ms/downloadazcopylinuxrhel6)
```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinuxrhel6
tar -xf azcopy.tar.gz
sudo ./install.sh
```

**Todas as outras distribuições do Linux**: [ligação de transferência](https://aka.ms/downloadazcopylinux64)
```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
tar -xf azcopy.tar.gz
sudo ./install.sh
```

Pode remover os ficheiros extraídos depois de instalado o AzCopy no Linux. Em alternativa, se não tiver privilégios de Superutilizador também pode executar `azcopy` com o azcopy de script de shell na pasta extraída.

### <a name="alternative-installation-on-ubuntu"></a>Instalação alternativa no Ubuntu

**Ubuntu 14.04**

Adicionar origem de apt para o repositório de produto do Microsoft Linux e instalar o AzCopy:

```bash
sudo echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod/ trusty main" > azure.list
sudo cp ./azure.list /etc/apt/sources.list.d/
sudo apt-key adv --keyserver packages.microsoft.com --recv-keys EB3E94ADBE1229CF
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

**Ubuntu 16.04**

Adicionar origem de apt para o repositório de produto do Microsoft Linux e instalar o AzCopy:

```bash
echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod/ xenial main" > azure.list
sudo cp ./azure.list /etc/apt/sources.list.d/
sudo apt-key adv --keyserver packages.microsoft.com --recv-keys EB3E94ADBE1229CF
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

## <a name="writing-your-first-azcopy-command"></a>Escrever seu primeiro comando do AzCopy
A sintaxe básica para comandos do AzCopy é:

```azcopy
azcopy --source <source> --destination <destination> [Options]
```

Os exemplos seguintes demonstram vários cenários para copiar dados para e de Blobs do Microsoft Azure e ficheiros. Consulte o `azcopy --help` menu para obter uma explicação detalhada dos parâmetros utilizados em cada exemplo.

## <a name="blob-download"></a>Blob: Transferência
### <a name="download-single-blob"></a>Transferir blob único

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key> 
```

Se a pasta `/mnt/myfiles` não existir, o AzCopy cria-a e transfere `abc.txt` na nova pasta. 

### <a name="download-single-blob-from-secondary-region"></a>Transferir blob único na região secundária

```azcopy
azcopy \
    --source https://myaccount-secondary.blob.core.windows.net/mynewcontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
```

Tenha em atenção que tem de ter o armazenamento georredundante de acesso de leitura ativado.

### <a name="download-all-blobs"></a>Transferir todos os blobs

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Suponha que os blobs seguintes residem no contentor especificado:  

```
abc.txt
abc1.txt
abc2.txt
vd1/a.txt
vd1/abcd.txt
```

Após a operação de transferência, o diretório `/mnt/myfiles` inclui os seguintes ficheiros:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/vd1/a.txt
/mnt/myfiles/vd1/abcd.txt
```

Se não especificar opção `--recursive`, será transferido sem blob.

### <a name="download-blobs-with-specified-prefix"></a>Transferir blobs com o prefixo especificado

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "a" \
    --recursive
```

Suponha que os blobs seguintes residem no contentor especificado. Todos os blobs a partir do prefixo `a` são transferidos.

```
abc.txt
abc1.txt
abc2.txt
xyz.txt
vd1\a.txt
vd1\abcd.txt
```

Após a operação de transferência, a pasta `/mnt/myfiles` inclui os seguintes ficheiros:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
```

O prefixo aplica-se para o diretório virtual, o que constitui a primeira parte do nome do blob. No exemplo mostrado acima, o diretório virtual não corresponde ao prefixo especificado, pelo que não blob é baixado. Além disso, se a opção `--recursive` não for especificado, o AzCopy não transfere os blobs.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Definir a hora da última modificação dos ficheiros exportados para ser a mesma que os blobs de origem

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination "/mnt/myfiles" \
    --source-key <key> \
    --preserve-last-modified-time
```

Também pode excluir blobs da operação de transferência com base na respetiva hora da última modificação. Por exemplo, se quiser excluir cuja última hora de modificação de blobs é o mesmo ou a mais recente do que o ficheiro de destino, adicionar o `--exclude-newer` opção:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-newer
```

Ou se pretende excluir cuja última hora de modificação de blobs é o mesmo ou mais antigo do que o ficheiro de destino, adicionar o `--exclude-older` opção:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-older
```

## <a name="blob-upload"></a>Blob: Carregar
### <a name="upload-single-file"></a>Carregar arquivo único

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --dest-key <key>
```

Se o contentor de destino especificado não existir, o AzCopy cria-o e carrega o ficheiro para o mesmo.

### <a name="upload-single-file-to-virtual-directory"></a>Carregar arquivo único para o diretório virtual

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/vd/abc.txt \
    --dest-key <key>
```

Se o diretório virtual especificado não existir, o AzCopy carrega o ficheiro para incluir o diretório virtual no nome do blob (*por exemplo,*, `vd/abc.txt` no exemplo acima).

### <a name="redirect-from-stdin"></a>Redirecionamento de stdin

```azcopy
gzip myarchive.tar -c | azcopy \
    --destination https://myaccount.blob.core.windows.net/mycontainer/mydir/myarchive.tar.gz \
    --dest-key <key>
```

### <a name="upload-all-files"></a>Carregar todos os ficheiros

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive
```

Especificar a opção `--recursive` carrega o conteúdo do diretório especificado para o armazenamento de BLOBs recursivamente, que significa que todas as subpastas e respetivos ficheiros são também carregados. Por exemplo, suponha que os seguintes arquivos residem na pasta `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

Após a operação de carregamento, o contentor inclui os seguintes ficheiros:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

Quando a opção `--recursive` não for especificada, apenas os seguintes três ficheiros são carregados:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="upload-files-matching-specified-pattern"></a>Carregar ficheiros correspondentes padrão especificado

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "a*" \
    --recursive
```

Suponha que os seguintes arquivos residem na pasta `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/xyz.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

Após a operação de carregamento, o contentor inclui os seguintes ficheiros:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

Quando a opção `--recursive` não for especificado, o AzCopy ignora os ficheiros que estão em subdiretórios:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Especifique o tipo de conteúdo MIME de um blob de destino
Por predefinição, o AzCopy define o tipo de conteúdo de um blob de destino para `application/octet-stream`. No entanto, é possível especificar explicitamente o tipo de conteúdo através da opção `--set-content-type [content-type]`. Essa sintaxe define o tipo de conteúdo para todos os blobs numa operação de carregamento.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type "video/mp4"
```

Se a opção `--set-content-type` for especificado sem um valor, em seguida, o AzCopy define o tipo de conteúdo do ficheiro, de acordo com a extensão de ficheiro ou de cada blob.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type
```

### <a name="customizing-the-mime-content-type-mapping"></a>Personalizando o mapeamento de tipo de conteúdo MIME
AzCopy utiliza um ficheiro de configuração que contém um mapeamento de extensão de ficheiro para o tipo de conteúdo. Pode personalizar esse mapeamento e adicionar os pares de novo, conforme necessário. O mapeamento está localizado em  ```/usr/lib/azcopy/AzCopyConfig.json```

## <a name="blob-copy"></a>Blob: Copiar
### <a name="copy-single-blob-within-storage-account"></a>Copiar blob único na conta de armazenamento

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key> \
    --dest-key <key>
```

Quando copiar um blob sem – opção de cópia de sincronização, uma [cópia do lado do servidor](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operação é executada.

### <a name="copy-single-blob-across-storage-accounts"></a>Copiar blob único em contas de armazenamento

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

Quando copiar um blob sem – opção de cópia de sincronização, uma [cópia do lado do servidor](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operação é executada.

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>Copiar blob único na região secundária para a região primária

```azcopy
azcopy \
    --source https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1/abc.txt \
    --destination https://myaccount2.blob.core.windows.net/mynewcontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

Tenha em atenção que tem de ter o armazenamento georredundante de acesso de leitura ativado.

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>Copiar blob único e os respetivos instantâneos em contas de armazenamento

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/ \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --include-snapshot
```

Após a operação de cópia, o contentor de destino inclui o blob e os respetivos instantâneos. O contentor inclui o blob seguinte e os respetivos instantâneos:

```
abc.txt
abc (2013-02-25 080757).txt
abc (2014-02-21 150331).txt
```

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>Forma síncrona copiar blobs em contas de armazenamento
AzCopy por predefinição copia dados entre dois pontos de extremidade de armazenamento de forma assíncrona. Por conseguinte, a operação de cópia é executada em segundo plano com capacidade de reserva de largura de banda que não tem SLA em termos de velocidade um blob é copiado. 

O `--sync-copy` opção garante que a operação de cópia obtenha velocidade consistente. AzCopy efetua a cópia síncrona ao transferir os blobs para copiar a partir da origem especificada para a memória local e, em seguida, carregá-los para o destino de armazenamento de Blobs.

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "ab" \
    --sync-copy
```

`--sync-copy` pode gerar custos de saída adicionais quando comparados com a cópia assíncrona. A abordagem recomendada é usar esta opção numa VM do Azure, que está na mesma região que a sua conta de armazenamento de origem para evitar custos de saída.

## <a name="file-download"></a>Ficheiro: Transferência
### <a name="download-single-file"></a>Baixar arquivo único

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/myfolder1/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
```

Se a origem especificada é uma partilha de ficheiros do Azure, tem de especificar o nome de ficheiro exatas, optar por (*por exemplo,* `abc.txt`) para transferir um ficheiro único ou especificar a opção `--recursive` para transferir todos os ficheiros na recursivamente a partilha. Tentar especificar um padrão de ficheiro e a opção `--recursive` juntas resulta num erro.

### <a name="download-all-files"></a>Transferir todos os ficheiros

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Tenha em atenção que quaisquer pastas vazias não são transferidas.

## <a name="file-upload"></a>Ficheiro: Carregar
### <a name="upload-single-file"></a>Carregar arquivo único

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.file.core.windows.net/myfileshare/abc.txt \
    --dest-key <key>
```

### <a name="upload-all-files"></a>Carregar todos os ficheiros

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --recursive
```

Tenha em atenção que quaisquer pastas vazias não são carregadas.

### <a name="upload-files-matching-specified-pattern"></a>Carregar ficheiros correspondentes padrão especificado

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --include "ab*" \
    --recursive
```

## <a name="file-copy"></a>Ficheiro: Copiar
### <a name="copy-across-file-shares"></a>Copiar de partilhas de ficheiros

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Quando copiar um ficheiro em partilhas de ficheiros, um [cópia do lado do servidor](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operação é executada.

### <a name="copy-from-file-share-to-blob"></a>Copiar de partilha de ficheiros para o blob

```azcopy
azcopy \ 
    --source https://myaccount1.file.core.windows.net/myfileshare/ \
    --destination https://myaccount2.blob.core.windows.net/mycontainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Quando copiar um ficheiro de partilha de ficheiros para o blob, um [cópia do lado do servidor](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operação é executada.

### <a name="copy-from-blob-to-file-share"></a>Copiar do blob para a partilha de ficheiros

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/mycontainer/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Quando copiar um ficheiro do blob para a partilha de ficheiros, um [cópia do lado do servidor](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operação é executada.

### <a name="synchronously-copy-files"></a>Forma síncrona copiar ficheiros
Pode especificar o `--sync-copy` opção para copiar dados de armazenamento de ficheiros para o armazenamento de ficheiros, do armazenamento de ficheiros para o armazenamento de BLOBs e armazenamento de Blobs do armazenamento de ficheiros forma síncrona. O AzCopy é executado esta operação ao transferir os dados de origem à memória local e, em seguida, carregá-lo para o destino. Neste caso, aplica-se custos de saída padrão.

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive \
    --sync-copy
```

Ao copiar a partir do armazenamento de ficheiros para o armazenamento de BLOBs, o tipo de blob predefinido é o blob de blocos, o utilizador pode especificar a opção `--blob-type page` para alterar o tipo de blob de destino. Tipos disponíveis são `page | block | append`.

Tenha em atenção que `--sync-copy` pode gerar saída adicional comparando com cópia assíncrona de custos. A abordagem recomendada é usar esta opção numa VM do Azure, que está na mesma região que a sua conta de armazenamento de origem para evitar custos de saída.

## <a name="other-azcopy-features"></a>Outras funcionalidades do AzCopy
### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Apenas os dados de cópia que não existem no destino
O `--exclude-older` e `--exclude-newer` parâmetros permitem-lhe excluir recursos de origem mais antigo ou mais recente de ser copiado, respectivamente. Se pretender apenas copiar recursos de origem que não existem no destino, pode especificar ambos os parâmetros no comando do AzCopy:

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --exclude-older --exclude-newer

    --source /mnt/myfiles --destination http://myaccount.file.core.windows.net/myfileshare --dest-key <destkey> --recursive --exclude-older --exclude-newer

    --source http://myaccount.blob.core.windows.net/mycontainer --destination http://myaccount.blob.core.windows.net/mycontainer1 --source-key <sourcekey> --dest-key <destkey> --recursive --exclude-older --exclude-newer

### <a name="use-a-configuration-file-to-specify-command-line-parameters"></a>Utilizar um ficheiro de configuração para especificar os parâmetros da linha de comandos

```azcopy
azcopy --config-file "azcopy-config.ini"
```

Pode incluir quaisquer parâmetros de linha de comandos do AzCopy num ficheiro de configuração. AzCopy processa os parâmetros no ficheiro como se tivesse sido especificados na linha de comando, executando uma substituição direta com o conteúdo do ficheiro.

Suponha que um ficheiro de configuração com o nome `copyoperation`, que contém as seguintes linhas. Cada parâmetro do AzCopy pode ser especificado numa única linha.

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --quiet

ou na separação em linhas:

    --source http://myaccount.blob.core.windows.net/mycontainer
    --destination /mnt/myfiles
    --source-key<sourcekey>
    --recursive
    --quiet

AzCopy falha, se dividir o parâmetro por duas linhas, conforme mostrado aqui para o `--source-key` parâmetro:

    http://myaccount.blob.core.windows.net/mycontainer
    /mnt/myfiles
    --sourcekey
    <sourcekey>
    --recursive
    --quiet

### <a name="specify-a-shared-access-signature-sas"></a>Especifique uma assinatura de acesso partilhado (SAS)

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-sas <SAS1> \
    --dest-sas <SAS2>
```

Também pode especificar uma SAS no contentor do URI:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken \
    --destination /mnt/myfiles \
    --recursive
```

### <a name="journal-file-folder"></a>Pasta do ficheiro de diário
Sempre que emitir um comando para o AzCopy, ele verifica se um ficheiro de diário já existe na pasta predefinida ou se existe numa pasta que especificou através desta opção. Se o ficheiro de diário não existir em qualquer lugar, o AzCopy trata a operação como o novo e gera um novo ficheiro de diário.

Se existir o ficheiro de diário, o AzCopy verifica se a linha de comandos que entrada coincide com a linha de comandos no ficheiro de diário. Se as duas linhas de comando corresponderem, o AzCopy retoma a operação incompleta. Se eles forem iguais, o AzCopy pede-lhe optar por substituir o ficheiro de diário para iniciar uma nova operação ou cancelar a operação atual.

Se pretender utilizar a localização predefinida para o ficheiro de diário:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --resume
```

Se omitir a opção `--resume`, ou especificar a opção `--resume` sem o caminho da pasta, conforme mostrado acima, o AzCopy cria o ficheiro de diário na localização predefinida, que é `~\Microsoft\Azure\AzCopy`. Se já existir um ficheiro de diário, em seguida, o AzCopy retoma a operação com base no ficheiro de diário.

Se pretender especificar uma localização personalizada para o ficheiro de diário:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key key \
    --resume "/mnt/myjournal"
```

Este exemplo cria o ficheiro de diário, caso ainda não exista. Se existir, o AzCopy retoma a operação com base no ficheiro de diário.

Se quiser retomar uma operação de AzCopy, repita o mesmo comando. AzCopy no Linux, em seguida, irá solicitar a confirmação:

```azcopy
Incomplete operation with same command line detected at the journal directory "/home/myaccount/Microsoft/Azure/AzCopy", do you want to resume the operation? Choose Yes to resume, choose No to overwrite the journal to start a new operation. (Yes/No)
```

### <a name="output-verbose-logs"></a>Saída de registos verbosos

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --verbose
```

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Especifique o número de operações simultâneas para começar
Opção `--parallel-level` Especifica o número de operações de cópia em simultâneo. Por predefinição, o AzCopy é iniciado um determinado número de operações simultâneas para aumentar o débito de transferência de dados. O número de operações simultâneas é igual oito vezes o número de processadores que tiver. Se estiver a executar o AzCopy numa rede de baixa largura de banda, pode especificar um número inferior para nível paralelo-- para evitar a falha causada pela concorrência de recursos.

>[!TIP]
>Para ver a lista completa de parâmetros de AzCopy, confira "azcopy – ajuda" menu.

## <a name="installation-steps-for-azcopy-71-and-earlier-versions"></a>Passos de instalação para o AzCopy 7.1 e versões anteriores

AzCopy no Linux (v7.1 e apenas anteriormente) requer o .NET Core framework. Estão disponíveis em instruções de instalação do [instalação do .NET Core](https://www.microsoft.com/net/core#linuxubuntu) página.

Por exemplo, comece por instalar o .NET Core no Ubuntu 16.10. Para obter o guia de instalação mais recente, visite [.NET Core no Linux](https://www.microsoft.com/net/core#linuxubuntu) página de instalação.


```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ yakkety main" > /etc/apt/sources.list.d/dotnetdev.list' 
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
sudo apt-get update
sudo apt-get install dotnet-sdk-2.0.0
```

Depois de ter instalado o .NET Core, transfira e instale o AzCopy.

```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopyprlinux
tar -xf azcopy.tar.gz
sudo ./install.sh
```

Pode remover os ficheiros extraídos depois de instalado o AzCopy no Linux. Em alternativa se não tiver privilégios de Superutilizador, também pode executar `azcopy` com o azcopy de script de shell na pasta extraída.

## <a name="known-issues-and-best-practices"></a>Problemas conhecidos e práticas recomendadas
### <a name="error-installing-azcopy"></a>Erro ao instalar o AzCopy
Se tiver problemas com a instalação do AzCopy, tente executar o AzCopy com o script de bash no extraídos `azcopy` pasta.

```bash
cd azcopy
./azcopy
```

### <a name="limit-concurrent-writes-while-copying-data"></a>Limitar as escritas simultâneas ao copiar dados
Quando copiar blobs ou ficheiros com o AzCopy, tenha em atenção que outra aplicação pode modificar os dados enquanto estiver a copiar. Se possível, certifique-se de que os dados que está a copiar não está a ser modificados durante a operação de cópia. Por exemplo, ao copiar um VHD associado a uma máquina virtual do Azure, certifique-se de que nenhum outro aplicativo esteja atualmente escrevendo para o VHD. É uma boa maneira de fazer isso por leasing o recurso a ser copiados. Em alternativa, pode criar um instantâneo do VHD primeiro e, em seguida, copiar o instantâneo.

Se não pode impedir que outros aplicativos escrita em blobs ou arquivos enquanto eles estão a ser copiados, em seguida, tenha em atenção que quando que a tarefa é concluída, os recursos copiados já não podem ter paridade completa com os recursos de origem.

### <a name="running-multiple-azcopy-processes"></a>Vários AzCopy processos em execução
Pode executar vários processos de AzCopy num único cliente, fornecendo o que usar pastas de diário diferentes. Não é suportada a utilizar uma pasta de diário único para vários processos de AzCopy.

1º de processo:
```azcopy
azcopy \
    --source /mnt/myfiles1 \
    --destination https://myaccount.blob.core.windows.net/mycontainer/myfiles1 \
    --dest-key <key> \
    --resume "/mnt/myazcopyjournal1"
```

2ª processo:
```azcopy
azcopy \
    --source /mnt/myfiles2 \
    --destination https://myaccount.blob.core.windows.net/mycontainer/myfiles2 \
    --dest-key <key> \
    --resume "/mnt/myazcopyjournal2"
```

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre o Armazenamento do Azure e o AzCopy, veja os seguintes recursos:

### <a name="azure-storage-documentation"></a>Documentação do armazenamento do Azure:
* [Introdução ao Armazenamento do Azure](../storage-introduction.md)
* [Criar uma conta de armazenamento](../storage-create-storage-account.md)
* [Gerir blobs com o Explorador de Armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)
* [Using the Azure CLI with Azure Storage (Utilizar a CLI do Azure com o Armazenamento do Azure)](../storage-azure-cli.md)
* [Como utilizar o armazenamento de Blobs do C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Como utilizar o Armazenamento de blobs do Java](../blobs/storage-java-how-to-use-blob-storage.md)
* [Como utilizar o Armazenamento de blobs do Node.js](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Como utilizar o Armazenamento de blobs do Python](../blobs/storage-python-how-to-use-blob-storage.md)

### <a name="azure-storage-blog-posts"></a>Postagens de blog de armazenamento do Azure:
* [Apresentamos o AzCopy no Linux pré-visualização](https://azure.microsoft.com/blog/announcing-azcopy-on-linux-preview/)
* [Introdução ao pré-visualização de biblioteca do movimento de dados de armazenamento do Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Introdução ao copiar síncrona e tipo de conteúdo personalizado](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: Anunciamos a disponibilidade geral de 3.0 AzCopy e a versão do AzCopy 4.0 com a tabela e ficheiro de suporte de pré-visualização](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: Otimizado para cenários de cópia em grande escala](https://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Suporte para armazenamento georredundante com acesso de leitura](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy: Transferir dados com o modo reiniciável e o SAS token](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: Utilizar o Blob de cópia entre-conta](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: A carregar/transferir ficheiros para os Blobs do Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
