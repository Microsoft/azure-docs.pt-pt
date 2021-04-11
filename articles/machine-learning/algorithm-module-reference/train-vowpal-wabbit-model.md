---
title: Train Vowpal Wabbit Model
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Modelo Wabbit Train Vowpal para criar um modelo de aprendizagem automática utilizando uma instância de Vowpal Wabbit.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 70d0fc456b3697e3c74a5ec45cc936a02b77e591
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104657659"
---
# <a name="train-vowpal-wabbit-model"></a>Train Vowpal Wabbit Model
Este artigo descreve como usar o módulo **Modelo Wabbit Train Vowpal** no designer Azure Machine Learning, para criar um modelo de aprendizagem automática utilizando o Vowpal Wabbit.  

Para utilizar o Vowpal Wabbit para machine learning, formate a sua entrada de acordo com os requisitos do Vowpal Wabbit e prepare os dados no formato necessário. Utilize este módulo para especificar os argumentos da linha de comando Vowpal Wabbit. 

Quando o gasoduto é executado, uma instância de Vowpal Wabbit é carregada no tempo de execução da experiência, juntamente com os dados especificados. Quando o treino está completo, o modelo é serializado de volta ao espaço de trabalho. Pode utilizar o modelo imediatamente para obter dados. 

Para formar gradualmente um modelo existente em novos dados, ligue um modelo guardado à porta de entrada do **modelo Vowpal Wabbit pré-treinado** do **Modelo De Trem Vowpal Wabbit**, e adicione os novos dados à outra porta de entrada.  

## <a name="what-is-vowpal-wabbit"></a>O que é Vowpal Wabbit?  

Vowpal Wabbit (VW) é uma estrutura de aprendizagem automática rápida e paralela que foi desenvolvida para computação distribuída pela Yahoo! A pesquisa. Mais tarde foi portador do Windows e adaptado por John Langford (Microsoft Research) para computação científica em arquiteturas paralelas.  

Características do Vowpal Wabbit que são importantes para o machine learning incluem aprendizagem contínua (aprendizagem on-line), redução de dimensionalidade e aprendizagem interativa. O Vowpal Wabbit também é uma solução para problemas quando não é possível encaixar os dados do modelo na memória.  

Os principais utilizadores do Vowpal Wabbit são cientistas de dados que já usaram o quadro para tarefas de aprendizagem automática, tais como classificação, regressão, modelação de tópicos ou factorização de matriz. O invólucro Azure para Vowpal Wabbit tem características de desempenho muito semelhantes à versão no local, para que possa utilizar as funcionalidades poderosas e desempenho nativo da Vowpal Wabbit, e facilmente publicar o modelo treinado como um serviço operacionalizado.  

O módulo [hashing de recurso](feature-hashing.md) também inclui a funcionalidade fornecida pela Vowpal Wabbit, que permite transformar conjuntos de dados de texto em funcionalidades binárias usando um algoritmo de hashing.  

## <a name="how-to-configure-vowpal-wabbit-model"></a>Como configurar o modelo Vowpal Wabbit  

Esta secção descreve como treinar um novo modelo e como adicionar novos dados a um modelo existente.

Ao contrário de outros módulos no designer, este módulo especifica os parâmetros do módulo e treina o modelo. Se tiver um modelo existente, pode adicioná-lo como uma entrada opcional, para treinar incrementalmente o modelo.

