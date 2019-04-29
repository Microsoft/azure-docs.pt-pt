---
title: Atualização dos serviços móveis para o serviço de aplicações do Azure
description: Saiba como atualizar facilmente a sua aplicação de serviços móveis para uma aplicação de Mobile do serviço de aplicações
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 9c0ac353-afb6-462b-ab94-d91b8247322f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: f5ffc795e6469971d1eaf335d6683f94d05f0807
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122443"
---
# <a name="upgrade-your-existing-net-azure-mobile-service-to-app-service"></a>Atualizar o seu serviço móvel do Azure de .NET existente para o serviço de aplicações
Serviço de aplicações móveis é uma nova forma de compilação de aplicações móveis com o Microsoft Azure. Para obter mais informações, consulte [O que são Aplicações Móveis?].

Este tópico descreve como atualizar uma aplicação de back-end de .NET existente de serviços móveis do Azure para um novo Mobile Apps do App Service. Enquanto executa esta atualização, a aplicação de serviços móveis existente pode continuar a operar.   Se precisar de atualizar uma aplicação de back-end de node. js, consulte [atualizar os seus serviços móveis node. js](app-service-mobile-node-backend-upgrading-from-mobile-services.md).

Quando um back-end móvel é atualizado para o serviço de aplicações do Azure, tem acesso a todas as funcionalidades do serviço de aplicações e são faturados de acordo com a [preços do serviço de aplicações], não os serviços móveis preços.

## <a name="migrate-vs-upgrade"></a>Migrar vs. a atualização
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> É recomendável que [efetuar uma migração](app-service-mobile-migrating-from-mobile-services.md) antes de passar por uma atualização. Dessa forma, pode colocar as duas versões do seu aplicativo no mesmo plano de serviço de aplicações e incorrer sem custos adicionais.
>
>

### <a name="improvements-in-mobile-apps-net-server-sdk"></a>Aprimoramentos no servidor de Mobile Apps .NET SDK
Atualizar para o novo [SDK de aplicações móveis](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) fornece as seguintes vantagens:

* Mais flexibilidade em dependências NuGet. O ambiente de hospedagem já não fornece suas própria versões de pacotes do NuGet, pelo que pode utilizar as versões compatíveis alternativas. No entanto, se existirem novas bugfixes críticas ou de atualizações de segurança para o SDK de servidor Mobile ou dependências, tem de atualizar o seu serviço manualmente.
* Mais flexibilidade no SDK móvel. Pode controlar explicitamente quais recursos e as rotas são configuradas, como autenticação, as APIs de tabela e o ponto de final do registo de push. Para obter mais informações, consulte [como utilizar o SDK do servidor .NET para aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).
* Suporte para outros tipos de projeto do ASP.NET e as rotas. Agora, pode alojar controladores MVC e a Web API no mesmo projeto do seu projeto de back-end móvel.
* Suporte para novos recursos de autenticação de serviço de aplicações, que permitem-lhe utilizar uma configuração de autenticação comum nas suas aplicações web e móveis.

## <a name="overview"></a>Descrição geral da atualização básica
Em muitos casos, a atualização irá ser tão simples como mudar para o novo servidor de aplicações móveis SDK e republicar o seu código numa nova instância de aplicação móvel. Há contudo alguns cenários que irão exigir configuração adicional, como cenários de autenticação avançadas e trabalhar com tarefas agendadas. Cada um destes é descrita nas secções posteriores.

> [!TIP]
> Recomenda-se que leia e compreenda o resto deste tópico completamente antes de iniciar uma atualização. Tome nota de quaisquer funcionalidades é utilizado que estão indicado abaixo.
>
>

O cliente de serviços móveis os SDKs estão **não** compatível com o novo servidor de aplicações móveis do SDK. Para fornecer continuidade do serviço para a sua aplicação, não deve publicar as alterações a um site publicados clientes atualmente a funcionar. Em vez disso, deve criar uma nova aplicação móvel que serve como um duplicado. Pode colocar o aplicativo no mesmo plano de serviço de aplicações para evitar incorrer em custos financeiros adicionais.

