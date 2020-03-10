---
title: Depuração e problemas de aprendizagem automática
titleSuffix: Azure Machine Learning
description: Debug e troubleshoot machine learning pipelines no Azure Machine Learning SDK para Python. Aprenda armadilhas comuns para desenvolver oleodutos e dicas para ajudá-lo a depurar os seus scripts antes e durante a execução remota. Aprenda a usar o Visual Studio Code para desinbugicar interativamente os seus oleodutos de aprendizagem automática.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 12/12/2019
ms.openlocfilehash: c81d4db5798c15327e06471f1cb0da4841bd61b2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396253"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Depuração e problemas de aprendizagem automática
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a depurar e resolver os gasodutos de [aprendizagem automática](concept-ml-pipelines.md) no [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) e [azure machine learning designer (pré-visualização)](https://docs.microsoft.com/azure/machine-learning/concept-designer). Informações são fornecidas sobre como:

* Depuração usando o Azure Machine Learning SDK
* Depuração usando o Designer de Aprendizagem automática Azure
* Depuração usando insights de aplicação
* Debug utilizando interativamente o Código do Estúdio Visual (Código VS) e as Ferramentas Python para Estúdio Visual (PTVSD)

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Depuração e resolução de problemas no Azure Machine Learning SDK
As seguintes secções fornecem uma visão geral das armadilhas comuns ao construir oleodutos, e diferentes estratégias para depurar o seu código que está em execução num oleoduto. Use as seguintes dicas quando tiver dificuldade em obter um oleoduto para funcionar como esperado.

### <a name="testing-scripts-locally"></a>Testar scripts localmente

Uma das falhas mais comuns num pipeline é que um script anexado (script de limpeza de dados, script de pontuação, etc.) não está a funcionar como pretendido, ou contém erros de tempo de execução no contexto de computação remota que são difíceis de depurar no seu espaço de trabalho na Máquina Azure Estúdio de aprendizagem. 

Os próprios oleodutos não podem ser executados localmente, mas executar os scripts isoladamente na sua máquina local permite-lhe depurar mais rapidamente porque não precisa esperar pelo processo de construção de cálculo e ambiente. Alguns trabalhos de desenvolvimento são necessários para o fazer:

* Se os seus dados estiverem numa loja de dados em nuvem, terá de descarregar dados e disponibilizá-lo para o seu script. Usar uma pequena amostra dos seus dados é uma boa maneira de reduzir o tempo de execução e obter rapidamente feedback sobre o comportamento do script
* Se estiver a tentar simular um passo intermédio do gasoduto, poderá ter de construir manualmente os tipos de objetos que o script em particular espera a partir do passo anterior
* Você também precisa definir seu próprio ambiente, e replicar as dependências definidas no seu ambiente de computação remota

Uma vez que você tem uma configuração de script para executar no seu ambiente local, é muito mais fácil fazer tarefas de depuração como:

* Anexar uma configuração de depuração personalizada
* Pausa na execução e inspeção do estado-objeto
* Capturar tipo ou erros lógicos que não serão expostos até o tempo de execução

> [!TIP] 
> Uma vez que você pode verificar se o seu script está funcionando como esperado, um bom passo seguinte é executar o script em um pipeline de um passo único antes de tentar executá-lo em um pipeline com vários passos.

### <a name="debugging-scripts-from-remote-context"></a>Depurando scripts do contexto remoto

Testar scripts localmente é uma ótima maneira de depurar grandes fragmentos de código e lógica complexa antes de começar a construir um oleoduto, mas em algum momento você provavelmente precisará de depurar scripts durante o pipeline real executar-se, especialmente quando diagnosticar o comportamento que ocorre durante a interação entre os passos do gasoduto. Recomendamos o uso liberal de declarações `print()` nos seus scripts de passo para que possa ver o estado do objeto e os valores esperados durante a execução remota, semelhanteà forma como iria depurar o código JavaScript.

O ficheiro de registo `70_driver_log.txt` contém: 

* Todas as declarações impressas durante a execução do seu guião
* O traço da pilha para o script 

Para encontrar este e outros ficheiros de registo no portal, clique primeiro no pipeline executado no seu espaço de trabalho.

![Página da lista de execução do gasoduto](./media/how-to-debug-pipelines/pipelinerun-01.png)

Navegue para a página de detalhes do pipeline run.

![Página de detalhes de execução de gasoduto](./media/how-to-debug-pipelines/pipelinerun-02.png)

Clique no módulo para obter o passo específico. Navegue para o separador **Logs.** Outros registos incluem informações sobre o seu processo de construção de imagem ambiental e scripts de preparação de passos.

![Separador de registo de página de execução de pipeline](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> As correções para *os gasodutos publicados* podem ser encontradas no separador **Endpoints** no seu espaço de trabalho. As *corridas para gasodutos não publicados* podem ser encontradas em **Experiências** ou **Gasodutos**.

### <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

A tabela que se segue contém problemas comuns durante o desenvolvimento do gasoduto, com potenciais soluções.

| Problema | Solução possível |
|--|--|
| Incapaz de passar dados para `PipelineData` diretório | Certifique-se de que criou um diretório no script que corresponde ao local onde o seu pipeline espera os dados de saída de passos. Na maioria dos casos, um argumento de entrada definirá o diretório de saída, e depois cria-se o diretório explicitamente. Use `os.makedirs(args.output_dir, exist_ok=True)` para criar o diretório de saída. Consulte o [tutorial](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) para obter um exemplo de roteiro de pontuação que mostra este padrão de design. |
| Insetos de dependência | Se desenvolveu e testou scripts localmente, mas encontra problemas de dependência ao executar uma computação remota no pipeline, certifique-se de que as dependências e versões do ambiente computacional correspondem ao seu ambiente de teste. |
| Erros ambíguos com alvos de cálculo | Eliminar e recriar alvos de cálculo pode resolver certos problemas com alvos de cálculo. |
| Gasoduto não reutilizar passos | A reutilização do passo está ativada por defeito, mas certifique-se de que não a desativou num passo de gasoduto. Se a reutilização for desativada, o parâmetro `allow_reuse` no degrau será definido para `False`. |
| O gasoduto está a refazer-se desnecessariamente. | Para garantir que os passos só se regerão quando os seus dados ou scripts subjacentes mudarem, dissolquem os seus diretórios para cada passo. Se utilizar o mesmo diretório de origem para várias etapas, poderá sofrer repetições desnecessárias. Utilize o parâmetro `source_directory` num objeto de passo de gasoduto para apontar o seu diretório isolado para esse passo e certifique-se de que não está a usar o mesmo caminho `source_directory` para vários passos. |

### <a name="logging-options-and-behavior"></a>Opções de exploração madeireira e comportamento

A tabela abaixo fornece informações para diferentes opções de depuração para oleodutos. Não é uma lista exaustiva, pois existem outras opções além das de Azure Machine Learning, Python e OpenCensus mostradas aqui.

| Biblioteca                    | Tipo   | Exemplo                                                          | Destino                                  | Recursos                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SDK de aprendizagem automática azure | Métrica | `run.log(name, val)`                                             | Portal de Aprendizagem automática Azure UI             | [Como rastrear experiências](how-to-track-experiments.md#available-metrics-to-track)<br>[classe azureml.core.Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Impressão/exploração de python    | Registar    | `print(val)`<br>`logging.info(message)`                          | Registos de motorista, Azure Machine Learning Designer | [Como rastrear experiências](how-to-track-experiments.md#available-metrics-to-track)<br><br>[Exploração madeireira python](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Registar    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Insights de Aplicação - vestígios                | [Condutas de depuração em Insights de Aplicação](how-to-debug-pipelines-application-insights.md)<br><br>[OpenCensus Azure Monitor Exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure) (Exportadores do Azure Monitor do OpenCensus)<br>[Livro de receitas de exploração de python](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Exemplo de opções de login

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>Debug e troubleshoot em Azure Machine Learning designer (pré-visualização)

Esta secção fornece uma visão geral de como resolver os oleodutos no designer.
Para os oleodutos criados no designer, pode encontrar os **ficheiros** de registo na página de autor, quer na página de detalhes do pipeline.

### <a name="access-logs-from-the-authoring-page"></a>Acesso de registos a partir da página de autor

Quando submeter uma execução de pipeline e permanecer na página de autor, pode encontrar os ficheiros de registo gerados para cada módulo.

1. Selecione qualquer módulo na tela de autoria.
1. No painel de propriedades, vá ao separador **Logs.**
1. Selecione o ficheiro de registo `70_driver_log.txt`

    ![Registos de módulos de página de autoria](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>Registos de acesso a partir de pipeline runs

Também pode encontrar os ficheiros de registo de execuções específicas na página de detalhes de execução do gasoduto nas secções **Pipelines** ou **Experiments.**

1. Selecione uma execução de pipeline criada no designer.
    ![Pipeline executar página](./media/how-to-debug-pipelines/pipelinerun-04.png)
1. Selecione qualquer módulo no painel de pré-visualização.
1. No painel de propriedades, vá ao separador **Logs.**
1. Selecione o ficheiro de registo `70_driver_log.txt`

## <a name="debug-and-troubleshoot-in-application-insights"></a>Depuração e resolução de problemas em Insights de Aplicação
Para obter mais informações sobre a utilização da biblioteca OpenCensus Python desta forma, consulte este guia: [Debug e troubleshoot machine learning pipelines in Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="debug-and-troubleshoot-in-visual-studio-code"></a>Debug e resolução de problemas no Código do Estúdio Visual

Em alguns casos, poderá ser necessário depurar interativamente o código Python utilizado no seu pipeline ML. Utilizando o Código do Estúdio Visual (Código VS) e as Ferramentas Python para Estúdio Visual (PTVSD), pode anexar-se ao código à medida que funciona no ambiente de treino.

### <a name="prerequisites"></a>Pré-requisitos

* Um espaço de __trabalho azure machine learning__ que está configurado para usar uma Rede Virtual __Azure__.
* Um __oleoduto Azure Machine Learning__ que usa scripts Python como parte dos passos do oleoduto. Por exemplo, um PythonScriptStep.
* Um cluster Azure Machine Learning Compute, que está __na rede virtual__ e é utilizado pelo oleoduto para a __formação.__
* Um ambiente de __desenvolvimento__ que está na __rede virtual.__ O ambiente de desenvolvimento pode ser um dos seguintes:

    * Uma Máquina Virtual Azure na rede virtual
    * Uma instância computada de Notebook VM na rede virtual
    * Uma máquina cliente ligada à rede virtual por uma rede privada virtual (VPN).

Para obter mais informações sobre a utilização de uma Rede Virtual Azure com Machine Learning Azure, consulte [a experimentação e os trabalhos de inferência do Secure Azure ML dentro](how-to-enable-virtual-network.md)de uma Rede Virtual Azure .

### <a name="how-it-works"></a>Como funciona

Os seus passos de oleoduto ML executam os scripts Python. Estes scripts são modificados para executar as seguintes ações:
    
1. Faça logno no endereço IP do anfitrião em que estão a funcionar. Usa o endereço IP para ligar o depurador ao guião.

2. Inicie o componente de depuração PTVSD e aguarde que um desordeiro se conecte.

3. Do seu ambiente de desenvolvimento, monitoriza os registos criados pelo processo de treino para encontrar o endereço IP onde o script está em execução.

4. Diga ao Código VS o endereço IP para ligar o depurador utilizando um ficheiro `launch.json`.

5. Liga-se o debugger e interactivomente atravessa o guião.

### <a name="configure-python-scripts"></a>Configure scripts Python

Para permitir a depuração, faça as seguintes alterações ao script(s) Python utilizados por passos no seu pipeline ML:

1. Adicione as seguintes declarações de importação:

    ```python
    import ptvsd
    import socket
    from azureml.core import Run
    ```

1. Adicione os seguintes argumentos. Estes argumentos permitem-lhe ativar o debugger conforme necessário, e definir o tempo de tempo para anexar o debugger:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the Azure ML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
    ```

1. Adicione as seguintes declarações. Estas declarações carregam o contexto atual de execução para que possa registar o endereço IP do nó em que o código está em execução:

    ```python
    global run
    run = Run.get_context()
    ```

1. Adicione uma declaração `if` que inicie o PTVSD e espere que um desordeiro anexe. Se nenhum desbugger se ligar antes do intervalo, o script continua normal.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        ip = socket.gethostbyname(socket.gethostname())
        print(f'ip_address: {ip}')
        ptvsd.enable_attach(address=('0.0.0.0', 5678),
                            redirect_output=True)
        # Wait for the timeout for debugger to attach
        ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
        print(f'Debugger attached = {ptvsd.is_attached()}')
    ```

O exemplo python seguinte mostra um ficheiro básico `train.py` que permite a depuração:

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import ptvsd
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    ip = socket.gethostbyname(socket.gethostname())
    print(f'ip_address: {ip}')
    ptvsd.enable_attach(address=('0.0.0.0', 5678),
                        redirect_output=True)
    # Wait for the timeout for debugger to attach
    ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
    print(f'Debugger attached = {ptvsd.is_attached()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>Configure o gasoduto ML

Para fornecer os pacotes Python necessários para iniciar o PTVSD e obter o contexto de execução, criar um ambiente e definir `pip_packages=['ptvsd', 'azureml-sdk==1.0.83']`. Altere a versão SDK para corresponder à que está a usar. O seguinte código de corte demonstra como criar um ambiente:

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['ptvsd', 'azureml-sdk==1.0.83'])
```

Na secção de [scripts Configure Python,](#configure-python-scripts) foram adicionados dois novos argumentos aos scripts utilizados pelos seus passos de pipeline ML. O seguinte código de corte demonstra como usar estes argumentos para permitir a depuração do componente e definir um tempo de paragem. Também demonstra como utilizar o ambiente criado anteriormente, definindo `runconfig=run_config`:

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300],
                         compute_target=aml_compute, 
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

Quando o gasoduto funciona, cada passo cria uma corrida de crianças. Se a depuração estiver ativada, o script modificado regista informações semelhantes ao seguinte texto no `70_driver_log.txt` para a execução da criança:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Poupe o valor `ip_address`. É usado na secção seguinte.

> [!TIP]
> Também pode encontrar o endereço IP dos registos de execução para a criança correr para este passo de gasoduto. Para obter mais informações sobre a visualização desta informação, consulte as [experiências e métricas da experiência Monitor Azure ML.](how-to-track-experiments.md)

### <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

1. Para instalar as Ferramentas Python para Estúdio Visual (PTVSD) no seu ambiente de desenvolvimento do Código VS, utilize o seguinte comando:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Para obter mais informações sobre a utilização do PTVSD com código VS, consulte [A Depuração Remota](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Para configurar o Código VS para comunicar com a computação Azure Machine Learning que está a executar o debugger, crie uma nova configuração de depuração:

    1. A partir do Código VS, selecione o menu __Debug__ e, em seguida, __selecione configurações abertas__. Um ficheiro chamado __Launch.json__ abre.

    1. No ficheiro __launch.json,__ encontre a linha que contém `"configurations": [`, e insira o seguinte texto depois. Altere a entrada `"host": "10.3.0.5"` do endereço IP devolvido nos seus registos da secção anterior. Altere a entrada `"localRoot": "${workspaceFolder}/code/step"` para um diretório local que contém uma cópia do guião a ser depurado:

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "10.3.0.5",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Se já houver outras entradas na secção de configurações, adicione uma vírmula (,) após o código que inseriu.

        > [!TIP]
        > A melhor prática é manter os recursos para scripts em diretórios separados, razão pela qual o `localRoot` exemplo referências de valor `/code/step1`.
        >
        > Se estiver a depurar vários scripts, em diretórios diferentes, crie uma secção de configuração separada para cada script.

    1. Guarde o ficheiro __launch.json.__

### <a name="connect-the-debugger"></a>Ligue o debugger

1. Abra o Código VS e abra uma cópia local do guião.
2. Detete pontos de rutura onde pretende que o script pare depois de ligado.
3. Enquanto o processo da criança estiver a executar o script e o `Timeout for debug connection` for exibido nos registos, utilize a tecla F5 ou selecione __Debug__. Quando solicitado, selecione o __Azure Machine Learning Compute: configuração de depuração remota.__ Também pode selecionar o ícone de depuração da barra lateral, o __Azure Machine Learning:__ entrada remota de depuração do menu de depuração de Debug e, em seguida, usar a seta verde para prender o debugger.

    Neste ponto, o Código VS liga-se ao PTVSD no nó computacional e para no ponto de rutura que definiu anteriormente. Agora pode passar pelo código à medida que corre, ver variáveis, etc.

    > [!NOTE]
    > Se o registo apresentar uma entrada indicando `Debugger attached = False`, então o tempo de paragem expirou e o script continuou sem o desordeiro. Submeta novamente o gasoduto e ligue o descato após a mensagem `Timeout for debug connection` e antes que o prazo expire.

## <a name="next-steps"></a>Passos seguintes

* Consulte a referência SDK para obter ajuda com o pacote de núcleo de [gasodutos em azureml](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e o pacote de [passos em gasodutos em azul.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)

* Consulte a lista de exceções do [designer e códigos](algorithm-module-reference/designer-error-codes.md)de erro .
