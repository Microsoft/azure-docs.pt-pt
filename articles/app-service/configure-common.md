---
title: Configurar aplicações no portal - serviço de aplicações do Azure
description: Saiba como configurar definições comuns para uma aplicação de serviço de aplicações no portal do Azure.
keywords: serviço de aplicações do Azure, aplicação web, as definições da aplicação, as variáveis de ambiente
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: bcc970375120f76e4ec8a90f487d251296f92dba
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957914"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Configurar uma aplicação de serviço de aplicações no portal do Azure

Este tópico explica como configurar definições comuns para aplicações web, back-end móvel ou aplicação de API com o [portal do Azure].

## <a name="configure-app-settings"></a>Configurar as definições da aplicação

No serviço de aplicações, utilize as definições da aplicação como variáveis de ambiente. Na [portal do Azure], navegue até à página de gestão da sua aplicação. No menu à esquerda da aplicação, clique em **Configuration** > **configurações de aplicativo**.

![Definições da Aplicação](./media/configure-common/open-ui.png)

Para os desenvolvedores ASP.NET e ASP.NET Core, as definições de configuração de aplicações no serviço de aplicações são como defini-las na `<appSettings>` no *Web. config*, mas os valores no serviço de aplicações de substituição nos *Web. config*. Pode manter as definições de desenvolvimento (por exemplo, MySQL palavra-passe local) *Web. config*, mas seguros no serviço de aplicações de segredos de produção (por exemplo, senha de base de dados MySQL do Azure). O mesmo código utiliza as definições de desenvolvimento quando depurar localmente, e utiliza os segredos de produção quando implementada no Azure.

Pilhas de outros idiomas, da mesma forma, obtém as definições da aplicação como variáveis de ambiente em tempo de execução. Para obter passos específicos de pilha de linguagem, veja:

