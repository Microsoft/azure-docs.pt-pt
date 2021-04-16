---
title: Sincronizar com o armazenamento Azure Blob utilizando a AzCopy v10 | Microsoft Docs
description: Este artigo contém uma coleção de comandos de exemplo AzCopy que o ajudam a sincronizar com o armazenamento Azure Blob.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 8b3340c00d856b13edefc7728d5baa327399a441
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502934"
---
# <a name="synchronize-with-azure-blob-storage-by-using-azcopy"></a>Sincronizar com o armazenamento Azure Blob utilizando a AzCopy

Pode sincronizar o armazenamento local com o armazenamento Azure Blob utilizando o utilitário de linha de comando AzCopy v10. 

Pode sincronizar o conteúdo de um sistema de ficheiros local com um recipiente de bolhas. Também pode sincronizar contentores e diretórios virtuais entre si. A sincronização é uma maneira. Por outras palavras, você escolhe qual destes dois pontos finais é a fonte e qual é o destino. A sincronização também utiliza o servidor para as APIs do servidor. Os exemplos apresentados nesta secção também funcionam com contas que têm um espaço hierárquico de nomes. 

> [!NOTE]
> O lançamento atual do AzCopy não sincroniza entre outras fontes e destinos (por exemplo: armazenamento de ficheiros ou baldes S3 da Amazon Web Services (AWS).

Para ver exemplos de outros tipos de tarefas, como carregar ficheiros, descarregar bolhas ou copiar bolhas entre contas, consulte os links apresentados na secção [Etapas Seguintes](#next-steps) deste artigo.

## <a name="get-started"></a>Introdução

Consulte o [artigo da AzCopy](storage-use-azcopy-v10.md) para descarregar o AzCopy e saiba como pode fornecer credenciais de autorização ao serviço de armazenamento.

> [!NOTE] 
> Os exemplos deste artigo assumem que forneceu credenciais de autorização utilizando o Azure Ative Directory (Azure AD).
>
> Se preferir usar um token SAS para autorizar o acesso a dados blob, então pode anexar esse símbolo ao URL de recursos em cada comando AzCopy. Por exemplo: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="guidelines"></a>Diretrizes

- O comando [de sincronização](storage-ref-azcopy-sync.md) compara nomes de ficheiros e últimos cartões de tempo modificados. Desa estama a `--delete-destination` bandeira opcional a um valor de ou para apagar `true` `prompt` ficheiros no diretório de destino se esses ficheiros já não existirem no diretório de origem.

- Se colocar a `--delete-destination` bandeira para , a `true` AzCopy elimina ficheiros sem fornecer uma solicitação. Se desejar que apareça uma solicitação antes de o AzCopy apagar um ficheiro, coloque a `--delete-destination` bandeira em `prompt` .

- Se pretender definir `--delete-destination` a bandeira para `prompt` `false` ou, considere utilizar o comando de [cópia](storage-ref-azcopy-copy.md) em vez do comando de [sincronização](storage-ref-azcopy-sync.md) e definir o `--overwrite` parâmetro para `ifSourceNewer` . O comando [de cópia](storage-ref-azcopy-copy.md) consome menos memória e incorre em menos custos de faturação porque uma operação de cópia não tem de indexar a fonte ou destino antes de mover ficheiros. 

- Para evitar supressões acidentais, certifique-se de que ativa a função [de eliminação suave](../blobs/soft-delete-blob-overview.md) antes de utilizar a `--delete-destination=prompt|true` bandeira.

- A máquina em que executou o comando de sincronização deve ter um relógio de sistema preciso, porque os últimos tempos modificados são cruciais para determinar se um ficheiro deve ser transferido. Se o seu sistema tiver um relógio significativo, evite modificar ficheiros no destino demasiado próximos do momento em que planeia executar um comando de sincronização.

## <a name="update-a-container-with-changes-to-a-local-file-system"></a>Atualizar um recipiente com alterações a um sistema de ficheiros local

Neste caso, o contentor é o destino e o sistema de ficheiros local é a origem. 

> [!TIP]
> Este exemplo encerra argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

**Syntax**

`azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Exemplo**

```azcopy
azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive
```

## <a name="update-a-local-file-system-with-changes-to-a-container"></a>Atualizar um sistema de ficheiros local com alterações a um contentor

Neste caso, o sistema de ficheiros local é o destino, e o contentor é a fonte.

> [!TIP]
> Este exemplo encerra argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

**Syntax**

`azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive`

**Exemplo**

```azcopy
azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive
```

## <a name="update-a-container-with-changes-in-another-container"></a>Atualizar um recipiente com alterações em outro recipiente

O primeiro recipiente que aparece neste comando é a fonte. O segundo é o destino.

> [!TIP]
> Este exemplo encerra argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Exemplo**

```azcopy
azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

## <a name="update-a-directory-with-changes-to-a-directory-in-another-container"></a>Atualizar um diretório com alterações a um diretório em outro recipiente

O primeiro diretório que aparece neste comando é a fonte. O segundo é o destino.

> [!TIP]
> Este exemplo encerra argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive`

**Exemplo**

```azcopy
azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive
```

## <a name="synchronize-with-optional-flags"></a>Sincronizar com bandeiras opcionais

Pode ajustar a sua operação de sincronização utilizando bandeiras opcionais. Aqui estão alguns exemplos.

|Scenario|Sinalizador|
|---|---|
|Especifique como os hashes MD5 devem ser validados ao descarregar.|**--check-md5** = \[ NoCheck \| LogOnly \| FailIfDifferent \| FailIfDifferentOrMissing\]|
|Excluir ficheiros com base num padrão.|**--excluir caminho**|
|Especifique o quão detalhado pretende que as suas entradas de registo relacionadas com sincronização sejam.|**--nível** = \[ de log \|INFORMAÇÃO DE ERRO \| DE AVISO \| NENHUMA\]|

Para obter uma lista completa de bandeiras, consulte [as opções.](storage-ref-azcopy-sync.md#options)

> [!NOTE]
> A `--recursive` bandeira está definida por `true` defeito. As `--exclude-pattern` `--include-pattern` bandeiras aplicam-se apenas aos nomes dos ficheiros e não a outras partes do caminho do ficheiro. 

## <a name="next-steps"></a>Passos seguintes

Encontre mais exemplos nestes artigos:

- [Exemplos: Carregar](storage-use-azcopy-blobs-upload.md)
- [Exemplos: Transferência](storage-use-azcopy-blobs-download.md)
- [Exemplos: Copiar entre contas](storage-use-azcopy-blobs-copy.md)
- [Exemplos: Registos Amazon S3](storage-use-azcopy-s3.md)
- [Exemplos: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Exemplos: Ficheiros Azure](storage-use-azcopy-files.md)
- [Tutorial: migrar dados no local para o armazenamento na cloud com o AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Consulte estes artigos para configurar definições, otimizar o desempenho e resolver problemas:

- [Definições de configuração azCopy](storage-ref-azcopy-configuration-settings.md)
- [Otimizar o desempenho da AzCopy](storage-use-azcopy-optimize.md)
- [Problemas problemas problemas AzCopy V10 em armazenamento Azure usando ficheiros de log](storage-use-azcopy-configure.md)

