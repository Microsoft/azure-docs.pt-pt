---
title: Engenharia de recursos na ciência de dados – processo de ciência de dados de equipe
description: Explica a finalidade da engenharia de recursos e fornece exemplos de sua função no processo de aprimoramento de dados do aprendizado de máquina.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/21/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e633c5742b8a7882149a347ced46e55440cb6913
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492434"
---
# <a name="feature-engineering-in-data-science"></a>Engenharia de recursos na ciência de dados
Este artigo explica a finalidade da engenharia de recursos e fornece exemplos de sua função no processo de aprimoramento de dados do aprendizado de máquina. Os exemplos usados para ilustrar esse processo são desenhados a partir de Azure Machine Learning Studio. 

Essa tarefa é uma etapa no [TDSP (processo de ciência de dados de equipe)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

A engenharia de recursos tenta aumentar a potência preditiva dos algoritmos de aprendizado criando recursos de dados brutos que ajudam a facilitar o processo de aprendizado. A engenharia e a seleção de recursos são uma parte do TDSP descrito em o [que é o ciclo de vida do processo de ciência de dados de equipe?](overview.md) Engenharia e seleção de recursos são partes da etapa **desenvolver recursos** do TDSP. 

* **engenharia de recursos**: esse processo tenta criar recursos relevantes adicionais a partir dos recursos brutos existentes nos dados e aumentar a potência preditiva do algoritmo de aprendizado.
* **seleção de recursos**: esse processo seleciona o subconjunto de chaves dos recursos de dados originais em uma tentativa de reduzir a dimensionalidade do problema de treinamento.

Normalmente, a **engenharia de recursos** é aplicada primeiro para gerar recursos adicionais e, em seguida, a etapa de **seleção de recursos** é executada para eliminar recursos irrelevantes, redundantes ou altamente correlacionados.

Os dados de treinamento usados no aprendizado de máquina geralmente podem ser aprimorados com a extração de recursos dos dados brutos coletados. Um exemplo de um recurso projetado no contexto de aprender a classificar as imagens de caracteres manuscritos é a criação de um mapa de densidade de bits construído a partir dos dados brutos de distribuição de bits. Esse mapa pode ajudar a localizar as bordas dos caracteres com mais eficiência do que simplesmente usar a distribuição bruta diretamente.

Para criar recursos para dados em ambientes específicos, consulte os seguintes artigos:

* [Criar recursos para dados no SQL Server](create-features-sql-server.md)
* [Criar recursos para dados em um cluster Hadoop usando consultas do hive](create-features-hive.md)

## <a name="create-features-from-your-data---feature-engineering"></a>Criar recursos de sua engenharia de recursos de dados
Os dados de treinamento consistem em uma matriz composta de exemplos (registros ou observações armazenadas em linhas), cada um com um conjunto de recursos (variáveis ou campos armazenados em colunas). Espera-se que os recursos especificados no design experimental caracterizam os padrões nos dados. Embora muitos dos campos de dados brutos possam ser incluídos diretamente no conjunto de recursos selecionado usado para treinar um modelo, geralmente é o caso em que recursos adicionais (desenvolvidos) precisam ser construídos a partir dos recursos nos dados brutos para gerar um conjunto de dado de treinamento aprimorado.

Que tipo de recursos deve ser criado para aprimorar o conjunto de os ao treinar um modelo? Recursos de engenharia que aprimoram o treinamento fornecem informações que diferenciam melhor os padrões dos dados. Os novos recursos devem fornecer informações adicionais que não são claramente capturadas ou aparentes facilmente no conjunto de recursos original ou existente. Mas esse processo é algo de uma arte. Decisões sólidas e produtivas geralmente exigem algum conhecimento do domínio.

Ao começar com Azure Machine Learning, é mais fácil entender esse processo concretamente usando exemplos fornecidos no estúdio. Dois exemplos são apresentados aqui:

* Uma [previsão de exemplo de regressão do número de locações de bicicletas](https://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) em um experimento supervisionado em que os valores de destino são conhecidos
* Um exemplo de classificação de mineração de texto usando [hash de recurso](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Exemplo 1: adicionar recursos temporais para um modelo de regressão
Vamos usar o experimento "Previsão de demanda de bicicletas" em Azure Machine Learning Studio (clássico) para demonstrar como desenvolver recursos para uma tarefa de regressão. O objetivo desse experimento é prever a demanda das bicicletas, ou seja, o número de locações de bicicletas em um mês/dia/hora específico. O conjunto de dados "conjunto de informações do aluguel de bicicletas" é usado como o dado de entrada bruto. Esse conjunto de dados baseia-se no real de Bikeshare da empresa de capital que mantém uma rede de aluguel de bicicletas em Washington, D.c. no Estados Unidos. O conjunto de informações representa o número de locações de bicicletas em uma hora específica de um dia nos anos 2011 e ano 2012 e contém 17379 linhas e 17 colunas. O conjunto de recursos brutos contém condições de clima (temperatura/umidade/velocidade do vento) e o tipo do dia (feriado/dia da semana). O campo a prever é a contagem "CNT", que representa a locações de bicicletas em uma hora específica e que varia de 1 a 977.

Com o objetivo de construir recursos eficazes nos dados de treinamento, quatro modelos de regressão são criados usando o mesmo algoritmo, mas com quatro conjuntos de dados de treinamento diferentes. Os quatro DataSets representam os mesmos dados de entrada brutos, mas com um número crescente de recursos definidos. Esses recursos são agrupados em quatro categorias:

1. A = clima + feriado + dia da semana + recursos de fim de semana para o dia previsto
2. B = número de bicicletas que foram alugadas em cada uma das 12 horas anteriores
3. C = número de bicicletas que foram alugadas em cada um dos 12 dias anteriores na mesma hora
4. D = número de bicicletas que foram alugadas em cada uma das 12 semanas anteriores na mesma hora e no mesmo dia

Além do conjunto de recursos A, que já existe nos dados brutos originais, os outros três conjuntos de recursos são criados por meio do processo de engenharia de recursos. O conjunto de recursos B captura uma demanda muito recente para as bicicletas. O conjunto de recursos C captura a demanda por bicicletas em uma hora específica. O conjunto de recursos D captura a demanda por bicicletas em uma hora específica e em um dia específico da semana. Cada um dos quatro conjuntos de itens de treinamento inclui o conjunto de recursos A, A + B, A + B + C e A + B + C + D, respectivamente.

No experimento de Azure Machine Learning, esses quatro conjuntos de dados de treinamento são formados por meio de quatro ramificações do conjunto de dados de entrada pré-processado. Exceto a ramificação mais à esquerda, cada uma dessas ramificações contém um módulo [Executar script R](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) , no qual os recursos derivados (conjunto de recursos B, C e D) são construídos e acrescentados ao conjunto de módulos importado. A figura a seguir demonstra o script R usado para criar o conjunto de recursos B no segundo Branch esquerdo.

![criar recursos](./media/create-features/addFeature-Rscripts.png)

Uma comparação dos resultados de desempenho dos quatro modelos é resumida na tabela a seguir: 

![comparação de resultados](./media/create-features/result1.png)

Os melhores resultados são mostrados por recursos A + B + C. Observe que a taxa de erros diminui quando um conjunto de recursos adicional é incluído nos dados de treinamento. Ele verifica a precontinuação de que o conjunto de recursos B, C fornece informações adicionais relevantes para a tarefa de regressão. Mas adicionar o recurso D não parece fornecer nenhuma redução adicional na taxa de erro.

## <a name="example2"></a>Exemplo 2: criando recursos na mineração de texto
A engenharia de recursos é amplamente aplicada em tarefas relacionadas à mineração de texto, como a classificação de documentos e a análise de sentimentos. Por exemplo, quando você deseja classificar documentos em várias categorias, uma suposição típica é que as palavras/frases incluídas em uma categoria de documento têm menos probabilidade de ocorrer em outra categoria de documento. Em outras palavras, a frequência da distribuição de palavras/frases é capaz de caracterizar diferentes categorias de documentos. Em aplicativos de mineração de texto, como partes individuais de conteúdo de texto geralmente servem como dados de entrada, o processo de engenharia de recursos é necessário para criar os recursos que envolvem frequências de palavra/frase.

Para obter essa tarefa, uma técnica chamada **hash de recurso** é aplicada para transformar com eficiência recursos de texto arbitrários em índices. Em vez de associar cada recurso de texto (palavras/frases) a um índice específico, esse método funciona aplicando uma função de hash aos recursos e usando seus valores de hash como índices diretamente.

No Azure Machine Learning, há um módulo de [hash de recurso](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) que cria esses recursos de palavra/frase convenientemente. A figura a seguir mostra um exemplo de como usar esse módulo. O conjunto de dados de entrada contém duas colunas: a classificação do livro variando de 1 a 5 e o conteúdo real da revisão. O objetivo desse módulo de [hash de recurso](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) é recuperar uma série de novos recursos que mostram a frequência de ocorrência das palavras correspondentes (s)/Phrase (s) na revisão do livro em particular. Para usar este módulo, conclua as seguintes etapas:

* Primeiro, selecione a coluna que contém o texto de entrada ("Col2" neste exemplo).
* Em segundo lugar, defina "bits de Hashize" como 8, o que significa que 2 ^ 8 = 256 recursos serão criados. A palavra/fase em todo o texto será transformada em hash em índices 256. O parâmetro "bits de Hashização" varia de 1 a 31. As palavras (s)/Phrase (s) têm menos probabilidade de serem configuradas em hash no mesmo índice, caso defina-o como um número maior.
* Em terceiro lugar, defina o parâmetro "N-grams" como 2. Esse valor Obtém a frequência de ocorrência de unigrams (um recurso para cada palavra) e bigrams (um recurso para cada par de palavras adjacentes) do texto de entrada. O parâmetro "N-grams" varia de 0 a 10, o que indica o número máximo de palavras sequenciais a serem incluídas em um recurso.  

![Módulo "hash de recurso"](./media/create-features/feature-Hashing1.png)

A figura a seguir mostra a aparência desses novos recursos.

![Exemplo de "hash de recurso"](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Conclusão
Os recursos projetados e selecionados aumentam a eficiência do processo de treinamento, que tenta extrair as principais informações contidas nos dados. Eles também melhoram o poder desses modelos para classificar os dados de entrada com precisão e prever os resultados de interesse de forma mais robusta. A engenharia de recursos e a seleção também podem combinar para tornar o aprendizado mais computacionalmente manejável. Ele faz isso aprimorando e reduzindo o número de recursos necessários para calibrar ou treinar um modelo. Em termos de matemática, os recursos selecionados para treinar o modelo são um conjunto mínimo de variáveis independentes que explicam os padrões nos dados e, em seguida, prevêm os resultados com êxito.

Nem sempre é necessariamente executar a engenharia de recursos ou a seleção de recursos. Se é necessário ou não depende dos dados a serem disponibilizados ou coletados, o algoritmo selecionado e o objetivo do experimento.

