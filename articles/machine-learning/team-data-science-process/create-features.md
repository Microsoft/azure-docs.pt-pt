---
title: Engenharia de recursos em ciência de dados - Team Data Science Process
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
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath, contperfq4
ms.openlocfilehash: c31cf0e5c655f53e8838c92f5463d3a85c2f6f65
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836826"
---
# <a name="feature-engineering-in-data-science"></a>Engenharia de recursos em ciência de dados

Neste artigo, aprende-se sobre engenharia de recursos e o seu papel na melhoria dos dados na aprendizagem automática. Aprenda com exemplos ilustrativos extraídos de experiências do [Azure Machine Learning Studio (clássico).](../studio/what-is-ml-studio.md) 

* **Engenharia**de recursos : O processo de criação de novas funcionalidades a partir de dados brutos para aumentar o poder preditivo do algoritmo de aprendizagem. As funcionalidades de engenharia devem capturar informações adicionais que não sejam facilmente aparentes no conjunto de funcionalidades originais.
* **Seleção de recursos**: O processo de seleção do subconjunto chave de funcionalidades para reduzir a dimensionalidade do problema de treino.

Normalmente, a engenharia de **funcionalidades** é aplicada primeiro para gerar funcionalidades adicionais, e depois a **seleção** de recursos é feita para eliminar funcionalidades irrelevantes, redundantes ou altamente correlacionadas.

A engenharia e seleção de recursos fazem parte da fase de [modelação](lifecycle-modeling.md) do Processo de Ciência de Dados da Equipa (TDSP). Para saber mais sobre o TDSP e o ciclo de vida da ciência dos dados, veja [o que é o TDSP?](overview.md)

## <a name="what-is-feature-engineering"></a>O que é engenharia de recursos?

Os dados de formação consistem numa matriz composta por linhas e colunas. Cada linha da matriz é uma observação ou registo. As colunas de cada linha são as características que descrevem cada registo. As funcionalidades especificadas no design experimental devem caracterizar os padrões nos dados.

Embora muitos dos campos de dados brutos possam ser usados diretamente para treinar um modelo, é muitas vezes necessário criar funcionalidades adicionais (projetadas) para um conjunto de dados de treino melhorado.

As características projetadas que melhoram a formação fornecem informações que melhor diferenciam os padrões nos dados. Mas este processo é uma espécie de arte. Decisões sólidas e produtivas muitas vezes requerem conhecimentos de domínio.

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Exemplo 1: Adicione características temporais para um modelo de regressão

