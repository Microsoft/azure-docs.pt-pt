---
title: Campos personalizados no Azure Monitor | Documentos da Microsoft
description: A funcionalidade de campos personalizados do Azure Monitor permite-lhe criar seus próprios campos pesquisáveis a partir de registos numa área de trabalho do Log Analytics que adicionar às propriedades de um registo recolhida.  Este artigo descreve o processo de criação de um campo personalizado e fornece instruções detalhadas com um evento de exemplo.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 31572b51-6b57-4945-8208-ecfc3b5304fc
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: bwren
ms.openlocfilehash: eebf3709657382eb403041e6637e32e5f5d43b15
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793350"
---
# <a name="create-custom-fields-in-a-log-analytics-workspace-in-azure-monitor"></a>Criar campos personalizados numa área de trabalho do Log Analytics no Azure Monitor

> [!NOTE]
> Este artigo descreve como analisar dados de texto numa área de trabalho do Log Analytics à medida que é recolhido. Há vantagens em dados de texto numa consulta de análise depois de serem recolhido conforme descrito em [analisar dados de texto no Azure Monitor](../log-query/parse-text.md).

O **campos personalizados** funcionalidade do Azure Monitor permite-lhe expandir registos existentes na sua área de trabalho do Log Analytics ao adicionar seus próprios campos pesquisáveis.  Campos personalizados são preenchidos automaticamente a partir dos dados extraídos de outras propriedades no mesmo registo.

![Descrição geral](media/custom-fields/overview.png)

Por exemplo, o registo de exemplo abaixo tem dados úteis em da descrição do evento. Extrair estes dados numa propriedade separada disponibiliza-o para ações como classificação e filtragem.

![Extração de exemplo](media/custom-fields/sample-extract.png)

> [!NOTE]
> Na pré-visualização, está limitado a 100 campos personalizados na sua área de trabalho.  Esse limite será expandido quando esta funcionalidade atinge disponibilidade geral.

## <a name="creating-a-custom-field"></a>Criar um campo personalizado
Quando cria um campo personalizado, do Log Analytics tem de compreender os dados a utilizar para preencher o seu valor.  Utiliza uma tecnologia da Microsoft Research chamado FlashExtract para identificar rapidamente estes dados.  Em vez de exigir que forneça instruções explícitas, o Azure Monitor aprende sobre os dados que pretende extrair a partir dos exemplos que fornecer.

As secções seguintes fornecem o procedimento para criar um campo personalizado.  Na parte inferior deste artigo é um passo a passo de uma extração de exemplo.

> [!NOTE]
> O campo personalizado é preenchido registos que correspondem aos critérios especificados são adicionadas à área de trabalho do Log Analytics, para que esta apenas será apresentada no registos recolhidos depois do campo personalizado é criado.  O campo personalizado não será adicionado aos registros que já estão no arquivo de dados quando é criado.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Passo 1 – identificar registos com o campo personalizado
A primeira etapa é identificar os registos que obterão o campo personalizado.  Começar com uma [consulta de registo padrão](../log-query/log-query-overview.md) e, em seguida, selecione um registo para atuar como o modelo que aprende do Azure Monitor.  Quando especifica que vai extrair dados para um campo personalizado, o **Assistente de extração de campo** é aberto, onde valida e refine os critérios.

1. Aceda a **Logs** e utilizar um [consulta para obter os registos](../log-query/log-query-overview.md) que farão com que o campo personalizado.
2. Selecione um registo que o Log Analytics irá utilizar para atuar como um modelo de extração de dados para preencher o campo personalizado.  Irá identificar os dados que pretende extrair este registo e do Log Analytics irá utilizar estas informações para determinar a lógica para preencher o campo personalizado para todos os registos semelhantes.
3. Expanda as propriedades de registo, clique nas reticências para a esquerda da propriedade principal do registo e selecione **extrair os campos de**.
4. O **Assistente de extração de campos** é aberto, e o registo que selecionou é apresentado na **exemplo principal** coluna.  O campo personalizado será definido para esses registos com os mesmos valores das propriedades selecionadas.  
5. Se a seleção não é exatamente o que deseja, selecione os campos adicionais para restringir os critérios.  Para alterar os valores dos campos para os critérios, tem de cancelar e selecione um registo diferente correspondentes aos critérios de que pretende.

