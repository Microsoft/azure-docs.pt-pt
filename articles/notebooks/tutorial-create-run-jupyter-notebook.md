---
title: Tutorial - criar e executar um bloco de notas do Jupyter no Azure
description: Como criar uma execução de um bloco de notas do Jupyter em blocos de notas do Azure que demonstra o processo de regressão linear em ciência de dados.
ms.topic: tutorial
ms.date: 01/11/2019
ms.openlocfilehash: 2c151cb0de2855856e92d9de07ad7dabfda2f55b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277414"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Tutorial: criar e executar um bloco de notas do Jupyter com Python

Este tutorial explica-lhe o processo de uso de blocos de notas do Azure para criar um bloco de notas Jupyter completo que demonstra a regressão linear simple. No decorrer neste tutorial, familiarize-se com o bloco de notas do Jupyter da interface do Usuário, que inclui a criação de células diferentes, execução de células e apresentar o bloco de notas como uma apresentação de slides.

O notebook concluído pode ser encontrado em [exemplos de Azure notebooks do GitHub](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps). No entanto, neste tutorial, começa com um novo projeto e um bloco de notas vazio para que pode assistir a criá-lo passo a passo.

## <a name="create-the-project"></a>Criar o projeto

1. Vá para [Azure notebooks](https://notebooks.azure.com) e entre. (Para obter detalhes, consulte [início rápido-entrar no Azure notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na página de seu perfil público, selecione **meus projetos** na parte superior da página:

    ![Meu link de projetos na parte superior da janela do browser](media/quickstarts/my-projects-link.png)

1. Na página **meus projetos** , selecione **+ novo projeto** (atalho de teclado: n); o botão pode aparecer somente como **+** se a janela do navegador for estreita:

    ![Comando de novo projeto na página meus projetos](media/quickstarts/new-project-command.png)

1. No pop-up **criar novo projeto** que aparece, insira ou defina os seguintes detalhes e, em seguida, selecione **criar**:

    - **Nome do projeto**: exemplo de regressão linear – Cricket chirps
    - **ID do projeto**: linear-regressão-exemplo
    - **Projeto público**: (desmarcado)
    - **Criar um README.MD**: (desmarcado)

1. Após alguns instantes, o Azure blocos de notas navega para o novo projeto.

## <a name="create-the-data-file"></a>Criar o ficheiro de dados

O modelo de regressão linear que você cria no bloco de anotações desenha dados de um arquivo em seu projeto chamado *cricket_chirps. csv*. Você pode criar esse arquivo copiando-o de [exemplos do GitHub-Azure notebooks](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)ou inserindo os dados diretamente. As secções seguintes descrevem as duas abordagens.

### <a name="upload-the-data-file"></a>Carregue o ficheiro de dados

1. No painel do projeto no Azure Notebooks, selecione **carregar** > **da URL**
1. No pop-up, insira a URL a seguir na **URL do arquivo** e *cricket_chirps. csv* no **nome do arquivo**e selecione **concluído**.

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. O arquivo *cricket_chirps. csv* agora deve aparecer na lista de arquivos do seu projeto:

    ![Recentemente criado o ficheiro CSV Mostrar na lista de ficheiros de projeto](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Crie um ficheiro a partir do zero

1. No painel do projeto no Azure Notebooks, selecione **+ novo** > **arquivo em branco**
1. Um campo é apresentado na lista de ficheiros do projeto. Insira *cricket_chirps. csv* e pressione Enter.
1. Clique com o botão direito do mouse em *cricket_chirps. csv* e selecione **Editar arquivo**.
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

1. Selecione **salvar arquivo** para salvar o arquivo e retornar ao painel do projeto.

## <a name="install-project-level-packages"></a>Instalar pacotes de nível de projeto

Em um notebook, você sempre pode usar comandos como `!pip install` em uma célula de código para instalar os pacotes necessários. No entanto, esses comandos são executados sempre que executar células de código ao bloco de notas e pode levar um tempo considerável. Por esse motivo, você pode, em vez disso, instalar pacotes no nível do projeto usando um arquivo de `requirements.txt`.

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

![O separador de ambiente de definições do projeto especificando um ficheiro Requirements. txt](media/tutorial/tutorial-requirements-txt.png)

Com este passo de configuração no local, qualquer bloco de notas que é executar o projeto será executado num ambiente em que esses pacotes são instalados.

## <a name="create-and-run-a-notebook"></a>Criar e executar um bloco de notas

Com o ficheiro de dados pronto e o conjunto de ambiente de projeto, pode criar e abrir o bloco de notas.

1. No painel do projeto, selecione **+ novo** > **bloco de anotações**.
1. No pop-up, insira o *exemplo de regressão linear-Cricket chirps. ipynb* para o **nome do item**, escolha **Python 3,6** para o idioma e, em seguida, selecione **novo**.
1. Depois do bloco de notas novo é apresentado na lista de ficheiros, selecione-o para iniciar o bloco de notas. Um novo separador do browser abre-se automaticamente.
1. Como você tem um arquivo *requirements. txt* nas configurações do ambiente, verá a mensagem "aguardando que o contêiner termine de ser preparado". Você pode selecionar **OK** para fechar a mensagem e continuar trabalhando no bloco de anotações; no entanto, você não pode executar células de código até que o ambiente esteja configurado completamente.
1. O bloco de notas abre-se na interface do Jupyter com uma célula de código vazio único como predefinição.

    [![exibição inicial de um novo bloco de anotações no Azure Notebooks](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Realizar uma visita guiada da interface do bloco de notas

Com o bloco de notas em execução, pode adicionar código células de Markdown, execute essas células e gerir a operação do bloco de notas. Em primeiro lugar, no entanto, vale a pena levando alguns minutos para se familiarizar com a interface. Para obter a documentação completa, **Selecione o comando de menu ajuda do** > **Notebook** .

Na parte superior da janela, verá os seguintes itens:

(A) o nome do bloco de anotações, o que pode editar clicando.
(B) botões para navegar para o projeto que contém e o seu dashboard de projetos, que abrir novas guias no seu browser.
Menu de R (C) com os comandos para trabalhar com o bloco de notas.
(D) uma barra de ferramentas com atalhos para operações comuns.
(I) a tela de edição que contém células.
(F) indica se o bloco de anotações é confiável (o padrão **não é confiável**).
(G) o kernel utilizado para executar o bloco de notas, juntamente com um indicador de atividade.

[![áreas da interface do usuário primária da interface Jupyter](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter fornece uma apresentação incorporada dos principais elementos da interface do Usuário. Inicie o Tour selecionando o comando **ajuda** > **Tour da interface do usuário** e clicando nos pop-ups.

Os grupos de comandos de menu são os seguintes:

| Menu | Descrição |
| --- | --- |
| Ficheiro | Comandos para gerir o ficheiro de bloco de notas, incluindo comandos para criar e copiar blocos de notas, mostram uma pré-visualização de impressa e Baixe o bloco de notas numa variedade de formatos. |
| Editar | Comandos típicos a cortar, copiar e colar células, localizar e substituir valores, gerir os anexos de célula e inserir as imagens.  |
| Vista | Comandos para controlar a visibilidade dos diferentes partes da interface do Usuário do Jupyter. |
| Inserir | Os comandos para inserir uma nova célula acima ou abaixo a célula atual. Utilizar estes comandos com frequência na criação de um bloco de notas. |
| Célula | Os vários comandos **Run** executam uma ou mais células em combinações diferentes. Os comandos de **tipo de célula** alteram o tipo de uma célula entre **código**, **redução**e **NBConvert bruto** (texto sem formatação). Os comandos Outputs **atuais** e **todos os saídas** controlam como a saída do código de execução é mostrada e inclui um comando para limpar toda a saída. |
| Kernel | Comandos para gerenciar como o código está sendo executado no kernel, juntamente com o **kernel de alteração** para alterar o idioma ou a versão do Python usada para executar o bloco de anotações. |
| Dados | Comandos para carregar e transferir ficheiros do projeto ou sessão. Consulte [trabalhar com arquivos de dados do projeto](work-with-project-data-files.md) |
| Widgets | Comandos para gerenciar os [widgets do Jupyter](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html), que fornecem recursos adicionais para visualização, mapeamento e plotagem.|
| Ajuda | Comandos que fornecem ajuda e documentação para a interface do Jupyter. |

A maioria dos comandos na barra de ferramentas tem comandos de menu equivalente. Uma exceção é **entrar/editar a apresentação de slides de elevação**, que é abordada em [blocos de anotações de compartilhamento e apresentação](present-jupyter-notebooks-slideshow.md).

Utilize um número desses comandos conforme preenche o bloco de notas em seções a seguir.

## <a name="create-a-markdown-cell"></a>Criar uma célula de Markdown

1. Clique na primeira célula vazia mostrada na tela do bloco de notas. Por padrão, uma célula é um tipo de **código** , o que significa que ele foi projetado para conter código executável para o kernel selecionado (Python, F#R ou). O tipo atual é mostrado no tipo de lista suspensa na barra de ferramentas:

    ![Barra de ferramentas do tipo de célula pendente](media/tutorial/tutorial-cell-type-drop-down.png)

1. Altere o tipo de célula para **redução** usando a lista suspensa barra de ferramentas; Como alternativa, use a **célula** > **tipo de célula** > comando de menu de **redução** :

    ![Comando de menu do tipo de célula](media/tutorial/tutorial-cell-type-menu.png)

1. Clique na célula para começar a editar, em seguida, introduza o seguinte Markdown:

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

1. Para renderizar a redução em HTML para o navegador, selecione o comando **executar** na barra de ferramentas ou use a **célula** > comando **executar células** . O código de Markdown para formatar e links agora aparecer como esperaria que eles num navegador.

1. Quando executa a última célula no bloco de notas, o Jupyter cria automaticamente uma nova célula abaixo aquele que executou. Coloca mais Markdown nessa célula, ao repetir os passos nesta secção com o seguinte Markdown:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Para editar o Markdown novamente, faça duplo clique na célula renderizada. Para compor HTML depois de efetuar alterações, execute a célula.

## <a name="create-a-code-cell-with-commands"></a>Criar uma célula de código com comandos

Conforme explicado na célula de Markdown anterior, pode incluir comandos diretamente no bloco de notas. Pode usar comandos para instalar pacotes, execute o curl ou wget para recuperar dados, ou qualquer outra coisa. Blocos de notas do Jupyter efetivamente executam dentro de uma máquina virtual do Linux, para que tenha o comando completo do Linux, definido para funcionar com.

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

1. Selecione a **célula** > comando **executar tudo** para executar todas as células no bloco de anotações. Tenha em atenção que as células de Markdown compostos como HTML e o comando Executar no kernel e observe o indicador de kernel, tal como descrito no Markdown em si:

    ![Indicador de ocupado para o kernel de bloco de notas](media/tutorial/tutorial-kernel-busy.png)

1. Também leva um pouco de tempo para que todos os comandos de `pip install` sejam executados, e como você já instalou esses pacotes no ambiente do projeto (e como eles também estão incluídos em Azure Notebooks por padrão), você vê muitas mensagens que lêem "requisito já satisfeito. " Toda essa saída pode ser visualmente confuso, portanto, selecione essa venda (usando um único clique) e, em seguida, use a **célula** > as **saídas de célula** > **alternar** para ocultar a saída. Você também pode usar o comando **Clear** no mesmo submenu para remover totalmente a saída.

    O comando de **alternância** oculta apenas a saída mais recente da célula; Se você executar a célula novamente, a saída será exibida novamente.

1. Como os pacotes são instalados no ambiente do projeto, comente os comandos do `! pip install` usando `#`; dessa forma, eles podem permanecer no bloco de anotações como material de ensino, mas não levarão tempo para executar e não produzirão saída desnecessária. Neste caso, manter os comandos comentados no bloco de notas também indica as dependências do bloco de notas.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>Criar as células restantes

Para preencher o restante do bloco de notas, em seguida criar uma série de células de Markdown e o código. Para cada célula listada abaixo, primeiro criar nova célula, em seguida, definir o tipo de, em seguida, cole o conteúdo.

Embora pode esperar para executar o bloco de notas depois de criar cada célula, é interessante executar cada célula como criá-la. Nem todas as células mostram saída; Se não vir algum erro, partem do princípio de que célula foi executado normalmente.

Cada célula de código depende do código foi executada em células anteriores e, se não executar das células, células posteriores podem produzir erros. Se você achar que esqueceu de executar uma célula, tente usar a **célula** > **executar tudo acima** antes de executar a célula atual.

Se vir resultados inesperados (o que provavelmente vai!), verifique se cada célula está definida, como "Código" ou "Markdown", conforme necessário. Por exemplo, um erro de "Sintaxe inválida" ocorre normalmente quando introduzir Markdown na célula de código.

1. Célula de markdown:

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
    > Poderá ver avisos desse código sobre o "tamanho de numpy.dtype alterado"; os avisos podem ser ignorados com segurança.

1. Célula de markdown:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Célula de código; quando executada, essa célula não tem nenhuma saída.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Célula de markdown:

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

1. Célula de markdown:

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

1. Célula de markdown:

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Célula de markdown:

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. Célula de código; quando executada, essa célula produz um desenho de gráfico. Se não vir o tempo de plotagem a primeira (e em vez disso, consulte o "Tamanho figura 640 x 480 com eixos 1"), execute novamente a célula.

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

    ![Saída de desenho do código matplotlib](media/tutorial/tutorial-plot-output.png)

1. Célula de markdown:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Limpar as saídas e volte a executar todas as células

Depois de seguir os passos na secção anterior para preencher todo o bloco de notas, criou tanto uma parte da execução de código no contexto de um tutorial completo sobre regressão linear. Esta combinação direta de código e o texto é uma das grandes vantagens para blocos de notas!

Tente executar novamente o bloco de notas todo agora:

1. Limpe todos os dados de sessão do kernel e todas as saídas de célula selecionando **kernel** > **reiniciar & limpar saída**. Este comando é sempre uma boa para ser executada quando tiver concluído um bloco de notas, apenas para certificar-se de que ainda não criou quaisquer dependências estranhas entre células de código.

1. Execute novamente o bloco de anotações usando a **célula** > **executar tudo**. Observe que o indicador de kernel é preenchido durante a execução de código.

    Se você tiver algum código que seja executado por muito tempo ou que se torne preso, você poderá interromper o kernel usando o comando **kernel** > **Interrupt** .

1. Percorra o bloco de notas para examinar os resultados. (Se novamente o desenho não aparecer, volte a executar essa célula.)

## <a name="save-halt-and-close-the-notebook"></a>Guardar, parar e fechar o bloco de notas

Durante o tempo em que você está editando um bloco de anotações, você pode salvar seu estado atual com o **arquivo** > **salvar e** o comando de ponto de verificação ou o botão salvar na barra de ferramentas. Um "ponto de verificação" cria um instantâneo que pode reverter para a qualquer momento durante a sessão. Os pontos de verificação permitem fazer uma série de alterações experimentais e, se essas alterações não funcionarem, você pode simplesmente reverter para um ponto de verificação usando o **arquivo** > **reverter para o comando de ponto de verificação** . Uma abordagem alternativa é criar células Extras e comente em qualquer código que não pretende executar; de qualquer forma funciona.

Você também pode usar o **arquivo** > **fazer um** comando de cópia a qualquer momento para fazer uma cópia do estado atual do bloco de anotações em um novo arquivo em seu projeto. Essa cópia abre-se automaticamente num novo separador do browser.

Quando você terminar com um bloco de anotações, use o comando **arquivo** > **fechar e parar** , que fecha o bloco de anotações e desliga o kernel que o está executando. Blocos de notas do Azure, em seguida, fecha-se o separador do browser automaticamente.

## <a name="debug-notebooks-using-visual-studio-code"></a>Depurar os blocos de notas com o Visual Studio Code

Se as células de código em seu bloco de anotações não se comportam como esperado, pode ter bugs de código ou outros defeitos. No entanto, além de usar instruções de `print` para mostrar o valor de variáveis, um ambiente típico de Jupyter não oferece nenhuma característica de depuração.

Felizmente, você pode baixar o arquivo *. ipynb* do notebook e, em seguida, abri-lo no Visual Studio Code usando a extensão do Python. A extensão importa diretamente um bloco de anotações como um único arquivo de código, preservando suas células de redução em comentários. Depois de importar o bloco de anotações, você poderá usar o depurador de Visual Studio Code para percorrer seu código, definir pontos de interrupção, examinar o estado e assim por diante. Depois de fazer correções no código, exporte o arquivo *. ipynb* de Visual Studio Code e carregue-o novamente no Azure notebooks.

Para obter mais informações, consulte [depurar um notebook Jupyter](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) na documentação do Visual Studio Code.

Consulte também [suporte a Visual Studio Code-Jupyter](https://code.visualstudio.com/docs/python/jupyter-support) para obter recursos adicionais de Visual Studio Code para notebooks Jupyter.

## <a name="next-steps"></a>Passos seguintes

- [Explorar blocos de anotações de exemplo](azure-notebooks-samples.md)

Artigos de procedimentos:

- [Criar e clonar projetos](create-clone-jupyter-notebooks.md)
- [Configurar e gerenciar projetos](configure-manage-azure-notebooks-projects.md)
- [Instalar pacotes de dentro de um bloco de anotações](install-packages-jupyter-notebook.md)
- [Apresentar uma apresentação de slides](present-jupyter-notebooks-slideshow.md)
- [Trabalhar com arquivos de dados](work-with-project-data-files.md)
- [Acessar recursos de dados](access-data-resources-jupyter-notebooks.md)
- [Usar Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
