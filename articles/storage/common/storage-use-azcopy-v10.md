---
title: Copiar ou mover dados para o armazenamento do Azure com AzCopy v10 | Documentos da Microsoft
description: Utilize o utilitário de linha de comandos do AzCopy para mover ou copiar dados de ou para o blob, o data lake e o conteúdo do ficheiro. Copiar dados para o armazenamento do Azure de arquivos locais ou copiar dados em ou entre contas de armazenamento. Migre facilmente os dados ao armazenamento do Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: b5a13dfd760f0c94343b151c9b4c1148c949e854
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790000"
---
# <a name="transfer-data-with-azcopy-v10"></a>Transferir dados com AzCopy v10

O AzCopy é o utilitário da linha de comandos para copiar dados de ou para armazenamento de Blobs do Microsoft Azure e o ficheiro. AzCopy oferece uma interface de linha de comandos reprojetada e transfere a nova arquitetura de dados fiável. Com o AzCopy, pode copiar dados entre um sistema de ficheiros e uma conta de armazenamento ou entre contas de armazenamento.

## <a name="whats-new-in-azcopy-v10"></a>O que há de novo no AzCopy v10

- Sincroniza os sistemas de ficheiros para o armazenamento de Blobs do Azure ou vice versa. Use `azcopy sync <source> <destination>`. Ideal para cenários de cópia incremental.
- Oferece suporte a APIs de geração 2 de Lake armazenamento de dados do Azure. Utilize `myaccount.dfs.core.windows.net` como um URI para chamar as APIs de geração 2 de armazenamento do Data Lake.
- Suporta a copiar de uma conta de toda (serviço de BLOBs apenas) para outra conta.
- Suporta a cópia de dados de um bucket do Amazon Web Services S3.
- Utiliza o novo [colocação blocos a partir do URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) APIs para oferecer suporte a cópia de conta de conta. A transferência de dados é mais rápida, uma vez que a transferência para o cliente não é necessária.
- Apresenta uma lista ou remove os ficheiros e blobs num determinado caminho.
- Oferece suporte a padrões de carateres universais num caminho de e --sinalizadores de exclusão.
- Cria um pedido de tarefa e um ficheiro de registo relacionados com todas as instâncias de AzCopy. Pode ver e reiniciar tarefas anteriores e retomar tarefas falhadas. AzCopy também automaticamente será repetida uma transferência após uma falha.
- Melhorias de desempenho geral de funcionalidades.

## <a name="download-and-install-azcopy"></a>Baixe e instale o AzCopy

### <a name="latest-production-version-v10"></a>Versão mais recente de produção (v10)

Baixe a versão mais recente do AzCopy:
- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (destino)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

### <a name="latest-azcopy-supporting-table-storage-service-v73"></a>AzCopy mais recente que suporta o serviço de armazenamento de tabelas (v7.3)

