---
title: Configurar aplicativos no portal
description: Aprenda a configurar configurações comuns para uma aplicação de Serviço de Aplicações no portal Azure. Configurações de aplicativos, cordas de conexão, plataforma, pilha de idiomas, recipiente, etc.
keywords: serviço de aplicativos azure, aplicativo web, configurações de aplicativos, variáveis ambientais
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.topic: article
ms.date: 08/13/2019
ms.custom: devx-track-csharp, seodec18, devx-track-azurecli
ms.openlocfilehash: 7c1b9ad033ade3b577dc4a06e84801dab3b7eb70
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763966"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Configurar uma app de Serviço de Aplicações no portal Azure

Este tópico explica como configurar configurações comuns para aplicações web, back end móvel ou app API usando o [portal Azure].

## <a name="configure-app-settings"></a>Configurar as definições da aplicação

No Serviço de Aplicações, as configurações das aplicações são variáveis passadas como variáveis ambientais para o código de aplicação. Para aplicações Linux e recipientes personalizados, o Serviço de Aplicações passa as definições de aplicações para o recipiente usando a `--env` bandeira para definir a variável ambiental no recipiente.

No [portal Azure,]procure e selecione **Serviços de Aplicações** e, em seguida, selecione a sua aplicação. 

![Pesquisa de Serviços de Aplicações](./media/configure-common/search-for-app-services.png)

No menu esquerdo da aplicação, selecione **Configuration**  >  **configurações de Aplicação de** configuração .

![Definições de Aplicação](./media/configure-common/open-ui.png)

Para ASP.NET e ASP.NET os desenvolvedores do Core, configurar as definições de aplicações no Serviço de Aplicações é como defini-las `<appSettings>` em *Web.config* ou *appsettings.jsem*, mas os valores no Serviço de Aplicações substituem os *deWeb.config* ou *appsettings.jsem*. Pode manter as definições de desenvolvimento (por exemplo, a palavra-passe local do MySQL) em *Web.config* ouappsettings.jse segredos *de* produção (por exemplo, palavra-passe de base de dados Azure MySQL) seguros no Serviço de Aplicações. O mesmo código utiliza as definições de desenvolvimento quando depura localmente, e utiliza os seus segredos de produção quando implantado no Azure.

Outras pilhas linguísticas, igualmente, obtêm as configurações da aplicação como variáveis ambientais no tempo de execução. Para etapas específicas de pilha de linguagem, consulte:

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#configure-data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [Personalizar contentores](configure-custom-container.md#configure-environment-variables)

As definições de aplicações são sempre encriptadas quando armazenadas (encriptadas em repouso).

