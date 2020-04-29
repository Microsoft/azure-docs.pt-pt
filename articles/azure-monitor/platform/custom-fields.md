---
title: Campos personalizados no Monitor Azure (Pré-visualização) / Microsoft Docs
description: A funcionalidade Custom Fields do Azure Monitor permite-lhe criar os seus próprios campos pesquisáveis a partir de registos num espaço de trabalho de Log Analytics que adicionam às propriedades de um registo recolhido.  Este artigo descreve o processo para criar um campo personalizado e fornece uma passagem detalhada com um evento de amostra.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/23/2019
ms.openlocfilehash: bfb0a73631564c96a4af745fe9d7540a3a84f9c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77655366"
---
# <a name="create-custom-fields-in-a-log-analytics-workspace-in-azure-monitor-preview"></a>Criar campos personalizados num espaço de trabalho de Log Analytics no Monitor Azure (Pré-visualização)

> [!NOTE]
> Este artigo descreve como analisar dados de texto num espaço de trabalho do Log Analytics à medida que é recolhido. Recomendamos analisar os dados de texto num filtro de consulta depois de recolhido seguem as orientações descritas nos dados de [texto parse no Monitor Azure](../log-query/parse-text.md). Proporciona várias vantagens sobre a utilização de campos personalizados.

A funcionalidade **Custom Fields** do Monitor Azure permite-lhe estender os registos existentes no seu espaço de trabalho Log Analytics adicionando os seus próprios campos pesquisáveis.  Os campos personalizados são automaticamente povoados a partir de dados extraídos de outras propriedades no mesmo registo.

![Descrição geral](media/custom-fields/overview.png)

Por exemplo, o registo da amostra abaixo tem dados úteis enterrados na descrição do evento. A extração destes dados numa propriedade separada disponibiliza-os para ações como a triagem e a filtragem.

![Extrato de amostra](media/custom-fields/sample-extract.png)

> [!NOTE]
> Na Pré-Visualização, está limitado a 100 campos personalizados no seu espaço de trabalho.  Este limite será expandido quando esta funcionalidade atingir a disponibilidade geral.

## <a name="creating-a-custom-field"></a>Criar um campo personalizado
Quando cria um campo personalizado, o Log Analytics deve compreender quais os dados a utilizar para povoar o seu valor.  Utiliza uma tecnologia da Microsoft Research chamada FlashExtract para identificar rapidamente estes dados.  Em vez de exigir que forneça instruções explícitas, o Azure Monitor aprende sobre os dados que pretende extrair a partir de exemplos que fornece.

As seguintes secções fornecem o procedimento para a criação de um campo personalizado.  Na parte inferior deste artigo está uma passagem por uma extração de amostra.

> [!NOTE]
> O campo personalizado é povoado à medida que os registos correspondentes aos critérios especificados são adicionados ao espaço de trabalho do Log Analytics, pelo que só aparecerá nos registos recolhidos após a criação do campo personalizado.  O campo personalizado não será adicionado aos registos que já estão na loja de dados quando for criado.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Passo 1 - Identificar registos que terão o campo personalizado
O primeiro passo é identificar os registos que terão o campo personalizado.  Comece com uma consulta de [log padrão](../log-query/log-query-overview.md) e, em seguida, selecione um disco para agir como o modelo com o qual o Azure Monitor aprenderá.  Quando especifica que vai extrair dados num campo personalizado, o Assistente de **Extração** de Campo é aberto onde valida e refina os critérios.

