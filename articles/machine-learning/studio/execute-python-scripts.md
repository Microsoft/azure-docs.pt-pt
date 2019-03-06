---
title: Executar scripts de Python machine learning
titleSuffix: Azure Machine Learning Studio
description: Contornos princípios subjacentes suporte a scripts de Python no Azure Machine Learning Studio e cenários de utilização básica, capacidades e limitações de design.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 11/29/2017
ms.openlocfilehash: 15aa3921703871b9403ed4c01e800e4ae61594ea
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432402"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Executar scripts de machine learning em Python no Azure Machine Learning Studio

Este tópico descreve os princípios de design subjacente o suporte atual para scripts do Python no Azure Machine Learning Studio. As principais capacidades fornecidas também são descritas, incluindo:

- executar cenários de utilização básica
- classificar uma experimentação num serviço web
- suporte para a importação de código existente
- visualizações de exportação
- efetuar a seleção de funcionalidades supervisionado
- compreender algumas limitações

[Python](https://www.python.org/) é uma ferramenta indispensável no qual a ferramenta de muitos cientistas de dados. Ele possui:

* uma sintaxe elegante e concisa, 
* suporte para várias plataformas, 
* uma grande coleção de bibliotecas eficientes, e 
* ferramentas de desenvolvimento maduro. 

Python está a ser utilizado em todas as fases de um fluxo de trabalho normalmente utilizados nas modelagem do machine learning:

- ingestão de dados e processamento 
- construção de funcionalidade
- Preparação de modelos 
- validação de modelo
- implementação dos modelos

O Azure Machine Learning Studio oferece suporte a incorporação de Python de scripts em várias partes de uma máquina experimentação de aprendizagem e publicá-los diretamente como serviços da web no Microsoft Azure.




## <a name="design-principles-of-python-scripts-in-machine-learning"></a>Princípios de design de scripts do Python no Machine Learning

A interface principal para Python no Azure Machine Learning Studio é através da [executar Script do Python] [ execute-python-script] módulo mostrado na figura 1.

![image1](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![image2](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

Figura 1. O **executar Script do Python** módulo.

O [executar Script do Python] [ execute-python-script] módulo no Azure ML Studio aceita até três entradas e produz até duas saídas (discutidas na seção a seguir), como o análogo do R, o [executar Script do R ] [ execute-r-script] módulo. O código de Python a ser executado é inserido na caixa de parâmetro especialmente nomeado como ponto de entrada de função chamada `azureml_main`. Aqui estão os princípios de design principais usados para implementar este módulo:

1. *Tem de ser idiomático para usuários de Python.* A maioria dos usuários de Python fatorar seu código, como funções dentro de módulos. Então, colocar muito instruções executáveis num módulo de nível superior é relativamente raro. Como resultado, a caixa de script também usa uma função de Python especialmente nomeada em vez de uma seqüência de instruções. Os objetos expostos na função são tipos de biblioteca de Python padrão, tal como [Pandas](http://pandas.pydata.org/) quadros de dados e [NumPy](http://www.numpy.org/) matrizes.
2. *Tem de ter alta-fidelidade entre o local e na cloud de execuções.* O back-end utilizado para executar o código de Python se baseia [Anaconda](https://store.continuum.io/cshop/anaconda/), um amplamente utilizadas distribuição da Python científica para várias plataformas. Ele vem com quase 200 os pacotes de Python mais comuns. Por conseguinte, os cientistas de dados podem depurar e qualificar seu código no seu ambiente do Azure Machine Learning Studio compatível com Anaconda local. Em seguida, utilizar um ambiente de desenvolvimento existente, como [IPython](http://ipython.org/) bloco de notas ou [ferramentas do Python para Visual Studio](https://aka.ms/ptvs), executá-lo como parte de uma experimentação do Azure ML. O `azureml_main` ponto de entrada é uma função de Python baunilha e, portanto, *** podem ser criadas sem código específico do ML do Azure ou o SDK instalado.
3. *Tem de ser facilmente compostos com outros módulos do Azure Machine Learning Studio.* O [executar Script do Python] [ execute-python-script] módulo aceita, como entradas e saídas, conjuntos de dados do Azure Machine Learning Studio padrão. A estrutura subjacente forma transparente e eficiente preenche os tempos de execução do Azure ML e Python. Portanto, o Python pode ser utilizado em conjunto com o Azure ML fluxos de trabalho existentes, incluindo os que chamar R e SQLite. Consequentemente, cientista de dados pode compor fluxos de trabalho que:
   * utilizar o Python e Pandas para dados de pré-processamento e a limpeza
   * Feed de dados para uma transformação de SQL, associar vários conjuntos de dados às funcionalidades de formulário
   * utilizar modelos com os algoritmos no Azure Machine Learning Studio 
   * avaliar e pós-processamento os resultados com o R.


## <a name="basic-usage-scenarios-in-ml-for-python-scripts"></a>Cenários de utilização básica no ML para scripts do Python

Nesta secção, vamos pesquisa alguns dos usos básicos do [executar Script do Python] [ execute-python-script] módulo. Entradas para o módulo de Python são expostas como quadros de dados de Pandas. A função tem de devolver um único quadro de dados de Pandas empacotado dentro do Python [sequência](https://docs.python.org/2/c-api/sequence.html) como uma cadeia de identificação, lista ou NumPy matriz. O primeiro elemento desta seqüência, em seguida, é devolvido na primeira porta de saída do módulo. Este esquema é mostrado na figura 2.

![image3](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

Figura 2. Mapeamento de portas para os parâmetros de entrada e retornar o valor para a porta de saída.

Mais detalhadas a semântica de como as portas de entrada são mapeadas para os parâmetros do `azureml_main` função são mostrados na tabela 1:

![image1T](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

Tabela 1. Mapeamento de portas de entrada para os parâmetros da função.

O mapeamento entre as portas de entrada e os parâmetros da função é posicional:

- A primeira porta de entrada ligada é mapeada para o primeiro parâmetro da função. 
- A segunda entrada (se ligado) é mapeada para o segundo parâmetro da função.

Ver *Python para análise de dados* (o ' Reilly, 2012) por McKinney Ocidental para obter mais informações em Python Pandas e em como ele pode ser usado para manipular dados com eficiência e eficácia. 


## <a name="translation-of-input-and-output-types"></a>Conversão de tipos de entrada e saídos 
Conjuntos de dados de entrada no Azure ML são convertidos em quadros de dados no Pandas. Quadros de dados de saída são convertidos novamente para conjuntos de dados do Azure ML. As seguintes conversões são executadas:

1. Colunas de cadeia de caracteres e numéricos são convertidas como-é e valores em falta num conjunto de dados são convertidos em valores de "ND" no Pandas. A mesma conversão acontece na direção contrária (os valores NA Pandas são convertidas em valores em falta no Azure ML).
2. Índice de vetores em Pandas não são suportadas no Azure ML. Todos os quadros de dados de entrada na função Python sempre tem um índice numérico de 64 bits de 0 para o número de linhas menos 1. 
3. Conjuntos de dados de ML do Azure não podem ter nomes de colunas duplicados e nomes de colunas que não são cadeias de caracteres. Se um quadro de dados de saída contém colunas não numéricos, a estrutura chama `str` sobre os nomes das colunas. Da mesma forma, a quaisquer nomes de colunas duplicados são automaticamente mangled para assegurar que os nomes são exclusivos. O sufixo (2) é adicionado à primeira duplicados, (3) para a segunda duplicata e assim por diante.


## <a name="operationalizing-python-scripts"></a>Operacionalização de scripts do Python

Qualquer [executar Script do Python] [ execute-python-script] módulos utilizados numa experimentação classificação são chamados quando publicado como um serviço web. Por exemplo, a figura 3 mostra uma experimentação de classificação que contém o código para avaliar uma única expressão de Python. 

![image4](./media/execute-python-scripts/figure3a.png)

![image5](./media/execute-python-scripts/python-script-with-python-pandas.png)

Figura 3. Serviço Web para a avaliação de uma expressão de Python.

Um serviço da web criado a partir desta experiência:

- utiliza como entrada uma expressão de Python (como uma cadeia de caracteres)
- envia-os para o interpretador de Python 
- Devolve uma tabela que contém a expressão e o resultado avaliado.


## <a name="importing-existing-python-script-modules"></a>Importar os módulos de script de Python existentes

É um caso de utilização comuns para os cientistas de dados muitos incorporar scripts existentes do Python em experimentações do Azure ML. Em vez de exigir que todo o código ser concatenado e colado uma caixa de script único, o [executar Script do Python] [ execute-python-script] módulo aceita um ficheiro zip que contém módulos de Python na porta de entrada terceiro. O ficheiro é descompactei pela estrutura de execução em tempo de execução e os conteúdos são adicionados ao caminho de biblioteca do interpretador de Python. O `azureml_main` função pode, em seguida, importar esses módulos diretamente de ponto de entrada.

Por exemplo, considere o arquivo Hello.py que contém uma função de "Olá, mundo" simples.

![image6](./media/execute-python-scripts/figure4.png)

Figura 4. Função definida pelo utilizador no ficheiro de Hello.py.

Em seguida, criamos um ficheiro zip que contém Hello.py:

![image7](./media/execute-python-scripts/figure5.png)

Figura 5. Ficheiro zip que contém o código de Python definido pelo utilizador.

Carregar o ficheiro zip como um conjunto de dados para o Azure Machine Learning Studio. Em seguida, criar e executar uma experiência que usa o código de Python no ficheiro zip ao ligá-la para a porta de entrada terceiro do **executar Script do Python** módulo, conforme mostrado na figura.

![image8](./media/execute-python-scripts/figure6a.png)

![image9](./media/execute-python-scripts/figure6b.png)

Figura 6. Experimentação de exemplo com o código de Python definidas pelo utilizador carregado como um ficheiro zip.

O resultado do módulo mostra que o ficheiro zip foi descompactado e que a função `print_hello` tiver sido executado.
 
![image10](./media/execute-python-scripts/figure7.png)

Figura 7. Função definida pelo utilizador em utilização dentro do [executar Script do Python] [ execute-python-script] módulo.


## <a name="working-with-visualizations"></a>Trabalhar com visualizações

Gráficos criados com o MatplotLib, que pode ser visualizado no navegador podem ser devolvidos pela [executar Script do Python][execute-python-script]. Mas os gráficos não for redirecionados automaticamente para imagens conforme forem ao utilizar o R. Portanto, o utilizador tem explicitamente de guardar quaisquer gráficos para ficheiros PNG se eles devem ser devolvidos ao Azure Machine Learning Studio. 

Para gerar imagens de MatplotLib, tem de concluir o procedimento a seguir:

* Mude o back-end para "AGG" do processador baseado em Qt padrão 
* criar um novo objeto de figura 
* Obtenha o eixo e gerar todos os gráficos na mesma 
* Guarde a figura num ficheiro PNG 

Este processo é ilustrado na figura 8 seguinte que cria uma matriz de gráfico de dispersão usando a função de scatter_matrix no Pandas.

![image1v](./media/execute-python-scripts/figure-v1-8.png)

Figura 8. Código para salvar as figuras de MatplotLib em imagens.

Figura 9 mostra uma experimentação que utiliza o script mostrado anteriormente para retornar desenha via a segunda porta de saída.

![image2v](./media/execute-python-scripts/figure-v2-9a.png) 

![image2v](./media/execute-python-scripts/figure-v2-9b.png) 

Figura 9. Visualização de gráficos gerados a partir do código de Python.

É possível retornar vários valores por salvá-los em imagens diferentes, o tempo de execução do Azure Machine Learning Studio seleciona todas as imagens e concatena-os para visualização.


## <a name="advanced-examples"></a>Exemplos avançados

O ambiente de Anaconda instalado no Azure Machine Learning Studio contém pacotes comuns, como NumPy, SciPy e Scikits de aprender. Esses pacotes podem ser usados com eficiência para várias tarefas de processamento de dados num pipeline de aprendizagem automática. Por exemplo, a experimentação seguinte e o script ilustram o uso de aprendizes ensemble no Scikits de aprender para calcular as pontuações de importância de funcionalidade para um conjunto de dados. As pontuações podem ser utilizadas para efetuar a seleção de funcionalidades supervisionado antes de a ser inserida em outro modelo de ML.

Segue-se a função de Python utilizada para calcular as pontuações de importância e os recursos com base nas pontuações de ordem:

![image11](./media/execute-python-scripts/figure8.png)

Figura 10. A função de recursos de classificação por pontuações.
 
Experimentação seguinte, em seguida, calcula e devolve as pontuações de importância dos recursos no conjunto de dados "Pima Rúpia Diabetes" no Azure Machine Learning Studio:

![image12](./media/execute-python-scripts/figure9a.png)
![image13](./media/execute-python-scripts/figure9b.png)    

Figura 11. Experimente a funcionalidades de classificação no conjunto de dados Pima Rúpia Diabetes.

## <a name="limitations"></a>Limitações
O [executar Script do Python] [ execute-python-script] atualmente tem as seguintes limitações:

1. *Execução em área restrita.* O tempo de execução do Python é atualmente restrita e, consequentemente, não permite o acesso à rede ou para o sistema de arquivos local de forma persistente. Todos os ficheiros guardados localmente são isolados e eliminados depois do módulo for concluída. O código de Python não é possível aceder a maioria dos diretórios na máquina que é executada, a exceção que está a ser o diretório atual e de seus subdiretórios.
2. *Falta de desenvolvimento sofisticado e a depuração de suporte.* O módulo de Python não suporta atualmente recursos do IDE, como o intellisense e depuração. Além disso, se o módulo falhar em tempo de execução, o rastreio de pilha de Python completo está disponível. Mas tem de ser visualizado no registo de saída para o módulo. Atualmente, é recomendável que desenvolve e depurar scripts do Python num ambiente, como o IPython e, em seguida, importar o código para o módulo.
3. *Saída do quadro de dados individual.* O ponto de entrada de Python apenas é permitido para retornar um quadro de dados única como saída. Não é atualmente possível devolver objetos arbitrários de Python, tais como modelos de formação diretamente ao tempo de execução do Azure Machine Learning Studio. Como [executar Script R][execute-r-script], que tem a mesma limitação, é possível em muitos casos pickle objetos numa matriz de bytes e, em seguida, retornará essa dentro de um quadro de dados.
4. *Incapacidade de personalizar a instalação do Python*. Atualmente, a única forma de adicionar módulos personalizados do Python é por meio do mecanismo de arquivo zip descrito anteriormente. Embora isso seja viável para pequenos módulos, torna-se complexo para módulos grandes (especialmente aqueles com DLLs nativas) ou um grande número de módulos. 

## <a name="conclusions"></a>Conclusões
O [executar Script do Python] [ execute-python-script] módulo permite que um cientista de dados para incorporar o código de Python existente em fluxos de trabalho de aprendizagem na cloud alojado no Azure Machine Learning Studio e, a forma totalmente integrada operacionalize-os como parte de um serviço web. O módulo de script de Python naturalmente interopera com outros módulos no Azure Machine Learning Studio. O módulo pode ser utilizado para uma variedade de tarefas de exploração de dados para processamento prévio de padrões e a extração de funcionalidade e, em seguida, para avaliação e pós-processamento dos resultados. O tempo de execução de back-end utilizado para a execução se baseia em Anaconda, uma distribuição de Python bem testada e amplamente utilizada. Este back-end torna simples para acumular ativos de código existentes para a cloud.

Esperamos que fornecer funcionalidade adicional para o [executar Script do Python] [ execute-python-script] módulo, como a capacidade de dar formação e operacionalizar modelos em Python e para adicionar um suporte melhor para o desenvolvimento e depuração de código no Azure Machine Learning Studio.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações, consulte o [Centro para Programadores do Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
