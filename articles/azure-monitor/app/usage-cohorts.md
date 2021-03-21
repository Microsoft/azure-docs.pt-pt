---
title: Coortes de utilização Azure Application Insights | Microsoft Docs
description: Analise diferentes conjuntos ou utilizadores, sessões, eventos ou operações que tenham algo em comum
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 04/10/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 2c35356951560b11a605334aba4c26dbc38086de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100583548"
---
# <a name="application-insights-cohorts"></a>Coortes de Insights de Aplicação

Um coorte é um conjunto de utilizadores, sessões, eventos ou operações que têm algo em comum. Em Azure Application Insights, as coortes são definidas por uma consulta de análise. Nos casos em que tem de analisar repetidamente um conjunto específico de utilizadores ou eventos, as coortes podem dar-lhe mais flexibilidade para expressar exatamente o conjunto em que está interessado.

![Painel de coortes](./media/usage-cohorts/001.png)

## <a name="cohorts-versus-basic-filters"></a>Coortes versus filtros básicos

Coortes são usadas de formas semelhantes aos filtros. Mas as definições de coortes são construídas a partir de consultas de análise personalizadas, por isso são muito mais adaptáveis e complexas. Ao contrário dos filtros, pode guardar coortes para que outros membros da sua equipa os possam reutilizar.

Pode definir um grupo de utilizadores que já experimentaram uma nova funcionalidade na sua aplicação. Pode guardar esta coorte no seu recurso Application Insights. É fácil analisar este grupo salvo de utilizadores específicos no futuro.

> [!NOTE]
> Depois de criados, coortes estão disponíveis a partir das ferramentas Utilizadores, Sessões, Eventos e Fluxos de Utilizador.

## <a name="example-engaged-users"></a>Exemplo: Utilizadores envolvidos

A sua equipa define um utilizador contratado como qualquer pessoa que utilize a sua app cinco ou mais vezes num determinado mês. Nesta secção, você define um coorte destes utilizadores envolvidos.

1. Abra a ferramenta Cohorts.

2. Selecione o **separador Galeria de Modelos.** Você vê uma coleção de modelos para várias coortes.

3. Selecione **Utilizadores Contratados - por Dias Utilizados**.

    Há três parâmetros para esta coorte:
    * **Atividades,** onde você escolhe quais eventos e vistas de página contam como "uso".
    * **Período,** a definição de um mês.
    * **UsedAtLeastCustom**, o número de vezes que os utilizadores precisam de usar algo dentro de um período para contar como contratado.

4. Alterar **UsedAtLeastCustom** para **mais de 5 dias**, e deixar o **Período** por defeito de 28 dias.

    ![Utilizadores envolvidos](./media/usage-cohorts/003.png)

    Este grupo representa todos os IDs do utilizador enviados com qualquer evento personalizado ou vista de página em 5 dias separados nos últimos 28.

5. Selecione **Guardar**.

   > [!TIP]
   > Dê um nome ao seu companheiro, como "Utilizadores Contratados (5+ Dias)." Guarde-o para "Os meus relatórios" ou "relatórios partilhados", dependendo se pretende que outras pessoas que tenham acesso a este recurso Application Insights vejam esta coorte.

6. Selecione **De volta para a Galeria**.

### <a name="what-can-you-do-by-using-this-cohort"></a>O que se pode fazer usando esta coorte?

Abra a ferramenta Utilizadores. Na caixa drop-down **Show,** escolha a coorte criada sob **os Utilizadores a quem pertence**.

Agora a ferramenta Utilizadores é filtrada para este grupo de utilizadores:

![Painel de utilizadores filtrado para uma determinada coorte](./media/usage-cohorts/004.png)

Algumas coisas importantes a notar:

* Não é possível criar este conjunto através de filtros normais. A lógica da data é mais avançada.
* Pode filtrar ainda mais esta coorte utilizando os filtros normais na ferramenta Utilizadores. Assim, embora a coorte seja definida em janelas de 28 dias, ainda pode ajustar o intervalo de tempo na ferramenta Utilizadores para ser de 30, 60 ou 90 dias.

