---
title: Copiar ou mover dados para o armazenamento do Azure com AzCopy no Windows | Documentos da Microsoft
description: Utilize o AzCopy no utilitário de Windows para mover ou copiar dados de ou para o blob, tabela e conteúdo do ficheiro. Copiar dados para o armazenamento do Azure de arquivos locais ou copiar dados em ou entre contas de armazenamento. Migre facilmente os dados ao armazenamento do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/03/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: a91910d474b13fe8cc5306c53109575d2883224e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64572945"
---
# <a name="transfer-data-with-the-azcopy-on-windows"></a>Transferir dados com o AzCopy no Windows

O AzCopy é um utilitário de linha de comandos concebido para copiar dados de/para armazenamento de Blobs do Microsoft Azure, arquivo e tabela, usando comandos concebidos para otimizar o desempenho. Pode copiar dados entre um sistema de ficheiros e uma conta de armazenamento ou entre contas de armazenamento.  

> [!IMPORTANT]
> Este artigo descreve uma versão mais antiga do AzCopy.
>Para instalar a versão mais recente do AzCopy, veja [AzCopy v10](storage-use-azcopy-v10.md).

Se optar por instalar a versão mais antiga do AzCopy (AzCopy v8.1), em seguida, há várias versões que pode baixar. AzCopy no Windows oferece opções de linha de comandos de estilo do Windows. [AzCopy no Linux](storage-use-azcopy-linux.md) destina-se plataformas Linux, oferecendo opções da linha de comandos de estilo POSIX. Este artigo aborda o AzCopy no Windows.

## <a name="download-and-install-azcopy-v81-on-windows"></a>Transfira e instale o AzCopy (v8.1) no Windows

