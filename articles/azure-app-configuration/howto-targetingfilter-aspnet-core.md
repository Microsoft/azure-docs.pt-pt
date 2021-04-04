---
title: Ativar o lançamento encenado de funcionalidades para públicos direcionados
titleSuffix: Azure App Configuration
description: Saiba como permitir o lançamento encenado de funcionalidades para públicos direcionados
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 11/20/2020
ms.openlocfilehash: c415eaeab2edd0a1b324bba4266266201cb50cbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96929689"
---
# <a name="enable-staged-rollout-of-features-for-targeted-audiences"></a>Ativar o lançamento encenado de funcionalidades para públicos direcionados

As bandeiras de funcionalidade permitem ativar ou desativar dinamicamente a funcionalidade na sua aplicação. Os filtros de recurso determinam o estado de uma bandeira de recurso cada vez que é avaliado. A `Microsoft.FeatureManagement` biblioteca inclui , que permite uma bandeira de recurso para uma lista `TargetingFilter` especificada de utilizadores e grupos, ou para uma percentagem especificada de utilizadores. `TargetingFilter` é "pegajoso". Isto significa que assim que um utilizador receber uma funcionalidade, continuará a ver essa funcionalidade em todos os pedidos futuros. Pode utilizar `TargetingFilter` para ativar uma funcionalidade de uma conta específica durante uma demonstração, para lançar progressivamente novas funcionalidades para utilizadores em diferentes grupos ou "anéis", e muito mais.

Neste artigo, você vai aprender a lançar uma nova funcionalidade numa aplicação web core ASP.NET para utilizadores e grupos especificados, usando `TargetingFilter` com Configuração de Aplicação Azure.

## <a name="create-a-web-application-with-feature-flags-and-authentication"></a>Crie uma aplicação web com bandeiras de recurso e autenticação

Para lançar funcionalidades com base em utilizadores e grupos, vai precisar de uma aplicação web que permita aos utilizadores iniciar sôsce.

1. Crie uma aplicação web que autua contra uma base de dados local utilizando o seguinte comando:

   ```dotnetcli
   dotnet new mvc --auth Individual -o TestFeatureFlags
   ```

1. Construa e corra e, em seguida, selecione o link **Registar** no canto superior direito para criar uma nova conta de utilizador. Utilize um endereço de e-mail de `test@contoso.com` . No ecrã **'Confirmação do Registo',** selecione **Clique aqui para confirmar a sua conta**.

1. Siga as instruções em [Quickstart: Adicione bandeiras de funcionalidades a uma aplicação Core ASP.NET](./quickstart-feature-flag-aspnet-core.md) para adicionar uma bandeira de recurso à sua nova aplicação web.

1. Alternar a bandeira de funcionalidade na Configuração da Aplicação. Validar que esta ação controla a visibilidade do item **Beta** na barra de navegação.

## <a name="update-the-web-application-code-to-use-targetingfilter"></a>Atualizar o código de aplicação web para usar o TargetingFilter

Neste momento, pode utilizar a bandeira de funcionalidades para ativar ou desativar a `Beta` funcionalidade para todos os utilizadores. Para ativar a bandeira da funcionalidade para alguns utilizadores enquanto a desativa para outros, atualize o seu código para usar `TargetingFilter` . Neste exemplo, utilizará o endereço de e-mail do utilizador inscrito como ID do utilizador e a parte do nome de domínio do endereço de e-mail como o grupo. Irá adicionar o utilizador e o grupo ao `TargetingContext` . Usa `TargetingFilter` este contexto para determinar o estado da bandeira de recurso para cada pedido.

1. Atualização para a versão mais recente do `Microsoft.FeatureManagement.AspNetCore` pacote.

   ```dotnetcli
   dotnet add package Microsoft.FeatureManagement.AspNetCore
   ```

1. Adicione um ficheiro *TestTargetingContextAccessor.cs:*

    ```csharp
    using Microsoft.AspNetCore.Http;
    using Microsoft.FeatureManagement.FeatureFilters;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;

    namespace TestFeatureFlags
    {
        public class TestTargetingContextAccessor : ITargetingContextAccessor
        {
            private const string TargetingContextLookup = "TestTargetingContextAccessor.TargetingContext";
            private readonly IHttpContextAccessor _httpContextAccessor;

            public TestTargetingContextAccessor(IHttpContextAccessor httpContextAccessor)
            {
                _httpContextAccessor = httpContextAccessor ?? throw new ArgumentNullException(nameof(httpContextAccessor));
            }

            public ValueTask<TargetingContext> GetContextAsync()
            {
                HttpContext httpContext = _httpContextAccessor.HttpContext;
                if (httpContext.Items.TryGetValue(TargetingContextLookup, out object value))
                {
                    return new ValueTask<TargetingContext>((TargetingContext)value);
                }
                List<string> groups = new List<string>();
                if (httpContext.User.Identity.Name != null)
                {
                    groups.Add(httpContext.User.Identity.Name.Split("@", StringSplitOptions.None)[1]);
                }
                TargetingContext targetingContext = new TargetingContext
                {
                    UserId = httpContext.User.Identity.Name,
                    Groups = groups
                };
                httpContext.Items[TargetingContextLookup] = targetingContext;
                return new ValueTask<TargetingContext>(targetingContext);
            }
        }
    }
    ```

