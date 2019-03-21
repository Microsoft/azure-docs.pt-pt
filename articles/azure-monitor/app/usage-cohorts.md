---
title: Coortes de utilização do Application Insights do Azure | Documentos da Microsoft
description: Analisar conjuntos diferentes ou os utilizadores, sessões, eventos ou operações que têm algo em comum
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/10/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: a575161be0a339973f9c59cc13c2320b38939d04
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58108530"
---
# <a name="application-insights-cohorts"></a>Coortes do Application Insights

Uma coorte é um conjunto de utilizadores, sessões, eventos ou operações que têm algo em comum. No Azure Application Insights, coortes são definidos por uma consulta do analytics. Em casos onde é necessário analisar específicas de uma conjunto de utilizadores ou eventos repetidamente, coortes podem lhe dar mais flexibilidade para expressar exatamente o conjunto que tiver interesse em.

![Painel coortes](./media/usage-cohorts/001.png)

## <a name="cohorts-versus-basic-filters"></a>Coortes versus filtros básicos

Coortes são utilizados de forma semelhante aos filtros. Mas as definições dos coortes são criadas a partir de consultas de análise personalizada, para que sejam muito mais adaptáveis e complexos. Ao contrário de filtros, pode salvar coortes para que outros membros da sua equipa podem reutilizá-los.

Pode definir uma coorte de utilizadores que ter tentado todas uma nova funcionalidade na sua aplicação. É possível guardar esta coorte no recurso do Application Insights. É fácil analisar este grupo guardado de utilizadores específicos no futuro.

> [!NOTE]
> Depois de ocorrer, coortes estão disponíveis a partir as ferramentas de utilizadores, sessões, eventos e fluxos de utilizador.

## <a name="example-engaged-users"></a>Exemplo: Usuários envolvidos

Sua equipe define um utilizador envolvido como qualquer pessoa que utilize a sua aplicação cinco ou mais vezes num determinado mês. Nesta secção, vai definir uma coorte destes utilizadores envolvidos.

1. Abra a ferramenta coortes.

2. Selecione o **Galeria de modelos** separador. Verá uma coleção de modelos para vários coortes.

3. Selecione **envolvidos utilizadores – por dias utilizaram**.

    Há três parâmetros para esta coorte:
    * **Atividades**, em que escolha que eventos e vistas de página contam como "utilização".
    * **Período**, a definição de um mês.
    * **UsedAtLeastCustom**, o número de vezes que os utilizadores têm de utilizar algo dentro de um período para contar como envolvidos.

4. Alteração **UsedAtLeastCustom** ao **5 + dias**e deixe **período** no valor predefinido de 28 dias.

    ![Usuários envolvidos](./media/usage-cohorts/003.png)

    Agora esta coorte representa todos os IDs de utilizador enviados com qualquer vista de evento ou página personalizada em 5 dias no passado de separar 28.

5. Selecione **Guardar**.

   > [!TIP]
   > Dê um nome, sua coorte como "Engaged utilizadores (5 + dias)." Guarde-o para "Os meus relatórios" ou "Partilhado relatórios,", dependendo se pretende que outras pessoas que têm acesso a este recurso do Application Insights para ver esta coorte.

6. Selecione **voltar à galeria**.

### <a name="what-can-you-do-by-using-this-cohort"></a>O que pode fazer ao utilizar esta coorte?

Abra a ferramenta utilizadores. Na **mostrar** pendente caixa, selecione a coorte criou sob **usuários que pertencem ao**.

Agora, a ferramenta utilizadores é filtrada para esta coorte de utilizadores:

![Painel de utilizadores filtrado para uma determinada coorte](./media/usage-cohorts/004.png)

Algumas coisas importantes a serem observadas:

* Não é possível criar este conjunto por meio de filtros normais. A lógica de data é mais avançada.
* Pode filtrar ainda mais esta coorte ao utilizar os filtros normais na ferramenta de utilizadores. Portanto, embora a coorte é definida no windows de 28 dias, pode ajustar o intervalo de tempo na ferramenta de utilizadores para ser 30, 60 ou 90 dias.

