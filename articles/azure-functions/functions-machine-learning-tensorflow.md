---
title: Use Python e TensorFlow para machine learning em Azure
description: Utilize as funções Python, TensorFlow e Azure com um modelo de aprendizagem automática para classificar uma imagem com base no seu conteúdo.
author: anthonychu
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: antchu
ms.custom: mvc
ms.openlocfilehash: c64d87b2430cc1d733a67bbc1e803590a37b1714
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190777"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Tutorial: Aplicar modelos de machine learning em funções Azure com Python e TensorFlow

Neste artigo, aprende-se a utilizar as Funções Python, TensorFlow e Azure com um modelo de aprendizagem automática para classificar uma imagem com base nos seus conteúdos. Como todos trabalham localmente e não criam recursos Azure na nuvem, não há qualquer custo para completar este tutorial.

> [!div class="checklist"]
> * Inicialize um ambiente local para o desenvolvimento de Funções Azure em Python.
> * Importe um modelo personalizado de aprendizagem automática TensorFlow numa aplicação de função.
> * Construa um HTTP API sem servidor para classificar uma imagem como contendo um cão ou um gato.
> * Consumir a API a partir de uma aplicação web.

## <a name="prerequisites"></a>Pré-requisitos 

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Python 3.7.4](https://www.python.org/downloads/release/python-374/). (Python 3.7.4 e Python 3.6.x são verificados com funções Azure; As versões Python 3.8 e posteriores ainda não são suportadas.)
- As [ferramentas nucleares das funções azure](functions-run-local.md#install-the-azure-functions-core-tools)
- Um editor de código como [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Verificação pré-requisito

1. Numa janela de terminais ou comando, faça `func --version` para verificar se as Ferramentas Core funções Do Azure são a versão 2.7.1846 ou posterior.
1. Execute `python --version` (Linux/MacOS) ou `py --version` (Windows) para verificar os relatórios da versão Python 3.7.x.

## <a name="clone-the-tutorial-repository"></a>Clone o repositório tutorial

1. Numa janela de terminais ou comando, clone o seguinte repositório utilizando Git:

    ```
    git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
    ```

1. Navegue na pasta e examine o seu conteúdo.

    ```
    cd functions-python-tensorflow-tutorial
    ```

    - *começar* é a sua pasta de trabalho para o tutorial.
    - *final* é o resultado final e implementação completa para a sua referência.
    - *recursos* contém o modelo de aprendizagem automática e bibliotecas auxiliares.
    - *frontend* é um site que chama a app de função.
    
## <a name="create-and-activate-a-python-virtual-environment"></a>Criar e ativar um ambiente virtual Python

Navegue até *à* pasta inicial e execute os seguintes comandos para criar e ativar um ambiente virtual chamado `.venv`. Certifique-se de utilizar python 3.7, que é suportado por Funções Azure.


# <a name="bash"></a>[bash](#tab/bash)

```bash
cd start
```

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Se python não instalou o pacote de veado na sua distribuição Linux, execute o seguinte comando:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
```

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
```

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Executa todos os comandos subsequentes neste ambiente virtual ativado. (Para sair do ambiente virtual, corra `deactivate`.)


## <a name="create-a-local-functions-project"></a>Criar um projeto de funções locais

Nas Funções Azure, um projeto de função é um recipiente para uma ou mais funções individuais que cada um responde a um gatilho específico. Todas as funções de um projeto partilham as mesmas configurações locais e de hospedagem. Nesta secção, cria-se um projeto de função que contém uma única função de placa de caldeira chamada `classify` que fornece um ponto final HTTP. Adicione código mais específico numa secção posterior.

1. Na pasta *inicial,* utilize as Ferramentas Core funções do Azure para inicializar uma aplicação de função Python:

    ```
    func init --worker-runtime python
    ```

    Após a inicialização, a pasta de *início* contém vários ficheiros para o projeto, incluindo ficheiros de configurações chamados [local.settings.json](functions-run-local.md#local-settings-file) e [host.json](functions-host-json.md). Como *as configurações locais.json* podem conter segredos descarregados do Azure, o ficheiro é excluído do controlo de origem por padrão no ficheiro *.gitignore.*

    > [!TIP]
    > Como um projeto de função está ligado a um tempo de execução específico, todas as funções do projeto devem ser escritas com a mesma língua.

1. Adicione uma função ao seu projeto utilizando o seguinte comando, onde o argumento `--name` é o nome único da sua função e o `--template` argumento especifica o gatilho da função. `func new` criar uma subpasta que contenha um ficheiro de código adequado ao idioma escolhido do projeto e um ficheiro de configuração chamado *função.json*.

    ```
    func new --name classify --template "HTTP trigger"
    ```

    Este comando cria uma pasta que corresponde ao nome da função, *classifica.* Nessa pasta encontram-se dois ficheiros: *\_\_inite\_\_.py*, que contém o código de função, e *função.json*, que descreve o gatilho da função e as suas encadernações de entrada e saída. Para obter informações sobre o conteúdo destes ficheiros, consulte [Examinar o conteúdo do ficheiro](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#optional-examine-the-file-contents) no arranque rápido da Python.


## <a name="run-the-function-locally"></a>Executar localmente a função

1. Inicie a função iniciando o hospedeiro de funcionamento das Funções Azure locais na pasta de *início:*

    ```
    func start
    ```
    
1. Assim que vir o ponto final `classify` aparecer na saída, navegue para o URL, ```http://localhost:7071/api/classify?name=Azure```. A mensagem "Olá Azure!" deve aparecer na saída.

1. Utilize **o Ctrl**-**C** para parar o hospedeiro.


## <a name="import-the-tensorflow-model-and-add-helper-code"></a>Importar o modelo TensorFlow e adicionar código de ajuda

Para modificar a função `classify` para classificar uma imagem com base no seu conteúdo, utiliza-se um modelo TensorFlow pré-construído que foi treinado e exportado do Azure Custom Vision Service. O modelo, que está contido na pasta de *recursos* da amostra que clonou anteriormente, classifica uma imagem com base na contenção de um cão ou de um gato. Em seguida, adicione um pouco de código de ajuda e dependências ao seu projeto.

> [!TIP]
> Se pretender construir o seu próprio modelo utilizando o nível livre do Serviço de Visão Personalizada, siga as instruções no [repositório](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md)do projeto de amostra .

1. Na pasta *inicial,* executar o comando seguinte para copiar os ficheiros do modelo na pasta *de classificação.* Certifique-se de incluir `\*` no comando. 

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    cp ../resources/model/* classify
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\model\* classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\model\* classify
    ```
    
    ---
    
1. Verifique se a pasta *de classificação* contém ficheiros *denominados model.pb* e *labels.txt*. Caso contrário, verifique se executou o comando na pasta de *início.*

1. Na pasta *inicial,* execute o seguinte comando para copiar um ficheiro com código de ajudante na pasta *de classificação:*

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    cp ../resources/predict.py classify
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\predict.py classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\predict.py classify
    ```
    
    ---

1. Verifique se a pasta *de classificação* contém agora um ficheiro chamado *predict.py*.

1. Abra *o início/requisitos.txt* num editor de texto e adicione as seguintes dependências exigidas pelo código de ajuda:

    ```txt
    tensorflow==1.14
    Pillow
    requests
    ```
    
1. Guardar *requisitos.txt*.

1. Instale as dependências executando o seguinte comando na pasta de *início.* A instalação pode demorar alguns minutos, durante o qual pode proceder com a modificação da função na secção seguinte.

    ```
    pip install --no-cache-dir -r requirements.txt
    ```
    
    No Windows, pode encontrar o erro: "Não foi possível instalar pacotes devido a um EnvironmentError: [Errno 2] Nenhum ficheiro ou diretório:" seguido de um longo nome de caminho para um ficheiro como *sharded_mutable_dense_hashtable.cpython-37.pyc*. Normalmente, este erro acontece porque a profundidade do caminho da pasta torna-se demasiado longa. Neste caso, detete to da `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` da chave de registo para `1` para permitir longos caminhos. Alternadamente, verifique onde está instalado o seu intérprete Python. Se esse local tiver um longo caminho, tente reinstalar-se numa pasta com um caminho mais curto.

> [!TIP]
> Ao pedir *predict.py* para fazer a sua primeira previsão, uma função chamada `_initialize` carrega o modelo TensorFlow a partir de disco e cache-lo em variáveis globais. Este cache acelera as previsões subsequentes. Para obter mais informações sobre a utilização de variáveis globais, consulte o guia de desenvolvimento de [Funções Azure Python](functions-reference-python.md#global-variables).

## <a name="update-the-function-to-run-predictions"></a>Atualizar a função para executar previsões

1. Abram *a classificação/\_\_inite\_\_.py* num editor de texto e adicione as seguintes linhas após as declarações `import` existentes para importar a biblioteca padrão JSON e os ajudantes *previstos:*

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Substitua todo o conteúdo da função `main` pelo seguinte código:

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="8-19":::

    Esta função recebe um URL de imagem num parâmetro de corda de consulta chamado `img`. Em seguida, chama `predict_image_from_url` da biblioteca de ajudantes para descarregar e classificar a imagem usando o modelo TensorFlow. A função devolve então uma resposta HTTP com os resultados. 

    > [!IMPORTANT]
    > Como este ponto final http é chamado por uma página web hospedada em outro domínio, a resposta inclui um cabeçalho `Access-Control-Allow-Origin` para satisfazer os requisitos de Partilha de Recursos De Origem Cruzada (CORS) do navegador.
    >
    > Numa aplicação de produção, altere `*` a origem específica da página web para uma maior segurança.

1. Guarde as suas alterações, assumindo então que as dependências terminaram de instalar, inicie novamente o hospedeiro de funções local com `func start`. Certifique-se de que executa o hospedeiro na *pasta* inicial com o ambiente virtual ativado. Caso contrário, o hospedeiro começará, mas verá erros ao invocar a função.

    ```
    func start
    ```

1. Num browser, abra o seguinte URL para invocar a função com o URL de uma imagem de gato e confirmar que o JSON devolvido classifica a imagem como um gato.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
    ```
    
1. Mantenha o hospedeiro a funcionar porque o usa no próximo passo. 

### <a name="run-the-local-web-app-front-end-to-test-the-function"></a>Executar a extremidade frontal da aplicação web local para testar a função

Para testar a invocação do ponto final da função a partir de outra aplicação web, há uma aplicação simples na pasta *frontal* do repositório.

1. Abra um novo terminal ou comando e ative o ambiente virtual (como descrito anteriormente em [Create e ative um ambiente virtual Python).](#create-and-activate-a-python-virtual-environment)

1. Navegue para a pasta *frontal* do repositório.

1. Inicie um servidor HTTP com Python:

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash 
    python -m http.server
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    py -m http.server
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -m http.server
    ```

1. Num browser, navegue para `localhost:8000`, em seguida, introduza uma das seguintes URLs fotográficas na caixa de texto, ou utilize o URL de qualquer imagem acessível ao público.

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`
    
1. Selecione **Submeter** para invocar o ponto final da função para classificar a imagem.

    ![Screenshot do projeto acabado](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

    Se o navegador reportar um erro ao submeter o URL de imagem, verifique o terminal em que está a executar a aplicação de funções. Se vir um erro como "Nenhum módulo encontrado 'PIL'", pode ter iniciado a aplicação de funções na *pasta* inicial sem antes ativar o ambiente virtual que criou anteriormente. Se ainda vir erros, volte a `pip install -r requirements.txt` com o ambiente virtual ativado e procure erros.

> [!NOTE]
> O modelo classifica sempre o conteúdo da imagem como um gato ou um cão, independentemente de a imagem conter qualquer um, incumpridor com o cão. Imagens de tigres e panteras, por exemplo, tipicamente classificam como gato, mas imagens de elefantes, cenouras ou aviões classificam-se como cães.

## <a name="clean-up-resources"></a>Limpar recursos

Como todo este tutorial funciona localmente na sua máquina, não existem recursos ou serviços Azure para limpar.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a construir e personalizar um ponto final http API com funções Azure para classificar imagens usando um modelo TensorFlow. Também aprendeu a chamar a API de uma aplicação web. Pode utilizar as técnicas deste tutorial para construir APIs de qualquer complexidade, tudo enquanto executa o modelo de computação sem servidor fornecido pela Azure Functions.

> [!div class="nextstepaction"]
> [Implementar a função para funções Azure utilizando o Guia CLI Azure](./functions-run-local.md#publish)

Veja também:

- [Implemente a função para Azure utilizando o Código do Estúdio Visual](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Guia de desenvolvimento de funções azure Python](./functions-reference-python.md)