Estes filtros suportam questões mais sofisticadas que são impossíveis de expressar através do construtor de consultas. Um exemplo são _as pessoas que estiveram envolvidas nos últimos 28 dias. Como é que essas mesmas pessoas se comportaram nos últimos 60 dias?_

## <a name="example-events-cohort"></a>Exemplo: Coorte de eventos

Também pode fazer coortes com eventos. Nesta secção, você define uma coorte dos eventos e vistas de página. Depois vê-se como usá-las a partir das outras ferramentas. Esta coorte pode definir um conjunto de eventos que a sua equipa considera _uso ativo_ ou um conjunto relacionado com uma determinada nova funcionalidade.

1. Abra a ferramenta Cohorts.

2. Selecione o **separador Galeria de Modelos.** Você verá uma coleção de modelos para várias coortes.

3. Selecione **Event Picker**.

    ![Screenshot do selecionador de eventos](./media/usage-cohorts/006.png)

4. Na caixa de entrega de **Atividades,** selecione os eventos que deseja estar na coorte.

5. Salve a coorte e dê-lhe um nome.

## <a name="example-active-users-where-you-modify-a-query"></a>Exemplo: Utilizadores ativos onde modifica uma consulta

As duas coortes anteriores foram definidas usando caixas de entrega. Mas também pode definir coortes usando consultas de análise para uma flexibilidade total. Para ver como, criar uma coorte de utilizadores do Reino Unido.

![Imagem animada andando através do uso da ferramenta Cohorts](./media/usage-cohorts/cohorts0001.gif)

1. Abra a ferramenta Cohorts, selecione o **separador Galeria de Modelos** e selecione **coorte de Utilizadores em branco**.

    ![Coorte de utilizadores em branco](./media/usage-cohorts/001.png)

    Há três secções:
   * Uma secção de texto de Markdown, onde descreve a coorte com mais detalhes para outros da sua equipa.

   * Uma secção de parâmetros, onde você faz os seus próprios parâmetros, como **Atividades** e outras caixas de entrega dos dois exemplos anteriores.

   * Uma secção de consulta, onde se define a coorte usando uma consulta de análise.

     Na secção de consultas, [escreve-se uma consulta analítica.](/azure/kusto/query) A consulta seleciona o conjunto de linhas que descrevem a coorte que pretende definir. A ferramenta Cohorts adiciona então implicitamente um "| resumir por cláusula user_Id" para a consulta. Estes dados são pré-visualizados abaixo da consulta numa tabela, para que possa certificar-se de que a sua consulta está a devolver os resultados.

     > [!NOTE]
     > Se não vir a consulta, tente redimensionar a secção para torná-la mais alta e revelar a consulta. A animação .gif no início desta secção ilustra o comportamento de redimensionamento.

2. Copiar e colar o seguinte texto no editor de consulta:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Selecione **Executar Consulta**. Se não vir iDs de utilizador aparecerem na tabela, mude para um país/região onde a sua aplicação tem utilizadores.

4. Salve e diga o nome da coorte.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

_Definai um grupo de utilizadores de um determinado país/região. Quando comparo esta coorte na ferramenta Utilizadores com a definição de um filtro naquele país/região, vejo resultados diferentes. Porquê?_

Coortes e filtros são diferentes. Suponha que tem uma coorte de utilizadores do Reino Unido (definido como o exemplo anterior), e compare os seus resultados com a definição do filtro "País ou região = Reino Unido".

* A versão coorte mostra todos os eventos de utilizadores que enviaram um ou mais eventos do Reino Unido na atual faixa de tempo. Se dividir por país ou região, provavelmente verá muitos países e regiões.
* A versão dos filtros só mostra eventos do Reino Unido. Mas se dividirmos por país ou região, só vemos o Reino Unido.

## <a name="learn-more"></a>Saiba mais

* [Linguagem de consulta de análise](../logs/log-analytics-tutorial.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Utilizadores, sessões, eventos](usage-segmentation.md)
* [Fluxos do utilizador](usage-flows.md)
* [Visão geral do uso](usage-overview.md)