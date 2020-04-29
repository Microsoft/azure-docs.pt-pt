---
title: Tutorial - crie e execute um caderno Jupyter - Pré-visualização de cadernos Azure
description: Aprenda a criar e executar um caderno Jupyter em Azure Notebooks Preview que demonstre o processo de regressão linear na ciência dos dados.
ms.topic: tutorial
ms.date: 01/11/2019
ms.openlocfilehash: 8a1c13f41ef1588b040b3540b852d83764c6ce79
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75660822"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Tutorial: crie e execute um caderno Jupyter com Python

Este tutorial acompanha-o através do processo de utilização de Cadernos Azure para criar um caderno jupyter completo que demonstra uma simples regressão linear. No decorrer deste tutorial, familiariza-se com o II do caderno Jupyter, que inclui a criação de diferentes células, células correntes e a apresentação do caderno como uma apresentação de diapositivos.

O caderno completo pode ser encontrado no [GitHub - Amostras de Cadernos Azure](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps). Este tutorial, no entanto, começa com um novo projeto e um caderno vazio para que possa experimentar criá-lo passo a passo.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um caderno de projeto com alguns dados de amostra
> * Use a interface do portátil para criar uma variedade de tipos de células
> * Executar o bloco de notas
> * Salve o caderno
> * Desinvejo o caderno no Código do Estúdio Visual

## <a name="create-the-project"></a>Criar o projeto

