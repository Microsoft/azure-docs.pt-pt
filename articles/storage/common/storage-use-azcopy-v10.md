---
title: Copiar ou mover dados para o armazenamento do Azure com AzCopy v10 (pré-visualização) | Documentos da Microsoft
description: Utilizar o AzCopy v10 utilitário (pré-visualização) para mover ou copiar dados de ou para o blob, o data lake e o conteúdo do ficheiro. Copiar dados para o armazenamento do Azure de arquivos locais ou copiar dados em ou entre contas de armazenamento. Migre facilmente os dados ao armazenamento do Azure.
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 02/24/2019
ms.author: artemuwka
ms.subservice: common
ms.openlocfilehash: 111c24c1cd608542a5ef7da85f93ca22082af6d9
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726724"
---
# <a name="transfer-data-with-the-azcopy-v10-preview"></a>Transferir dados com v10 o AzCopy (pré-visualização)

AzCopy v10 (pré-visualização) é o utilitário de linha de comando da próxima geração para copiar dados de/para armazenamento de Blobs do Microsoft Azure e o ficheiro, que oferece uma interface de linha de comandos Reprojetado e nova arquitetura para transferências de dados fiável de alto desempenho. Com o AzCopy pode copiar dados entre um sistema de ficheiros e uma conta de armazenamento ou entre contas de armazenamento.

## <a name="whats-new-in-azcopy-v10"></a>O que há de novo no AzCopy v10

- Sincronize um sistema de arquivos para BLOBs do Azure e vice-versa. Use `azcopy sync <source> <destination>`. Ideal para cenários de cópia incremental.
- Oferece suporte a APIs de geração 2 de Lake armazenamento de dados do Azure. Utilize `myaccount.dfs.core.windows.net` como um URI para chamar as APIs de geração 2 do ADLS.
- Suporta a copiar de uma conta de toda (serviço de BLOBs apenas) para outra conta.
- Conta para cópia de conta agora está a utilizar a nova [colocação blocos a partir do URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) APIs. Nenhuma transferência de dados para o cliente é necessário que torna a transferência com mais rapidez!
- Lista/remover ficheiros e blobs num determinado caminho.
- Oferece suporte a padrões de carateres universais num caminho, bem como na – excluir sinalizador.
- Resiliência melhorada: todas as instâncias de AzCopy criará uma ordem de tarefa e um ficheiro de registo relacionados. Pode ver e reiniciar tarefas anteriores e retomar tarefas falhadas. AzCopy também automaticamente será repetida uma transferência após uma falha.
- Melhorias de desempenho geral.

## <a name="download-and-install-azcopy"></a>Baixe e instale o AzCopy

### <a name="latest-preview-version-v10"></a>Versão de pré-visualização mais recente (v10)

Baixe a versão de pré-visualização mais recente do AzCopy:
- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (destino)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

### <a name="latest-production-version-v81"></a>Versão mais recente de produção (v8.1)

