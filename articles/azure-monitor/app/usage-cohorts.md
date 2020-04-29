---
title: Coortes de utilização de Insights de Aplicação Azure [ ] Microsoft Docs
description: Analise diferentes conjuntos ou utilizadores, sessões, eventos ou operações que tenham algo em comum
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 04/10/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 0c552e8f3e732c49da02b2f5704fb9cf312fb3e8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77671091"
---
# <a name="application-insights-cohorts"></a>Coortes de Insights de Aplicação

Um coorte é um conjunto de utilizadores, sessões, eventos ou operações que têm algo em comum. Em Azure Application Insights, as coortes são definidas por uma consulta de análise. Nos casos em que tem de analisar repetidamente um conjunto específico de utilizadores ou eventos, as coortes podem dar-lhe mais flexibilidade para expressar exatamente o conjunto em que está interessado.

![Painel de coortes](./media/usage-cohorts/001.png)

## <a name="cohorts-versus-basic-filters"></a>Coortes versus filtros básicos

As coortes são usadas de formasemelhante aos filtros. Mas as definições de coortes são construídas a partir de consultas de análise personalizadas, por isso são muito mais adaptáveis e complexas. Ao contrário dos filtros, pode sossecar coortes para que outros membros da sua equipa possam reutilizá-los.

Pode definir um grupo de utilizadores que experimentaram uma nova funcionalidade na sua aplicação. Pode guardar esta coorte no seu recurso Application Insights. É fácil analisar este grupo de utilizadores específicos guardados no futuro.

> [!NOTE]
> Depois de criadas, as coortes estão disponíveis nas ferramentas Utilizadores, Sessões, Eventos e Fluxos de Utilizador.

## <a name="example-engaged-users"></a>Exemplo: Utilizadores contratados

A sua equipa define um utilizador contratado como qualquer pessoa que utilize a sua app cinco ou mais vezes num determinado mês. Nesta secção, define uma coorte destes utilizadores envolvidos.

1. Abra a ferramenta Cohorts.

2. Selecione o separador **Galeria do Modelo.** Você vê uma coleção de modelos para várias coortes.

3. Selecione **Utilizadores Contratados -- por Dias Usados**.

    Há três parâmetros para esta coorte:
    * **Atividades,** onde escolhe quais eventos e vistas de página contam como "uso".
    * **Período,** a definição de um mês.
    * **UsedAtLeastCustom**, o número de vezes que os utilizadores precisam de usar algo dentro de um período para contar como contratados.

4. Alterar **UsedLeastLeastCustom** a **5 dias+** e deixar **o Período** no padrão de 28 dias.

    ![Utilizadores contratados](./media/usage-cohorts/003.png)

    Agora, este coorte representa todos os IDs do utilizador enviados com qualquer evento ou visualização de página personalizada em 5 dias separados nos últimos 28.

5. Selecione **Guardar**.

   > [!TIP]
   > Dê ao seu coorte um nome, como "Utilizadores Contratados (5+ Dias)". Guarde-o para "Os meus relatórios" ou "Relatórios partilhados", dependendo se quer que outras pessoas que tenham acesso a este recurso Desinformação de Aplicações vejam esta coorte.

6. Selecione **De volta à Galeria**.

### <a name="what-can-you-do-by-using-this-cohort"></a>O que se pode fazer usando esta coorte?

Abra a ferramenta Utilizadores. Na caixa de lançamento do **Show,** escolha a coorte que criou no âmbito dos **Utilizadores que pertencem**a .

Agora a ferramenta Utilizadores é filtrada para este grupo de utilizadores:

![Painel dos utilizadores filtrado para uma determinada coorte](./media/usage-cohorts/004.png)

Algumas coisas importantes para notar:

* Não é possível criar este conjunto através de filtros normais. A lógica da data é mais avançada.
* Pode filtrar ainda mais esta coorte utilizando os filtros normais na ferramenta Utilizadores. Assim, embora a coorte esteja definida em janelas de 28 dias, ainda pode ajustar o intervalo de tempo na ferramenta Utilizadores para ser de 30, 60 ou 90 dias.

