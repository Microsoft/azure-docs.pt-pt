---
title: Capacidades de caça em Azure Sentinel| Microsoft Docs
description: Use as consultas de caça incorporadas do Azure Sentinel para guiá-lo a fazer as perguntas certas para encontrar problemas nos seus dados.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2019
ms.author: yelevin
ms.openlocfilehash: b88185a09e69b1c7bd6c6c58b8e22190b4a2e394
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576772"
---
# <a name="hunt-for-threats-with-azure-sentinel"></a>Caça às ameaças com Azure Sentinel

Se você é um investigador que quer ser proativo sobre a procura de ameaças à segurança, Azure Sentinel poderosa busca de caça e ferramentas de consulta para caçar ameaças de segurança através das fontes de dados da sua organização. Mas os seus sistemas e aparelhos de segurança geram montanhas de dados que podem ser difíceis de analisar e filtrar em eventos significativos. Para ajudar os analistas de segurança a procurar proativamente novas anomalias que não foram detetadas pelas suas aplicações de segurança, as consultas de caça incorporadas do Azure Sentinel guiam-no a fazer as perguntas certas para encontrar problemas nos dados que já tem na sua rede. 

Por exemplo, uma consulta incorporada fornece dados sobre os processos mais incomuns em execução na sua infraestrutura - você não quereria um alerta sobre cada vez que são executados, eles podem ser totalmente inocentes, mas você pode querer dar uma olhada na consulta de vez em quando para ver se há algo incomum. 



Com a caça do Azure Sentinel, pode aproveitar as seguintes capacidades:

- Consultas incorporadas: Para começar, uma página inicial fornece exemplos de consulta pré-carregados projetados para começar e familiarizar-se com as tabelas e o idioma de consulta. Estas consultas de caça incorporadas são desenvolvidas por investigadores de segurança da Microsoft de forma contínua, adicionando novas consultas e afinando as consultas existentes para fornecer-lhe um ponto de entrada para procurar novas deteções e descobrir onde começar a caçar para o início de novos ataques. 

- Linguagem de consulta poderosa com o IntelliSense: Construído em cima de uma linguagem de consulta que lhe dá a flexibilidade necessária para levar a caça para o próximo nível.

- Crie os seus próprios favoritos: Durante o processo de caça, poderá encontrar fósforos ou descobertas, dashboards ou atividades que pareçam incomuns ou suspeitas. Para marcar estes itens para que possa voltar a eles no futuro, utilize a funcionalidade de marcação de livros. Os favoritos permitem guardar itens para mais tarde, para ser usado para criar um incidente para a investigação. Para obter mais informações sobre os marcadores, consulte [use os marcadores na caça.](hunting.md)
- Use cadernos para automatizar a investigação: Os cadernos são como livros de jogadas passo a passo que você pode construir para percorrer os passos de uma investigação e caça.  Os cadernos encapsulam todos os passos de caça num livro reutilizável que pode ser partilhado com outros da sua organização. 
- Consultar os dados armazenados: Os dados estão acessíveis em tabelas para consulta. Por exemplo, você pode consultar a criação de processos, eventos DNS, e muitos outros tipos de eventos.

- Links para a comunidade: Aproveite o poder da comunidade para encontrar consultas adicionais e fontes de dados.
 
## <a name="get-started-hunting"></a>Começar a caçar

1. No portal Azure Sentinel, clique em **Hunting.**
  ![Azure Sentinel começa a caçar](media/tutorial-hunting/hunting-start.png)

2. Quando abre a página **De Caça,** todas as consultas de caça são exibidas numa única mesa. A tabela lista todas as consultas escritas pela equipa de analistas de segurança da Microsoft, bem como qualquer consulta adicional que tenha criado ou modificado. Cada consulta fornece uma descrição do que procura, e que tipo de dados funciona. Estes modelos são agrupados pelas suas várias táticas - os ícones à direita categorizam o tipo de ameaça, como o acesso inicial, persistência e exfiltração. Pode filtrar estes modelos de consulta de caça usando qualquer um dos campos. Pode guardar qualquer consulta aos seus favoritos. Ao guardar uma consulta aos seus favoritos, a consulta executa automaticamente cada vez que a página **De Caça** é acedida. Você pode criar sua própria consulta de caça ou clone e personalizar um modelo de consulta de caça existente. 
 
2. Clique em **executar consulta** na página de detalhes da consulta de caça para executar qualquer consulta sem sair da página de caça.  O número de partidas é apresentado dentro da tabela. Reveja a lista de consultas de caça e seus fósforos. Vê em que fase da cadeia de morte a partida está associada.

