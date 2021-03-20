---
title: Use Python e TensorFlow para aprendizagem automática em Azure
description: Utilize funções Python, TensorFlow e Azure com um modelo de aprendizagem automática para classificar uma imagem com base no seu conteúdo.
author: anthonychu
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: antchu
ms.custom: mvc, devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: ca9ce27583168dfee1a597fce559afad38a3a8c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994609"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Tutorial: Aplicar modelos de aprendizagem automática em funções Azure com Python e TensorFlow

Neste artigo, aprende-se a usar as Funções Python, TensorFlow e Azure com um modelo de machine learning para classificar uma imagem com base no seu conteúdo. Como você faz todos os trabalhos locais e não cria recursos Azure na nuvem, não há nenhum custo para completar este tutorial.

> [!div class="checklist"]
> * Inicialize um ambiente local para o desenvolvimento de Funções Azure em Python.
> * Importe um modelo de aprendizagem automática tensorFlow personalizado para uma aplicação de função.
> * Construa uma API HTTP sem servidor para classificar uma imagem como contendo um cão ou um gato.
> * Consumir a API numa aplicação Web.

## <a name="prerequisites"></a>Pré-requisitos 

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Python 3.7.4](https://www.python.org/downloads/release/python-374/). (Python 3.7.4 e Python 3.6.x são verificados com Funções Azure; Python 3.8 e versões posteriores ainda não estão suportadas.)
- As [ferramentas centrais de funções Azure](functions-run-local.md#install-the-azure-functions-core-tools)
- Um editor de código como [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Verificação pré-requisito

1. Numa janela de terminal ou comando, corra `func --version` para verificar se as Ferramentas Centrais de Funções Azure são a versão 2.7.1846 ou posterior.
1. Executar `python --version` (Linux/MacOS) ou `py --version` (Windows) para verificar os relatórios da sua versão Python 3.7.x.

## <a name="clone-the-tutorial-repository"></a>Clone o repositório tutorial

1. Numa janela terminal ou de comando, clone o seguinte repositório utilizando Git:

    ```
    git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
    ```

1. Navegue na pasta e examine o seu conteúdo.

    ```
    cd functions-python-tensorflow-tutorial
    ```

    - *iniciar* é a sua pasta de trabalho para o tutorial.
    - *fim* é o resultado final e implementação completa para a sua referência.
    - *os recursos* contêm o modelo de aprendizagem automática e bibliotecas auxiliares.
    - *frontend* é um site que chama a app de função.
    
## <a name="create-and-activate-a-python-virtual-environment"></a>Criar e ativar um ambiente virtual Python

Navegue para a pasta *inicial* e execute os seguintes comandos para criar e ativar um ambiente virtual chamado `.venv` . Certifique-se de que utiliza python 3.7, que é suportado por Azure Functions.


# <a name="bash"></a>[festa](#tab/bash)

```bash
cd start
```

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Se python não instalou o pacote venv na sua distribuição Linux, execute o seguinte comando:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
```

```powershell
py -3.7 -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
```

```cmd
py -3.7 -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Você dirige todos os comandos subsequentes neste ambiente virtual ativado. (Para sair do ambiente virtual, `deactivate` corra.)


## <a name="create-a-local-functions-project"></a>Criar um projeto de funções locais

Em Funções Azure, um projeto de função é um recipiente para uma ou mais funções individuais que cada um responde a um gatilho específico. Todas as funções de um projeto partilham as mesmas configurações locais e de hospedagem. Nesta secção, cria-se um projeto de função que contém uma única função de placa de caldeira chamada `classify` que fornece um ponto final HTTP. Adicione um código mais específico numa secção posterior.

1. Na pasta *inicial,* utilize as Ferramentas Principais de Funções Azure para rubricar uma aplicação de função Python:

    ```
    func init --worker-runtime python
    ```

    Após a inicialização, a pasta *inicial* contém vários ficheiros para o projeto, incluindo ficheiros de configurações nomeados [local.settings.jse](functions-run-local.md#local-settings-file) [host.jsem](functions-host-json.md). Como *local.settings.jspode* conter segredos descarregados do Azure, o ficheiro é excluído do controlo de origem por padrão no ficheiro *.gitignore.*

    > [!TIP]
    > Como um projeto de função está ligado a um tempo de execução específico, todas as funções do projeto devem ser escritas com a mesma língua.

1. Adicione uma função ao seu projeto utilizando o seguinte comando, onde o `--name` argumento é o nome único da sua função e o argumento especifica o gatilho da `--template` função. `func new` criar uma sub-dobradeira que corresponda ao nome da função que contém um ficheiro de código adequado à língua escolhida do projeto e um ficheiro de configuração denominado *function.jsem*.

    ```
    func new --name classify --template "HTTP trigger"
    ```

    Este comando cria uma pasta que corresponde ao nome da função, *classifica .* Nesta pasta *\_ \_ encontram-se \_ \_* dois ficheiros: .py , que contém o código de função, e *function.js,* que descreve o gatilho da função e as suas ligações de entrada e saída. Para obter mais informações sobre o conteúdo destes ficheiros, consulte [examinar o conteúdo do ficheiro](./create-first-function-cli-python.md#optional-examine-the-file-contents) no arranque rápido do Python.


## <a name="run-the-function-locally"></a>Executar a função localmente

1. Inicie a função iniciando o hospedeiro local de tempo de funcionamento do Azure Functions na pasta *inicial:*

    ```
    func start
    ```
    
1. Assim que vir o `classify` ponto final aparecer na saída, navegue para o URL, ```http://localhost:7071/api/classify?name=Azure``` . A mensagem "Olá Azure!" deve aparecer na saída.

1. Use **ctrl** - **C** para parar o hospedeiro.


## <a name="import-the-tensorflow-model-and-add-helper-code"></a>Importe o modelo TensorFlow e adicione o código de ajuda

Para modificar a `classify` função para classificar uma imagem com base no seu conteúdo, utiliza um modelo tensorFlow pré-construído que foi treinado e exportado do Azure Custom Vision Service. O modelo, que está contido na pasta de *recursos* da amostra que clonou anteriormente, classifica uma imagem baseada no facto de conter um cão ou um gato. Em seguida, adicione algum código de ajuda e dependências ao seu projeto.

Para construir o seu próprio modelo utilizando o nível gratuito do Serviço de Visão Personalizada, siga as instruções do [repositório](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md)do projeto de amostra.

> [!TIP]
> Se pretender hospedar o seu modelo TensorFlow independentemente da aplicação de função, pode, em vez disso, montar uma partilha de ficheiros contendo o seu modelo na sua aplicação de função Linux. Para saber mais, consulte [mount a file share to a Python function app using Azure CLI](./scripts/functions-cli-mount-files-storage-linux.md).

1. Na pasta *inicial,* corra seguindo o comando para copiar os ficheiros do modelo na pasta *de classificação.* Certifique-se de incluir `\*` no comando. 

    # <a name="bash"></a>[festa](#tab/bash)
    
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
    
1. Verifique se a pasta *de classificação* contém ficheiros nomeados *model.pb* e *labels.txt*. Caso contrário, verifique se executou o comando na pasta *inicial.*

1. Na pasta *inicial,* executar o seguinte comando para copiar um ficheiro com código auxiliar na pasta *de classificação:*

    # <a name="bash"></a>[festa](#tab/bash)
    
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

1. Verifique se a pasta *de classificação* agora contém um ficheiro nomeado *predict.py*.

1. Abrir *o início/requirements.txt* num editor de texto e adicionar as seguintes dependências exigidas pelo código de ajuda:

    ```txt
    tensorflow==1.14
    Pillow
    requests
    ```
    
1. Salve *requirements.txt.*

1. Instale as dependências executando o seguinte comando na pasta *inicial.* A instalação pode demorar alguns minutos, durante o qual pode proceder à modificação da função na secção seguinte.

    ```
    pip install --no-cache-dir -r requirements.txt
    ```
    
    No Windows, pode encontrar o erro: "Não foi possível instalar pacotes devido a um EnvironmentError: [Errno 2] Nenhum ficheiro ou diretório deste tipo:" seguido de um longo nome de pathname para um ficheiro como *sharded_mutable_dense_hashtable.cpython-37.pyc*. Normalmente, este erro acontece porque a profundidade do caminho da pasta torna-se demasiado longa. Neste caso, estale a chave de registo `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` para `1` permitir caminhos longos. Em alternativa, verifique onde está instalado o seu intérprete Python. Se essa localização tiver um longo caminho, tente reinstalar-se numa pasta com um caminho mais curto.

> [!TIP]
> Ao apelar *predict.py* para fazer a sua primeira previsão, uma função chamada `_initialize` carrega o modelo TensorFlow do disco e caches-lo em variáveis globais. Este caching acelera as previsões subsequentes. Para obter mais informações sobre a utilização de variáveis globais, consulte o [guia de desenvolvimento Azure Functions Python](functions-reference-python.md#global-variables).

## <a name="update-the-function-to-run-predictions"></a>Atualizar a função para executar previsões

1. A *classificação aberta/ \_ \_ \_ \_ init .py* num editor de texto e adicione as seguintes linhas após as declarações existentes para importar a biblioteca `import` JSON padrão e os ajudantes *de previsão:*

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Substitua todo o conteúdo da `main` função pelo seguinte código:

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="8-19":::

    Esta função recebe um URL de imagem num parâmetro de cadeia de consulta denominado `img` . Em seguida, chama `predict_image_from_url` da biblioteca de ajudantes para descarregar e classificar a imagem usando o modelo TensorFlow. Em seguida, a função devolve uma resposta HTTP com os resultados. 

    > [!IMPORTANT]
    > Como este ponto final HTTP é chamado por uma página web hospedada noutro domínio, a resposta inclui um `Access-Control-Allow-Origin` cabeçalho para satisfazer os requisitos de Partilha de Recursos de Origem Cruzada (CORS) do navegador.
    >
    > Numa aplicação de produção, altere `*` a origem específica da página web para uma maior segurança.

1. Guarde as suas alterações e, em seguida, assumindo que as dependências terminaram de instalar, inicie novamente o anfitrião da função local com `func start` . Certifique-se de que funciona o hospedeiro na pasta *inicial* com o ambiente virtual ativado. Caso contrário, o anfitrião começará, mas verá erros ao invocar a função.

    ```
    func start
    ```

1. Num browser, abra o seguinte URL para invocar a função com o URL de uma imagem de gato e confirme que o JSON devolvido classifica a imagem como um gato.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
    ```
    
1. Mantenha o hospedeiro a funcionar porque o usa no próximo passo. 

### <a name="run-the-local-web-app-front-end-to-test-the-function"></a>Executar a linha frontal da aplicação web local para testar a função

Para testar a invocação do ponto final da função a partir de outra aplicação web, há uma aplicação simples na *pasta* frontal do repositório.

1. Abra um novo terminal ou pedido de comando e ative o ambiente virtual (como descrito anteriormente no [Create e ativar um ambiente virtual Python).](#create-and-activate-a-python-virtual-environment)

1. Navegue para a *pasta* frontal do repositório.

1. Inicie um servidor HTTP com Python:

    # <a name="bash"></a>[festa](#tab/bash)

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

1. Num browser, navegue para `localhost:8000` , em seguida, introduza um dos urLs fotográficos seguintes na caixa de texto, ou use o URL de qualquer imagem acessível ao público.

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`
    
1. **Selecione Submeter-se** a invocar o ponto final da função para classificar a imagem.

    ![Screenshot do projeto acabado](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

    Se o navegador reportar um erro ao submeter o URL de imagem, verifique o terminal em que está a executar a aplicação de função. Se vir um erro como "Nenhum módulo encontrado 'PIL'", pode ter iniciado a aplicação de função na pasta *inicial* sem primeiro ativar o ambiente virtual que criou anteriormente. Se ainda vir erros, volte a correr `pip install -r requirements.txt` com o ambiente virtual ativado e procure erros.

> [!NOTE]
> O modelo classifica sempre o conteúdo da imagem como um gato ou um cão, independentemente de a imagem conter ou não, desresposição do cão. Imagens de tigres e panteras, por exemplo, tipicamente classificam-se como gatos, mas imagens de elefantes, cenouras ou aviões classificam-se como cães.

## <a name="clean-up-resources"></a>Limpar os recursos

Como todo este tutorial funciona localmente na sua máquina, não existem recursos ou serviços Azure para limpar.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a construir e personalizar um ponto final http API com Funções Azure para classificar as imagens utilizando um modelo TensorFlow. Também aprendeu a chamar a API de uma aplicação web. Você pode usar as técnicas neste tutorial para construir APIs de qualquer complexidade, tudo enquanto executa no modelo de computação sem servidor fornecido pela Azure Functions.

> [!div class="nextstepaction"]
> [Implementar a função para funções Azure utilizando o Guia Azure CLI](./functions-run-local.md#publish)

Veja também:

- [Implementar a função para Azure utilizando o Código do Estúdio Visual](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Guia de desenvolvedores de funções Azure](./functions-reference-python.md)
- [Monte uma partilha de ficheiros para uma aplicação de função Python usando O Azure CLI](./scripts/functions-cli-mount-files-storage-linux.md)
