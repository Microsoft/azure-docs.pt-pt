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
ms.openlocfilehash: 94aca33b2f12c1c39297221a856296dcca052b0f
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565807"
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

> [!NOTE] 
> Como proprietário da conta de armazenamento do Azure, não são atribuídos automaticamente permissões para aceder aos dados. Antes de poder fazer algo significativo com o AzCopy, precisa decidir como irá fornecer credenciais de autorização para o serviço de armazenamento. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Escolha a forma como irá fornecer credenciais de autorização

Pode fornecer credenciais de autorização com o Azure Active Directory (AD) ou através de um token de assinatura de acesso partilhado (SAS).

Utilize esta tabela como guia:

| Tipo de armazenamento | Método atualmente suportado de autorização |
|--|--|
|**Armazenamento de blobs** | Azure AD & SAS |
|**Armazenamento de BLOBs (espaço de nomes hierárquicas)** | Azure AD & SAS |
|**Armazenamento de ficheiros** | Apenas a SAS |

### <a name="option-1-use-azure-ad"></a>Opção 1: Utilizar o Azure AD

Ao utilizar o Azure AD, pode fornecer credenciais de uma vez, em vez de precisar acrescentar um token SAS para cada comando.  

O nível de autorização que terá de se baseia em se planeia carregar ficheiros ou apenas transferi-los.

Se apenas pretender transferir os ficheiros, em seguida, certifique-se de que o [leitor de dados de Blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) foi atribuída a sua identidade de utilizador ou principal de serviço. 

> [!NOTE]
> As identidades dos utilizadores e os principais de serviço são cada um tipo de *entidade de segurança*, por isso, usaremos o termo *entidade de segurança* durante o restante deste artigo.

Se pretender carregar ficheiros, em seguida, certifique-se de que uma destas funções foi atribuída a sua entidade de segurança:

