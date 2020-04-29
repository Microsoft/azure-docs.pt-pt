---
title: Livros Azure Monitor que criam parâmetros
description: Simplificar relatórios complexos com livros pré-construídos e parametrizados personalizados
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 3e7dda85f1f890d5ae0eb4722c3e028b373fdcab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77658222"
---
# <a name="workbook-parameters"></a>Parâmetros do livro

Os parâmetros permitem que os autores do livro recolham a entrada dos consumidores e a refiram noutras partes do livro – normalmente para examinar o conjunto de resultados ou definir o visual certo. É uma capacidade chave que permite aos autores construir relatórios e experiências interativas. 

Os livros de restão permitem controlar como os seus controlos de parâmetros são apresentados aos consumidores – caixa de texto vs. drop down, single-vs. multi-select, valores de texto, JSON, KQL ou Azure Resource Graph, etc.  

Os tipos de parâmetros suportados incluem:
* [Tempo](workbooks-time.md) - permite que um utilizador selecione a partir de intervalos de tempo pré-povoados ou selecione uma gama personalizada
* [Drop down](workbooks-dropdowns.md) - permite que um utilizador selecione a partir de um valor ou conjunto de valores
* [Texto](workbooks-text.md) - permite que um utilizador introduza texto arbitrário
* [Recurso](workbooks-resources.md) - permite que um utilizador selecione um ou mais recursos Azure
* [Subscrição](workbooks-resources.md) - permite que um utilizador selecione um ou mais recursos de subscrição do Azure
* Tipo de Recurso - permite que um utilizador selecione um ou mais valores do tipo de recurso Azure
* Localização - permite que um utilizador selecione um ou mais valores de localização Azure

Estes valores de parâmetros podem ser referenciados noutras partes dos livros, seja através de encadernações ou expansões de valor.

## <a name="creating-a-parameter"></a>Criar um parâmetro
1. Comece com um livro vazio no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro do livro.
3. Clique no botão azul _Adicionar Parâmetro._
4. Na nova vidraça de parâmetroque aparece:
    1. Nome do `TimeRange` parâmetro: (note que os nomes dos * __parâmetros__ **não podem** incluir espaços ou caracteres especiais)*
    2. Nome do `Time Range`ecrã: *(no entanto, __os nomes__ de exibição podem incluir espaços, caracteres especiais, emojis, etc.)*  
    2. Tipo de parâmetro:`Time range picker`
    3. Necessário:`checked`
    4. Intervalos de tempo disponíveis: Última hora, Últimas 12 horas, Últimas 24 horas, Últimas 48 horas, Últimos 3 dias, Últimos 7 dias e Permitir a seleção de horários personalizados
5. Escolha 'Guardar' da barra de ferramentas para criar o parâmetro.

   ![Imagem mostrando a criação de um parâmetro de intervalo de tempo](./media/workbooks-parameters/time-settings.png)

É assim que o livro será em modo de leitura, no estilo "Comprimidos".

   ![Imagem mostrando um parâmetro de intervalo de tempo no modo de leitura](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>Referenciando um parâmetro
### <a name="via-bindings"></a>Via Encadernações
1. Adicione um controlo de consulta ao livro e selecione um recurso Application Insights.
2. Abra a _hora_ de cair `Time Range` e selecione a opção a partir da secção Parâmetros na parte inferior.
3. Isto liga o parâmetro de intervalo de tempo à faixa de tempo do gráfico. O intervalo de tempo da consulta da amostra é agora de 24 horas.
4. Corra para ver os resultados

    ![Imagem mostrando um parâmetro de intervalo de tempo referenciado através de encadernações](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>Em KQL
1. Adicione um controlo de consulta ao livro e selecione um recurso Application Insights.
2. No KQL, introduza um filtro de alcance de tempo utilizando o parâmetro:`| where timestamp {TimeRange}`
3. Isto expande-se no `| where timestamp > ago(1d)`tempo de avaliação da consulta para, que é o valor de intervalo de tempo do parâmetro.
4. Corra para ver os resultados

    ![Imagem mostrando um intervalo de tempo referenciado em KQL](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>Em Texto 
1. Adicione um controlo de texto ao livro.
2. Na marcação, entrar`The chosen time range is {TimeRange:label}`
3. Escolha _edição feita_
4. O controlo de texto mostrará texto: O intervalo de _tempo escolhido é de 24 horas_

## <a name="parameter-options"></a>Opções de parâmetros
A secção _Em_ `label` Texto utilizou o parâmetro em vez do seu valor. Os parâmetros expõem várias dessas opções dependendo do seu tipo - por exemplo, os apanhadores de intervalo de tempo permitem valor, etiqueta, consulta, início, fim e grão.

Utilize `Previews` a secção do painel _de parâmetros_ editar para ver as opções de expansão para o seu parâmetro:

![Imagem mostrando uma gama de tempo paralímlo opções](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>Passos seguintes

* [Começar a](workbooks-visualizations.md) aprender mais sobre livros de trabalho muitas opções de visualizações ricas.
* [Controle](workbooks-access-control.md) e partilhe o acesso aos recursos do seu livro.
