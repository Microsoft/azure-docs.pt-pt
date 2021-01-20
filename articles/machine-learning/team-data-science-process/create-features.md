---
title: Engenharia de recursos em machine learning - Processo de Ciência de Dados de Equipa
description: Conheça a engenharia de recursos e o seu papel no processo de melhoria de dados de machine learning.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: conceptual
ms.date: 05/14/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath, contperf-fy20q4
ms.openlocfilehash: b20a6744644678879fedf44e960854f558eb0f03
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610442"
---
# <a name="feature-engineering-in-machine-learning"></a>Engenharia de recursos em machine learning

Neste artigo, aprende-se sobre engenharia de recursos e o seu papel na melhoria dos dados na aprendizagem automática. Aprenda com exemplos ilustrativos retirados de experiências [do Azure Machine Learning Studio (clássico).](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio) 

* **Engenharia de recursos**: O processo de criação de novas funcionalidades a partir de dados brutos para aumentar o poder preditivo do algoritmo de aprendizagem. As funcionalidades concebidas devem capturar informações adicionais que não sejam facilmente visíveis no conjunto de funcionalidades originais.
* **Seleção de recursos**: O processo de seleção do subconjunto chave das funcionalidades para reduzir a dimensionalidade do problema de treino.

**Normalmente, a engenharia de recursos** é aplicada primeiro para gerar funcionalidades adicionais, e então a **seleção de recursos** é feita para eliminar características irrelevantes, redundantes ou altamente correlacionadas.

A engenharia e seleção de recursos fazem parte da fase de [modelação](lifecycle-modeling.md) do Processo de Ciência de Dados de Equipa (TDSP). Para saber mais sobre o TDSP e o ciclo de vida da ciência dos dados, veja [o que é o TDSP?](overview.md)

## <a name="what-is-feature-engineering"></a>O que é engenharia de recursos?

Os dados de formação consistem numa matriz composta por linhas e colunas. Cada linha na matriz é uma observação ou registo. As colunas de cada linha são as características que descrevem cada registo. As características especificadas no desenho experimental devem caracterizar os padrões nos dados.

Embora muitos dos campos de dados brutos possam ser usados diretamente para treinar um modelo, é muitas vezes necessário criar funcionalidades adicionais (projetadas) para um conjunto de dados de treino melhorado.

As funcionalidades de engenharia que melhoram a formação fornecem informações que melhor diferenciam os padrões dos dados. Mas este processo é uma espécie de arte. Decisões sãs e produtivas requerem frequentemente conhecimentos de domínio.

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Exemplo 1: Adicionar características temporais para um modelo de regressão