- [Contribuinte de dados de Blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Proprietário de dados de Blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Estas funções podem ser atribuídas a sua identidade em qualquer uma nestes âmbitos:

- Contentor (sistema de ficheiros)
- Conta de armazenamento
- Grupo de recursos
- Subscrição

Para saber como verificar e atribuir funções, veja [conceder acesso a dados BLOBs e filas do Azure com o RBAC no portal do Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE] 
> Tenha em atenção que as atribuições de funções do RBAC podem demorar até cinco minutos para propagar.

Não precisa de ter uma destas funções atribuídas a sua entidade de segurança, se a sua entidade de segurança é adicionada à lista de controlo de acesso (ACL) do contentor de destino ou diretório. Na ACL, sua entidade de segurança precisa de permissão de escrita no diretório de destino e permissão de execução no contentor e cada diretório principal.

Para obter mais informações, consulte [controlo de acesso no Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-a-user-identity"></a>Autenticar uma identidade de utilizador

Depois de verificar que foi dada sua identidade de utilizador o nível de autorização necessário, abra uma linha de comandos, escreva o seguinte comando e, em seguida, prima a tecla ENTER.

```azcopy
azcopy login
```

Se pertence a mais do que uma organização, inclua o ID de inquilino da organização a que pertence a conta de armazenamento.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Substitua o `<tenant-id>` espaço reservado com o ID de inquilino da organização a que pertence a conta de armazenamento. Para localizar o ID de inquilino, selecione **do Azure Active Directory > propriedades > ID de diretório** no portal do Azure.

Este comando devolve um código de autenticação e o URL de um Web site. Abra o Web site, fornecer o código e, em seguida, escolha o **seguinte** botão.

![Criar um contentor](media/storage-use-azcopy-v10/azcopy-login.png)

Será apresentada uma janela de início de sessão. Nessa janela, inicie sessão na sua conta do Azure, utilizando as credenciais da conta do Azure. Depois de se com êxito, pode fechar a janela do browser e começar a utilizar o AzCopy.

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>Autenticar um principal de serviço

Esta é uma ótima opção se planear utilizar o AzCopy dentro de um script que é executada sem interação do utilizador. 

Antes de executar esse script, terá de iniciar sessão interativamente, pelo menos, uma vez, para que pode fornecer o AzCopy com as credenciais do principal de serviço.  Essas credenciais são armazenadas num ficheiro encriptado e protegido para que o seu script não tem de fornecer as informações confidenciais.

Pode iniciar sessão na sua conta com um segredo do cliente ou ao utilizar a palavra-passe de um certificado que está associado ao registo da aplicação do principal de serviço. 

Para saber mais sobre criar principal de serviço, consulte [como: Utilizar o portal para criar um Azure AD principal de aplicações e serviço que pode aceder a recursos](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Para obter mais informações sobre principais de serviço em geral, consulte [aplicação e objetos de principal de serviço no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>Usando um segredo do cliente

Comece definindo o `AZCOPY_SPA_CLIENT_SECRET` variável de ambiente para o segredo do cliente de seu principal de serviço do registo de aplicações. 

> [!NOTE]
> Certifique-se definir este valor de linha de comandos e não no ambiente de definições de variáveis do seu sistema operativo. Dessa forma, o valor está disponível apenas para a sessão atual.

Este exemplo mostra como pode fazer isso no PowerShell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Considere utilizar uma linha de comandos, como mostrado neste exemplo. Dessa forma, o segredo do cliente não será apresentado no histórico de comando da sua consola. 

Em seguida, escreva o seguinte comando e, em seguida, prima a tecla ENTER.

```azcopy
azcopy login --service-principal --application-id <application-id>
```

Substitua o `<application-id>` espaço reservado com o ID da aplicação do seu principal de serviço do registo de aplicações.

##### <a name="using-a-certificate"></a>Utilizar um certificado

Se preferir usar suas próprias credenciais para autorização, pode carregar um certificado para o registo de aplicação e, em seguida, utilizar esse certificado para o início de sessão.

Além de carregar o certificado para o registo de aplicação, terá também de ter uma cópia do certificado guardado para a máquina ou VM onde irá executar o AzCopy. Esta cópia do certificado deve estar no. PFX ou. PEM formate e tem de incluir a chave privada. A chave privada deve ser protegido por palavra-passe. Se estiver a utilizar o Windows e seu certificado só existe num arquivo de certificados, certifique-se exportar o certificado para um ficheiro PFX (incluindo a chave privada). Para obter orientações, veja [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

Em seguida, configure o `AZCOPY_SPA_CERT_PASSWORD` variável de ambiente para a palavra-passe do certificado.

> [!NOTE]
> Certifique-se definir este valor de linha de comandos e não no ambiente de definições de variáveis do seu sistema operativo. Dessa forma, o valor está disponível apenas para a sessão atual.

Este exemplo mostra como pode fazer isso no PowerShell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Em seguida, escreva o seguinte comando e, em seguida, prima a tecla ENTER.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file>
```

Substitua o `<path-to-certificate-file>` espaço reservado com o caminho relativo ou totalmente qualificado para o ficheiro de certificado. AzCopy salva o caminho para este certificado, mas ele não guarde uma cópia do certificado, certifique-se de manter esse certificado no local.

> [!NOTE]
> Considere utilizar uma linha de comandos, como mostrado neste exemplo. Dessa forma, a palavra-passe não será apresentado no histórico de comando da sua consola. 

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

- [Transferir dados com AzCopy e o Azure Stack de armazenamento](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>Utilizar o AzCopy num script

Antes de executar esse script, terá de iniciar sessão interativamente, pelo menos, uma vez, para que pode fornecer o AzCopy com as credenciais do principal de serviço.  Essas credenciais são armazenadas num ficheiro encriptado e protegido para que o seu script não tem de fornecer as informações confidenciais. Para obter exemplos, consulte a [autenticar seu principal de serviço](#service-principal) seção deste artigo.

Ao longo do tempo, o AzCopy [ligação de transferência](#download-and-install-azcopy) apontará para novas versões do AzCopy. Se o seu script downloads AzCopy, o script poderá parar de funcionar se a recursos que o script depende de modifica uma versão mais recente do AzCopy. 

Para evitar esses problemas, obtenha uma ligação (não alterar) estática para a versão atual do AzCopy. Dessa forma, o script transfere a mesma versão exata do AzCopy cada vez que ele é executado.

Para obter a ligação, execute este comando:

| Sistema operativo  | Comando |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> Para o Linux, `--strip-components=1` sobre o `tar` comando remove a pasta de nível superior que contém o nome da versão e, em vez disso, extrai o binário diretamente na pasta atual. Isso permite que o script para ser atualizado com uma nova versão do `azcopy` atualizando apenas a `wget` URL.

O URL apresentado na saída deste comando. O script, em seguida, pode transferir o AzCopy utilizando esse URL.

| Sistema operativo  | Comando |
|--------|-----------|
| **Linux** | `wget -O azcopyv10.tar https://azcopyvnext.azureedge.net/release20190301/azcopy_linux_amd64_10.0.8.tar.gz tar -xf azcopyv10.tar --strip-components=1 ./azcopy` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

## <a name="use-azcopy-in-storage-explorer"></a>Utilizar o AzCopy no Explorador de armazenamento

Se desejar aproveitar as vantagens de desempenho do AzCopy, mas preferir usar o Explorador de armazenamento, em vez da linha de comandos para interagir com os ficheiros, em seguida, ative o AzCopy no Explorador de armazenamento. 

No Explorador de armazenamento, escolha **pré-visualização**->**AzCopy de utilização para aprimorado a carregar blobs e a transferência**.

![Ativar o AzCopy como um motor de transferência no Explorador de armazenamento do Azure](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> Não tem de ativar esta definição se ativou um espaço de nomes hierárquico na sua conta de armazenamento. Isso ocorre porque o Explorador de armazenamento utiliza automaticamente o AzCopy em contas de armazenamento que têm um espaço de nomes hierárquico.  

Explorador de armazenamento utiliza a chave da conta para realizar operações como, por isso depois de iniciar sessão no Explorador de armazenamento, não terá de fornecer as credenciais de autorização adicionais.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Utilizar a versão anterior do AzCopy

Se precisar de utilizar a versão anterior do AzCopy (AzCopy v8.1), veja qualquer um dos links a seguir:

- [AzCopy no Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy no Linux (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurar, otimizar e resolver problemas relacionados com o AzCopy

Consulte [configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>Passos Seguintes

Se tiver dúvidas, questões ou comentários gerais, submetê-las [no GitHub](https://github.com/Azure/azure-storage-azcopy) página.
