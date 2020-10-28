---
title: Copiar ou mover dados para o Azure Storage utilizando a AzCopy v10 Microsoft Docs
description: O AzCopy é um utilitário de linha de comando que pode usar para copiar dados para, a partir ou entre contas de armazenamento. Este artigo ajuda-o a transferir o AzCopy, a ligar-se à conta de armazenamento e a transferir ficheiros.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: ce6398f63149a7f5dd3102d75c8db324f526c419
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791159"
---
# <a name="get-started-with-azcopy"></a>Introdução ao AzCopy

O AzCopy é um utilitário da linha de comando que pode utilizar para copiar blobs ou ficheiros de/para uma conta de armazenamento. Este artigo ajuda-o a transferir o AzCopy, a ligar-se à conta de armazenamento e a transferir ficheiros.

> [!NOTE]
> AzCopy **V10** é a versão atualmente suportada do AzCopy.
>
> Se precisar de utilizar uma versão anterior do AzCopy, consulte a versão anterior da secção [AzCopy](#previous-version) deste artigo.

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>Baixar AzCopy

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

## <a name="choose-how-youll-provide-authorization-credentials"></a>Escolher como disponibilizar as credenciais de autorização

Pode fornecer credenciais de autorização utilizando o Azure Ative Directory (AD), ou utilizando um token de Assinatura de Acesso Partilhado (SAS).

Use esta tabela como guia:

| Tipo de armazenamento | Método de autorização atualmente suportado |
|--|--|
|**Armazenamento de blobs** | Azure Active Directory e SAS |
|**Armazenamento de bolhas (espaço hierárquico)** | Azure Active Directory e SAS |
|**Armazenamento de ficheiros** | Apenas SAS |

### <a name="option-1-use-azure-active-directory"></a>Opção 1: Utilizar o Diretório Ativo Azure

Ao utilizar o Azure Ative Directory, pode fornecer credenciais uma vez em vez de ter de anexar um token SAS a cada comando.  

> [!NOTE]
> Na versão atual, se planeia copiar bolhas entre contas de armazenamento, terá de anexar um token SAS a cada URL de origem. Você pode omitir o token SAS apenas a partir do URL de destino. Por exemplo, consulte [bolhas de cópia entre contas de armazenamento](storage-use-azcopy-blobs.md).

O nível de autorização de que precisa baseia-se na possibilidade de fazer o upload de ficheiros ou simplesmente descarregá-los.

Se apenas pretender descarregar ficheiros, verifique se o [Leitor de Dados de Armazenamento blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) foi atribuído à identidade do utilizador, identidade gerida ou principal de serviço.

> Identidades dos utilizadores, identidades geridas e princípios de serviço são cada um um dos *principais* de segurança , por isso vamos usar o termo principal de *segurança* para o resto deste artigo.

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

Para saber mais, consulte [o controlo de acesso no Azure Data Lake Storage Gen2](../blobs/data-lake-storage-access-control.md).

#### <a name="authenticate-a-user-identity"></a>Autenticar uma identidade de utilizador

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

#### <a name="authenticate-a-service-principal"></a>Autenticar um diretor de serviço

Esta é uma ótima opção se você planeja usar AzCopy dentro de um script que funciona sem interação do utilizador, particularmente quando executa no local. Se planeia executar a AzCopy em VMs que funcionam em Azure, uma identidade de serviço gerida é mais fácil de administrar. Para saber mais, consulte a [Autenticação de uma](#managed-identity) secção de identidade gerida deste artigo.

Antes de executar um script, tem de iniciar súpite interativamente pelo menos uma vez para poder fornecer ao AzCopy as credenciais do seu principal de serviço.  Essas credenciais são armazenadas num ficheiro seguro e encriptado para que o seu script não tenha de fornecer essa informação sensível.

Pode inscrever-se na sua conta utilizando um segredo de cliente ou utilizando a palavra-passe de um certificado associado ao registo de aplicações do seu titular de serviço.

Para saber mais sobre a criação de um responsável de [serviços, consulte Como: Use o portal para criar uma aplicação AD AD Azure que possa aceder aos recursos.](../../active-directory/develop/howto-create-service-principal-portal.md)

Para saber mais sobre os principais serviços em geral, consulte [os objetos principais de aplicação e serviço no Azure Ative Directory](../../active-directory/develop/app-objects-and-service-principals.md)

##### <a name="using-a-client-secret"></a>Usando um segredo de cliente

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

##### <a name="using-a-certificate"></a>Usando um certificado

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

#### <a name="authenticate-a-managed-identity"></a>Autenticar uma identidade gerida

Esta é uma ótima opção se planeia usar o AzCopy dentro de um script que funciona sem interação do utilizador, e o script é executado a partir de uma Máquina Virtual Azure (VM). Ao utilizar esta opção, não terá de armazenar credenciais no VM.

Pode inscrever-se na sua conta utilizando uma identidade gerida em todo o sistema que ativou no seu VM, ou utilizando o ID do cliente, iD de objeto ou ID de recursos de uma identidade gerida atribuída pelo utilizador que atribuiu ao seu VM.

Para saber mais sobre como ativar uma identidade gerida em todo o sistema ou criar uma identidade gerida atribuída pelo utilizador, consulte [identidades geridas configure para recursos Azure num VM utilizando o portal Azure](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).

##### <a name="using-a-system-wide-managed-identity"></a>Usando uma identidade gerida em todo o sistema

Primeiro, certifique-se de que ativou uma identidade gerida em todo o sistema no seu VM. Ver [identidade gerida atribuída pelo Sistema.](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)

Em seguida, na sua consola de comando, digite o seguinte comando e, em seguida, prima a tecla ENTER.

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>Usando uma identidade gerida atribuída pelo utilizador

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

### <a name="option-2-use-a-sas-token"></a>Opção 2: Use um token SAS

Pode anexar um token SAS a cada URL de origem ou destino que utilize nos comandos AzCopy.

Este exemplo de comando copia novamente dados de um diretório local para um recipiente de bolhas. Um símbolo SAS fictício é anexado ao final do URL do contentor.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Para saber mais sobre fichas SAS e como obter um, consulte [Usando assinaturas de acesso partilhado (SAS)](./storage-sas-overview.md).

## <a name="transfer-files"></a>Transferir ficheiros

Depois de autenticar a sua identidade ou obter um token SAS, pode começar a transferir ficheiros.

Para encontrar comandos de exemplo, consulte qualquer um destes artigos.

- [Transferir dados com AzCopy e armazenamento de bolhas](storage-use-azcopy-blobs.md)

- [Transferir dados com o AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)

- [Transfira dados com baldes AzCopy e Amazon S3](storage-use-azcopy-s3.md)

- [Transferir dados com armazenamento AzCopy e Azure Stack](/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>Use AzCopy em um script

### <a name="obtain-a-static-download-link"></a>Obtenha um link de descarregamento estático

Com o tempo, o [link de descarregamento](#download-and-install-azcopy) AzCopy irá apontar para novas versões do AzCopy. Se o seu script descarregar AzCopy, o script pode parar de funcionar se uma versão mais recente do AzCopy modificar as funcionalidades de que o seu script depende.

Para evitar estes problemas, obtenha uma ligação estática (não alterando) com a versão atual do AzCopy. Dessa forma, o seu script descarrega a mesma versão exata do AzCopy cada vez que é executado.

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

### <a name="escape-special-characters-in-sas-tokens"></a>Escape personagens especiais em fichas SAS

Nos ficheiros de lote que têm a `.cmd` extensão, terás de escapar aos `%` caracteres que aparecem em fichas SAS. Pode fazê-lo adicionando um carácter adicional ao lado dos `%` `%` caracteres existentes na cadeia de fichas SAS.

### <a name="run-scripts-by-using-jenkins"></a>Executar scripts usando Jenkins

Se planeias usar o [Jenkins](https://jenkins.io/) para executar scripts, certifica-te de que colocas o seguinte comando no início do script.

```
/usr/bin/keyctl new_session
```

## <a name="use-azcopy-in-azure-storage-explorer"></a>Use AzCopy no Explorador de Armazenamento Azure

[O Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) utiliza a AzCopy para executar todas as suas operações de transferência de dados. Pode utilizar o [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) se quiser aproveitar as vantagens de desempenho do AzCopy, mas prefere utilizar uma interface gráfica do utilizador em vez da linha de comando para interagir com os seus ficheiros.

O Storage Explorer utiliza a chave da sua conta para realizar operações, por isso, depois de assinar no Storage Explorer, não necessitará de fornecer credenciais de autorização adicionais.

<a id="previous-version"></a>

## <a name="use-the-previous-version-of-azcopy"></a>Utilize a versão anterior do AzCopy

Se precisar de utilizar a versão anterior do AzCopy, consulte qualquer uma das seguintes ligações:

- [AzCopy no Windows (v8)](/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy em Linux (v7)](/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurar, otimizar e resolver problemas AzCopy

Consulte [Configuração, otimização e resolução de problemas AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>Passos seguintes

Se tiver dúvidas, problemas ou feedback geral, envie-os na página [do GitHub.](https://github.com/Azure/azure-storage-azcopy)