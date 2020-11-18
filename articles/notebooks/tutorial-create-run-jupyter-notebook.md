---
title: Tutorial - crie e execute um Caderno Jupyter - Pré-visualização de cadernos Azure
description: Saiba como criar e executar um Caderno Jupyter em Azure Notebooks Preview que demonstra o processo de regressão linear na ciência dos dados.
ms.topic: tutorial
ms.date: 01/11/2019
ms.custom: devx-track-python
ms.openlocfilehash: 47e9a07c7b8abffc06bfd30a792af46ba04adf5c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844476"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Tutorial: criar e executar um Caderno Jupyter com Python

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Este tutorial acompanha-o através do processo de utilização de Cadernos Azure para criar um Notebook Jupyter completo que demonstra uma simples regressão linear. No decorrer deste tutorial, você se familiariza com o Jupyter Notebook UI, que inclui a criação de diferentes células, células de execução, e apresentando o caderno como uma apresentação de diapositivos.

O caderno completo pode ser encontrado em [Amostras de Cadernos GitHub - Azure Notebooks](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps). Este tutorial, no entanto, começa com um novo projeto e um caderno vazio para que você possa experimentar criá-lo passo a passo.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Criar um caderno de projeto com alguns dados da amostra
> * Use a interface do portátil para criar uma variedade de tipos de células
> * Executar o bloco de notas
> * Guarde o caderno
> * Depurar o caderno no Código do Estúdio Visual

## <a name="create-the-project"></a>Criar o projeto