Em seguida, terá duas versões da aplicação: aplicações que permanece o mesmo e serve publicadas no terreno e a outra que pode, em seguida, atualizar e de destino com uma nova versão de cliente. Pode mover e testar o seu código ao seu ritmo, mas deve certificar-se de que quaisquer correções de erros que fizer são aplicadas para ambos. Assim que se sentir que um número pretendido de aplicações no terreno tem atualizado para a versão mais recente do cliente, pode eliminar a aplicação migrada original se desejar.

A estrutura completa para o processo de atualização é o seguinte:

1. Criar uma nova aplicação móvel
2. Atualizar o projeto para utilizar os novos SDKs de servidor
3. Lançar uma nova versão da sua aplicação de cliente
4. (Opcional) Eliminar a instância original do migrados

## <a name="mobile-app-version"></a>Criar uma segunda instância de aplicação
A primeira etapa na atualização é criar o recurso de aplicação móvel que irá alojar a nova versão do seu aplicativo. Se já tiver migrado serviços móveis existentes, desejará criar esta versão no mesmo plano de alojamento. Abra o [portal do Azure] e navegue para a sua aplicação migrado. Aponte o plano de serviço de aplicações está em execução no.

Em seguida, crie a segunda instância de aplicação ao seguir a [instruções de criação de back-end de .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app). Quando lhe for pedido para selecionar o plano do serviço de aplicações ou "plano de alojamento" Escolha o plano da sua aplicação migrada.

Provavelmente irá querer utilizar o mesmo banco de dados e o Hub de notificação, tal como fez nos serviços móveis. Pode copiar esses valores, abrindo [portal do Azure] e, em seguida, navegar para o aplicativo original, clique em **definições** > **definições da aplicação**. Sob **cadeias de ligação**, cópia `MS_NotificationHubConnectionString` e `MS_TableConnectionString`. Navegue para o novo site de atualização e colá-los, substituindo quaisquer valores existentes. Repita este processo para quaisquer outras definições de aplicação necessidades da sua aplicação.

Faça uma cópia do projeto ASP.NET para a sua aplicação e publicá-lo no seu novo site. Utilizar uma cópia da sua aplicação de cliente atualizada com o novo URL, valide que tudo funciona conforme esperado.

## <a name="updating-the-server-project"></a>A atualizar o projeto de servidor
Aplicações móveis fornece uma nova [SDK do servidor de aplicações móveis] que fornece grande parte a mesma funcionalidade que o tempo de execução de serviços móveis. Em primeiro lugar, deve remover todas as referências para os pacotes de serviços móveis. No Gestor de pacotes NuGet, procure `WindowsAzure.MobileServices.Backend`. A maioria das aplicações irão ver diversos pacotes aqui, incluindo `WindowsAzure.MobileServices.Backend.Tables` e `WindowsAzure.MobileServices.Backend.Entity`. Nesse caso, começar com o pacote mais baixo na árvore de dependência, como `Entity`e removê-lo. Quando lhe for pedido, não remova todos os pacotes dependentes. Repita este processo até ter removido `WindowsAzure.MobileServices.Backend` em si.

Neste momento terá um projeto que já não faz referência SDK dos serviços móveis.

Em seguida adicionará referências o SDK de aplicações móveis. Para esta atualização, a maioria dos desenvolvedores irá querer transferir e instalar o `Microsoft.Azure.Mobile.Server.Quickstart` empacotar, como isto irá extrair em todo o conjunto necessário.

Haverá alguns erros de compilador resultantes de diferenças entre os SDKs, mas estas são fáceis de endereço e o restante desta seção são abordadas.

### <a name="base-configuration"></a>Configuração base
Em seguida, no WebApiConfig.cs, pode substituir:

```csharp
// Use this class to set configuration options for your mobile service
ConfigOptions options = new ConfigOptions();

// Use this class to set WebAPI configuration options
HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));
```

com

```csharp
HttpConfiguration config = new HttpConfiguration();
new MobileAppConfiguration()
    .UseDefaultConfiguration()
.ApplyTo(config);

```

> [!NOTE]
> Se quiser saber mais sobre o novo servidor de .NET SDK e como adicionar ou remover recursos da sua aplicação, volte a ver o [como utilizar o SDK do servidor .NET] tópico.
>
>

Se torna a sua aplicação utilizar os recursos de autenticação, também terá de registar um middleware da OWIN. Neste caso, deve mover o código de configuração acima numa nova classe de Startup da OWIN.

