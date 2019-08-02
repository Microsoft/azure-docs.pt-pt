---
title: 'Tutorial: Usar Python e TensorFlow em Azure Functions para fazer inferências de aprendizado de máquina | Microsoft Docs'
description: Este tutorial demonstra como aplicar modelos de aprendizado de máquina TensorFlow no Azure Functions
services: functions
author: anthonychu
manager: gwallace
ms.service: azure-functions
ms.devlang: python
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: antchu
ms.custom: mvc
ms.openlocfilehash: abc7302ee59103a9cbab156b95a41b77eb95d474
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68729177"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Tutorial: Aplicar modelos de aprendizado de máquina em Azure Functions com Python e TensorFlow

Este artigo demonstra como Azure Functions permite que você use python e TensorFlow com um modelo de aprendizado de máquina para classificar uma imagem com base em seu conteúdo.

Neste tutorial, irá aprender a: 

> [!div class="checklist"]
> * Inicializar um ambiente local para o desenvolvimento de Azure Functions no Python
> * Importar um modelo de aprendizado de máquina TensorFlow personalizado para um aplicativo de funções
> * Crie uma API HTTP sem servidor para prever se uma foto contém um cão ou um gato
> * Consumir a API de um aplicativo Web

![Captura de tela do projeto concluído](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos 

Para criar Azure Functions em Python, você precisa instalar algumas ferramentas.

- [Python 3,6](https://www.python.org/downloads/release/python-360/)
- [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- Um editor de códigos como [Visual Studio Code](https://code.visualstudio.com/)

## <a name="clone-the-tutorial-repository"></a>Clonar o repositório do tutorial

Para começar, abra um terminal e clone o repositório a seguir usando o Git:

```console
git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
cd functions-python-tensorflow-tutorial
```

O repositório contém algumas pastas.

- *início*:  Esta é sua pasta de trabalho para o tutorial
- *fim*: Este é o resultado final e a implementação completa para sua referência
- *recursos*: Contém o modelo do Machine Learning e as bibliotecas auxiliares
- *front-end*: Um site que chama o aplicativo de funções

## <a name="create-and-activate-a-python-virtual-environment"></a>Criar e ativar um ambiente virtual Python

Azure Functions requer Python 3.6. x. Você criará um ambiente virtual para garantir que está usando a versão necessária do Python.

Altere o diretório de trabalho atual para a pasta *inicial* . Em seguida, crie e ative um ambiente virtual chamado *. venv*. Dependendo da instalação do Python, os comandos para criar um ambiente virtual Python 3,6 podem ser diferentes das instruções a seguir.

#### <a name="linux-and-macos"></a>Linux e macOS:

```bash
cd start
python3.6 -m venv .venv
source .venv/bin/activate
```

#### <a name="windows"></a>Windows:

```powershell
cd start
py -3.6 -m venv .venv
.venv\scripts\activate
```

O prompt de terminal agora é prefixado com `(.venv)` o que indica que você ativou com êxito o ambiente virtual. Confirme que `python` , no ambiente virtual, é, de fato, Python 3.6. x.

```console
python --version
```

> [!NOTE]
> Para o restante do tutorial, você executa comandos no ambiente virtual. Se você precisar reativar o ambiente virtual em um terminal, execute o comando de ativação apropriado para seu sistema operacional.

## <a name="create-an-azure-functions-project"></a>Criar um projeto das Funções do Azure

Na pasta *inicial* , use o Azure Functions Core Tools para inicializar um aplicativo de funções do Python.

```console
func init --worker-runtime python
```

Um aplicativo de funções pode conter um ou mais Azure Functions. Abra a pasta *Iniciar* em um editor e examine o conteúdo.

- [*local. Settings. JSON*](functions-run-local.md#local-settings-file): Contém as configurações de aplicativo usadas para o desenvolvimento local
- [*host. JSON*](functions-host-json.md): Contém configurações para o host Azure Functions e as extensões
- [*requirements. txt*](functions-reference-python.md#python-version-and-package-management): Contém os pacotes do Python exigidos por este aplicativo

## <a name="create-an-http-function"></a>Criar uma função HTTP

O aplicativo requer um único ponto de extremidade de API HTTP que usa uma URL de imagem como entrada e retorna uma previsão de se a imagem contém um cachorro ou um gato.

No terminal, use o Azure Functions Core Tools para Scaffold uma nova função HTTP denominada *classificar*.

```console
func new --language python --template HttpTrigger --name classify
```

Uma nova pasta chamada *classificar* é criada, contendo dois arquivos.

- *init.py\_: \_ \_\_* Um arquivo para a função principal
- *Function. JSON*:  Um arquivo que descreve o gatilho da função e suas associações de entrada e saída

### <a name="run-the-function"></a>Executar a função

No terminal com o ambiente virtual Python ativado, inicie o aplicativo de funções.

```console
func start
```

Abra um navegador e navegue até a URL a seguir. A função deve ser executada e retornar *Olá Azure!*

```
http://localhost:7071/api/classify?name=Azure
```

Use `Ctrl-C` para interromper o aplicativo de funções.

## <a name="import-the-tensorflow-model"></a>Importar o modelo TensorFlow

Você usará um modelo TensorFlow predefinido que foi treinado e exportado do Azure Serviço de Visão Personalizada.

> [!NOTE]
> Se você quiser criar seu próprio usando a camada gratuita do Serviço de Visão Personalizada, poderá seguir as [instruções no repositório do projeto de exemplo](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md).

O modelo consiste em dois arquivos na pasta *< REPOSITORY_ROOT >/Resources/Model* : *Model. db* e *Labels. txt*. Copie-os para a pasta *classificar* da função.

#### <a name="linux-and-macos"></a>Linux e macOS:

```bash
cp ../resources/model/* classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\model\* classify
```

Certifique-se de incluir \* o no comando acima. Confirme se a *classificação* agora contém arquivos denominados *Model. PB* e *Labels. txt*.

## <a name="add-the-helper-functions-and-dependencies"></a>Adicionar as funções e dependências do auxiliar

Algumas funções auxiliares para preparar a imagem de entrada e fazer uma previsão usando TensorFlow estão em um arquivo chamado *Predict.py* na pasta *recursos* . Copie esse arquivo para a pasta da função de *classificação* .

#### <a name="linux-and-macos"></a>Linux e macOS:

```bash
cp ../resources/predict.py classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\predict.py classify
```

Confirme se a *classificação* agora contém um arquivo chamado *Predict.py*.

### <a name="install-dependencies"></a>Instalar dependências

A biblioteca auxiliar tem algumas dependências que precisam ser instaladas. Abra *Start/requirements. txt* em seu editor e adicione as seguintes dependências ao arquivo.

```txt
tensorflow
Pillow
requests
```

Guarde o ficheiro.

No terminal com o ambiente virtual ativado, execute o comando a seguir na pasta *inicial* para instalar as dependências. Algumas etapas de instalação podem levar alguns minutos para serem concluídas.

```console
pip install --no-cache-dir -r requirements.txt
```

### <a name="caching-the-model-in-global-variables"></a>Armazenando em cache o modelo em variáveis globais

No editor, abra *Predict.py* e examine a `_initialize` função próxima à parte superior do arquivo. Observe que o modelo TensorFlow é carregado do disco na primeira vez em que a função é executada e salva em variáveis globais. O carregamento do disco é ignorado nas execuções subsequentes da `_initialize` função. Armazenar em cache o modelo na memória com essa técnica acelera as previsões posteriores.

Para obter mais informações sobre variáveis globais, consulte o [Guia do desenvolvedor do Azure Functions Python](functions-reference-python.md#global-variables).

## <a name="update-function-to-run-predictions"></a>Atualizar função para executar previsões

Abra *classificar/\_\_Init\_. py emseueditor.\_* Importe a biblioteca de *previsão* que você adicionou à mesma pasta anteriormente. Adicione as seguintes `import` instruções abaixo das outras importações que já estão no arquivo.

```python
import json
from .predict import predict_image_from_url
```

Substitua o código do modelo de função pelo seguinte.

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    image_url = req.params.get('img')
    results = predict_image_from_url(image_url)

    headers = {
        "Content-type": "application/json",
        "Access-Control-Allow-Origin": "*"
    }
    return func.HttpResponse(json.dumps(results), headers = headers)
```

Certifique-se guardar as alterações.

Essa função recebe uma URL de imagem em um parâmetro de cadeia `img`de caracteres de consulta chamado. Ele chama `predict_image_from_url` da biblioteca auxiliar que baixa a imagem e retorna uma previsão usando o modelo TensorFlow. Em seguida, a função retorna uma resposta HTTP com os resultados.

Como o ponto de extremidade http é chamado por uma página da web hospedada em outro domínio, a `Access-Control-Allow-Origin` resposta http inclui um cabeçalho para atender aos requisitos de CORS (compartilhamento de recursos entre origens) do navegador.

> [!NOTE]
> Em um aplicativo de produção, `*` altere para a origem específica da página da Web para obter segurança adicional.

### <a name="run-the-function-app"></a>Executar o aplicativo de funções

Verifique se o ambiente virtual do Python ainda está ativado e inicie o aplicativo de funções usando o comando a seguir.

```console
func start
```

Em um navegador, abra essa URL que chama sua função com a URL de uma foto Cat. Confirme se um resultado de previsão válido é retornado.

```
http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
```

Mantenha o aplicativo de funções em execução.

### <a name="run-the-web-app"></a>Executar a aplicação Web

Há um aplicativo Web simples na pasta *front-end* que CONSOME a API http no aplicativo de funções.

Abra um terminal *separado* e altere para a pasta *frontend* . Inicie um servidor HTTP com Python 3,6.

#### <a name="linux-and-macos"></a>Linux e macOS:

```bash
cd <FRONT_END_FOLDER>
python3.6 -m http.server
```

#### <a name="windows"></a>Windows:

```powershell
cd <FRONT_END_FOLDER>
py -3.6  -m http.server
```

Em um navegador, navegue até a URL do servidor HTTP que é exibida no terminal. Um aplicativo Web deve aparecer. Insira uma das URLs de foto a seguir na caixa de texto. Você também pode usar uma URL de uma foto de gato ou cachorro publicamente acessível.

- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`

Quando você clica em enviar, o aplicativo de funções é chamado e um resultado é exibido na página.

## <a name="clean-up-resources"></a>Limpar recursos
A totalidade deste tutorial é executada localmente em seu computador, portanto, não há recursos ou serviços do Azure a serem limpos.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a criar e personalizar uma API HTTP com Azure Functions para fazer previsões usando um modelo TensorFlow. Você também aprendeu como chamar a API de um aplicativo Web.

Você pode usar as técnicas deste tutorial para criar APIs de qualquer complexidade, tudo durante a execução no modelo de computação sem servidor fornecido pelo Azure Functions.

Para implantar o aplicativo de funções no Azure, use o [Azure Functions Core Tools](./functions-run-local.md#publish) ou [Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).

> [!div class="nextstepaction"]
> [Guia do desenvolvedor do Azure Functions Python](./functions-reference-python.md)
