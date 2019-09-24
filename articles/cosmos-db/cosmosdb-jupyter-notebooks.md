---
title: Introdução ao suporte do Jupyter notebooks interno no Azure Cosmos DB
description: Saiba como você pode usar o suporte interno do Jupyter notebooks no Azure Cosmos DB para executar consultas interativamente.
ms.service: cosmos-db
ms.topic: overview
ms.date: 09/22/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: 57df9e0bd6f359a45333fcde51c51f49321fec9e
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213881"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db"></a>Suporte interno a notebooks Jupyter no Azure Cosmos DB

O Jupyter Notebook é um aplicativo Web de software livre que permite criar e compartilhar documentos que contêm código ao vivo, equações, visualizações e texto de narração. O Azure Cosmos DB dá suporte a blocos de anotações Jupyter internos para todas as APIs, como Cassandra, MongoDB, SQL, Gremlin e tabela. O suporte ao bloco de anotações interno para todos os Azure Cosmos DB APIs e modelos de dados permite executar consultas interativamente. Os notebooks Jupyter são executados nas contas do Azure Cosmos e permitem que os desenvolvedores realizem exploração de dados, limpeza de dados, transformações de dados, simulações numéricas, modelagem estatística, visualização de dados e aprendizado de máquina.

Os notebooks Jupyter dão suporte a funções mágicas que estendem os recursos do kernel ao dar suporte a comandos adicionais. Cosmos Magic é um comando que estende os recursos do kernel do Python no notebook Jupyter para que você possa executar consultas da API do SQL do Azure Cosmos, além de Apache Spark. Você pode combinar facilmente consultas de API do Python e do SQL para consultar e Visualizar dados usando bibliotecas de visualização avançadas integradas com comandos de renderização.
Portal do Azure integra nativamente a experiência do Jupyter Notebook às contas do Azure Cosmos, conforme mostrado na imagem a seguir:

![Suporte a notebooks Jupyter no Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png)

## <a name="benefits-of-jupyter-notebooks"></a>Benefícios dos Notebooks Jupyter

Os notebooks Jupyter foram desenvolvidos originalmente para aplicativos de ciência de dados escritos em Python, R. No entanto, eles podem ser usados de várias maneiras para diferentes tipos de projetos, como:

* ***Visualizações de dados:** Os notebooks Jupyter permitem visualizar dados na forma de um bloco de anotações compartilhado que processa alguns conjuntos de dados como um gráfico. O Jupyter Notebook permite que você crie visualizações, compartilhe-as e permita alterações interativas no código compartilhado e no conjunto de dados.

* **Compartilhamento de código:** Serviços como o GitHub fornecem maneiras de compartilhar código, mas eles são amplamente não interativos. Com um notebook Jupyter, você pode exibir o código, executá-lo e exibir os resultados diretamente no portal do Azure.

* **Interações ao vivo com código:** O código do bloco de anotações Jupyter é dinâmico; Ele pode ser editado e executado novamente de forma incremental em tempo real. Os blocos de anotações também podem inserir controles de usuário (por exemplo, seletores ou campos de entrada de texto) que podem ser usados como fontes de entrada para código, demonstrações ou provas de conceitos (POCs).

* **Documentação de exemplos de código e resultados de exploração de dados:** Se você tiver um trecho de código e desejar explicar como ele funciona em Azure Cosmos DB, com saída em tempo real, tudo ao longo do caminho, você poderá incorporá-lo em um Jupyter Notebook. O código permanecerá totalmente funcional. Você pode adicionar interatividade junto com a documentação ao mesmo tempo.

* **Comandos mágicos do Cosmos:** Nos notebooks Jupyter, você pode usar comandos mágicos personalizados para Azure Cosmos DB para facilitar a computação interativa. Por exemplo, a mágica%% SQL que permite consultar um contêiner Cosmos usando a API do SQL diretamente em um notebook.

* **Tudo em um ambiente único local:** Os notebooks Jupyter combinam código, Rich Text, imagens, vídeos, animações, equações matemáticas, plotagens, mapas, figuras interativas, widgets e interfaces gráficas do usuário em um único documento.

## <a name="components-of-a-jupyter-notebook"></a>Componentes de um notebook Jupyter

Os notebooks Jupyter podem incluir vários tipos de componentes, cada um organizado em blocos discretos:

* **Texto e HTML:** Texto sem formatação ou texto anotado na sintaxe de redução para gerar HTML, pode ser inserido no documento a qualquer momento. O estilo de CSS também pode ser incluído embutido ou adicionado ao modelo usado para gerar o bloco de anotações.

* **Código e saída:** Os notebooks Jupyter dão suporte ao código Python. Os resultados do código executado aparecem imediatamente após os blocos de código, e os blocos de código podem ser executados várias vezes em qualquer ordem desejada.

* **Visualizações** Elementos gráficos e gráficos podem ser gerados a partir do código, usando módulos como matplotlib, Plotal ou bokeh. Semelhante à saída, essas visualizações aparecem embutidas ao lado do código que as gera.

* **Média** Como o Jupyter Notebook é criado na tecnologia da Web, ele pode exibir todos os tipos de multimídia com suporte em uma página da Web. Você pode incluí-los em um bloco de anotações como elementos HTML ou pode gerá-los programaticamente usando o `IPython.display` módulo.

* **Dado** Os dados dos contêineres Cosmos do Azure e os resultados das consultas podem ser importados em um notebook Jupyter de forma programática. Por exemplo, ao incluir o código no bloco de anotações para consultar os dados usando qualquer uma das APIs de Cosmos DB ou Apache Spark interno nativo.

## <a name="next-steps"></a>Passos seguintes

Para começar a usar o Jupyter notebooks internos no Azure Cosmos DB consulte os seguintes artigos:

* [Como criar um bloco de anotações]()
* [Como instalar dependências em um bloco de anotações]()
* [Como usar um bloco de anotações e uma extensão mágica do cosmos para analisar dados]()



