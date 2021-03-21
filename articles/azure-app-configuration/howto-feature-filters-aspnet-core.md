---
title: Utilize filtros de funcionalidades para permitir bandeiras de características condicionais
titleSuffix: Azure App Configuration
description: Saiba como utilizar filtros de funcionalidades para permitir bandeiras de características condicionais
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 39455c4bc193cce036bd169c702b5c020d53d2f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98602296"
---
# <a name="use-feature-filters-to-enable-conditional-feature-flags"></a>Utilize filtros de funcionalidades para permitir bandeiras de características condicionais

As bandeiras de funcionalidade permitem ativar ou desativar a funcionalidade na sua aplicação. Uma simples bandeira de recurso está dentro ou fora. A aplicação comporta-se sempre da mesma forma. Por exemplo, pode lançar uma nova funcionalidade por trás de uma bandeira de recurso. Quando a bandeira da funcionalidade está ativada, todos os utilizadores vêem a nova funcionalidade. Desativar a bandeira de recurso esconde a nova funcionalidade.

Em contrapartida, uma _bandeira de característica_ condicional permite que a bandeira de características seja ativada ou desativada de forma dinâmica. A aplicação pode comportar-se de forma diferente, dependendo dos critérios de bandeira de características. Suponha que pretenda mostrar a sua nova funcionalidade a um pequeno subconjunto de utilizadores no início. Uma bandeira de funcionalidade condicional permite-lhe ativar a bandeira da funcionalidade para alguns utilizadores, desativando-a para outros. _Os filtros de recurso_ determinam o estado da bandeira de recurso cada vez que é avaliado.

A `Microsoft.FeatureManagement` biblioteca inclui três filtros de características:

- `PercentageFilter` permite a bandeira de recurso com base numa percentagem.
- `TimeWindowFilter` permite a bandeira de recurso durante uma janela de tempo especificada.
- `TargetingFilter` permite a bandeira de funcionalidade para utilizadores e grupos especificados.

Também pode criar o seu próprio filtro de funcionalidades que implementa a [interface Microsoft.FeatureManagement.IFeatureFilter](/dotnet/api/microsoft.featuremanagement.ifeaturefilter).

## <a name="registering-a-feature-filter"></a>Registar um filtro de recurso

Regista um filtro de funcionalidades chamando o `AddFeatureFilter` método, especificando o nome do tipo do filtro de características pretendido. Por exemplo, os seguintes registos de `PercentageFilter` código:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Configurar um filtro de funcionalidades na Configuração da Aplicação Azure

Alguns filtros de funcionalidades têm configurações adicionais. Por exemplo, `PercentageFilter` ativa uma funcionalidade com base numa percentagem. Tem uma definição que define a percentagem a utilizar.

Pode configurar estas definições para bandeiras de funcionalidades definidas na Configuração da Aplicação Azure. Por exemplo, siga estes passos para `PercentageFilter` permitir a bandeira da funcionalidade para 50% dos pedidos a uma aplicação web:

1. Siga as instruções em [Quickstart: Adicione bandeiras de funcionalidades a uma aplicação Core ASP.NET](./quickstart-feature-flag-aspnet-core.md) para criar uma aplicação web com uma bandeira de recurso.

1. No portal Azure, vá à sua loja de configuração e clique em **Feature Manager**.

1. Clique no menu de contexto para a bandeira de funcionalidade *Beta* que criou no arranque rápido. Clique em **Editar**.

    > [!div class="mx-imgBorder"]
    > ![Editar bandeira de recurso Beta](./media/edit-beta-feature-flag.png)

1. No ecrã **Editar,** verifique a caixa de verificação **da bandeira de funcionalidade Enable** se ainda não está ativada. Em seguida, verifique a caixa de verificação **do filtro de recurso Use** e selecione **Custom**. 

1. No campo **Nome,** selecione *Microsoft.Percentagem*.

    > [!div class="mx-imgBorder"]
    > ![Adicionar filtro de funcionalidade](./media/feature-flag-add-filter.png)

1. Clique no menu de contexto ao lado do nome do filtro de funcionalidade. Clique **em Editar os parâmetros do filtro**.

    > [!div class="mx-imgBorder"]
    > ![Editar parâmetros de filtro de recursos](./media/feature-flags-edit-filter-parameters.png)

1. Insira um **Nome** de *Valor* e um **Valor** de 50. O campo **Valor** indica a percentagem de pedidos para ativar o filtro de funcionalidades.

    > [!div class="mx-imgBorder"]
    > ![Definir parâmetros de filtro de função](./media/feature-flag-set-filter-parameters.png)

1. Clique **em Aplicar** para voltar ao ecrã da bandeira da funcionalidade **Editar.** Em seguida, clique em **Aplicar** novamente para guardar as definições da bandeira de recurso.

1. Na página **do gestor de funcionalidades,** a bandeira de funcionalidade tem agora um valor de **filtro de funcionalidade** de *Costume*. 

    > [!div class="mx-imgBorder"]
    > ![Bandeira de recurso listada com um valor de filtro de recurso de "Personalizado"](./media/feature-flag-filter-custom.png)

## <a name="feature-filters-in-action"></a>Filtros de recurso em ação

Para ver os efeitos desta bandeira de funcionalidade, lance a aplicação e acerte o botão **Refresh** no seu navegador várias vezes. Verá que o item *Beta* aparece na barra de ferramentas cerca de 50% das vezes. Está escondido o resto do tempo, porque a `PercentageFilter` funcionalidade *Beta* desativa para um subconjunto de pedidos. O vídeo que se segue mostra este comportamento em ação.

> [!div class="mx-imgBorder"]
> ![DireccionamentoFiltro em ação](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ativar o lançamento encenado de funcionalidades para públicos direcionados](./howto-targetingfilter-aspnet-core.md)
