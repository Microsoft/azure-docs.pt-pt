---
title: Capacidades de caça em Azure Sentinel. Microsoft Docs
description: Este artigo descreve como usar as capacidades de caça Azure Sentinel.
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
ms.openlocfilehash: 52af688917aa531d125f83844df29a988ed7cb7e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81686639"
---
# <a name="hunt-for-threats-with-azure-sentinel"></a>Caça às ameaças com O Sentinela

Se você é um investigador que quer ser proativo em procurar ameaças à segurança, Azure Sentinel poderosa procura de caça e ferramentas de consulta para caçar ameaças de segurança através de fontes de dados da sua organização. Mas os seus sistemas e aparelhos de segurança geram montanhas de dados que podem ser difíceis de analisar e filtrar em eventos significativos. Para ajudar os analistas de segurança a procurarem proativamente novas anomalias que não foram detetadas pelas suas aplicações de segurança, as consultas de caça incorporadas do Azure Sentinel guiam-no a fazer as perguntas certas para encontrar problemas nos dados que já tem na sua rede. 

Por exemplo, uma consulta incorporada fornece dados sobre os processos mais incomuns que estão em execução na sua infraestrutura - você não quereria um alerta sobre cada vez que eles são executados, eles podem ser totalmente inocentes, mas você pode querer dar uma olhada na consulta de vez em quando para ver se há algo incomum. 



Com a caça azure sentinel, você pode aproveitar as seguintes capacidades:

- Consultas incorporadas: Para começar, uma página inicial fornece exemplos de consulta pré-carregados projetados para começar e levá-lo familiarizar-se com as tabelas e a linguagem de consulta. Estas consultas de caça incorporadas são desenvolvidas por investigadores de segurança da Microsoft de forma contínua, adicionando novas consultas e afinando as consultas existentes para lhe fornecer um ponto de entrada para procurar novas deteções e descobrir onde começar a caçar para o início de novos ataques. 

- Linguagem de consulta poderosa com IntelliSense: Construída em cima de uma linguagem de consulta que lhe dá a flexibilidade que você precisa para levar a caça para o próximo nível.

- Crie os seus próprios favoritos: Durante o processo de caça, poderá encontrar fósforos ou descobertas, dashboards ou atividades que pareçam incomuns ou suspeitas. Para marcar esses itens para que possa voltar para eles no futuro, use a funcionalidade bookmark. Os marcadores permitem-lhe guardar itens para mais tarde, para ser usado para criar um incidente para investigação. Para mais informações sobre os marcadores, consulte [Use os marcadores na caça.](hunting.md)
- Use cadernos para automatizar a investigação: Os cadernos são como livros passo a passo que você pode construir para percorrer os degraus de uma investigação e caça.  Os cadernos encapsulam todos os passos de caça num livro reutilizável que pode ser partilhado com outros da sua organização. 
- Consulta dos dados armazenados: Os dados são acessíveis em tabelas para consultar. Por exemplo, você pode consultar a criação de processos, eventos DNS, e muitos outros tipos de eventos.

- Ligações à comunidade: Aproveite o poder da maior comunidade para encontrar consultas adicionais e fontes de dados.
 
## <a name="get-started-hunting"></a>Começar a caçar

1. No portal Azure Sentinel, clique em **Hunting**.
  ![Azure Sentinel começa a caçar](media/tutorial-hunting/hunting-start.png)

2. Quando abres a página **da Caça,** todas as consultas de caça são exibidas numa única mesa. A tabela lista todas as consultas escritas pela equipa de analistas de segurança da Microsoft, bem como qualquer consulta adicional que tenha criado ou modificado. Cada consulta fornece uma descrição do que caça, e que tipo de dados ele executa. Estes modelos são agrupados pelas suas várias táticas - os ícones da direita categorizam o tipo de ameaça, como o acesso inicial, a persistência e a exfiltração. Pode filtrar estes modelos de consulta de caça utilizando qualquer um dos campos. Podeguardar qualquer consulta aos seus favoritos. Ao guardar uma consulta aos seus favoritos, a consulta corre automaticamente cada vez que a página **de Caça** é acedida. Você pode criar sua própria consulta de caça ou clone e personalizar um modelo de consulta de caça existente. 
 
2. Clique em **Executar consulta** na página de detalhes da consulta de caça para executar qualquer consulta sem sair da página de caça.  O número de fósforos é apresentado dentro da tabela. Reveja a lista de consultas de caça e os seus fósforos. Confira em que fase da cadeia de morte o fósforo está associado.

