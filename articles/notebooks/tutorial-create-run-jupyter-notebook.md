---
title: Tutorial – criar e executar um notebook Jupyter no Azure
description: Como criar um Jupyter notebook em execução no Azure Notebooks que demonstra o processo de regressão linear na ciência de dados.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 65bbb5fe-9939-4e8e-8f5b-c197d4be142a
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: kraigb
ms.openlocfilehash: 42072894b99ebf1cfcbb5e622c2a9c3a4321a944
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496662"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Tutorial: criar e executar um notebook Jupyter com Python

Este tutorial orienta você pelo processo de uso de Azure Notebooks para criar um notebook Jupyter completo que demonstra uma regressão linear simples. No decorrer deste tutorial, você se familiariza com a interface do usuário do Jupyter notebook, que inclui a criação de células diferentes, a execução de células e a apresentação do bloco de anotações como uma apresentação de slides.

O notebook concluído pode ser encontrado em [exemplos de Azure notebooks do GitHub](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps). No entanto, este tutorial começa com um novo projeto e um bloco de anotações vazio para que você possa experimentar criá-lo passo a passo.

## <a name="create-the-project"></a>Criar o projeto

1. Vá para [Azure notebooks](https://notebooks.azure.com) e entre. (Para obter detalhes, consulte [início rápido-entrar no Azure notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na página de seu perfil público, selecione **meus projetos** na parte superior da página:

    ![Link meus projetos na parte superior da janela do navegador](media/quickstarts/my-projects-link.png)

1. Na página **meus projetos** , selecione **+ novo projeto** (atalho de teclado: n); o botão pode aparecer somente como **+** se a janela do navegador for estreita:

    ![Comando novo projeto na página meus projetos](media/quickstarts/new-project-command.png)

1. No pop-up **criar novo projeto** que aparece, insira ou defina os seguintes detalhes e, em seguida, selecione **criar**:

    - **Nome do projeto**: exemplo de regressão linear – Cricket chirps
    - **ID do projeto**: linear-regressão-exemplo
    - **Projeto público**: (desmarcado)
    - **Criar um README.MD**: (desmarcado)

1. Depois de alguns instantes, Azure Notebooks navega para o novo projeto.

## <a name="create-the-data-file"></a>Criar o arquivo de dados

O modelo de regressão linear que você cria no bloco de anotações desenha dados de um arquivo em seu projeto chamado *cricket_chirps. csv*. Você pode criar esse arquivo copiando-o de [exemplos do GitHub-Azure notebooks](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)ou inserindo os dados diretamente. As seções a seguir descrevem as duas abordagens.

### <a name="upload-the-data-file"></a>Carregar o arquivo de dados

1. No painel do projeto no Azure Notebooks, selecione **carregar** > **da URL**
1. No pop-up, insira a URL a seguir na **URL do arquivo** e *cricket_chirps. csv* no **nome do arquivo**e selecione **concluído**.

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. O arquivo *cricket_chirps. csv* agora deve aparecer na lista de arquivos do seu projeto:

    ![Arquivo CSV recém-criado mostrando na lista de arquivos do projeto](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Criar um arquivo do zero

1. No painel do projeto no Azure Notebooks, selecione **+ novo** > **arquivo em branco**
1. Um campo é exibido na lista de arquivos do projeto. Insira *cricket_chirps. csv* e pressione Enter.
1. Clique com o botão direito do mouse em *cricket_chirps. csv* e selecione **Editar arquivo**.
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

1. Selecione **salvar arquivo** para salvar o arquivo e retornar ao painel do projeto.

## <a name="install-project-level-packages"></a>Instalar pacotes de nível de projeto

Em um notebook, você sempre pode usar comandos como `!pip install` em uma célula de código para instalar os pacotes necessários. No entanto, esses comandos são executados sempre que você executa as células de código do notebook e pode levar um tempo considerável. Por esse motivo, você pode, em vez disso, instalar pacotes no nível do projeto usando um arquivo de `requirements.txt`.

1. Use o processo descrito em [criar um arquivo do zero](#create-a-file-from-scratch) para criar um arquivo chamado `requirements.txt` com o seguinte conteúdo:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    Você também pode carregar um arquivo de `requirements.txt` do seu computador local, se preferir, conforme descrito em [carregar o arquivo de dados](#upload-the-data-file).

1. No painel do projeto, selecione **configurações do projeto**.
1. No pop-up que aparece, selecione a guia **ambiente** e, em seguida, selecione **+ Adicionar**.
1. No primeiro controle suspenso (a operação) em **etapas de configuração do ambiente**, escolha **requirements. txt**.
1. No segundo controle suspenso (o nome do arquivo), escolha *requirements. txt* (o arquivo que você criou).
1. No terceiro controle suspenso (a versão do Python), escolha **Python versão 3,6**.
1. Selecione **Guardar**.

![A guia ambiente de configurações do projeto especificando um arquivo Requirements. txt](media/tutorial/tutorial-requirements-txt.png)

Com essa etapa de instalação em vigor, qualquer bloco de anotações que você executar no projeto será executado em um ambiente em que esses pacotes estão instalados.

## <a name="create-and-run-a-notebook"></a>Criar e executar um bloco de notas

Com o arquivo de dados pronto e o ambiente do projeto definido, agora você pode criar e abrir o bloco de anotações.

1. No painel do projeto, selecione **+ novo** > **bloco de anotações**.
1. No pop-up, insira o *exemplo de regressão linear-Cricket chirps. ipynb* para o **nome do item**, escolha **Python 3,6** para o idioma e, em seguida, selecione **novo**.
1. Depois que o novo bloco de anotações for exibido na lista arquivo, selecione-o para iniciar o bloco de anotações. Uma nova guia do navegador é aberta automaticamente.
1. Como você tem um arquivo *requirements. txt* nas configurações do ambiente, verá a mensagem "aguardando que o contêiner termine de ser preparado". Você pode selecionar **OK** para fechar a mensagem e continuar trabalhando no bloco de anotações; no entanto, você não pode executar células de código até que o ambiente esteja configurado completamente.
1. O notebook é aberto na interface Jupyter com uma única célula de código vazia como padrão.

    [![exibição inicial de um novo bloco de anotações no Azure Notebooks](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Conheça a interface do notebook

Com o bloco de anotações em execução, você pode adicionar código e redução de células, executar essas células e gerenciar a operação do notebook. Primeiro, no entanto, vale a pena levar alguns minutos para se familiarizar com a interface. Para obter a documentação completa, **Selecione o comando de menu ajuda do** > **Notebook** .

Ao longo da parte superior da janela, você verá os seguintes itens:

(A) o nome do bloco de anotações, que pode ser editado clicando em.
(B) botões para navegar até o projeto de contenção e o painel de projetos, que abrem novas guias em seu navegador.
(C) um menu com comandos para trabalhar com o bloco de anotações.
(D) uma barra de ferramentas com atalhos para operações comuns.
(E) a tela de edição que contém células.
(F) indica se o bloco de anotações é confiável (o padrão **não é confiável**).
(G) o kernel usado para executar o bloco de anotações junto com um indicador de atividade.

[![áreas da interface do usuário primária da interface Jupyter](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

O Jupyter fornece um tour interno dos principais elementos da interface do usuário. Inicie o Tour selecionando o comando **ajuda** > **Tour da interface do usuário** e clicando nos pop-ups.

Os grupos de comandos de menu são os seguintes:

| Menu | Descrição |
| --- | --- |
| Ficheiro | Comandos para gerenciar o arquivo de bloco de anotações, incluindo comandos para criar e copiar notebooks, mostrar uma visualização de impressão e baixar o bloco de anotações em vários formatos. |
| Editar | Comandos típicos para recortar, copiar e colar células, localizar e substituir valores, gerenciar anexos de células e inserir imagens.  |
| Ver | Comandos para controlar a visibilidade de diferentes partes da interface do usuário do amJupyter. |
| Inserir | Comandos para inserir uma nova célula acima ou abaixo da célula atual. Use esses comandos com frequência ao criar um bloco de anotações. |
| CÉL | Os vários comandos **Run** executam uma ou mais células em combinações diferentes. Os comandos de **tipo de célula** alteram o tipo de uma célula entre **código**, **redução**e **NBConvert bruto** (texto sem formatação). Os comandos Outputs **atuais** e **todos os saídas** controlam como a saída do código de execução é mostrada e inclui um comando para limpar toda a saída. |
| Kernel | Comandos para gerenciar como o código está sendo executado no kernel, juntamente com o **kernel de alteração** para alterar o idioma ou a versão do Python usada para executar o bloco de anotações. |
| Dados | Comandos para carregar e baixar arquivos do projeto ou da sessão. Consulte [trabalhar com arquivos de dados do projeto](work-with-project-data-files.md) |
| Widgets | Comandos para gerenciar os [widgets do Jupyter](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html), que fornecem recursos adicionais para visualização, mapeamento e plotagem.|
| Ajuda | Comandos que fornecem ajuda e documentação para a interface Jupyter. |

A maioria dos comandos na barra de ferramentas têm comandos de menu equivalentes. Uma exceção é **entrar/editar a apresentação de slides de elevação**, que é abordada em [blocos de anotações de compartilhamento e apresentação](present-jupyter-notebooks-slideshow.md).

Você usa vários desses comandos conforme preenche o notebook nas seções a seguir.

## <a name="create-a-markdown-cell"></a>Criar uma célula de redução

1. Clique na primeira célula vazia mostrada na tela do notebook. Por padrão, uma célula é um tipo de **código** , o que significa que ele foi projetado para conter código executável para o kernel selecionado (Python, F#R ou). O tipo atual é mostrado na lista suspensa tipo na barra de ferramentas:

    ![Menu suspenso da barra de ferramentas de tipo de célula](media/tutorial/tutorial-cell-type-drop-down.png)

1. Altere o tipo de célula para **redução** usando a lista suspensa barra de ferramentas; Como alternativa, use a **célula** > **tipo de célula** > comando de menu de **redução** :

    ![Comando de menu de tipo de célula](media/tutorial/tutorial-cell-type-menu.png)

1. Clique na célula para iniciar a edição e, em seguida, insira a seguinte redução:

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

1. Para renderizar a redução em HTML para o navegador, selecione o comando **executar** na barra de ferramentas ou use a **célula** > comando **executar células** . O código de redução para formatação e links agora aparece da forma esperada em um navegador.

1. Quando você executa a última célula no bloco de anotações, o Jupyter cria automaticamente uma nova célula abaixo da que você executou. Coloque mais redução nessa célula repetindo as etapas nesta seção com a seguinte redução:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Para editar a redução novamente, clique duas vezes na célula renderizada. Para renderizar o HTML novamente após fazer alterações, execute a célula.

## <a name="create-a-code-cell-with-commands"></a>Criar uma célula de código com comandos

Conforme explicado na célula de redução anterior, você pode incluir comandos diretamente no bloco de anotações. Você pode usar comandos para instalar pacotes, executar a rotação ou wget para recuperar dados ou qualquer outra coisa. Os notebooks Jupyter são executados com eficiência em uma máquina virtual Linux, portanto, você tem o conjunto de comandos completo do Linux com o qual trabalhar.

1. Insira os comandos abaixo na célula de código exibida depois que você usou **executar** na célula de redução anterior. Se você não vir uma nova célula, crie uma com **inserir** > **célula de inserção abaixo** ou use o botão **+** na barra de ferramentas.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. Antes de executar a célula, crie uma nova célula com o botão **+** na barra de ferramentas, defina-a como redução e insira a seguinte explicação:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Selecione a **célula** > comando **executar tudo** para executar todas as células no bloco de anotações. Observe que as células de redução são renderizadas como HTML, e o comando é executado no kernel e observa o indicador do kernel, conforme descrito na própria redução:

    ![Indicador de ocupado para o kernel do notebook](media/tutorial/tutorial-kernel-busy.png)

1. Também leva um pouco de tempo para que todos os comandos de `pip install` sejam executados, e como você já instalou esses pacotes no ambiente do projeto (e como eles também estão incluídos em Azure Notebooks por padrão), você vê muitas mensagens que lêem "requisito já satisfeito. " Toda essa saída pode ser visualmente confuso, portanto, selecione essa venda (usando um único clique) e, em seguida, use a **célula** > as **saídas de célula** > **alternar** para ocultar a saída. Você também pode usar o comando **Clear** no mesmo submenu para remover totalmente a saída.

    O comando de **alternância** oculta apenas a saída mais recente da célula; Se você executar a célula novamente, a saída será exibida novamente.

1. Como os pacotes são instalados no ambiente do projeto, comente os comandos do `! pip install` usando `#`; dessa forma, eles podem permanecer no bloco de anotações como material de ensino, mas não levarão tempo para executar e não produzirão saída desnecessária. Nesse caso, manter os comandos comentados no bloco de anotações também indica as dependências do notebook.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>Criar as células restantes

Para preencher o restante do bloco de anotações, você cria uma série de células de redução e de código. Para cada célula listada abaixo, primeiro crie a nova célula e, em seguida, defina o tipo e cole-o no conteúdo.

Embora você possa esperar para executar o bloco de anotações depois de criar cada célula, é interessante executar cada célula ao criá-la. Nem todas as células mostram a saída; Se você não vir nenhum erro, presuma que a célula foi executada normalmente.

Cada célula de código depende do código que foi executado nas células anteriores e, se você não executar uma das células, as células posteriores poderão gerar erros. Se você achar que esqueceu de executar uma célula, tente usar a **célula** > **executar tudo acima** antes de executar a célula atual.

Se você vir resultados inesperados (o que provavelmente vai!), verifique se cada célula está definida como "código" ou "redução", conforme necessário. Por exemplo, um erro de "sintaxe inválida" normalmente ocorre quando você inseriu a redução na célula de código.

1. Célula de redução:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Célula de código; Quando executado, mostra o conteúdo da tabela como saída. Você pode suprimir a saída comentando a instrução `print`.

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > Você pode ver avisos desse código sobre "numpy. dtype tamanho alterado"; os avisos podem ser ignorados com segurança.

1. Célula de redução:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Célula de código; Quando executado, essa célula não tem nenhuma saída.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Célula de redução:

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept_`).
    ```

1. Célula de código; Quando executado, essa célula mostra a saída, `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)`.

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Célula de redução:

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. Célula de código; Quando executado, essa célula mostra resultados como `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]`.

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Célula de redução:

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Célula de redução:

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. Célula de código; Quando executado, essa célula produz um gráfico gráfico. Se você não vir o gráfico pela primeira vez (e, em vez disso, consulte "figura tamanho 640x480 com 1 eixo"), execute a célula novamente.

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

    ![Plotar a saída do código matplotlib](media/tutorial/tutorial-plot-output.png)

1. Célula de redução:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Limpar saídas e executar novamente todas as células

Depois de seguir as etapas na seção anterior para preencher o bloco de anotações inteiro, você criou uma parte do código em execução no contexto de um tutorial completo sobre a regressão linear. Essa combinação direta de código e texto é uma das grandes vantagens dos notebooks!

Tente executar novamente o bloco de anotações inteiro agora:

1. Limpe todos os dados de sessão do kernel e todas as saídas de célula selecionando **kernel** > **reiniciar & limpar saída**. Esse comando é sempre um bom para ser executado quando você conclui um bloco de anotações, apenas para certificar-se de que você não criou nenhuma dependência estranha entre as células de código.

1. Execute novamente o bloco de anotações usando a **célula** > **executar tudo**. Observe que o indicador kernel está preenchido enquanto o código está em execução.

    Se você tiver algum código que seja executado por muito tempo ou que se torne preso, você poderá interromper o kernel usando o comando **kernel** > **Interrupt** .

1. Percorra o bloco de anotações para examinar os resultados. (Se novamente o gráfico não aparecer, execute novamente a célula.)

## <a name="save-halt-and-close-the-notebook"></a>Salvar, parar e fechar o bloco de anotações

Durante o tempo em que você está editando um bloco de anotações, você pode salvar seu estado atual com o **arquivo** > **salvar e** o comando de ponto de verificação ou o botão salvar na barra de ferramentas. Um "ponto de verificação" cria um instantâneo que você pode reverter a qualquer momento durante a sessão. Os pontos de verificação permitem fazer uma série de alterações experimentais e, se essas alterações não funcionarem, você pode simplesmente reverter para um ponto de verificação usando o **arquivo** > **reverter para o comando de ponto de verificação** . Uma abordagem alternativa é criar células extras e comentar qualquer código que você não deseja executar; de qualquer forma funciona.

Você também pode usar o **arquivo** > **fazer um** comando de cópia a qualquer momento para fazer uma cópia do estado atual do bloco de anotações em um novo arquivo em seu projeto. Essa cópia é aberta automaticamente em uma nova guia do navegador.

Quando você terminar com um bloco de anotações, use o comando **arquivo** > **fechar e parar** , que fecha o bloco de anotações e desliga o kernel que o está executando. Azure Notebooks, em seguida, fecha a guia do navegador automaticamente.

## <a name="debug-notebooks-using-visual-studio-code"></a>Depurar blocos de anotações usando Visual Studio Code

Se as células de código no bloco de anotações não se comportarem da maneira esperada, você poderá ter bugs de código ou outros defeitos. No entanto, além de usar instruções de `print` para mostrar o valor de variáveis, um ambiente típico de Jupyter não oferece nenhuma característica de depuração.

Felizmente, você pode baixar o arquivo *. ipynb* do notebook e, em seguida, abri-lo no Visual Studio Code usando a extensão do Python. A extensão importa diretamente um bloco de anotações como um único arquivo de código, preservando suas células de redução em comentários. Depois de importar o bloco de anotações, você poderá usar o depurador de Visual Studio Code para percorrer seu código, definir pontos de interrupção, examinar o estado e assim por diante. Depois de fazer correções no código, exporte o arquivo *. ipynb* de Visual Studio Code e carregue-o novamente no Azure notebooks.

Para obter mais informações, consulte [depurar um notebook Jupyter](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) na documentação do Visual Studio Code.

Consulte também [suporte a Visual Studio Code-Jupyter](https://code.visualstudio.com/docs/python/jupyter-support) para obter recursos adicionais de Visual Studio Code para notebooks Jupyter.

## <a name="next-steps"></a>Passos seguintes

- [Explorar blocos de anotações de exemplo](azure-notebooks-samples.md)

Artigos de instruções:

- [Criar e clonar projetos](create-clone-jupyter-notebooks.md)
- [Configurar e gerenciar projetos](configure-manage-azure-notebooks-projects.md)
- [Instalar pacotes de dentro de um bloco de anotações](install-packages-jupyter-notebook.md)
- [Apresentar uma apresentação de slides](present-jupyter-notebooks-slideshow.md)
- [Trabalhar com arquivos de dados](work-with-project-data-files.md)
- [Acessar recursos de dados](access-data-resources-jupyter-notebooks.md)
- [Usar Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