Estes filtros suportam questões mais sofisticadas que são impossíveis de expressar através do construtor de consultas. Um exemplo são _as pessoas que estiveram envolvidas nos últimos 28 dias. Como é que essas mesmas pessoas se comportaram nos últimos 60 dias?_

## <a name="example-events-cohort"></a>Exemplo: Coorte de eventos

Também pode fazer coortes de eventos. Nesta secção, você define uma coorte dos eventos e vistas de página. Depois vês como usá-las a partir das outras ferramentas. Esta coorte pode definir um conjunto de eventos que a sua equipa considera _de uso ativo_ ou um conjunto relacionado com uma determinada nova funcionalidade.

1. Abra a ferramenta Cohorts.

2. Selecione o separador **Galeria do Modelo.** Você verá uma coleção de modelos para várias coortes.

3. Selecione **Events Picker**.

    ![Screenshot do apanhador de eventos](./media/usage-cohorts/006.png)

4. Na caixa de entrega de **atividades,** selecione os eventos que pretende estar na coorte.

5. Salve a coorte e dê-lhe um nome.

## <a name="example-active-users-where-you-modify-a-query"></a>Exemplo: Utilizadores ativos onde modifica uma consulta

As duas coortes anteriores foram definidas usando caixas de entrega. Mas também pode definir coortes usando consultas de análise para total flexibilidade. Para ver como, criar uma coorte de utilizadores do Reino Unido.

![Imagem animada caminhando através do uso da ferramenta Cohorts](./media/usage-cohorts/cohorts0001.gif)

1. Abra a ferramenta Cohorts, selecione o separador **Galeria do Modelo** e selecione o coorte Utilizadores Em **Branco**.

    ![Coorte de utilizadores em branco](./media/usage-cohorts/001.png)

    Há três secções:
   * Uma secção de texto de Markdown, onde descreve si a coorte com mais detalhes para os outros da sua equipa.

   * Uma secção de parâmetros, onde você faz seus próprios parâmetros, como **Atividades** e outras caixas de entrega dos dois exemplos anteriores.

   * Uma secção de consulta, onde se define a coorte utilizando uma consulta de análise.

     Na secção de consulta, [escreve-se uma consulta de análise.](/azure/kusto/query) A consulta seleciona o conjunto certo de linhas que descrevem a coorte que pretende definir. A ferramenta Cohorts adiciona então implicitamente um "[ [ ] resumo pela cláusula user_Id" à consulta. Estes dados são pré-visualizados abaixo da consulta numa tabela, para que possa certificar-se de que a sua consulta está a devolver resultados.

     > [!NOTE]
     > Se não vir a consulta, tente redimensionar a secção para torná-la mais alta e revelar a consulta. O animado .gif no início desta secção ilustra o comportamento de redimensionamento.

2. Copiar e colar o seguinte texto no editor de consulta:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Selecione **Executar Consulta**. Se não vir as iDs dos utilizadores aparecerem na tabela, mude para um país/região onde a sua aplicação tem utilizadores.

4. Salve e nomeie a coorte.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

_Defini uma coorte de utilizadores de um determinado país/região. Quando comparo esta coorte na ferramenta Utilizadores a penas de configuração de um filtro naquele país/região, vejo resultados diferentes. Porquê?_

Coortes e filtros são diferentes. Suponha que tenha uma coorte de utilizadores do Reino Unido (definido como o exemplo anterior), e compare os seus resultados com a definição do filtro "País ou região = Reino Unido".

* A versão cohort mostra todos os eventos de utilizadores que enviaram um ou mais eventos do Reino Unido na faixa hordosiais atuais. Se dividir por país ou região, provavelmente verá muitos países e regiões.
* A versão dos filtros só mostra eventos do Reino Unido. Mas se dividirpor país ou região, só vê o Reino Unido.

## <a name="learn-more"></a>Saiba mais

* [Linguagem de consulta de análise](https://go.microsoft.com/fwlink/?linkid=856587)
* [Utilizadores, sessões, eventos](usage-segmentation.md)
* [Fluxos de utilizadores](usage-flows.md)
* [Visão geral do uso](usage-overview.md)
