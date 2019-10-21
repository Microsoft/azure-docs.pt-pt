---
title: Copiar ou mover dados para o armazenamento do Azure usando AzCopy v10 | Microsoft Docs
description: AzCopy é um utilitário de linha de comando que você pode usar para copiar dados para, de ou entre as contas de armazenamento. Este artigo ajuda você a baixar o AzCopy, conectar-se à sua conta de armazenamento e, em seguida, transferir arquivos.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 37d8c483a1ef49a87205dcc848aae974f5b0e47e
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675868"
---
# <a name="get-started-with-azcopy"></a>Introdução ao AzCopy

AzCopy é um utilitário de linha de comando que você pode usar para copiar BLOBs ou arquivos de ou para uma conta de armazenamento. Este artigo ajuda você a baixar o AzCopy, conectar-se à sua conta de armazenamento e, em seguida, transferir arquivos.

> [!NOTE]
> AzCopy **V10** é a versão com suporte no momento do AzCopy.
>
> Se você precisar usar o AzCopy **v 8.1**, consulte a seção [usar a versão anterior do AzCopy](#previous-version) deste artigo.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Baixar o AzCopy

Primeiro, baixe o arquivo executável AzCopy V10 em qualquer diretório em seu computador. AzCopy V10 é apenas um arquivo executável, portanto, não há nada a ser instalado.

- [Windows de 64 bits](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows de 32 bits](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Esses arquivos são compactados como um arquivo zip (Windows e Mac) ou um arquivo tar (Linux).

Você pode usar esses comandos para baixar e descompactar o arquivo tar no Linux.

```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopy-v10-linux
tar -xf azcopy.tar.gz
```

> [!NOTE]
> Se você quiser copiar dados de e para o serviço de [armazenamento de tabelas do Azure](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) , instale o [AzCopy versão 7,3](https://aka.ms/downloadazcopynet).


## <a name="run-azcopy"></a>Executar AzCopy

Para sua conveniência, considere adicionar o local do diretório do executável AzCopy ao caminho do sistema para facilitar o uso. Dessa forma, você pode digitar `azcopy` de qualquer diretório em seu sistema.

Se optar por não adicionar o diretório AzCopy ao seu caminho, você precisará alterar os diretórios para o local do executável do AzCopy e digitar `azcopy` ou `.\azcopy` nos prompts de comando do Windows PowerShell.

Para ver uma lista de comandos, digite `azcopy -h` e pressione a tecla ENTER.

Para saber mais sobre um comando específico, apenas inclua o nome do comando (por exemplo: `azcopy list -h`).

![Ajuda embutida](media/storage-use-azcopy-v10/azcopy-inline-help.png)

> [!NOTE] 
> Como proprietário de sua conta de armazenamento do Azure, você não recebe automaticamente permissões para acessar dados. Antes de poder fazer qualquer coisa significativa com o AzCopy, você precisa decidir como fornecerá credenciais de autorização para o serviço de armazenamento. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Escolha como você fornecerá credenciais de autorização

Você pode fornecer credenciais de autorização usando o Azure Active Directory (AD) ou usando um token SAS (assinatura de acesso compartilhado).

Use esta tabela como um guia:

| Tipo de armazenamento | Método de autorização atualmente com suporte |
|--|--|
|**Armazenamento de blobs** | SAS do Azure AD & |
|**Armazenamento de BLOBs (namespace de hierarquia)** | SAS do Azure AD & |
|**Armazenamento de arquivos** | Somente SAS |

### <a name="option-1-use-azure-active-directory"></a>Opção 1: usar Azure Active Directory

Usando Azure Active Directory, você pode fornecer credenciais uma vez, em vez de ter que acrescentar um token SAS a cada comando.  

> [!NOTE]
> Na versão atual, se você planeja copiar BLOBs entre contas de armazenamento, precisará acrescentar um token SAS a cada URL de origem. Você pode omitir o token SAS somente da URL de destino. Para obter exemplos, consulte [copiar BLOBs entre contas de armazenamento](storage-use-azcopy-blobs.md).

O nível de autorização de que você precisa se baseia se você planeja carregar arquivos ou apenas baixá-los.

Se você apenas deseja baixar arquivos, verifique se o leitor de [dados de blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) foi atribuído à sua identidade de usuário, identidade gerenciada ou entidade de serviço.

> Identidades de usuário, identidades gerenciadas e entidades de serviço são cada um tipo de *entidade de segurança*, portanto, usaremos a *entidade de segurança* do termo para o restante deste artigo.

Se você quiser carregar arquivos, verifique se uma dessas funções foi atribuída à sua entidade de segurança:

- [Colaborador de dados do blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Proprietário de dados do blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Essas funções podem ser atribuídas à entidade de segurança em qualquer um desses escopos:

- Contêiner (sistema de arquivos)
- Conta de armazenamento
- Grupo de recursos
- Subscrição

Para saber como verificar e atribuir funções, consulte [conceder acesso ao blob do Azure e dados de fila com RBAC no portal do Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Tenha em mente que as atribuições de função do RBAC podem levar até cinco minutos para serem propagadas.

Você não precisará ter uma dessas funções atribuídas à entidade de segurança se sua entidade de segurança for adicionada à lista de controle de acesso (ACL) do contêiner ou diretório de destino. Na ACL, sua entidade de segurança precisa de permissão de gravação no diretório de destino e permissão de execução no contêiner e em cada diretório pai.

Para saber mais, consulte [controle de acesso em Azure data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-a-user-identity"></a>Autenticar uma identidade de usuário

Depois de verificar se a identidade do usuário recebeu o nível de autorização necessário, abra um prompt de comando, digite o comando a seguir e pressione a tecla ENTER.

```azcopy
azcopy login
```

Se você pertencer a mais de uma organização, inclua a ID de locatário da organização à qual a conta de armazenamento pertence.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Substitua o espaço reservado `<tenant-id>` pela ID de locatário da organização à qual a conta de armazenamento pertence. Para localizar a ID de locatário, selecione **Azure Active Directory Propriedades de > > ID de diretório** no portal do Azure.

Esse comando retorna um código de autenticação e a URL de um site. Abra o site, forneça o código e, em seguida, escolha o botão **Avançar** .

![Criar um contentor](media/storage-use-azcopy-v10/azcopy-login.png)

Uma janela de entrada será exibida. Nessa janela, entre em sua conta do Azure usando suas credenciais de conta do Azure. Depois de entrar com êxito, você pode fechar a janela do navegador e começar a usar o AzCopy.

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>Autenticar uma entidade de serviço

Essa é uma ótima opção se você planeja usar o AzCopy dentro de um script que é executado sem interação do usuário, especialmente quando executado no local. Se você planeja executar o AzCopy em VMs que são executadas no Azure, uma identidade de serviço gerenciada é mais fácil de administrar. Para saber mais, consulte a seção [autenticar uma identidade gerenciada](#managed-identity) deste artigo.

Antes de executar um script, você precisa entrar interativamente pelo menos uma vez para que possa fornecer AzCopy com as credenciais de sua entidade de serviço.  Essas credenciais são armazenadas em um arquivo seguro e criptografado para que o script não precise fornecer essas informações confidenciais.

Você pode entrar em sua conta usando um segredo do cliente ou usando a senha de um certificado associado ao registro do aplicativo da sua entidade de serviço.

Para saber mais sobre como criar a entidade de serviço, consulte [como: usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que possa acessar recursos](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Para saber mais sobre as entidades de serviço em geral, consulte [objetos de aplicativo e entidade de serviço no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>Usando um segredo do cliente

Comece definindo a variável de ambiente `AZCOPY_SPA_CLIENT_SECRET` como o segredo do cliente do registro do aplicativo da sua entidade de serviço.

> [!NOTE]
> Certifique-se de definir esse valor no prompt de comando e não nas configurações de variável de ambiente do seu sistema operacional. Dessa forma, o valor estará disponível somente para a sessão atual.

Este exemplo mostra como você pode fazer isso no PowerShell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Considere usar um prompt, conforme mostrado neste exemplo. Dessa forma, sua senha não aparecerá no histórico de comandos do console.  

Em seguida, digite o comando a seguir e pressione a tecla ENTER.

```azcopy
azcopy login --service-principal --application-id <application-id> --tenant-id=<tenant-id>
```

Substitua o espaço reservado `<application-id>` pela ID do aplicativo do registro do aplicativo da sua entidade de serviço. Substitua o espaço reservado `<tenant-id>` pela ID de locatário da organização à qual a conta de armazenamento pertence. Para localizar a ID de locatário, selecione **Azure Active Directory Propriedades de > > ID de diretório** no portal do Azure. 

##### <a name="using-a-certificate"></a>Usando um certificado

Se preferir usar suas próprias credenciais para autorização, você poderá carregar um certificado para o registro do aplicativo e, em seguida, usar esse certificado para fazer logon.

Além de carregar seu certificado para o registro do aplicativo, você também precisará ter uma cópia do certificado salvo no computador ou na VM em que o AzCopy será executado. Esta cópia do certificado deve estar no. PFX ou. O formato PEM e deve incluir a chave privada. A chave privada deve ser protegida por senha. Se você estiver usando o Windows e seu certificado existir somente em um repositório de certificados, certifique-se de exportar esse certificado para um arquivo PFX (incluindo a chave privada). Para obter diretrizes, consulte [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

Em seguida, defina a variável de ambiente `AZCOPY_SPA_CERT_PASSWORD` como a senha do certificado.

> [!NOTE]
> Certifique-se de definir esse valor no prompt de comando e não nas configurações de variável de ambiente do seu sistema operacional. Dessa forma, o valor estará disponível somente para a sessão atual.

Este exemplo mostra como você pode executar essa tarefa no PowerShell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Em seguida, digite o comando a seguir e pressione a tecla ENTER.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

Substitua o espaço reservado `<path-to-certificate-file>` pelo caminho relativo ou totalmente qualificado para o arquivo de certificado. AzCopy salva o caminho para esse certificado, mas não salva uma cópia do certificado, portanto, certifique-se de manter esse certificado em vigor. Substitua o espaço reservado `<tenant-id>` pela ID de locatário da organização à qual a conta de armazenamento pertence. Para localizar a ID de locatário, selecione **Azure Active Directory Propriedades de > > ID de diretório** no portal do Azure.

> [!NOTE]
> Considere usar um prompt, conforme mostrado neste exemplo. Dessa forma, sua senha não aparecerá no histórico de comandos do console. 

<a id="managed-identity" />

#### <a name="authenticate-a-managed-identity"></a>Autenticar uma identidade gerenciada

Essa é uma ótima opção se você planeja usar o AzCopy dentro de um script que é executado sem interação do usuário e o script é executado de uma VM (máquina virtual) do Azure. Ao usar essa opção, você não precisará armazenar nenhuma credencial na VM.

Você pode entrar em sua conta usando a identidade gerenciada de todo o sistema que você habilitou em sua VM ou usando a ID do cliente, a ID de objeto ou a ID de recurso de uma identidade gerenciada atribuída pelo usuário que você atribuiu à sua VM.

Para saber mais sobre como habilitar uma identidade gerenciada em todo o sistema ou criar uma identidade gerenciada atribuída pelo usuário, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).

##### <a name="using-a-system-wide-managed-identity"></a>Usando uma identidade gerenciada em todo o sistema

Primeiro, verifique se você habilitou uma identidade gerenciada em todo o sistema em sua VM. Consulte [identidade gerenciada atribuída pelo sistema](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity).

Em seguida, no console de comando, digite o comando a seguir e pressione a tecla ENTER.

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>Usando uma identidade gerenciada atribuída pelo usuário

Primeiro, verifique se você habilitou uma identidade gerenciada atribuída pelo usuário em sua VM. Consulte [identidade gerenciada atribuída pelo usuário](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity).

Em seguida, no console de comando, digite qualquer um dos comandos a seguir e pressione a tecla ENTER.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

Substitua o espaço reservado `<client-id>` pela ID do cliente da identidade gerenciada atribuída pelo usuário.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

Substitua o espaço reservado `<object-id>` pela ID de objeto da identidade gerenciada atribuída pelo usuário.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

Substitua o espaço reservado `<resource-id>` pela ID de recurso da identidade gerenciada atribuída pelo usuário.

### <a name="option-2-use-a-sas-token"></a>Opção 2: usar um token SAS

Você pode acrescentar um token SAS a cada URL de origem ou de destino que usa em seus comandos AzCopy.

Esse comando de exemplo copia recursivamente os dados de um diretório local para um contêiner de BLOB. Um token SAS fictício é acrescentado ao final do da URL do contêiner.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Para saber mais sobre tokens SAS e como obter um, consulte [usando assinaturas de acesso compartilhado (SAS)](https://docs.microsoft.com/azure/storage/common/storage-sas-overview).

## <a name="transfer-files"></a>Transferir arquivos

Depois de autenticar sua identidade ou obter um token SAS, você pode começar a transferir arquivos.

Para encontrar comandos de exemplo, consulte qualquer um desses artigos.

- [Transferir dados com o armazenamento de BLOBs e AzCopy](storage-use-azcopy-blobs.md)

- [Transferir dados com o AzCopy e o armazenamento de arquivos](storage-use-azcopy-files.md)

- [Transferir dados com os buckets AzCopy e Amazon S3](storage-use-azcopy-s3.md)

- [Transferir dados com AzCopy e armazenamento de Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>Usar AzCopy em um script

### <a name="obtain-a-static-download-link"></a>Obter um link de download estático

Ao longo do tempo, o [link de download](#download-and-install-azcopy) do AzCopy apontará para novas versões do AzCopy. Se o script baixar AzCopy, o script poderá parar de funcionar se uma versão mais recente do AzCopy modificar os recursos dependentes do seu script.

Para evitar esses problemas, obtenha um link estático (sem alteração) para a versão atual do AzCopy. Dessa forma, o script baixará a mesma versão exata do AzCopy cada vez que for executado.

Para obter o link, execute este comando:

| Sistema operativo  | Comando |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> Para o Linux, `--strip-components=1` no comando `tar` remove a pasta de nível superior que contém o nome da versão e, em vez disso, extrai o binário diretamente na pasta atual. Isso permite que o script seja atualizado com uma nova versão do `azcopy` atualizando apenas a URL `wget`.

A URL aparece na saída deste comando. O script pode então baixar o AzCopy usando essa URL.

| Sistema operativo  | Comando |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>Caracteres especiais de escape em tokens SAS

Em arquivos em lotes que têm a extensão `.cmd`, você terá que escapar os caracteres de `%` que aparecem em tokens SAS. Você pode fazer isso adicionando um caractere de `%` de adição ao lado dos caracteres `%` existentes na cadeia de caracteres do token SAS.

### <a name="run-scripts-by-using-jenkins"></a>Executar scripts usando Jenkins

Se você planeja usar o [Jenkins](https://jenkins.io/) para executar scripts, certifique-se de colocar o comando a seguir no início do script.

```
/usr/bin/keyctl new_session
```

## <a name="use-azcopy-in-storage-explorer"></a>Usar AzCopy no Gerenciador de Armazenamento

Se você quiser aproveitar as vantagens de desempenho do AzCopy, mas preferir usar Gerenciador de Armazenamento em vez da linha de comando para interagir com seus arquivos, habilite AzCopy no Gerenciador de Armazenamento.

Em Gerenciador de Armazenamento, escolha **visualização**->**use AzCopy para upload e download de blob aprimorados**.

![Habilitar o AzCopy como um mecanismo de transferência no Gerenciador de Armazenamento do Azure](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> Você não precisará habilitar essa configuração se tiver habilitado um namespace hierárquico em sua conta de armazenamento. Isso ocorre porque Gerenciador de Armazenamento usa automaticamente AzCopy em contas de armazenamento que têm um namespace hierárquico.  

Gerenciador de Armazenamento usa sua chave de conta para executar operações, portanto, depois de entrar no Gerenciador de Armazenamento, você não precisará fornecer credenciais de autorização adicionais.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Usar a versão anterior do AzCopy

Se você precisar usar a versão anterior do AzCopy (AzCopy v 8.1), consulte um dos links a seguir:

- [AzCopy no Windows (V8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy no Linux (V8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurar, otimizar e solucionar problemas do AzCopy

Consulte [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>Passos seguintes

Se você tiver dúvidas, problemas ou comentários gerais, envie-os [na página do GitHub](https://github.com/Azure/azure-storage-azcopy) .
