---
title: Configure aplicações no portal
description: Aprenda a configurar configurações comuns para uma aplicação de Serviço de Aplicações no portal Azure. Definições de aplicativos, cordas de ligação, plataforma, pilha de idiomas, recipiente, etc.
keywords: serviço de aplicações azure, aplicação web, configurações de aplicativos, variáveis ambientais
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.topic: article
ms.date: 08/13/2019
ms.custom: seodec18
ms.openlocfilehash: ce0a170a629f347e2687a2e9f63fb3438fe2bd2f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280173"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Configure uma app de serviço de aplicações no portal Azure

Este tópico explica como configurar configurações comuns para aplicações web, back back back móvel ou aplicação API usando o [Portal do Azure].

## <a name="configure-app-settings"></a>Configurar as definições da aplicação

No Serviço de Aplicações, as definições de aplicações são variáveis passadas como variáveis ambientais para o código de aplicação. Para aplicações Linux e recipientes personalizados, o App Service passa as definições de aplicativos para o contentor usando a bandeira `--env` para definir a variável ambiental no recipiente.

No [Portal do Azure]procure e selecione **Serviços de Aplicações**e, em seguida, selecione a sua aplicação. 

![Pesquisa de Serviços de Aplicações](./media/configure-common/search-for-app-services.png)

No menu esquerdo da aplicação, selecione definições de **Configuração** > **Aplicação**.

![Definições de aplicação](./media/configure-common/open-ui.png)

Para ASP.NET e ASP.NET os desenvolvedores core, definir as definições de aplicativos no Serviço de Aplicações são como defini-las em `<appSettings>` em *Web.config* ou *appsettings.json*, mas os valores no Serviço de Aplicações sobrepõem-se aos *de Web.config* ou *appsettings.json*. Pode manter as definições de desenvolvimento (por exemplo, senha mySQL local) em *Web.config* ou *appsettings.json,* mas segredos de produção (por exemplo, senha de base de dados Azure MySQL) seguros no Serviço de Aplicações. O mesmo código utiliza as definições de desenvolvimento quando depura localmente, e utiliza os seus segredos de produção quando implantado no Azure.

Outras pilhas de idiomas, da mesma forma, obtêm as definições da aplicação como variáveis ambientais no tempo de execução. Para passos específicos da pilha de idiomas, consulte:

- [Núcleo do ASP.NET](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Recipientes personalizados](containers/configure-custom-container.md#configure-environment-variables)

As definições da aplicação são sempre encriptadas quando armazenadas (encriptadas em repouso).

> [!NOTE]
> As definições da aplicação também podem ser resolvidas a partir do [Cofre chave](/azure/key-vault/) utilizando [referências chave vault](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Mostrar valores ocultos

Por padrão, os valores para definições de aplicações estão escondidos no portal para segurança. Para ver um valor oculto de uma definição de aplicação, clique no campo **Valor** dessa definição. Para ver os valores de todas as definições da aplicação, clique no botão de **valor do Show.**

### <a name="add-or-edit"></a>Adicionar ou editar

Para adicionar uma nova definição de aplicação, clique na **definição de nova aplicação**. No diálogo, pode [colar a regulação à ranhura atual](deploy-staging-slots.md#which-settings-are-swapped).

Para editar uma definição, clique no botão **Editar** no lado direito.

Quando terminar, clique em **Atualizar**. Não se esqueça de clicar em **Guardar** de volta na página de **Configuração.**

> [!NOTE]
> Num recipiente Linux padrão ou num recipiente Linux personalizado, qualquer estrutura chave JSON aninhada no nome de definição de aplicações como `ApplicationInsights:InstrumentationKey` precisa de ser configurada no App Service como `ApplicationInsights__InstrumentationKey` para o nome-chave. Por outras palavras, qualquer `:` deve ser substituído por `__` (duplo sublinhado).
>

### <a name="edit-in-bulk"></a>Editar a granel

Para adicionar ou editar as definições de aplicações a granel, clique no botão **de edição Avançada.** Quando terminar, clique em **Atualizar**. Não se esqueça de clicar em **Guardar** de volta na página de **Configuração.**

As definições da aplicação têm a seguinte formatação JSON:

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

No [Portal do Azure]procure e selecione **Serviços de Aplicações**e, em seguida, selecione a sua aplicação. No menu esquerdo da aplicação, selecione definições de **Configuração** > **Aplicação**.

![Definições de aplicação](./media/configure-common/open-ui.png)

Para ASP.NET e ASP.NET os desenvolvedores core, definir cordas de ligação no Serviço de Aplicações é como defini-los em `<connectionStrings>` em *Web.config*, mas os valores que definiu no Serviço de Aplicações sobrepõem-se aos *da Web.config*. Pode manter as definições de desenvolvimento (por exemplo, um ficheiro de base de dados) em *Web.config* e segredos de produção (por exemplo, credenciais de base de dados SQL) com segurança no Serviço de Aplicações. O mesmo código utiliza as definições de desenvolvimento quando depura localmente, e utiliza os seus segredos de produção quando implantado no Azure.

Para outras pilhas de idiomas, é melhor utilizar as definições da [aplicação,](#configure-app-settings) porque as cordas de ligação requerem formatação especial nas teclas variáveis para aceder aos valores. No entanto, aqui está uma exceção: certos tipos de bases de dados do Azure são apoiados juntamente com a aplicação se configurar as suas cordas de ligação na sua aplicação. Para mais informações, veja [o que é apoiado.](manage-backup.md#what-gets-backed-up) Se não precisar desta cópia de segurança automatizada, utilize as definições da aplicação.

No tempo de execução, as cordas de ligação estão disponíveis como variáveis ambientais, pré-fixadas com os seguintes tipos de ligação:

* Servidor SQL: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* Base de Dados SQL: `SQLAZURECONNSTR_`
* Costume: `CUSTOMCONNSTR_`

Por exemplo, uma cadeia de ligação MySql chamada string1 de *ligação* pode ser acedida à medida que a variável ambiental `MYSQLCONNSTR_connectionString1`. Para passos específicos da pilha de idiomas, consulte:

- [Núcleo do ASP.NET](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Recipientes personalizados](containers/configure-custom-container.md#configure-environment-variables)

As cordas de ligação são sempre encriptadas quando armazenadas (encriptadas em repouso).

> [!NOTE]
> As cordas de ligação também podem ser resolvidas a partir do [Cofre chave](/azure/key-vault/) utilizando [referências chave vault](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Mostrar valores ocultos

Por padrão, os valores das cordas de ligação estão escondidos no portal para segurança. Para ver um valor oculto de uma cadeia de ligação, basta clicar no campo **Valor** dessa corda. Para ver os valores de todas as cordas de ligação, clique no botão de **valor do Show.**

### <a name="add-or-edit"></a>Adicionar ou editar

Para adicionar uma nova cadeia de ligação, clique em Nova cadeia de **ligação**. No diálogo, pode [colar a corda de ligação à ranhura atual](deploy-staging-slots.md#which-settings-are-swapped).

Para editar uma definição, clique no botão **Editar** no lado direito.

Quando terminar, clique em **Atualizar**. Não se esqueça de clicar em **Guardar** de volta na página de **Configuração.**

### <a name="edit-in-bulk"></a>Editar a granel

Para adicionar ou editar cordas de ligação a granel, clique no botão **de edição Avançada.** Quando terminar, clique em **Atualizar**. Não se esqueça de clicar em **Guardar** de volta na página de **Configuração.**

As cordas de ligação têm a seguinte formatação JSON:

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

No [Portal do Azure]procure e selecione **Serviços de Aplicações**e, em seguida, selecione a sua aplicação. No menu esquerdo da aplicação, selecione **Configuração** > **configurações gerais**.

![Definições gerais](./media/configure-common/open-general.png)

Aqui, pode configurar algumas definições comuns para a aplicação. Algumas definições exigem que se dimensione para níveis de [preços mais elevados](manage-scale-up.md).

- **Configurações de stack**: A pilha de software para executar a aplicação, incluindo o idioma e as versões SDK. Para aplicações Linux e aplicações de contentores personalizadas, também pode definir um comando ou ficheiro de arranque opcional.
- **Definições da plataforma**: Permite configurar as definições para a plataforma de hospedagem, incluindo:
    - **Bitness**: 32-bit ou 64-bit.
    - **Protocolo WebSocket**: Para [sinal de ASP.NET] ou [socket.io,](https://socket.io/)por exemplo.
    - **Always On**: Mantenha a aplicação carregada mesmo quando não há trânsito. É necessário para WebJobs contínuos ou para WebJobs que são desencadeados usando uma expressão CRON.
      > [!NOTE]
      > Com a funcionalidade Always On, não se pode controlar o ponto final. Envia sempre um pedido para a raiz de aplicação.
    - **Versão gerida do gasoduto**: O [modo de gasoduto]IIS . Desloque-o para **Classic** se tiver uma aplicação antiga que requer uma versão mais antiga do IIS.
    - **Versão HTTP**: Definir para **2.0** para permitir o suporte ao protocolo [HTTPS/2.](https://wikipedia.org/wiki/HTTP/2)
    > [!NOTE]
    > A maioria dos navegadores modernos suportam o protocolo HTTP/2 apenas sobre tLS, enquanto o tráfego não encriptado continua a usar HTTP/1.1. Para garantir que os navegadores de clientes se conectam à sua aplicação com HTTP/2, [proteja o seu nome DNS personalizado com uma ligação SSL no Serviço de Aplicações Azure](configure-ssl-bindings.md).
    - **Afinidade ARR**: Numa implantação em várias instâncias, certifique-se de que o cliente é encaminhado para o mesmo caso durante a vida útil da sessão. Pode definir esta opção para **Off** para aplicações apátridas.
- **Depuração**: Ative a depuração remota para [aplicações ASP.NET,](troubleshoot-dotnet-visual-studio.md#remotedebug) [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)ou [Node.js.](containers/configure-language-nodejs.md#debug-remotely) Esta opção desliga-se automaticamente após 48 horas.
- **Certificados**de cliente de entrada : exigir certificados de cliente em [autenticação mútua](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Configurar documentos predefinidos

Esta definição é apenas para aplicações windows.

No [Portal do Azure]procure e selecione **Serviços de Aplicações**e, em seguida, selecione a sua aplicação. No menu esquerdo da aplicação, selecione **Configuração** > **documentos Predefinidos**.

![Documentos predefinidos](./media/configure-common/open-documents.png)

O documento predefinido é a página web que é exibida no URL raiz para um site. O primeiro ficheiro correspondente na lista é utilizado. Para adicionar um novo documento predefinido, clique em **Novo documento**. Não se esqueça de clicar em **Guardar**.

Se a aplicação utilizar módulos que encaminham com base em URL em vez de servir conteúdo estático, não há necessidade de documentos predefinidos.

## <a name="configure-path-mappings"></a>Configurar mapeamentos de caminhos

No [Portal do Azure]procure e selecione **Serviços de Aplicações**e, em seguida, selecione a sua aplicação. No menu esquerdo da aplicação, selecione **Configuração** > **Mapeamentos caminho**.

![Mapeamento de caminhos](./media/configure-common/open-path.png)

A página de **mapeamento si** mostra coisas diferentes com base no tipo S.

### <a name="windows-apps-uncontainerized"></a>Aplicativos para janelas (não contentorizados)

Para aplicações Windows, pode personalizar os mapeamentos do manipulador IIS e aplicações virtuais e diretórios.

Os mapeamentos do manipulador permitem adicionar processadores de script personalizados para lidar com pedidos de extensões de ficheiros específicas. Para adicionar um manipulador personalizado, clique em **Novo manipulador**. Configure o manipulador da seguinte forma:

- **Extensão**. A extensão do ficheiro que pretende manusear, como *\*.php* ou *handler.fcgi*.
- **Processador script**. O caminho absoluto do processador de scripts para si. Os pedidos para ficheiros que correspondam à extensão do ficheiro são processados pelo processador de scripts. Utilize o caminho `D:\home\site\wwwroot` para se referir ao diretório raiz da sua aplicação.
- **Argumentos.** Argumentos opcionais da linha de comando para o processador de scripts.

Cada aplicação tem o caminho raiz padrão (`/`) mapeado para `D:\home\site\wwwroot`, onde o seu código é implementado por padrão. Se a sua raiz de aplicação estiver numa pasta diferente, ou se o seu repositório tiver mais do que uma aplicação, pode editar ou adicionar aplicações virtuais e diretórios aqui. Clique em **Nova aplicação virtual ou diretório.**

Para configurar aplicações e diretórios virtuais, especifique cada diretório virtual e o seu caminho físico correspondente em relação à raiz do website (`D:\home`). Opcionalmente, pode selecionar a caixa de verificação **da Aplicação** para marcar um diretório virtual como aplicação.

### <a name="containerized-apps"></a>Aplicativos contentorizados

Pode [adicionar armazenamento personalizado para a sua aplicação contentorizada](containers/how-to-serve-content-from-azure-storage.md). As aplicações contentorizadas incluem todas as aplicações Linux e também os recipientes personalizados Windows e Linux que estão em execução no Serviço de Aplicações. Clique no Novo Monte de **Armazenamento Azure** e configure o seu armazenamento personalizado da seguinte forma:

- **Nome**: O nome do visor.
- **Opções de configuração**: **Básico** ou **Avançado**.
- **Contas de armazenamento**: A conta de armazenamento com o recipiente que deseja.
- Tipo de **armazenamento:** **Azure Blobs** ou **Azure Files**.
  > [!NOTE]
  > As aplicações de contentores windows suportam apenas ficheiros Azure.
- **Recipiente de armazenamento**: Para configuração básica, o recipiente que desejar.
- **Nome da partilha**: Para configuração avançada, o nome da partilha de ficheiros.
- **Chave de acesso**: Para uma configuração avançada, a chave de acesso.
- **Caminho do montagem**: O caminho absoluto no seu recipiente para montar o armazenamento personalizado.

Para obter mais informações, veja [Fornecer conteúdo do Armazenamento do Microsoft Azure no Serviço de Aplicações no Linux](containers/how-to-serve-content-from-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Configurar definições de pilha de idiomas

Para aplicativos Linux, consulte:

- [Núcleo do ASP.NET](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
- [python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Configurar recipientes personalizados

Consulte [um recipiente linux personalizado para o Serviço de Aplicações Azure](containers/configure-custom-container.md)

## <a name="next-steps"></a>Passos Seguintes

- [Configurar um nome de domínio personalizado no Serviço de Aplicações do Azure]
- [Configurar ambientes de teste no Serviço de Aplicações do Azure]
- [Proteger um nome DNS personalizado com um enlace SSL no Serviço de Aplicações do Azure](configure-ssl-bindings.md)
- [Ativar registos de diagnóstico](troubleshoot-diagnostic-logs.md)
- [Escala uma aplicação no Serviço de Aplicações Azure]
- [Monitorização de bases no Serviço de Aplicações Azure]
- [Alterar as definições host.config com applicationHost.xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[sinal de ASP.NET]: https://www.asp.net/signalr
[Portal do Azure]: https://portal.azure.com/
[Configurar um nome de domínio personalizado no Serviço de Aplicações do Azure]: ./app-service-web-tutorial-custom-domain.md
[Configurar ambientes de teste no Serviço de Aplicações do Azure]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Monitorização de bases no Serviço de Aplicações Azure]: ./web-sites-monitor.md
[modo de gasoduto]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Escala uma aplicação no Serviço de Aplicações Azure]: ./manage-scale-up.md
