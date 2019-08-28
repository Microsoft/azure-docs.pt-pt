---
title: Configurar aplicativos no portal-serviço de Azure App
description: Saiba como definir configurações comuns para um aplicativo do serviço de aplicativo no portal do Azure.
keywords: serviço de aplicativo do Azure, aplicativo Web, configurações de aplicativo, variáveis de ambiente
services: app-service\web
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 11a29a980fbbbafad850daeda5af11b78580bcaa
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067015"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Configurar um aplicativo do serviço de aplicativo no portal do Azure

Este tópico explica como definir configurações comuns para aplicativos Web, back-end móvel ou aplicativo de API usando o [portal do Azure].

## <a name="configure-app-settings"></a>Configurar as definições da aplicação

No serviço de aplicativo, as configurações de aplicativo são variáveis passadas como variáveis de ambiente para o código do aplicativo. Para aplicativos Linux e contêineres personalizados, o serviço de aplicativo passa configurações de aplicativo para `--env` o contêiner usando o sinalizador para definir a variável de ambiente no contêiner.

Na [portal do Azure], navegue até a página de gerenciamento do aplicativo. No menu à esquerda do aplicativo, clique em**configurações do aplicativo**de **configuração** > .

![Definições da Aplicação](./media/configure-common/open-ui.png)

Para os desenvolvedores de ASP.net e ASP.NET Core, definir configurações de aplicativo no serviço de aplicativo é `<appSettings>` como configurá-los no *Web. config* ou *appSettings. JSON*, mas os valores no serviço de aplicativo substituem aqueles em *Web. config* ou  *appSettings. JSON*. Você pode manter as configurações de desenvolvimento (por exemplo, a senha local do MySQL) em *Web. config* ou *appSettings. JSON*, mas os segredos de produção (por exemplo, a senha do banco de dados MySQL do Azure) são seguros no serviço de aplicativo. O mesmo código usa suas configurações de desenvolvimento quando você depura localmente e usa seus segredos de produção quando implantado no Azure.

Outras pilhas de idiomas, da mesma forma, obtêm as configurações do aplicativo como variáveis de ambiente no tempo de execução. Para obter as etapas específicas da pilha de idiomas, consulte:

