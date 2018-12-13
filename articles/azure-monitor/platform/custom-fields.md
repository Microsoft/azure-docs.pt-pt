---
title: Campos personalizados no Azure Log Analytics | Documentos da Microsoft
description: A funcionalidade de campos personalizados do Log Analytics permite-lhe criar seus próprios campos pesquisáveis a partir de registos do Log Analytics que adicionar às propriedades de um registo recolhida.  Este artigo descreve o processo de criação de um campo personalizado e fornece instruções detalhadas com um evento de exemplo.
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
ms.date: 12/04/2018
ms.author: bwren
ms.openlocfilehash: 5a97a4e35f4d2a5f8ec2884c93329e180172f9e1
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53185227"
---
# <a name="create-custom-fields-in-log-analytics"></a>Criar campos personalizados no Log Analytics

> [!NOTE]
> Este artigo descreve como analisar dados de texto no Log Analytics à medida que é recolhido. Há vantagens em texto datain uma consulta de análise depois de serem recolhido conforme descrito em [analisar dados de texto no Log Analytics](../log-query/parse-text.md).

O **campos personalizados** recurso do Log Analytics permite-lhe expandir registos existentes no Log Analytics ao adicionar seus próprios campos pesquisáveis.  Campos personalizados são preenchidos automaticamente a partir dos dados extraídos de outras propriedades no mesmo registo.

![Descrição geral de campos personalizado](media/custom-fields/overview.png)

Por exemplo, o registo de exemplo abaixo tem dados úteis em da descrição do evento.  Extrair estes dados para propriedades separadas disponibiliza-o para ações como classificação e filtragem.

![Botão pesquisa de registos](media/custom-fields/sample-extract.png)

> [!NOTE]
> Na pré-visualização, está limitado a 100 campos personalizados na sua área de trabalho.  Esse limite será expandido quando esta funcionalidade atinge disponibilidade geral.
> 
> 

## <a name="creating-a-custom-field"></a>Criar um campo personalizado
Quando cria um campo personalizado, do Log Analytics tem de compreender os dados a utilizar para preencher o seu valor.  Utiliza uma tecnologia da Microsoft Research chamado FlashExtract para identificar rapidamente estes dados.  Em vez de exigir que forneça instruções explícitas, o Log Analytics aprende sobre os dados que pretende extrair a partir dos exemplos que fornecer.

As secções seguintes fornecem o procedimento para criar um campo personalizado.  Na parte inferior deste artigo é um passo a passo de uma extração de exemplo.

> [!NOTE]
> O campo personalizado é preenchido registos que correspondem aos critérios especificados são adicionadas ao Log Analytics, para que esta apenas será apresentada no registos recolhidos depois do campo personalizado é criado.  O campo personalizado não será adicionado aos registros que já estão no arquivo de dados quando é criado.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Passo 1 – identificar registos com o campo personalizado
A primeira etapa é identificar os registos que obterão o campo personalizado.  Começar com uma [consulta de registo padrão](../log-query/log-query-overview.md) e, em seguida, selecione um registo para atuar como o modelo que aprende do Log Analytics.  Quando especifica que vai extrair dados para um campo personalizado, o **Assistente de extração de campo** é aberto, onde valida e refine os critérios.

1. Aceda a **pesquisa de registos** e utilizar um [consulta para obter os registos](../log-query/log-query-overview.md) que farão com que o campo personalizado.
2. Selecione um registo que o Log Analytics irá utilizar para atuar como um modelo de extração de dados para preencher o campo personalizado.  Irá identificar os dados que pretende extrair este registo e do Log Analytics irá utilizar estas informações para determinar a lógica para preencher o campo personalizado para todos os registos semelhantes.
3. Clique no botão à esquerda de qualquer propriedade de texto do registo e selecione **extrair os campos de**.
4. O **é aberto o Assistente de extração de campos**, e o registo que selecionou é apresentado na **exemplo principal** coluna.  O campo personalizado será definido para esses registos com os mesmos valores das propriedades selecionadas.  
5. Se a seleção não é exatamente o que deseja, selecione os campos adicionais para restringir os critérios.  Para alterar os valores dos campos para os critérios, tem de cancelar e selecione um registo diferente correspondentes aos critérios de que pretende.

### <a name="step-2---perform-initial-extract"></a>Passo 2 - executar extração inicial.
Depois de identificar os registos que terão o campo personalizado, basta identificar os dados que pretende extrair.  O log Analytics irá utilizar estas informações para identificar padrões semelhantes nos registos semelhantes.  No passo após este será capaz de validar os resultados e fornecem mais detalhes para o Log Analytics a utilizar na sua análise.

