---
title: Copiar ou mover dados para o Armazenamento Azure utilizando o AzCopy v10 [ Microsoft Docs
description: O AzCopy é um utilitário de linha de comando que pode utilizar para copiar dados para, a partir ou entre contas de armazenamento. Este artigo ajuda-o a descarregar o AzCopy, a ligar-se à sua conta de armazenamento e, em seguida, a transferir ficheiros.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 8701fe6857e95334a5e1d24bfe70feb130d5512c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756025"
---
# <a name="get-started-with-azcopy"></a>Introdução ao AzCopy

O AzCopy é um utilitário de linha de comando que pode utilizar para copiar bolhas ou ficheiros de ou para uma conta de armazenamento. Este artigo ajuda-o a descarregar o AzCopy, a ligar-se à sua conta de armazenamento e, em seguida, a transferir ficheiros.

> [!NOTE]
> AzCopy **V10** é a versão atualmente suportada do AzCopy.
>
> Se precisar de utilizar uma versão anterior do AzCopy, consulte a versão anterior da secção [AzCopy](#previous-version) deste artigo.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Baixar AzCopy

Primeiro, baixe o ficheiro executável AzCopy V10 para qualquer diretório no seu computador. AzCopy V10 é apenas um ficheiro executável, por isso não há nada para instalar.

- [Windows 64-bit](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32-bit](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (alcatrão)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Estes ficheiros são comprimidos como um ficheiro zip (Windows e Mac) ou um ficheiro de alcatrão (Linux). Para descarregar e descomprimir o ficheiro de alcatrão no Linux, consulte a documentação para a sua distribuição linux.

> [!NOTE]
> Se pretender copiar dados de e para o seu serviço de [armazenamento de mesa Seletiva, instale](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) a [versão AzCopy 7.3](https://aka.ms/downloadazcopynet).


## <a name="run-azcopy"></a>Executar AzCopy

Por conveniência, considere adicionar a localização do diretório do AzCopy executável ao seu caminho do sistema para facilitar a utilização. Assim pode escrever `azcopy` de qualquer diretório no seu sistema.

Se optar por não adicionar o diretório AzCopy ao seu caminho, terá de alterar os diretórios para a localização do seu AzCopy executável e digitado `azcopy` ou `.\azcopy` em solicitações de comando Do Windows PowerShell.

Para ver uma lista de `azcopy -h` comandos, digite e, em seguida, prima a tecla ENTER.

Para conhecer um comando específico, basta incluir o nome `azcopy list -h`do comando (por exemplo: ).

![Ajuda inline](media/storage-use-azcopy-v10/azcopy-inline-help.png)

Para encontrar documentação de referência detalhada para cada parâmetro de comando e comando, consulte [a zcopy](storage-ref-azcopy.md)

> [!NOTE] 
> Como proprietário da sua conta de Armazenamento Azure, não lhe são atribuídas automaticamente permissões para aceder a dados. Antes de poder fazer algo significativo com a AzCopy, tem de decidir como irá fornecer credenciais de autorização ao serviço de armazenamento. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Escolha como irá fornecer credenciais de autorização

Pode fornecer credenciais de autorização utilizando o Diretório Ativo Azure (AD), ou utilizando um símbolo de Assinatura de Acesso Partilhado (SAS).

Utilize esta tabela como guia:

| Tipo de armazenamento | Atualmente apoiado método de autorização |
|--|--|
|**Armazenamento de blobs** | Azure AD & SAS |
|**Armazenamento de blob (espaço hierárquico)** | Azure AD & SAS |
|**Armazenamento de ficheiros** | SAS apenas |

### <a name="option-1-use-azure-active-directory"></a>Opção 1: Utilizar o Diretório Ativo Azure

Ao utilizar o Azure Ative Directory, pode fornecer credenciais uma vez em vez de ter de anexar um token SAS a cada comando.  

> [!NOTE]
> No lançamento atual, se planeia copiar bolhas entre contas de armazenamento, terá de anexar um token SAS a cada URL de origem. Só pode omitir o token SAS a partir do URL de destino. Por exemplo, ver [Copias bolhas entre contas](storage-use-azcopy-blobs.md)de armazenamento .

O nível de autorização de que precisa baseia-se no facto de pretender fazer o upload de ficheiros ou apenas descarregá-los.

Se apenas quiser descarregar ficheiros, verifique se o Leitor de Dados do [Depósito Blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) foi atribuído à sua identidade de utilizador, identidade gerida ou diretor de serviço.

> Identidades de utilizador, identidades geridas e diretores de serviço são cada um tipo de diretor de *segurança,* por isso usaremos o termo diretor de *segurança* para o resto deste artigo.

Se quiser fazer o upload de ficheiros, verifique se uma destas funções foi atribuída ao seu diretor de segurança:

- [Contribuinte de dados blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Proprietário de dados blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Estas funções podem ser atribuídas ao seu diretor de segurança em qualquer um destes âmbitos:

- Recipiente (sistema de ficheiros)
- Conta de armazenamento
- Grupo de recursos
- Subscrição

Para aprender a verificar e atribuir funções, consulte o Acesso ao Grant à blob Azure e aos dados de [fila com o RBAC no portal Azure.](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

> [!NOTE]
> Tenha em mente que as atribuições de funções RBAC podem levar até cinco minutos para se propagar.

Não precisa de ter uma destas funções atribuídas ao seu diretor de segurança se o seu diretor de segurança for adicionado à lista de controlo de acesso (ACL) do contentor ou diretório alvo. Na ACL, o seu diretor de segurança precisa de uma autorização para escrever no diretório alvo, e executar permissão no contentor e em cada diretório dos pais.

Para saber mais, consulte [o controlo de acesso em Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-a-user-identity"></a>Autenticar uma identidade de utilizador

Depois de verificar que a sua identidade de utilizador recebeu o nível de autorização necessário, abra um pedido de comando, escreva o seguinte comando e, em seguida, prima a tecla ENTER.

```azcopy
azcopy login
```

Se você pertence a mais de uma organização, inclua a identificação do inquilino da organização a que pertence a conta de armazenamento.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Substitua `<tenant-id>` o espaço reservado pela identificação do arrendatário da organização a que pertence a conta de armazenamento. Para encontrar o ID do inquilino, selecione **Azure Ative Directory > Properties > Id diretório** no portal Azure.

Este comando devolve um código de autenticação e o URL de um website. Abra o site, forneça o código e, em seguida, escolha o botão **Seguinte.**

![Criar um contentor](media/storage-use-azcopy-v10/azcopy-login.png)

Aparecerá uma janela de inscrição. Nessa janela, inscreva-se na sua conta Azure utilizando as credenciais da sua conta Azure. Depois de ter assinado com sucesso, pode fechar a janela do navegador e começar a usar o AzCopy.

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>Autenticar um diretor de serviço

Esta é uma ótima opção se planeia utilizar o AzCopy dentro de um script que funciona sem interação do utilizador, especialmente quando se executa no local. Se planeia executar a AzCopy em VMs que funcionam em Azure, uma identidade de serviço gerida é mais fácil de administrar. Para saber mais, consulte o [Autenticator uma](#managed-identity) secção de identidade gerida deste artigo.

Antes de executar um script, tem de iniciar sessão interativamente pelo menos uma vez para que possa fornecer à AzCopy as credenciais do seu diretor de serviço.  Essas credenciais são armazenadas num ficheiro seguro e encriptado para que o seu script não tenha de fornecer essa informação sensível.

Pode iniciar sessão na sua conta utilizando um segredo de cliente ou utilizando a palavra-passe de um certificado que esteja associado ao registo da aplicação do seu diretor de serviço.

Para saber mais sobre a criação do principal de serviço, consulte [Como: Use o portal para criar uma aplicação e diretor](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)de serviço seletiva azure que possa aceder a recursos.

Para saber mais sobre os principais de serviço em geral, consulte [aplicação e serviço objetos principais no Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>Usando um segredo de cliente

Comece por `AZCOPY_SPA_CLIENT_SECRET` definir a variável ambiental para o segredo do cliente do registo de aplicações do seu diretor de serviço.

> [!NOTE]
> Certifique-se de que define este valor a partir do seu pedido de comando e não nas configurações variáveis ambientais do seu sistema operativo. Desta forma, o valor está disponível apenas para a sessão atual.

Este exemplo mostra como se pode fazer isto no PowerShell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Considere usar um pedido como mostrado neste exemplo. Assim, a tua palavra-passe não aparecerá no histórico de comando da tua consola.  

Em seguida, digite o seguinte comando e, em seguida, pressione a tecla ENTER.

```azcopy
azcopy login --service-principal --application-id <application-id> --tenant-id=<tenant-id>
```

Substitua `<application-id>` o espaço reservado pela identificação da aplicação do registo da aplicação do seu diretor de serviço. Substitua `<tenant-id>` o espaço reservado pela identificação do arrendatário da organização a que pertence a conta de armazenamento. Para encontrar o ID do inquilino, selecione **Azure Ative Directory > Properties > Id diretório** no portal Azure. 

##### <a name="using-a-certificate"></a>Utilização de um certificado

Se preferir usar as suas próprias credenciais para autorização, pode fazer o upload de um certificado para o registo da sua aplicação e, em seguida, utilizar esse certificado para iniciar sessão.

Além de fazer o upload do seu certificado para o registo da sua aplicação, também terá de ter uma cópia do certificado guardada para a máquina ou VM onde a AzCopy estará em execução. Esta cópia do certificado deve estar em . PFX ou . Formato PEM, e deve incluir a chave privada. A chave privada deve ser protegida por palavra-passe. Se estiver a utilizar o Windows, e o seu certificado existir apenas numa loja de certificados, certifique-se de exportar esse certificado para um ficheiro PFX (incluindo a chave privada). Para obter orientação, consulte [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

Em seguida, `AZCOPY_SPA_CERT_PASSWORD` detete a variável ambiental para a palavra-passe do certificado.

> [!NOTE]
> Certifique-se de que define este valor a partir do seu pedido de comando e não nas configurações variáveis ambientais do seu sistema operativo. Desta forma, o valor está disponível apenas para a sessão atual.

Este exemplo mostra como pode fazer esta tarefa no PowerShell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Em seguida, digite o seguinte comando e, em seguida, pressione a tecla ENTER.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

Substitua `<path-to-certificate-file>` o espaço reservado pelo caminho relativo ou totalmente qualificado para o ficheiro do certificado. A AzCopy guarda o caminho para este certificado, mas não guarda uma cópia do certificado, por isso certifique-se de manter o certificado no lugar. Substitua `<tenant-id>` o espaço reservado pela identificação do arrendatário da organização a que pertence a conta de armazenamento. Para encontrar o ID do inquilino, selecione **Azure Ative Directory > Properties > Id diretório** no portal Azure.

> [!NOTE]
> Considere usar um pedido como mostrado neste exemplo. Assim, a tua palavra-passe não aparecerá no histórico de comando da tua consola. 

<a id="managed-identity" />

#### <a name="authenticate-a-managed-identity"></a>Autenticar uma identidade gerida

Esta é uma ótima opção se planeia utilizar o AzCopy dentro de um script que funciona sem interação do utilizador, e o script é executado a partir de uma Máquina Virtual Azure (VM). Ao utilizar esta opção, não terá de armazenar credenciais no VM.

Pode iniciar sessão na sua conta utilizando uma identidade gerida a nível do sistema que ativou no seu VM, ou utilizando o ID do cliente, o ID do Objeto ou o ID de recurso de uma identidade gerida atribuída ao utilizador que atribuiu ao seu VM.

Para saber mais sobre como permitir uma identidade gerida a nível do sistema ou criar uma identidade gerida atribuída pelo utilizador, consulte [as identidades geridas pela Configure para os recursos Azure num VM utilizando o portal Azure.](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)

##### <a name="using-a-system-wide-managed-identity"></a>Usando uma identidade gerida em todo o sistema

Primeiro, certifique-se de ter ativado uma identidade gerida em todo o sistema no seu VM. Consulte [a identidade gerida atribuída pelo Sistema.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity)

Em seguida, na sua consola de comando, digite o seguinte comando e, em seguida, pressione a tecla ENTER.

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>Utilizando uma identidade gerida atribuída pelo utilizador

Em primeiro lugar, certifique-se de ter ativado uma identidade gerida atribuída ao utilizador no seu VM. Consulte [a identidade gerida atribuída pelo utilizador.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity)

Em seguida, na sua consola de comando, digite qualquer um dos seguintes comandos e, em seguida, prima a tecla ENTER.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

Substitua `<client-id>` o espaço reservado pelo ID do cliente da identidade gerida atribuída pelo utilizador.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

Substitua `<object-id>` o espaço reservado pelo ID do objeto da identidade gerida atribuída pelo utilizador.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

Substitua `<resource-id>` o espaço reservado pelo id de recurso da identidade gerida atribuída pelo utilizador.

### <a name="option-2-use-a-sas-token"></a>Opção 2: Utilize um símbolo SAS

Pode anexar um token SAS a cada fonte ou URL de destino que utilize nos seus comandos AzCopy.

Este comando de exemplo copia recursivamente dados de um diretório local para um recipiente de bolhas. Um token SAS fictício é anexado ao fim do URL do recipiente.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Para saber mais sobre tokens SAS e como obter um, consulte Utilizar assinaturas de [acesso partilhado (SAS)](https://docs.microsoft.com/azure/storage/common/storage-sas-overview).

## <a name="transfer-files"></a>Transferir ficheiros

Depois de autenticar a sua identidade ou obter um token SAS, pode começar a transferir ficheiros.

Para encontrar comandos de exemplo, consulte qualquer um destes artigos.

- [Transferir dados com armazenamento azCopy e blob](storage-use-azcopy-blobs.md)

- [Transferir dados com a AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)

- [Transferir dados com baldes AzCopy e Amazon S3](storage-use-azcopy-s3.md)

- [Transferir dados com armazenamento AzCopy e Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>Use a AzCopy num script

### <a name="obtain-a-static-download-link"></a>Obtenha um link de descarregamento estático

Com o tempo, o link de [descarregamento](#download-and-install-azcopy) AzCopy irá apontar para novas versões do AzCopy. Se o seu script descarregar a AzCopy, o script pode deixar de funcionar se uma versão mais recente do AzCopy modificar as funcionalidades de que o seu script depende.

Para evitar estes problemas, obtenha uma ligação estática (não alterando) à versão atual do AzCopy. Dessa forma, o seu script descarrega a mesma versão exata do AzCopy sempre que funciona.

Para obter o link, executar este comando:

| Sistema operativo  | Comando |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> Para o `--strip-components=1` Linux, no `tar` comando remove a pasta de nível superior que contém o nome da versão e, em vez disso, extrai o binário diretamente na pasta atual. Isto permite que o script seja `azcopy` atualizado com `wget` uma nova versão apenas atualizando o URL.

O URL aparece na saída deste comando. O seu script pode então descarregar a AzCopy utilizando esse URL.

| Sistema operativo  | Comando |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>Escapar de personagens especiais em tokens SAS

Nos ficheiros de `.cmd` lote que têm a `%` extensão, terá de escapar aos caracteres que aparecem em fichas SAS. Pode fazê-lo adicionando um personagem adicional `%` ao lado dos caracteres existentes `%` na corda token SAS.

### <a name="run-scripts-by-using-jenkins"></a>Executar scripts usando Jenkins

Se planeias usar o [Jenkins](https://jenkins.io/) para executar scripts, certifique-se de colocar o seguinte comando no início do script.

```
/usr/bin/keyctl new_session
```

## <a name="use-azcopy-in-azure-storage-explorer"></a>Use a AzCopy no Explorador de Armazenamento Azure

[O Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) utiliza o AzCopy para realizar todas as suas operações de transferência de dados. Pode utilizar o [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) se pretender aproveitar as vantagens de desempenho do AzCopy, mas prefere utilizar uma interface gráfica do utilizador em vez da linha de comando para interagir com os seus ficheiros.

O Storage Explorer utiliza a chave da conta para realizar operações, por isso, depois de assinar no Storage Explorer, não precisará de fornecer credenciais de autorização adicionais.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Utilize a versão anterior do AzCopy

Se necessitar de utilizar a versão anterior do AzCopy, consulte qualquer uma das seguintes ligações:

- [AzCopy no Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy em Linux (v7)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configure, otimize e problemas AzCopy

Ver [Configure, otimizar e resolver problemas AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>Passos seguintes

Se tiver dúvidas, questões ou feedback geral, submeta-as na página [do GitHub.](https://github.com/Azure/azure-storage-azcopy)
