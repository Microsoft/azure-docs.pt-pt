---
title: Copiar ou mover dados para o armazenamento do Azure com AzCopy v10 | Documentos da Microsoft
description: O AzCopy é um utilitário de linha de comandos que pode utilizar para copiar dados para, de ou entre contas de armazenamento. Este artigo ajuda-o a transferir o AzCopy, ligue à sua conta de armazenamento e, em seguida, transferir ficheiros.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: bfa3e5a943ee59b1ed335f45e113a60f62572675
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735019"
---
# <a name="get-started-with-azcopy"></a>Introdução ao AzCopy

O AzCopy é um utilitário de linha de comandos que pode utilizar para copiar blobs ou ficheiros de ou para uma conta de armazenamento. Este artigo ajuda-o a transferir o AzCopy, ligue à sua conta de armazenamento e, em seguida, transferir ficheiros.

> [!NOTE]
> AzCopy **V10** é a versão atualmente suportada do AzCopy.
>
> Se precisar de utilizar o AzCopy **v8.1**, consulte a [utilizar a versão anterior do AzCopy](#previous-version) seção deste artigo.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Baixe o AzCopy

Em primeiro lugar, transfira o ficheiro de executável do AzCopy V10 para qualquer diretório no seu computador. 

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (destino)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

> [!NOTE]
> Se pretender copiar dados de e para seus [armazenamento de tabelas do Azure](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) do serviço, em seguida, instale [AzCopy versão 7.3](https://aka.ms/downloadazcopynet).

## <a name="run-azcopy"></a>Executar o AzCopy

Para sua comodidade, considere a adicionar a localização do diretório do executável AzCopy ao caminho do sistema para facilidade de utilização. Dessa forma, pode digitar `azcopy` partir de qualquer diretório no seu sistema.

Se optar por não adicionar o diretório de AzCopy ao caminho, terá mude de diretório para a localização do seu tipo e o executável do AzCopy `azcopy` ou `.\azcopy` no prompts de comando do Windows PowerShell.

Para ver uma lista de comandos, escreva `azcopy -h` e, em seguida, prima a tecla ENTER.

Para saber mais sobre um comando específico, basta incluir o nome do comando (por exemplo: `azcopy list -h`).

![Ajuda inline](media/storage-use-azcopy-v10/azcopy-inline-help.png)

Antes de poder fazer algo significativo com o AzCopy, precisa decidir como irá fornecer credenciais de autorização para o serviço de armazenamento.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Escolha a forma como irá fornecer credenciais de autorização

Pode fornecer credenciais de autorização com o Azure Active Directory (AD) ou através de um token de assinatura de acesso partilhado (SAS).

Utilize esta tabela como guia:

| Tipo de armazenamento | Método atualmente suportado de autorização |
|--|--|
|**Armazenamento de blobs** | Azure AD & SAS |
|**Armazenamento de BLOBs (espaço de nomes hierárquicas)** | Apenas do Azure AD |
|**Armazenamento de ficheiros** | Apenas a SAS |

### <a name="option-1-use-azure-ad"></a>Opção 1: Utilizar o Azure AD

O nível de autorização que terá de se baseia em se planeia carregar ficheiros ou apenas transferi-los.

#### <a name="authorization-to-upload-files"></a>Autorização para carregar ficheiros

Certifique-se de que uma destas funções foi atribuída à sua identidade:

- [Contribuinte de dados de Blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Proprietário de dados de Blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Estas funções podem ser atribuídas a sua identidade em qualquer uma nestes âmbitos:

- Contentor (sistema de ficheiros)
- Conta de armazenamento
- Grupo de recursos
- Subscrição

Para saber como verificar e atribuir funções, veja [conceder acesso a dados BLOBs e filas do Azure com o RBAC no portal do Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Não precisa de ter uma destas funções atribuídas a sua identidade, se a sua identidade é adicionada à lista de controlo de acesso (ACL) do contentor de destino ou diretório. Na ACL, sua identidade precisa de permissão de escrita no diretório de destino e permissão de execução no contentor e cada diretório principal.

Para obter mais informações, consulte [controlo de acesso no Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authorization-to-download-files"></a>Autorização para transferir ficheiros

Certifique-se de que uma destas funções foi atribuída à sua identidade:

- [Leitor de dados de Blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)
- [Contribuinte de dados de Blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Proprietário de dados de Blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Estas funções podem ser atribuídas a sua identidade em qualquer uma nestes âmbitos:

- Contentor (sistema de ficheiros)
- Conta de armazenamento
- Grupo de recursos
- Subscrição

Para saber como verificar e atribuir funções, veja [conceder acesso a dados BLOBs e filas do Azure com o RBAC no portal do Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Não precisa de ter uma destas funções atribuídas a sua identidade, se a sua identidade é adicionada à lista de controlo de acesso (ACL) do contentor de destino ou diretório. Na ACL, sua identidade precisa de permissão de leitura no diretório de destino e permissão de execução no contentor e cada diretório principal.

Para obter mais informações, consulte [controlo de acesso no Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-your-identity"></a>Autenticar a sua identidade

Depois de verificar que foi dada o nível de autorização necessário sua identidade, abra uma linha de comandos, escreva o seguinte comando e, em seguida, prima a tecla ENTER.

```azcopy
azcopy login
```

Este comando devolve um código de autenticação e o URL de um Web site. Abra o Web site, fornecer o código e, em seguida, escolha o **seguinte** botão.

![Criar um contentor](media/storage-use-azcopy-v10/azcopy-login.png)

Será apresentada uma janela de início de sessão. Nessa janela, inicie sessão na sua conta do Azure, utilizando as credenciais da conta do Azure. Depois de se com êxito, pode fechar a janela do browser e começar a utilizar o AzCopy.

### <a name="option-2-use-a-sas-token"></a>Opção 2: Utilizar um token SAS

Pode acrescentar um token SAS para cada URL de origem ou de destino que utilizam nos seus comandos do AzCopy.

Recursivamente de comando neste exemplo copia dados de um diretório local para um contentor de Blobs. Um token SAS fictício é acrescentado ao fim da do URL do contentor.

```azcopy
azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Para saber mais sobre os tokens SAS e como obter uma, veja [Using partilhado assinaturas de acesso (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="transfer-files"></a>Transferir ficheiros

Depois de autenticado sua identidade ou de obteve um token SAS, pode começar a transferir ficheiros.

Para encontrar os comandos de exemplo, veja qualquer um destes artigos.

- [Transferir dados com AzCopy e armazenamento de BLOBs](storage-use-azcopy-blobs.md)

- [Transferir dados com AzCopy e o ficheiro de armazenamento](storage-use-azcopy-files.md)

- [Transferir dados com AzCopy e o Amazon S3 registos](storage-use-azcopy-s3.md)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurar, otimizar e resolver problemas relacionados com o AzCopy

Consulte [configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="use-azcopy-in-storage-explorer"></a>Utilizar o AzCopy no Explorador de armazenamento

Se desejar aproveitar as vantagens de desempenho do AzCopy, mas preferir usar o Explorador de armazenamento, em vez da linha de comandos para interagir com os ficheiros, em seguida, ative o AzCopy no Explorador de armazenamento.

No Explorador de armazenamento, escolha **pré-visualização**->**AzCopy de utilização para aprimorado a carregar blobs e a transferência**.

![Ativar o AzCopy como um motor de transferência no Explorador de armazenamento do Azure](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> Não tem de ativar esta definição se ativou um espaço de nomes hierárquico na sua conta de armazenamento. Isso ocorre porque o Explorador de armazenamento utiliza automaticamente o AzCopy em contas de armazenamento que têm um espaço de nomes hierárquico.  

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Utilizar a versão anterior do AzCopy

Se precisar de utilizar a versão anterior do AzCopy (AzCopy v8.1), veja qualquer um dos links a seguir:

- [AzCopy no Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)
- [AzCopy no Linux (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>Passos Seguintes

Se tiver dúvidas, questões ou comentários gerais, submetê-las [no GitHub](https://github.com/Azure/azure-storage-azcopy) página.
