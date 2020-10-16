---
title: Implementar um modelo PyTorch como uma aplicação Azure Functions
description: Utilize uma rede neural profunda resNet 18 pré-treinada de PyTorch com Funções Azure para atribuir 1 de 1000 etiquetas ImageNet a uma imagem.
author: gvashishtha
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: gopalv
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: dd8de1b56927e158a181df952ce0dbeed140d6b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89078661"
---
# <a name="tutorial-deploy-a-pre-trained-image-classification-model-to-azure-functions-with-pytorch"></a>Tutorial: Implementar um modelo de classificação de imagem pré-treinado para funções Azure com PyTorch

Neste artigo, aprende-se a usar as Funções Python, PyTorch e Azure para carregar um modelo pré-treinado para classificar uma imagem com base no seu conteúdo. Como você faz todos os trabalhos locais e não cria recursos Azure na nuvem, não há nenhum custo para completar este tutorial.

> [!div class="checklist"]
> * Inicialize um ambiente local para o desenvolvimento de Funções Azure em Python.
> * Importe um modelo de aprendizagem automática PyTorch pré-treinado para uma aplicação de função.
> * Construa uma API HTTP sem servidor para classificar uma imagem como uma das 1000 [classes](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a)ImageNet .
> * Consumir a API a partir de uma aplicação web.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Python 3.7.4 ou superior](https://www.python.org/downloads/release/python-374/). (Python 3.8.x e Python 3.6.x também são verificados com Funções Azure.)
- As [ferramentas centrais de funções Azure](functions-run-local.md#install-the-azure-functions-core-tools)
- Um editor de código como [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Verificação pré-requisito

1. Numa janela de terminal ou comando, corra `func --version` para verificar se as Ferramentas Centrais de Funções Azure são a versão 2.7.1846 ou posterior.
1. Executar `python --version` (Linux/MacOS) ou `py --version` (Windows) para verificar os relatórios da sua versão Python 3.7.x.

## <a name="clone-the-tutorial-repository"></a>Clone o repositório tutorial

1. Numa janela terminal ou de comando, clone o seguinte repositório utilizando Git:

    ```
    git clone https://github.com/Azure-Samples/functions-python-pytorch-tutorial.git
    ```

1. Navegue na pasta e examine o seu conteúdo.

    ```
    cd functions-python-pytorch-tutorial
    ```

    - *iniciar* é a sua pasta de trabalho para o tutorial.
    - *fim* é o resultado final e implementação completa para a sua referência.
    - *os recursos* contêm o modelo de aprendizagem automática e bibliotecas auxiliares.
    - *frontend* é um site que chama a app de função.

## <a name="create-and-activate-a-python-virtual-environment"></a>Criar e ativar um ambiente virtual Python

Navegue para a pasta *inicial* e execute os seguintes comandos para criar e ativar um ambiente virtual chamado `.venv` .


# <a name="bash"></a>[festa](#tab/bash)

```bash
cd start
python -m venv .venv
source .venv/bin/activate
```

Se python não instalou o pacote venv na sua distribuição Linux, execute o seguinte comando:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
py -m venv .venv
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
py -m venv .venv
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

    Este comando cria uma pasta que corresponde ao nome da função, *classifica .* Nessa pasta encontram-se dois ficheiros: * \_ \_ init \_ \_ .py*, que contém o código de função, e *function.jsligados*, que descreve o gatilho da função e as suas ligações de entrada e saída. Para obter mais informações sobre o conteúdo destes ficheiros, consulte [examinar o conteúdo do ficheiro](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-python#optional-examine-the-file-contents) no arranque rápido do Python.


## <a name="run-the-function-locally"></a>Executar a função localmente

1. Inicie a função iniciando o hospedeiro local de tempo de funcionamento do Azure Functions na pasta *inicial:*

    ```
    func start
    ```

1. Assim que vir o `classify` ponto final aparecer na saída, navegue para o URL, ```http://localhost:7071/api/classify?name=Azure``` . A mensagem "Olá Azure!" deve aparecer na saída.

1. Use **ctrl** - **C** para parar o hospedeiro.


## <a name="import-the-pytorch-model-and-add-helper-code"></a>Importe o modelo PyTorch e adicione código de ajuda

Para modificar a `classify` função para classificar uma imagem com base no seu conteúdo, utiliza um modelo [ResNet](https://arxiv.org/abs/1512.03385) pré-treinado. O modelo pré-treinado, que vem de [PyTorch,](https://pytorch.org/hub/pytorch_vision_resnet/)classifica uma imagem em 1 de 1000 [classes ImageNet](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a). Em seguida, adicione algum código de ajuda e dependências ao seu projeto.

1. Na pasta *inicial,* executar o seguinte comando para copiar o código de previsão e as etiquetas na pasta *de classificação.*

    # <a name="bash"></a>[festa](#tab/bash)

    ```bash
    cp ../resources/predict.py classify
    cp ../resources/labels.txt classify
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    ---

1. Verifique se a pasta *de classificação* contém ficheiros nomeados *predict.py* e *labels.txt*. Caso contrário, verifique se executou o comando na pasta *inicial.*

1. Abrir *o início/requirements.txt* num editor de texto e adicionar as dependências exigidas pelo código auxiliar, que deve parecer o seguinte:

    ```txt
    azure-functions
    requests
    -f https://download.pytorch.org/whl/torch_stable.html
    torch==1.5.0+cpu
    torchvision==0.6.0+cpu
    ```

1. Guarde *requirements.txt, *em seguida, executar o seguinte comando a partir da pasta *de partida* para instalar as dependências.


    ```
    pip install --no-cache-dir -r requirements.txt
    ```

A instalação pode demorar alguns minutos, durante o qual pode proceder à modificação da função na secção seguinte.
> [!TIP]
> >No Windows, pode encontrar o erro: "Não foi possível instalar pacotes devido a um EnvironmentError: [Errno 2] Nenhum ficheiro ou diretório deste tipo:" seguido de um longo nome de pathname para um ficheiro como *sharded_mutable_dense_hashtable.cpython-37.pyc*. Normalmente, este erro acontece porque a profundidade do caminho da pasta torna-se demasiado longa. Neste caso, estale a chave de registo `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` para `1` permitir caminhos longos. Em alternativa, verifique onde está instalado o seu intérprete Python. Se essa localização tiver um longo caminho, tente reinstalar-se numa pasta com um caminho mais curto.

## <a name="update-the-function-to-run-predictions"></a>Atualizar a função para executar previsões

1. Abra *a classificação/init \_ \_ \_ \_ .py* num editor de texto e adicione as seguintes linhas após as `import` declarações existentes para importar a biblioteca JSON padrão e os ajudantes *de previsão:*

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Substitua todo o conteúdo da `main` função pelo seguinte código:

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="8-19":::

    Esta função recebe um URL de imagem num parâmetro de cadeia de consulta denominado `img` . Em seguida, chama `predict_image_from_url` da biblioteca de ajudantes para descarregar e classificar a imagem usando o modelo PyTorch. Em seguida, a função devolve uma resposta HTTP com os resultados.

    > [!IMPORTANT]
    > Como este ponto final HTTP é chamado por uma página web hospedada noutro domínio, a resposta inclui um `Access-Control-Allow-Origin` cabeçalho para satisfazer os requisitos de Partilha de Recursos de Origem Cruzada (CORS) do navegador.
    >
    > Numa aplicação de produção, altere `*` a origem específica da página web para uma maior segurança.

1. Guarde as suas alterações e, em seguida, assumindo que as dependências terminaram de instalar, inicie novamente o anfitrião da função local com `func start` . Certifique-se de que funciona o hospedeiro na pasta *inicial* com o ambiente virtual ativado. Caso contrário, o anfitrião começará, mas verá erros ao invocar a função.

    ```
    func start
    ```

1. Num browser, abra o seguinte URL para invocar a função com o URL de uma imagem bernese mountain dog e confirmar que o JSON devolvido classifica a imagem como um Cão de Montanha Bernese.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg
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

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg`
    - `https://github.com/Azure-Samples/functions-python-pytorch-tutorial/blob/master/resources/assets/bald-eagle.jpg?raw=true`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/penguin.jpg`

1. **Selecione Submeter-se** a invocar o ponto final da função para classificar a imagem.

    ![Screenshot do projeto acabado](media/machine-learning-pytorch/screenshot.png)

    Se o navegador reportar um erro ao submeter o URL de imagem, verifique o terminal em que está a executar a aplicação de função. Se vir um erro como "Nenhum módulo encontrado 'PIL'", pode ter iniciado a aplicação de função na pasta *inicial* sem primeiro ativar o ambiente virtual que criou anteriormente. Se ainda vir erros, volte a correr `pip install -r requirements.txt` com o ambiente virtual ativado e procure erros.

## <a name="clean-up-resources"></a>Limpar os recursos

Como todo este tutorial funciona localmente na sua máquina, não existem recursos ou serviços Azure para limpar.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a construir e personalizar um ponto final http API com Funções Azure para classificar imagens usando um modelo PyTorch. Também aprendeu a chamar a API de uma aplicação web. Você pode usar as técnicas neste tutorial para construir APIs de qualquer complexidade, tudo enquanto executa no modelo de computação sem servidor fornecido pela Azure Functions.

Veja também:

- [Implementar a função para Azure utilizando o Código do Estúdio Visual](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Guia de desenvolvedores de funções Azure](./functions-reference-python.md)


> [!div class="nextstepaction"]
> [Implementar a função para funções Azure utilizando o Guia Azure CLI](./functions-run-local.md#publish)