> [!NOTE]
> As definições de aplicativos também podem ser resolvidas a partir do [Key Vault](../key-vault/index.yml) usando [referências do Key Vault](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Mostrar valores ocultos

Por predefinição, os valores das definições de aplicações estão escondidos no portal para segurança. Para ver um valor oculto de uma definição de aplicação, clique no campo **Valor** dessa definição. Para ver os valores de todas as definições de aplicações, clique no botão **De valor Mostrar.**

### <a name="add-or-edit"></a>Adicionar ou editar

Para adicionar uma nova definição de aplicação, clique em **Nova definição de aplicação**. No diálogo, pode [colar a definição à ranhura atual](deploy-staging-slots.md#which-settings-are-swapped).

Para editar uma definição, clique no botão **Editar** no lado direito.

Quando terminar, clique em **Update**. Não se esqueça de clicar em **Guardar** de volta na página **de Configuração.**

> [!NOTE]
> Num recipiente Linux predefinido ou num recipiente Linux personalizado, qualquer estrutura de chave JSON aninhada no nome de definição de aplicações `ApplicationInsights:InstrumentationKey` como deve ser configurada no Serviço de Aplicações como `ApplicationInsights__InstrumentationKey` para o nome chave. Por outras palavras, qualquer `:` um deve ser substituído por `__` (duplo sublinhado).
>

### <a name="edit-in-bulk"></a>Editar a granel

Para adicionar ou editar as definições de aplicações a granel, clique no botão **de edição Advanced.** Quando terminar, clique em **Update**. Não se esqueça de clicar em **Guardar** de volta na página **de Configuração.**

As definições de aplicativo têm a seguinte formatação JSON:

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

### <a name="automate-app-settings-with-the-azure-cli"></a>Automatizar as definições de aplicativos com o Azure CLI

Pode utilizar o CLI Azure para criar e gerir as definições a partir da linha de comando.

- Atribua um valor a uma definição com configurações de configurações de [aplicações config do webapp az](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set):

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings <setting-name>="<value>"
    ```
        
    `<setting-name>`Substitua-o pelo nome da definição e `<value>` pelo valor a atribuir-lhe. Este comando cria a definição se já não existir.
    
- Mostrar todas as configurações e seus valores com [a lista de configurações de configuração de appsapp webapp az](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_list):
    
    ```azurecli-interactive
    az webapp config appsettings list --name <app-name> --resource-group <resource-group-name>
    ```
    
- Remova uma ou mais configurações com configurações de [app config do webapp az delete](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_delete):

    ```azurecli-interactive
    az webapp config appsettings delete --name <app-name> --resource-group <resource-group-name> --setting-names {<names>}
    ```
    
    Substitua `<names>` por uma lista de nomes de definição separadas pelo espaço.

## <a name="configure-connection-strings"></a>Configurar cadeias de ligação

No [portal Azure,]procure e selecione **Serviços de Aplicações** e, em seguida, selecione a sua aplicação. No menu esquerdo da aplicação, selecione **Configuration**  >  **configurações de Aplicação de** configuração .

![Definições de Aplicação](./media/configure-common/open-ui.png)

Para ASP.NET e ASP.NET os desenvolvedores do Core, configurar as cadeias de ligação no Serviço de Aplicações é como defini-las `<connectionStrings>` em *Web.config*, mas os valores que definiu no Serviço de Aplicações substituem os *Web.config*. Pode manter as definições de desenvolvimento (por exemplo, um ficheiro de base de dados) em *segredos deWeb.config* e de produção (por exemplo, credenciais de base de dados SQL) em segurança no Serviço de Aplicações. O mesmo código utiliza as definições de desenvolvimento quando depura localmente, e utiliza os seus segredos de produção quando implantado no Azure.

Para outras pilhas de idiomas, é melhor usar [as definições](#configure-app-settings) de aplicações em vez disso, porque as cordas de conexão requerem formatação especial nas teclas variáveis para aceder aos valores. No entanto, há uma exceção: certos tipos de bases de dados Azure são apoiados juntamente com a aplicação se configurar as suas cadeias de ligação na sua aplicação. Para mais informações, veja [o que é ressoou.](manage-backup.md#what-gets-backed-up) Se não precisar desta cópia de segurança automatizada, use as definições de aplicações.

No tempo de funcionação, as cadeias de ligação estão disponíveis como variáveis ambientais, prefixadas com os seguintes tipos de ligação:

* SQLServer: `SQLCONNSTR_`  
* MySQl: `MYSQLCONNSTR_` 
* SQLAzure: `SQLAZURECONNSTR_` 
* Personalizado: `CUSTOMCONNSTR_`
* PostgresqL: `POSTGRESQLCONNSTR_`  

Por exemplo, uma cadeia de ligação MySql chamada *connectionstring1* pode ser acedida como variável ambiental `MYSQLCONNSTR_connectionString1` . Para etapas específicas de pilha de linguagem, consulte:

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#configure-data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [Personalizar contentores](configure-custom-container.md#configure-environment-variables)

As cadeias de ligação são sempre encriptadas quando armazenadas (encriptadas em repouso).

> [!NOTE]
> As cordas de ligação também podem ser resolvidas a partir do [Key Vault](../key-vault/index.yml) utilizando [referências do Key Vault](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Mostrar valores ocultos

Por predefinição, os valores das cadeias de ligação estão escondidos no portal para segurança. Para ver um valor oculto de uma cadeia de ligação, basta clicar no campo **Valor** dessa cadeia. Para ver os valores de todas as cadeias de ligação, clique no botão **De valor Show.**

### <a name="add-or-edit"></a>Adicionar ou editar

Para adicionar uma nova cadeia de ligação, clique em **Nova cadeia de ligação**. No diálogo, pode [colar o fio de ligação à ranhura atual](deploy-staging-slots.md#which-settings-are-swapped).

Para editar uma definição, clique no botão **Editar** no lado direito.

Quando terminar, clique em **Update**. Não se esqueça de clicar em **Guardar** de volta na página **de Configuração.**

### <a name="edit-in-bulk"></a>Editar a granel

Para adicionar ou editar as cadeias de ligação a granel, clique no botão **de edição Advanced.** Quando terminar, clique em **Update**. Não se esqueça de clicar em **Guardar** de volta na página **de Configuração.**

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

No [portal Azure,]procure e selecione **Serviços de Aplicações** e, em seguida, selecione a sua aplicação. No menu esquerdo da aplicação, selecione **Configuration**  >  **Configuração Definições Gerais**.

![Definições gerais](./media/configure-common/open-general.png)

Aqui, pode configurar algumas definições comuns para a aplicação. Algumas configurações exigem que você [escalone até níveis de preços mais elevados](manage-scale-up.md).

- **Configurações de** pilha : A pilha de software para executar a aplicação, incluindo as versões idioma e SDK.

    Para aplicações Linux e aplicativos de contentores personalizados, pode selecionar a versão de tempo de execução do idioma e definir um **comando de Arranque** opcional ou um ficheiro de comando de arranque.

    ![Configurações gerais para contentores Linux](./media/configure-common/open-general-linux.png)

- **Definições da plataforma**: Permite configurar as definições para a plataforma de hospedagem, incluindo:
    - **Bitness**: 32-bit ou 64-bit.
    - **Protocolo WebSocket**: Para [ASP.NET SignalR] ou [socket.io,](https://socket.io/)por exemplo.
    - **Sempre ligado:** Mantém a aplicação carregada mesmo quando não há trânsito. É necessário para WebJobs contínuos ou para WebJobs que são desencadeados usando uma expressão CRON.
      > [!NOTE]
      > Com a função Always On, o balançador de carga frontal envia um pedido para a raiz da aplicação. Este ponto final de aplicação do Serviço de Aplicações não pode ser configurado.
    - **Versão de gasoduto gerido**: O [modo de gasoduto]IIS . Desempenhe-o para **Classic** se tiver uma aplicação antiga que requer uma versão mais antiga do IIS.
    - **Versão HTTP**: Definir a **2.0** para permitir o suporte para o protocolo [HTTPS/2.](https://wikipedia.org/wiki/HTTP/2)
    > [!NOTE]
    > A maioria dos navegadores modernos suportam o protocolo HTTP/2 apenas sobre TLS, enquanto o tráfego não encriptado continua a utilizar HTTP/1.1. Para garantir que os navegadores de clientes se conectam à sua aplicação com HTTP/2, proteja o seu nome DNS personalizado. Para mais informações, consulte [Secure a custom DNS name with a TLS/SSL binding in Azure App Service](configure-ssl-bindings.md).
    - **Afinidade ARR**: Numa implementação em várias instâncias, certifique-se de que o cliente é encaminhado para o mesmo caso para a vida da sessão. Pode definir esta opção para **Off** para aplicações apátridas.
- **Depuragem**: Habilita a depurar remotamente para [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug), [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure), ou aplicações [deNode.js.](configure-language-nodejs.md#debug-remotely) Esta opção desliga-se automaticamente após 48 horas.
- **Certificados de clientes:** requerem certificados de cliente em [autenticação mútua.](app-service-web-configure-tls-mutual-auth.md)

## <a name="configure-default-documents"></a>Configure documentos padrão

Esta definição destina-se apenas a aplicações do Windows.

No [portal Azure,]procure e selecione **Serviços de Aplicações** e, em seguida, selecione a sua aplicação. No menu esquerdo da aplicação, selecione **Configuration**  >  **Os documentos Padrão de** Configuração .

![Documentos predefinidos](./media/configure-common/open-documents.png)

O documento predefinido é a página web que é exibida no URL raiz para um site. O primeiro ficheiro de correspondência da lista é usado. Para adicionar um novo documento predefinido, clique em **Novo documento**. Não se esqueça de clicar em **Guardar**.

Se a aplicação utilizar módulos que se encaminham com base em URL em vez de servir conteúdo estático, não há necessidade de documentos predefinidos.

## <a name="configure-path-mappings"></a>Configurar mapeamentos de caminhos

No [portal Azure,]procure e selecione **Serviços de Aplicações** e, em seguida, selecione a sua aplicação. No menu esquerdo da aplicação, selecione **Configuration**  >  **os mapeamentos do Caminho de** Configuração .

![Mapeamentos de caminhos](./media/configure-common/open-path.png)

A página **de mapeamentos Path** mostra-lhe coisas diferentes com base no tipo de SO.

### <a name="windows-apps-uncontainerized"></a>Aplicativos Windows (não detidos)

Para aplicações windows, você pode personalizar os mapeamentos do manipulador IIS e aplicações virtuais e diretórios.

Os mapeamentos do manipulador permitem adicionar processadores de script personalizados para lidar com pedidos de extensões de ficheiros específicas. Para adicionar um manipulador personalizado, clique em **Novo manipulador**. Configure o manipulador da seguinte forma:

- **Extensão**. A extensão do ficheiro que pretende manusear, como *\* .php* ou *handler.fcgi*.
- **Processador script**. O caminho absoluto do processador de scripts para si. Os pedidos para ficheiros que correspondam à extensão do ficheiro são processados pelo processador script. Utilize o caminho `D:\home\site\wwwroot` para se referir ao diretório de raiz da sua aplicação.
- **Argumentos.** Argumentos de linha de comando opcionais para o processador de scripts.

Cada aplicação tem o caminho de raiz padrão `/` () mapeado para `D:\home\site\wwwroot` , onde o seu código é implementado por padrão. Se a raiz da sua aplicação estiver numa pasta diferente, ou se o seu repositório tiver mais do que uma aplicação, pode editar ou adicionar aplicações virtuais e diretórios aqui. Clique em **Nova aplicação virtual ou diretório**.

Para configurar aplicações virtuais e diretórios, especifique cada diretório virtual e o seu percurso físico correspondente em relação à raiz do site ( `D:\home` ). Opcionalmente, pode selecionar a caixa de **verificação 'Aplicação'** para marcar um diretório virtual como uma aplicação.

### <a name="containerized-apps"></a>Aplicativos contentorizados

Pode [adicionar armazenamento personalizado para a sua aplicação contentorizada.](configure-connect-to-azure-storage.md) As aplicações contentorizadas incluem todas as aplicações Linux e também os recipientes personalizados Windows e Linux em execução no Serviço de Aplicações. Clique em **Novo Suporte de Armazenamento Azure** e configuure o seu armazenamento personalizado da seguinte forma:

- **Nome**: O nome do visor.
- **Opções de configuração**: **Básico** ou **Avançado**.
- **Contas de armazenamento**: A conta de armazenamento com o recipiente que deseja.
- **Tipo de armazenamento**: **Azure Blobs** ou **Ficheiros Azure**.
  > [!NOTE]
  > As aplicações para contentores do Windows suportam apenas ficheiros Azure.
- **Recipiente de armazenamento**: Para configuração básica, o recipiente deseja.
- **Nome da partilha**: Para configuração avançada, o nome da partilha de ficheiros.
- **Tecla de acesso**: Para configuração avançada, a chave de acesso.
- **Caminho de montagem**: O caminho absoluto no seu recipiente para montar o armazenamento personalizado.

Para obter mais informações, consulte [o Access Azure Storage como uma partilha de rede a partir de um contentor no Serviço de Aplicações.](configure-connect-to-azure-storage.md)

## <a name="configure-language-stack-settings"></a>Configurar configurações de pilha de idioma

Para aplicações Linux, consulte:

- [ASP.NET Core](configure-language-dotnetcore.md)
- [Node.js](configure-language-nodejs.md)
- [PHP](configure-language-php.md)
- [Python](configure-language-python.md)
- [Java](configure-language-java.md)
- [Ruby](configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Configure recipientes personalizados

Consulte [configurar um recipiente Linux personalizado para o Serviço de Aplicações Azure](configure-custom-container.md)

## <a name="next-steps"></a>Passos seguintes

- [Configure um nome de domínio personalizado no Azure App Service]
- [Configurar ambientes de teste no Serviço de Aplicações do Azure]
- [Proteja um nome DNS personalizado com uma ligação TLS/SSL no Azure App Service](configure-ssl-bindings.md)
- [Ativar registos de diagnóstico](troubleshoot-diagnostic-logs.md)
- [Dimensione uma aplicação no Azure App Service]
- [Monitorização do básico no Azure App Service]
- [Alterar definições de applicationHost.config com aplicaçãoHost.xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[Sinaleiro de ASP.NET]: https://www.asp.net/signalr
[Portal do Azure]: https://portal.azure.com/
[Configure um nome de domínio personalizado no Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Configurar ambientes de teste no Serviço de Aplicações do Azure]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: ./web-sites-monitor.md
[Monitorização do básico no Azure App Service]: ./web-sites-monitor.md
[modo pipeline]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Dimensione uma aplicação no Azure App Service]: ./manage-scale-up.md
