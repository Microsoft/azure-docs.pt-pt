---
title: Copiar bolhas entre contas de armazenamento Azure com AzCopy v10 Microsoft Docs
description: Este artigo contém uma coleção de comandos de exemplo AzCopy que ajudam a copiar bolhas entre contas de armazenamento.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: f536e163e3d19d91c150506ab44fdd9cbc02c693
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96907573"
---
# <a name="copy-blobs-between-azure-storage-accounts-by-using-azcopy-v10"></a>Copiar bolhas entre contas de armazenamento Azure utilizando AzCopy v10

Pode copiar bolhas, diretórios e contentores entre contas de armazenamento utilizando o utilitário de linha de comando AzCopy v10. 

Para ver exemplos de outros tipos de tarefas, tais como carregar ficheiros, descarregar bolhas e sincronizar com o armazenamento Blob, consulte os links apresentados na secção [Etapas Seguintes](#next-steps) deste artigo.

O AzCopy utiliza [APIs](/rest/api/storageservices/put-page-from-url) [de servidor a servidor,](/rest/api/storageservices/put-block-from-url) por isso os dados são copiados diretamente entre servidores de armazenamento. Estas operações de cópia não utilizam a largura de banda de rede do seu computador.

Para baixar a AzCopy e saber sobre as formas de fornecer credenciais de autorização para o serviço de armazenamento, consulte [Começar com a AzCopy](storage-use-azcopy-v10.md). 

## <a name="guidelines"></a>Diretrizes

Aplique as seguintes diretrizes nos comandos AzCopy. 

- Anexar um token SAS a cada URL de origem. 

  Se fornecer credenciais de autorização utilizando o Azure Ative Directory (Azure AD), pode omitir o token SAS apenas a partir do URL de destino. Certifique-se de que estabeleceu as funções adequadas na sua conta de destino. Ver [Opção 1: Utilizar o Diretório Ativo Azure](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory). 

  Os exemplos deste artigo assumem que autenticou a sua identidade usando a Azure AD para que os exemplos omitem os tokens SAS do URL de destino.

-  Se copiar para uma conta de armazenamento de blocos premium, omita o nível de acesso de uma bolha da operação de cópia, definindo o `s2s-preserve-access-tier` `false` de (Por exemplo: `--s2s-preserve-access-tier=false` ). As contas de armazenamento de blocos premium não suportam níveis de acesso. 

- Se copiar para ou a partir de uma conta que tenha um espaço hierárquico de nomes, use `blob.core.windows.net` em vez de na `dfs.core.windows.net` sintaxe URL. [O acesso multi-protocolo ao Armazenamento do Lago de Dados](../blobs/data-lake-storage-multi-protocol-access.md) permite-lhe utilizar `blob.core.windows.net` , e é a única sintaxe suportada para apresentar cenários de cópia de conta. 

- Pode aumentar a produção de operações de cópia definindo o valor da `AZCOPY_CONCURRENCY_VALUE` variável ambiente. Para saber mais, consulte [a produção otimiza.](storage-use-azcopy-configure.md#optimize-throughput) 

- Se as bolhas de origem tiverem etiquetas de índice, e quiser reter essas etiquetas, terá de as reaplicar às bolhas de destino. Para obter informações sobre como definir etiquetas de índice, consulte as [bolhas copy para outra conta](#copy-between-accounts-and-add-index-tags) de armazenamento com a secção de etiquetas de índice deste artigo.

## <a name="copy-a-blob"></a>Copiar uma bolha

Copie uma bolha para outra conta de armazenamento utilizando o comando [de cópia azcopia.](storage-ref-azcopy-copy.md) 

> [!TIP]
> Este exemplo encerra argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

A operação de cópia é sincronizada, pelo que quando o comando é devolvido, indica que todos os ficheiros foram copiados. 

## <a name="copy-a-directory"></a>Copiar um diretório

Copie um diretório para outra conta de armazenamento utilizando o comando [de cópia azcopia.](storage-ref-azcopy-copy.md) 

> [!TIP]
> Este exemplo encerra argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

A operação de cópia é sincronizada, pelo que quando o comando é devolvido, indica que todos os ficheiros foram copiados.

## <a name="copy-a-container"></a>Copiar um recipiente

Copie um recipiente para outra conta de armazenamento utilizando o comando [de cópia azcopia.](storage-ref-azcopy-copy.md)

> [!TIP]
> Este exemplo encerra argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

A operação de cópia é sincronizada, pelo que quando o comando é devolvido, indica que todos os ficheiros foram copiados.

## <a name="copy-containers-directories-and-blobs"></a>Copiar recipientes, diretórios e bolhas

Copie todos os recipientes, diretórios e bolhas para outra conta de armazenamento utilizando o comando [de cópia azcopy.](storage-ref-azcopy-copy.md)

> [!TIP]
> Este exemplo encerra argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

A operação de cópia é sincronizada, pelo que quando o comando é devolvido, indica que todos os ficheiros foram copiados.

<a id="copy-between-accounts-and-add-index-tags"></a>

## <a name="copy-blobs-and-add-index-tags"></a>Copiar bolhas e adicionar etiquetas de índice

Copie as bolhas para outra conta de armazenamento e adicione [etiquetas de índice blob (pré-visualização)](../blobs/storage-manage-find-blobs.md) à bolha-alvo.

Se estiver a utilizar a autorização Azure AD, o seu principal de segurança deve ser atribuído à função [de Proprietário de Dados de Armazenamento blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) ou deve ser dada permissão à operação do `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [fornecedor de recursos Azure](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) através de uma função Azure personalizada. Se estiver a utilizar um token de Assinatura de Acesso Partilhado (SAS), esse token deve fornecer acesso às etiquetas da bolha através da `t` permissão SAS.

Para adicionar tags, utilize a `--blob-tags` opção juntamente com um url codificado par de valores-chave. 

Por exemplo, para adicionar a chave `my tag` e um `my tag value` valor, adicionaria `--blob-tags='my%20tag=my%20tag%20value'` ao parâmetro de destino. 

Separe várias etiquetas de índice utilizando um ampersand `&` ().  Por exemplo, se pretender adicionar uma chave `my second tag` e um `my second tag value` valor, a cadeia completa de opções será `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

Os exemplos a seguir mostram como utilizar a `--blob-tags` opção.

> [!TIP]
> Estes exemplos encerram argumentos de caminho com citações simples ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

|    |     |
|--------|-----------|
| **Blob** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Diretório** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Container** (Contentor) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Conta** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

A operação de cópia é sincronizada, pelo que quando o comando é devolvido, indica que todos os ficheiros foram copiados.

> [!NOTE]
> Se especificar um diretório, um contentor ou uma conta para a fonte, todas as bolhas que são copiadas para o destino terão as mesmas etiquetas que especifica no comando.

## <a name="copy-with-optional-flags"></a>Copiar com bandeiras opcionais

Pode ajustar a sua operação de cópia utilizando bandeiras opcionais. Aqui estão alguns exemplos.

|Cenário|Sinalizador|
|---|---|
|Copiar bolhas como Block, Page ou Append Blobs.|**--blob-type** = \[ \|Apendb BlockBlob PageBlob \|\]|
|Copiar para um nível de acesso específico (como o nível de arquivo).|**--bloco-blob-tier** = \[ Nenhum \| Arquivo Quente \| Cool \|\]|
|Descompressão automática de ficheiros.|**--descompressão** = \[ gzip \| esvaziar\]|

Para obter uma lista completa, consulte [as opções.](storage-ref-azcopy-copy.md#options) 

## <a name="next-steps"></a>Passos seguintes

Encontre mais exemplos nestes artigos:

- [Exemplos: Upload](storage-use-azcopy-blobs-upload.md)
- [Exemplos: Download](storage-use-azcopy-blobs-download.md)
- [Exemplos: Sincronizar](storage-use-azcopy-blobs-synchronize.md)
- [Exemplos: Baldes Amazon S3](storage-use-azcopy-s3.md)
- [Exemplos: Ficheiros Azure](storage-use-azcopy-files.md)
- [Tutorial: migrar dados no local para o armazenamento na cloud com o AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Configurar, otimizar e resolver problemas AzCopy](storage-use-azcopy-configure.md)