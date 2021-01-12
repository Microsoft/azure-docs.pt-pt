---
title: Copiar ou mover dados para o Azure Storage utilizando a AzCopy v10 Microsoft Docs
description: O AzCopy é um utilitário de linha de comando que pode usar para copiar dados para, a partir ou entre contas de armazenamento. Este artigo ajuda-o a transferir o AzCopy, a ligar-se à conta de armazenamento e a transferir ficheiros.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: ab3cb403a8757f424a141206f99f9c29bf3e0b49
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108153"
---
# <a name="get-started-with-azcopy"></a>Introdução ao AzCopy

O AzCopy é um utilitário da linha de comando que pode utilizar para copiar blobs ou ficheiros de/para uma conta de armazenamento. Este artigo ajuda-o a transferir o AzCopy, a ligar-se à conta de armazenamento e a transferir ficheiros.

> [!NOTE]
> AzCopy **V10** é a versão atualmente suportada do AzCopy.
>
> Se precisar de utilizar uma versão anterior do AzCopy, consulte a versão anterior da secção [AzCopy](#previous-version) deste artigo.

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>Transferir o AzCopy

Em primeiro lugar, descarregue o ficheiro executável AzCopy V10 para qualquer diretório do seu computador. O AzCopy V10 é apenas um ficheiro executável, por isso não há nada para instalar.

- [Windows 64-bit](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32-bit](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux) (alcatrão)
- [macOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Estes ficheiros são comprimidos como um ficheiro zip (Windows e Mac) ou um ficheiro de alcatrão (Linux). Para descarregar e descomprimir o ficheiro de alcatrão no Linux, consulte a documentação para a sua distribuição Linux.

> [!NOTE]
> Se pretender copiar dados de e para o seu serviço de [armazenamento Azure Table,](../tables/table-storage-overview.md) instale a [versão AzCopy 7.3](https://aka.ms/downloadazcopynet).


## <a name="run-azcopy"></a>Executar o AzCopy

Para sua comodidade, considere adicionar a localização do diretório do AzCopy executável ao caminho do sistema para facilitar a utilização. Assim pode escrever `azcopy` de qualquer diretório no seu sistema.

Se optar por não adicionar o diretório AzCopy ao seu caminho, terá de alterar os diretórios para a localização do seu AzCopy executável e digitar ou nas indicações de `azcopy` comando Do Windows `.\azcopy` PowerShell.

Para ver uma lista de comandos, escreva `azcopy -h` e, em seguida, prima a tecla ENTER.

Para conhecer um comando específico, basta incluir o nome do comando (por exemplo: `azcopy list -h` ).

> [!div class="mx-imgBorder"]
> ![Ajuda inline](media/storage-use-azcopy-v10/azcopy-inline-help.png)


Para encontrar documentação de referência detalhada para cada parâmetro de comando e comando, consulte [a azcopy](storage-ref-azcopy.md)

> [!NOTE] 
> Como proprietário da sua conta de Armazenamento Azure, não lhe são atribuídas automaticamente permissões de acesso aos dados. Antes de poder fazer algo significativo com a AzCopy, tem de decidir como irá fornecer credenciais de autorização ao serviço de armazenamento. 

<a id="choose-how-youll-provide-authorization-credentials"></a>

## <a name="authorize-azcopy"></a>Autorizar a AzCopy

Pode fornecer credenciais de autorização utilizando o Azure Ative Directory (AD), ou utilizando um token de Assinatura de Acesso Partilhado (SAS).

Use esta tabela como guia:

| Tipo de armazenamento | Método de autorização atualmente suportado |
|--|--|
|**Armazenamento de blobs** | Azure Active Directory e SAS |
|**Armazenamento de bolhas (espaço hierárquico)** | Azure Active Directory e SAS |
|**Armazenamento de ficheiros** | Apenas SAS |

#### <a name="option-1-use-azure-active-directory"></a>Opção 1: Utilizar o Diretório Ativo Azure

Esta opção está disponível apenas para armazenamento de bolhas. Ao utilizar o Azure Ative Directory, pode fornecer credenciais uma vez em vez de ter de anexar um token SAS a cada comando.  

> [!NOTE]
> Na versão atual, se planeia copiar bolhas entre contas de armazenamento, terá de anexar um token SAS a cada URL de origem. Você pode omitir o token SAS apenas a partir do URL de destino. Por exemplo, consulte [bolhas de cópia entre contas de armazenamento](storage-use-azcopy-blobs.md).

Para autorizar o acesso utilizando a Azure AD, consulte [o Acesso Autorizado a bolhas com AzCopy e Azure Ative Directory (Azure AD)](storage-use-azcopy-authorize-azure-active-directory.md).

#### <a name="option-2-use-a-sas-token"></a>Opção 2: Use um token SAS

Pode anexar um token SAS a cada URL de origem ou destino que utilize nos comandos AzCopy.

Este exemplo de comando copia novamente dados de um diretório local para um recipiente de bolhas. Um símbolo SAS fictício é anexado ao final do URL do recipiente.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Para saber mais sobre fichas SAS e como obter um, consulte [Usando assinaturas de acesso partilhado (SAS)](./storage-sas-overview.md).

<a id="transfer-data"></a>

## <a name="transfer-data"></a>Transferir dados

Depois de ter autorizado a sua identidade ou obtido um token SAS, pode começar a transferir dados.

Para encontrar comandos de exemplo, consulte qualquer um destes artigos.

| Serviço | Artigo |
|--------|-----------|
|Armazenamento de Blobs do Azure |[Faça upload de ficheiros para o armazenamento do Azure Blob](storage-use-azcopy-blobs-upload.md)<br><br>[Descarregue bolhas do armazenamento Azure Blob](storage-use-azcopy-blobs-download.md)<br><br>[Bolhas de cópia entre contas de armazenamento Azure](storage-use-azcopy-blobs-copy.md)<br><br>[Sincronizar com o armazenamento da Azure Blob](storage-use-azcopy-blobs-synchronize.md)|
|Ficheiros do Azure |[Transferir dados com o AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)|
|Amazon S3|[Transfira dados com baldes AzCopy e Amazon S3](storage-use-azcopy-s3.md)|
|Armazenamento Azure Stack|[Transferir dados com armazenamento AzCopy e Azure Stack](/azure-stack/user/azure-stack-storage-transfer#azcopy)|

## <a name="use-in-a-script"></a>Use em um script

#### <a name="obtain-a-static-download-link"></a>Obtenha um link de descarregamento estático

Com o tempo, o [link de descarregamento](#download-and-install-azcopy) AzCopy irá apontar para novas versões do AzCopy. Se o seu script descarregar AzCopy, o script pode parar de funcionar se uma versão mais recente do AzCopy modificar as funcionalidades de que o seu script depende.

Para evitar estes problemas, obtenha uma ligação estática (imutável) à versão atual do AzCopy. Dessa forma, o seu script descarrega a mesma versão exata do AzCopy cada vez que é executado.

Para obter o link, executar este comando:

| Sistema operativo  | Comando |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> Para o Linux, `--strip-components=1` no comando remove a pasta de `tar` nível superior que contém o nome da versão e, em vez disso, extrai o binário diretamente para a pasta atual. Isto permite que o script seja atualizado com uma nova versão de `azcopy` apenas atualizar o `wget` URL.

A URL aparece na saída deste comando. O seu script pode então descarregar AzCopy usando esse URL.

| Sistema operativo  | Comando |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

#### <a name="escape-special-characters-in-sas-tokens"></a>Escape personagens especiais em fichas SAS

Nos ficheiros de lote que têm a `.cmd` extensão, terás de escapar aos `%` caracteres que aparecem em fichas SAS. Pode fazê-lo adicionando um carácter adicional ao lado dos `%` `%` caracteres existentes na cadeia de fichas SAS.

#### <a name="run-scripts-by-using-jenkins"></a>Executar scripts usando Jenkins

Se planeias usar o [Jenkins](https://jenkins.io/) para executar scripts, certifica-te de que colocas o seguinte comando no início do script.

```
/usr/bin/keyctl new_session
```

## <a name="use-in-azure-storage-explorer"></a>Utilização no Explorador de Armazenamento Azure

[O Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) utiliza a AzCopy para executar todas as suas operações de transferência de dados. Pode utilizar o [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) se quiser aproveitar as vantagens de desempenho do AzCopy, mas prefere utilizar uma interface gráfica do utilizador em vez da linha de comando para interagir com os seus ficheiros.

O Storage Explorer utiliza a chave da sua conta para realizar operações, por isso, depois de assinar no Storage Explorer, não necessitará de fornecer credenciais de autorização adicionais.

<a id="previous-version"></a>

## <a name="configure-optimize-and-fix"></a>Configurar, otimizar e corrigir

Consulte [Configuração, otimização e resolução de problemas AzCopy](storage-use-azcopy-configure.md)

## <a name="use-a-previous-version"></a>Use uma versão anterior

Se precisar de utilizar a versão anterior do AzCopy, consulte qualquer uma das seguintes ligações:

- [AzCopy no Windows (v8)](/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy em Linux (v7)](/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>Passos seguintes

Se tiver dúvidas, problemas ou feedback geral, envie-os na página [do GitHub.](https://github.com/Azure/azure-storage-azcopy)