Vamos usar a experiência [A previsão da procura de aluguer de bicicletas](https://gallery.azure.ai/Experiment/Regression-Demand-estimation-4) no Azure Machine Learning Studio (clássico) para demonstrar como projetar funcionalidades para uma tarefa de regressão. O objetivo desta experiência é prever a procura de aluguer de bicicletas dentro de um mês/dia/hora específico.

### <a name="bike-rental-dataset"></a>Conjunto de dados de aluguer de bicicletas

O conjunto de [dados da Bike Rental UCI](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset/) baseia-se em dados reais de uma empresa de partilha de bicicletas sediada nos Estados Unidos. Representa o número de alugueres de bicicletas dentro de uma hora específica de um dia para os anos de 2011 e 2012. Contém 17.379 linhas e 17 colunas.

O conjunto de características brutas contém condições meteorológicas (temperatura/humidade/velocidade do vento) e o tipo do dia (feriado/dia da semana). O campo a prever é a contagem, que representa as rendas de bicicletas dentro de uma hora específica. A contagem varia entre 1 e 977.

### <a name="create-a-feature-engineering-experiment"></a>Criar uma experiência de engenharia de recursos

Com o objetivo de construir funcionalidades eficazes nos dados de treino, quatro modelos de regressão são construídos usando o mesmo algoritmo, mas com quatro conjuntos de dados de treino diferentes. Os quatro conjuntos de dados representam os mesmos dados de entrada crua, mas com um número crescente de funcionalidades definidas. Estas características estão agrunadas em quatro categorias:

1. A = tempo + férias + semana + características do fim de semana para o dia previsto
2. B = número de bicicletas que foram alugadas em cada uma das 12 horas anteriores
3. C = número de bicicletas que foram alugadas em cada um dos 12 dias anteriores à mesma hora
4. D = número de bicicletas que foram alugadas em cada uma das 12 semanas anteriores à mesma hora e no mesmo dia

Além do conjunto de funcionalidades A, que já existe nos dados brutos originais, os outros três conjuntos de funcionalidades são criados através do processo de engenharia de funcionalidades. O conjunto de funcionalidades B capta a procura recente das bicicletas. O conjunto de recursos C capta a procura de bicicletas a uma hora específica. O conjunto de funcionalidades D capta a procura de bicicletas em determinada hora e dia particular da semana. Os quatro conjuntos de dados de treino incluem cada um o conjunto de funcionalidades A, A+B, A+B+C e A+B+C+D, respectivamente.

### <a name="feature-engineering-using-studio-classic"></a>Engenharia de recursos usando Studio (clássico)

Na experiência Studio (clássica), estes quatro conjuntos de dados de treino são formados através de quatro ramos do conjunto de dados de entrada pré-processado. Com exceção do ramo mais à esquerda, cada um destes ramos contém um módulo [Execute R Script,](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) no qual as características derivadas (conjunto de funcionalidades B, C e D) são construídas e anexadas ao conjunto de dados importado.

A figura seguinte demonstra o script R usado para criar o conjunto de características B no segundo ramo esquerdo.

![criar funcionalidades](./media/create-features/addFeature-Rscripts.png)

### <a name="results"></a>Resultados

Uma comparação dos resultados de desempenho dos quatro modelos é resumida na tabela seguinte: 

![comparação de resultados](./media/create-features/result1.png)

Os melhores resultados são mostrados pelas características A+B+C. A taxa de erro diminui quando o conjunto adicional de funcionalidades é incluído nos dados de formação. Verifica a presunção de que o conjunto de características B, C fornece informações relevantes adicionais para a tarefa de regressão. Mas adicionar a funcionalidade D não parece fornecer qualquer redução adicional na taxa de erro.

## <a name="example-2-create-features-for-text-mining"></a><a name="example2"></a>Exemplo 2: Criar funcionalidades para a mineração de texto

A engenharia de recursos é amplamente aplicada em tarefas relacionadas com a mineração de texto, tais como classificação de documentos e análise de sentimentos. Uma vez que as peças individuais de texto bruto servem normalmente como dados de entrada, o processo de engenharia de funcionalidades é necessário para criar as funcionalidades que envolvem frequências de palavra/frase.

### <a name="feature-hashing"></a>Hashing de recurso

Para alcançar esta tarefa, uma técnica chamada [hashing de características](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/feature-hashing) é aplicada para transformar eficientemente as funcionalidades arbitrárias em índices. Em vez de associar cada característica de texto (palavras/frases) a um determinado índice, este método aplica uma função hash às características e utiliza os seus valores de hash como índices diretamente.

No Studio (clássico), existe um módulo [de Hashing de Recurso](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/feature-hashing) que cria funcionalidades de palavra/frase convenientemente. A figura seguinte mostra um exemplo de utilização deste módulo. O conjunto de dados de entrada contém duas colunas: a classificação do livro que varia de 1 a 5 e o conteúdo real da revisão. O objetivo deste módulo é recuperar um conjunto de novas funcionalidades que mostram a frequência de ocorrência da palavra(s)/frases correspondentes dentro da revisão do livro particular. Para utilizar este módulo, complete os seguintes passos:

* Em primeiro lugar, selecione a coluna que contém o texto de entrada ("Col2" neste exemplo).
* Em segundo lugar, coloque o "Hashing bitsize" para 8, o que significa que serão criadas 2^8=256 características. A palavra/fase em todo o texto será apressada para 256 índices. O parâmetro "Hashing bitsize" varia de 1 a 31. A palavra(s)/frase(s) é menos suscetível de ser hashed no mesmo índice se a definir como um número maior.
* Terceiro, coloque o parâmetro "N-grams" para 2. Este valor obtém a frequência de ocorrência de unigramas (uma característica para cada palavra) e bigrams (uma característica para cada par de palavras adjacentes) a partir do texto de entrada. O parâmetro "N-grams" varia entre 0 e 10, o que indica o número máximo de palavras sequenciais a incluir numa característica.  

![Módulo "Hashing de Recurso"](./media/create-features/feature-Hashing1.png)

A figura que se segue mostra como são estas novas funcionalidades.

![Exemplo de "Hashing de Recurso"](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Conclusão
As funcionalidades concebidas e selecionadas aumentam a eficiência do processo de formação, que tenta extrair as informações-chave contidas nos dados. Também melhoram o poder destes modelos para classificar os dados de entrada com precisão e prever os resultados de interesse de forma mais robusta.

A engenharia e seleção de recursos também podem combinar para tornar a aprendizagem mais computacionalmente tratável. Fá-lo melhorando e reduzindo depois o número de funcionalidades necessárias para calibrar ou treinar um modelo. Matematicamente, as características selecionadas são um conjunto mínimo de variáveis independentes que explicam os padrões nos dados e preveem resultados com sucesso.

Nem sempre é necessariamente realizar engenharia de recursos ou seleção de recursos. Depende dos dados, do algoritmo selecionado e do objetivo da experiência.

## <a name="next-steps"></a>Passos seguintes

Para criar funcionalidades para dados em ambientes específicos, consulte os seguintes artigos:

* [Criar funcionalidades para dados no Servidor SQL](create-features-sql-server.md)
* [Criar funcionalidades para dados num cluster Hadoop usando consultas da Hive](create-features-hive.md)