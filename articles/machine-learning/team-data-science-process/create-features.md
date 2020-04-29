---
title: Engenharia de recursos em ciência de dados - Team Data Science Process
description: Explica as finalidades da engenharia de funcionalidades e fornece exemplos do seu papel no processo de melhoria de dados de aprendizagem automática.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 02f109f250fa9bcd4c77cecd0b1b3e4514ecd8bc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76721137"
---
# <a name="feature-engineering-in-data-science"></a>Engenharia de recursos em ciência de dados
Este artigo explica as finalidades da engenharia de funcionalidades e fornece exemplos do seu papel no processo de melhoria de dados de machine learning. Os exemplos usados para ilustrar este processo são retirados do Azure Machine Learning Studio. 

Esta tarefa é um passo no Processo de Ciência de [Dados da Equipa (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

A engenharia de recursos tenta aumentar o poder preditivo dos algoritmos de aprendizagem, criando funcionalidades a partir de dados brutos que ajudam a facilitar o processo de aprendizagem. A engenharia e seleção de funcionalidades é uma parte do TDSP delineado no ciclo de vida do Processo de Ciência de [Dados da Equipa?](overview.md) Engenharia e seleção de recursos são partes do passo de **desenvolvimento** do TDSP. 

* **engenharia**de recursos : Este processo tenta criar funcionalidades relevantes adicionais a partir das características cruas existentes nos dados, e aumentar o poder preditivo do algoritmo de aprendizagem.
* **seleção de recursos**: Este processo seleciona o subconjunto chave das características originais dos dados numa tentativa de reduzir a dimensionalidade do problema de treino.

Normalmente, a engenharia de **recursos** é aplicada primeiro para gerar funcionalidades adicionais, e em seguida, o passo de **seleção** de recursos é realizado para eliminar funcionalidades irrelevantes, redundantes ou altamente correlacionadas.

Os dados de formação utilizados na aprendizagem automática podem muitas vezes ser melhorados através da extração de funcionalidades a partir dos dados brutos recolhidos. Um exemplo de uma característica projetada no contexto de aprender a classificar as imagens de personagens manuscritas é a criação de um mapa de densidade bit construído a partir dos dados de distribuição de bits brutos. Este mapa pode ajudar a localizar as bordas dos caracteres de forma mais eficiente do que simplesmente usar a distribuição bruta diretamente.

Para criar funcionalidades para dados em ambientes específicos, consulte os seguintes artigos:

* [Criar funcionalidades para dados no Servidor SQL](create-features-sql-server.md)
* [Criar funcionalidades para dados num cluster Hadoop usando consultas da Hive](create-features-hive.md)

## <a name="create-features-from-your-data---feature-engineering"></a>Crie funcionalidades a partir dos seus dados - engenharia de funcionalidades
Os dados de formação consistem numa matriz composta por exemplos (registos ou observações armazenadas em linhas), cada uma das quais possui um conjunto de características (variáveis ou campos armazenados em colunas). Espera-se que as funcionalidades especificadas no design experimental caracterizam os padrões dos dados. Embora muitos dos campos de dados brutos possam ser diretamente incluídos no conjunto de funcionalidades selecionado utilizado para treinar um modelo, muitas vezes é o caso de que as funcionalidades adicionais (projetadas) precisam de ser construídas a partir das funcionalidades dos dados brutos para gerar um conjunto de dados de treino melhorado.

Que tipo de funcionalidades devem ser criadas para melhorar o conjunto de dados ao treinar um modelo? As características projetadas que melhoram a formação fornecem informações que melhor diferenciam os padrões dos dados. Espera-se que as novas funcionalidades forneçam informações adicionais que não sejam claramente capturadas ou facilmente aparentes no conjunto de funcionalidades original ou existente. Mas este processo é uma espécie de arte. Decisões sólidas e produtivas muitas vezes requerem alguma experiência de domínio.

Ao começar com o Azure Machine Learning, é mais fácil compreender este processo concretamente utilizando amostras fornecidas no Estúdio. São apresentados dois exemplos aqui:

* Um exemplo de regressão [Previsão do número de alugueres de bicicletas](https://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) numa experiência supervisionada onde os valores-alvo são conhecidos
* Um exemplo de classificação de mineração de texto usando [o Hashing feature](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Exemplo 1: Adicione características temporais para um modelo de regressão
Vamos usar a experiência "Demand forecasting of bikes" no Azure Machine Learning Studio (clássico) para demonstrar como projetar funcionalidades para uma tarefa de regressão. O objetivo desta experiência é prever a procura das bicicletas, ou seja, o número de alugueres de bicicletas dentro de um mês/dia/hora específico. O conjunto de dados "Bike Rental UCI dataset" é utilizado como dados de entrada bruta. Este conjunto de dados baseia-se em dados reais da empresa Capital Bikeshare que mantém uma rede de aluguer de bicicletas em Washington DC, nos Estados Unidos. O conjunto de dados representa o número de alugueres de bicicletas dentro de uma hora específica de um dia nos anos de 2011 e ano de 2012 e contém 17379 linhas e 17 colunas. O conjunto de características brutas contém condições meteorológicas (temperatura/humidade/velocidade do vento) e o tipo do dia (feriado/dia da semana). O campo a prever é a contagem 'CNT', que representa as rendas de bicicletas dentro de uma hora específica e que varia entre 1 e 977.

Com o objetivo de construir funcionalidades eficazes nos dados de treino, quatro modelos de regressão são construídos usando o mesmo algoritmo, mas com quatro conjuntos de dados de treino diferentes. Os quatro conjuntos de dados representam os mesmos dados de entrada crua, mas com um número crescente de funcionalidades definidas. Estas características estão agrunadas em quatro categorias:

1. A = tempo + férias + semana + características do fim de semana para o dia previsto
2. B = número de bicicletas que foram alugadas em cada uma das 12 horas anteriores
3. C = número de bicicletas que foram alugadas em cada um dos 12 dias anteriores à mesma hora
4. D = número de bicicletas que foram alugadas em cada uma das 12 semanas anteriores à mesma hora e no mesmo dia

Além do conjunto de funcionalidades A, que já existe nos dados brutos originais, os outros três conjuntos de funcionalidades são criados através do processo de engenharia de funcionalidades. O conjunto de funcionalidades B capta a procura recente das bicicletas. O conjunto de recursos C capta a procura de bicicletas a uma hora específica. O conjunto de funcionalidades D capta a procura de bicicletas em determinada hora e dia particular da semana. Os quatro conjuntos de dados de treino incluem cada um o conjunto de funcionalidades A, A+B, A+B+C e A+B+C+D, respectivamente.

Na experiência Azure Machine Learning, estes quatro conjuntos de dados de formação são formados através de quatro ramos do conjunto de dados de entrada pré-processado. Com exceção do ramo mais à esquerda, cada um destes ramos contém um módulo [Execute R Script,](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) no qual as características derivadas (conjunto de funcionalidades B, C e D) são respectivamente construídas e anexadas ao conjunto de dados importado. A figura seguinte demonstra o script R usado para criar o conjunto de características B no segundo ramo esquerdo.

![criar funcionalidades](./media/create-features/addFeature-Rscripts.png)

Uma comparação dos resultados de desempenho dos quatro modelos é resumida na tabela seguinte: 

![comparação de resultados](./media/create-features/result1.png)

Os melhores resultados são mostrados pelas características A+B+C. A taxa de erro diminui quando o conjunto adicional de funcionalidades é incluído nos dados de formação. Verifica a presunção de que o conjunto de características B, C fornece informações relevantes adicionais para a tarefa de regressão. Mas adicionar a funcionalidade D não parece fornecer qualquer redução adicional na taxa de erro.

## <a name="example-2-creating-features-in-text-mining"></a><a name="example2"></a>Exemplo 2: Criação de funcionalidades na mineração de texto
A engenharia de recursos é amplamente aplicada em tarefas relacionadas com a mineração de texto, tais como a classificação de documentos e a análise de sentimentos. Por exemplo, quando se pretende classificar os documentos em várias categorias, uma suposição típica é que a palavra/frases incluídas numa categoria do doc são menos propensos a ocorrer noutra categoria do doc. Por outras palavras, a frequência da distribuição de palavras/frases é capaz de caracterizar diferentes categorias de documentos. Nas aplicações de mineração de texto, uma vez que as peças individuais de conteúdo de texto servem normalmente como dados de entrada, o processo de engenharia de funcionalidades é necessário para criar as funcionalidades que envolvem frequências de palavra/frase.

Para alcançar esta tarefa, uma técnica chamada **hashing de características** é aplicada para transformar eficientemente as funcionalidades arbitrárias em índices. Em vez de associar cada característica de texto (palavras/frases) a um determinado índice, este método funciona aplicando uma função hash às características e usando os seus valores de hash como índices diretamente.

No Azure Machine Learning, existe um módulo [de Hashing feature](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) que cria funcionalidades de palavra/frase convenientemente. A figura seguinte mostra um exemplo de utilização deste módulo. O conjunto de dados de entrada contém duas colunas: a classificação do livro que varia de 1 a 5 e o conteúdo real da revisão. O objetivo deste módulo [de Hashing feature](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) é recuperar um monte de novas funcionalidades que mostram a frequência de ocorrência da palavra(s)/frase(s) correspondente dentro da revisão do livro particular. Para utilizar este módulo, complete os seguintes passos:

* Em primeiro lugar, selecione a coluna que contém o texto de entrada ("Col2" neste exemplo).
* Em segundo lugar, coloque o "Hashing bitsize" para 8, o que significa que serão criadas 2^8=256 características. A palavra/fase em todo o texto será apressada para 256 índices. O parâmetro "Hashing bitsize" varia de 1 a 31. A palavra(s)/frase(s) é menos suscetível de ser hashed no mesmo índice se a definir como um número maior.
* Terceiro, coloque o parâmetro "N-grams" para 2. Este valor obtém a frequência de ocorrência de unigramas (uma característica para cada palavra) e bigrams (uma característica para cada par de palavras adjacentes) a partir do texto de entrada. O parâmetro "N-grams" varia entre 0 e 10, o que indica o número máximo de palavras sequenciais a incluir numa característica.  

![Módulo "Hashing de Recurso"](./media/create-features/feature-Hashing1.png)

A figura que se segue mostra como são estas novas funcionalidades.

![Exemplo de "Hashing de Recurso"](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Conclusão
As funcionalidades concebidas e selecionadas aumentam a eficiência do processo de formação, que tenta extrair as informações-chave contidas nos dados. Também melhoram o poder destes modelos para classificar os dados de entrada com precisão e prever os resultados de interesse de forma mais robusta. A engenharia e seleção de recursos também podem combinar para tornar a aprendizagem mais computacionalmente tratável. Fá-lo melhorando e reduzindo depois o número de funcionalidades necessárias para calibrar ou treinar um modelo. Matematicamente falando, as características selecionadas para treinar o modelo são um conjunto mínimo de variáveis independentes que explicam os padrões nos dados e depois preveem resultados com sucesso.

Nem sempre é necessariamente realizar engenharia de recursos ou seleção de recursos. Se é necessário ou não depende dos dados à mão ou recolhidos, do algoritmo selecionado e do objetivo da experiência.

