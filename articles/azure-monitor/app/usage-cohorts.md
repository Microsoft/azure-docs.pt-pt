---
title: Aplicativo Azure de uso do insights coortes | Microsoft Docs
description: Analisar diferentes conjuntos ou usuários, sessões, eventos ou operações que têm algo em comum
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 04/10/2018
ms.reviewer: mbullwin
ms.openlocfilehash: d6762ac9253c838f715588451441aa9aa467f673
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899502"
---
# <a name="application-insights-cohorts"></a>Application Insights coortes

Um coorte é um conjunto de usuários, sessões, eventos ou operações que têm algo em comum. No Aplicativo Azure insights, coortes são definidos por uma consulta de análise. Nos casos em que você precisa analisar um conjunto específico de usuários ou eventos repetidamente, o coortes pode oferecer mais flexibilidade para expressar exatamente o conjunto em que você está interessado.

![Painel de coortes](./media/usage-cohorts/001.png)

## <a name="cohorts-versus-basic-filters"></a>Coortes versus filtros básicos

Coortes são usados de maneiras semelhantes a filtros. Mas as definições de coortes são criadas a partir de consultas de análise personalizadas, para que elas sejam muito mais adaptáveis e complexas. Ao contrário dos filtros, você pode salvar coortes para que outros membros da sua equipe possam reutilizá-los.

Você pode definir um coorte de usuários que tentaram um novo recurso em seu aplicativo. Você pode salvar esse coorte em seu recurso de Application Insights. É fácil analisar esse grupo salvo de usuários específicos no futuro.

> [!NOTE]
> Depois de criados, os coortes estão disponíveis nos usuários, sessões, eventos e ferramentas de Fluxos dos Usuários.

## <a name="example-engaged-users"></a>Exemplo: usuários envolvidos

Sua equipe define um usuário envolvido como qualquer pessoa que usa seu aplicativo cinco ou mais vezes em um determinado mês. Nesta seção, você definirá um coorte desses usuários envolvidos.

1. Abra a ferramenta coortes.

2. Selecione a guia **Galeria de modelos** . Você verá uma coleção de modelos para vários coortes.

3. Selecione **usuários envolvidos--por dias usados**.

    Há três parâmetros para este coorte:
    * **Atividades**, nas quais você escolhe quais eventos e exibições de página contam como "uso".
    * **Período**, a definição de um mês.
    * **UsedAtLeastCustom**, o número de vezes que os usuários precisam usar algo dentro de um período para contar como envolvidos.

4. Altere **UsedAtLeastCustom** para **5 dias**e deixe o **período** no padrão de 28 dias.

    ![Usuários envolvidos](./media/usage-cohorts/003.png)

    Agora esse coorte representa todas as IDs de usuário enviadas com qualquer exibição de página ou evento personalizado em 5 dias separados nos últimos 28.

5. Selecione **Guardar**.

   > [!TIP]
   > Dê um nome ao seu coorte, como "usuários envolvidos (5 + dias)". Salve-o em "meus relatórios" ou "relatórios compartilhados", dependendo se você deseja que outras pessoas tenham acesso a esse Application Insights recurso para ver esse coorte.

6. Selecione **voltar à Galeria**.

### <a name="what-can-you-do-by-using-this-cohort"></a>O que você pode fazer usando este coorte?

Abra a ferramenta usuários. Na caixa suspensa **Mostrar** , escolha o coorte que você criou em **usuários que pertencem ao**.

Agora, a ferramenta de usuários é filtrada para este coorte de usuários:

![Painel de usuários filtrado para um determinado coorte](./media/usage-cohorts/004.png)

Algumas coisas importantes a serem observadas:

* Você não pode criar esse conjunto por meio de filtros normais. A lógica de data é mais avançada.
* Você pode filtrar ainda mais esse coorte usando os filtros normais na ferramenta usuários. Portanto, embora o coorte seja definido em janelas de 28 dias, você ainda pode ajustar o intervalo de tempo na ferramenta de usuários para ser 30, 60 ou 90 dias.

