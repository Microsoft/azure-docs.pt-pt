---
title: Introdução ao suporte de cadernos jupyter incorporado sintetizadores em Azure Cosmos DB (Pré-visualização)
description: Saiba como pode usar o suporte de cadernos Jupyter incorporado sinuoso em Azure Cosmos DB para executar consultas interativas.
ms.service: cosmos-db
ms.topic: overview
ms.date: 09/22/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 5b320485001d6cbc457d39ef193ed8c57f7161df
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76760288"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Suporte de cadernos jupyter incorporados em Azure Cosmos DB (pré-visualização)

O portátil Jupyter é uma aplicação web de código aberto que permite criar e partilhar documentos que contenham código vivo, equações, visualizações e texto narrativo. A Azure Cosmos DB suporta cadernos jupyter incorporados para todas as APIs como Cassandra, MongoDB, SQL, Gremlin e Table. O suporte portátil incorporado para todas as APIs DB do Azure Cosmos e modelos de dados permite-lhe executar consultas interativamente. Os cadernos Jupyter funcionam dentro das contas do Azure Cosmos e permitem aos desenvolvedores realizar a exploração de dados, limpeza de dados, transformações de dados, simulações numéricas, modelação estatística, visualização de dados e machine learning.

![Visualizações de cadernos jupyter em Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png)

Os cadernos Jupyter suportam funções mágicas que alargam as capacidades do núcleo suportando comandos adicionais. Cosmos magic é um comando que estende as capacidades do kernel Python no caderno Jupyter para que você possa executar consultas De API Azure Cosmos SQL além de Apache Spark. Você pode facilmente combinar consultas de Python e SQL API para consulta e visualização de dados usando bibliotecas de visualização ricas integradas com comandos render.
O portal Azure integra de forma nativa a experiência do portátil Jupyter nas contas da Azure Cosmos, como mostra a seguinte imagem:

![Suporte de cadernos jupyter em Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png)

## <a name="benefits-of-jupyter-notebooks"></a>Benefícios dos cadernos Jupyter

Os cadernos jupyter foram originalmente desenvolvidos para aplicações de ciência de dados escritas em Python, R. No entanto, podem ser utilizados de várias formas para diferentes tipos de projetos, tais como:

* ***Visualizações de dados:** Os cadernos jupyter permitem visualizar dados sob a forma de um caderno partilhado que torna alguns conjuntos de dados como um gráfico. O caderno jupyter permite visualizações de autor, partilhá-las e permitir alterações interativas no código e conjunto de dados partilhados.

* **Partilha de códigos:** Serviços como o GitHub fornecem formas de partilhar código, mas são em grande parte não interativos. Com um caderno Jupyter, pode ver código, executá-lo e exibir os resultados diretamente no portal Azure.

* **Interações ao vivo com código:** O código do caderno jupyter é dinâmico; pode ser editado e reexecutado incrementalmente em tempo real. Os cadernos também podem incorporar controlos de utilizadores (por exemplo, sliders ou campos de entrada de texto) que podem ser usados como fontes de entrada para código, demonstrações ou provas de conceitos (POCs).

* **Documentação de amostras de código e resultados da exploração de dados:** Se tiver um pedaço de código e quiser explicar line-by-line como funciona em Azure Cosmos DB, com saída em tempo real ao longo do caminho, você poderia inseri-lo em um Caderno Jupyter. O código permanecerá totalmente funcional. Pode adicionar interatividade juntamente com a documentação ao mesmo tempo.

* **Comandos mágicos cosmos:** Nos cadernos jupyter, você pode usar comandos de magia personalizados para Azure Cosmos DB para facilitar a computação interativa. Por exemplo, a magia %%sql que permite consultar um recipiente Cosmos usando API SQL diretamente em um caderno.

* **Tudo num só lugar ambiente:** Os cadernos jupyter combinam código, texto rico, imagens, vídeos, animações, equações matemáticas, enredos, mapas, figuras interativas, widgets e interfaces gráficas de utilizadores num único documento.

## <a name="components-of-a-jupyter-notebook"></a>Componentes de um caderno Jupyter

Os cadernos jupyter podem incluir vários tipos de componentes, cada um organizado em blocos discretos:

* **Texto e HTML:** O texto simples, ou texto anotado na sintaxe de marcação para gerar HTML, pode ser inserido no documento em qualquer ponto. O estilo CSS também pode ser incluído inline ou adicionado ao modelo utilizado para gerar o caderno.

* **Código e saída:** Os cadernos jupyter suportam o código Python. Os resultados do código executado aparecem imediatamente após os blocos de código, e os blocos de código podem ser executados várias vezes em qualquer ordem que você quiser.

* **Visualizações:** Gráficos e gráficos podem ser gerados a partir do código, usando módulos como Matplotlib, Plotly ou Bokeh. Semelhante à saída, estas visualizações aparecem inline ao lado do código que as gera.

* **Multimédia:** Como o portátil Jupyter é construído sobre a tecnologia web, pode exibir todos os tipos de multimédia suportados numa página web. Pode incluí-los num caderno como elementos HTML, ou pode `IPython.display` gerá-los programáticamente utilizando o módulo.

* **Dados:** Os dados dos contentores da Azure Cosmos e os resultados das consultas podem ser importados para um caderno Jupyter programáticamente. Por exemplo, ao incluir código no caderno para consultar os dados usando qualquer um dos APIs db cosmos ou apache spark nativo-incorporado.

## <a name="next-steps"></a>Passos seguintes

Para começar com cadernos jupyter embutidos em Azure Cosmos DB veja os seguintes artigos:

* [Ativar cadernos numa conta Azure Cosmos](enable-notebooks.md)
* [Utilize funcionalidades e comandos de cadernos](use-notebook-features-and-commands.md)