Vamos usar a experiência [Demand previsão de aluguer de bicicletas](https://gallery.azure.ai/Experiment/Regression-Demand-estimation-4) no Azure Machine Learning Studio (clássico) para demonstrar como projetar funcionalidades para uma tarefa de regressão. O objetivo desta experiência é prever a procura de aluguer de bicicletas dentro de um mês/dia/hora específico.

### <a name="bike-rental-dataset"></a>Conjunto de dados de aluguer de bicicletas

O [conjunto de dados da UCI de Aluguer de Bicicletas](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset/) baseia-se em dados reais de uma empresa de partilha de bicicletas sediada nos Estados Unidos. Representa o número de alugueres de bicicletas dentro de uma hora específica de um dia para os anos 2011 e 2012. Contém 17.379 linhas e 17 colunas.

O conjunto de características brutas contém condições meteorológicas (temperatura/humidade/velocidade do vento) e o tipo de dia (feriado/dia de semana). O campo a prever é a contagem, que representa o aluguer de bicicletas dentro de uma hora específica. A contagem varia de 1 a 977.

### <a name="create-a-feature-engineering-experiment"></a>Criar uma experiência de engenharia de recursos

Com o objetivo de construir funcionalidades eficazes nos dados de treino, quatro modelos de regressão são construídos usando o mesmo algoritmo, mas com quatro conjuntos de dados de treino diferentes. Os quatro conjuntos de dados representam os mesmos dados de entrada bruta, mas com um número crescente de funcionalidades definidas. Estas características são agrupadas em quatro categorias:

1. A = tempo + feriado + dia da semana + recursos de fim de semana para o dia previsto
2. B = número de bicicletas alugadas em cada uma das 12 horas anteriores
3. C = número de bicicletas alugadas em cada um dos 12 dias anteriores à mesma hora
4. D = número de bicicletas que foram alugadas em cada uma das 12 semanas anteriores à mesma hora e no mesmo dia

Além do conjunto de funcionalidades A, que já existe nos dados brutos originais, os outros três conjuntos de funcionalidades são criados através do processo de engenharia de funcionalidades. O conjunto de recursos B capta a procura recente das bicicletas. O conjunto de recursos C capta a procura de bicicletas a uma determinada hora. O conjunto de recursos D capta a procura de bicicletas em determinada hora e dia particular da semana. Os quatro conjuntos de dados de treino incluem os conjuntos de recursos A, A+B, A+B+C e A+B+C+D, respectivamente.

### <a name="feature-engineering-using-studio-classic"></a>Engenharia de recursos usando Studio (clássico)

Na experiência Studio (clássica), estes quatro conjuntos de dados de treino são formados através de quatro ramos do conjunto de dados de entrada pré-processado. Com exceção do ramo mais à esquerda, cada um destes ramos contém um módulo [executivo de script r,](/azure/machine-learning/studio-module-reference/execute-r-script) no qual as características derivadas (conjunto de funcionalidades B, C e D) são construídas e anexadas ao conjunto de dados importado.

A figura a seguir demonstra o script R utilizado para criar o conjunto de funcionalidades B no segundo ramo esquerdo.

![criar funcionalidades](./media/create-features/addFeature-Rscripts.png)

### <a name="results"></a>Resultados

Uma comparação dos resultados de desempenho dos quatro modelos é resumida no quadro seguinte: 

![comparação de resultados](./media/create-features/result1.png)

Os melhores resultados são mostrados pelas características A+B+C. A taxa de erro diminui quando o conjunto adicional de recursos está incluído nos dados de formação. Verifica a presunção de que o conjunto de funcionalidades B, C fornece informações relevantes adicionais para a tarefa de regressão. Mas adicionar a funcionalidade D não parece fornecer qualquer redução adicional na taxa de erro.

## <a name="example-2-create-features-for-text-mining"></a><a name="example2"></a> Exemplo 2: Criar funcionalidades para a mineração de texto

A engenharia de recursos é amplamente aplicada em tarefas relacionadas com a mineração de texto, tais como classificação de documentos e análise de sentimento. Uma vez que peças individuais de texto bruto geralmente servem como dados de entrada, o processo de engenharia de recursos é necessário para criar as características que envolvem frequências de palavras/frases.

### <a name="feature-hashing"></a>Hashing de recurso

Para realizar esta tarefa, uma técnica chamada [hashing de recurso](/azure/machine-learning/studio-module-reference/feature-hashing) é aplicada para transformar eficientemente funcionalidades de texto arbitrárias em índices. Em vez de associar cada recurso de texto (palavras/frases) a um determinado índice, este método aplica uma função de haxixe às características e utiliza os seus valores de haxixe como índices diretamente.

No Studio (clássico), existe um módulo [de Hashing de Recurso](/azure/machine-learning/studio-module-reference/feature-hashing) que cria funcionalidades de palavras/frases convenientemente. A figura que se segue mostra um exemplo de utilização deste módulo. O conjunto de dados de entrada contém duas colunas: a classificação do livro que varia de 1 a 5 e o conteúdo de revisão real. O objetivo deste módulo é recuperar um conjunto de novas funcionalidades que mostrem a frequência de ocorrência das palavras(s) correspondentes dentro da revisão de livros em particular. Para utilizar este módulo, complete os seguintes passos:

* Em primeiro lugar, selecione a coluna que contém o texto de entrada ("Col2" neste exemplo).
* Em segundo lugar, definir o "bitsize de hashing" para 8, o que significa que serão criadas 2^8=256 funcionalidades. A palavra/fase em todo o texto será hashed a 256 índices. O parâmetro "Hashing bitsize" varia de 1 a 31. A palavra(s) /frase(s) é menos provável de ser hashed no mesmo índice se a definição de um número maior.
* Em terceiro lugar, coloque o parâmetro "N-grams" em 2. Este valor obtém a frequência de ocorrência de unigramas (uma característica para cada palavra) e bigrams (uma característica para cada par de palavras adjacentes) a partir do texto de entrada. O parâmetro "N-grams" varia de 0 a 10, o que indica o número máximo de palavras sequenciais a incluir numa característica.  

![Módulo "Feature Hashing"](./media/create-features/feature-Hashing1.png)

A seguinte figura mostra como são estas novas funcionalidades.

![Exemplo de "Hashing de Recurso"](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Conclusão
As funcionalidades concebidas e selecionadas aumentam a eficiência do processo de formação, que tenta extrair a informação-chave contida nos dados. Também melhoram o poder destes modelos para classificar os dados de entrada com precisão e prever os resultados de interesse de forma mais robusta.

A engenharia e a seleção de recursos também podem combinar para tornar a aprendizagem mais computacionalmente tratável. Fá-lo melhorando e reduzindo depois o número de funcionalidades necessárias para calibrar ou treinar um modelo. Matematicamente, as características selecionadas são um conjunto mínimo de variáveis independentes que explicam os padrões nos dados e preveem os resultados com sucesso.

Nem sempre é necessariamente para realizar engenharia de recursos ou seleção de recursos. Depende dos dados, do algoritmo selecionado e do objetivo da experiência.

## <a name="next-steps"></a>Passos seguintes

Para criar funcionalidades para dados em ambientes específicos, consulte os seguintes artigos:

* [Criar funcionalidades para dados no SQL Server](create-features-sql-server.md)
* [Criar funcionalidades para dados num cluster Hadoop usando consultas de Colmeia](create-features-hive.md)