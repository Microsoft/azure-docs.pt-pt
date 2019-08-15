---
title: Guia de solução de problemas Gerenciador de Armazenamento do Azure | Microsoft Docs
description: Visão geral das técnicas de depuração para Gerenciador de Armazenamento do Azure
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 96a8eab57f1714eed4831bea01508e9140d1dfad
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934977"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Guia de solução de problemas Gerenciador de Armazenamento do Azure

Gerenciador de Armazenamento do Microsoft Azure é um aplicativo autônomo que permite que você trabalhe facilmente com os dados do armazenamento do Azure no Windows, no macOS e no Linux. O aplicativo pode se conectar a contas de armazenamento hospedadas no Azure, nuvens nacionais e Azure Stack.

Este guia resume as soluções para problemas comuns vistos em Gerenciador de Armazenamento.

## <a name="role-based-access-control-permission-issues"></a>Problemas de permissão de controle de acesso baseado em função

O [RBAC (controle de acesso baseado em função)](https://docs.microsoft.com/azure/role-based-access-control/overview) fornece gerenciamento de acesso refinado de recursos do Azure, combinando conjuntos de permissões em _funções_. Aqui estão algumas sugestões que você pode seguir para fazer com que o RBAC funcione no Gerenciador de Armazenamento.

### <a name="what-do-i-need-to-see-my-resources-in-storage-explorer"></a>O que é necessário para ver meus recursos no Gerenciador de Armazenamento?

Se você estiver tendo problemas para acessar os recursos de armazenamento usando o RBAC, pode ser porque você não recebeu as funções apropriadas. As seções a seguir descrevem as permissões Gerenciador de Armazenamento atualmente exige que o acesse seus recursos de armazenamento.

Entre em contato com o administrador da conta do Azure se você não tiver certeza de que tem as funções ou permissões apropriadas.

#### <a name="read-listget-storage-accounts"></a>Leitura Listar/Obter Conta(s) de Armazenamento

Você deve ter permissão para listar contas de armazenamento. Você pode obter essa permissão ao receber a função de "leitor".

#### <a name="list-storage-account-keys"></a>Listar Chaves de Conta de Armazenamento

Gerenciador de Armazenamento também pode usar chaves de conta para autenticar solicitações. Você pode obter acesso a chaves com funções mais poderosas, como a função de "colaborador".

> [!NOTE]
> Chaves de acesso concedem permissões irrestritas a qualquer pessoa que as mantenha. Portanto, geralmente não é recomendável que eles sejam entregues aos usuários da conta. Se você precisar revogar chaves de acesso, poderá gerá-las novamente no [portal do Azure](https://portal.azure.com/).

#### <a name="data-roles"></a>Funções de dados

Você deve receber pelo menos uma função que concede acesso de leitura de dados de recursos. Por exemplo, se você precisar listar ou baixar BLOBs, precisará de pelo menos a função "leitor de dados de blob de armazenamento".

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Por que preciso de uma função de camada de gerenciamento para ver meus recursos no Gerenciador de Armazenamento?

O armazenamento do Azure tem duas camadas de acesso: _Gerenciamento_ e _dados_. As assinaturas e as contas de armazenamento são acessadas por meio da camada de gerenciamento. Contêineres, BLOBs e outros recursos de dados são acessados por meio da camada de dados. Por exemplo, se você quiser obter uma lista de suas contas de armazenamento do Azure, envie uma solicitação para o ponto de extremidade de gerenciamento. Se você quiser uma lista de contêineres de BLOB em uma conta, envie uma solicitação para o ponto de extremidade de serviço apropriado.

As funções de RBAC podem conter permissões para acesso à camada de dados ou gerenciamento. A função "leitor", por exemplo, concede recursos de camada de gerenciamento de acesso somente leitura.

Estritamente falando, a função de "leitor" não fornece nenhuma permissão de camada de dados e não é necessária para acessar a camada de dados.

Gerenciador de Armazenamento facilita o acesso aos recursos coletando as informações necessárias para se conectar aos recursos do Azure para você. Por exemplo, para exibir seus contêineres de BLOB, Gerenciador de Armazenamento envia uma solicitação de contêineres de lista para o ponto de extremidade do serviço BLOB. Para obter esse ponto de extremidade, Gerenciador de Armazenamento pesquisa a lista de assinaturas e contas de armazenamento às quais você tem acesso. Mas, para localizar suas assinaturas e contas de armazenamento, Gerenciador de Armazenamento também precisa acessar a camada de gerenciamento.

Se você não tiver uma função concedendo permissões de camada de gerenciamento, Gerenciador de Armazenamento não poderá obter as informações necessárias para se conectar à camada de dados.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>E se eu não conseguir obter as permissões da camada de gerenciamento que preciso do meu administrador?

Ainda não temos uma solução relacionada a RBAC no momento. Como alternativa, você pode solicitar um URI de SAS para [anexar ao recurso](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-sas-uri).

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Erro: Certificado autoassinado na cadeia de certificados (e erros semelhantes)

Os erros de certificado são causados por uma das duas situações a seguir:

1. O aplicativo é conectado por meio de um "proxy transparente", o que significa que um servidor (como o servidor da empresa) está interceptando o tráfego HTTPS, descriptografando-o e, em seguida, criptografando-o usando um certificado autoassinado.
2. Você está executando um aplicativo que está injetando um certificado SSL autoassinado nas mensagens HTTPS recebidas. Exemplos de aplicativos que inserem certificados incluem software de inspeção de tráfego de rede e antivírus.

Quando Gerenciador de Armazenamento vê um certificado autoassinado ou não confiável, não é mais possível saber se a mensagem HTTPS recebida foi alterada. Se você tiver uma cópia do certificado autoassinado, poderá instruir Gerenciador de Armazenamento a confiar seguindo as seguintes etapas:

1. Obter uma cópia de X. 509 (. cer) codificada em base 64 do certificado
2. Clique em **Editar** > **certificados** > SSL**importar certificados**e, em seguida, use o seletor de arquivos para localizar, selecionar e abrir o arquivo. cer

Esse problema também pode ser o resultado de vários certificados (raiz e intermediária). Ambos os certificados devem ser adicionados para superar o erro.

Se não tiver certeza de onde o certificado é proveniente, você pode tentar estas etapas para encontrá-lo:

1. Instalar o Open SSL
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (qualquer uma das versões leves deve ser suficiente)
    * Mac e Linux: deve ser incluído no seu sistema operacional
2. Executar o Open SSL
    * Windows: Abra o diretório de instalação, clique em **/bin/** e, em seguida, clique duas vezes em **OpenSSL. exe**.
    * Mac e Linux: execute **OpenSSL** a partir de um terminal.
3. Execute `s_client -showcerts -connect microsoft.com:443`
4. Procure certificados autoassinados. Se você não tiver certeza de quais certificados são autoassinados, procure em qualquer lugar que `("s:")` o assunto e `("i:")` o emissor sejam os mesmos.
5. Quando você encontrar todos os certificados autoassinados, para cada um, copie e cole tudo de e incluindo **-----iniciar-----de certificado** para **----------de certificado final** para um novo arquivo. cer.
6. Abra Gerenciador de armazenamento, clique em **Editar** > **certificados** > SSL**importar certificados**e, em seguida, use o seletor de arquivos para localizar, selecionar e abrir os arquivos. cer que você criou.

Se você não encontrar certificados autoassinados usando as etapas anteriores, entre em contato conosco por meio da ferramenta de comentários para obter mais ajuda. Você também pode optar por iniciar Gerenciador de armazenamento na linha de comando com o `--ignore-certificate-errors` sinalizador. Quando iniciado com esse sinalizador, Gerenciador de Armazenamento irá ignorar os erros de certificado.

## <a name="sign-in-issues"></a>Problemas de início de sessão

### <a name="blank-sign-in-dialog"></a>Caixa de diálogo de entrada em branco

Caixas de diálogo de entrada em branco geralmente são causadas pelo ADFS solicitando Gerenciador de Armazenamento para executar um redirecionamento, o que não é suportado pelo de todos os. Para contornar esse problema, você pode tentar usar o fluxo de código do dispositivo para entrar. Para fazer isso, conclua as seguintes etapas:

1. AdicionarMenu Visualização-> "usar entrada de código de dispositivo".
2. Abra a caixa de diálogo Conectar (por meio do ícone de plugue na barra vertical esquerda ou "adicionar conta" no painel de conta).
3. Escolha em qual ambiente você deseja entrar.
4. Clique no botão "entrar".
5. Siga as instruções no painel seguinte.

Se você estiver tendo problemas para entrar na conta que deseja usar, pois seu navegador padrão já está conectado a uma conta diferente, você pode:

1. Copie manualmente o link e o código em uma sessão privada do seu navegador.
2. Copie manualmente o link e o código em um navegador diferente.

### <a name="reauthentication-loop-or-upn-change"></a>Loop de reautenticação ou alteração de UPN

Se você estiver em um loop de reautenticação ou tiver alterado o UPN de uma de suas contas, tente as seguintes etapas:

1. Remova todas as contas e feche Gerenciador de Armazenamento
2. Exclua o. IdentityService a pasta do seu computador. No Windows, a pasta está localizada em `C:\users\<username>\AppData\Local`. Para Mac e Linux, você pode encontrar a pasta na raiz do seu diretório de usuário.
3. Se você estiver no Mac ou no Linux, também precisará excluir a entrada Microsoft. Developer. IdentityService do repositório de chaves do seu sistema operacional. No Mac, o keystore é o aplicativo "conjunto de chaves do GNOME". Para o Linux, o aplicativo é geralmente chamado de "token de toque", mas o nome pode ser diferente dependendo da sua distribuição.

### <a name="conditional-access"></a>Acesso Condicional

Não há suporte para o acesso condicional quando o Gerenciador de Armazenamento está sendo usado no Windows 10, Linux ou macOS. Isso ocorre devido a uma limitação na biblioteca do AAD usada pelo Gerenciador de Armazenamento.

## <a name="mac-keychain-errors"></a>Erros de conjunto de chaves do Mac

Às vezes, o conjunto de chaves do macOS pode entrar em um estado que causa problemas para a biblioteca de autenticação de Gerenciador de Armazenamento. Para obter o conjunto de chaves desse Estado, tente as seguintes etapas:

1. Feche Gerenciador de Armazenamento.
2. Abra o conjunto de chaves (**cmd + espaço**, digite em conjunto de chaves, pressione Enter).
3. Selecione o conjunto de chaves "logon".
4. Clique no ícone de cadeado para bloquear o conjunto de chaves (o cadeado será animado em uma posição bloqueada quando for concluído, pode levar alguns segundos, dependendo de quais aplicativos você abriu).

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Iniciar Gerenciador de Armazenamento.
6. Um pop-up deve aparecer dizendo algo como "o Hub de serviço deseja acessar o conjunto de chaves". Quando tiver, insira sua senha da conta de administrador do Mac e clique em **sempre permitir** (ou **permitir** se o **sempre permitir** não estiver disponível).
7. Tente entrar.

### <a name="general-sign-in-troubleshooting-steps"></a>Etapas de solução de problemas de entrada geral

* Se você estiver no macOS, e a janela de entrada nunca aparecer sobre a "aguardando autenticação..." e tente [estas etapas](#mac-keychain-errors)
* Reiniciar Gerenciador de Armazenamento
* Se a janela de autenticação estiver em branco, aguarde pelo menos um minuto antes de fechar a caixa de diálogo de autenticação.
* Verifique se as configurações de proxy e certificado estão configuradas corretamente para seu computador e Gerenciador de Armazenamento.
* Se você estiver no Windows e tiver acesso ao Visual Studio 2019 no mesmo computador e entrar, tente entrar no Visual Studio 2019. Após uma entrada bem-sucedida no Visual Studio 2019, você pode abrir Gerenciador de Armazenamento e ver sua conta no painel de conta.

Se nenhum desses métodos funcionar, [abra um problema no GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Assinaturas ausentes e locatários desfeitos

Se não for possível recuperar suas assinaturas depois de entrar com êxito, tente os seguintes métodos de solução de problemas:

* Verifique se sua conta tem acesso às assinaturas que você espera. Você pode verificar seu acesso entrando no portal para o ambiente do Azure que você está tentando usar.
* Verifique se você entrou usando o ambiente correto do Azure (Azure, Azure China 21Vianet, Azure Alemanha, governo dos EUA do Azure ou ambiente personalizado).
* Se você estiver protegido por um proxy, certifique-se de ter configurado o proxy de Gerenciador de Armazenamento corretamente.
* Tente remover e adicionar novamente a conta.
* Se houver um link "mais informações", procure e veja quais mensagens de erro estão sendo relatadas para os locatários que estão falhando. Se you'ren'tr o que fazer com as mensagens de erro que você vê, fique à vontade para [abrir um problema no GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-attached-account-or-storage-resource"></a>Não é possível remover a conta anexada ou o recurso de armazenamento

Se não for possível remover uma conta anexada ou um recurso de armazenamento por meio da interface do usuário, você poderá excluir manualmente todos os recursos anexados excluindo as seguintes pastas:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Feche Gerenciador de Armazenamento antes de excluir as pastas acima.

> [!NOTE]
> Se você já importou quaisquer certificados SSL, faça backup do conteúdo `certs` do diretório. Posteriormente, você pode usar o backup para reimportar seus certificados SSL.

## <a name="proxy-issues"></a>Problemas de proxy

Primeiro, certifique-se de que as seguintes informações inseridas estão corretas:

* A URL do proxy e o número da porta
* Nome de usuário e senha se exigido pelo proxy

> [!NOTE]
> Gerenciador de Armazenamento não dá suporte a arquivos de configuração automática de proxy para definir as configurações de proxy.

### <a name="common-solutions"></a>Soluções comuns

Se você ainda tiver problemas, tente os seguintes métodos de solução de problemas:

* Se você puder se conectar à Internet sem usar o proxy, verifique se Gerenciador de Armazenamento funciona sem as configurações de proxy habilitadas. Se esse for o caso, pode haver um problema com as configurações de proxy. Trabalhe com o administrador de proxy para identificar os problemas.
* Verifique se outros aplicativos que usam o servidor proxy funcionam conforme o esperado.
* Verifique se você pode se conectar ao portal para o ambiente do Azure que você está tentando usar
* Verifique se você pode receber respostas de seus pontos de extremidade de serviço. Insira uma das suas URLs de ponto de extremidade em seu navegador. Se você puder se conectar, deverá receber uma resposta XML InvalidQueryParameterValue ou semelhante.
* Se outra pessoa também estiver usando Gerenciador de Armazenamento com o servidor proxy, verifique se ele pode se conectar. Se eles puderem se conectar, talvez seja necessário entrar em contato com o administrador do servidor proxy.

### <a name="tools-for-diagnosing-issues"></a>Ferramentas para diagnosticar problemas

Se você tiver ferramentas de rede, como o Fiddler para Windows, poderá diagnosticar os problemas da seguinte maneira:

* Se precisar trabalhar com o proxy, talvez seja necessário configurar sua ferramenta de rede para se conectar por meio do proxy.
* Verifique o número da porta usado pela sua ferramenta de rede.
* Insira a URL do host local e o número da porta da ferramenta de rede como configurações de proxy em Gerenciador de Armazenamento. Quando feito corretamente, a ferramenta de rede inicia o log de solicitações de rede feitas por Gerenciador de Armazenamento para pontos de extremidade de serviço e gerenciamento. Por exemplo, insira https://cawablobgrs.blob.core.windows.net/ para o ponto de extremidade de BLOB em um navegador e você receberá uma resposta semelhante à seguinte, que sugere que o recurso existe, embora você não possa acessá-lo.

![exemplo de código](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Contatar o administrador do servidor proxy

Se as configurações de proxy estiverem corretas, talvez seja necessário entrar em contato com o administrador do servidor proxy e

* Certifique-se de que o proxy não bloqueie o tráfego para os pontos de extremidade de gerenciamento ou recursos do Azure.
* Verifique o protocolo de autenticação usado pelo servidor proxy. No momento, o Gerenciador de Armazenamento não oferece suporte a proxies NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Mensagem de erro "não é possível recuperar filhos"

Se você estiver conectado ao Azure por meio de um proxy, verifique se as configurações de proxy estão corretas. Se você tiver acesso a um recurso do proprietário da assinatura ou da conta, verifique se você tem permissões de leitura ou de lista para esse recurso.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>A cadeia de conexão não tem definições de configuração completas

Se você receber essa mensagem de erro, é possível que você não tenha as permissões necessárias para obter as chaves para sua conta de armazenamento. Para confirmar se esse é o caso, vá para o portal e localize sua conta de armazenamento. Você pode fazer isso rapidamente clicando com o botão direito do mouse no nó da sua conta de armazenamento e clicando em "abrir no portal". Depois de fazer isso, vá para a folha "chaves de acesso". Se você não tiver permissões para exibir chaves, verá uma página com a mensagem "você não tem acesso". Para contornar esse problema, você pode obter a chave de conta de outra pessoa e anexá-la com nome e chave, ou pode pedir a alguém uma SAS para a conta de armazenamento e usá-la para anexar a conta de armazenamento.

Se você vir as chaves de conta, execute um problema no GitHub para que possamos ajudá-lo a resolver o problema.

## <a name="issues-with-sas-url"></a>Problemas com a URL SAS

Se você estiver se conectando a um serviço usando uma URL SAS e apresentando esse erro:

* Verifique se a URL fornece as permissões necessárias para ler ou listar recursos.
* Verifique se a URL não expirou.
* Se a URL SAS for baseada em uma política de acesso, verifique se a política de acesso não foi revogada.

Se você anexou acidentalmente usando uma URL SAS inválida e não puder desanexar, siga estas etapas:

1. Ao executar Gerenciador de Armazenamento, pressione F12 para abrir a janela ferramentas de desenvolvedor.
2. Clique na guia aplicativo e, em seguida, clique em armazenamento local > file://na árvore à esquerda.
3. Localize a chave associada ao tipo de serviço do URI de SAS problemático. Por exemplo, se o URI de SAS inválidos for para um contêiner de BLOB, procure `StorageExplorer_AddStorageServiceSAS_v1_blob`a chave chamada.
4. O valor da chave deve ser uma matriz JSON. Localize o objeto associado ao URI inadequado e remova-o.
5. Pressione CTRL + R para recarregar Gerenciador de Armazenamento.

## <a name="linux-dependencies"></a>Dependências do Linux

<!-- Storage Explorer 1.9.0 and later is available as a snap from the Snap Store. The Storage Explorer snap installs all of its dependencies with no extra hassle.

Storage Explorer requires the use of a password manager, which may need to be connected manually before Storage Explorer will work correctly. You can connect Storage Explorer to your system's password manager with the following command:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

You can also download the application .tar.gz file, but you'll have to install dependencies manually. -->

> [!IMPORTANT]
> Gerenciador de Armazenamento conforme fornecido no download. tar. gz só tem suporte para distribuições do Ubuntu. Outras distribuições não foram verificadas e podem exigir pacotes adicionais ou alternativos.

Esses pacotes são os requisitos mais comuns para Gerenciador de Armazenamento no Linux:

* [Tempo de execução do .NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libgconf-2-4`
* `libgnome-keyring0` ou `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> Gerenciador de Armazenamento versão 1.7.0 e anteriores exigem o .NET Core 2,0. Se você tiver uma versão mais recente do .NET Core instalada, será necessário [aplicar patches Gerenciador de armazenamento](#patching-storage-explorer-for-newer-versions-of-net-core). Se estiver executando o Gerenciador de Armazenamento 1.8.0 ou superior, você poderá usar até o .NET Core 2,2. As versões além de 2,2 não foram verificadas para funcionar neste momento.

# <a name="ubuntu-1904tab1904"></a>[Ubuntu 19.04](#tab/1904)

1. Baixar Gerenciador de Armazenamento.
2. Instale o [tempo de execução do .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current).
3. Execute o seguinte comando:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18.04](#tab/1804)

1. Baixar Gerenciador de Armazenamento.
2. Instale o [tempo de execução do .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current).
3. Execute o seguinte comando:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16.04](#tab/1604)

1. Baixar Gerenciador de Armazenamento
2. Instale o [tempo de execução do .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current).
3. Execute o seguinte comando:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14.04](#tab/1404)

1. Baixar Gerenciador de Armazenamento
2. Instale o [tempo de execução do .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current).
3. Execute o seguinte comando:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Aplicação de patch Gerenciador de Armazenamento para versões mais recentes do .NET Core

Para Gerenciador de Armazenamento 1.7.0 ou mais antigo, talvez seja necessário corrigir a versão do .NET Core usada pelo Gerenciador de Armazenamento.

1. Baixe a versão 1.5.43 do StreamJsonRpc [do NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Procure o link "pacote de download" no lado direito da página.
2. Depois de baixar o pacote, altere sua extensão de `.nupkg` arquivo `.zip`de para.
3. Descompacte o pacote.
4. Abra o `streamjsonrpc.1.5.43/lib/netstandard1.1/` pasta.
5. Copie `StreamJsonRpc.dll` para os seguintes locais dentro da pasta gerenciador de armazenamento:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-azure-portal-doesnt-work"></a>Abrir no Explorer do portal do Azure não funciona

Se o botão "abrir no Explorer" na portal do Azure não funcionar para você, verifique se você está usando um navegador compatível. Os seguintes navegadores foram testados quanto à compatibilidade.
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Passos Seguintes

Se nenhuma das soluções funcionar para você, [abra um problema no GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Você também pode acessar rapidamente o GitHub usando o botão "relatar problema ao GitHub" no canto inferior esquerdo.

![Comentários](./media/storage-explorer-troubleshooting/feedback-button.PNG)
