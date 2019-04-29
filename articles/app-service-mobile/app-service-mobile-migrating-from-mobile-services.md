---
title: Migrar dos serviços móveis a uma aplicação móvel do serviço de aplicações
description: Saiba como migrar facilmente a sua aplicação de serviços móveis para uma aplicação de Mobile do serviço de aplicações
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 07507ea2-690f-4f79-8776-3375e2adeb9e
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: crdun
ms.openlocfilehash: dfc5e2923215b1669b0a3300653ad0cae7379655
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122477"
---
# <a name="article-top"></a>Migrar o seu serviço de móvel atual do Azure App Service do Azure
Com o [disponibilidade geral do serviço de aplicações do Azure], sites de serviços móveis do Azure podem ser migradas facilmente no local para tirar partido de todas as funcionalidades do serviço de aplicações do Azure.  Este documento explica o que esperar ao migrar seu site de serviços móveis do Azure App Service do Azure.

## <a name="what-does-migration-do"></a>O que fazer migração para o seu site
Migração do seu serviço móvel do Azure ativa o seu serviço móvel para um [Serviço de Aplicações do Azure] aplicação sem afetar o código.  O Hubs de notificação, SQL ligação de dados, definições de autenticação, tarefas agendadas e nome de domínio permanecem inalterados.  Os clientes móveis com o seu serviço móvel do Azure continuam a funcionar normalmente.  Migração reinicia o serviço depois de serem transferidos para o serviço de aplicações do Azure.