+ [Preparar dados de entrada num dos formatos necessários](#prepare-the-input-data)
+ [Treine um novo modelo](#create-and-train-a-vowpal-wabbit-model)
+ [Treina gradualmente um modelo existente](#retrain-an-existing-vowpal-wabbit-model)

### <a name="prepare-the-input-data"></a>Preparar os dados de entrada

Para formar um modelo utilizando este módulo, o conjunto de dados de entrada deve ser composto por uma única coluna de texto num dos dois formatos suportados: **SVMLight** ou **VW**. Isto não significa que o Vowpal Wabbit analise apenas os dados de texto, apenas que as funcionalidades e valores devem ser preparados no formato de ficheiro de texto necessário.  

Os dados podem ser lidos a partir de dois tipos de conjuntos de dados, conjunto de dados de ficheiros ou conjunto de dados tabulares. Ambos os conjuntos de dados devem ser indicados no formato SVMLight ou VW. O formato de dados Vowpal Wabbit tem a vantagem de não necessitar de um formato colunar, que economize espaço ao lidar com dados escassos. Para obter mais informações sobre este formato, consulte a [página wiki Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format).  

### <a name="create-and-train-a-vowpal-wabbit-model"></a>Criar e treinar um modelo Vowpal Wabbit

1. Adicione o módulo **modelo Train Vowpal Wabbit** à sua experiência. 
  
2. Adicione o conjunto de dados de formação e conecte-o aos **dados de Formação.** Se o conjunto de dados de formação for um diretório, que contém o ficheiro de dados de formação, especifique o nome do ficheiro de dados de formação com **o nome do ficheiro de dados de formação**. Se o conjunto de dados de formação for um único ficheiro, deixe **o nome do ficheiro de dados de formação** vazio.

3. Na caixa de texto de **argumentos VW,** digite os argumentos da linha de comando para o Vowpal Wabbit executável.

     Por exemplo, pode adicionar *`–l`* para especificar a taxa de aprendizagem, ou *`-b`* para indicar o número de bits de hashing.  

     Para mais informações, consulte a secção [de parâmetros Vowpal Wabbit.](#supported-and-unsupported-parameters)  

4. **Nome do ficheiro de dados de formação**: Digite o nome do ficheiro que contém os dados de entrada. Este argumento só é utilizado quando o conjunto de dados de formação é um diretório.

5. **Especificar o tipo de ficheiro**: Indicar qual o formato que os seus dados de treino utilizam. A Vowpal Wabbit suporta estes dois formatos de ficheiro de entrada:  

    - **A VW** representa o formato interno utilizado pela Vowpal Wabbit . Consulte a [página wiki do Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format) para obter mais detalhes. 
    - **SVMLight** é um formato utilizado por outras ferramentas de aprendizagem automática. 

6. **Ficheiro de modelo legível de saída**: selecione a opção se quiser que o módulo guarde o modelo legível para os registos de execução. Este argumento corresponde ao `--readable_model` parâmetro da linha de comando VW.  

7. **Ficheiro de haxixe invertido** de saída : selecione a opção se quiser que o módulo guarde a função de hashing invertida para um ficheiro nos registos de execução. Este argumento corresponde ao `--invert_hash` parâmetro da linha de comando VW.  

8. Envie o oleoduto.

### <a name="retrain-an-existing-vowpal-wabbit-model"></a>Retreine um modelo Vowpal Wabbit existente

A Vowpal Wabbit suporta a formação incremental adicionando novos dados a um modelo existente. Há duas formas de obter um modelo existente de reciclagem:

+ Utilize a saída de outro módulo **modelo de Wabbit Wabpal de comboio** no mesmo oleoduto.  
  
+ Localize um modelo guardado na categoria **Datasets** do painel de navegação esquerdo do designer e arraste-o para o seu oleoduto.  

1. Adicione o módulo **modelo Train Vowpal Wabbit** ao seu oleoduto.  
2. Ligue o modelo previamente treinado à porta de entrada **do modelo Vowpal Wabbit pré-treinado** do módulo.
3. Ligue os novos dados de formação à porta de entrada de dados de **formação** do módulo.
4. No painel de parâmetros do **Modelo Wabbit Do Comboio Vowpal,** especifique o formato dos novos dados de formação e também o nome do ficheiro de dados de formação se o conjunto de dados de entrada for um diretório.
5. Selecione o **ficheiro de modelo legível de saída** e opções de **ficheiros de hash invertidos** de saída se os ficheiros correspondentes precisarem de ser guardados nos registos de execução.

6. Envie o oleoduto.  
7. Selecione o módulo e **selecione Conjunto de dados de registo** no separador **Outputs+logs** no painel direito, para preservar o modelo atualizado no seu espaço de trabalho Azure Machine Learning.  Se não especificar um novo nome, o modelo atualizado substitui o modelo guardado existente.

## <a name="results"></a>Resultados

+ Para gerar pontuações a partir do modelo, utilize o [Modelo Wabbit Score Vowpal](score-vowpal-wabbit-model.md).

> [!NOTE]
> Se precisar de implementar o modelo treinado no designer, certifique-se de que o [Modelo Wabbit Score Vowpal](score-vowpal-wabbit-model.md) em vez do **Score Model** está ligado à entrada do módulo de saída de serviço [web](web-service-input-output.md) no pipeline de inferência.

## <a name="technical-notes"></a>Notas técnicas

Esta secção contém detalhes de implementação, dicas e respostas a perguntas frequentes.

### <a name="advantages-of-vowpal-wabbit"></a>Vantagens do Vowpal Wabbit

Vowpal Wabbit proporciona uma aprendizagem extremamente rápida sobre características não lineares como n-gramas.  

A Vowpal Wabbit utiliza técnicas *de aprendizagem on-line* como a descida do gradiente estocástico (SGD) para encaixar num modelo um recorde de cada vez. Assim, itera muito rapidamente sobre dados brutos e pode desenvolver um bom preditor mais rápido do que a maioria dos outros modelos. Esta abordagem também evita ter que ler todos os dados de treino na memória.  

Vowpal Wabbit converte todos os dados em hashes, não apenas dados de texto, mas outras variáveis categóricas. A utilização de hashes torna a procura de pesos de regressão mais eficientes, o que é fundamental para uma descida eficaz do gradiente estocástico.  

###  <a name="supported-and-unsupported-parameters"></a>Parâmetros suportados e não suportados 

Esta secção descreve o suporte para parâmetros da linha de comando Vowpal Wabbit no designer de Aprendizagem automática Azure. 

Geralmente, todos, menos um conjunto limitado de argumentos são apoiados. Para obter uma lista completa de argumentos, utilize a [página wiki Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).    

Não são suportados os seguintes parâmetros:

-   As opções de entrada/saída especificadas em [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     Estas propriedades já estão configuradas automaticamente pelo módulo.  
  
-   Além disso, qualquer opção que gere várias saídas ou tome várias entradas é proibida. Estes incluem *`--cbt`* *`--lda`* , e *`--wap`* .  
  
-   Apenas os algoritmos de aprendizagem supervisionados são suportados. Por conseguinte, estas opções não são apoiadas: *`–active`* `--rank` , *`--search`* etc. 

### <a name="restrictions"></a>Restrições

Uma vez que o objetivo do serviço é apoiar utilizadores experientes do Vowpal Wabbit, os dados de entrada devem ser preparados com antecedência utilizando o formato de texto nativo Vowpal Wabbit, em vez do formato de conjunto de dados utilizado por outros módulos.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