Transfira o [v7.3 AzCopy que suporta a cópia de dados de/para o serviço de armazenamento de tabelas do Azure de Microsoft](https://aka.ms/downloadazcopynet).

## <a name="post-installation-steps"></a>Passos de pós-instalação

AzCopy não exige uma instalação. Abra a aplicação de linha de comando preferencial e navegue até à pasta onde `azcopy.exe` está localizado. Se for necessário, pode adicionar a localização da pasta AzCopy ao caminho do sistema para facilidade de utilização.

## <a name="authentication-options"></a>Opções de autenticação

AzCopy suporta as seguintes opções quando a autenticação com o armazenamento do Azure:
- **O Azure Active Directory** (suportada para **serviços Blob e de geração 2 de armazenamento do Data Lake**). Utilize ```.\azcopy login``` para iniciar sessão com o Azure Active Directory.  O utilizador deve ter [função de "Contribuinte de dados de Blob de armazenamento" atribuída](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) ao escrever no armazenamento de Blobs com a autenticação do Azure Active Directory. Para a autenticação através de identidades geridas para recursos do Azure, utilize `azcopy login --identity`.
- **Partilhado de tokens de assinatura de acesso [suportados para serviços de ficheiros e BLOBs]**. Acrescente o token de assinatura (SAS) de acesso partilhado para o caminho de blob na linha de comando para utilizá-lo. Tokens SAS com o portal do Azure, podem ser gerados [Explorador de armazenamento](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/), [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageblobsastoken), ou outras ferramentas da sua preferência. Para obter mais informações, consulte [exemplos](https://docs.microsoft.com/azure/storage/blobs/common/storage-dotnet-shared-access-signature-part-1).

## <a name="getting-started"></a>Introdução

> [!TIP]
> **Prefere uma interface gráfica do usuário?**
>
> [Explorador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/), um cliente de ambiente de trabalho que simplifica a gerir dados de armazenamento do Azure, utiliza agora o AzCopy para acelerar a transferência de dados para dentro e fora do armazenamento do Azure.
>
> Ativar o AzCopy no Explorador de armazenamento sob a **pré-visualização** menu.
> ![Ativar o AzCopy como um motor de transferência no Explorador de armazenamento do Azure](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

AzCopy v10 tem uma sintaxe de Self-documentada. Quando tiver sessão iniciada Azure Active Directory, a sintaxe geral é semelhante ao seguinte:

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>

# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you're using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?st=2019-04-05T04%3A10%3A00Z&se=2019-04-13T04%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=Qdihej%2Bsbg4AiuyLVyQZklm9pSuVGzX27qJ508wi6Es%3D" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?st=2019-04-05T04%3A10%3A00Z&se=2019-04-13T04%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=Qdihej%2Bsbg4AiuyLVyQZklm9pSuVGzX27qJ508wi6Es%3D"
```

Eis como pode obter uma lista de comandos disponíveis:

```azcopy
.\azcopy --help
# To use the alias instead
.\azcopy -h
```

Para ver a página de ajuda e exemplos para um comando específico, execute o seguinte comando:

```azcopy
.\azcopy <cmd> --help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>Criar uma partilha de ficheiro ou de contentor do blob 

**Criar um contentor de BLOBs**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**Criar uma partilha de ficheiros**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**Criar um contentor de Blobs com a geração 2 de armazenamento do Azure Data Lake**

Se tiver ativado o espaços de nomes hierárquicos na sua conta de armazenamento de BLOBs, pode utilizar o seguinte comando para criar um novo contentor de BLOBs para carregar ficheiros.

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>Copiar dados para o armazenamento do Azure

Utilize o comando de cópia para transferir dados da origem para o destino. A origem ou destino pode ser:
- Sistema de ficheiros local
- Diretório/contentor de Blobs do Azure/Virtual URI
- Partilha de ficheiro/diretório/ficheiro URI do Azure
- URI de sistema de ficheiros/diretório/ficheiro de geração 2 de Lake armazenamento de dados do Azure

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using the alias instead 
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

O comando seguinte carrega todos os ficheiros na pasta `C:\local\path` recursivamente para o contentor `mycontainer1`, criar `path` diretório no contentor. Quando `--put-md5` sinalizador é fornecido, o AzCopy calcula e armazena o hash md5 de cada ficheiro em `Content-md5` propriedade do blob correspondente para utilização posterior.

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true --put-md5
```

O comando seguinte carrega todos os ficheiros na pasta `C:\local\path` (sem recursing para os subdiretórios) para o contentor `mycontainer1`:

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --put-md5
```

Para obter mais exemplos, utilize o seguinte comando:

```azcopy
.\azcopy cp -h
```

## <a name="copy-blob-data-between-two-storage-accounts"></a>Copiar dados de BLOBs entre duas contas de armazenamento

Copiar dados entre duas contas de armazenamento utiliza a [colocar o bloco de URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API e não utiliza largura de banda de rede de máquina cliente. Dados são copiados entre dois servidores de armazenamento do Azure diretamente, enquanto o AzCopy simplesmente orquestra a operação de cópia. Esta opção só está atualmente disponível para armazenamento de Blobs.

Para copiar o todos os dados de BLOBs entre duas contas de armazenamento, utilize o seguinte comando:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

Para copiar um contentor de BLOBs para outro contentor de BLOBs, utilize o seguinte comando:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/mycontainer/<sastoken>" "https://myotheraccount.blob.core.windows.net/mycontainer/<sastoken>" --recursive=true
```

## <a name="copy-a-vhd-image-to-a-storage-account"></a>Copiar uma imagem VHD para uma conta de armazenamento

AzCopy por predefinição carrega dados para blobs de blocos. Para carregar ficheiros como Blobs de acréscimo, ou os Blobs de página, utilize o sinalizador `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

```azcopy
.\azcopy cp "C:\local\path\mydisk.vhd" "https://myotheraccount.blob.core.windows.net/mycontainer/mydisk.vhd<sastoken>" --blob-type=PageBlob
```

## <a name="sync-incremental-copy-and-delete-blob-storage-only"></a>Sincronização: cópia incremental e delete (apenas para armazenamento de BLOBs)

O comando de sincronização sincroniza o conteúdo de um diretório de origem para um diretório no destino, comparando os nomes de ficheiros e modificada pela última vez carimbos. Esta operação inclui a eliminação opcional de ficheiros de destino, se elas não existam na origem quando a `--delete-destination=prompt|true` sinalizador é fornecido. Por predefinição, o comportamento de eliminação está desativado. 

> [!NOTE] 
> Utilize o `--delete-destination` sinalizador com cuidado. Ativar a [eliminação de forma recuperável](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) de recursos antes de ativar o comportamento de eliminação em sincronia, para impedir eliminações acidentais na sua conta. 
>
> Quando `--delete-destination` está definido como true, o AzCopy irá eliminar os ficheiros que não existem na origem do destino sem qualquer pedido ao utilizador. Se pretender que seja pedida a confirmação, utilize `--delete-destination=prompt`.

Para sincronizar o seu sistema de ficheiros local para uma conta de armazenamento, utilize o seguinte comando:

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Também pode sincronizar um contentor de BLOBs para baixo para um sistema de arquivos local:

```azcopy
# The SAS token isn't required for Azure Active Directory authentication.
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

Este comando incrementalmente sincroniza a origem para o destino com base no último carimbos de data / modificada. Se adicionar ou eliminar um ficheiro na origem, o AzCopy irá fazer o mesmo no destino. Antes da eliminação, o AzCopy irá solicitar-lhe para confirmar.

## <a name="copy-data-from-amazon-web-services-aws-s3"></a>Copiar dados do Amazon Web Services (AWS) S3

Para autenticar com um registo de AWS S3, defina as seguintes variáveis de ambiente:

```
# For Windows:
set AWS_ACCESS_KEY_ID=<your AWS access key>
set AWS_SECRET_ACCESS_KEY=<AWS secret access key>
# For Linux:
export AWS_ACCESS_KEY_ID=<your AWS access key>
export AWS_SECRET_ACCESS_KEY=<AWS secret access key>
# For MacOS
export AWS_ACCESS_KEY_ID=<your AWS access key>
export AWS_SECRET_ACCESS_KEY=<AWS secret access key>
```

Para copiar o bucket para um contentor de BLOBs, emita o seguinte comando:

```
.\azcopy cp "https://s3.amazonaws.com/mybucket" "https://myaccount.blob.core.windows.net/mycontainer?<sastoken>" --recursive
```

Para obter mais detalhes sobre a cópia de dados de AWS S3, com o AzCopy, veja a página [aqui](https://github.com/Azure/azure-storage-azcopy/wiki/Copy-from-AWS-S3).

## <a name="advanced-configuration"></a>Configuração avançada

### <a name="configure-proxy-settings"></a>Configurar definições de proxy

Para configurar as definições de proxy para AzCopy v10, defina o https_proxy de variável de ambiente utilizando o seguinte comando:

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>Otimizar o débito

Defina a variável de ambiente AZCOPY_CONCURRENCY_VALUE para configurar o número de pedidos simultâneos e para controlar o consumo de recursos e desempenho de taxa de transferência. O valor é definido para 300 por predefinição. Reduzir o valor irá limitar a largura de banda e CPU utilizado pelo v10 de AzCopy.

```cmd
# For Windows:
set AZCOPY_CONCURRENCY_VALUE=<value>
# For Linux:
export AZCOPY_CONCURRENCY_VALUE=<value>
# For MacOS
export AZCOPY_CONCURRENCY_VALUE=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank then the default value is currently in use
```

### <a name="change-the-location-of-the-log-files"></a>Alterar a localização dos ficheiros de registo

Pode alterar a localização dos ficheiros de registo, se for necessário ou para evitar o disco do SO a ser preenchida.

```cmd
# For Windows:
set AZCOPY_LOG_LOCATION=<value>
# For Linux:
export AZCOPY_LOG_LOCATION=<value>
# For MacOS
export AZCOPY_LOG_LOCATION=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank, then the default value is currently in use
```
### <a name="change-the-default-log-level"></a>Alterar o nível de registo predefinido 

Por predefinição, o nível de registo de AzCopy é definido para informações. Se gostaria de reduzir a verbosidade de registo para poupar espaço em disco, substituir a definição utilizando ``--log-level`` opção. Níveis de registo disponíveis são: DEBUG, INFO, aviso, erro, PÂNICO e FATAL.

### <a name="review-the-logs-for-errors"></a>Rever os registos de erros

O seguinte comando irá obter todos os erros com o estado UPLOADFAILED do 04dc9ca9-158f-7945-5933-564021086c79 log:

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```
## <a name="troubleshooting"></a>Resolução de problemas

O AzCopy cria ficheiros de registo e ficheiros de plano para cada tarefa. Pode utilizar os registos para investigar e resolver quaisquer problemas potenciais. Os registos irão conter o estado de falha (UPLOADFAILED COPYFAILED e DOWNLOADFAILED), o caminho completo e o motivo da falha. Os registos da tarefa e os ficheiros de plano estão localizados em % USERPROFILE\\.azcopy pasta no Windows ou $HOME\\.azcopy pasta em Mac e Linux.

> [!IMPORTANT]
> Ao submeter um pedido para Support da Microsoft (ou resolver o problema que envolvem qualquer terceiro), partilhar a versão eliminada do comando que deseja executar. Isto garante que a SAS acidentalmente não serem partilhada com qualquer pessoa. Pode encontrar a versão eliminada no início do ficheiro de registo.

### <a name="view-and-resume-jobs"></a>Ver e retomar tarefas

Cada operação de transferência irá criar uma tarefa de AzCopy. Utilize o seguinte comando para ver o histórico de tarefas:

```azcopy
.\azcopy jobs list
```

Para ver as estatísticas de tarefa, utilize o seguinte comando:

```azcopy
.\azcopy jobs show <job-id>
```

Para filtrar as transferências por Estado, utilize o seguinte comando:

```azcopy
.\azcopy jobs show <job-id> --with-status=Failed
```

Utilize o seguinte comando para retomar uma tarefa falha/cancelado. Este comando utiliza o seu identificador juntamente com o token SAS, pois não é persistente por motivos de segurança:

```azcopy
.\azcopy jobs resume <jobid> --source-sas="<sastokenhere>"
.\azcopy jobs resume <jobid> --destination-sas="<sastokenhere>"
```

## <a name="next-steps"></a>Passos Seguintes

Se tiver dúvidas, questões ou comentários gerais, submetê-las [no GitHub](https://github.com/Azure/azure-storage-azcopy).