3. Faça uma revisão rápida da consulta subjacente no painel de detalhes da consulta ou clique em ver o resultado da **consulta** para abrir a consulta no Log Analytics. No fundo, reveja os fósforos para a consulta.

4.    Clique na linha e selecione **Adicionar marcador** para adicionar as linhas a serem investigadas - pode fazê-lo por qualquer coisa que pareça suspeito. 

5. Em seguida, volte para a página principal **de Caça** e clique no separador **Bookmarks** para ver todas as atividades suspeitas. 

6. Selecione um marcador e, em seguida, clique em **Investigar** para abrir a experiência de investigação. Pode filtrar os marcadores. Por exemplo, se estiver a investigar uma campanha, pode criar uma etiqueta para a campanha e depois filtrar todos os marcadores com base na campanha.

1. Depois de descobrir que consulta de caça fornece insights de alto valor em possíveis ataques, também pode criar regras de deteção personalizadas com base na sua consulta e emergir essas insights como alertas para os seus incidentes de segurança.

 

## <a name="query-language"></a>Linguagem da consulta 

A caça em Azure Sentinel é baseada na linguagem de consulta de Kusto. Para mais informações sobre a linguagem de consulta e os operadores apoiados, consulte a [Referência da Linguagem](/azure/azure-monitor/log-query/get-started-queries)consulta.

## <a name="public-hunting-query-github-repository"></a>Consulta pública de caça GitHub repositório

Confira o [repositório](https://github.com/Azure/Orion)de consulta de caça. Contribuir e utilizar consultas de exemplo partilhadas pelos nossos clientes.

 

## <a name="sample-query"></a>Consulta de exemplo

Uma consulta típica começa com um nome de mesa \|seguido por uma série de operadores separados por .

No exemplo acima, comece com o nome de tabela SecurityEvent e adicione elementos canalizados conforme necessário.

1. Defina um filtro de tempo para rever apenas os registos dos sete dias anteriores.

2. Adicione um filtro na consulta para mostrar apenas o ID 4688 do evento.

3. Adicione um filtro na consulta na Linha de Comando para conter apenas as instâncias de cscript.exe.

4. Projete apenas as colunas que está interessado em explorar e limitar os resultados a 1000 e clique na **consulta de Executar**.
5. Clique no triângulo verde e execute a consulta. Pode testar a consulta e executá-la para procurar comportamentos anómalos.

## <a name="useful-operators"></a>Operadores úteis

A linguagem de consulta é poderosa e tem muitos operadores disponíveis, alguns operadores úteis estão listados aqui:

**onde** - Filtrar uma tabela para o subconjunto de linhas que satisfaçam um predicado.

**resumo** - Produzir uma tabela que agrega o conteúdo da tabela de entrada.

**aderir** - Fundir as linhas de duas tabelas para formar um novo quadro, combinando valores das colunas especificadas de cada tabela.

**contagem** - Devolva o número de registos no conjunto de registos de entrada.

**topo** - Devolva os primeiros registos N classificados pelas colunas especificadas.

**limite** - Volte ao número especificado de linhas.

**projeto** - Selecione as colunas para incluir, mudar o nome ou largar, e inserir novas colunas computadas.

**estender** - Criar colunas calculadas e acomodá-las ao conjunto de resultados.

**makeset** - Devolver uma gama dinâmica (JSON) do conjunto de valores distintos que a Expr leva no grupo

**encontrar** linhas que correspondam a um predicado através de um conjunto de mesas.

## <a name="save-a-query"></a>Poupe uma consulta

Pode criar ou modificar uma consulta e guardá-la como sua própria consulta ou partilhá-la com utilizadores que estejam no mesmo inquilino.

   ![Guardar consulta](./media/tutorial-hunting/save-query.png)

Criar uma nova consulta de caça:

1. Clique em **Nova consulta** e selecione **Guardar**.
2. Preencha todos os campos em branco e selecione **Guardar**.

   ![Nova consulta](./media/tutorial-hunting/new-query.png)

Clone e modifique uma consulta de caça existente:

1. Selecione a consulta de caça na tabela que pretende modificar.
2. Selecione a elipse (...) na linha da consulta que pretende modificar e selecione a **consulta clone**.

   ![consulta de clone](./media/tutorial-hunting/clone-query.png)
 

3. Modifique a consulta e selecione **Criar**.

   ![consulta personalizada](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a dirigir uma investigação de caça com Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:


- [Use cadernos para executar campanhas de caça automatizadas](notebooks.md)
- [Use marcadores para guardar informações interessantes durante a caça](bookmarks.md)