1. Vá aos [Cadernos Azure](https://notebooks.azure.com) e inscreva-se. (Para mais detalhes, consulte [Quickstart - Inscreva-se nos Cadernos Azure).](quickstart-sign-in-azure-notebooks.md)

1. Na sua página de perfil público, selecione **My Projects** no topo da página:

    ![My Projects link no topo da janela do navegador](media/quickstarts/my-projects-link.png)

1. Na página **My Projects,** selecione **+ Novo Projeto** (atalho de teclado: n); o botão só **+** pode aparecer como se a janela do navegador fosse estreita:

    ![Novo comando do Projeto na página my projects](media/quickstarts/new-project-command.png)

1. No popup **Create New Project** que aparece, introduza ou detetete os seguintes detalhes, em seguida, selecione **Criar:**

   - **Nome do projeto**: Exemplo de Regressão Linear - Chilrear de Críquete
   - **Id do projeto**: exemplo linear de regressão
   - **Projeto público**: (apurado)
   - **Criar um README.md:**(limpo)

1. Após alguns momentos, os Cadernos Azure navegam para o novo projeto.

## <a name="create-the-data-file"></a>Criar o ficheiro de dados

O modelo linear de regressão que cria no caderno extrai dados de um ficheiro do seu projeto chamado *cricket_chirps.csv*. Pode criar este ficheiro copiando-o a partir de Amostras de [Cadernos GitHub - Azure,](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)ou através da introdução direta dos dados. As seguintes secções descrevem ambas as abordagens.

### <a name="upload-the-data-file"></a>Faça upload do ficheiro de dados

1. No seu painel de instrumentos de projeto em Cadernos Azure, selecione **Upload** > **From URL**
1. No popup, introduza o URL seguinte no URL de **ficheiro** e *cricket_chirps.csv* no Nome do **Ficheiro**e, em seguida, selecione **Done**.

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. O ficheiro *cricket_chirps.csv* deve agora figurar na lista de ficheiros do seu projeto:

    ![Arquivo CSV recém-criado mostrando na lista de ficheiros do projeto](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Criar um ficheiro do zero

1. No seu painel de instrumentos de projeto em Cadernos Azure, selecione **+ New** > **Blank File**
1. Um campo aparece na lista de ficheiros do projeto. Introduza *cricket_chirps.csv* e prima Enter.
1. Clique à direita *cricket_chirps.csv* e **selecione Editar File**.
1. No editor que aparece, introduza os seguintes dados:

    ```csv
    Chirps/Minute,Temperature
    20,88.6
    16,71.6
    19.8,93.3
    18.4,84.3
    17.1,80.6
    15.5,75.2
    14.7,69.7
    17.1,82
    15.4,69.4
    16.2,83.3
    15,79.6
    17.2,82.6
    16,80.6
    17,83.5
    14.4,76.3
    ```

1. Selecione **Guardar Ficheiro** para guardar o ficheiro e voltar ao painel de instrumentos do projeto.

## <a name="install-project-level-packages"></a>Instalar pacotes de nível de projeto

Dentro de um bloco de notas, pode sempre utilizar comandos como `!pip install` numa célula de código para instalar as embalagens necessárias. No entanto, tais comandos são executados cada vez que executa as células de código do caderno, e pode levar um tempo considerável. Por esta razão, pode, em vez disso, instalar pacotes ao nível do projeto utilizando um `requirements.txt` ficheiro.

1. Utilize o processo descrito em Criar um ficheiro `requirements.txt` de [raiz](#create-a-file-from-scratch) para criar um ficheiro nomeado com o seguinte conteúdo:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    Também pode fazer `requirements.txt` o upload de um ficheiro do seu computador local, se preferir, conforme descrito no [Upload do ficheiro de dados](#upload-the-data-file).

1. No painel de instrumentos do projeto, selecione **Definições de Projeto**.
1. No popup que aparece, selecione o separador **Ambiente** e, em seguida, selecione **+Adicionar**.
1. No primeiro controlo de queda (a operação) em **passos**de configuração do ambiente, escolha **Requirements.txt**.
1. No segundo controlo de entrega (o nome do ficheiro), escolha *requisitos.txt* (o ficheiro que criou).
1. No terceiro controlo de queda (a versão Python), escolha **a Versão Python 3.6**.
1. Selecione **Guardar**.

![O separador Ambiente de Definições do Projeto especificando um ficheiro requisitos.txt](media/tutorial/tutorial-requirements-txt.png)

Com este passo de configuração, qualquer caderno que execute no projeto será executado num ambiente onde esses pacotes estão instalados.

## <a name="create-and-run-a-notebook"></a>Criar e executar um bloco de notas

Com o ficheiro de dados pronto e o conjunto de ambiente do projeto, agora pode criar e abrir o caderno.

1. No painel de instrumentos do projeto, selecione **+ Novo** > **Caderno**.
1. No popup, insira *Linear Regression Exemplo - Cricket Chirps.ipynb* for **Item Name**, escolha **Python 3.6** para o idioma, em seguida, selecione **New**.
1. Depois de aparecer o novo caderno na lista de ficheiros, selecione-o para iniciar o caderno. Um novo separador de navegador abre automaticamente.
1. Como tem um ficheiro *requisitos.txt* nas definições ambientais, vê a mensagem: "À espera que o seu recipiente termine de ser preparado." Pode selecionar **OK** para fechar a mensagem e continuar a trabalhar no caderno; no entanto, não se pode executar células de código até que o ambiente esteja totalmente configurado.
1. O caderno abre na interface Jupyter com uma única célula de código vazia como padrão.

    [![Vista inicial de um novo caderno em Cadernos Azure](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Visite a interface do caderno

Com o caderno em execução, pode adicionar código e células Markdown, executar essas células e gerir o funcionamento do caderno. Primeiro, no entanto, vale a pena demorar alguns minutos para se familiarizar com a interface. Para obter documentação completa, selecione o comando do menu ajuda do Caderno **de Ajuda.** > **Notebook Help**

Ao longo da parte superior da janela vê-se os seguintes itens:

(A) O nome do seu caderno, que pode editar clicando.
(B) Botões para navegar para o projeto contendo e o seu painel de projetos, que abrem novos separadores no seu navegador.
(C) Um menu com comandos para trabalhar com o caderno.
D Uma barra de ferramentas com atalhos para operações comuns.
(E) a tela de edição contendo células.
(F) indicador de se o caderno é fidedigno (o predefinido não é **fiável).**
(G) o núcleo utilizado para executar o caderno juntamente com um indicador de atividade.

[![Áreas primárias de UI da interface Jupyter](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter proporciona um passeio embutido dos elementos primários da UI. Inicie o passeio selecionando o comando **Help** > **User Interface Tour** e clicando nos popups.

Os grupos de comandos de menu são os seguintes:

| Menu | Descrição |
| --- | --- |
| Ficheiro | Comandos para gerir o ficheiro de caderno, incluindo comandos para criar e copiar cadernos, mostrar uma pré-visualização de impressão e baixar o caderno em vários formatos. |
| Editar | Os comandos típicos para cortar, copiar e colar células, encontrar e substituir valores, gerir anexos celulares e inserir imagens.  |
| Vista | Ordena controlar a visibilidade de diferentes partes da UI jupyter. |
| Inserir | Ordena a inserção de uma nova célula acima ou abaixo da célula atual. Usa-se estes comandos com frequência ao criar um caderno. |
| Cell | Os vários comandos **Run** executam uma ou mais células em diferentes combinações. Os comandos **do Tipo Celular** alteram o tipo de célula entre **Código,** **Markdown**e **Raw NBConvert** (texto simples). As **saídas atuais** e **os comandos de todas as saídas** controlam a forma como a saída do código de execução é mostrada e incluem um comando para limpar toda a saída. |
| Kernel | Comandos para gerir como o código está sendo executado no núcleo, juntamente com **change kernel** para alterar a linguagem ou versão Python usado para executar o caderno. |
| Dados | Comandos para fazer upload e download de ficheiros do projeto ou da sessão. Ver [Trabalhar com ficheiros](work-with-project-data-files.md) de dados do projeto |
| Widgets | Comandos para gerir [Jupyter Widgets](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html), que fornecem capacidades adicionais para visualização, mapeamento e conspiração.|
| Ajuda | Comandos que fornecem ajuda e documentação para a interface Jupyter. |

A maioria dos comandos na barra de ferramentas têm comandos de menu equivalentes. Uma exceção é **Enter/Edit RISE Slideshow,** que é discutido em [Share e apresentar cadernos](present-jupyter-notebooks-slideshow.md).

Usa-se alguns destes comandos enquanto povoa o caderno nas secções que se seguem.

## <a name="create-a-markdown-cell"></a>Criar uma célula De markdown

1. Clique na primeira célula vazia mostrada na tela do caderno. Por padrão, uma célula é um tipo **código,** o que significa que foi concebida para conter código runnável para o núcleo selecionado (Python, R ou F#). O tipo atual é mostrado no tipo de queda na barra de ferramentas:

    ![Barra de ferramentas tipo pilha cai](media/tutorial/tutorial-cell-type-drop-down.png)

1. Mude o tipo de célula para **Markdown** utilizando a barra de ferramentas que cai; alternadamente, utilize o comando do menu > **Demarcação** do > **Tipo** **Celular:**

    ![Comando de menu tipo celular](media/tutorial/tutorial-cell-type-menu.png)

1. Clique na célula para começar a editar e, em seguida, introduza o seguinte Markdown:

    ```markdown
    # Example Linear Regression

    This notebook contains a walkthrough of a simple linear regression. The data, obtained from [college.cengage.com](https://college.cengage.com/mathematics/brase/understandable_statistics/7e/students/datasets/slr/frames/frame.html), relates the rate of cricket chirps to temperature from *The Song of Insects*, by Dr. G. W. Pierce, Harvard College Press.

    In this example we're using the count of chirps per minute as the independent varible to then predict the dependent variable, temperature. In short, we're using a little data science to make ourselves a cricket thermometer. (You could also reverse the data and use temperature to predict the number of chirps, but it's more fun to use crickets as the thermometer itself!)

    The methods shown in this example follow what's presented in the Udemy course, [Machine Learning A to Z](https://www.udemy.com/machinelearning/learn/v4/).

    A lovely aspect of Notebooks is that you can use Markdown cells to explain what the code is doing rather than code comments. There are several benefits to doing so:

    - Markdown allows for richer text formatting, like *italics*, **bold**, `inline code`, hyperlinks, and headers.
    - Markdown cells automatically word wrap whereas code cells do not. Code comments typically use explicit line breaks for formatting, but that's not necessary in Markdown.
    - Using Markdown cells makes it easier to run the Notebook as a slide show.
    - Markdown cells help you remove lengthy comments from the code, making the code easier to scan.

    When you run a code cell, Jupyter executes the code; when you run a Markdown cell, Jupyter renders all the formatting into text that's suitable for presentation.
    ```

1. Para tornar o Markdown em HTML para o navegador, selecione o comando **Executar** na barra de ferramentas ou utilize o comando **Cell** > **Run Cells.** O código Markdown para formatação e links aparece agora como espera num navegador.

1. Quando executas a última célula do caderno, o Jupyter cria automaticamente uma nova célula abaixo da que geriste. Coloque mais Markdown nesta célula repetindo os passos nesta secção com o seguinte Markdown:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Para editar novamente o Markdown, clique duas vezes na célula renderizada. Para tornar o HTML novamente após fazer alterações, executar a célula.

## <a name="create-a-code-cell-with-commands"></a>Criar uma célula de código com comandos

Como explicou a célula Markdown anterior, pode incluir comandos diretamente no caderno. Pode utilizar comandos para instalar pacotes, correr caracol ou wget para recuperar dados, ou qualquer outra coisa. Os cadernos jupyter funcionam eficazmente dentro de uma máquina virtual Linux, por isso tem o conjunto completo de comandolinux para trabalhar.

1. Introduza os comandos abaixo na célula de código que apareceu depois de ter usado **run** na célula Markdown anterior. Se não vir uma nova célula, crie uma com **+** insert **insert** > cell**below** ou use o botão na barra de ferramentas.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. Antes de executar a célula, **+** crie uma nova célula com o botão na barra de ferramentas, detetetete-a para Markdown e introduza a seguinte explicação:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Selecione o comando **Cell** > **Run Todos** os comandos para executar todas as células do caderno. Note que as células Markdown são renderizadas como HTML, e o comando corre no núcleo, e observe o indicador de kernel como descrito no próprio Markdown:

    ![Indicador ocupado para o núcleo de caderno](media/tutorial/tutorial-kernel-busy.png)

1. Também leva algum tempo para `pip install` que todos os comandos sejam executados, e porque já instalou estes pacotes no ambiente do projeto (e porque também estão incluídos nos Cadernos Azure por padrão), vê muitas mensagens que lêem: "Requisito já satisfeito". Toda esta saída pode ser visualmente distraída, por isso selecione essa célula (usando um único clique), em seguida, use as saídas de **células** > **celulares** > **Toggle** para ocultar a saída. Também pode utilizar o comando **Clear** no mesmo submenu para remover completamente a saída.

    O comando **Toggle** esconde apenas a saída mais recente da célula; se voltar a executar a célula, a saída reaparece.

1. Uma vez que as embalagens estão instaladas no ambiente do projeto, comente os `! pip install` comandos utilizando; `#` desta forma podem permanecer no caderno como material instrutivo, mas não levarão tempo a correr e não produzirão uma saída desnecessária. Neste caso, manter os comandos comentados no caderno também indica as dependências do caderno.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>Criar as células restantes

Para povoar o resto do caderno, cria-se uma série de células De Markdown e código. Para cada célula listada abaixo, primeiro crie a nova célula, depois desloque o tipo e, em seguida, comente o conteúdo.

Embora possa esperar para executar o caderno depois de criar cada célula, é interessante executar cada célula à medida que a cria. Nem todas as células mostram saída; se não vir erros, assuma que a célula funcionou normalmente.

Cada célula de código depende do código que foi executado em células anteriores, e se você negligenciar a execução de uma das células, as células posteriores podem produzir erros. Se descobrir que esqueceu de gerir uma célula, tente usar a **Célula** > **Executar Tudo Acima** antes de executar a célula atual.

Se vir resultados inesperados (o que provavelmente verá!), verifique se cada célula está definida para "Código" ou "Markdown" conforme necessário. Por exemplo, um erro de "sintaxe inválido" ocorre normalmente quando se entra no Markdown na célula Código.

1. Célula de marcação:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Célula de código; quando executado, mostra o conteúdo da tabela como saída. Pode suprimir a saída comentando `print` a declaração.

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > Pode ver avisos deste código sobre "tamanho numpy.dtype alterado"; os avisos podem ser ignorados com segurança.

1. Célula de marcação:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Célula de código; quando executado, esta célula não tem saída.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Célula de marcação:

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept_`).
    ```

1. Célula de código; quando executado, esta célula `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)`mostra a saída, .

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Célula de marcação:

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. Célula de código; quando executado, esta célula `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]`mostra resultados como .

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Célula de marcação:

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Célula de marcação:

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. Célula de código; quando executado, esta célula produz um enredo gráfico. Se não vir o enredo da primeira vez (e em vez disso ver "Figura tamanho 640x480 com 1 Machados"), volte a executar a célula.

    ```python
    import matplotlib.pyplot as plt

    plt.scatter(X_train, y_train, color = 'green')
    plt.scatter(X_test, y_test, color = 'red')   
    plt.scatter(X_test, y_pred, color = 'blue')  # The predicted temperatures of the same X_test input.
    plt.plot(X_train, regressor.predict(X_train), color = 'gray')
    plt.title('Temperature based on chirp count')
    plt.xlabel('Chirps/minute')
    plt.ylabel('Temperature')
    plt.show()
    ```

    ![Saída de enredo do código matplotlib](media/tutorial/tutorial-plot-output.png)

1. Célula de marcação:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Limpar saídas e reexecutar todas as células

Depois de seguir os passos na secção anterior para povoar todo o caderno, criou um código de execução no contexto de um tutorial completo sobre regressão linear. Esta combinação direta de código e texto é uma das grandes vantagens dos cadernos!

Tente reexecutar todo o caderno agora:

1. Limpe todos os dados da sessão do kernel e toda a saída celular selecionando **Kernel** > **Restart & Clear Output**. Este comando é sempre bom para executar quando se completa um caderno, apenas para ter certeza de que não criou nenhuma dependência estranha entre células de código.

1. Reexecutar o caderno utilizando **cell** > **run all**. Note que o indicador de kernel está preenchido enquanto o código está em funcionamento.

    Se tiver algum código que se prolonge por muito tempo ou se ficar preso, pode parar o núcleo utilizando o comando **Kernel** > **Interrupt.**

1. Percorra o caderno para examinar os resultados. (Se, mais uma vez, o enredo não aparecer, repita a célula.)

## <a name="save-halt-and-close-the-notebook"></a>Salvar, parar e fechar o caderno

Durante o tempo em que está a editar um portátil, pode guardar o seu estado atual com o comando **de** > **guardar ficheiros e de verificação** de dados ou o botão de salvamento na barra de ferramentas. Um "checkpoint" cria um instantâneo a que pode voltar a qualquer momento durante a sessão. Os pontos de verificação permitem-lhe fazer uma série de alterações experimentais, e se essas alterações não funcionarem, pode simplesmente voltar a um ponto de verificação utilizando o comando **'Rever' para o Checkpoint** do **Ficheiro.** >  Uma abordagem alternativa é criar células extras e comentar qualquer código que não queira executar; de qualquer forma funciona.

Também pode utilizar o comando **File** > **Make a Copy** a qualquer momento para fazer uma cópia do estado atual do caderno num novo ficheiro no seu projeto. Essa cópia abre automaticamente num novo separador de navegador.

Quando terminar com um caderno, use o comando De Perto de **Ficheiros** > **e pare,** que fecha o caderno e desliga o núcleo que o tem gerido. Os Cadernos Azure fecham automaticamente o separador do navegador.

## <a name="debug-notebooks-using-visual-studio-code"></a>Depuração de cadernos usando código de estúdio visual

Se as células de código do seu caderno não se comportarem da forma que espera, poderá ter bugs de código ou outros defeitos. No entanto, `print` além de usar declarações para mostrar o valor das variáveis, um ambiente típico de Jupyter não oferece nenhuma instalação de depuração.

Felizmente, pode descarregar o ficheiro *.ipynb* do caderno e, em seguida, abri-lo no Código do Estúdio Visual utilizando a extensão Python. A extensão importa diretamente um caderno como um único ficheiro de código, preservando as suas células Markdown em comentários. Depois de importar o caderno, pode usar o código do estúdio visual para passar pelo seu código, definir pontos de rutura, examinar o estado, e assim por diante. Depois de espaçar o seu código, exporta o ficheiro *.ipynb* do Visual Studio Code e envia-o de volta para os Cadernos Azure.

Para mais informações, consulte [debug um caderno Jupyter](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) na documentação do Código do Estúdio Visual.

Consulte também [o Visual Studio Code - Suporte jupyter](https://code.visualstudio.com/docs/python/jupyter-support) para funcionalidades adicionais do Código do Estúdio Visual para os cadernos Jupyter.

## <a name="next-steps"></a>Passos seguintes

- [Explore os cadernos de amostras](azure-notebooks-samples.md)

Artigos:

- [Criar e clonar projetos](create-clone-jupyter-notebooks.md)
- [Configurar e gerir projetos](configure-manage-azure-notebooks-projects.md)
- [Instale pacotes dentro de um caderno](install-packages-jupyter-notebook.md)
- [Apresentar diapositivos](present-jupyter-notebooks-slideshow.md)
- [Trabalhar com ficheiros de dados](work-with-project-data-files.md)
- [Aceder a recursos de dados](access-data-resources-jupyter-notebooks.md)
- [Utilizar o Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