1. Realce o texto no registo de exemplo que deseja preencher o campo personalizado.  , Em seguida, será apresentada uma caixa de diálogo para fornecer um nome para o campo de e para realizar a extração inicial.  Os carateres  **\_CF** será automaticamente anexado.
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
A secção seguinte descreve um exemplo completo de criação de um campo personalizado.  Neste exemplo extrai o nome do serviço nos eventos do Windows que indicam um serviço de alterar o estado.  Baseia-se nos eventos criados pelo Gestor de controlo de serviços no registo do sistema em computadores Windows.  Se quiser siga este exemplo, tem de ser [recolha de eventos de informações para o registo do sistema](data-sources-windows-events.md).

Introduza a seguinte consulta para devolver todos os eventos do Gestor de controlo de serviço que tenha um ID de evento de 7036 que é o evento que indica um serviço de início ou paragem.

![Consulta](media/custom-fields/query.png)

Podemos, em seguida, selecione qualquer registo com o evento ID 7036.

![Registo de origem](media/custom-fields/source-record.png)

Queremos que o nome do serviço que aparece no **RenderedDescription** propriedade e selecione o botão junto a esta propriedade.

![Extrair campos](media/custom-fields/extract-fields.png)

O **Assistente de extração de campos** é aberto e o **registo de eventos** e **EventID** campos estiverem selecionados no **exemplo principal** coluna.  Isto indica que o campo personalizado será definido para eventos de registo do sistema com um ID de evento de 7036.  Isso é suficiente para que não temos de selecionar quaisquer outros campos.

![Exemplo principal](media/custom-fields/main-example.png)

Nós destacamos o nome do serviço no **RenderedDescription** propriedade e a utilização **serviço** para identificar o nome do serviço.  O campo personalizado será chamado **Service_CF**.

![Título do campo](media/custom-fields/field-title.png)

Podemos ver que o nome do serviço é identificado corretamente para alguns registos, mas não para outras pessoas.   O **resultados de pesquisa** mostrar essa parte do nome para o **adaptador de desempenho de WMI** não foi selecionado.  O **resumo** mostra quatro regista com **DPRMA** serviço incluído incorretamente uma palavra extra e dois registos identificados **instalador de módulos** em vez de **Instalador de módulos do Windows**.  

![Resultados da pesquisa](media/custom-fields/search-results-01.png)

Vamos começar com o **adaptador de desempenho de WMI** registo.  Clicamos em seu ícone de edição e, em seguida **modificar este realce**.  

![Modificar o realce](media/custom-fields/modify-highlight.png)

Vamos aumentar o destaque para incluir a palavra **WMI** e, em seguida, volte a executar o extrair.  

![Exemplo de adicional](media/custom-fields/additional-example-01.png)

Podemos ver que as entradas para **adaptador de desempenho de WMI** ter sido corrigido, e também utilizado o Log Analytics essas informações para corrigir os registos para **instalador de módulo do Windows**.  Podemos ver na **resumo** secção entanto que **DPMRA** ainda não for a ser identificado corretamente.

![Resultados da pesquisa](media/custom-fields/search-results-02.png)

Podemos rolar para a um registo com o serviço DPMRA e usar o mesmo processo para corrigir esse registo.

![Exemplo de adicional](media/custom-fields/additional-example-02.png)

 Quando executamos a extração, podemos ver que todos os resultados são precisos.

![Resultados da pesquisa](media/custom-fields/search-results-03.png)

Podemos ver que **Service_CF** é criado, mas ainda não é adicionada para quaisquer registos.

![Contagem inicial](media/custom-fields/initial-count.png)

Após algum tempo ter passado por isso, novos eventos são recolhidos, podemos ver que o **Service_CF** campo agora está a ser adicionado para registos que correspondem aos nossos critérios.

![Resultados finais](media/custom-fields/final-results.png)

Agora podemos usar o campo personalizado, como qualquer outra propriedade de registo.  Para ilustrar isso, vamos criar uma consulta que agrupa pelos novos **Service_CF** campo para inspecionar quais serviços são mais ativos.

![Agrupar por consulta](media/custom-fields/query-group.png)

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [pesquisas de registos](../log-query/log-query-overview.md) para criar consultas com campos personalizados para critérios.
* Monitor [ficheiros de registo personalizado](data-sources-custom-logs.md) que analisar com campos personalizados.

