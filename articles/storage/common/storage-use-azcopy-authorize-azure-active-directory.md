---
title: Autorizar o acesso a blobs com AzCopy & Azure Ative Directory [ Microsoft Docs
description: Pode fornecer credenciais de autorização para operações AzCopy utilizando o Azure Ative Directory (Azure AD).
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 89aab37b750a61bd21ba9af23875536a8cfbff4a
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414826"
---
# <a name="authorize-access-to-blobs-with-azcopy-and-azure-active-directory-azure-ad"></a>Autorizar o acesso a blobs com AzCopy e Azure Ative Directory (Azure AD)

Pode fornecer credenciais de autorização à AzCopy utilizando a Azure AD. Dessa forma, não terá de anexar uma assinatura de acesso partilhado (SAS) a cada comando. 

Comece por verificar as suas funções. Então, escolha o tipo de princípio de _segurança_ que pretende autorizar. Uma [identidade de utilizador](../../active-directory/fundamentals/add-users-azure-active-directory.md), uma identidade [gerida,](../../active-directory/managed-identities-azure-resources/overview.md)e um [principal de serviço](../../active-directory/develop/app-objects-and-service-principals.md) são cada um um tipo de princípio de segurança.

A identidade do utilizador é qualquer utilizador que tenha uma identidade no Azure AD. É o diretor de segurança mais fácil de autorizar. Identidades geridas e principais de serviço são ótimas opções se você planeja usar AzCopy dentro de um script que funciona sem interação do utilizador. Uma identidade gerida é mais adequada para scripts que funcionam a partir de uma Máquina Virtual Azure (VM), e um diretor de serviço é mais adequado para scripts que funcionam no local. 

Para mais informações sobre a AzCopy, [inicie com a AzCopy.](storage-use-azcopy-v10.md)

## <a name="verify-role-assignments"></a>Verificar atribuições de funções

O nível de autorização de que precisa baseia-se na possibilidade de fazer o upload de ficheiros ou simplesmente descarregá-los.

Se apenas pretender descarregar ficheiros, verifique se a função [de Leitor de Dados de Armazenamento blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) foi atribuída à identidade do utilizador, identidade gerida ou principal de serviço.

Se pretender fazer o upload de ficheiros, verifique se uma destas funções foi atribuída ao seu principal de segurança:

- [Contribuinte de Dados do Armazenamento de Blobs](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- [Proprietário dos Dados do Armazenamento de Blobs](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

Estas funções podem ser atribuídas ao seu principal de segurança em qualquer um destes âmbitos:

- Recipiente (sistema de ficheiros)
- Conta de armazenamento
- Grupo de recursos
- Subscrição

Para saber como verificar e atribuir funções, consulte [utilizar o portal Azure para atribuir uma função Azure para acesso a dados de bolhas e filas](./storage-auth-aad-rbac-portal.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json).

> [!NOTE]
> Tenha em mente que as atribuições de funções do Azure podem demorar até cinco minutos a propagar-se.

Não precisa de ter uma destas funções atribuídas ao seu diretor de segurança se o seu diretor de segurança for adicionado à lista de controlo de acesso (ACL) do contentor-alvo ou diretório. Na ACL, o seu diretor de segurança precisa de escrever permissão no directório-alvo, e executar permissão em contentores e cada diretório dos pais.

Para saber mais, consulte o [modelo de controlo de acesso no Azure Data Lake Storage Gen2](../blobs/data-lake-storage-access-control-model.md).

## <a name="authorize-a-user-identity"></a>Autorizar a identidade do utilizador

Depois de verificar que a sua identidade de utilizador recebeu o nível de autorização necessário, abra uma pressão de comando, digite o seguinte comando e, em seguida, prima a tecla ENTER.

```azcopy
azcopy login
```

Se receber um erro, tente incluir a identificação do inquilino da organização a que pertence a conta de armazenamento.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Substitua o `<tenant-id>` espaço reservado pelo ID do inquilino da organização a que pertence a conta de armazenamento. Para encontrar o ID do inquilino, selecione **Azure Ative Directory > Properties > ID do Diretório** no portal Azure.

Este comando devolve um código de autenticação e o URL de um website. Abra o site, forneça o código e, em seguida, escolha o botão **Seguinte.**

![Criar um contentor](media/storage-use-azcopy-v10/azcopy-login.png)

Aparecerá uma janela de inscrição. Nessa janela, inscreva-se na sua conta Azure utilizando as credenciais da sua conta Azure. Depois de ter assinado com sucesso, pode fechar a janela do navegador e começar a usar a AzCopy.

<a id="service-principal"></a>

## <a name="authorize-a-managed-identity"></a>Autorizar uma identidade gerida

Esta é uma ótima opção se planeia usar o AzCopy dentro de um script que funciona sem interação do utilizador, e o script é executado a partir de uma Máquina Virtual Azure (VM). Ao utilizar esta opção, não terá de armazenar credenciais no VM.

Pode inscrever-se na sua conta utilizando uma identidade gerida em todo o sistema que ativou no seu VM, ou utilizando o ID do cliente, iD de objeto ou ID de recursos de uma identidade gerida atribuída pelo utilizador que atribuiu ao seu VM.

Para saber mais sobre como ativar uma identidade gerida em todo o sistema ou criar uma identidade gerida atribuída pelo utilizador, consulte [identidades geridas configure para recursos Azure num VM utilizando o portal Azure](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).

#### <a name="authorize-by-using-a-system-wide-managed-identity"></a>Autorizar utilizando uma identidade gerida em todo o sistema

Primeiro, certifique-se de que ativou uma identidade gerida em todo o sistema no seu VM. Ver [identidade gerida atribuída pelo Sistema.](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)

Em seguida, na sua consola de comando, digite o seguinte comando e, em seguida, prima a tecla ENTER.

```azcopy
azcopy login --identity
```

#### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>Autorizar utilizando uma identidade gerida atribuída pelo utilizador

Primeiro, certifique-se de que ativou uma identidade gerida atribuída pelo utilizador no seu VM. Consulte [a identidade gerida atribuída pelo utilizador](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity).

Em seguida, na sua consola de comando, digite qualquer um dos seguintes comandos e, em seguida, prima a tecla ENTER.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

Substitua o `<client-id>` espaço reservado pelo ID do cliente da identidade gerida atribuída ao utilizador.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

Substitua o `<object-id>` espaço reservado pelo ID do objeto da identidade gerida atribuída pelo utilizador.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

Substitua o `<resource-id>` espaço reservado pelo ID de recursos da identidade gerida atribuída pelo utilizador.

## <a name="authorize-a-service-principal"></a>Autorizar um diretor de serviço

Esta é uma ótima opção se você planeja usar AzCopy dentro de um script que funciona sem interação do utilizador, particularmente quando executa no local. Se planeia executar a AzCopy em VMs que funcionam em Azure, uma identidade de serviço gerida é mais fácil de administrar. Para saber mais, consulte a Secção de [Identidade Gerida](#authorize-a-managed-identity) deste artigo.

Antes de executar um script, tem de assinar interativamente pelo menos uma vez para poder fornecer ao AzCopy as credenciais do seu principal de serviço.  Essas credenciais são armazenadas num ficheiro seguro e encriptado para que o seu script não tenha de fornecer essa informação sensível.

Pode inscrever-se na sua conta utilizando um segredo de cliente ou utilizando a palavra-passe de um certificado associado ao registo de aplicações do seu titular de serviço.

Para saber mais sobre a criação de um responsável de [serviços, consulte Como: Use o portal para criar uma aplicação AD AD Azure que possa aceder aos recursos.](../../active-directory/develop/howto-create-service-principal-portal.md)

Para saber mais sobre os principais serviços em geral, consulte [os objetos principais de aplicação e serviço no Azure Ative Directory](../../active-directory/develop/app-objects-and-service-principals.md)

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>Autorizar um diretor de serviço usando um segredo de cliente

Comece por definir a `AZCOPY_SPA_CLIENT_SECRET` variável ambiental para o segredo do cliente do registo de aplicações do seu diretor de serviço.

> [!NOTE]
> Certifique-se de que define este valor a partir da sua solicitação de comando e não nas definições variáveis ambientais do seu sistema operativo. Desta forma, o valor está disponível apenas para a sessão atual.

Este exemplo mostra como pôde fazer isto no PowerShell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Considere usar um pedido como mostrado neste exemplo. Assim, a sua palavra-passe não aparecerá no histórico de comando da sua consola.  

Em seguida, digite o seguinte comando e, em seguida, prima a tecla ENTER.

```azcopy
azcopy login --service-principal  --application-id application-id --tenant-id=tenant-id
```

Substitua o `<application-id>` espaço reservado pelo ID de aplicação do registo da aplicação do seu titular de serviço. Substitua o `<tenant-id>` espaço reservado pelo ID do inquilino da organização a que pertence a conta de armazenamento. Para encontrar o ID do inquilino, selecione **Azure Ative Directory > Properties > ID do Diretório** no portal Azure. 

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>Autorizar um principiante de serviço usando um certificado

Se preferir usar as suas credenciais próprias para autorização, pode enviar um certificado para o registo da sua aplicação e, em seguida, utilizar esse certificado para iniciar sessão.

Além de enviar o seu certificado para o registo da sua aplicação, também terá de ter uma cópia do certificado guardada na máquina ou VM onde a AzCopy estará a funcionar. Esta cópia do certificado deve estar em . PFX ou . Formato PEM, e deve incluir a chave privada. A chave privada deve estar protegida por palavra-passe. Se estiver a utilizar o Windows e o seu certificado existir apenas numa loja de certificados, certifique-se de exportar esse certificado para um ficheiro PFX (incluindo a chave privada). Para orientação, consulte [Export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate)

Em seguida, desaprote o `AZCOPY_SPA_CERT_PASSWORD` ambiente variável para a senha do certificado.

> [!NOTE]
> Certifique-se de que define este valor a partir da sua solicitação de comando e não nas definições variáveis ambientais do seu sistema operativo. Desta forma, o valor está disponível apenas para a sessão atual.

Este exemplo mostra como podes fazer esta tarefa no PowerShell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Em seguida, digite o seguinte comando e, em seguida, prima a tecla ENTER.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

Substitua o `<path-to-certificate-file>` espaço reservado pelo caminho relativo ou totalmente qualificado para o ficheiro do certificado. A AzCopy guarda o caminho para este certificado mas não guarda uma cópia do certificado, por isso certifique-se de manter esse certificado no lugar. Substitua o `<tenant-id>` espaço reservado pelo ID do inquilino da organização a que pertence a conta de armazenamento. Para encontrar o ID do inquilino, selecione **Azure Ative Directory > Properties > ID do Diretório** no portal Azure.

> [!NOTE]
> Considere usar um pedido como mostrado neste exemplo. Assim, a sua palavra-passe não aparecerá no histórico de comando da sua consola. 

<a id="managed-identity"></a>


## <a name="next-steps"></a>Passos seguintes

- Para mais informações sobre a AzCopy, [inicie com a AzCopy](storage-use-azcopy-v10.md)

- Se tiver dúvidas, problemas ou feedback geral, envie-os na página [do GitHub.](https://github.com/Azure/azure-storage-azcopy)