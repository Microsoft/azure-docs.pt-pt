---
title: Capacidades de procura na pré-visualização de sentinela de Azure | Documentos da Microsoft
description: Este artigo descreve como utilizar as capacidades de buscar sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 24792a0099d4e45e100fa6cf0b211c62fc8630de
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242331"
---
# <a name="hunt-for-threats-with-in-azure-sentinel-preview"></a>Busca de ameaças com sentinela na pré-visualização no Azure

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se for um investigador que deseja seja proativo quanto à procura de ameaças de segurança do Azure sentinela a ferramentas de pesquisa e consulta de arquivo terá poderosas para ameaças de segurança entre origens de dados da sua organização. Mas Montanhas de dados que podem ser difíceis de analisar e filtrar em eventos significativos de gerar seus sistemas e aplicações de segurança. Para ajudar a segurança analistas procurar proativamente anomalias novas que não foram detetadas por seus aplicativos de segurança do Azure sentinela "consultas de detecção internos guiá-lo para fazer as perguntas certas para localizar problemas nos dados já tem na sua rede. 

Por exemplo, uma consulta incorporada fornece dados sobre os processos mais incomuns em execução na sua infraestrutura – não iria querer que um alerta sobre sempre que forem executadas, poderiam ser inteiramente inofensivo, mas talvez queira dar uma olhada na consulta ocasionalmente para ver se th ERE's algo invulgar. 



Com sorte sentinela do Azure, pode tirar partido dos recursos a seguir:

- Consultas internas: Para começar, uma página inicial fornece exemplos de consulta pré-carregada projetados para ajudá-lo a utilizar e se familiarizar com as tabelas e a linguagem de consulta. Estas consultas de detecção internos são desenvolvidas pelos pesquisadores de segurança Microsoft numa base contínua, adicionar novas consultas, e a consulta com o ajuste fino existente para lhe fornecer um ponto de entrada para procurar novas deteções e descobrir onde começar a buscar o início do desenvolvimento da novos ataques. 

- Linguagem de consulta poderosa com o IntelliSense: Criada sobre uma linguagem de consulta que lhe dá a flexibilidade que necessária para tirar o arquivo terá para o próximo nível.

- Crie seus próprios indicadores: Durante o processo de detecção, o que pode encontrar as correspondências ou descobertas, dashboards ou atividades que são invulgares ou suspeitas. Para marcar os itens para que pode voltar a eles no futuro, utilize a funcionalidade de marcador. Indicadores permitem-lhe guardar itens para utilizar mais tarde, a ser utilizado para criar um pedido para a investigação. Para obter mais informações sobre marcadores, consulte utilização [marcadores no sorte!].

- Utilize blocos de notas para automatizar a investigação: São blocos de notas como playbooks passo a passo que pode criar para percorrer os passos de uma investigação e hunt.  Blocos de notas encapsulam todas as etapas de buscar um playbook reutilizável que podem ser partilhados com outras pessoas na sua organização. 
- Consulte os dados armazenados: Os dados estão acessíveis nas tabelas para que possa consultar. Por exemplo, pode consultar o processo de criação, eventos DNS e muitos outros tipos de evento.

- Ligações à Comunidade: Tire partido do poder da Comunidade do maior ao localizar as origens de dados e consultas adicionais.
 
## <a name="get-started-hunting"></a>Introdução ao caçar

1. No portal do Azure sentinela, clique em **Hunting**.
  ![Azure Sentinel começa sorte!](media/tutorial-hunting/hunting-start.png)

2. Quando abre o **Hunting** página, todas as consultas de buscar são exibidas numa única tabela. A tabela apresenta uma lista de todas as consultas de escrita pela equipe da Microsoft de analistas de segurança, bem como qualquer consulta adicional criado ou modificado. Cada consulta fornece uma descrição de ele hunts para e que tipo de dados é executada no. Estes modelos são agrupados por suas várias táticas - os ícones no lado direito categorizar o tipo de ameaça, como o acesso inicial, persistência e filtragem. Pode filtrar estes modelos de consulta de arquivo terá usando qualquer um dos campos. Pode salvar qualquer consulta aos seus favoritos. Ao guardar os seus favoritos uma consulta, a consulta é executada automaticamente sempre que o **Hunting** página for acessada. Pode criar sua própria consulta de arquivo terá ou um clone e personalizar um modelo de consulta de arquivo terá existente. 
 
2. Clique em **executar consulta** no arquivo terá página Detalhes de consulta para executar consultas sem sair da página de procura.  O número de correspondências é apresentado na tabela. Reveja a lista de consultas de buscar e suas correspondências. Veja qual estágio da cadeia de eliminação a correspondência é associada.

