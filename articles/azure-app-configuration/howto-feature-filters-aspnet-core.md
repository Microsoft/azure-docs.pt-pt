---
title: Utilize filtros de funcionalidades para ativar uma funcionalidade para um subconjunto de utilizadores
titleSuffix: Azure App Configuration
description: Aprenda a utilizar filtros de funcionalidades para ativar uma funcionalidade para um subconjunto de utilizadores
ms.service: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 181c97615985283011834dcf9145810b1563fb4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057004"
---
# <a name="use-feature-filters-to-enable-a-feature-for-a-subset-of-users"></a>Utilize filtros de funcionalidades para ativar uma funcionalidade para um subconjunto de utilizadores

As bandeiras de funcionalidade permitem ativar ou desativar a funcionalidade na sua aplicação. Uma simples bandeira de recurso está dentro ou fora. A aplicação comporta-se sempre da mesma forma. Por exemplo, pode lançar uma nova funcionalidade atrás de uma bandeira de recurso. Quando a bandeira da funcionalidade está ativada, todos os utilizadores vêem a nova funcionalidade. Desativar a bandeira de recurso esconde a nova funcionalidade.

Em contraste, uma _bandeira de característica condicional_ permite ativar ou desativar dinamicamente a bandeira da característica. A aplicação pode comportar-se de forma diferente, dependendo dos critérios de bandeira do recurso. Suponha que queira mostrar a sua nova funcionalidade a um pequeno subconjunto de utilizadores no início. Uma bandeira de funcionalidade condicional permite ativar a bandeira de funcionalidade para alguns utilizadores, desativando-a para outros. _Os filtros_ de funcionalidades determinam o estado da bandeira de recurso cada vez que é avaliado.

A `Microsoft.FeatureManagement` biblioteca inclui dois filtros de recurso:

- `PercentageFilter`permite a bandeira de recurso com base numa percentagem.
- `TimeWindowFilter`ativa a bandeira de recurso durante uma janela de tempo especificada.

Também pode criar o seu próprio filtro de funcionalidades que implementa a [interface Microsoft.FeatureManagement.IFeatureFilter](/dotnet/api/microsoft.featuremanagement.ifeaturefilter).

## <a name="registering-a-feature-filter"></a>Registar um filtro de funcionalidade

Regista um filtro de `AddFeatureFilter` funcionalidade sintetizando o método, especificando o nome do filtro de características. Por exemplo, os seguintes registos de `PercentageFilter`código:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Configurar um filtro de funcionalidade sintetizar na configuração da aplicação Azure

Alguns filtros de funcionalidades têm configurações adicionais. Por exemplo, `PercentageFilter` ativa uma funcionalidade com base numa percentagem. Tem uma definição que define a percentagem a utilizar.

Pode configurar estas definições para as bandeiras de características definidas na Configuração da Aplicação Azure. Por exemplo, siga estes `PercentageFilter` passos a utilizar para ativar a bandeira de funcionalidade para 50% dos pedidos para uma aplicação web:

1. Siga as instruções em [Quickstart: Adicione bandeiras de recurso a uma aplicação ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) para criar uma aplicação web com uma bandeira de recurso.

1. No portal Azure, vá à sua loja de configuração e clique em **Gestor de Funcionalidades**.

1. Clique no menu de contexto para a bandeira de recurso *Beta* que criou no arranque rápido. Clique em **Editar**.

    > [!div class="mx-imgBorder"]
    > ![Editar bandeira de recurso Beta](./media/edit-beta-feature-flag.png)

1. No ecrã **Editar,** selecione o botão de rádio **On** se ainda não estiver selecionado. Em seguida, clique no botão **Adicionar Filtro.** (A etiqueta do botão de rádio **On** altera-se para ler **Condicional**.)

1. No campo **Chave,** introduza *Microsoft.Percentagem*.

    > [!div class="mx-imgBorder"]
    > ![Adicionar filtro de recurso](./media/feature-flag-add-filter.png)

1. Clique no menu de contexto ao lado da tecla de filtro de funcionalidades. Clique em **Parâmetros de Edição**.

    > [!div class="mx-imgBorder"]
    > ![Editar os parâmetros do filtro de funcionalidades](./media/feature-flag-edit-filter-parameters.png)

1. Passe por baixo do cabeçalho de **nome** para que as caixas de texto apareçam na grelha. Introduza um **Nome** de *Valor* e um **Valor** de 50. O campo **Valor** indica a percentagem de pedidos para os quais ativa o filtro de características.

    > [!div class="mx-imgBorder"]
    > ![Definir parâmetros de filtro de características](./media/feature-flag-set-filter-parameters.png)

1. Clique **Em Aplicar** para voltar ao ecrã de bandeira da funcionalidade **Editar.** Em seguida, clique **em Aplicar** novamente para guardar as definições da bandeira de recurso.

1. O **Estado** da bandeira de recurso aparece agora como *Condicional.* Este estado indica que a bandeira de características será ativada ou desativada por pedido, com base nos critérios aplicados pelo filtro de características.

    > [!div class="mx-imgBorder"]
    > ![Bandeira de característica condicional](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>Filtros de recurso em ação

Para ver os efeitos desta bandeira de funcionalidade, lance a aplicação e acerte o botão **Refresh** no seu navegador várias vezes. Verá que o item *Beta* aparece na barra de ferramentas cerca de 50% das vezes. Está escondido o resto do tempo, `PercentageFilter` porque o desativa a funcionalidade *Beta* para um subconjunto de pedidos. O vídeo seguinte mostra este comportamento em ação.

> [!div class="mx-imgBorder"]
> ![Filtro percentual em ação](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Visão geral da gestão de recursos](./concept-feature-management.md)