Estes filtros suportam perguntas mais sofisticadas que são impossíveis de express por meio do construtor de consultas. Um exemplo é _pessoas que estavam envolvidas nos últimos 28 dias. Como essas mesmas pessoas terão um comportamento durante os últimos 60 dias?_

## <a name="example-events-cohort"></a>Exemplo: Coorte de eventos

Também é possível tornar coortes de eventos. Nesta secção, vai definir uma coorte de eventos e vistas de página. Em seguida, verá como usá-los a partir de outras ferramentas. Esta coorte poderá definir um conjunto de eventos que sua equipe considera _utilização de Active Directory_ ou um conjunto relacionado com um determinado recurso novo.

1. Abra a ferramenta coortes.

2. Selecione o **Galeria de modelos** separador. Verá uma coleção de modelos para vários coortes.

3. Selecione **Seletor de eventos**.

    ![Captura de ecrã do Seletor de eventos](./media/usage-cohorts/006.png)

4. Na **atividades** pendente caixa, selecione os eventos que pretende ser na coorte.

5. Guardar a coorte e atribua um nome.

## <a name="example-active-users-where-you-modify-a-query"></a>Exemplo: Utilizadores ativos em que modificar uma consulta

Os duas coortes anteriores foram definidos usando caixas de lista pendente. Mas também pode definir coortes utilizando consultas de análise para total flexibilidade. Para ver como, criar uma coorte de utilizadores do Reino Unido.

![Imagem animada percorrendo a utilização da ferramenta coortes](./media/usage-cohorts/cohorts0001.gif)

1. Abra a ferramenta coortes, selecione o **Galeria de modelos** separador e selecione **coorte de utilizadores em branco**.

    ![Coorte de utilizadores em branco](./media/usage-cohorts/001.png)

    Existem três secções:
   * Uma Markdown texto seção, onde descreve a coorte mais detalhadamente para outras pessoas na sua equipa.

   * Uma secção de parâmetros, em que fazem os seus parâmetros, como **atividades** e outras caixas de lista pendente dos dois exemplos anteriores.

   * Uma secção de consulta, onde define a coorte utilizando uma consulta do analytics.

     Na secção de consulta, [escrever uma consulta do analytics](/azure/kusto/query). A consulta seleciona o determinado conjunto de linhas que descrevem a coorte que pretende definir. A ferramenta coortes, em seguida, adiciona implicitamente um "| cláusula resumir por user_Id"para a consulta. Estes dados são visualizados abaixo a consulta numa tabela, para que pode verificar se a que sua consulta está a devolver resultados.

     > [!NOTE]
     > Se não vir a consulta, tente redimensionar a secção para torná-lo aumentasse e revelar a consulta. Os GIF animados no início desta secção ilustra o comportamento de redimensionamento.

2. Copie e cole o seguinte texto no editor de consultas:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Selecione **executar consulta**. Se não vir os IDs de utilizador são apresentadas na tabela, altere para um país em que seu aplicativo tem utilizadores.

4. Guardar e nomeie a coorte.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

_Eu defini uma coorte de utilizadores de um determinado país. Quando posso comparar esta coorte na ferramenta de utilizadores para apenas definindo um filtro no país, vejo resultados diferentes. Porquê?_

Coortes e os filtros são diferentes. Suponha que tem uma coorte de utilizadores do Reino Unido (definidos como no exemplo anterior) e comparar os resultados para definir o filtro "país ou região = Reino Unido."

* A versão de coorte mostra todos os eventos dos utilizadores que enviaram um ou mais eventos do Reino Unido no intervalo de tempo atual. Se dividir por país ou região, provavelmente ver diversos países e regiões.
* A versão de filtros mostra apenas os eventos do Reino Unido. Mas se dividir por país ou região, verá apenas o Reino Unido.

## <a name="learn-more"></a>Saiba mais

* [Linguagem de consulta do Analytics](https://go.microsoft.com/fwlink/?linkid=856587)
* [Os utilizadores, sessões, eventos](usage-segmentation.md)
* [Fluxos de utilizador](usage-flows.md)
* [Descrição geral da utilização](usage-overview.md)