### <a name="step-2---perform-initial-extract"></a>Passo 2 - executar extração inicial.
Depois de identificar os registos que terão o campo personalizado, basta identificar os dados que pretende extrair.  O log Analytics irá utilizar estas informações para identificar padrões semelhantes nos registos semelhantes.  No passo após este será capaz de validar os resultados e fornecem mais detalhes para o Log Analytics a utilizar na sua análise.

1. Realce o texto no registo de exemplo que deseja preencher o campo personalizado.  , Em seguida, será apresentada uma caixa de diálogo para fornecer um nome e tipo de dados para o campo de e para realizar a extração inicial.  Os carateres  **\_CF** será automaticamente anexado.
2. Clique em **extrair** para efetuar uma análise de registos recolhidos.  
3. O **resumo** e **resultados da pesquisa** secções exibem os resultados da extração, para que pode inspecionar sua precisão.  **Resumo** apresenta os critérios utilizados para identificar registos e uma contagem de cada um dos valores de dados identificados.  **Os resultados da pesquisa** fornece uma lista detalhada de registos que correspondam os critérios.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Passo 3 – verificar a precisão do extrair e criar campo personalizado
Assim que o utilizador tiver executado o extrair inicial, o Log Analytics irá apresentar os resultados com base nos dados que já tenham sido recolhidos.  Se os resultados ser precisos, em seguida, pode criar o campo personalizado com mais nenhum trabalho.  Caso contrário, em seguida, pode refinar os resultados para que o Log Analytics pode melhorar a sua lógica.

1. Se os valores existentes no extrair inicial não estiverem corretos, em seguida, clique nas **edite** ícone junto a um incorreta de registo e selecione **modificar este realce** para modificar a seleção.
2. A entrada é copiada para o **exemplos adicionais** secção por baixo da **exemplo principal**.  Pode ajustar o destaque aqui para ajudar a compreender a seleção deve fizeram do Log Analytics.
3. Clique em **extrair** usar essas novas informações para avaliar todos os registros existentes.  Os resultados podem ser modificados para registos que não seja o que acabaram de ser modificadas com base nesta nova inteligência.
4. Continue a adicionar as correções até que todos os registos no extrato de identificar corretamente os dados para preencher o campo personalizado novo.
5. Clique em **guardar extrair** quando estiver satisfeito com os resultados.  O campo personalizado está agora definido, mas este não ser adicionado ao quaisquer registos ainda.
6. Aguarde pela novos registos que correspondem aos critérios especificados a ser recolhido e, em seguida, execute novamente a pesquisa de registos. Novos registos devem ter o campo personalizado.
7. Utilize o campo personalizado, como qualquer outra propriedade de registo.  Pode usá-lo para dados de agregação e de grupo e até mesmo usá-lo para produzir novas informações.

## <a name="viewing-custom-fields"></a>Visualizar os campos personalizados
Pode ver uma lista de todos os campos personalizados no seu grupo de gestão a partir da **definições avançadas** menu da área de trabalho do Log Analytics no portal do Azure.  Selecione **dados** e, em seguida **campos personalizados** para obter uma lista de todos os campos personalizados na sua área de trabalho.  