- [Núcleo do ASP.NET](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Contêineres personalizados](containers/configure-custom-container.md#configure-environment-variables)

As configurações do aplicativo são sempre criptografadas quando armazenadas (criptografadas em repouso).

> [!NOTE]
> As configurações do aplicativo também podem ser resolvidas de [Key Vault](/azure/key-vault/) usando [referências Key Vault](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Mostrar valores ocultos

Por padrão, os valores para as configurações do aplicativo ficam ocultos no portal para segurança. Para ver um valor oculto de uma configuração de aplicativo, clique no campo **valor** dessa configuração. Para ver os valores de todas as configurações de aplicativo, clique no botão **Mostrar valor** .

### <a name="add-or-edit"></a>Adicionar ou editar

Para adicionar uma nova configuração de aplicativo, clique em **nova configuração de aplicativo**. Na caixa de diálogo, você pode [fixar a configuração no slot atual](deploy-staging-slots.md#which-settings-are-swapped).

Para editar uma configuração, clique no botão **Editar** no lado direito.

Quando terminar, clique em **Atualizar**. Não se esqueça de clicar em **salvar** de volta na página de **configuração** .

> [!NOTE]
> Em um contêiner do Linux padrão ou um contêiner personalizado do Linux, qualquer estrutura de chave JSON aninhada no nome `ApplicationInsights:InstrumentationKey` da configuração do aplicativo como precisa ser configurada no serviço de aplicativo como `ApplicationInsights__InstrumentationKey` para o nome da chave. Em outras palavras, qualquer `:` um deve ser substituído `__` por (sublinhado duplo).
>

### <a name="edit-in-bulk"></a>Editar em massa

Para adicionar ou editar as configurações do aplicativo em massa, clique no botão de **edição avançado** . Quando terminar, clique em **Atualizar**. Não se esqueça de clicar em **salvar** de volta na página de **configuração** .

As configurações do aplicativo têm a seguinte formatação JSON:

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

## <a name="configure-connection-strings"></a>Configurar cadeias de ligação

Na [portal do Azure], navegue até a página de gerenciamento do aplicativo. No menu à esquerda do aplicativo, clique em**configurações do aplicativo**de **configuração** > .

![Definições da Aplicação](./media/configure-common/open-ui.png)

Para desenvolvedores de ASP.net e ASP.NET Core, definir cadeias de conexão no serviço de aplicativo é `<connectionStrings>` como configurá-los no *Web. config*, mas os valores definidos no serviço de aplicativo substituem aqueles em *Web. config*. Você pode manter as configurações de desenvolvimento (por exemplo, um arquivo de banco de dados) em *Web. config* e segredos de produção (por exemplo, credenciais do banco de dados SQL) seguras no serviço de aplicativo. O mesmo código usa suas configurações de desenvolvimento quando você depura localmente e usa seus segredos de produção quando implantado no Azure.

Para outras pilhas de idiomas, é melhor usar [as configurações do aplicativo](#configure-app-settings) , pois as cadeias de conexão exigem formatação especial nas chaves variáveis para acessar os valores. Aqui está uma exceção. no entanto: certos tipos de banco de dados do Azure são submetidos a backup junto com o aplicativo se você configurar suas cadeias de conexão em seu aplicativo. Para obter mais informações, consulte [o que é feito](manage-backup.md#what-gets-backed-up)backup. Se você não precisar desse backup automatizado, use as configurações do aplicativo.

Em tempo de execução, as cadeias de conexão estão disponíveis como variáveis de ambiente, prefixadas com os seguintes tipos de conexão:

* SQL Server: `SQLCONNSTR_`
* MySQL`MYSQLCONNSTR_`
* Banco de dados SQL:`SQLAZURECONNSTR_`
* Personalizar`CUSTOMCONNSTR_`

Por exemplo, uma cadeia de conexão do MySql chamada *connectionstring1* pode ser acessada `MYSQLCONNSTR_connectionString1`como a variável de ambiente. Para obter as etapas específicas da pilha de idiomas, consulte:

- [Núcleo do ASP.NET](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Contêineres personalizados](containers/configure-custom-container.md#configure-environment-variables)

As cadeias de conexão são sempre criptografadas quando armazenadas (criptografadas em repouso).

> [!NOTE]
> As cadeias de conexão também podem ser resolvidas de [Key Vault](/azure/key-vault/) usando [referências Key Vault](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Mostrar valores ocultos

Por padrão, os valores para cadeias de conexão ficam ocultos no portal para segurança. Para ver um valor oculto de uma cadeia de conexão, basta clicar no campo **valor** dessa cadeia de caracteres. Para ver os valores de todas as cadeias de conexão, clique no botão **Mostrar valor** .

### <a name="add-or-edit"></a>Adicionar ou editar

Para adicionar uma nova cadeia de conexão, clique em **nova cadeia de conexão**. Na caixa de diálogo, você pode colocar [a cadeia de conexão no slot atual](deploy-staging-slots.md#which-settings-are-swapped).

Para editar uma configuração, clique no botão **Editar** no lado direito.

Quando terminar, clique em **Atualizar**. Não se esqueça de clicar em **salvar** de volta na página de **configuração** .

### <a name="edit-in-bulk"></a>Editar em massa

Para adicionar ou editar cadeias de conexão em massa, clique no botão de **edição avançado** . Quando terminar, clique em **Atualizar**. Não se esqueça de clicar em **salvar** de volta na página de **configuração** .

As cadeias de conexão têm a seguinte formatação JSON:

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>Configurar as definições gerais

Na [portal do Azure], navegue até a página de gerenciamento do aplicativo. No menu à esquerda do aplicativo, clique em**configurações do aplicativo**de **configuração** > .

![Definições gerais](./media/configure-common/open-general.png)

Aqui, você pode definir algumas configurações comuns para o aplicativo. Algumas configurações exigem que você [Escale verticalmente para os tipos de preço mais altos](manage-scale-up.md).

- **Configurações de pilha**: A pilha de software para executar o aplicativo, incluindo as versões de linguagem e SDK. Para aplicativos do Linux e aplicativos de contêiner personalizados, você também pode definir um arquivo ou comando de inicialização opcional.
- **Configurações da plataforma**: Permite que você defina as configurações para a plataforma de hospedagem, incluindo:
    - Bit de **bits**: 32 bits ou 64 bits.
    - **Protocolo WebSocket**: Para [Sinalizador ASP.NET] ou [Socket.Io](https://socket.io/), por exemplo.
    - **Always on**: Mantenha o aplicativo carregado mesmo quando não houver tráfego. Ele é necessário para trabalhos Web contínuos ou para trabalhos Web que são disparados usando uma expressão CRON.
    - **Versão do pipeline gerenciado**: O [modo de pipeline]do IIS. Defina-o como **clássico** se você tiver um aplicativo herdado que exija uma versão mais antiga do IIS.
    - **Versão de http**: Defina como **2,0** para habilitar o suporte para o protocolo [https/2](https://wikipedia.org/wiki/HTTP/2) .
    > [!NOTE]
    > A maioria dos navegadores modernos dá suporte ao protocolo HTTP/2 somente por TLS, enquanto o tráfego não criptografado continua a usar HTTP/1.1. Para garantir que os navegadores cliente se conectem ao seu aplicativo com HTTP/2, [compre um certificado do serviço de aplicativo](web-sites-purchase-ssl-web-site.md) para o domínio personalizado do aplicativo ou [associe um certificado de](app-service-web-tutorial-custom-ssl.md)terceiros.
    - **Afinidade de arr**: Em uma implantação de várias instâncias, verifique se o cliente é roteado para a mesma instância durante a vida útil da sessão. Você pode definir essa opção como **off** para aplicativos sem estado.
- **Depuração**: Habilite a depuração remota para aplicativos [ASP.net](troubleshoot-dotnet-visual-studio.md#remotedebug), [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)ou [node. js](containers/configure-language-nodejs.md#debug-remotely) . Essa opção é desativada automaticamente após 48 horas.
- **Certificados de cliente de entrada**: requer certificados de cliente na [autenticação mútua](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Configurar documentos padrão

Essa configuração é apenas para aplicativos do Windows.

Na [portal do Azure], navegue até a página de gerenciamento do aplicativo. No menu à esquerda do aplicativo, clique em **configuração** > **documentos padrão**.

![Definições gerais](./media/configure-common/open-documents.png)

O documento padrão é a página da Web que é exibida na URL raiz de um site. O primeiro arquivo correspondente na lista é usado. Para adicionar um novo documento padrão, clique em **novo documento**. Não se esqueça de clicar em **salvar**.

Se o aplicativo usa módulos que roteiam com base na URL em vez de fornecer conteúdo estático, não há necessidade de documentos padrão.

## <a name="configure-path-mappings"></a>Configurar mapeamentos de caminho

Na [portal do Azure], navegue até a página de gerenciamento do aplicativo. No menu à esquerda do aplicativo, clique em mapeamentos de**caminho**de **configuração** > .

![Definições gerais](./media/configure-common/open-path.png)

A página Mapeamentos de **caminho** mostra diferentes coisas com base no tipo de sistema operacional.

### <a name="windows-apps-uncontainerized"></a>Aplicativos do Windows (não-contêinered)

Para aplicativos do Windows, você pode personalizar os mapeamentos de manipulador do IIS e os diretórios e aplicativos virtuais.

Mapeamentos de manipulador permitem que você adicione processadores de script personalizados para manipular solicitações para extensões de arquivo específicas. Para adicionar um manipulador personalizado, clique em **novo manipulador**. Configure o manipulador da seguinte maneira:

- **Extensão**. A extensão de arquivo que você deseja manipular,  *\*como. php* ou *Handler. fcgi*.
- **Processador de script**. O caminho absoluto do processador de script para você. As solicitações para arquivos que correspondem à extensão de arquivo são processadas pelo processador de script. Use o caminho `D:\home\site\wwwroot` para se referir ao diretório raiz do seu aplicativo.
- **Argumentos**. Argumentos de linha de comando opcionais para o processador de script.

Cada aplicativo tem o caminho raiz padrão (`/`) mapeado para `D:\home\site\wwwroot`, onde seu código é implantado por padrão. Se a raiz do aplicativo estiver em uma pasta diferente ou se o repositório tiver mais de um aplicativo, você poderá editar ou adicionar diretórios e aplicativos virtuais aqui. Clique em **novo aplicativo virtual ou diretório**.

Para configurar aplicativos e diretórios virtuais, especifique cada diretório virtual e seu caminho físico correspondente em relação à raiz do site`D:\home`(). Opcionalmente, você pode marcar a caixa de seleção **aplicativo** para marcar um diretório virtual como um aplicativo.

### <a name="containerized-apps"></a>Aplicativos em contêineres

Você pode [Adicionar armazenamento personalizado para seu aplicativo em contêiner](containers/how-to-serve-content-from-azure-storage.md). Os aplicativos em contêineres incluem todos os aplicativos do Linux e também os contêineres personalizados do Windows e Linux em execução no serviço de aplicativo. Clique em **nova montagem de armazenamento do Azure** e configure seu armazenamento personalizado da seguinte maneira:

- **Nome**: O nome de exibição.
- **Opções de configuração**: **Básico** ou **avançado**.
- **Contas de armazenamento**: A conta de armazenamento com o contêiner desejado.
- **Tipo de armazenamento**: **BLOBs do Azure** ou **arquivos do Azure**.
  > [!NOTE]
  > Os aplicativos de contêiner do Windows só dão suporte a arquivos do Azure.
- **Contêiner de armazenamento**: Para a configuração básica, o contêiner desejado.
- **Nome do compartilhamento**: Para configuração avançada, o nome do compartilhamento de arquivos.
- **Chave de acesso**: Para configuração avançada, a chave de acesso.
- **Caminho de montagem**: O caminho absoluto em seu contêiner para montar o armazenamento personalizado.

Para obter mais informações, consulte [fornecer conteúdo do armazenamento do Azure no serviço de aplicativo no Linux](containers/how-to-serve-content-from-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Definir configurações de pilha de idiomas

Para aplicativos do Linux, consulte:

- [Núcleo do ASP.NET](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Configurar contêineres personalizados

Consulte [configurar um contêiner personalizado do Linux para o serviço Azure app](containers/configure-custom-container.md)

## <a name="next-steps"></a>Passos Seguintes

- [Configurar um nome de domínio personalizado no Serviço de Aplicações do Azure]
- [Configurar ambientes de teste no Serviço de Aplicações do Azure]
- [Habilitar HTTPS para um aplicativo no serviço Azure App]
- [Habilitar logs de diagnóstico](troubleshoot-diagnostic-logs.md)
- [Dimensionar um aplicativo no serviço Azure App]
- [Noções básicas de monitoramento no serviço Azure App]
- [Alterar as configurações de applicationHost. config com applicationHost. xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[Sinalizador ASP.NET]: https://www.asp.net/signalr
[Portal do Azure]: https://portal.azure.com/
[Configurar um nome de domínio personalizado no Serviço de Aplicações do Azure]: ./app-service-web-tutorial-custom-domain.md
[Configurar ambientes de teste no Serviço de Aplicações do Azure]: ./deploy-staging-slots.md
[Habilitar HTTPS para um aplicativo no serviço Azure App]: ./app-service-web-tutorial-custom-ssl.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Noções básicas de monitoramento no serviço Azure App]: ./web-sites-monitor.md
[modo de pipeline]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Dimensionar um aplicativo no serviço Azure App]: ./manage-scale-up.md