1. Adicionar o pacote NuGet `Microsoft.Owin.Host.SystemWeb` se ele já não está incluído no seu projeto.
2. No Visual Studio, clique com botão direito no seu projeto e selecione **Add** -> **Novo Item**. Selecione **Web** -> **geral** -> **classe de Startup da OWIN**.
3. Mover o código acima para MobileAppConfiguration partir `WebApiConfig.Register()` para o `Configuration()` método da sua nova classe de inicialização.

Certifique-se de que o `Configuration()` método termina com:

```csharp
app.UseWebApi(config)
app.UseAppServiceAuthentication(config);
```

Existem alterações adicionais relacionadas à autenticação que são abordadas na secção autenticação completo abaixo.

### <a name="working-with-data"></a>Trabalhar com Dados
Nos serviços móveis, o nome de aplicação móvel serviu-se como o nome do esquema padrão na configuração da Entity Framework.

Para se certificar de que tem o mesmo esquema a ser referenciado como antes, utilize o seguinte para definir o esquema no DbContext para a sua aplicação:

```csharp
string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");
```

Certifique-se de que tem MS_MobileServiceName definir se o fizer acima. Também pode fornecer outro nome de esquema se seu aplicativo personalizado isso anteriormente.

### <a name="system-properties"></a>Propriedades do Sistema
#### <a name="naming"></a>Atribuição de nomes
No servidor de serviços móveis do Azure SDK, as propriedades do sistema sempre contenham um caráter de sublinhado duplo (`__`) prefixo para as propriedades:

* __createdAt
* __updatedAt
* __deleted
* __version

Os SDKs do cliente de serviços móveis ter uma lógica especial para analisar as propriedades do sistema neste formato.

Mobile Apps do Azure, as propriedades do sistema já não tem um formato especial e tem os seguintes nomes:

* createdAt
* updatedAt
* eliminado
* versão

Os SDKs do cliente de Mobile Apps usar os novos nomes de propriedades do sistema, pelo que não são necessárias para o código de cliente alterações. No entanto, se estiver a efetuar chamadas REST para o seu serviço diretamente, em seguida, deve alterar as suas consultas em conformidade.

#### <a name="local-store"></a>Arquivo local
As alterações aos nomes das propriedades do sistema significam que uma base de dados local de sincronização offline para os serviços móveis não é compatível com as aplicações móveis. Se possível, deve evitar a atualização de cliente foram enviadas para o servidor de aplicações a partir dos serviços móveis para as aplicações móveis até depois das alterações pendentes. Em seguida, a aplicação atualizada deve utilizar um novo nome de ficheiro de base de dados.

Se uma aplicação de cliente é atualizada a partir dos serviços móveis para as aplicações móveis enquanto estiverem offline alterações pendentes na fila de operação, em seguida, a base de dados do sistema tem de ser atualizado para usar os novos nomes de coluna. No iOS, isso pode ser obtido com migrações leves para alterar os nomes das colunas. No Android e o cliente gerenciado do .NET, deve escrever personalizado SQL para mudar o nome de colunas para as suas tabelas de objeto de dados.

No iOS, deve alterar o esquema de dados principais para as entidades de dados de acordo com o seguinte. Tenha em atenção que as propriedades `createdAt`, `updatedAt` e `version` deixará de ter um `ms_` prefixo:

| Atributo | Type | Nota |
| --- | --- | --- |
| ID |Cadeia de caracteres, marcada como necessária |chave primária no arquivo remoto |
| createdAt |Date |(opcional) é mapeado para a propriedade de sistema createdAt |
| updatedAt |Date |(opcional) é mapeado para a propriedade de sistema updatedAt |
| versão |String |(opcional) usado para detectar conflitos, mapas para a versão |

#### <a name="querying-system-properties"></a>Consultar as propriedades do sistema
Em Mobile Services do Azure, as propriedades do sistema não são enviadas por predefinição, mas apenas quando eles são solicitados usando-se a cadeia de consulta `__systemProperties`. Por outro lado, no sistema de aplicações móveis do Azure propriedades são **sempre selecionado** , uma vez que eles fazem parte do modelo de objeto do SDK de servidor.

