---
title: Introdução ao suporte de cadernos Jupyter incorporados em Azure Cosmos DB (Preview)
description: Saiba como pode usar o suporte de Cadernos Jupyter incorporados em Azure Cosmos DB para executar consultas interativas.
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/19/2020
author: deborahc
ms.author: dech
ms.openlocfilehash: 8dca9e3e29796618b905c4d266eb674f82565969
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097622"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Suporte de cadernos Jupyter embutidos em Azure Cosmos DB (pré-visualização)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Jupyter Notebook é uma aplicação Web de open source que lhe permite criar e partilhar documentos que contenham código dinâmico, equações, visualizações e texto narrativo. 

Os Cadernos Jupyter embutidos em Azure Cosmos estão diretamente integrados no portal Azure e nas suas contas DB Azure Cosmos, tornando-as convenientes e fáceis de usar. Desenvolvedores, cientistas de dados, engenheiros e analistas podem usar a experiência familiar de Jupyter Notebooks para fazer a exploração de dados, limpeza de dados, transformações de dados, simulações numéricas, modelação estatística, visualização de dados e aprendizagem automática.

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png" alt-text="Jupyter Notebooks visualizações em Azure Cosmos DB":::

Azure Cosmos DB suporta os cadernos C# e Python para todas as APIs, incluindo Core (SQL), Cassandra, Gremlin, Table e API para o MongoDB. Dentro do caderno, pode aproveitar comandos e funcionalidades incorporadas que facilitam a criação de recursos DB do Azure Cosmos, upload de dados e consulta e visualização dos seus dados em Azure Cosmos DB. 

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png" alt-text="Jupyter Notebooks visualizações em Azure Cosmos DB":::

## <a name="benefits-of-jupyter-notebooks"></a>Benefícios dos Cadernos Jupyter

Os Cadernos Jupyter foram originalmente desenvolvidos para aplicações de ciência de dados escritas em Python e R. No entanto, podem ser utilizados de várias formas para diferentes tipos de projetos, incluindo:

**Visualização de dados:** Os Cadernos Jupyter permitem visualizar dados sob a forma de um caderno partilhado que torna um conjunto de dados como um gráfico. Pode criar visualizações, fazer alterações interativas no código partilhado e no conjunto de dados e partilhar os resultados.

**Partilha de códigos:** Serviços como o GitHub fornecem formas de partilhar código, mas são em grande parte não interativos. Com um Caderno Jupyter, pode ver código, executá-lo e exibir os resultados diretamente no portal Azure.

**Interações ao vivo com código:** Código num Caderno Jupyter é dinâmico; pode editá-lo e executar as atualizações incrementalmente em tempo real. Também pode incorporar controlos de utilizador (por exemplo, sliders ou campos de entrada de texto) que são utilizados como fontes de entrada para código, demos ou Prova de Conceitos (POCs).

**Documentação de amostras de código e resultados da exploração de dados:** Se tiver um código e quiser explicar linha a linha como funciona, pode inseri-lo num Caderno Jupyter. Pode adicionar interatividade juntamente com a documentação ao mesmo tempo.

**Comandos incorporados para Azure Cosmos DB:** Os comandos mágicos incorporados da Azure Cosmos DB facilitam a interação com a sua conta. Pode utilizar comandos como %% upload e %%sql para carregar dados num recipiente e questioná-lo utilizando [a sintaxe API SQL](sql-query-getting-started.md). Não precisa escrever um código personalizado adicional.

**Tudo num ambiente de um só lugar:** Os Cadernos Jupyter combinam código, texto rico, imagens, vídeos, animações, equações matemáticas, enredos, mapas, figuras interativas, widgets e interfaces gráficas de utilizador num único documento.

## <a name="components-of-a-jupyter-notebook"></a>Componentes de um caderno Jupyter

Os Cadernos Jupyter podem incluir vários tipos de componentes, cada um organizado em blocos ou células discretas:

**Texto e HTML:** O texto simples, ou texto anotado na sintaxe de redução para gerar HTML, pode ser inserido no documento em qualquer ponto. O estilo CSS também pode ser incluído em linha ou adicionado ao modelo utilizado para gerar o caderno.

**Código e saída:** Os Cadernos Jupyter suportam o código Python e C#. Os resultados do código executado aparecem imediatamente após os blocos de código, e os blocos de código podem ser executados várias vezes em qualquer ordem que quiser.

**Visualizações:** Pode gerar gráficos e gráficos a partir do código usando módulos como Matplotlib, Plotly, Bokeh, entre outros. Semelhantes à saída, estas visualizações aparecem em linha ao lado do código que as gera. Semelhantes à saída, estas visualizações aparecem em linha ao lado do código que as gera.

**Multimédia:** Como os Jupyter Notebooks são construídos com tecnologia web, podem exibir todos os tipos de multimédia suportados por uma página web. Pode incluí-los num caderno como elementos HTML, ou pode gere-los programáticamente utilizando o `IPython.display` módulo.

**Dados:** Você pode importar os dados de recipientes Azure Cosmos ou os resultados de consultas em um programa de Caderno Jupyter programático. Utilize comandos mágicos incorporados para carregar ou consultar dados em Azure Cosmos DB. 

## <a name="next-steps"></a>Passos seguintes

Para começar com cadernos Jupyter embutidos em Azure Cosmos DB, consulte os seguintes artigos:

* [Ativar cadernos numa conta da Azure Cosmos](enable-notebooks.md)
* [Use funcionalidades e comandos de caderno Python](use-python-notebook-features-and-commands.md)
* [Use funcionalidades e comandos de caderno C#](use-csharp-notebook-features-and-commands.md)