Transfira o [versão de produção mais recente do AzCopy para Windows](https://aka.ms/downloadazcopy).

### <a name="azcopy-supporting-table-storage-service-v73"></a>AzCopy que suporta o serviço de armazenamento de tabelas (v7.3)

Transfira o [v7.3 AzCopy que suporta a cópia de dados de/para o serviço de armazenamento de tabelas do Azure de Microsoft](https://aka.ms/downloadazcopynet).

## <a name="post-installation-steps"></a>Passos de pós-instalação

AzCopy v10 não necessita de uma instalação. Abra uma aplicação de linha de comando preferencial e navegue para a pasta onde `azcopy.exe` (Windows) ou `azcopy` executável (Linux) está localizado. Se assim o desejar, pode adicionar a localização da pasta AzCopy ao caminho do sistema.

## <a name="authentication-options"></a>Opções de autenticação

AzCopy v10 permite-lhe utilizar as opções seguintes quando a autenticação com o armazenamento do Azure:
- **O Azure Active Directory [suportados para os serviços de Blob e de geração 2 do ADLS]**. Utilize ```.\azcopy login``` para iniciar sessão com o Azure Active Directory.  O utilizador deve ter [função de "Contribuinte de dados de Blob de armazenamento" atribuída](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) ao escrever no armazenamento de Blobs com a autenticação do Azure Active Directory. Para autenticar com a identidade de serviço gerida (MSI), utilize `azcopy login --identity` depois de conceder a instância de computação do Azure a função de contribuinte de dados.
- **Tokens de SAS [suportados para serviços de ficheiros e BLOBs]**. Acrescente o token SAS para o caminho de blob na linha de comando para utilizá-lo. Pode gerar o token SAS através do Portal do Azure, [Explorador de armazenamento](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/), [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageblobsastoken), ou outras ferramentas da sua preferência. Para obter mais informações, consulte [exemplos](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).

## <a name="getting-started"></a>Introdução

> [!TIP]
> **Prefere uma interface gráfica do usuário?**
>
> Tente [Explorador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/), um cliente de desktop que simplifica a gerir dados de armazenamento do Azure, e **utiliza agora o AzCopy** para acelerar a transferência de dados para dentro e fora do armazenamento do Azure.
>
> Simplesmente habilite o recurso de AzCopy no Explorador de armazenamento no menu de "Preview". Explorador de armazenamento, em seguida, irá utilizar o AzCopy durante o carregamento e transferência de dados para armazenamento de BLOBs para um melhor desempenho.
> ![Ativar o AzCopy como um motor de transferência no Explorador de armazenamento do Azure](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

AzCopy v10 tem uma sintaxe simple de Self-documentada. A sintaxe geral será semelhante ao seguinte quando tiver sessão iniciada no Azure Active Directory:

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>
# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you are using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?sastoken" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?sastoken"
```

Eis como pode obter uma lista de comandos disponíveis:

```azcopy
.\azcopy --help
# Using the alias instead
.\azcopy -h
```

Para ver a página de ajuda e exemplos para um comando específico, execute o comando abaixo:

```azcopy
.\azcopy <cmd> --help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>Criar um contentor de BLOBs ou uma partilha de ficheiros 

**Criar um contentor de BLOBs**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**Criar uma partilha de ficheiros**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**Criar um contentor de Blobs com o ADLS Gen2**

Se tiver ativado o espaços de nomes hierárquicos na sua conta de armazenamento de BLOBs, pode utilizar o seguinte comando para criar um novo sistema de ficheiros (contentor de BLOBs), de modo a que pode carregar ficheiros para o mesmo.

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>Copiar dados para o armazenamento do Azure

Utilize o comando de cópia para transferir dados da origem para o destino. A origem/destino podem ser:
- Sistema de ficheiros local
- Diretório/contentor de Blobs do Azure/Virtual URI
- Partilha de ficheiro/diretório/ficheiro URI do Azure
- URI de sistema de ficheiros/diretório/ficheiro de geração 2 de Lake armazenamento de dados do Azure

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using alias instead
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

O comando seguinte carrega todos os ficheiros na pasta `C:\local\path` recursivamente para o contentor `mycontainer1` criando `path` diretório no contentor:

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

O comando seguinte carrega todos os ficheiros na pasta `C:\local\path` (sem recursing para os subdiretórios) para o contentor `mycontainer1`:

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>"
```

Para obter mais exemplos, utilize o seguinte comando:

```azcopy
.\azcopy cp -h
```

## <a name="copy-data-between-two-storage-accounts"></a>Copiar dados entre duas contas de armazenamento

Copiar dados entre duas contas de armazenamento utiliza a [colocar o bloco de URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API e não utiliza largura de banda de rede de máquina cliente. Dados são copiados entre dois servidores de armazenamento do Azure diretamente enquanto AzCopy simplesmente orquestra a operação de cópia. Atualmente esta opção só está disponível para armazenamento de Blobs.

Para copiar os dados entre duas contas de armazenamento, utilize o seguinte comando:
```azcopy
.\azcopy cp "https://account.blob.core.windows.net/<sastoken>" "https://otheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

> [!NOTE]
> O comando irá enumerar todos os contentores de BLOBs e copiá-los para a conta de destino. Neste momento AzCopy v10 suporta apenas blobs de blocos entre contas de armazenamento de dois a copiar. Todos os outros objetos de conta de armazenamento (blobs, blobs de páginas, ficheiros, tabelas e filas de acréscimo) serão ignorados.

## <a name="copy-a-vhd-image-to-a-storage-account"></a>Copiar uma imagem VHD para uma conta de armazenamento

Utilize `--blob-type=PageBlob` para carregar uma imagem de disco para armazenamento de BLOBs como um Blob de página.

```azcopy
.\azcopy cp "C:\myimages\diskimage.vhd" "https://account.blob.core.windows.net/mycontainer/diskimage.vhd<sastoken>" --blob-type=PageBlob
```

## <a name="sync-incremental-copy-and-optional-delete-blob-storage-only"></a>Sincronização: cópia incremental e delete (opcional) (apenas para armazenamento de BLOBs)

Comando de sincronização sincroniza o conteúdo de um diretório de origem para um diretório na última carimbos de data / modificada e nomes de ficheiro comparando de destino. Opcionalmente, esta operação inclui a eliminação de ficheiros de destino se elas não existam na origem quando `--delete-destination=prompt|true` sinalizador é fornecido. Por predefinição, o comportamento de eliminação está desativado.

> [!NOTE]
> Utilize `--delete-destination` sinalizador com cuidado. Ativar [eliminação de forma recuperável](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) de recursos antes de ativar o comportamento de eliminação em sincronia, para impedir eliminações acidentais na sua conta.
>
> Quando `--delete-destination` está definido como true, o AzCopy irá eliminar os ficheiros que não existem na origem do destino sem qualquer pedido ao utilizador. Se gostaria de pedido de confirmação, utilize `--delete-destination=prompt`.

Para sincronizar o seu sistema de ficheiros local para uma conta de armazenamento, utilize o seguinte comando:

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer<sastoken>"
```

Da mesma forma pode sincronizar um contentor de BLOBs para baixo para um sistema de arquivos local:

```azcopy
# If you're using Azure Active Directory authentication the sastoken is not required
.\azcopy sync "https://account.blob.core.windows.net/mycontainer" "C:\local\path"
```

O comando permite-lhe uma forma incremental a sincronizar a origem para o destino com base na última carimbos de data / modificada. Se adicionar ou eliminar um ficheiro na origem, o AzCopy v10 irá fazer o mesmo no destino. Se o comportamento de eliminação estiver ativado no comando de sincronização, o AzCopy irá eliminar ficheiros de destino se eles já não existe na origem.

## <a name="advanced-configuration"></a>Configuração avançada

### <a name="configure-proxy-settings"></a>Configurar definições de proxy

Para configurar as definições de proxy para AzCopy v10, defina o https_proxy de variável de ambiente com o seguinte comando:

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>Otimizar o débito

Defina a variável de ambiente AZCOPY_CONCURRENCY_VALUE para configurar o número de pedidos simultâneos e controlar o desempenho de débito e o consumo de recursos. O valor é definido para 300 por predefinição. Reduzir o valor irá limitar a largura de banda e CPU utilizado pelo v10 de AzCopy.

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
# If the value is blank then the default value is currently in use
```

### <a name="change-the-default-log-level"></a>Alterar o nível de registo predefinido

Por predefinição, o nível de registo de AzCopy está definido para informações. Se gostaria de reduzir a verbosidade de registo para poupar espaço em disco, substituir a definição utilizando ``--log-level`` opção. Níveis de registo disponíveis são: DEBUG, INFO, aviso, erro, PÂNICO e FATAL

## <a name="troubleshooting"></a>Resolução de problemas

AzCopy v10 cria ficheiros de registo e ficheiros de plano para todas as tarefas. Pode utilizar os registos para investigar e resolver quaisquer problemas potenciais. Os registos irão conter o estado de falha (UPLOADFAILED COPYFAILED e DOWNLOADFAILED), o caminho completo e o motivo da falha. Os registos da tarefa e os ficheiros de plano estão localizados em % USERPROFILE %\\.azcopy pasta no Windows ou $HOME\\.azcopy pasta em Mac e Linux.

> [!IMPORTANT]
> Ao submeter um pedido de suporte para Microsoft Support (ou o problema que envolvem qualquer parte 3 de resolução de problemas). a versão eliminada do comando que está a tentar executar para garantir que a SAS não é acidentalmente partilhada com qualquer pessoa de partilha. Pode encontrar a versão eliminada no início do ficheiro de registo.

### <a name="review-the-logs-for-errors"></a>Rever os registos de erros

O seguinte comando irá obter todos os erros com o estado UPLOADFAILED do 04dc9ca9-158f-7945-5933-564021086c79 log:

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```

Em alternativa pode ver os nomes de ficheiro que não são transferidos com `azcopy jobs show <jobid> --with-status=Failed` comando.

### <a name="view-and-resume-jobs"></a>Ver e retomar tarefas

Cada operação de transferência irá criar uma tarefa de AzCopy. Pode ver o histórico de tarefas utilizando o seguinte comando:

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

É possível retomar uma tarefa de falha/cancelado com o respetivo identificador, juntamente com o token SAS (não é persistente por motivos de segurança):

```azcopy
.\azcopy jobs resume <jobid> --sourcesastokenhere --destinationsastokenhere
```

## <a name="next-steps"></a>Passos Seguintes

Sempre é welcomed seus comentários. Se tiver alguma questão, questões ou comentários gerais submetê-las em https://github.com/Azure/azure-storage-azcopy. Obrigado!
