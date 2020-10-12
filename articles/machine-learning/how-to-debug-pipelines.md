---
title: Debug & os oleodutos ML de resolução de problemas
titleSuffix: Azure Machine Learning
description: Depurar os seus oleodutos Azure Machine Learning em Python. Aprenda armadilhas comuns para desenvolver oleodutos e dicas para ajudá-lo a depurar os seus scripts antes e durante a execução remota. Aprenda a usar o Código do Estúdio Visual para depurar interativamente os seus oleodutos de aprendizagem automática.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 08/28/2020
ms.topic: conceptual
ms.custom: troubleshooting, devx-track-python
ms.openlocfilehash: be68ad35deca754df70bb51e83929e73ff132ba6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91315410"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Depurar e resolver problemas de pipelines de machine learning

Neste artigo, aprende-se a depurar e resolver [os problemas de aprendizagem](concept-ml-pipelines.md) de máquinas no [SDK de Aprendizagem automática Azure](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) e [no designer de Machine Learning Azure.](https://docs.microsoft.com/azure/machine-learning/concept-designer) São fornecidas informações sobre como:

## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

O quadro a seguir contém problemas comuns durante o desenvolvimento do gasoduto, com soluções potenciais.

| Problema | Solução possível |
|--|--|
| Incapaz de passar dados para o `PipelineData` diretório | Certifique-se de que criou um diretório no script que corresponde ao local onde o seu pipeline espera os dados de saída do passo. Na maioria dos casos, um argumento de entrada definirá o diretório de saída, e então você cria o diretório explicitamente. Use `os.makedirs(args.output_dir, exist_ok=True)` para criar o diretório de saída. Veja o [tutorial](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) para um exemplo de script de pontuação que mostra este padrão de design. |
| Insetos de dependência | Se vir erros de dependência no seu gasoduto remoto que não ocorreram durante os testes locais, confirme as dependências e versões do ambiente remoto que correspondem às do seu ambiente de teste. (Ver [construção, caching e reutilização](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse) de ambiente|
| Erros ambíguos com metas de computação | Tente eliminar e recriar alvos de computação. A recriação de metas de computação é rápida e pode resolver alguns problemas transitórios. |
| Gasoduto não reutilização de etapas | A reutilização de passos é ativada por padrão, mas certifique-se de que não a desativou num passo de oleoduto. Se a reutilização for desativada, `allow_reuse` o parâmetro do passo será definido para `False` . |
| O gasoduto está a ser reensaque desnecessariamente | Para garantir que os passos só se reexame quando os seus dados ou scripts subjacentes mudarem, dissocie os seus diretórios de código de origem para cada passo. Se utilizar o mesmo diretório de origem para vários passos, poderá sofrer repetições desnecessárias. Utilize o parâmetro num objeto de `source_directory` passo de gasoduto para apontar para o seu diretório isolado para esse passo e certifique-se de que não está a usar o mesmo caminho para `source_directory` vários passos. |
| Passo abrandando sobre épocas de treino ou outro comportamento em looping | Tente mudar quaisquer escritos de ficheiros, incluindo registos, de `as_mount()` `as_upload()` . O modo **de montagem** utiliza um sistema de ficheiros virtualizado remoto e carrega todo o ficheiro sempre que este é anexado. |

## <a name="debugging-techniques"></a>Técnicas de depuração

Existem três grandes técnicas para depurar gasodutos: 

* Depurar passos individuais de gasoduto no seu computador local
* Utilize insights de registo e aplicação para isolar e diagnosticar a origem do problema
* Prenda um depurador remoto a um oleoduto que funciona em Azure

### <a name="debug-scripts-locally"></a>Scripts de depurar localmente

Uma das falhas mais comuns num oleoduto é que o script de domínio não funciona como pretendido, ou contém erros de tempo de execução no contexto do computação remoto que são difíceis de depurar.

Os oleodutos em si não podem ser executados localmente, mas executar os scripts em isolamento na sua máquina local permite-lhe depurar mais rapidamente porque você não tem que esperar pelo processo de construção do cálculo e ambiente. Alguns trabalhos de desenvolvimento são necessários para o fazer:

* Se os seus dados estiverem numa loja de dados em nuvem, terá de descarregar dados e disponibilizá-lo para o seu script. Usar uma pequena amostra dos seus dados é uma boa maneira de reduzir o tempo de execução e obter rapidamente feedback sobre o comportamento do script
* Se estiver a tentar simular um passo de gasoduto intermédio, poderá ter de construir manualmente os tipos de objetos que o script em particular espera do passo anterior.
* Também terá de definir o seu próprio ambiente e replicar as dependências definidas no seu ambiente de computação remota

Uma vez que você tem uma configuração de script para executar no seu ambiente local, é muito mais fácil fazer tarefas de depuração como:

* Anexar uma configuração de depuragem personalizada
* Pausa na execução e inspeção do estado-objecto
* Apanhar erros tipo ou lógicos que não serão expostos até ao tempo de funcionação

> [!TIP] 
> Uma vez que você pode verificar que o seu script está funcionando como esperado, um bom próximo passo é executar o script em um oleoduto de um só passo antes de tentar executá-lo em um oleoduto com vários passos.

## <a name="configure-write-to-and-review-pipeline-logs"></a>Configurar, escrever e rever os registos de gasodutos

Testar scripts localmente é uma ótima maneira de depurar grandes fragmentos de código e lógica complexa antes de começar a construir um oleoduto, mas em algum momento você provavelmente precisará de depurar scripts durante o próprio pipeline real, especialmente quando se diagnostica o comportamento que ocorre durante a interação entre etapas de pipeline. Recomendamos o uso liberal de `print()` declarações nos seus scripts de passo para que possa ver o estado do objeto e os valores esperados durante a execução remota, semelhante à forma como depuraria o código JavaScript.

### <a name="logging-options-and-behavior"></a>Opções de registo e comportamento

O quadro abaixo fornece informações para diferentes opções de depuragem para gasodutos. Não é uma lista exaustiva, pois existem outras opções para além das Azure Machine Learning, Python e OpenCensus mostradas aqui.

| Biblioteca                    | Tipo   | Exemplo                                                          | Destino                                  | Recursos                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Métrica | `run.log(name, val)`                                             | Azure Machine Learning Portal UI             | [Como acompanhar experiências](how-to-track-experiments.md)<br>[azureml.core.Run classe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true)                                                                                                                                                 |
| Impressão/registo de pítons    | Registar    | `print(val)`<br>`logging.info(message)`                          | Troncos de motorista, Azure Machine Learning designer | [Como acompanhar experiências](how-to-track-experiments.md)<br><br>[Abate de python](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Registar    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Insights de Aplicação - vestígios                | [Depurar pipelines no Application Insights](how-to-debug-pipelines-application-insights.md)<br><br>[OpenCensus Azure Monitor Exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure) (Exportadores do Azure Monitor do OpenCensus)<br>[Livro de receitas de madeira python](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Exemplo de opções de registo

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

## <a name="azure-machine-learning-designer"></a>Estruturador do Azure Machine Learning

Para os oleodutos criados no designer, pode encontrar o ficheiro **70_driver_log** na página de autoria ou na página de detalhes do pipeline run.

### <a name="enable-logging-for-real-time-endpoints"></a>Ativar a sessão para pontos finais em tempo real

Para resolver problemas e depurar pontos finais em tempo real no designer, deve ativar o registo de insight de aplicação utilizando o SDK. O registo permite-lhe resolver problemas e depurar problemas de implementação e utilização do modelo. Para obter mais informações, consulte [Registar registos para modelos implantados.](how-to-enable-logging.md#logging-for-deployed-models) 

### <a name="get-logs-from-the-authoring-page"></a>Obtenha registos da página de autoria

Quando submeter uma corrida de pipeline e permanecer na página de autoria, pode encontrar os ficheiros de registo gerados para cada módulo à medida que cada módulo termina em execução.

1. Selecione um módulo que tenha terminado de funcionar na tela de autoria.
1. No painel direito do módulo, aceda ao **separador Saídas + registos.**
1. Expanda o painel direito e selecione o **70_driver_log.txt** para ver o ficheiro no navegador. Também pode baixar registos localmente.

    ![Painel de saída expandido no designer](./media/how-to-debug-pipelines/designer-logs.png)?view=azure-ml-py&preserve-view=true)?view=azure-ml-py&preserve-view=true)

### <a name="get-logs-from-pipeline-runs"></a>Obtenha registos de corridas de gasodutos

Também pode encontrar os ficheiros de registo para execuções específicas na página de detalhes do pipeline run, que pode ser encontrada na secção **Pipelines** ou **Experiments** do estúdio.

1. Selecione uma corrida de gasodutos criada no designer.

    ![Página de execução do gasoduto](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Selecione um módulo no painel de pré-visualização.
1. No painel direito do módulo, aceda ao **separador Saídas + registos.**
1. Expanda o painel direito para ver o ficheiro **70_driver_log.txt** no navegador ou selecione o ficheiro para descarregar os registos localmente.

> [!IMPORTANT]
> Para atualizar um gasoduto a partir da página de detalhes do gasoduto, tem de **clonar** o gasoduto para um novo projeto de oleoduto. Um gasoduto é uma imagem do oleoduto. É semelhante a um ficheiro de registo, e não pode ser alterado. 

## <a name="application-insights"></a>Application Insights
Para obter mais informações sobre a utilização da biblioteca OpenCensus Python desta forma, consulte este guia: [Debug e troubleshoot machine learning pipelines in Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="interactive-debugging-with-visual-studio-code"></a>Depuragem interativa com código de estúdio visual

Em alguns casos, poderá ser necessário depurar interativamente o código Python utilizado no seu gasoduto ML. Ao utilizar o Código de Estúdio Visual (Código VS) e o depuro, pode anexar-se ao código tal como funciona no ambiente de treino. Para mais informações, visite a [depuragem interativa no guia do Código VS.](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines)

## <a name="next-steps"></a>Passos seguintes

* Consulte a referência SDK para obter ajuda com o pacote [de núcleo de gasodutos azureml](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py&preserve-view=true) e o pacote [de passos de gasodutos azureml.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py&preserve-view=true)

* Consulte a lista de [exceções e códigos de erro](algorithm-module-reference/designer-error-codes.md)do designer.