1. Vá aos [Cadernos Azure](https://notebooks.azure.com) e inscreva-se. (Para mais detalhes, consulte [Quickstart - Inicie súm em Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na sua página de perfil público, selecione **Os Meus Projetos** no topo da página:

    ![O meu link de projetos no topo da janela do navegador](media/quickstarts/my-projects-link.png)

1. Na página **My Projects,** selecione **+ Novo Projeto** (atalho de teclado: n); o botão só pode aparecer como **+** se a janela do navegador fosse estreita:

    ![Novo comando do projeto na página My Projects](media/quickstarts/new-project-command.png)

1. No popup **Create New Project** que aparece, introduz ou define os seguintes detalhes e, em seguida, selecione **Criar**:

   - **Nome do projeto**: Exemplo linear de regressão - Cricket Chirps
   - **ID do projeto**: exemplo linear-regressão
   - **Projeto público**: (limpo)
   - **Criar um README.md:**(limpo)

1. Após alguns momentos, a Azure Notebooks navega para o novo projeto.

## <a name="create-the-data-file"></a>Criar o ficheiro de dados

O modelo linear de regressão que cria no caderno extrai dados de um ficheiro do seu projeto chamado *cricket_chirps.csv*. Pode criar este ficheiro copiando-o a partir de [Amostras de Cadernos GitHub - Azure,](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)ou introduzindo os dados diretamente. As seguintes secções descrevem ambas as abordagens.

### <a name="upload-the-data-file"></a>Faça o upload do ficheiro de dados

1. No painel de instrumentos do seu projeto em Azure Notebooks, **selecione Upload**  >  **From URL**
1. No popup, introduza o seguinte URL em **URL de ficheiro** e *cricket_chirps.csv* no Nome **de Ficheiro,** selecione **'Fazer'**

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. O *ficheirocricket_chirps.csv* deve agora constar da lista de ficheiros do seu projeto:

    ![Arquivo CSV recentemente criado mostrando na lista de ficheiros do projeto](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Criar um arquivo a partir do zero

1. No painel de instrumentos do seu projeto em Azure Notebooks, **selecione + Novo** Ficheiro Em  >  **Branco**
1. Um campo aparece na lista de ficheiros do projeto. Introduza *cricket_chirps.csv* e prima Enter.
1. *Clique* cricket_chirps.csvà direita e selecione **Editar File**.
1. No editor que aparece, insira os seguintes dados:

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

1. **Selecione Guardar ficheiro** para guardar o ficheiro e regressar ao painel de instrumentos do projeto.

## <a name="install-project-level-packages"></a>Instalar pacotes de nível de projeto

Dentro de um caderno, pode sempre usar comandos como `!pip install` numa célula de código para instalar as embalagens necessárias. No entanto, tais comandos são executados cada vez que você executar as células de código do caderno, e pode levar um tempo considerável. Por esta razão, pode instalar pacotes ao nível do projeto utilizando um `requirements.txt` ficheiro.

1. Utilize o processo descrito na [Criar um ficheiro a partir do zero](#create-a-file-from-scratch) para criar um ficheiro com o seguinte `requirements.txt` conteúdo:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    Também pode fazer o upload de um `requirements.txt` ficheiro a partir do seu computador local, se preferir, conforme descrito no Upload do ficheiro de [dados](#upload-the-data-file).

1. No painel de instrumentos do projeto, selecione **Definições de Projeto**.
1. No popup que aparece, selecione o **separador Ambiente** e, em seguida, selecione **+Adicionar**.
1. No primeiro controlo de queda (a operação) em **Etapas de Configuração do Ambiente,** escolha **Requirements.txt**.
1. No segundo controlo de entrega (o nome do ficheiro), escolha *requirements.txt* (o ficheiro que criou).
1. No terceiro controlo de drop-down (a versão Python), escolha **a Versão Python 3.6**.
1. Selecione **Guardar**.

![O separador Ambiente de Definições de Projeto especificando um ficheiro requirements.txt](media/tutorial/tutorial-requirements-txt.png)

Com este passo de configuração no lugar, qualquer caderno que você executar no projeto será executado em um ambiente onde esses pacotes estão instalados.

## <a name="create-and-run-a-notebook"></a>Criar e executar um bloco de notas

Com o ficheiro de dados pronto e o conjunto de ambiente do projeto, pode agora criar e abrir o caderno.

1. No painel de instrumentos do projeto, selecione **+**  >  **Novo Caderno.**
1. No pop-up, insira *o Exemplo linear de regressão - Cricket Chirps.ipynb* para o nome do **item,** escolha **Python 3.6** para o idioma e, em seguida, selecione **New**.
1. Depois de aparecer o novo caderno na lista de ficheiros, selecione-o para iniciar o portátil. Um novo separador de navegador abre automaticamente.
1. Como tem um ficheiro *requirements.txt* nas definições ambientais, vê a mensagem: "À espera que o seu contentor termine de ser preparado." Pode selecionar **OK** para fechar a mensagem e continuar a trabalhar no caderno; não é possível executar células de código, no entanto, até que o ambiente seja totalmente configurado.
1. O caderno abre na interface Jupyter com uma única célula de código vazia como padrão.

    [![Vista inicial de um novo caderno em Cadernos Azure](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Visite a interface do portátil

Com o caderno em funcionamento, pode adicionar células code e Markdown, executar essas células e gerir o funcionamento do caderno. Primeiro, porém, vale a pena demorar alguns minutos a familiarizar-se com a interface. Para obter documentação **Help** completa, selecione o comando do menu  >  **Ajuda do Bloco de Ajuda.**

Ao longo da parte superior da janela vê-se os seguintes itens:

(A) O nome do seu caderno, que pode editar clicando.
(B) Botões para navegar para o projeto contendo e o painel de instrumentos dos seus projetos, que abrem novos separadores no seu navegador.
(C) Um menu com comandos para trabalhar com o caderno.
(D) uma barra de ferramentas com atalhos para operações comuns.
(E) a tela de edição que contém células.
(F) indicador de se o caderno é confiável (o padrão não é **fidedigno**).
(G) o núcleo utilizado para executar o caderno juntamente com um indicador de atividade.

[![Áreas primárias de UI da interface Jupyter](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter fornece um tour incorporado dos elementos primários da UI. Inicie o tour selecionando o comando **Help**  >  **User Interface Tour** e clicando através dos popups.

Os grupos de comandos de menu são os seguintes:

| Menu | Descrição |
| --- | --- |
| Ficheiro | Os comandos para gerir o ficheiro do portátil, incluindo comandos para criar e copiar cadernos, mostrar uma pré-visualização de impressão e descarregar o caderno em vários formatos. |
| Editar | Comandos típicos para cortar, copiar e colar células, encontrar e substituir valores, gerir os anexos celulares e inserir imagens.  |
| Vista | Comandos para controlar a visibilidade de diferentes partes da UI Jupyter. |
| Inserir | Ordena a inserção de uma nova célula acima ou abaixo da célula atual. Utiliza estes comandos frequentemente ao criar um caderno. |
| Cell | Os vários comandos **Run** executam uma ou mais células em diferentes combinações. Os comandos **do Tipo de Célula** alteram o tipo de célula entre **Código**, **Markdown** e **Raw NBConvert** (texto simples). Os **comandos Current Outputs** e **All Outputs** controlam a forma como a saída do código de execução é mostrada e inclui um comando para limpar toda a saída. |
| Kernel | Comandos para gerir como o código está sendo executado no núcleo, juntamente com **Change kernel** para alterar a versão idioma ou Python usada para executar o caderno. |
| Dados | Comandos para carregar e descarregar ficheiros do projeto ou sessão. Ver [Trabalhar com ficheiros de dados do projeto](work-with-project-data-files.md) |
| Widgets | Comandos para gerir [Jupyter Widgets,](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html)que fornecem capacidades adicionais para visualização, mapeamento e conspiração.|
| Ajuda | Comandos que fornecem ajuda e documentação para a interface Jupyter. |

A maioria dos comandos na barra de ferramentas tem comandos de menu equivalentes. Uma exceção é **Enter/Edit RISE Slideshow,** que é discutido em [Partilhar e apresentar cadernos.](present-jupyter-notebooks-slideshow.md)

Usa-se vários destes comandos à medida que preenche o caderno nas secções que se seguem.

## <a name="create-a-markdown-cell"></a>Criar uma célula Markdown

1. Clique na primeira célula vazia mostrada na tela do caderno. Por predefinição, uma célula é um tipo de **código,** o que significa que foi concebida para conter código runível para o núcleo selecionado (Python, R ou F#). O tipo atual é indicado no tipo de queda na barra de ferramentas:

    ![Queda da barra de ferramentas do tipo de pilha](media/tutorial/tutorial-cell-type-drop-down.png)

1. Altere o tipo de célula para **Markdown** utilizando a barra de ferramentas para baixo; alternadamente, **Cell** utilize o comando do  >  menu de marcação do tipo **de célula**  >  **celular:**

    ![Comando do menu do tipo de pilha](media/tutorial/tutorial-cell-type-menu.png)

1. Clique na célula para começar a editar e, em seguida, insira o seguinte Markdown:

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

1. Para tornar o Markdown em HTML para o navegador, selecione **Cell** o comando **'Executar'** na barra de ferramentas ou utilize o comando  >  **Cell Run Cells.** O código Markdown para formatação e links aparece agora como espera que eles apareçam num browser.

1. Quando executas a última célula no caderno, o Jupyter cria automaticamente uma nova célula abaixo daquela que correste. Coloque mais Markdown nesta célula repetindo os passos nesta secção com o seguinte Markdown:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Para editar novamente o Markdown, clique duas vezes na célula renderizada. Para tornar o HTML novamente depois de es fazer alterações, executar a célula.

## <a name="create-a-code-cell-with-commands"></a>Criar uma célula de código com comandos

Como a célula markdown anterior explicou, pode incluir comandos diretamente no caderno. Pode utilizar comandos para instalar pacotes, executar caracóis ou wget para recuperar dados, ou qualquer outra coisa. Os Cadernos Jupyter funcionam eficazmente dentro de uma máquina virtual Linux, por isso tens o comando Linux completo preparado para trabalhar.

1. Introduza os comandos abaixo na célula de código que apareceu depois de ter usado **Run** na célula Markdown anterior. Se não vir uma nova célula, **Insert** crie uma com  >  **insira a célula abaixo** ou utilize o **+** botão na barra de ferramentas.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. Antes de executar a célula, crie uma nova célula com o **+** botão na barra de ferramentas, coloque-a em Markdown e introduza a seguinte explicação:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Selecione o comando **Cell**  >  **Run All** para executar todas as células do caderno. Note que as células Markdown prestam como HTML, e o comando funciona no núcleo, e observe o indicador do núcleo tal como descrito na própria Marca:

    ![Indicador ocupado para o núcleo do caderno](media/tutorial/tutorial-kernel-busy.png)

1. Também leva algum tempo para que todos os `pip install` comandos sejam executados, e porque já instalou estes pacotes no ambiente do projeto (e porque também estão incluídos nos Cadernos Azure por defeito), vê-se muitas mensagens que dizem: "Requisito já satisfeito". Toda esta saída pode ser distrativa visualmente, por isso selecione **Cell** essa célula (usando um único clique), em seguida, use o  >  Toggle de **saídas de células**  >  **celulares** para ocultar a saída. Também pode utilizar o comando **Clear** no mesmo submenu para remover completamente a saída.

    O comando **Toggle** esconde apenas a saída mais recente da célula; se voltar a executar a célula, a saída reaparece.

1. Como os pacotes estão instalados no ambiente do projeto, comente os `! pip install` comandos `#` usando; desta forma podem permanecer no caderno como material instrutivo, mas não demorarão algum tempo a ser executados e não produzirão saídas desnecessárias. Neste caso, manter os comandos comentados no caderno também indica as dependências do caderno.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>Criar as células restantes

Para preencher o resto do caderno, cria-se uma série de markdown e células de código. Para cada célula listada abaixo, primeiro crie a nova célula, em seguida, desa estalhe o tipo e, em seguida, cole no conteúdo.

Embora possa esperar para executar o caderno depois de criar cada célula, é interessante executar cada célula à medida que o cria. Nem todas as células mostram a produção; se não vir nenhum erro, assuma que a célula correu normalmente.

Cada célula de código depende do código que foi executado em células anteriores, e se você não executar uma das células, as células posteriores podem produzir erros. Se descobrir que se esqueceu de executar uma célula, tente usar a **célula**  >  **Run All Above** antes de executar a célula atual.

Se vir resultados inesperados (o que provavelmente verá!), verifique se cada célula está definida como "Código" ou "Markdown" se necessário. Por exemplo, um erro de "sintaxe inválida" ocorre normalmente quando inseriu o Código na célula Code.

1. Célula de marcação:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Célula de código; quando executado, mostra o conteúdo da tabela como saída. Pode suprimir a saída comentando a `print` declaração.

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

1. Célula de código; quando executado, esta célula mostra a saída, `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)` .

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

1. Célula de código; quando executado, esta célula mostra resultados como `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]` .

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

1. Célula de código; quando executado, esta célula produz um enredo gráfico. Se não vir o enredo pela primeira vez (e em vez disso ver "Figura tamanho 640x480 com 1 Machados"), volte a executar a célula.

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

    ![Saída do enredo do código matplotlib](media/tutorial/tutorial-plot-output.png)

1. Célula de marcação:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Limpar saídas e refazer todas as células

Depois de seguir os passos na secção anterior para povoar todo o caderno, criou um código de execução no contexto de um tutorial completo sobre regressão linear. Esta combinação direta de código e texto é uma das grandes vantagens dos cadernos!

Tente refazer o caderno inteiro agora:

1. Limpe todos os dados de sessão do kernel e toda a saída celular selecionando **Kernel**  >  **Restart & Clear Output**. Este comando é sempre bom de correr quando se completa um caderno, só para ter a certeza de que não criou nenhuma dependência estranha entre as células de código.

1. Reexame o caderno utilizando **Cell**  >  **Run All**. Note que o indicador do núcleo é preenchido enquanto o código está em funcionamento.

    Se tiver algum código que seja executado durante demasiado tempo ou se ficar preso de outra forma, pode parar o núcleo utilizando o comando **Kernel**  >  **Interrupt.**

1. Percorra o caderno para examinar os resultados. (Se, mais uma vez, o enredo não aparecer, volte a repetir a célula.)

## <a name="save-halt-and-close-the-notebook"></a>Poupe, pare e feche o caderno

Durante o período em que está a editar um **File** caderno, pode guardar o seu estado atual com o comando  >  **'Guardar e Checkpoint'** ou com o botão de guardar na barra de ferramentas. Um "checkpoint" cria uma imagem a que pode voltar a qualquer momento durante a sessão. Os pontos de verificação permitem-lhe fazer uma série de alterações experimentais, e se essas alterações não funcionarem, pode simplesmente reverter para um ponto de verificação utilizando o comando **Desatada** de Ficheiro  >  **reverte para Checkpoint.** Uma abordagem alternativa é criar células extras e comentar qualquer código que não queira executar; de qualquer forma funciona.

Também pode utilizar o comando **File**  >  **Make a Copy** a qualquer momento para fazer uma cópia do estado atual do caderno num novo ficheiro no seu projeto. Essa cópia abre automaticamente num novo separador de navegador.

Quando terminar com um caderno, use o comando **'File**  >  **Close' e stop,** que fecha o caderno e desliga o núcleo que o tem feito. Azure Notebooks, em seguida, fecha o separador do navegador automaticamente.

## <a name="debug-notebooks-using-visual-studio-code"></a>Cadernos de depurar usando Código de Estúdio Visual

Se as células de código do seu caderno não se comportarem da forma que espera, poderá ter erros de código ou outros defeitos. No entanto, além de usar `print` declarações para mostrar o valor das variáveis, um ambiente típico do Jupyter não oferece nenhuma instalação de depurar.

Felizmente, pode descarregar o ficheiro *.ipynb* do portátil e depois abri-lo no Código do Estúdio Visual utilizando a extensão Python. A extensão importa diretamente um caderno como um único ficheiro de código, preservando as suas células Markdown em comentários. Uma vez importada o caderno, pode usar o depurador do Código do Estúdio Visual para passar pelo seu código, definir pontos de rutura, examinar o estado, e assim por diante. Depois de fazer correções ao seu código, em seguida, exporta o ficheiro *.ipynb* do Código do Estúdio Visual e envia-o de volta para os Cadernos Azure.

Para mais informações, consulte [Debug a Jupyter Notebook](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) na documentação do Código do Estúdio Visual.

Consulte também o [Código do Estúdio Visual - Suporte jupyter](https://code.visualstudio.com/docs/python/jupyter-support) para funcionalidades adicionais do Código do Estúdio Visual para Cadernos Jupyter.

## <a name="next-steps"></a>Passos seguintes

- [Explore cadernos de amostras](azure-notebooks-samples.md)

Artigos de como fazer:

- [Criar e clonar projetos](create-clone-jupyter-notebooks.md)
- [Configurar e gerir projetos](configure-manage-azure-notebooks-projects.md)
- [Instalar pacotes a partir de um caderno](install-packages-jupyter-notebook.md)
- [Apresentar diapositivos](present-jupyter-notebooks-slideshow.md)
- [Trabalhar com ficheiros de dados](work-with-project-data-files.md)
- [Aceder a recursos de dados](access-data-resources-jupyter-notebooks.md)
- [Utilizar o Azure Machine Learning](../machine-learning/samples-notebooks.md)