1. Vá ao **Logs** e use uma [consulta para recuperar os registos](../log-query/log-query-overview.md) que terão o campo personalizado.
2. Selecione um registo que o Log Analytics utilizará para funcionar como um modelo para extrair dados para povoar o campo personalizado.  Você irá identificar os dados que pretende extrair deste registo, e o Log Analytics usará esta informação para determinar a lógica de povoar o campo personalizado para todos os registos semelhantes.
3. Expanda as propriedades de registo, clique na elipse à esquerda da propriedade superior do disco e selecione **campos extratos de**.
4. O **Assistente de Extração** de Campo está aberto e o registo selecionado está apresentado na coluna **Exemplo Principal.**  O campo personalizado será definido para aqueles registos com os mesmos valores nas propriedades selecionadas.  
5. Se a seleção não for exatamente o que deseja, selecione campos adicionais para reduzir os critérios.  Para alterar os valores de campo para os critérios, deve cancelar e selecionar um registo diferente que corresponda aos critérios que pretende.

### <a name="step-2---perform-initial-extract"></a>Passo 2 - Executar extrato inicial.
Depois de identificar os registos que terão o campo personalizado, identifica os dados que pretende extrair.  O Log Analytics utilizará esta informação para identificar padrões semelhantes em registos semelhantes.  No passo seguinte poderá validar os resultados e fornecer mais detalhes para o Log Analytics utilizar na sua análise.

1. Realce o texto no registo da amostra que pretende povoar o campo personalizado.  Em seguida, será-lhe apresentada uma caixa de diálogo para fornecer um nome e tipo de dados para o campo e para executar o extrato inicial.  Os ** \_** caracteres CF serão automaticamente anexados.
2. Clique em **Extrato** para efetuar uma análise dos registos recolhidos.  
3. As secções **de Resumo** e **Resultados** de Pesquisa mostram os resultados do extrato para que possa inspecionar a sua precisão.  **O resumo** apresenta os critérios utilizados para identificar registos e uma contagem para cada um dos valores de dados identificados.  **Os Resultados** da Pesquisa fornecem uma lista detalhada de registos que correspondem aos critérios.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Passo 3 – Verificar a precisão do extrato e criar campo personalizado
Depois de ter realizado o extrato inicial, o Log Analytics apresentará os seus resultados com base em dados já recolhidos.  Se os resultados parecerem precisos, então pode criar o campo personalizado sem mais trabalhos.  Caso contrário, pode refinar os resultados para que o Log Analytics possa melhorar a sua lógica.

1. Se algum valor no extrato inicial não estiver correto, clique no ícone **Editar** ao lado de um registo impreciso e selecione **Modificar este destaque** de forma a modificar a seleção.
2. A entrada é copiada para a secção **de exemplos adicionais** por baixo do **Exemplo Principal**.  Pode ajustar o destaque aqui para ajudar o Log Analytics a compreender a seleção que deveria ter feito.
3. Clique em **Extrato** para utilizar esta nova informação para avaliar todos os registos existentes.  Os resultados podem ser modificados para registos que não aquele que acabaste de modificar com base nesta nova inteligência.
4. Continue a adicionar correções até que todos os registos do extrato identifiquem corretamente os dados para povoar o novo campo personalizado.
5. Clique em **Guardar Extrato** quando estiver satisfeito com os resultados.  O campo personalizado está agora definido, mas ainda não será adicionado a nenhum registo.
6. Aguarde que sejam recolhidos novos registos correspondentes aos critérios especificados e, em seguida, faça a pesquisa de registo novamente. Novos registos devem ter o campo personalizado.
7. Use o campo personalizado como qualquer outra propriedade de gravação.  Pode usá-lo para agregar e agrupar dados de grupo e até usá-lo para produzir novos conhecimentos.

## <a name="viewing-custom-fields"></a>Visualização de campos personalizados
Pode ver uma lista de todos os campos personalizados do seu grupo de gestão a partir do menu **De Definições Avançadas** do seu espaço de trabalho Log Analytics no portal Azure.  Selecione **dados** e, em seguida, **campos personalizados** para uma lista de todos os campos personalizados no seu espaço de trabalho.  