[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>Por que migrar seu site
Microsoft é recomendando que migrar o seu serviço móvel do Azure para tirar partido das funcionalidades do serviço de aplicações do Azure, incluindo:

* Novos recursos de anfitrião, incluindo [WebJobs] e [nomes de domínio personalizados].
* Monitorização e resolução de problemas com [Application Insights].
* Ferramentas de DevOps incorporada, incluindo [blocos de teste], reversão, em produção e teste.
* [Dimensionamento automático], balanceamento de carga, e [Monitorização de desempenho].

Para obter mais informações sobre os benefícios do serviço de aplicações do Azure, consulte o [Os serviços móveis vs. Serviço de aplicações] tópico.

## <a name="before-you-begin"></a>Antes de começar
Antes de iniciar qualquer trabalho principais no seu site, deve copiar em segurança os seus scripts de serviço móvel e a base de dados SQL.

## <a name="migrating-site"></a>Migrar os seus sites
O processo de migração migra todos os sites numa única região do Azure.

Para migrar seu site:

1. Inicie sessão para o [Portal clássico do Azure].
2. Selecione um serviço móvel na região que pretende migrar.
3. Clique nas **migre para o serviço de aplicações** botão.

   ![O botão de migrar][0]
4. Leia o migrar para a caixa de diálogo do serviço de aplicações.
5. Introduza o nome do seu serviço móvel na caixa fornecida.  Por exemplo, se seu nome de domínio contoso. Azure-mobile.net, em seguida, introduza *contoso* na caixa fornecida.
6. Clique no botão de escala.

Monitorize o estado da migração no monitor de atividade. O site está listado como *migrar* no Portal clássico do Azure.

  ![Monitor de atividade de migração][1]

Cada migração pode demorar entre 3 e 15 minutos por serviço móvel a ser migrados.  Seu site permanece disponível durante a migração.
Seu site é reiniciado no fim do processo de migração.  O site não está disponível durante o processo de reinício, o que pode durar a alguns segundos.

## <a name="finalizing-migration"></a>A finalizar a migração
Planeie o teste seu site a partir de um cliente móvel na conclusão do processo de migração.  Certifique-se de que pode efetuar todas as ações de cliente comuns sem alterações para o cliente móvel.  

### <a name="update-app-service-tier"></a>Selecione um escalão de preço de serviço de aplicações adequada
Tem mais flexibilidade no preço após a migração para o serviço de aplicações do Azure.

1. Inicie sessão no [Portal do Azure].
2. Selecione **todos os recursos** ou **dos serviços de aplicações** , em seguida, clique no nome do seu serviço móvel migrados.
3. É aberto o painel de definições por predefinição.
4. Clique em **plano do serviço de aplicações** no menu de definições.
5. Clique nas **escalão de preço** mosaico.
6. Clique no mosaico adequado aos seus requisitos, em seguida, clique em **selecione**.  Poderá ter de clique **ver todos os** para ver os escalões de preços disponíveis.

Como ponto de partida, recomendamos os seguintes escalões:

| Escalão de preço do serviço móvel | Escalão de preço do serviço de aplicações |
|:--- |:--- |
| Gratuito |F1 Gratuito |
| Básica |Básico B1 |
| Standard |S1 Standard |

Há uma considerável flexibilidade na escolha o direito de preços para a sua aplicação.  Consulte a [Preços do serviço de aplicações] para todos os detalhes sobre os preços do seu novo serviço de aplicações.

> [!TIP]
> O escalão Standard de serviço de aplicação contém o acesso a diversos recursos que pretende utilizar, incluindo [blocos de teste], cópias de segurança automáticas e o dimensionamento automático.  Confira os novos recursos, enquanto estão lá!
>
>

### <a name="review-migration-scheduler-jobs"></a>Reveja as tarefas do Scheduler migrados
Tarefas do Scheduler não serão visíveis até aproximadamente 30 minutos após a migração.  Tarefas agendadas continuam a ser executado em segundo plano.
Para ver as tarefas agendadas, depois de serem visíveis novamente:

1. Inicie sessão no [Portal do Azure].
2. Selecione **Procurar >**, introduza **agenda** no *filtro* caixa, em seguida, selecione **coleções do Scheduler**.

Há um número limitado de scheduler de gratuito tarefas disponíveis após a migração.  Rever a sua utilização e o [Planos do Scheduler do Azure].

### <a name="configure-cors"></a>Configurar a CORS, se necessário
Recursos de várias origens de partilha é uma técnica para permitir que um Web site aceder a uma API Web num domínio diferente.  Se estiver a utilizar os serviços móveis do Azure com um Web site associado, em seguida, terá de configurar a CORS como parte da migração.  Se exclusivamente a partir de dispositivos móveis que está a aceder a serviços móveis do Azure, em seguida, CORS não precisa ser configurado, exceto em casos raros.

As definições de CORS migradas estão disponíveis como o **MS_CrossDomainWhitelist** definição de aplicação.  Para migrar o seu site para o recurso CORS do App Service:

1. Inicie sessão no [Portal do Azure].
2. Selecione **todos os recursos** ou **dos serviços de aplicações** , em seguida, clique no nome do seu serviço móvel migrados.
3. É aberto o painel de definições por predefinição.
4. Clique em **CORS** no menu de API.
5. Introduza quaisquer origens permitidas na caixa fornecida, premindo Enter após cada um deles.
6. Assim que a sua lista de origens permitidas estiver correta, clique no botão guardar.

> [!TIP]
> Uma das vantagens da utilização de um serviço de aplicações do Azure é que pode executar o seu web site e o serviço móvel no mesmo site.  Para obter mais informações, consulte a [passos seguintes](#next-steps) secção.
>
>

### <a name="download-publish-profile"></a>Transferir um novo perfil de publicação
O perfil de publicação do seu site é alterado ao migrar para o serviço de aplicações do Azure.  Se pretende publicar seu site a partir do Visual Studio, terá um novo perfil de publicação.  Para transferir o novo perfil de publicação:

1. Inicie sessão no [Portal do Azure].
2. Selecione **todos os recursos** ou **dos serviços de aplicações** , em seguida, clique no nome do seu serviço móvel migrados.
3. Clique em **obter perfil de publicação**.

O ficheiro PublishSettings é transferido para o seu computador.  Ele é normalmente chamado *sitename*. PublishSettings.  Importe as definições de publicação para o seu projeto existente:

1. Abra o Visual Studio e o seu projeto de serviço móveis do Azure.
2. Com o botão direito do rato no **Explorador de soluções** e selecione **publicar...**
3. Clique em **Importar**
4. Clique em **procurar** e selecione publicar seu transferido o ficheiro de definições.  Clique em **OK**
5. Clique em **validar ligação** para garantir que o trabalho de definições de publicação.
6. Clique em **publicar** para publicar o seu site.

## <a name="working-with-your-site"></a>Trabalhar com sua pós-migração de site
Começar a trabalhar com o seu novo serviço de aplicações do [portal do Azure] após a migração.  Seguem-se algumas notas sobre operações específicas que utilizou para executar o [Portal clássico do Azure], juntamente com seu equivalente de serviço de aplicações.

### <a name="publishing-your-site"></a>Transferir e publicar o seu site migrado
O site está disponível através de git ou ftp e pode ser replicado com vários mecanismos diferentes, incluindo o WebDeploy, TFS, Mercurial, GitHub e FTP.  As credenciais de implementação são migradas com o resto do seu site.  Se não definiu as suas credenciais de implementação ou não se lembrá-los, pode repô-los:

1. Inicie sessão no [Portal do Azure].
2. Selecione **todos os recursos** ou **dos serviços de aplicações** , em seguida, clique no nome do seu serviço móvel migrados.
3. É aberto o painel de definições por predefinição.
4. Clique em **credenciais de implementação** na publicação menu.
5. Introduza as credenciais de implementação novo nas caixas fornecidas e, em seguida, clique no botão guardar.

Pode utilizar estas credenciais para clonar o site com o git ou configurar implementações automáticas a partir do GitHub, TFS ou Mercurial.  Para obter mais informações, consulte a [documentação de implementação do serviço de aplicações do Azure].

### <a name="appsettings"></a>Definições da aplicação
A maioria das definições para um serviço móvel migrado estão disponíveis através de definições da aplicação.  Pode obter uma lista de definições da aplicação a partir da [portal do Azure].
Para ver ou alterar as definições da aplicação:

1. Inicie sessão no [Portal do Azure].
2. Selecione **todos os recursos** ou **dos serviços de aplicações** , em seguida, clique no nome do seu serviço móvel migrados.
3. É aberto o painel de definições por predefinição.
4. Clique em **as definições da aplicação** no menu geral.
5. Desloque-se para a secção de definições da aplicação e encontre a definição de aplicação.
6. Clique no valor da definição de aplicação para editar o valor.  Clique em **guardar** para guardar o valor.

Pode atualizar as definições de aplicações ao mesmo tempo.

> [!TIP]
> Existem duas configurações de aplicativo com o mesmo valor.  Por exemplo, poderá ver *ApplicationKey* e *MS\_ApplicationKey*.  Atualize as definições da aplicação ao mesmo tempo.
>
>

### <a name="authentication"></a>Autenticação
Todas as definições de autenticação estão disponíveis como definições da aplicação no seu site migrado.  Para atualizar as definições de autenticação, tem de alterar as definições de aplicações adequada.  A tabela seguinte mostra as definições de aplicações adequada para o seu fornecedor de autenticação:

| Fornecedor | ID de Cliente | Segredo do Cliente | Outras definições |
|:--- |:--- |:--- |:--- |
| Conta Microsoft |**MS\_MicrosoftClientID** |**MS\_MicrosoftClientSecret** |**MS\_MicrosoftPackageSID** |
| Facebook |**MS\_FacebookAppID** |**MS\_FacebookAppSecret** | |
| Twitter |**MS\_TwitterConsumerKey** |**MS\_TwitterConsumerSecret** | |
| Google |**MS\_GoogleClientID** |**MS\_GoogleClientSecret** | |
| Azure AD |**MS\_AadClientID** | |**MS\_AadTenants** |

Nota: **MS\_AadTenants** é armazenado como uma lista separada por vírgulas de domínios do inquilino (os campos "Inquilinos permitidos" no portal dos serviços móveis).

> [!WARNING]
> **Não utilize os mecanismos de autenticação no menu de definições**
>
> Serviço de aplicações do Azure fornece um "sem código" autenticação e autorização sistema separado no *autenticação / autorização* menu definições e (preterido) *autenticação Mobile* opção no menu de definições.  Estas opções são incompatíveis com um serviço de Mobile migrado do Azure.  Pode [atualizar o seu site](app-service-mobile-net-upgrading-from-mobile-services.md) para aproveitar a autenticação do serviço de aplicações do Azure.
>
>

### <a name="easytables"></a>Dados
O *dados* separador nos serviços móveis foi substituído por *tabelas simples* no portal do Azure.  Para aceder a tabelas simples:

1. Inicie sessão no [Portal do Azure].
2. Selecione **todos os recursos** ou **dos serviços de aplicações** , em seguida, clique no nome do seu serviço móvel migrados.
3. É aberto o painel de definições por predefinição.
4. Clique em **tabelas simples** no menu de móvel.

Pode adicionar uma tabela ao clicar o **adicionar** botão ou acessar suas tabelas existentes ao clicar num nome de tabela.  Existem várias operações que pode fazer a partir deste painel, incluindo:

* Alterar as permissões de tabela
* Editar os scripts operacionais
* Gerir o esquema da tabela
* A eliminar a tabela
* Limpar o conteúdo da tabela
* A eliminar linhas específicas da tabela

### <a name="easyapis"></a>API
O *API* separador nos serviços móveis foi substituído por *APIs simples* no portal do Azure.  Para aceder a APIs simples:

1. Inicie sessão no [Portal do Azure].
2. Selecione **todos os recursos** ou **dos serviços de aplicações** , em seguida, clique no nome do seu serviço móvel migrados.
3. É aberto o painel de definições por predefinição.
4. Clique em **APIs simples** no menu de móvel.

As suas APIs migrados já estão listados no painel.  Também pode adicionar uma API a partir deste painel.  Para gerir uma API específica, clique a API.
No novo painel, pode ajustar as permissões e editar os scripts para a API.

### <a name="on-demand-jobs"></a>Tarefas do Scheduler
Todas as tarefas do scheduler estão disponíveis através da secção de coleções de tarefas do Scheduler.  Para aceder aos seus trabalhos do programador:

1. Inicie sessão no [Portal do Azure].
2. Selecione **Procurar >**, introduza **agenda** no *filtro* caixa, em seguida, selecione **coleções do Scheduler**.
3. Selecione a coleção de tarefas para o seu site.  Ele é denominado *sitename*-tarefas.
4. Clique em **definições**.
5. Clique em **tarefas do Scheduler** em GERIR.

Tarefas agendadas são listadas com frequência especificada antes da migração.  Tarefas a pedido estão desativadas.  Para executar uma tarefa a pedido:

1. Selecione a tarefa que pretende executar.
2. Se necessário, clique em **ativar** ao ativar a tarefa.
3. Clique em **configurações**, em seguida, **agenda**.
4. Selecione uma recorrência de **uma vez**, em seguida, clique em **guardar**

As tarefas a pedido estão localizadas em `App_Data/config/scripts/scheduler post-migration`.  Recomendamos que converta todas as tarefas de sob demanda para [WebJobs] ou [funções].  Escrever novas tarefas do scheduler como [WebJobs] ou [funções].

### <a name="notification-hubs"></a>Hubs de notificação
Os serviços móveis utiliza os Hubs de notificação para notificações push.  As seguintes definições de aplicação são utilizadas para ligar o Hub de notificação ao seu serviço móvel após a migração:

| Definição de aplicação | Descrição |
|:--- |:--- |
| **MS\_PushEntityNamespace** |O espaço de nomes do Hub de notificação |
| **MS\_NotificationHubName** |O nome do Hub de notificação |
| **MS\_NotificationHubConnectionString** |A cadeia de ligação do Hub de notificação |
| **MS\_NamespaceName** |Um alias para MS_PushEntityNamespace |

O Hub de notificação é gerido através da [portal do Azure].  Tenha em atenção o nome do Hub de notificação (pode encontrá-lo com as definições de aplicação):

1. Inicie sessão no [Portal do Azure].
2. Selecione **navegue**>, em seguida, selecione **os Hubs de notificação**
3. Clique no nome do Notification Hub associado ao serviço móvel.

> [!NOTE]
> Se o seu HUb de notificação é um tipo de "Misto", não é visível.  "Mistos" tipo de notificação dos hubs de utilizam os Hubs de notificação e as funcionalidades legadas do Service Bus.  [Converter os espaços de nomes mistos] antes de continuar.  Assim que a conversão estiver concluída, o hub de notificação é apresentada no [portal do Azure].
>
>

Para obter mais informações, reveja os [Hubs de Notificação] documentação.

> [!TIP]
> Funcionalidades de gestão de Hubs de notificação no [portal do Azure] são ainda em pré-visualização.  O [Portal clássico do Azure] permanece disponível para o gerenciamento de todos os Hubs de notificação.
>
>

### <a name="legacy-push"></a>Definições Push legado
Se tiver configurado o Push no seu serviço móvel antes da introdução nos Hubs de notificação, que está a utilizar *push herdado*.  Se estiver a utilizar o Push e não vir um Hub de notificação listados na sua configuração, em seguida, é provável que está a utilizar *push herdado*.  Esta funcionalidade é migrada com todos os outros recursos.  No entanto, recomendamos que Atualize para os Hubs de notificação, logo após a migração estiver concluída.

Até lá, a todas as definições de push herdado (com a diferença notável do certificado APNS) estão disponíveis nas definições da aplicação.  Atualize o certificado APNS ao substituir o ficheiro adequado no sistema de ficheiros.

### <a name="app-settings"></a>Outras definições de aplicação
As seguintes definições de aplicação adicionais são migrados a partir do seu serviço móvel e disponíveis na *configurações* > *definições de aplicação*:

| Definição de aplicação | Descrição |
|:--- |:--- |
| **MS\_MobileServiceName** |O nome da sua aplicação |
| **MS\_MobileServiceDomainSuffix** |O prefixo de domínio. ou seja azure-mobile.net |
| **MS\_ApplicationKey** |A chave de aplicação |
| **MS\_MasterKey** |A chave mestra de aplicação |

A chave da aplicação e a chave mestra são idênticas às chaves de aplicação do seu serviço móvel original.  Em particular, a chave da aplicação é enviada pelos clientes móveis, para validar a sua utilização da API móvel.

### <a name="cliequivalents"></a>Equivalentes de linha de comandos
Já pode utilizar o *azure móvel* comandos para gerir o seu site de serviços móveis do Azure.  Em vez disso, muitas funções foram substituídas com o *site do azure* comando.  Utilize a seguinte tabela para localizar os equivalentes de comandos comuns:

| *O Azure Mobile* comando | Equivalente *Site do Azure* comando |
|:--- |:--- |
| localizações móveis |lista de localização do site |
| lista móvel |lista de sites |
| Mostrar móvel *nome* |site show *nome* |
| reinício móvel *nome* |reinício do site *nome* |
| reimplementação móvel *nome* |reimplementação da implementação de site *commitId* *nome* |
| conjunto de chaves móvel *name* *tipo* *valor* |Eliminar site appsetting *chave* *nome* <br/> Adicionar site appsetting *chave*=*valor* *nome* |
| lista de configuração móvel *nome* |lista de sites de appsetting *nome* |
| obter configuração móvel *name* *chave* |site appsetting show *chave* *nome* |
| conjunto de configuração móvel *name* *chave* |Eliminar site appsetting *chave* *nome* <br/> Adicionar site appsetting *chave*=*valor* *nome* |
| lista de domínio móveis *nome* |lista de domínio do site *nome* |
| Adicionar domínio móvel *name* *domínio* |Adicionar domínio do site *domínio* *nome* |
| Eliminar domínio móveis *nome* |eliminação de domínio do site *domínio* *nome* |
| Mostrar escala móvel *nome* |site show *nome* |
| mudança de escala móvel *nome* |modo de dimensionamento do site *modo* *nome* <br /> instâncias de dimensionamento do site *instâncias* *nome* |
| lista de appsetting móveis *nome* |lista de sites de appsetting *nome* |
| Adicionar móvel appsetting *name* *chave* *valor* |Adicionar site appsetting *chave*=*valor* *nome* |
| Eliminar móvel appsetting *name* *chave* |Eliminar site appsetting *chave* *nome* |
| appsetting móveis show *name* *chave* |Eliminar site appsetting *chave* *nome* |

Atualizar autenticação ou as definições de notificação push ao atualizar a definição de aplicação adequada.
Editar ficheiros e publicar o seu site através de ftp ou o git.

### <a name="diagnostics"></a>Registo e diagnóstico
Registo de diagnósticos normalmente está desativado no serviço de aplicações do Azure.  Para ativar o registo de diagnóstico:

1. Inicie sessão no [Portal do Azure].
2. Selecione **todos os recursos** ou **dos serviços de aplicações** , em seguida, clique no nome do seu serviço móvel migrados.
3. É aberto o painel de definições por predefinição.
4. Selecione **registos de diagnóstico** no menu de recursos.
5. Clique em **ON** para os seguintes registos: **Registo de aplicação (sistema de ficheiros)**, **mensagens de erro detalhadas**, e **rastreio de pedidos falhados**
6. Clique em **sistema de ficheiros** para o registo do servidor Web
7. Clicar em **Guardar**

Para ver os registos:

1. Inicie sessão no [Portal do Azure].
2. Selecione **todos os recursos** ou **dos serviços de aplicações** , em seguida, clique no nome do seu serviço móvel migrados.
3. Clique nas **ferramentas** botão
4. Selecione **Log Stream** no OBSERVE menu.

Os registos são apresentados na janela à medida que são gerados.  Também pode transferir os registos para análise posterior com as suas credenciais de implementação. Para obter mais informações, consulte a [Logging] documentação.

## <a name="known-issues"></a>Problemas conhecidos
### <a name="deleting-a-migrated-mobile-app-clone-causes-a-site-outage"></a>A eliminar um Clone de migrados de aplicações móveis faz com que uma interrupção do site
Se clonar seu serviço móvel migrado com o Azure PowerShell, em seguida, elimine o clone, é removida a entrada DNS para o seu serviço de produção.  Seu site é já não estar acessível a partir da Internet.  

Resolução: Se deseja clonar seu site, faça-o através do portal.

### <a name="changing-webconfig-does-not-work"></a>A alteração de Web. config não funciona
Se tiver um site ASP.NET, é alterado para o `Web.config` não são aplicadas ao ficheiro.  O serviço de aplicações do Azure baseia-se um adequado `Web.config` ficheiro durante o arranque para suportar o tempo de execução de serviços móveis.  É possível substituir determinadas definições (por exemplo, os cabeçalhos personalizados), utilizando um ficheiro de transformação XML.  Crie um ficheiro na chamada `applicationHost.xdt` -este ficheiro deve resultar no `D:\home\site` no serviço do Azure.  Carregar o `applicationHost.xdt` de ficheiros através de um script de implementação personalizado ou utilizar o Kudu diretamente.  O código a seguir mostra um documento de exemplo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <system.webServer>
    <httpProtocol>
      <customHeaders>
        <add name="X-Frame-Options" value="DENY" xdt:Transform="Replace" />
        <remove name="X-Powered-By" xdt:Transform="Insert" />
      </customHeaders>
    </httpProtocol>
    <security>
      <requestFiltering removeServerHeader="true" xdt:Transform="SetAttributes(removeServerHeader)" />
    </security>
  </system.webServer>
</configuration>
```

Para obter mais informações, consulte a [Exemplos de transformação XDT] documentação no GitHub.

### <a name="migrated-mobile-services-cannot-be-added-to-traffic-manager"></a>Os serviços móveis migrados não pode ser adicionados ao Gestor de tráfego
Quando cria um perfil do Gestor de tráfego, não é possível diretamente de escolher um serviço móvel migrado para o perfil.  Utilize um "ponto final externo."  O ponto final externo apenas pode ser adicionado através do PowerShell.  Para obter mais informações, consulte a [tutorial do Gestor de tráfego](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/).

## <a name="next-steps"></a>Passos Seguintes
Agora que a aplicação é migrada para o serviço de aplicações, há ainda mais funcionalidades que pode utilizar:

* Implantação [blocos de teste] permitem-lhe testar alterações ao seu site e executar A testes a / B.
* [WebJobs] fornecem uma substituição para tarefas agendada a pedido.
* Pode [implemente continuamente] seu site ao ligar o seu site do GitHub, TFS ou Mercurial.
* Pode usar [Application Insights] para monitorizar o seu site.
* Fornecer um Web site e uma API de Mobile a partir do mesmo código.

### <a name="upgrading-your-site"></a>Atualizar o seu site de serviços móveis para o SDK de aplicações móveis do Azure
* Para projetos de servidor baseado em node. js, o novo [SDK de node. js de aplicações móveis] oferece vários recursos novos. Por exemplo, pode agora fazer desenvolvimento local e a depuração, utilize qualquer versão do node. js acima 0,10 e personalizar com qualquer middleware Express. js.
* Para. Projetos de servidor baseado em rede, a nova [pacotes de NuGet do SDK de aplicações de Mobile](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) tenha mais flexibilidade no dependências NuGet.  Esses pacotes suportam a autenticação de serviço de aplicações de novas e compõem com qualquer projeto do ASP.NET. Para saber mais sobre a atualização, veja [atualizar o seu serviço de móvel atual do .NET para o serviço de aplicações](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[Preços do serviço de aplicações]: https://azure.microsoft.com/pricing/details/app-service/
[Application Insights]: ../azure-monitor/app/app-insights-overview.md
[Dimensionamento automático]: ../app-service/web-sites-scale.md
[Serviço de Aplicações do Azure]: ../app-service/overview.md
[Portal Clássico do Azure]: https://manage.windowsazure.com
[Portal do Azure]: https://portal.azure.com
[Azure Region]: https://azure.microsoft.com/regions/
[Planos do Scheduler do Azure]: ../scheduler/scheduler-plans-billing.md
[implemente continuamente]: ../app-service/deploy-continuous-deployment.md
[Converter os espaços de nomes mistos]: https://azure.microsoft.com/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: https://curl.haxx.se/
[Nomes de domínio personalizados]: ../app-service/app-service-web-tutorial-custom-domain.md
[Fiddler]: https://www.telerik.com/fiddler
[disponibilidade geral do serviço de aplicações do Azure]: https://azure.microsoft.com/blog/announcing-general-availability-of-app-service-mobile-apps/
[Hybrid Connections]: ../app-service/app-service-hybrid-connections.md
[Logging]: ../app-service/troubleshoot-diagnostic-logs.md
[SDK de node. js de aplicações móveis]: https://github.com/azure/azure-mobile-apps-node
[Os serviços móveis vs. Serviço de aplicações]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Hubs de Notificação]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Monitorização de desempenho]: ../app-service/web-sites-monitor.md
[Postman]: https://www.getpostman.com/
[blocos de teste]: ../app-service/deploy-staging-slots.md
[VNet]: ../app-service/web-sites-integrate-with-vnet.md
[Exemplos de transformação XDT]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples
[Funções]: ../azure-functions/functions-overview.md