![Campos personalizados](media/custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Remover um campo personalizado
Existem duas formas de remover um campo personalizado.  A primeira é o **remover** opção para cada campo ao visualizar a lista completa, conforme descrito acima.  O outro método é obter um registo e clique no botão à esquerda do campo.  O menu terá uma opção para remover o campo personalizado.

## <a name="sample-walkthrough"></a>Instruções de exemplo
A secção seguinte descreve um exemplo completo de criação de um campo personalizado.  Neste exemplo extrai o nome do serviço nos eventos do Windows que indicam um serviço de alterar o estado.  Baseia-se nos eventos criados pelo Gestor de controlo de serviços no início de sessão do sistema em computadores Windows.  Se quiser siga este exemplo, tem de ser [recolha de eventos de informações para o registo do sistema](data-sources-windows-events.md).

Introduza a seguinte consulta para devolver todos os eventos do Gestor de controlo de serviço que tenha um ID de evento de 7036 que é o evento que indica um serviço de início ou paragem.

![Consulta](media/custom-fields/query.png)

Podemos, em seguida, selecione e expanda qualquer registo com o evento ID 7036.

![Registo de origem](media/custom-fields/source-record.png)

Podemos definir campos personalizados ao clicar na elipse junto à propriedade superior.

![Extrair campos](media/custom-fields/extract-fields.png)

O **Assistente de extração de campos** é aberto e o **registo de eventos** e **EventID** campos estiverem selecionados no **exemplo principal** coluna.  Isto indica que o campo personalizado será definido para eventos de registo do sistema com um ID de evento de 7036.  Isso é suficiente para que não temos de selecionar quaisquer outros campos.

![Exemplo principal](media/custom-fields/main-example.png)

Nós destacamos o nome do serviço no **RenderedDescription** propriedade e a utilização **serviço** para identificar o nome do serviço.  O campo personalizado será chamado **Service_CF**. Nesse caso, o tipo de campo é uma cadeia de caracteres, portanto, podemos deixar que inalterados.

![Título do campo](media/custom-fields/field-title.png)

Podemos ver que o nome do serviço é identificado corretamente para alguns registos, mas não para outras pessoas.   O **resultados de pesquisa** mostrar essa parte do nome para o **adaptador de desempenho de WMI** não foi selecionado.  O **resumo** mostra que um registo identificado **instalador de módulos** em vez de **instalador de módulos do Windows**.  

![Resultados da pesquisa](media/custom-fields/search-results-01.png)

Vamos começar com o **adaptador de desempenho de WMI** registo.  Clicamos em seu ícone de edição e, em seguida **modificar este realce**.  

![Modificar o realce](media/custom-fields/modify-highlight.png)

Vamos aumentar o destaque para incluir a palavra **WMI** e, em seguida, volte a executar o extrair.  

![Exemplo de adicional](media/custom-fields/additional-example-01.png)

Podemos ver que as entradas para **adaptador de desempenho de WMI** ter sido corrigido, e também utilizado o Log Analytics essas informações para corrigir os registos para **instalador de módulo do Windows**.

![Resultados da pesquisa](media/custom-fields/search-results-02.png)

Agora podemos executar uma consulta que verifica **Service_CF** é criado, mas ainda não é adicionada para quaisquer registos. Isso ocorre porque o campo personalizado não funciona em relação a registos existentes, pelo que precisamos esperar para novos registros a recolher.

![Contagem inicial](media/custom-fields/initial-count.png)

Após algum tempo ter passado por isso, novos eventos são recolhidos, podemos ver que o **Service_CF** campo agora está a ser adicionado para registos que correspondem aos nossos critérios.

![Resultados finais](media/custom-fields/final-results.png)

Agora podemos usar o campo personalizado, como qualquer outra propriedade de registo.  Para ilustrar isso, vamos criar uma consulta que agrupa pelos novos **Service_CF** campo para inspecionar quais serviços são mais ativos.

![Agrupar por consulta](media/custom-fields/query-group.png)

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [registar as consultas](../log-query/log-query-overview.md) para criar consultas com campos personalizados para critérios.
* Monitor [ficheiros de registo personalizado](data-sources-custom-logs.md) que analisar com campos personalizados.