3. Efetue uma revisão rápida da consulta subjacente no painel de detalhes de consulta ou clique no **resultado da consulta** para abrir a consulta no Log Analytics. Na parte inferior, reveja os fósforos para a consulta.

4.    Clique na linha e **selecione Adicionar o marcador** para adicionar as linhas a serem investigadas - pode fazê-lo para qualquer coisa que pareça suspeita. 

5. Em seguida, volte para a página principal **de Hunting** e clique no separador **Bookmarks** para ver todas as atividades suspeitas. 

6. Selecione um marcador e, em seguida, clique **em Investigar** para abrir a experiência de investigação. Pode filtrar os marcadores. Por exemplo, se estiver a investigar uma campanha, pode criar uma etiqueta para a campanha e depois filtrar todos os favoritos com base na campanha.

1. Depois de descobrir que consulta de caça fornece insights de alto valor sobre possíveis ataques, também pode criar regras de deteção personalizadas com base na sua consulta e emergir essas informações como alertas para os seus socorristas de incidentes de segurança.

 

## <a name="query-language"></a>Linguagem da consulta 

A caça em Azure Sentinel é baseada na linguagem de consulta kusto. Para obter mais informações sobre a língua de consulta e os operadores apoiados, consulte [Referência linguística de consulta.](../azure-monitor/logs/get-started-queries.md)

## <a name="public-hunting-query-github-repository"></a>Consulta pública de caça GitHub repositório

Confira o [repositório de consultas de caça.](https://github.com/Azure/Orion) Contribuir e utilizar consultas de exemplo partilhadas pelos nossos clientes.

 

## <a name="sample-query"></a>Consulta de exemplo

Uma consulta típica começa com um nome de mesa seguido por uma série de operadores separados por \| .

No exemplo acima, comece com o nome de mesa SecurityEvent e adicione elementos canalizados conforme necessário.

1. Defina um filtro de tempo para rever apenas os registos dos sete dias anteriores.

2. Adicione um filtro na consulta apenas para mostrar o ID 4688 do evento.

3. Adicione um filtro na consulta no CommandLine para conter apenas instâncias de cscript.exe.

4. Projete apenas as colunas que está interessado em explorar e limite os resultados a 1000 e clique em **Executar consulta**.
5. Clique no triângulo verde e execute a consulta. Pode testar a consulta e executá-la para procurar um comportamento anómalo.

## <a name="useful-operators"></a>Operadores úteis

A linguagem de consulta é poderosa e tem muitos operadores disponíveis, alguns operadores úteis estão listados aqui:

**onde** - Filtrar uma tabela para o subconjunto de linhas que satisfazem um predicado.

**resumir** - Produzir uma tabela que agrega o conteúdo da tabela de entrada.

**juntar** - Fundir as linhas de duas tabelas para formar uma nova tabela, combinando valores das colunas especificadas a partir de cada mesa.

**contagem** - Devolva o número de registos no conjunto de registos de entrada.

**top** - Devolva os primeiros registos N classificados pelas colunas especificadas.

**limite** - Volte ao número especificado de linhas.

**projeto** - Selecione as colunas para incluir, renomear ou largar e inserir novas colunas computadas.

**prolongar** - Criar colunas calculadas e apdicê-las ao conjunto de resultados.

**makeset** - Devolva uma matriz dinâmica (JSON) do conjunto de valores distintos que a Expr recebe no grupo

**encontrar** - Encontrar linhas que combinem com um predicado através de um conjunto de mesas.

## <a name="save-a-query"></a>Guarde uma consulta

Você pode criar ou modificar uma consulta e guardá-la como sua própria consulta ou partilhá-la com utilizadores que estão no mesmo inquilino.

   ![Salvar consulta](./media/tutorial-hunting/save-query.png)

Criar uma nova consulta de caça:

1. Clique **em Nova consulta** e selecione **Guardar**.
2. Preencha todos os campos em branco e **selecione Guardar**.

   ![Nova consulta](./media/tutorial-hunting/new-query.png)

Clone e modifique uma consulta de caça existente:

1. Selecione a consulta de caça na tabela que pretende modificar.
2. Selecione a elipse (...) na linha da consulta que pretende modificar e selecione **a consulta Clone**.

   ![consulta clone](./media/tutorial-hunting/clone-query.png)
 

3. Modifique a consulta e **selecione Criar**.

   ![consulta personalizada](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a dirigir uma investigação de caça com Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:


- [Use cadernos para executar campanhas de caça automatizadas](notebooks.md)
- [Use marcadores para guardar informações interessantes durante a caça](bookmarks.md)