1. No *Startup.cs*, adicione uma referência ao espaço de *nomes Microsoft.FeatureManagement.FeatureFilters:*

    ```csharp
    using Microsoft.FeatureManagement.FeatureFilters;
    ```

1. Atualizar o método *ConfigureServices* para `TargetingFilter` registar, após a chamada `AddFeatureManagement()` para:

    ```csharp
    services.AddFeatureManagement()
            .AddFeatureFilter<TargetingFilter>();
    ```

1. Atualize o método *ConfigureServices* para adicionar o `TestTargetingContextAccessor` criado no passo anterior à recolha de serviços. O *TargetingFilter* utiliza-o para determinar o contexto de segmentamento sempre que a bandeira de recurso é avaliada.

    ```csharp
      services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    ```

Todo o método *ConfigureServices* será assim:

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    services.AddDefaultIdentity<IdentityUser>(options => options.SignIn.RequireConfirmedAccount = true)
            .AddEntityFrameworkStores<ApplicationDbContext>();
    services.AddControllersWithViews();
    services.AddRazorPages();

    // Add feature management, targeting filter, and ITargetingContextAccessor to service collection
    services.AddFeatureManagement().AddFeatureFilter<TargetingFilter>();
    services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    }
```

## <a name="update-the-feature-flag-to-use-targetingfilter"></a>Atualize a bandeira de funcionalidade para utilizar o TargetingFilter

1. No portal Azure, vá à sua loja de Configuração de Aplicações e selecione **O Gestor de Funcionalidades**.

1. Selecione o menu de contexto para a bandeira de funcionalidade *Beta* que criou no arranque rápido. Selecione **Editar**.

    > [!div class="mx-imgBorder"]
    > ![Editar bandeira de recurso Beta](./media/edit-beta-feature-flag.png)

1. No ecrã **Editar,** selecione a caixa de verificação **de bandeira de funcionalidade Enable** se ainda não estiver selecionada. Em seguida, selecione a caixa de verificação **do filtro de função Utilização.**

1. Selecione o botão de rádio **Targeting.**

1. Selecione as seguintes opções:

    - **Percentagem de predefinição**: 0
    - **Grupos**: Insira um **nome** de _contoso.com_ e uma **percentagem** de _50_
    - **Utilizadores:**`test@contoso.com`

    O ecrã do filtro de funcionalidade será assim:

    > [!div class="mx-imgBorder"]
    > ![Bandeira de característica condicional](./media/feature-flag-filter-enabled.png)

    Estas definições resultam no seguinte comportamento:

    - A bandeira da funcionalidade está sempre ativada para o utilizador `test@contoso.com` , pois `test@contoso.com` encontra-se listada na secção _Utilizadores._
    - A bandeira da funcionalidade está ativada para 50% dos outros utilizadores do grupo _contoso.com,_ uma _vez contoso.com_ está listado na secção _Grupos_ com uma _Percentagem_ de _50_.
    - A funcionalidade é sempre desativada para todos os outros utilizadores, uma vez que a _percentagem de Predefinição_ está definida para _0_.

1. **Selecione Aplicar** para guardar estas definições e voltar ao ecrã **do gestor de funcionalidades.**

1. O **filtro 'Recurso'** para a bandeira de recurso aparece agora como *Targeting*. Este estado indica que o pavilhão de funcionalidades será ativado ou desativado por pedido, com base nos critérios aplicados pelo filtro de funções *Targeting.*

## <a name="targetingfilter-in-action"></a>DireccionamentoFiltro em ação

Para ver os efeitos desta bandeira de recurso, construa e execute a aplicação. Inicialmente, o item *Beta* não aparece na barra de ferramentas, porque a opção _percentagem Padrão_ está definida para 0.

Agora faça o sômin como `test@contoso.com` , usando a palavra-passe que definiu ao registar.. O item *Beta* aparece agora na barra de ferramentas, porque `test@contoso.com` é especificado como um utilizador-alvo.

O vídeo que se segue mostra este comportamento em ação.

> [!div class="mx-imgBorder"]
> ![DireccionamentoFiltro em ação](./media/feature-flags-targetingfilter.gif)

Pode criar utilizadores adicionais com `@contoso.com` endereços de e-mail para ver o comportamento das definições do grupo. 50% destes utilizadores verão o item *Beta.* Os outros 50% não verão o item *Beta.*

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Visão geral da gestão de recursos](./concept-feature-management.md)