Transfira o [AzCopy (v8.1) no Windows](https://aka.ms/downloadazcopy).

#### <a name="azcopy-on-windows-81-release-notes"></a>Notas de versão do AzCopy no Windows 8.1

- Serviço tabela já não é suportado na versão mais recente. Se utilizar a funcionalidade de exportação de tabela, transfira a versão 7.3 do AzCopy.
- Criada com .NET Core 2.1 e todas as dependências do .NET Core agora são empacotadas na instalação.
- Foi adicionado suporte de autenticação OAuth. Utilize ```azcopy login``` para iniciar sessão com o Azure Active Directory.

### <a name="azcopy-with-table-support-v73"></a>Azcopy com o suporte de tabela (v7.3)

Transfira o [AzCopy 7.3 com suporte de tabela](https://aka.ms/downloadazcopynet).

### <a name="post-installation-step"></a>Após a instalação passo

Depois de instalar o AzCopy no Windows através do instalador, abra uma janela de comando e navegue para o diretório de instalação do AzCopy no seu computador - onde o `AzCopy.exe` executável está localizado. Se assim o desejar, pode adicionar a localização de instalação do AzCopy ao caminho do sistema. Por predefinição, o AzCopy é instalado para `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` ou `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`.

## <a name="writing-your-first-azcopy-command"></a>Escrever seu primeiro comando do AzCopy

A sintaxe básica para comandos do AzCopy é:

```azcopy
AzCopy /Source:<source> /Dest:<destination> [Options]
```

Os exemplos seguintes demonstram uma variedade de cenários para copiar dados para e de tabelas, ficheiros e Blobs do Microsoft Azure. Consulte a [AzCopy parâmetros](#azcopy-parameters) secção para obter uma explicação detalhada dos parâmetros utilizados em cada exemplo.

## <a name="download-blobs-from-blob-storage"></a>Transferir blobs de armazenamento de BLOBs

Vamos dar uma olhada em várias formas para transferir blobs com o AzCopy.

### <a name="download-a-single-blob"></a>Transferir um blob único

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"
```

Tenha em atenção que, se a pasta `C:\myfolder` não existir, o AzCopy cria-lo e a transferência `abc.txt` na nova pasta.

### <a name="download-a-single-blob-from-the-secondary-region"></a>Transferir um blob único da região secundária

```azcopy
AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"
```

Tenha em atenção que tem de ter o armazenamento georredundante de acesso de leitura ativado para aceder a região secundária.

### <a name="download-all-blobs-in-a-container"></a>Transferir todos os blobs num contentor

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S
```

Suponha que os blobs seguintes residem no contentor especificado:  

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

Após a operação de transferência, o diretório `C:\myfolder` inclui os seguintes ficheiros:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

Se não especificar opção `/S`, nenhum blobs são transferidos.

### <a name="download-blobs-with-a-specific-prefix"></a>Transferir blobs com um prefixo específico

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S
```

Suponha que os blobs seguintes residem no contentor especificado. Todos os blobs a partir do prefixo `a` são transferidos:

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

Após a operação de transferência, a pasta `C:\myfolder` inclui os seguintes ficheiros:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

O prefixo aplica-se para o diretório virtual, o que constitui a primeira parte do nome do blob. No exemplo mostrado acima, o diretório virtual não corresponde ao prefixo especificado, pelo que não está a ser transferido. Além disso, se a opção `/S` não for especificado, o AzCopy não transfere os blobs.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Definir a hora da última modificação dos ficheiros exportados para ser a mesma que os blobs de origem

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT
```

Também pode excluir blobs da operação de transferência com base na respetiva hora da última modificação. Por exemplo, se quiser excluir cuja última hora de modificação de blobs é o mesmo ou a mais recente do que o ficheiro de destino, adicionar o `/XN` opção:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN
```

Se pretende excluir cuja última hora de modificação de blobs é o mesmo ou mais antigo do que o ficheiro de destino, adicionar o `/XO` opção:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO
```

## <a name="upload-blobs-to-blob-storage"></a>Carregar blobs no armazenamento de BLOBs

Vamos dar uma olhada em várias formas de carregar blobs com o AzCopy.

### <a name="upload-a-single-blob"></a>Carregar um blob único

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"
```

Se o contentor de destino especificado não existir, o AzCopy cria-o e carrega o ficheiro para o mesmo.

### <a name="upload-a-single-blob-to-a-virtual-directory"></a>Carregar um blob único para um diretório virtual

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

Se o diretório virtual especificado não existir, o AzCopy carrega o ficheiro para incluir o diretório virtual em seu nome (*por exemplo,*, `vd/abc.txt` no exemplo acima).

### <a name="upload-all-blobs-in-a-folder"></a>Carregar todos os blobs numa pasta

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S
```

Especificar a opção `/S` carrega o conteúdo do diretório especificado para o armazenamento de BLOBs recursivamente, que significa que todas as subpastas e respetivos ficheiros são também carregados. Por exemplo, suponha que os seguintes arquivos residem na pasta `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Após a operação de carregamento, o contentor inclui os seguintes ficheiros:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Se não especificar opção `/S`, AzCopy não carrega recursivamente. Após a operação de carregamento, o contentor inclui os seguintes ficheiros:

    abc.txt
    abc1.txt
    abc2.txt

### <a name="upload-blobs-matching-a-specific-pattern"></a>Carregar blobs que correspondam um padrão específico

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S
```

Suponha que os seguintes arquivos residem na pasta `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Após a operação de carregamento, o contentor inclui os seguintes ficheiros:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Se não especificar opção `/S`, o AzCopy carrega apenas os blobs que não residem num diretório virtual:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Especifique o tipo de conteúdo MIME de um blob de destino

Por predefinição, o AzCopy define o tipo de conteúdo de um blob de destino para `application/octet-stream`. A partir da versão 3.1.0, é possível especificar explicitamente o tipo de conteúdo através da opção `/SetContentType:[content-type]`. Essa sintaxe define o tipo de conteúdo para todos os blobs numa operação de carregamento.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4
```

Se especificar `/SetContentType` sem um valor, o AzCopy define cada blob ou tipo de conteúdo do ficheiro, de acordo com a extensão de ficheiro.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType
```

## <a name="copy-blobs-in-blob-storage"></a>Copiar blobs no armazenamento de BLOBs

Vamos examinar as várias formas de copiar blobs a partir de uma localização para outra com o AzCopy.

### <a name="copy-a-single-blob-from-one-container-to-another-within-the-same-storage-account"></a>Copiar um blob único de um contentor para outro dentro da mesma conta de armazenamento 

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt
```

Quando copiar um blob numa conta de armazenamento, um [cópia do lado do servidor](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operação é executada.

### <a name="copy-a-single-blob-from-one-storage-account-to-another"></a>Copiar um único blob de uma conta de armazenamento para outra

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

Quando copiar um blob entre contas de armazenamento, um [cópia do lado do servidor](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operação é executada.

### <a name="copy-a-single-blob-from-the-secondary-region-to-the-primary-region"></a>Copiar um blob único a partir da região secundária para a região primária

```azcopy
AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

Tenha em atenção que tem de ter o armazenamento georredundante de acesso de leitura ativado para aceder ao armazenamento secundário.

### <a name="copy-a-single-blob-and-its-snapshots-from-one-storage-account-to-another"></a>Copiar um blob único e os respetivos instantâneos de uma conta de armazenamento para outra

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot
```

Após a operação de cópia, o contentor de destino inclui o blob e os respetivos instantâneos. Partindo do princípio de que o blob no exemplo acima tem dois instantâneos, o contentor inclui os BLOBs e os instantâneos que se seguem:

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### <a name="copy-all-blobs-in-a-container-to-another-storage-account"></a>Copiar todos os blobs num contentor para outra conta de armazenamento 

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 
/Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /S
```

Especificar a opção /S carrega o conteúdo do recursivamente contentor especificado. Ver [carregar todos os blobs numa pasta](#upload-all-blobs-in-a-folder) para obter mais informações e um exemplo.

### <a name="synchronously-copy-blobs-from-one-storage-account-to-another"></a>Forma síncrona copiar blobs de uma conta de armazenamento para outra

AzCopy por predefinição copia dados entre dois pontos de extremidade de armazenamento de forma assíncrona. Por conseguinte, a operação de cópia é executado em segundo plano com capacidade de reserva de largura de banda que não tem SLA em termos de como rapidamente um blob é copiado e AzCopy verifica periodicamente o estado de cópia até que a cópia é concluída ou falhada.

O `/SyncCopy` opção garante que a operação de cópia obtenha velocidade consistente. AzCopy efetua a cópia síncrona ao transferir os blobs para copiar a partir da origem especificada para a memória local e, em seguida, carregá-los para o destino de armazenamento de Blobs.

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy
```

`/SyncCopy` pode gerar custos de saída adicionais quando comparados com a cópia assíncrona, a abordagem recomendada é usar esta opção numa VM do Azure que está na mesma região que a sua conta de armazenamento de origem para evitar custos de saída.

## <a name="download-files-from-file-storage"></a>Transferir os ficheiros do armazenamento de ficheiros

Vamos dar uma olhada em várias formas de transferir os ficheiros com o AzCopy.

### <a name="download-a-single-file"></a>Transferir um ficheiro único

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt
```

Se a origem especificada é uma partilha de ficheiros do Azure, tem de especificar o nome de ficheiro exatas, optar por (*por exemplo,* `abc.txt`) para transferir um ficheiro único ou especificar a opção `/S` para transferir todos os ficheiros na recursivamente a partilha. Tentar especificar um padrão de ficheiro e a opção `/S` juntas resulta num erro.

### <a name="download-all-files-in-a-directory"></a>Transferir todos os ficheiros num diretório

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S
```

Tenha em atenção que as pastas vazias não são transferidas.

## <a name="upload-files-to-an-azure-file-share"></a>Carregar ficheiros para uma partilha de ficheiros do Azure

Vamos dar uma olhada em várias formas de carregar ficheiros com o AzCopy.

### <a name="upload-a-single-file"></a>Carregar um único ficheiro

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt
```

### <a name="upload-all-files-in-a-folder"></a>Carregar todos os ficheiros numa pasta

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S
```

Tenha em atenção que as pastas vazias não são carregadas.

### <a name="upload-files-matching-a-specific-pattern"></a>Carregar ficheiros que correspondem um padrão específico

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S
```

## <a name="copy-files-in-file-storage"></a>Copiar ficheiros no armazenamento de ficheiros

Vamos dar uma olhada em várias formas de copiar os ficheiros numa partilha de ficheiros do Azure com o AzCopy.

### <a name="copy-from-one-file-share-to-another"></a>Copiar de uma partilha de ficheiros para outra

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S
```
Quando copiar um ficheiro em partilhas de ficheiros, um [cópia do lado do servidor](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operação é executada.

### <a name="copy-from-an-azure-file-share-to-blob-storage"></a>Copiar de uma partilha de ficheiros do Azure para armazenamento de BLOBs

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S
```
Quando copiar um ficheiro de partilha de ficheiros para o blob, um [cópia do lado do servidor](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operação é executada.

### <a name="copy-a-blob-from-blob-storage-to-an-azure-file-share"></a>Copiar um blob de armazenamento de BLOBs para uma partilha de ficheiros do Azure

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S
```
Quando copiar um ficheiro a partir de um blob para uma partilha de ficheiros, um [cópia do lado do servidor](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) operação é executada.

### <a name="synchronously-copy-files"></a>Forma síncrona copiar ficheiros

Pode especificar o `/SyncCopy` a opção de copiar dados do armazenamento de ficheiros para o armazenamento de ficheiros, do armazenamento de ficheiros para o armazenamento de BLOBs e de armazenamento de BLOBs para o armazenamento de arquivo de forma síncrona, o AzCopy fá-lo ao transferir os dados de origem para a memória local e carregá-lo novamente ao destino. Aplica-se em custos de saída padrão.

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy
```

Ao copiar a partir do armazenamento de ficheiros para o armazenamento de BLOBs, o tipo de blob predefinido é de blob de blocos o utilizador pode especificar a opção `/BlobType:page` para alterar o tipo de blob de destino.

Tenha em atenção que `/SyncCopy` pode gerar os custos de saída adicionais em comparação comparados a cópia assíncrona. A abordagem recomendada é usar esta opção na VM do Azure que se encontra na mesma região que a sua conta de armazenamento de origem para evitar custos de saída.

## <a name="export-data-from-table-storage"></a>Exportar os dados do armazenamento de tabelas

Vamos dar uma olhada na exportação de dados do armazenamento de tabelas do Azure com o AzCopy.

### <a name="export-a-table"></a>Exportar uma tabela

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key
```

AzCopy escreve um arquivo de manifesto para a pasta de destino especificado. O ficheiro de manifesto é utilizado no processo de importação para localizar os ficheiros de dados necessários e efetuar a validação de dados. O ficheiro de manifesto utiliza a seguinte convenção de nomenclatura, por predefinição:

    <account name>_<table name>_<timestamp>.manifest

Utilizador também pode especificar a opção `/Manifest:<manifest file name>` para definir o nome de ficheiro de manifesto.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest
```

### <a name="split-an-export-from-table-storage-into-multiple-files"></a>Dividir uma exportação de armazenamento de tabelas em vários arquivos

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100
```

AzCopy utiliza um *índice de volume* em nomes de ficheiros de dados split para distinguir os vários ficheiros. O índice de volume consiste em duas partes, um *índice do intervalo da chave de partição* e uma *índice do arquivo de divisão*. Ambos os índices são baseado em zero.

O índice do intervalo da chave de partição é 0 se o utilizador não especificar a opção `/PKRS`.

Por exemplo, suponha que AzCopy gera dois arquivos de dados depois do usuário especificar a opção `/SplitSize`. Os nomes de arquivo de dados resultante podem ser:

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Tenha em atenção que o mínimo possível de valor para a opção `/SplitSize` é de 32 MB. Se o destino especificado é o armazenamento de BLOBs, o AzCopy divide o ficheiro de dados assim que seus tamanhos de atingir o limite de tamanho do blob (200GB), independentemente de se opção `/SplitSize` foi especificado pelo utilizador.

### <a name="export-a-table-to-json-or-csv-data-file-format"></a>Exportar uma tabela para o formato de arquivo de dados JSON ou CSV

Por predefinição, o AzCopy exporta as tabelas para ficheiros de dados JSON. Pode especificar a opção `/PayloadFormat:JSON|CSV` para exportar as tabelas como JSON ou CSV.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV
```

Ao especificar o formato do payload CSV, o AzCopy também gera um ficheiro de esquema com a extensão de ficheiro `.schema.csv` para cada ficheiro de dados.

### <a name="export-table-entities-concurrently"></a>Exportar as entidades da tabela em simultâneo

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"
```

O AzCopy é iniciado operações simultâneas para exportar a entidades, quando o usuário especificar a opção `/PKRS`. Cada operação exporta um intervalo de chaves de partição.

Tenha em atenção que o número de operações simultâneas também é controlado pela opção `/NC`. AzCopy utiliza o número de processadores de núcleo, como o valor predefinido `/NC` ao copiar as entidades da tabela, mesmo se `/NC` não foi especificado. Quando o usuário especificar a opção `/PKRS`, AzCopy utiliza o menor dos dois valores – intervalos de chaves de partição versus implícita ou explicitamente especificado operações simultâneas - para determinar o número de operações simultâneas para iniciar. Para obter mais detalhes, escreva `AzCopy /?:NC` na linha de comandos.

### <a name="export-a-table-to-blob-storage"></a>Exportar uma tabela para o armazenamento de BLOBs

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2
```

AzCopy gera um ficheiro de dados JSON para o contentor de Blobs com a seguir a Convenção de nomenclatura:

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

O arquivo de dados JSON gerado segue o formato do payload de metadados mínimo. Para obter detalhes sobre este formato de payload, consulte [formato de Payload para operações de serviço de tabela](https://msdn.microsoft.com/library/azure/dn535600.aspx).

Tenha em atenção que, ao exportar tabelas para blobs, AzCopy transfere as entidades da tabela para ficheiros de dados temporário local e, em seguida, carrega essas entidades para o blob. Esses arquivos de dados temporários são colocados na pasta do ficheiro de diário, com o caminho predefinido "<code>%LocalAppData%\Microsoft\Azure\AzCopy</code>", pode especificar a opção/z: [pasta de ficheiros de diário] para o diário de alterações a localização da pasta de ficheiros e, portanto, alterar a localização de ficheiros de dados temporário. Os tamanho dos ficheiros é decidido por tamanho de entidades da sua tabela e o tamanho especificado com a opção /SplitSize, embora o arquivo de dados temporários no disco local é eliminado de forma instantânea depois de ter sido carregado para o blob de dados temporários Certifique-se de que tem suficiente locais espaço em disco para armazenar esses arquivos de dados temporário antes de serem eliminados.

## <a name="import-data-into-table-storage"></a>Importar dados para o armazenamento de tabelas

Vamos dar uma olhada na importação de dados para o armazenamento de tabelas do Azure com o AzCopy.

### <a name="import-a-table"></a>Importar uma tabela

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

A opção `/EntityOperation` indica como inserir entidades na tabela. Os valores possíveis são:

* `InsertOrSkip`: Ignora a uma entidade existente ou inserir uma nova entidade se não existir na tabela.
* `InsertOrMerge`: Intercala uma entidade existente ou inserir uma nova entidade se não existir na tabela.
* `InsertOrReplace`: Substitui uma entidade existente ou inserir uma nova entidade se não existir na tabela.

Tenha em atenção que não é possível especificar a opção `/PKRS` no cenário de importação. Ao contrário do cenário de exportação, em que tem de especificar a opção `/PKRS` para começar a operações simultâneas, o AzCopy inicia o operações simultâneas por predefinição quando importar uma tabela. O número predefinido de operações simultâneas iniciado é igual ao número de processadores de núcleo No entanto, pode especificar um número diferente de em simultâneo com a opção `/NC`. Para obter mais detalhes, escreva `AzCopy /?:NC` na linha de comandos.

Tenha em atenção que o AzCopy suporta apenas a importar para JSON, CSV não. AzCopy não suporta as importações de tabela de JSON criados pelo utilizador e os arquivos de manifesto. Ambos os ficheiros devem provir de uma exportação de tabela do AzCopy. Para evitar erros,. não modificar o JSON exportado ou arquivo de manifesto.

### <a name="import-entities-into-a-table-from-blob-storage"></a>Importar entidades para uma tabela de armazenamento de BLOBs

Suponha que um contentor de Blob contém o seguinte: Um ficheiro JSON que representa uma tabela do Azure e o arquivo de manifesto que acompanha este artigo.

    myaccount_mytable_20140103T112020.manifest
    myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

Pode executar o seguinte comando para importar entidades para uma tabela com o arquivo de manifesto nesse contentor de BLOBs:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"
```

## <a name="other-azcopy-features"></a>Outras funcionalidades do AzCopy

Vamos dar uma olhada em alguns outros recursos de AzCopy.

### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Apenas os dados de cópia que não existem no destino

O `/XO` e `/XN` parâmetros permitem-lhe excluir recursos de origem mais antigo ou mais recente de ser copiado, respectivamente. Se pretender apenas copiar recursos de origem que não existem no destino, pode especificar ambos os parâmetros no comando do AzCopy:

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

    /Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

Tenha em atenção que isto não é suportado quando a origem ou de destino é uma tabela.

### <a name="use-a-response-file-to-specify-command-line-parameters"></a>Utilizar um ficheiro de resposta para especificar os parâmetros da linha de comandos

```azcopy
AzCopy /@:"C:\responsefiles\copyoperation.txt"
```

Pode incluir quaisquer parâmetros de linha de comandos do AzCopy num arquivo de resposta. AzCopy processa os parâmetros no ficheiro como se tivesse sido especificados na linha de comando, executando uma substituição direta com o conteúdo do ficheiro.

Suponha que um ficheiro de resposta denominado `copyoperation.txt`, que contém as seguintes linhas. Cada parâmetro do AzCopy pode ser especificado numa única linha

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

ou na separação em linhas:

    /Source:http://myaccount.blob.core.windows.net/mycontainer
    /Dest:C:\myfolder
    /SourceKey:<sourcekey>
    /S
    /Y

AzCopy falha, se dividir o parâmetro por duas linhas, conforme mostrado aqui para o `/sourcekey` parâmetro:

    http://myaccount.blob.core.windows.net/mycontainer
     C:\myfolder
    /sourcekey:
    <sourcekey>
    /S
    /Y

### <a name="use-multiple-response-files-to-specify-command-line-parameters"></a>Utilizar vários arquivos de resposta para especificar os parâmetros da linha de comandos

Suponha que um ficheiro de resposta denominado `source.txt` que especifica um contentor de origem:

    /Source:http://myaccount.blob.core.windows.net/mycontainer

E um ficheiro de resposta denominado `dest.txt` que especifica uma pasta de destino no sistema de ficheiros:

    /Dest:C:\myfolder

E um ficheiro de resposta denominado `options.txt` que especifica as opções para o AzCopy:

    /S /Y

Para chamar o AzCopy com esses arquivos de resposta, os quais residem num diretório `C:\responsefiles`, utilize este comando:

```azcopy
AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   
```

AzCopy processa este comando, tal como faria se tiver incluído todos os parâmetros individuais na linha de comando:

```azcopy
AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y
```

### <a name="specify-a-shared-access-signature-sas"></a>Especifique uma assinatura de acesso partilhado (SAS)

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt
```

Também pode especificar uma SAS no contentor do URI:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S
```

### <a name="journal-file-folder"></a>Pasta do ficheiro de diário

Sempre que emitir um comando para o AzCopy, ele verifica se um ficheiro de diário já existe na pasta predefinida ou se existe numa pasta que especificou através desta opção. Se o ficheiro de diário não existir em qualquer lugar, o AzCopy trata a operação como o novo e gera um novo ficheiro de diário.

Se existir o ficheiro de diário, o AzCopy verifica se a linha de comandos que entrada coincide com a linha de comandos no ficheiro de diário. Se as duas linhas de comando corresponderem, o AzCopy retoma a operação incompleta. Se não forem iguais, deve optar por substituir o ficheiro de diário para iniciar uma nova operação ou cancelar a operação atual.

Se pretender utilizar a localização predefinida para o ficheiro de diário:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z
```

Se omitir a opção `/Z`, ou especificar a opção `/Z` sem o caminho da pasta, conforme mostrado acima, o AzCopy cria o ficheiro de diário na localização predefinida, que é `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Se já existir um ficheiro de diário, em seguida, o AzCopy retoma a operação com base no ficheiro de diário.

Se pretender especificar uma localização personalizada para o ficheiro de diário:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\
```

Este exemplo cria o ficheiro de diário, caso ainda não exista. Se existir, o AzCopy retoma a operação com base no ficheiro de diário.

Se pretender retomar uma operação do AzCopy:

```azcopy
AzCopy /Z:C:\journalfolder\
```

Neste exemplo retoma a última operação, pode ter falhado para concluir.

### <a name="generate-a-log-file"></a>Gerar um ficheiro de registo

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V
```

Se especificar a opção `/V` sem fornecer um caminho de ficheiro para o registo verboso, em seguida, o AzCopy cria o ficheiro de registo na localização predefinida, que é `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

Caso contrário, pode criar um arquivo de log num local personalizado:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log
```

Observe que, se especificar uma opção a seguir um caminho relativo `/V`, tal como `/V:test/azcopy1.log`, em seguida, o registo verboso é criado no diretório de trabalho atual numa subpasta com o nome `test`.

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Especifique o número de operações simultâneas para começar

Opção `/NC` Especifica o número de operações de cópia em simultâneo. Por predefinição, o AzCopy é iniciado um determinado número de operações simultâneas para aumentar o débito de transferência de dados. Para operações de tabela, o número de operações simultâneas é igual ao número de processadores que tiver. Para operações de BLOBs e ficheiros, o número de operações simultâneas é igual a 8 vezes o número de processadores que tiver. Se estiver a executar o AzCopy numa rede de baixa largura de banda, pode especificar um número mais baixo para /NC evitar a falha causada pela concorrência de recursos.

### <a name="run-azcopy-against-the-azure-storage-emulator"></a>Executar o AzCopy com o emulador de armazenamento do Azure

Pode executar o AzCopy em relação a [emulador de armazenamento do Azure](storage-use-emulator.md) para blobs:

```azcopy
AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S
```

Também pode executá-la para tabelas:

```azcopy
AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table
```

### <a name="automatically-determine-content-type-of-a-blob"></a>Determinar automaticamente o tipo de conteúdo de um Blob

AzCopy determina o tipo de conteúdo de um blob com base num ficheiro JSON que armazena o tipo de conteúdo para o mapeamento de extensão de ficheiro. Este ficheiro JSON com o nome AzCopyConfig.json e está localizado no diretório do AzCopy. Se tiver um tipo de ficheiro que não esteja na lista pode acrescentar o mapeamento para o ficheiro JSON:

```
{
  "MIMETypeMapping": {
    ".myext": "text/mycustomtype",
    .
    .
  }
}
```     

## <a name="azcopy-parameters"></a>Parâmetros do AzCopy

Parâmetros para o AzCopy são descritos abaixo. Também pode digitar um dos seguintes comandos da linha de comando para obter ajuda com o AzCopy:

* Para obter ajuda detalhada da linha de comandos para o AzCopy: `AzCopy /?`
* Para obter ajuda detalhada com qualquer parâmetro do AzCopy: `AzCopy /?:SourceKey`
* Para obter exemplos da linha de comandos: `AzCopy /?:Sample`

### <a name="sourcesource"></a>/Source:"source"

Especifica a origem de dados dos quais quer copiar. A origem pode ser um diretório de sistema de ficheiros, um contentor de BLOBs, um diretório virtual de BLOBs, uma partilha de ficheiros de armazenamento, um diretório do ficheiro de armazenamento ou uma tabela do Azure.

**Aplicável para:** BLOBs, ficheiros, tabelas

### <a name="destdestination"></a>/Dest:"destination"

Especifica o destino para copiar para. O destino pode ser um diretório de sistema de ficheiros, um contentor de BLOBs, um diretório virtual de BLOBs, uma partilha de ficheiros de armazenamento, um diretório do ficheiro de armazenamento ou uma tabela do Azure.

**Aplicável para:** BLOBs, ficheiros, tabelas

### <a name="patternfile-pattern"></a>/Pattern:"file-pattern"

Especifica um padrão de arquivo que indica quais arquivos para copiar. O comportamento do parâmetro /Pattern é determinado pela localização dos dados de origem e a presença da opção de modo recursivo. Modo de recursiva é especificado por meio da opção /S.

Se a origem especificada é um diretório no sistema de arquivos, em seguida, os carateres universais padrão estão em vigor e o padrão de ficheiro fornecido é comparado com ficheiros no diretório. Se a opção que /s for especificada, em seguida, o AzCopy também coincide com o padrão especificado em relação a todos os ficheiros em subpastas sob o diretório.

Se a origem especificada é um contentor de BLOBs ou o diretório virtual, carateres universais não são aplicadas. Se a opção que /s for especificada, em seguida, AzCopy interpreta o padrão de ficheiro especificado como um prefixo de Blobs. Se corresponder a opção que /s não for especificado, o AzCopy, em seguida, o padrão de ficheiros com nomes de BLOBs exato.

Se a origem especificada é uma partilha de ficheiros do Azure, em seguida, terá de especificar o nome do ficheiro exata, (por exemplo, abc.txt) para copiar um ficheiro único, ou especificar a opção /S para copiar todos os ficheiros no recursivamente a partilha. Tentar especificar ambos os arquivos padrão e a opção /S em conjunto os resultados num erro.

AzCopy utiliza a correspondência de maiúsculas e minúsculas, quando o /Source é um contentor de BLOBs ou diretório virtual do blob e utiliza a correspondência de maiúsculas e minúsculas em todos os outros casos.

O padrão de ficheiro predefinido utilizado quando não é especificado nenhum padrão de arquivo é *.* para uma localização do ficheiro de sistema ou um prefixo vazio para uma localização de armazenamento do Azure. Especificar vários padrões de ficheiro não é suportado.

**Aplicável para:** Ficheiros e BLOBs

### <a name="destkeystorage-key"></a>/DestKey:"storage-key"

Especifica a chave de conta de armazenamento para o recurso de destino.

**Aplicável para:** BLOBs, ficheiros, tabelas

### <a name="destsassas-token"></a>/DestSAS:"sas-token"

Especifica uma assinatura de acesso partilhado (SAS) com permissões de leitura e escrita para o destino (se aplicável). Coloque a SAS com aspas duplas, porque pode contém carateres especiais de linha de comandos.

Se o recurso de destino é um contentor de BLOBs, uma partilha de ficheiros ou tabela, pode especificar esta opção seguida o token SAS ou pode especificar a SAS como parte de contentor do blob de destino, a partilha de ficheiros ou a URI da tabela, sem esta opção.

Se a origem e de destino são ambos os blobs, em seguida, o blob de destino têm de residir na mesma conta de armazenamento como o blob de origem.

**Aplicável para:** BLOBs, ficheiros, tabelas

### <a name="sourcekeystorage-key"></a>/SourceKey:"storage-key"

Especifica a chave de conta de armazenamento para o recurso de origem.

**Aplicável para:** BLOBs, ficheiros, tabelas

### <a name="sourcesassas-token"></a>/SourceSAS:"sas-token"

Especifica uma assinatura de acesso partilhado com permissões de leitura e de lista para a origem (se aplicável). Coloque a SAS com aspas duplas, porque pode contém carateres especiais de linha de comandos.

Se o recurso de origem é um contentor de BLOBs e não uma chave nem uma SAS é fornecida, o contentor de BLOBs é de leitura por meio do acesso anónimo.

Se a origem é uma partilha de ficheiros ou tabela, uma chave ou uma SAS tem de ser fornecida.

**Aplicável para:** BLOBs, ficheiros, tabelas

### <a name="s"></a>/S

Especifica o modo de recursiva para operações de cópia. No modo de recursiva, o AzCopy copia todos os blobs ou ficheiros que correspondam ao padrão de ficheiro especificado, incluindo aquelas em subpastas.

**Aplicável para:** Ficheiros e BLOBs

### <a name="blobtypeblock--page--append"></a>/BlobType:"block" | "page" | "append"

Especifica se o blob de destino é um blob de blocos, BLOBs de páginas ou blob de acréscimo. Esta opção só é aplicável apenas quando estiver a carregar um blob. Caso contrário, é gerado um erro. Se o destino é um blob e esta opção não for especificada, por predefinição, o AzCopy cria um blob de blocos.

**Aplicável para:** Blobs

### <a name="checkmd5"></a>/CheckMD5

Calcula um hash MD5 para dados transferidos e verifica se o hash MD5 armazenados no blob ou propriedade de MD5 do conteúdo do ficheiro corresponda ao hash calculado. Se os valores não corresponderem, o AzCopy falhará transferir os dados. A verificação MD5 está desativada por predefinição, pelo que tem de especificar esta opção para efetuar a verificação MD5 quando a transferência de dados.

Tenha em atenção que o armazenamento do Azure não garante que o hash MD5 armazenado para o blob ou o ficheiro está atualizado. É responsabilidade do cliente para atualizar o MD5 sempre que o blob ou o ficheiro é modificado. No caso de imagens de disco (discos geridos ou não gerenciados), as VMs do Azure não atualizar o valor de MD5 como a alteração de conteúdo do disco, por conseguinte, /CheckMD5 irá gerar um erro ao transferir as imagens de disco.

AzCopy v8 sempre define a propriedade de MD5 de conteúdo para um ficheiro de Blobs do Azure ou após a carregá-lo para o serviço.  

**Aplicável para:** Ficheiros e BLOBs

### <a name="snapshot"></a>/Snapshot

Indica se a transferência de instantâneos. Esta opção só é válida quando a origem é um blob.

Os instantâneos de blob transferidos são mudar o nome no seguinte formato: nome do blob (instantâneo-time) .extension

Por predefinição, os instantâneos não são copiados.

**Aplicável para:** Blobs

### <a name="vverbose-log-file"></a>/V:[verbose-log-file]

Mensagens de estado detalhado de saídas num arquivo de log.

Por predefinição, o ficheiro de registo verboso é denominado AzCopyVerbose.log no `%LocalAppData%\Microsoft\Azure\AzCopy`. Se especificar uma localização de ficheiro existentes para esta opção, o registo verboso é acrescentado a esse ficheiro.  

**Aplicável para:** BLOBs, ficheiros, tabelas

### <a name="zjournal-file-folder"></a>/Z:[journal-file-folder]

Especifica uma pasta do ficheiro de diário para retomar uma operação.

AzCopy sempre oferece suporte a retomada, se uma operação foi interrompida.

Se esta opção não for especificada, ou se for especificado sem um caminho de pasta, o AzCopy cria o ficheiro de diário na localização predefinida, que é % LocalAppData%\Microsoft\Azure\AzCopy.

Sempre que emitir um comando para o AzCopy, ele verifica se um ficheiro de diário já existe na pasta predefinida ou se existe numa pasta que especificou através desta opção. Se o ficheiro de diário não existir em qualquer lugar, o AzCopy trata a operação como o novo e gera um novo ficheiro de diário.

Se existir o ficheiro de diário, o AzCopy verifica se a linha de comandos que entrada coincide com a linha de comandos no ficheiro de diário. Se as duas linhas de comando corresponderem, o AzCopy retoma a operação incompleta. Se não forem iguais, deve optar por substituir o ficheiro de diário para iniciar uma nova operação ou cancelar a operação atual.

O ficheiro de diário é eliminado após a conclusão bem-sucedida da operação.

Tenha em atenção que a retomar uma operação de um ficheiro de diário criada por uma versão anterior do AzCopy não é suportada.

**Aplicável para:** BLOBs, ficheiros, tabelas

### <a name="parameter-file"></a>/@:"parameter-file"

Especifica um ficheiro que contém parâmetros. AzCopy processa os parâmetros no ficheiro, como se tivesse sido especificados na linha de comandos.

Num arquivo de resposta, pode especificar vários parâmetros numa única linha, ou especificar cada parâmetro numa linha própria. Tenha em atenção que um parâmetro individual não pode abranger várias linhas.

Arquivos de resposta podem incluir linhas de comentários que começam com o símbolo #.

É possível especificar vários ficheiros de resposta. No entanto, tenha em atenção que não suporta o AzCopy aninhadas arquivos de resposta.

**Aplicável para:** BLOBs, ficheiros, tabelas

### <a name="y"></a>/Y

Suprime a todos os pedidos de confirmação de AzCopy. Esta opção também permite a utilização de tokens SAS só de escrita para cenários de carregamento de dados, quando /XO e /XN não foram especificados.

**Aplicável para:** BLOBs, ficheiros, tabelas

### <a name="l"></a>/L

Especifica uma operação de listagem apenas; Não existem dados são copiados.

AzCopy interpreta a através desta opção como uma simulação para executar a linha de comando sem esta opção /L e contagens de quantos objetos são copiados, pode especificar a opção /V ao mesmo tempo para verificar quais os objetos são copiados no registo verboso.

O comportamento desta opção também é determinado pela localização dos dados de origem e a presença recursiva modo opção /S e de arquivo padrão da opção de /Pattern.

AzCopy requer permissão de LIST e READ desta localização de origem ao utilizar esta opção.

**Aplicável para:** Ficheiros e BLOBs

### <a name="mt"></a>/MT

Define o tempo de última modificação do ficheiro transferido para ser o mesmo como o blob de origem ou do ficheiro.

**Aplicável para:** Ficheiros e BLOBs

### <a name="xn"></a>/XN

Exclui um recurso de origem mais recente. O recurso não é copiado se a hora da última modificação da origem de é igual ou mais recente do que o destino.

**Aplicável para:** Ficheiros e BLOBs

### <a name="xo"></a>/XO
Exclui um recurso de origem mais antigo. O recurso não é copiado se a hora da última modificação da origem é o mesmo ou com mais de destino.

**Aplicável para:** Ficheiros e BLOBs

### <a name="a"></a>/A

Carrega apenas os ficheiros que tenham o atributo de arquivo definido.

**Aplicável para:** Ficheiros e BLOBs

### <a name="iarashcnetoi"></a>/IA:[RASHCNETOI]

Carrega apenas os ficheiros que contenham qualquer um conjunto de atributos especificado.

Atributos disponíveis incluem:

* R = ficheiros só de leitura
* A = ficheiros prontos para arquivar
* S = arquivos do sistema
* H = Hidden ficheiros
* C = arquivos compactados
* N = ficheiros Normal
* I = ficheiros encriptados
* T = ficheiros temporários
* U = arquivos Offline
* Eu = arquivos não-indexada

**Aplicável para:** Ficheiros e BLOBs

### <a name="xarashcnetoi"></a>/XA:[RASHCNETOI]

Exclui ficheiros que contenham qualquer um conjunto de atributos especificado.

Atributos disponíveis incluem:

* R = ficheiros só de leitura
* A = ficheiros prontos para arquivar
* S = arquivos do sistema
* H = Hidden ficheiros
* C = arquivos compactados
* N = ficheiros Normal
* I = ficheiros encriptados
* T = ficheiros temporários
* U = arquivos Offline
* Eu = arquivos não-indexada

**Aplicável para:** Ficheiros e BLOBs

### <a name="delimiterdelimiter"></a>/Delimiter:"delimiter"

Indica o caractere de delimitador usado para delimitar os diretórios virtuais num nome de blob.

Por predefinição, utiliza AzCopy / como caráter delimitador. No entanto, o AzCopy suporta a utilização de qualquer caractere comuns (por exemplo, @, #, ou %) como um delimitador. Se tiver de incluir um destes carateres especiais na linha de comando, coloque o nome de ficheiro com aspas duplas.

Esta opção só é aplicável para transferir blobs.

**Aplicável para:** Blobs

### <a name="ncnumber-of-concurrent-operations"></a>/NC:"number-of-concurrent-operations"

Especifica o número de operações simultâneas.

AzCopy por predefinição é iniciado um determinado número de operações simultâneas para aumentar o débito de transferência de dados. Tenha em atenção que o grande número de operações simultâneas num ambiente de largura de banda baixa pode sobrecarregar a ligação de rede e impedir que as operações de totalmente concluir. Limite operações simultâneas com base na largura de banda de rede disponível real.

O limite superior para operações simultâneas é 512.

**Aplicável para:** BLOBs, ficheiros, tabelas

### <a name="sourcetypeblob--table"></a>/SourceType:"Blob" | "Table"

Especifica que o `source` recursos é um disponível no ambiente de desenvolvimento local, em execução no emulador do armazenamento de Blobs.

**Aplicável para:** BLOBs, tabelas

### <a name="desttypeblob--table"></a>/DestType:"Blob" | "Table"

Especifica que o `destination` recursos é um disponível no ambiente de desenvolvimento local, em execução no emulador do armazenamento de Blobs.

**Aplicável para:** BLOBs, tabelas

### <a name="pkrskey1key2key3"></a>/PKRS:"key1#key2#key3#..."

Divide o intervalo da chave de partição para permitir a exportação de dados de tabela em paralelo, o que aumenta a velocidade da operação de exportação.

Se esta opção não for especificada, o AzCopy utiliza um único thread para exportar as entidades da tabela. Por exemplo, se o utilizador Especifica /PKRS: "aa #bb", em seguida, o AzCopy é iniciado três operações simultâneas.

Cada operação exporta um dos três intervalos de chaves de partição, conforme mostrado abaixo:

  [primeiro-chave de partição, aa)

  [aa, bb)

  [bb, chave de partição de último]

**Aplicável para:** Tabelas

### <a name="splitsizefile-size"></a>/SplitSize:"file-size"

Especifica o ficheiro exportado dividir o tamanho em MB, o valor mínimo permitido é de 32.

Se esta opção não for especificada, o AzCopy exporta os dados da tabela para um único arquivo.

Se os dados de tabela são exportados para um blob e o tamanho de ficheiro exportado atingir o limite de 200 GB de tamanho do blob, AzCopy divide o ficheiro exportado, mesmo se esta opção não for especificada.

**Aplicável para:** Tabelas

### <a name="entityoperationinsertorskip--insertormerge--insertorreplace"></a>/EntityOperation:"InsertOrSkip" | "InsertOrMerge" | "InsertOrReplace"

Especifica o comportamento de importação de dados de tabela.

* InsertOrSkip - ignora uma entidade existente ou inserir uma nova entidade, se não existir na tabela.
* InsertOrMerge - intercala uma entidade existente ou inserir uma nova entidade, se não existir na tabela.
* InsertOrReplace - substitui uma entidade existente ou inserir uma nova entidade, se não existir na tabela.

**Aplicável para:** Tabelas

### <a name="manifestmanifest-file"></a>/Manifest:"manifest-file"

Especifica o ficheiro de manifesto para a tabela Exportar e importar a operação.

Esta opção é opcional durante a operação de exportação, o AzCopy gera um arquivo de manifesto com o nome predefinido, se esta opção não for especificada.

Esta opção é necessária durante a operação de importação para localizar os ficheiros de dados.

**Aplicável para:** Tabelas

### <a name="synccopy"></a>/SyncCopy

Indica se deve ser de forma síncrona copiar blobs ou ficheiros entre dois pontos de extremidade de armazenamento do Azure.

AzCopy por padrão usa a cópia assíncrona do lado do servidor. Especifique esta opção para efetuar uma cópia síncrona, o que transfere blobs ou ficheiros para a memória local e, em seguida, carrega-os para o armazenamento do Azure.

Pode utilizar esta opção ao copiar arquivos dentro do armazenamento de BLOBs no armazenamento de ficheiros ou de armazenamento de BLOBs para o armazenamento de ficheiros ou vice-versa.

**Aplicável para:** Ficheiros e BLOBs

### <a name="setcontenttypecontent-type"></a>/SetContentType:"content-type"

Especifica o tipo de conteúdo MIME para ficheiros ou de blobs de destino.

AzCopy define o tipo de conteúdo para um ficheiro ou blob como application/octet-stream por predefinição. Pode definir o tipo de conteúdo para todos os blobs ou ficheiros ao especificar explicitamente um valor para esta opção.

Se especificar esta opção sem um valor, em seguida, o AzCopy define cada blob ou tipo de conteúdo do ficheiro, de acordo com a extensão de ficheiro.

**Aplicável para:** Ficheiros e BLOBs

### <a name="payloadformatjson--csv"></a>/PayloadFormat:"JSON" | "CSV"

Especifica o formato do arquivo de dados exportados de tabela.

Se esta opção não for especificada, por predefinição, o AzCopy exporta o ficheiro de dados de tabela no formato JSON.

**Aplicável para:** Tabelas

## <a name="known-issues-and-best-practices"></a>Problemas conhecidos e práticas recomendadas

Vamos dar uma olhada em alguns dos problemas conhecidos e práticas recomendadas.

### <a name="limit-concurrent-writes-while-copying-data"></a>Limitar as escritas simultâneas ao copiar dados

Quando copiar blobs ou ficheiros com o AzCopy, tenha em atenção que outra aplicação pode modificar os dados enquanto estiver a copiar. Se possível, certifique-se de que os dados que está a copiar não está a ser modificados durante a operação de cópia. Por exemplo, ao copiar um VHD associado a uma máquina virtual do Azure, certifique-se de que nenhum outro aplicativo esteja atualmente escrevendo para o VHD. É uma boa maneira de fazer isso por leasing o recurso a ser copiados. Em alternativa, pode criar um instantâneo do VHD primeiro e, em seguida, copiar o instantâneo.

Se não pode impedir que outros aplicativos escrita em blobs ou arquivos enquanto eles estão a ser copiados, em seguida, tenha em atenção que quando que a tarefa é concluída, os recursos copiados já não podem ter paridade completa com os recursos de origem.

### <a name="enable-fips-compliant-md5-algorithms-for-azcopy-when-you-use-fips-compliant-algorithms-for-encryption-hashing-and-signing"></a>Ativar algoritmos MD5 compatíveis com FIPS para o AzCopy quando "algoritmos compatíveis com o uso FIPS para encriptação, hash e assinatura".

AzCopy por padrão usa a implementação de .NET MD5 para calcular o MD5 ao copiar objetos, mas existem alguns requisitos de segurança que precisam AzCopy para ativar a definição de MD5 compatíveis com FIPS.

Pode criar um arquivo App. config `AzCopy.exe.config` com a propriedade `AzureStorageUseV1MD5` e colocá-la aside com AzCopy.exe.

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <appSettings>
        <add key="AzureStorageUseV1MD5" value="false"/>
      </appSettings>
    </configuration>

Para a propriedade "AzureStorageUseV1MD5":

* VERDADEIRO - o valor predefinido, o AzCopy utiliza implementação MD5 de .NET.
* FALSE – AzCopy usa o algoritmo de MD5 compatíveis com FIPS.

Algoritmos compatíveis com FIPS estão desativados por predefinição no Windows. Pode alterar esta definição de política no seu computador. Na janela executar (Windows + R), digite secpol. msc para abrir o **política de segurança Local** janela. Na **definições de segurança** janela, navegue para **configurações de segurança** > **políticas locais** > **asopçõesdesegurança**. Localize o **criptografia de sistema: Utilizar algoritmos compatíveis com FIPS para encriptação, hash e assinatura** política. Faça duplo clique na política para ver o valor apresentado na **definição de segurança** coluna.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Armazenamento do Azure e o AzCopy, veja os seguintes recursos:

### <a name="azure-storage-documentation"></a>Documentação do armazenamento do Azure:
* [Introdução ao Armazenamento do Azure](../storage-introduction.md)
* [Como utilizar o armazenamento de Blobs do .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Como utilizar o armazenamento de ficheiros do .NET](../storage-dotnet-how-to-use-files.md)
* [Como utilizar o armazenamento de tabelas do .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Como criar, gerir ou eliminar uma conta de armazenamento](../storage-create-storage-account.md)
* [Transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md)

### <a name="azure-storage-blog-posts"></a>Postagens de blog de armazenamento do Azure:
* [Introdução ao pré-visualização de biblioteca do movimento de dados de armazenamento do Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Introdução ao copiar síncrona e tipo de conteúdo personalizado](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: Anunciamos a disponibilidade geral de 3.0 AzCopy e a versão do AzCopy 4.0 com a tabela e ficheiro de suporte de pré-visualização](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: Otimizado para cenários de cópia em grande escala](https://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Suporte para armazenamento georredundante com acesso de leitura](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy: Transferir dados com o modo reiniciável e o SAS token](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: Utilizar o Blob de cópia entre-conta](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: A carregar/transferir ficheiros para os Blobs do Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