Esses filtros dão suporte a perguntas mais sofisticadas que são impossíveis de expressar por meio do construtor de consultas. Um exemplo é _as pessoas que estavam envolvidas nos últimos 28 dias. Como as mesmas pessoas se comportaram nos últimos 60 dias?_

## <a name="example-events-cohort"></a>Exemplo: eventos coorte

Você também pode fazer coortes de eventos. Nesta seção, você define um coorte dos eventos e das exibições de página. Em seguida, você verá como usá-los de outras ferramentas. Esse coorte pode definir um conjunto de eventos que sua equipe considera o _uso ativo_ ou um conjunto relacionado a um determinado recurso novo.

1. Abra a ferramenta coortes.

2. Selecione a guia **Galeria de modelos** . Você verá uma coleção de modelos para vários coortes.

3. Selecione **seletor de eventos**.

    ![Captura de tela do seletor de eventos](./media/usage-cohorts/006.png)

4. Na caixa suspensa **atividades** , selecione os eventos que você deseja que estejam no coorte.

5. Salve o coorte e dê um nome a ele.

## <a name="example-active-users-where-you-modify-a-query"></a>Exemplo: usuários ativos em que você modifica uma consulta

As duas coortes anteriores foram definidas usando caixas suspensas. Mas você também pode definir coortes usando consultas de análise para flexibilidade total. Para ver como criar um coorte de usuários do Reino Unido.

![Imagem animada percorrendo o uso da ferramenta coortes](./media/usage-cohorts/cohorts0001.gif)

1. Abra a ferramenta coortes, selecione a guia **Galeria de modelos** e selecione **usuários em branco coorte**.

    ![Usuários em branco coorte](./media/usage-cohorts/001.png)

    Há três seções:
   * Uma seção de texto de redução, na qual você descreve o coorte em mais detalhes para outras pessoas em sua equipe.

   * Uma seção de parâmetros, na qual você faz seus próprios parâmetros, como **atividades** e outras caixas suspensas dos dois exemplos anteriores.

   * Uma seção de consulta, onde você define o coorte usando uma consulta de análise.

     Na seção de consulta, você [escreve uma consulta do Analytics](/azure/kusto/query). A consulta seleciona o determinado conjunto de linhas que descreve o coorte que você deseja definir. Em seguida, a ferramenta coortes adiciona implicitamente um "| resumir por user_Id "para a consulta. Esses dados são visualizados abaixo da consulta em uma tabela, para que você possa verificar se a consulta está retornando os resultados.

     > [!NOTE]
     > Se você não vir a consulta, tente redimensionar a seção para torná-la mais alta e revelar a consulta. O. gif animado no início desta seção ilustra o comportamento de redimensionamento.

2. Copie e cole o seguinte texto no editor de consultas:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Selecione **Executar consulta**. Se você não vir as IDs de usuário aparecerem na tabela, altere para um país/região em que seu aplicativo tenha usuários.

4. Salve e nomeie o coorte.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

_Defini um coorte de usuários de um determinado país/região. Quando comparo esse coorte na ferramenta de usuários para definir apenas um filtro nesse país/região, vejo resultados diferentes. Por?_

Coortes e filtros são diferentes. Suponha que você tenha um coorte de usuários do Reino Unido (definido como o exemplo anterior) e compare seus resultados com a definição do filtro "país ou região = Reino Unido".

* A versão coorte mostra todos os eventos de usuários que enviaram um ou mais eventos do Reino Unido no intervalo de tempo atual. Se você dividir por país ou região, provavelmente verá muitos países e regiões.
* A versão de filtros mostra apenas os eventos do Reino Unido. Mas se você dividir por país ou região, verá apenas o Reino Unido.

## <a name="learn-more"></a>Saber mais

* [Linguagem de consulta do Analytics](https://go.microsoft.com/fwlink/?linkid=856587)
* [Usuários, sessões, eventos](usage-segmentation.md)
* [Fluxos de usuário](usage-flows.md)
* [Visão geral de uso](usage-overview.md)