- [Núcleo do ASP.NET](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Contentores personalizados](containers/configure-custom-container.md#configure-environment-variables)

Definições da aplicação são sempre encriptadas quando armazenada (encriptados em repouso).

> [!NOTE]
> Também podem ser resolvidas de definições da aplicação [Key Vault](/azure/key-vault/) usando [referencia o Key Vault](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Mostrar valores ocultos

Por predefinição, os valores para as definições da aplicação estão ocultos no portal de segurança. Para ver um valor oculto de uma definição de aplicação, clique a **valor** campo dessa definição. Para ver os valores de todas as definições de aplicação, clique nas **Mostrar valor** botão.

### <a name="add-or-edit"></a>Adicionar ou editar

Para adicionar uma nova definição de aplicação, clique em **nova definição de aplicação**. Na caixa de diálogo, pode [adotar a definição de ranhura atual](deploy-staging-slots.md#which-settings-are-swapped).

Para editar uma definição, clique a **editar** botão à direita.

Quando terminar, clique em **atualização**. Não se esqueça de clicar **salvar** de volta a **configuração** página.

> [!NOTE]
> No contentor do Linux padrão ou um contentor personalizado do Linux, como qualquer estrutura de chave JSON aninhada no nome da definição da aplicação `ApplicationInsights:InstrumentationKey` tem de ser configurado no serviço de aplicações como `ApplicationInsights__InstrumentationKey` para o nome da chave. Em outras palavras, qualquer `:` deve ser substituído por `__` (sublinhado duplo).
>

### <a name="edit-in-bulk"></a>Editar em massa

Para adicionar ou editar as definições da aplicação em massa, clique nas **Advanced editar** botão. Quando terminar, clique em **atualização**. Não se esqueça de clicar **salvar** de volta a **configuração** página.

Definições da aplicação têm a formatação do JSON seguinte:

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

Na [portal do Azure], navegue até à página de gestão da aplicação. No menu à esquerda da aplicação, clique em **Configuration** > **configurações de aplicativo**.

![Definições da Aplicação](./media/configure-common/open-ui.png)

Para os desenvolvedores ASP.NET e ASP.NET Core, cadeias de ligação da definição no serviço de aplicações são como defini-las na `<connectionStrings>` no *Web. config*, mas os valores definidos no serviço de aplicações de substituição nos *Web. config*. Pode manter as definições de desenvolvimento (por exemplo, um ficheiro de base de dados) *Web. config* e seguro no serviço de aplicações de segredos de produção (por exemplo, credenciais de base de dados SQL). O mesmo código utiliza as definições de desenvolvimento quando depurar localmente, e utiliza os segredos de produção quando implementada no Azure.

Para outras pilhas de idioma, é melhor usar [as definições da aplicação](#configure-app-settings) em vez disso, como cadeias de ligação requerem formatação especial nas chaves de variável em para poder acessar os valores. Aqui está uma exceção, no entanto: determinados tipos de base de dados do Azure são uma cópia de segurança, juntamente com a aplicação se configurar suas cadeias de caracteres de conexão na sua aplicação. Para obter mais informações, consulte [o que obtém uma cópia de segurança](manage-backup.md#what-gets-backed-up). Se não tiver esta cópia de segurança automatizada, em seguida, utilize as definições da aplicação.

No tempo de execução, as cadeias de ligação estão disponíveis como variáveis de ambiente, o prefixo com os seguintes tipos de ligação:

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* Base de dados SQL: `SQLAZURECONNSTR_`
* Personalizada: `CUSTOMCONNSTR_`

Por exemplo, uma cadeia de ligação do MySql com o nome *connectionstring1* pode ser acessada como a variável de ambiente `MYSQLCONNSTR_connectionString1`. Para obter passos específicos de pilha de linguagem, veja:

- [Núcleo do ASP.NET](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Contentores personalizados](containers/configure-custom-container.md#configure-environment-variables)

Cadeias de ligação são sempre encriptadas quando armazenada (encriptados em repouso).

> [!NOTE]
> Também podem ser resolvidas de cadeias de ligação [Key Vault](/azure/key-vault/) usando [referencia o Key Vault](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Mostrar valores ocultos

Por predefinição, os valores de cadeias de ligação estão ocultos no portal de segurança. Basta clicar para ver um valor oculto de uma cadeia de ligação, o **valor** campo dessa cadeia de caracteres. Para ver os valores de todas as cadeias de ligação, clique nas **Mostrar valor** botão.

### <a name="add-or-edit"></a>Adicionar ou editar

Para adicionar uma nova cadeia de ligação, clique em **nova cadeia de ligação**. Na caixa de diálogo, pode [permanecer fiel a cadeia de ligação para a ranhura atual](deploy-staging-slots.md#which-settings-are-swapped).

Para editar uma definição, clique a **editar** botão à direita.

Quando terminar, clique em **atualização**. Não se esqueça de clicar **salvar** de volta a **configuração** página.

### <a name="edit-in-bulk"></a>Editar em massa

Para adicionar ou editar as cadeias de ligação em massa, clique nas **Advanced editar** botão. Quando terminar, clique em **atualização**. Não se esqueça de clicar **salvar** de volta a **configuração** página.

Cadeias de ligação têm a formatação do JSON seguinte:

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

Na [portal do Azure], navegue até à página de gestão da aplicação. No menu à esquerda da aplicação, clique em **Configuration** > **configurações de aplicativo**.

![Definições gerais](./media/configure-common/open-general.png)

Aqui, pode configurar algumas definições comuns para a aplicação. Algumas configurações exigem que [aumente verticalmente para escalões de preço superior](web-sites-scale.md).

- **Definições de pilha**: A pilha de software para executar a aplicação, incluindo o idioma e as versões do SDK. Para aplicações do Linux e aplicações de contentor personalizado, também pode definir um ficheiro ou do comando de arranque opcional.
- **As configurações da plataforma**: Permite-lhe configurar definições para a plataforma de hospedagem, incluindo:
    - **Número de bits**: 32 bits ou 64 bits.
    - **Protocolo WebSocket**: Para [SignalR de ASP.NET] ou [socket.io](https://socket.io/), por exemplo.
    - **Always On**: Manter a aplicação carregada, mesmo quando não houver tráfego. Terá de ativá-la para o WebJobs contínuos ou para o WebJobs que são acionados com uma expressão CRON.
    - **Versão de pipeline gerido**: O IIS [modo de pipeline]. Defina-o como **clássico** se tiver uma aplicação legada que requer uma versão mais antiga do IIS.
    - **Versão HTTP**: Defina como **2.0** para ativar o suporte para [HTTPS/2](https://wikipedia.org/wiki/HTTP/2) protocolo.
    > [!NOTE]
    > Os navegadores mais modernos suportam protocolo HTTP/2 por TLS apenas, enquanto o tráfego não encriptadas continua a utilizar HTTP/1.1. Para se certificar de que o cliente navegadores ligar à sua aplicação com o HTTP/2, seja [compre um certificado de serviço de aplicações](web-sites-purchase-ssl-web-site.md) para o domínio personalizado da aplicação ou [vincular um certificado de terceiros](app-service-web-tutorial-custom-ssl.md).
    - **Afinidade ARR**: Numa implementação de várias instâncias, certifique-se de que o cliente é encaminhado para a mesma instância para o ciclo de vida da sessão. Pode configurar esta opção como **desativar** para aplicativos sem monitoração de estado.
- **Depuração**: Ativar a depuração remota para [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug), [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure), ou [node. js](containers/configure-language-nodejs.md#debug-remotely) aplicações. Esta opção se desligar automaticamente após 48 horas.
- **Certificados de cliente recebidos**: exigir certificados de cliente no [autenticação mútua](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Configurar documentos predefinidos

Esta definição é apenas para aplicações do Windows.

Na [portal do Azure], navegue até à página de gestão da aplicação. No menu à esquerda da aplicação, clique em **Configuration** > **predefinido documentos**.

![Definições gerais](./media/configure-common/open-documents.png)

O documento predefinido é a página da web que é apresentado no URL raiz para um Web site. É utilizado o primeiro arquivo correspondente na lista. Para adicionar um novo documento predefinido, clique em **novo documento**. Não se esqueça de clicar **guardar**.

Se a aplicação utilizar módulos que encaminham com base no URL em vez de servir conteúdo estático, não é necessário para documentos predefinidos.

## <a name="configure-path-mappings"></a>Configurar mapeamentos de caminho

Na [portal do Azure], navegue até à página de gestão da aplicação. No menu à esquerda da aplicação, clique em **Configuration** > **mapeamentos de caminho**.

![Definições gerais](./media/configure-common/open-path.png)

O **mapeamentos de caminho** página mostra-lhe coisas diferentes, consoante o tipo de SO.

### <a name="windows-apps-uncontainerized"></a>Aplicações do Windows (uncontainerized)

Para aplicações do Windows, pode personalizar os mapeamentos do processador IIS e aplicações virtuais e diretórios.

Mapeamentos do processador permitem-lhe adicionar processadores de script personalizado para processar pedidos para extensões de ficheiro específicas. Para adicionar um manipulador personalizado, clique em **novo manipulador**. Configure o manipulador da seguinte forma:

- **Extensão**. A extensão de ficheiro que pretende processar, tal como  *\*PHP* ou *handler.fcgi*.
- **Processador de scripts**. O caminho absoluto do processador de script para. Pedidos para ficheiros correspondentes à extensão de ficheiro são processados pelo processador do script. Utilize o caminho `D:\home\site\wwwroot` para fazer referência ao diretório de raiz da sua aplicação.
- **Argumentos**. Argumentos da linha de comandos opcionais para o processador de scripts.

Cada aplicação tem o caminho de raiz predefinido (`/`) mapeado para `D:\home\site\wwwroot`, onde o seu código é implementado por predefinição. Se a raiz da aplicação está numa pasta diferente, ou se o seu repositório tem mais de uma aplicação, pode editar ou adicionar aplicativos virtuais e diretórios aqui. Clique em **novo aplicativo virtual ou diretório**.

Para configurar aplicações virtuais e diretórios, especificar cada diretório virtual e o caminho físico correspondente relativo à raiz do Web site (`D:\home`). Opcionalmente, pode selecionar o **aplicativo** caixa de verificação para marcar um diretório virtual como uma aplicação.

### <a name="containerized-apps"></a>Aplicações em contentores

Pode [adicionar armazenamento personalizado para a sua aplicação em contentores](containers/how-to-serve-content-from-azure-storage.md). As aplicações em contentores incluem todas as aplicações do Linux e também os contentores de personalizado Windows e Linux em execução no serviço de aplicações. Clique em **armazenamento do Azure novo montar** e configurar o armazenamento de personalizado da seguinte forma:

- **Nome**: O nome a apresentar.
- **Opções de configuração**: **Básica** ou **Advanced**.
- **Contas de armazenamento**: A conta de armazenamento com o contentor que pretende.
- **Tipo de armazenamento**: **Blobs do Azure** ou **os ficheiros do Azure**.
  > [!NOTE]
  > Aplicações de contentor do Windows só suportam ficheiros do Azure.
- **Contentor de armazenamento**: Para a configuração básica, o contentor que pretende.
- **Nome da partilha**: Para uma configuração avançada, o partilha de ficheiros nome.
- **Chave de acesso**: Para configuração avançada, a chave de acesso.
- **Caminho de montagem**: O caminho absoluto no seu contentor de montagem do armazenamento personalizado.

Para obter mais informações, consulte [servir conteúdo a partir do armazenamento do Azure no serviço de aplicações no Linux](containers/how-to-serve-content-from-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Configurar definições de pilha de idioma

Para aplicações do Linux, consulte:

- [Núcleo do ASP.NET](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Configurar contentores personalizados

Consulte [configurar um contentor personalizado do Linux para o serviço de aplicações do Azure](containers/configure-custom-container.md)

## <a name="next-steps"></a>Passos Seguintes

- [Configurar um nome de domínio personalizado no Serviço de Aplicações do Azure]
- [Configurar ambientes de teste no Serviço de Aplicações do Azure]
- [Ativar HTTPS para uma aplicação no serviço de aplicações do Azure]
- [Ativar registos de diagnóstico](troubleshoot-diagnostic-logs.md)
- [Dimensionar uma aplicação no serviço de aplicações do Azure]
- [Noções básicas de monitorização no App Service do Azure]
- [Alterar as definições de applicationHost. config com applicationHost.xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[SignalR de ASP.NET]: https://www.asp.net/signalr
[Portal do Azure]: https://portal.azure.com/
[Configurar um nome de domínio personalizado no Serviço de Aplicações do Azure]: ./app-service-web-tutorial-custom-domain.md
[Configurar ambientes de teste no Serviço de Aplicações do Azure]: ./deploy-staging-slots.md
[Ativar HTTPS para uma aplicação no serviço de aplicações do Azure]: ./app-service-web-tutorial-custom-ssl.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Noções básicas de monitorização no App Service do Azure]: ./web-sites-monitor.md
[modo de pipeline]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Dimensionar uma aplicação no serviço de aplicações do Azure]: ./web-sites-scale.md