Esta alteração afeta principalmente implementações personalizadas dos gestores de domínio, como extensões de `MappedEntityDomainManager`. Nos serviços móveis, se um cliente solicita nunca quaisquer propriedades do sistema, é possível utilizar um `MappedEntityDomainManager` que não é mapeado, na verdade, todas as propriedades. No entanto, em aplicações móveis do Azure, estas propriedades não mapeadas causará um erro em consultas GET.

A maneira mais fácil para resolver o problema é modificar os DTOs para que eles herdam `ITableData` em vez de `EntityData`. Em seguida, adicione o `[NotMapped]` atributo para os campos que devem ser omitidos.

Por exemplo, define o seguinte `TodoItem` sem propriedades de sistema:

```csharp
using System.ComponentModel.DataAnnotations.Schema;

public class TodoItem : ITableData
{
    public string Text { get; set; }

    public bool Complete { get; set; }

    public string Id { get; set; }

    [NotMapped]
    public DateTimeOffset? CreatedAt { get; set; }

    [NotMapped]
    public DateTimeOffset? UpdatedAt { get; set; }

    [NotMapped]
    public bool Deleted { get; set; }

    [NotMapped]
    public byte[] Version { get; set; }
}
```

Nota: Se obtiver erros no `NotMapped`, adicione uma referência ao assembly `System.ComponentModel.DataAnnotations`.

