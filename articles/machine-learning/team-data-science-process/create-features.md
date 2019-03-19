---
title: Engenharia de funcionalidades em ciência de dados - Team Data Science Process
description: Explica a finalidade de engenharia de funcionalidades e fornece exemplos de seu papel no processo de aprimoramento de dados do machine learning.
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
ms.openlocfilehash: 8b0973007a78b492cff1c5ffc2ce1e43116a0847
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57835089"
---
# <a name="feature-engineering-in-data-science"></a>Engenharia de funcionalidades em ciência de dados
Este artigo explica os fins de engenharia de funcionalidades e fornece exemplos de seu papel no processo de aprimoramento de dados do machine learning. Os exemplos usados para ilustrar esse processo são obtidos a partir do Azure Machine Learning Studio. 

Esta tarefa é um passo na [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

Tentativas de engenharia para aumentar a capacidade de previsão de algoritmos de aprendizagem através da criação de recursos de dados não processados que ajudam a facilitar o processo de aprendizado de recursos. A engenharia e a seleção de funcionalidades é parte integrante do TDSP descrito no [o que é o ciclo de vida do processo de ciência de dados de equipa?](overview.md) Engenharia de funcionalidades e seleção são partes do **desenvolver funcionalidades** passo do TDSP. 

* **"Feature Engineering"**: Este processo tenta para criar as funcionalidades relevantes adicionais dos recursos existentes não processados nos dados e para aumentar o poder de previsão do algoritmo de aprendizagem.
* **seleção de funcionalidades**: Este processo seleciona o subconjunto de chave dos recursos de dados original numa tentativa de reduzir a dimensionalidade do problema de treinamento.

Normalmente **com engenharia** é aplicada primeiro para gerar recursos adicionais e, em seguida, o **seleção de funcionalidades** passo é realizado para eliminar recursos irrelevantes, redundantes ou altamente correlacionados.

Os dados de treinamento utilizados na machine learning, muitas vezes, podem ser melhorados pela extração dos recursos dos dados brutos coletados. Um exemplo de uma funcionalidade de engenharia no contexto de aprender classificar as imagens de caracteres manuscritas é a criação de um pouco mapa de densidade construído a partir de dados de distribuição bits brutos. Este mapa pode ajudar a localizar os limites dos caracteres de forma mais eficiente do que simplesmente usar a distribuição bruta diretamente.

Criar características para dados em ambientes específicos, consulte os artigos seguintes:

* [Criar características para dados no SQL Server](create-features-sql-server.md)
* [Criar características para dados num cluster do Hadoop com consultas do Hive](create-features-hive.md)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="create-features-from-your-data---feature-engineering"></a>Criar recursos dos seus dados - "feature Engineering"
Os dados de treinamento consiste numa matriz composta por exemplos (registos ou observações armazenadas em linhas), cada um com um conjunto de recursos (variáveis ou armazenados em colunas de campos). Espera-se que os recursos especificados no experimental design caracterizam os padrões nos dados. Embora muitos dos campos de dados não processados podem ser diretamente incluídos no conjunto de funcionalidade selecionada usado para preparar um modelo, é muitas vezes o caso que funcionalidades adicionais de (engenharia) tem de ser criada a partir os recursos de dados não processados para gerar um conjunto de dados de treinamento avançado.

Que tipo de recursos deve ser criado para melhorar o conjunto de dados quando preparar um modelo? Engenharia de funcionalidades que melhoram o treinamento fornecem informações que diferencia melhor os padrões nos dados. Espera-se que os novos recursos fornecem informações adicionais que não é claramente capturada ou facilmente aparente no conjunto de recursos existente ou original. Mas esse processo é algo de arte. Decisões de som e produtivas, muitas vezes, requerem alguma experiência de domínio.

Quando a partir do Azure Machine Learning, é mais fácil entender esse processo concretamente usando exemplos fornecidos no Studio. Dois exemplos são apresentados aqui:

* Um exemplo de regressão [previsão do número de bicicletas alugadas](https://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) numa experimentação supervisionada onde os valores de destino são conhecidos
* Um através do exemplo de classificação do extração de texto [Hashing de funcionalidade](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Exemplo 1: Adicionar funcionalidades temporais para um modelo de regressão
Vamos utilizar a experiência "previsão da procura de bicicletas" no Azure Machine Learning Studio para demonstrar como fazer a engenharia de funcionalidades para uma tarefa de regressão. O objetivo desta experiência é prever a procura por bicicletas, ou seja, o número de bicicletas alugadas dentro de uma específica mês/dia/hora. O conjunto de dados "aluguer de bicicletas UCI conjunto de dados" é utilizado como os dados de entrada não processados. Este conjunto de dados baseia-se em dados reais da empresa de Capital Bikeshare que mantém uma rede de aluguer de bicicletas em Washington DC nos Estados Unidos. O conjunto de dados representa o número de bicicletas alugadas dentro de uma hora específica de um dia nos anos de 2011 e o ano 2012 e contém 17379 linhas e colunas de 17. O conjunto de recursos não processados contém condições meteorológicas (velocidade de temperatura/umidade/vento) e o tipo do dia (feriado/dia da semana). O campo para prever é a contagem de "cnt", que representa o aluguer de bicicletas dentro de uma hora específica e que varia de 1 a 977.

Com o objetivo de construção de funcionalidades em vigor nos dados de treinamento, quatro regressão modelos são criados com o mesmo algoritmo, mas com quatro conjuntos de dados de treinamento diferentes. Os quatro conjuntos de dados representam os mesmos dados de entrada não processados, mas com um número crescente de recursos do conjunto. Esses recursos são agrupados em quatro categorias:

1. Um = Meteorologia + feriado + weekday + final de semana funcionalidades para o dia previsto
2. B = número de bicicletas que foram alugados em cada um das 12 horas anteriores
3. C = número de bicicletas que foram alugados em cada um dos últimos dias 12 na mesma hora
4. 1!d = número de bicicletas que foram alugados em cada um das 12 semanas anteriores na mesma hora e o mesmo dia

Além de um conjunto de funcionalidade, que já existe nos dados não processados originais, os outros três conjuntos de recursos são criados através da funcionalidade de processo de engenharia. Conjunto de recursos capturas de B muito recente procura de bicicletas. Funcionalidade definida a procura por bicicletas capturas de C numa determinada hora. Conjunto de recursos a pedido de capturas de D para bicicletas em determinada hora e dia determinado da semana. O quatro treinamento conjuntos de dados cada incluem um conjunto de recursos, A + B, A + B + C e A + B + C + D, respectivamente.

Na experimentação do Azure Machine Learning, estes quatro conjuntos de dados de treinamento são formados por meio de quatro ramificações do conjunto de dados de entrada previamente processada. Exceto o ramo mais à esquerda, cada uma dessas agências contém um [executar Script do R](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) módulo, na qual os recursos derivados (conjunto de recursos B, C e D) são respectivamente construídos e acrescentados ao conjunto de dados importado. A figura a seguir demonstra o script de R utilizado para criar o conjunto de recursos B no segundo ramo à esquerda.

![Criar recursos](./media/create-features/addFeature-Rscripts.png)

Uma comparação dos resultados do desempenho de quatro modelos é resumida na tabela a seguir: 

![comparação de resultado](./media/create-features/result1.png)

Os melhores resultados são mostrados pelas funcionalidades A + B + C. Tenha em atenção que a taxa de erros diminui quando o conjunto de funcionalidades adicionais estão incluídos nos dados de treinamento. Verifica a suposição de que o conjunto de recursos B, C informações adicionais relevantes para a tarefa de regressão. Mas adicionar a funcionalidade de D aparenta não fornecem qualquer redução adicional a taxa de erros.

## <a name="example2"></a> Exemplo 2: Criar recursos na extração de texto
Engenharia de funcionalidades amplamente é aplicada em tarefas relacionadas com a extração de texto, como a análise de classificação e de sentimento do documento. Por exemplo, quando deseja classificar documentos em várias categorias, uma suposição típica é que as incluído numa categoria de documento do word/frases é menos provável que ocorram noutra categoria de documento. Em outras palavras, a frequência da distribuição de palavras/frases é capaz de caracterizam categorias diferentes de documento. Em aplicativos de extração de texto, uma vez que as partes individuais de conteúdos de texto, normalmente, servem como os dados de entrada, a funcionalidade de processo de engenharia é necessária para criar as funcionalidades que envolvam frequências palavra/expressão.

Para realizar esta tarefa, uma técnica chamada **hashing de funcionalidade** é aplicada a transformar eficientemente recursos de texto arbitrário em índices. Em vez de associar cada funcionalidade de texto (palavras/frases) para um índice específico, este funções de método ao aplicar uma função de hash para os recursos e usar seus valores de hash como índices diretamente.

No Azure Machine Learning, existe uma [Hashing de funcionalidade](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) módulo que cria estas palavra/expressão funcionalidades convenientemente. Figura a seguir mostra um exemplo de como utilizar este módulo. O conjunto de dados de entrada contém duas colunas: a classificação de livro entre 1 e 5 e o conteúdo de revisão real. O objetivo desta [Hashing de funcionalidade](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) módulo é para recuperar um monte de novas funcionalidades que mostram a frequência de ocorrência da word(s) correspondente / rever phrase(s) neste livro específico. Para utilizar este módulo, conclua os seguintes passos:

* Primeiro, selecione a coluna que contém o texto de entrada ("Col2" neste exemplo).
* Em segundo lugar, defina "bitsize de Hashing" como 8, que significa 2 ^ 8 = 256 funcionalidades serão criadas. O word/fase em todo o texto será convertido para 256 índices. O parâmetro "Hashing bitsize" intervalos de 1 e 31. O word(s) / phrase(s) têm menos probabilidade de ser protegido por hash para o mesmo índice se defini-la para ser um número maior.
* Em terceiro lugar, defina o parâmetro "N-grams" como 2. Este valor é a frequência de ocorrência de unigrams (um recurso para cada palavra única) e bigrams (um recurso para cada par de palavras adjacentes), o texto de entrada. O parâmetro "N-grams" varia de 0 a 10, que indica o número máximo de sequenciais palavras a serem incluídos num recurso.  

![Módulo "Hashing de funcionalidade"](./media/create-features/feature-Hashing1.png)

A figura seguinte mostra o que eles nova funcionalidade de aspeto.

![Exemplo de "Hashing de funcionalidade"](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Conclusão
Funcionalidades de engenharia e selecionadas aumentam a eficiência do processo de treinamento, que tenta extrair as informações da chave contidas nos dados. Eles também melhorem o poder desses modelos para classificar os dados de entrada com precisão e prever os resultados de interesse com mais robustez. Engenharia de funcionalidades e a seleção também podem combinar para tornar a aprendizagem mais recursos computacionais tractable. Ele faz isso, a melhorar e, em seguida, reduzindo o número de recursos necessários para calibrar ou preparar um modelo. Falando em matemática, dos recursos selecionados para preparar o modelo são um conjunto mínimo de variáveis independentes, que explicam os padrões nos dados e, em seguida, prever os resultados com êxito.

Nem sempre é necessariamente para efetuar a seleção de funcionalidade ou de engenharia de funcionalidades. Se for necessário ou não depende a transferência dos dados ou recolhidos, o algoritmo selecionado e o objetivo da experimentação.

