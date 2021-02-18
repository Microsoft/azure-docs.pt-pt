---
title: Livros do Azure Monitor que criam parâmetros
description: Saiba como os parâmetros permitem que os autores de livros recolham a entrada dos consumidores e a refiram noutras partes do livro.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 7c086938ec98fc1d34ba21f678b2091c3b5ac1c4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100613892"
---
# <a name="workbook-parameters"></a>Parâmetros do livro

Os parâmetros permitem que os autores do livro recolham a entrada dos consumidores e a refiram noutras partes do livro – normalmente para definir o conjunto de resultados ou definir o visual certo. É uma capacidade fundamental que permite aos autores construir relatórios e experiências interativas. 

Os livros de trabalho permitem controlar a forma como os seus controlos de parâmetros são apresentados aos consumidores – caixa de texto vs. drop down, single-vs. multi-select, valores de texto, JSON, KQL ou Azure Resource Graph, etc.  

Os tipos de parâmetros suportados incluem:
* [Tempo](workbooks-time.md) - permite que um utilizador selecione a partir de intervalos de tempo pré-autónomos ou selecione uma gama personalizada
* [Drop down](workbooks-dropdowns.md) - permite que um utilizador selecione a partir de um valor ou conjunto de valores
* [Texto](workbooks-text.md) - permite que um utilizador introduza texto arbitrário
* [Recurso](workbooks-resources.md) - permite que um utilizador selecione um ou mais recursos Azure
* [Subscrição](workbooks-resources.md) - permite que um utilizador selecione um ou mais recursos de subscrição do Azure
* Tipo de recurso - permite que um utilizador selecione um ou mais valores do tipo de recurso Azure
* Localização - permite que um utilizador selecione um ou mais valores de localização Azure

Estes valores de parâmetro podem ser referenciados noutras partes de livros, quer através de encadernações quer de expansões de valor.

## <a name="creating-a-parameter"></a>Criar um parâmetro
1. Comece com um livro vazio no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro do livro.
3. Clique no botão azul _adicionar parâmetro._
4. No novo painel de parâmetros que aparece, insira:
    1. Nome do parâmetro: `TimeRange` *(note que __os nomes__ dos parâmetros **não podem** incluir espaços ou caracteres especiais)*
    2. Nome do visor: `Time Range` *(no entanto, __os nomes do visor__ podem incluir espaços, caracteres especiais, emojis, etc.)*  
    2. Tipo de parâmetro: `Time range picker`
    3. Necessário: `checked`
    4. Intervalos de tempo disponíveis: Última hora, Últimas 12 horas, Últimas 24 horas, Últimas 48 horas, Últimos 3 dias, Últimos 7 dias e Permitir a seleção de intervalos de tempo personalizados
5. Escolha 'Guardar' da barra de ferramentas para criar o parâmetro.

   ![Imagem mostrando a criação de um parâmetro de intervalo de tempo](./media/workbooks-parameters/time-settings.png)

É assim que o livro será em modo de leitura, no estilo "Pills".

   ![Imagem mostrando um parâmetro de intervalo de tempo no modo de leitura](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>Referenciando um parâmetro
### <a name="via-bindings"></a>Via Encadernações
1. Adicione um controlo de consulta ao livro de trabalho e selecione um recurso Application Insights.
2. Abra o _intervalo do intervalo_ do intervalo e selecione a `Time Range` opção a partir da secção Parâmetros na parte inferior.
3. Isto liga o parâmetro do intervalo de tempo ao intervalo de tempo do gráfico. O âmbito de tempo da consulta da amostra é agora dura 24 horas.
4. Fazer consulta para ver os resultados

    ![Imagem mostrando um parâmetro de intervalo de tempo referenciado através de encadernações](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>Em KQL
1. Adicione um controlo de consulta ao livro de trabalho e selecione um recurso Application Insights.
2. No KQL, introduza um filtro de âmbito temporal utilizando o parâmetro: `| where timestamp {TimeRange}`
3. Isto expande-se no tempo de avaliação de consulta para `| where timestamp > ago(1d)` , que é o valor da gama de tempo do parâmetro.
4. Fazer consulta para ver os resultados

    ![Imagem mostrando um intervalo de tempo referenciado em KQL](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>Em Texto 
1. Adicione um controlo de texto ao livro.
2. Na marcação, insira `The chosen time range is {TimeRange:label}`
3. Escolha _A Edição Feita_
4. O controlo de texto mostrará texto: _O intervalo de tempo escolhido é de últimas 24 horas_

## <a name="parameter-options"></a>Opções de parâmetros
A secção _In Text_ utilizou o parâmetro em vez do `label` seu valor. Os parâmetros expõem várias opções deste tipo dependendo do seu tipo - por exemplo, os selecionadores de intervalos de tempo permitem valor, etiqueta, consulta, início, extremidade e grão.

Utilize a `Previews` secção do painel de _parâmetros de edição_ para ver as opções de expansão do seu parâmetro:

![Imagem mostrando uma variedade de tempo de parâmetros](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>Passos seguintes

* [Começar a](../platform/workbooks-overview.md#visualizations) aprender mais sobre livros de trabalho muitas opções de visualizações ricas.
* [Controle](../platform/workbooks-access-control.md) e partilhe o acesso aos seus recursos do livro.