3. Executar uma revisão rápida da consulta subjacente no painel de detalhes de consulta ou clique em **ver o resultado da consulta** para abrir a consulta do Log Analytics. Na parte inferior, reveja as correspondências para a consulta.

4.  Clique na linha e selecione **Adicionar indicador** para adicionar as linhas a ser investigado - pode fazê-lo para tudo o que parecer suspeito. 

5. Em seguida, volte para os principais **Hunting** página e clique no **indicadores** separador para ver todas as atividades suspeitas. 

6. Selecione um marcador e, em seguida, clique em **investigar** para abrir a experiência de investigação. Pode filtrar os indicadores. Por exemplo, se estiver a investigar uma campanha, pode criar uma etiqueta para a campanha e, em seguida, filtrar todos os indicadores com base na campanha.

1. Depois de descobriu que consulta de arquivo terá fornece informações de alto valor sobre possíveis ataques, também pode criar uma detecção personalizadas regras com base na sua consulta e aborde essas perspectivas como alertas para os respondentes de incidentes de segurança.

 

## <a name="query-language"></a>Linguagem da consulta 

Arquivo terá no Azure sentinela baseia-se a linguagem de consulta do Log Analytics do Azure. Para obter mais informações sobre a linguagem de consulta e os operadores suportados, consulte [referência de linguagem de consulta](https://docs.loganalytics.io/docs/Language-Reference/).

## <a name="public-hunting-query-github-repository"></a>Repositório do GitHub público buscar consulta

Veja a [repositório de consulta de arquivo terá](https://github.com/Azure/Orion). Contribuir e utilizar consultas de exemplo partilhadas por nossos clientes.

 

## <a name="sample-query"></a>Consulta de exemplo

Uma consulta típica começa com um nome de tabela, seguido de uma série de operadores, separados por \|.

No exemplo acima, começar com a tabela nomeie SecurityEvent e adicionar elementos de pipe, conforme necessário.

1. Define um filtro de tempo para rever apenas os registos dos últimos sete dias.

2. Adicione um filtro na consulta para mostrar apenas o evento ID 4688.

3. Adicione um filtro na consulta na linha de comandos para conter apenas instâncias do cscript.exe.

4. Apenas as colunas que está interessado em explorar e limita os resultados a 1000 e clique em do projeto **executar consulta**.
5. Clique no triângulo verde e execute a consulta. Pode testar a consulta e execute-o para procurar o comportamento anómalo.

## <a name="useful-operators"></a>Operadores úteis

A linguagem de consulta é poderosa e tem muitos operadores disponíveis, alguns operadores útil estão listados aqui:

**onde** -uma tabela para o subconjunto de linhas que satisfazem um predicado de filtro.

**resumir** -produzir uma tabela que agrega o conteúdo da tabela de entrada.

**associação** -unindo as linhas das duas tabelas para formar uma nova tabela ao corresponder valores das colunas especificadas de cada tabela.

**Contagem de** -devolver o número de registos no conjunto de registo de entrada.

**parte superior** -registos de devolver o primeiro N ordenados pelas colunas especificadas.

**limite** -retornar até o número especificado de linhas.

**projeto** - Selecione as colunas a incluir, mudar o nome ou remova e insira novas colunas calculadas.

**expandir** - criar colunas calculadas e anexe-os para o conjunto de resultados.

**makeset** -retornar uma matriz (JSON) dinâmica do conjunto de valores distintos, que usa Expr no grupo

**encontrar** -determinar linhas que correspondem a um predicado num conjunto de tabelas.

## <a name="save-a-query"></a>Guardar uma consulta

Pode criar ou modificar uma consulta e salve-o como sua própria consulta ou partilhá-lo com os utilizadores que estão no mesmo inquilino.

   ![Guardar consulta](./media/tutorial-hunting/save-query.png)

Crie uma nova consulta de procura:

1. Clique em **nova consulta** e selecione **guardar**.
2. Preencha todos os campos em branco e selecione **guardar**.

 ![Nova consulta](./media/tutorial-hunting/new-query.png)

Clonar e modificar uma consulta de arquivo terá existente:

1. Selecione a consulta de procura na tabela que pretende modificar.
2. Selecione as reticências (...) na linha da consulta que pretende modificar e selecione **clonar consulta**.

 ![Clonar consulta](./media/tutorial-hunting/clone-query.png)
 

3.  Modificar a consulta e selecione **criar**.

 ![consulta personalizada](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a executar uma investigação de detecção com sentinela do Azure. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:


- [Utilizar blocos de notas para executar as campanhas de detecção automática](notebooks.md)
- [Utilizar marcadores para salvar informações interessantes ao sorte!](bookmarks.md)