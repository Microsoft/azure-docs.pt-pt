---
title: Introdução ao suporte de cadernos jupyter incorporado sintetizadores em Azure Cosmos DB (Pré-visualização)
description: Saiba como pode usar o suporte de cadernos Jupyter incorporado sinuoso em Azure Cosmos DB para executar consultas interativas.
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/19/2020
author: deborahc
ms.author: dech
ms.openlocfilehash: 9152bdfa9575738c853521814938cd0d7ec25efb
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657371"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Suporte de cadernos jupyter incorporados em Azure Cosmos DB (pré-visualização)

O portátil Jupyter é uma aplicação web de código aberto que permite criar e partilhar documentos que contenham código vivo, equações, visualizações e texto narrativo. 

Os cadernos Jupyter integrados azure Cosmos DB estão diretamente integrados no portal Azure e nas suas contas Azure Cosmos DB, tornando-os convenientes e fáceis de usar. Desenvolvedores, cientistas de dados, engenheiros e analistas podem usar a experiência familiar de cadernos Jupyter para fazer a exploração de dados, limpeza de dados, transformações de dados, simulações numéricas, modelação estatística, visualização de dados e machine learning.

![Visualizações de cadernos jupyter em Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png)

Azure Cosmos DB suporta os cadernos C# e Python para todas as APIs, incluindo Core (SQL), Cassandra, Gremlin, Table e API para MongoDB. Dentro do caderno, você pode aproveitar os comandos e funcionalidades incorporados que facilitam a criação de recursos Db Da Azure Cosmos, fazer upload de dados e consultar e visualizar os seus dados em Azure Cosmos DB. 

![Suporte de cadernos jupyter em Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png)

## <a name="benefits-of-jupyter-notebooks"></a>Benefícios dos cadernos Jupyter

Os cadernos jupyter foram originalmente desenvolvidos para aplicações de ciência de dados escritas em Python e R. No entanto, podem ser utilizados de várias formas para diferentes tipos de projetos, incluindo:

**Visualização de dados:** Os cadernos jupyter permitem visualizar dados sob a forma de um caderno partilhado que torna um conjunto de dados como um gráfico. Pode criar visualizações, fazer alterações interativas no código partilhado e conjunto de dados e partilhar os resultados.

**Partilha de códigos:** Serviços como o GitHub fornecem formas de partilhar código, mas são em grande parte não interativos. Com um caderno Jupyter, pode ver código, executá-lo e exibir os resultados diretamente no portal Azure.

**Interações ao vivo com código:** O código num caderno jupyter é dinâmico; pode editá-lo e executar as atualizações de forma incremental em tempo real. Também pode incorporar os controlos dos utilizadores (por exemplo, sliders ou campos de entrada de texto) que são usados como fontes de entrada para código, demonstrações ou Provas de Conceitos (POCs).

**Documentação de amostras de código e resultados da exploração de dados:** Se tiver um pedaço de código e quiser explicar line-by-line como funciona, pode inseri-lo num Caderno Jupyter. Pode adicionar interatividade juntamente com a documentação ao mesmo tempo.

**Comandos incorporados para Azure Cosmos DB:** Os comandos mágicos incorporados da Azure Cosmos DB facilitam a interação com a sua conta. Pode utilizar comandos como %%upload e %%sql para carregar dados num recipiente e questioná-lo usando [sintaxe SQL API](sql-query-getting-started.md). Não precisas de escrever um código personalizado adicional.

**Tudo num só lugar ambiente:** Os cadernos jupyter combinam código, texto rico, imagens, vídeos, animações, equações matemáticas, enredos, mapas, figuras interativas, widgets e interfaces gráficas de utilizadores num único documento.

## <a name="components-of-a-jupyter-notebook"></a>Componentes de um caderno Jupyter

Os cadernos jupyter podem incluir vários tipos de componentes, cada um organizado em blocos ou células discretos:

**Texto e HTML:** O texto simples, ou texto anotado na sintaxe de marcação para gerar HTML, pode ser inserido no documento em qualquer ponto. O estilo CSS também pode ser incluído inline ou adicionado ao modelo utilizado para gerar o caderno.

**Código e saída:** Os cadernos jupyter suportam o código Python e C# Os resultados do código executado aparecem imediatamente após os blocos de código, e os blocos de código podem ser executados várias vezes em qualquer ordem que você quiser.

**Visualizações:** Você pode gerar gráficos e gráficos a partir do código usando módulos como Matplotlib, Plotly, Bokeh, e outros. Semelhante à saída, estas visualizações aparecem inline ao lado do código que as gera. Semelhante à saída, estas visualizações aparecem inline ao lado do código que as gera.

**Multimédia:** Como os cadernos Jupyter são construídos sobre tecnologia web, eles podem exibir todos os tipos de multimédia suportados por uma página web. Pode incluí-los num caderno como elementos HTML, ou pode gerá-los programáticamente utilizando o `IPython.display` módulo.

**Dados:** Pode importar os dados dos contentores Azure Cosmos ou os resultados das consultas num caderno jupyter programáticamente. Utilize comandos mágicos incorporados para carregar ou consultar dados em Azure Cosmos DB. 

## <a name="next-steps"></a>Próximos passos

Para começar com cadernos jupyter embutidos em Azure Cosmos DB, consulte os seguintes artigos:

* [Ativar cadernos numa conta Azure Cosmos](enable-notebooks.md)
* [Use funcionalidades e comandos de caderno Python](use-python-notebook-features-and-commands.md)
* [Utilize funcionalidades e comandos de caderno C#](use-csharp-notebook-features-and-commands.md)