![Campos personalizados](media/custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Remoção de um campo personalizado
Há duas maneiras de remover um campo personalizado.  A primeira é a opção **Remover** para cada campo ao visualizar a lista completa como descrito acima.  O outro método é recuperar um registo e clicar no botão à esquerda do campo.  O menu terá uma opção para remover o campo personalizado.

## <a name="sample-walkthrough"></a>Instruções de exemplo
A secção seguinte percorre um exemplo completo de criação de um campo personalizado.  Este exemplo extrai o nome de serviço em eventos Windows que indicam um estado de mudança de serviço.  Isto baseia-se em eventos criados pelo Service Control Manager durante o arranque do sistema em computadores Windows.  Se quiser seguir este exemplo, deve estar a recolher eventos de [Informação para o registo do Sistema](data-sources-windows-events.md).

Entramos na seguinte consulta para devolver todos os eventos do Gestor de Controlo de Serviço que têm um ID de evento de 7036 que é o evento que indica um serviço de arranque ou paragem.

![Consulta](media/custom-fields/query.png)

Em seguida, selecionamos e expandimos qualquer disco com id 7036 do evento.

![Registo de origem](media/custom-fields/source-record.png)

Definimos campos personalizados clicando na elipse ao lado da propriedade superior.

![Campos de extração](media/custom-fields/extract-fields.png)

O **Assistente de Extração** de Campo está aberto e os campos **EventLog** e **EventID** são selecionados na coluna **Exemplo Principal.**  Isto indica que o campo personalizado será definido para eventos a partir do registo do Sistema com um ID de evento de 7036.  Isto é suficiente para não termos de selecionar outros campos.

![Exemplo principal](media/custom-fields/main-example.png)

Destacamos o nome do serviço na propriedade **RenderedDescription** e utilizamos o **Serviço** para identificar o nome do serviço.  O campo personalizado será chamado **de Service_CF.** O tipo de campo neste caso é uma corda, para que possamos deixá-lo inalterado.

![Título de campo](media/custom-fields/field-title.png)

Vemos que o nome do serviço está devidamente identificado para alguns registos, mas não para outros.   Os **Resultados** de Pesquisa mostram que parte do nome para o Adaptador de **Desempenho wMI** não foi selecionada.  O **Resumo** mostra que um registo identificou o **Instalador** de Módulos em vez do Instalador de **Módulos Windows**.  

![Resultados de pesquisa](media/custom-fields/search-results-01.png)

Começamos com o disco do Adaptador de **Desempenho wMI.**  Clicamos no seu ícone de edição e, em seguida, **modificamos este destaque**.  

![Modificar destaque](media/custom-fields/modify-highlight.png)

Aumentamos o destaque para incluir a palavra **WMI** e, em seguida, reexecutar o extrato.  

![Exemplo adicional](media/custom-fields/additional-example-01.png)

Podemos ver que as entradas para **o Adaptador** de Desempenho WMI foram corrigidas, e o Log Analytics também utilizou essa informação para corrigir os registos do Instalador de **Módulos windows**.

![Resultados de pesquisa](media/custom-fields/search-results-02.png)

Podemos agora fazer uma consulta que verifica **Service_CF** é criada, mas ainda não é adicionado a quaisquer registos. Isso porque o campo personalizado não funciona contra os registos existentes, por isso temos de esperar que sejam recolhidos novos registos.

![Contagem inicial](media/custom-fields/initial-count.png)

Depois de algum tempo passado para que novos eventos sejam recolhidos, podemos ver que o campo **Service_CF** está agora a ser adicionado a registos que correspondem aos nossos critérios.

![Resultados finais](media/custom-fields/final-results.png)

Agora podemos usar o campo personalizado como qualquer outra propriedade de discos.  Para ilustrar isto, criamos uma consulta que agrupa junto ao novo campo **Service_CF** para inspecionar quais os serviços mais ativos.

![Grupo por consulta](media/custom-fields/query-group.png)

## <a name="next-steps"></a>Passos seguintes
* Aprenda sobre consultas de [registo](../log-query/log-query-overview.md) para construir consultas usando campos personalizados para critérios.
* Monitorize [ficheiros de registo personalizados](data-sources-custom-logs.md) que analise utilizando campos personalizados.