### <a name="cors"></a>CORS
Os serviços móveis incluído algum suporte para CORS, envolvendo a solução de CORS do ASP.NET. Esta camada de encapsulamento de aplicações foi removida para dar mais controle, o desenvolvedor de forma que possa aproveitar diretamente [suporte de CORS do ASP.NET](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

As principais áreas de preocupação se utilizando a CORS são que o `eTag` e `Location` cabeçalhos têm de ser permitidos por ordem para os SDKs do cliente para funcionar corretamente.

### <a name="push-notifications"></a>Notificações Push
Para push, o item principal que pode encontrar em falta do SDK do servidor é a classe de PushRegistrationHandler. Registos são tratados de forma um pouco diferente em aplicações móveis e tagless registos estão ativados por predefinição. Gerir etiquetas pode ser conseguida com APIs personalizadas. Consulte a [registar para etiquetas](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) instruções para obter mais informações.

### <a name="scheduled-jobs"></a>Tarefas Agendadas
Tarefas agendadas não são criadas nas aplicações móveis, para que os trabalhos existentes que tiver no seu back-end de .NET tem de ser atualizado separadamente. Uma opção é criar um agendadas [Webjob] no site de código de aplicação móvel. Também pode configurar um controlador que contém o código de tarefa e configurar o [Azure Scheduler] para atingir o ponto de extremidade na agenda esperado.

### <a name="miscellaneous-changes"></a>Alterações diversas
Todos os ApiControllers que serão consumidas por um cliente móvel agora tem de ter o `[MobileAppController]` atributo. Isto já não é incluído por predefinição, para que outros ApiControllers ir não afetado pela formatadores móveis.

O `ApiServices` objeto já não faz parte do SDK. Para aceder às definições da aplicação móvel, pode utilizar o seguinte:

```csharp
MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
```

Da mesma forma, o registo agora é conseguido com a escrita de rastreamento do ASP.NET padrão:

```csharp
ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
traceWriter.Info("Hello, World");  
```

## <a name="authentication"></a>Considerações de autenticação
Os componentes de autenticação dos serviços móveis agora foram movidos para a funcionalidade de autenticação/autorização do serviço da aplicação. Pode aprender sobre como ativar isso para o seu site com a leitura a [adicionar autenticação à sua aplicação móvel](app-service-mobile-ios-get-started-users.md) tópico.

Para alguns fornecedores, como o AAD, o Facebook e o Google, devem ser capazes de tirar partido do registo existente da sua aplicação de cópia. Basta navegar para o portal do fornecedor de identidade e adicionar um novo URL de redirecionamento para o registo. Em seguida, configure o serviço de autenticação/autorização da aplicação com o ID de cliente e o segredo.

### <a name="controller-action-authorization"></a>Autorização de ação de controlador
Quaisquer instâncias do `[AuthorizeLevel(AuthorizationLevel.User)]` atributo agora tem de ser alterado para utilizar o ASP.NET padrão `[Authorize]` atributo. Além disso, os controladores são agora anónimo por predefinição, como em outros aplicativos do ASP.NET.
Se estivesse usando uma das outras opções de AuthorizeLevel, como administrador ou um aplicativo, tenha em atenção que estes desapareceram. Em vez disso, pode configurar a AuthorizationFilters para segredos compartilhados ou configurar um Principal de serviço do AAD para ativar chamadas de serviço para serviço com segurança.

### <a name="getting-additional-user-information"></a>Ao obter as informações adicionais de utilizadores
Pode obter informações de utilizador adicionais, incluindo os tokens de acesso por meio do `GetAppServiceIdentityAsync()` método:

```csharp
FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();
```

Além disso, se seu aplicativo usa dependências no utilizador IDs, por exemplo, armazená-los numa base de dados, é importante observar que os IDs de utilizador entre os serviços móveis e aplicações do serviço de aplicações móveis são diferentes. No entanto, ainda pode obter o ID de utilizador de serviços móveis. Todas as subclasses de ProviderCredentials têm uma propriedade de ID de utilizador. Assim, continuar do exemplo antes:

```csharp
string mobileServicesUserId = creds.Provider + ":" + creds.UserId;
```

Se a sua aplicação assumir quaisquer dependências de IDs de usuário, é importante que aproveitar o registo do mesmo com um fornecedor de identidade se possível. IDs de usuário geralmente passam para o registo de aplicação que foi utilizado para que introduzir um novo registo, pode criar problemas com utilizadores aos respetivos dados correspondentes.

### <a name="custom-authentication"></a>Autenticação personalizada
Se a sua aplicação utilizar uma solução de autenticação personalizado, deverá certificar-se de que o site atualizado tem acesso ao sistema. Siga as instruções de novo para a autenticação personalizada no [Descrição geral do SDK de servidor .NET] para integrar a sua solução. Tenha em atenção que os componentes de autenticação personalizado ainda estão em pré-visualização.

## <a name="updating-clients"></a>Atualizar clientes
Depois de ter um back-end de aplicação móvel operacional, pode trabalhar numa versão nova da sua aplicação de cliente que consome-lo. Aplicações móveis também inclui uma nova versão dos SDKs do cliente e, assim como a atualização do servidor acima, terá de remover todas as referências para os SDKs de serviços móveis antes de instalar as versões de aplicações móveis.

Uma das principais alterações entre as versões é que os construtores já não necessitam de uma chave de aplicação. Agora simplesmente passa a URL da aplicação móvel. Por exemplo, nos clientes .NET, o `MobileServiceClient` construtor é agora:

```csharp
public static MobileServiceClient MobileService = new MobileServiceClient(
    "https://contoso.azurewebsites.net", // URL of the Mobile App
);
```

Pode ler sobre como instalar os novos SDKs e usando a nova estrutura por meio dos links a seguir:

* [versão 3.0.0 do iOS ou posterior](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (Windows/Xamarin) versão 2.0.0 ou posterior](app-service-mobile-dotnet-how-to-use-client-library.md)

Se a aplicação faz usar notificações push, anote as instruções de registro específicos para cada plataforma, como houve algumas alterações aqui também.

Quando tiver a nova versão de cliente pronta, faça um teste em relação a seu projeto de servidor atualizado. Depois de confirmar que funciona, pode lançar uma nova versão da sua aplicação para os clientes. Eventualmente, depois dos seus clientes tenham tido a oportunidade para receber estas atualizações, pode eliminar a versão de serviços móveis da sua aplicação. Neste momento, completamente tiver atualizado para uma aplicação móvel do serviço de aplicação com o servidor de aplicações móveis SDK mais recente.

<!-- URLs. -->

[Portal do Azure]: https://portal.azure.com/
[O que são Aplicações Móveis?]: app-service-mobile-value-prop.md
[SDK do servidor de aplicações móveis]: https://www.nuget.org/packages/microsoft.azure.mobile.server
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /azure/scheduler/
[Webjob]: https://github.com/Azure/azure-webjobs-sdk/wiki
[Como utilizar o SDK do servidor .NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Preços do serviço de aplicações]: https://azure.microsoft.com/pricing/details/app-service/
[Descrição geral do SDK de servidor .NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
