---
title: Campos personalizados em Azure Monitor (Pré-visualização) | Microsoft Docs
description: A funcionalidade Campos Personalizados do Azure Monitor permite-lhe criar os seus próprios campos pes pesjáveis a partir de registos num espaço de trabalho do Log Analytics que adicionam às propriedades de um registo recolhido.  Este artigo descreve o processo para criar um campo personalizado e fornece uma passagem detalhada com um evento de amostragem.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/23/2019
ms.openlocfilehash: f35100d703e56d1fda731aab5e6a96c791c8f0d9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713682"
---
# <a name="create-custom-fields-in-a-log-analytics-workspace-in-azure-monitor-preview"></a>Criar campos personalizados num espaço de trabalho log analytics em Azure Monitor (Pré-visualização)

> [!NOTE]
> Este artigo descreve como analisar dados de texto num espaço de trabalho do Log Analytics à medida que são recolhidos. Recomendamos a análise dos dados de texto num filtro de consulta depois de recolhidos seguindo as orientações descritas nos [dados de texto parse no Azure Monitor](./parse-text.md). Proporciona várias vantagens ao usar campos personalizados.

> [!IMPORTANT]
> Os campos personalizados aumentam a quantidade de dados recolhidos no espaço de trabalho Log Analytics, o que pode aumentar o seu custo. Consulte [Gerir a utilização e os custos com os Registos do Monitor Azure](./manage-cost-storage.md#pricing-model) para obter mais detalhes.

A funcionalidade **Campos Personalizados** do Azure Monitor permite-lhe alargar os registos existentes no seu espaço de trabalho Log Analytics adicionando os seus próprios campos pes pescáveis.  Os campos personalizados são automaticamente povoados a partir de dados extraídos de outras propriedades no mesmo registo.

![O diagrama mostra um registo original associado a um registo modificado num espaço de trabalho do Log Analytics com pares de valor da propriedade adicionados à propriedade original no registo modificado.](media/custom-fields/overview.png)

Por exemplo, o registo da amostra abaixo tem dados úteis enterrados na descrição do evento. Extrair estes dados numa propriedade separada torna-os disponíveis para ações como triagem e filtragem.

![Extrato de amostra](media/custom-fields/sample-extract.png)

> [!NOTE]
> Na Pré-visualização, está limitado a 100 campos personalizados no seu espaço de trabalho.  Este limite será expandido quando esta funcionalidade atingir a disponibilidade geral.

## <a name="creating-a-custom-field"></a>Criar um campo personalizado
Ao criar um campo personalizado, o Log Analytics deve entender quais os dados a utilizar para preencher o seu valor.  Utiliza uma tecnologia da Microsoft Research chamada FlashExtract para identificar rapidamente estes dados.  Em vez de exigir que forneça instruções explícitas, o Azure Monitor aprende sobre os dados que pretende extrair dos exemplos que fornece.

As seguintes secções fornecem o procedimento para a criação de um campo personalizado.  Na parte inferior deste artigo está uma passagem de uma extração de amostra.

> [!NOTE]
> O campo personalizado é preenchido à medida que os registos correspondentes aos critérios especificados são adicionados ao espaço de trabalho Log Analytics, pelo que só aparecerá nos registos recolhidos após a criação do campo personalizado.  O campo personalizado não será adicionado aos registos que já se encontram na loja de dados quando este for criado.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Passo 1 - Identificar registos que terão o campo personalizado
O primeiro passo é identificar os registos que terão o campo personalizado.  Começa-se com uma [consulta de registo padrão](./log-query-overview.md) e, em seguida, seleciona um registo para funcionar como o modelo com o qual o Azure Monitor aprenderá.  Quando especificar que vai extrair dados num campo personalizado, abre-se o **Assistente de Extração de Campo** onde valida e aperfeiçoa os critérios.

1. Vá a **Logs** e use uma [consulta para recuperar os registos](./log-query-overview.md) que terão o campo personalizado.
2. Selecione um registo que o Log Analytics utilizará para funcionar como um modelo para extrair dados para preencher o campo personalizado.  Irá identificar os dados que pretende extrair deste registo, e o Log Analytics utilizará estas informações para determinar a lógica de povoar o campo personalizado para todos os registos similares.
3. Expanda as propriedades de registo, clique na elipse à esquerda da propriedade superior do registo e selecione **os campos extratos de**.
4. O **Assistente de Extração de Campo** é aberto e o registo selecionado é apresentado na coluna Exemplo **Principal.**  O campo personalizado será definido para esses registos com os mesmos valores nas propriedades que são selecionadas.  
5. Se a seleção não for exatamente o que deseja, selecione campos adicionais para reduzir os critérios.  Para alterar os valores de campo para os critérios, deve cancelar e selecionar um registo diferente correspondente aos critérios que pretende.

### <a name="step-2---perform-initial-extract"></a>Passo 2 - Executar o extrato inicial.
Uma vez identificados os registos que terão o campo personalizado, identifica os dados que pretende extrair.  O Log Analytics utilizará esta informação para identificar padrões semelhantes em registos semelhantes.  No passo seguinte poderá validar os resultados e fornecer mais detalhes para que o Log Analytics possa utilizar na sua análise.

1. Realce o texto no registo da amostra que pretende preencher o campo personalizado.  Em seguida, será apresentada uma caixa de diálogo para fornecer um nome e tipo de dados para o campo e para realizar o extrato inicial.  Os caracteres **\_ CF** serão automaticamente anexados.
2. Clique **em Extrato** para realizar uma análise dos registos recolhidos.  
3. As secções **Resumo** e **Resultados de Pesquisa** exibem os resultados do extrato para que possa verificar a sua precisão.  **O resumo** apresenta os critérios utilizados para identificar registos e uma contagem para cada um dos valores de dados identificados.  **Os Resultados da Pesquisa** fornecem uma lista detalhada de registos correspondentes aos critérios.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Passo 3 - Verificar a precisão do extrato e criar campo personalizado
Depois de ter realizado o extrato inicial, o Log Analytics apresentará os seus resultados com base em dados já recolhidos.  Se os resultados parecerem precisos, poderá criar o campo personalizado sem mais trabalhos.  Caso contrário, pode refinar os resultados para que o Log Analytics possa melhorar a sua lógica.

1. Se quaisquer valores no extrato inicial não estiverem corretos, clique no ícone **Editar** ao lado de um registo impreciso e selecione **Modifique este destaque** para modificar a seleção.
2. A entrada é copiada para a secção **exemplos adicionais** por baixo do **Exemplo Principal**.  Pode ajustar o destaque aqui para ajudar o Log Analytics a compreender a seleção que deveria ter feito.
3. Clique em **Extrato** para utilizar esta nova informação para avaliar todos os registos existentes.  Os resultados podem ser modificados para outros registos que não o que acabaste de modificar com base nesta nova inteligência.
4. Continue a adicionar correções até que todos os registos do extrato identifiquem corretamente os dados para povoar o novo campo personalizado.
5. Clique **em Guardar Extrato** quando estiver satisfeito com os resultados.  O campo personalizado está agora definido, mas ainda não será adicionado a nenhum registo.
6. Aguarde que novos registos correspondam aos critérios especificados sejam recolhidos e, em seguida, volte a executar a pesquisa de registo. Novos registos devem ter o campo personalizado.
7. Use o campo personalizado como qualquer outra propriedade discográfica.  Pode usá-lo para agregar e agrupar dados e até usá-lo para produzir novos insights.

## <a name="viewing-custom-fields"></a>Visualização de campos personalizados
Pode ver uma lista de todos os campos personalizados do seu grupo de gestão a partir do menu **Definições Avançadas** do seu espaço de trabalho Log Analytics no portal Azure.  Selecione **Data** e, em seguida, **campos personalizados** para uma lista de todos os campos personalizados no seu espaço de trabalho.  

![Campos personalizados](media/custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Remoção de um campo personalizado
Há duas maneiras de remover um campo personalizado.  A primeira é a opção **Remover** para cada campo ao visualizar a lista completa como descrito acima.  O outro método é recuperar um registo e clicar no botão à esquerda do campo.  O menu terá a opção de remover o campo personalizado.

## <a name="sample-walkthrough"></a>Instruções de exemplo
A secção seguinte percorre um exemplo completo de criação de um campo personalizado.  Este exemplo extrai o nome de serviço em eventos do Windows que indicam um estado de mudança de serviço.  Isto baseia-se em eventos criados pelo Service Control Manager durante a arranque do sistema em computadores Windows.  Se quiser seguir este exemplo, deve estar [a recolher eventos de Informação para o registo do Sistema](../agents/data-sources-windows-events.md).

Entramos na seguinte consulta para devolver todos os eventos do Service Control Manager que tenham um ID de Evento de 7036 que é o evento que indica um serviço a iniciar ou a parar.

![A screenshot mostra uma consulta para uma fonte de evento e ID.](media/custom-fields/query.png)

Em seguida, selecionamos e expandimos qualquer registo com o ID 7036 do evento.

![Registo de origem](media/custom-fields/source-record.png)

Definimos campos personalizados clicando na elipse ao lado da propriedade superior.

![Campos de extração](media/custom-fields/extract-fields.png)

O **Assistente de Extração de Campo** é aberto e os campos **EventLog** e **EventID** são selecionados na coluna **Exemplo Principal.**  Isto indica que o campo personalizado será definido para eventos a partir do registo do Sistema com um ID de evento de 7036.  Isto é suficiente para não termos de selecionar outros campos.

![Exemplo principal](media/custom-fields/main-example.png)

Destacamos o nome do serviço na propriedade **RenderedDescription** e utilizamos o **Serviço** para identificar o nome do serviço.  O campo personalizado será chamado **Service_CF.** O tipo de campo neste caso é uma corda, então podemos deixar isso inalterado.

![Título de campo](media/custom-fields/field-title.png)

Vemos que o nome de serviço é identificado corretamente para alguns registos, mas não para outros.   Os **Resultados** da Pesquisa mostram que parte do nome do **Adaptador de Desempenho WMI** não foi selecionado.  O **Resumo** mostra que um gravador identificou **o instalador** de módulos em vez do Instalador de **Módulos** do Windows .  

![Screenshot mostrando partes do nome de serviço realçadas no painel de Resultados de Pesquisa e um nome de serviço incorreto realçado no Resumo.](media/custom-fields/search-results-01.png)

Começamos com o disco do **Adaptador de Desempenho WMI.**  Clicamos no ícone de edição e depois **modificamos este destaque.**  

![Alterar destaque](media/custom-fields/modify-highlight.png)

Aumentamos o destaque para incluir a palavra **WMI** e, em seguida, reexamecê-lo.  

![Exemplo adicional](media/custom-fields/additional-example-01.png)

Podemos ver que as entradas para o **Adaptador de Desempenho WMI** foram corrigidas, e o Log Analytics também utilizou essa informação para corrigir os registos do **Instalador de Módulos do Windows**.

![Screenshot mostrando o nome de serviço completo realçado no painel de Resultados de Pesquisa e os nomes de serviço corretos destacados no Resumo.](media/custom-fields/search-results-02.png)

Agora podemos fazer uma consulta que verifica **Service_CF** é criado, mas ainda não é adicionado a nenhum registo. Isso é porque o campo personalizado não funciona contra os registos existentes, por isso temos de esperar que sejam recolhidos novos registos.

![Contagem inicial](media/custom-fields/initial-count.png)

Depois de algum tempo passado para que novos eventos sejam recolhidos, podemos ver que o **campo Service_CF** está agora a ser adicionado a registos que correspondam aos nossos critérios.

![Resultados finais](media/custom-fields/final-results.png)

Agora podemos usar o campo personalizado como qualquer outra propriedade discográfica.  Para ilustrar isto, criamos uma consulta que agruba pelo novo campo **Service_CF** para inspecionar quais os serviços mais ativos.

![Grupo por consulta](media/custom-fields/query-group.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais [sobre consultas de registo](./log-query-overview.md) para construir consultas usando campos personalizados para critérios.
* Monitorize [ficheiros de registo personalizados](../agents/data-sources-custom-logs.md) que analisa utilizando campos personalizados.