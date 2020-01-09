---
title: Depurar e solucionar problemas de pipelines do Machine Learning
titleSuffix: Azure Machine Learning
description: Depure e solucione problemas de pipelines do Machine Learning no SDK do Azure Machine Learning para Python. Aprenda armadilhas comuns para desenvolver pipelines e dicas para ajudá-lo a depurar seus scripts antes e durante a execução remota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 12/12/2019
ms.openlocfilehash: 7bb7ffc353d70c692dd5b9d3fab497d070e036fd
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562711"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Depurar e solucionar problemas de pipelines do Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a depurar e solucionar problemas de [pipelines do Machine Learning](concept-ml-pipelines.md) no [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) e no [Designer de Azure Machine Learning (versão prévia)](https://docs.microsoft.com/azure/machine-learning/service/concept-designer).


## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Depuração e solução de problemas no SDK do Azure Machine Learning
As seções a seguir fornecem uma visão geral das armadilhas comuns ao criar pipelines e estratégias diferentes para depurar seu código em execução em um pipeline. Use as dicas a seguir quando estiver tendo problemas para fazer com que um pipeline seja executado conforme o esperado. 
### <a name="testing-scripts-locally"></a>Testando scripts localmente

Uma das falhas mais comuns em um pipeline é que um script anexado (script de limpeza de dados, script de pontuação, etc.) não está em execução como pretendido, ou contém erros de tempo de execução no contexto de computação remota que são difíceis de Depurar em seu espaço de trabalho no computador do Azure Learning Studio. 

Os pipelines propriamente ditos não podem ser executados localmente, mas a execução dos scripts em isolamento no computador local permite que você depure mais rápido porque não precisa esperar o processo de compilação do ambiente e da computação. Alguns trabalhos de desenvolvimento são necessários para fazer isso:

* Se os seus dados estiverem em um armazenamento em nuvem, será necessário baixar os dados e disponibilizá-los para o script. Usar uma pequena amostra de seus dados é uma boa maneira de reduzir o tempo de execução e obter rapidamente comentários sobre o comportamento do script
* Se você estiver tentando simular uma etapa intermediária de pipeline, talvez seja necessário criar manualmente os tipos de objeto que o script específico está esperando na etapa anterior
* Você também precisará definir seu próprio ambiente e replicar as dependências definidas em seu ambiente de computação remota

Quando você tiver uma configuração de script para ser executada no seu ambiente local, será muito mais fácil fazer tarefas de depuração como:

* Anexando uma configuração de depuração personalizada
* Pausando a execução e inspecionando o estado do objeto
* Captura de tipo ou erros lógicos que não serão expostos até o tempo de execução

> [!TIP] 
> Depois que você puder verificar se o script está sendo executado conforme o esperado, uma boa próxima etapa é executar o script em um pipeline de etapa única antes de tentar executá-lo em um pipeline com várias etapas.

### <a name="debugging-scripts-from-remote-context"></a>Depurando scripts do contexto remoto

O teste de scripts localmente é uma ótima maneira de depurar fragmentos de código principais e lógica complexa antes de começar a criar um pipeline, mas, em algum momento, você provavelmente precisará depurar scripts durante a execução do pipeline propriamente dita, especialmente ao diagnosticar o comportamento que ocorre durante a interação entre as etapas do pipeline. É recomendável liberal o uso de instruções `print()` em seus scripts de etapa para que você possa ver o estado do objeto e os valores esperados durante a execução remota, semelhante a como você depuraria o código JavaScript.

O arquivo de log `70_driver_log.txt` contém: 

* Todas as instruções impressas durante a execução do script
* O rastreamento de pilha para o script 

Para localizar esse e outros arquivos de log no portal, primeiro clique no pipeline executado no seu espaço de trabalho.

![Página de lista de execução de pipeline](./media/how-to-debug-pipelines/pipelinerun-01.png)

Navegue até a página de detalhes da execução do pipeline.

![Página de detalhes da execução do pipeline](./media/how-to-debug-pipelines/pipelinerun-02.png)

Clique no módulo para a etapa específica. Navegue até a guia **logs** . Outros logs incluem informações sobre o processo de compilação da imagem do ambiente e os scripts de preparação de etapa.

![Guia log da página detalhes da execução do pipeline](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> Execuções para *pipelines publicados* podem ser encontradas na guia **pontos de extremidade** em seu espaço de trabalho. Execuções para *pipelines não publicados* podem ser encontradas em **experimentos** ou **pipelines**.

### <a name="troubleshooting-tips"></a>Sugestões para a resolução de problemas

A tabela a seguir contém problemas comuns durante o desenvolvimento de pipeline, com possíveis soluções.

| Problema | Solução possível |
|--|--|
| Não é possível passar dados para `PipelineData` diretório | Verifique se você criou um diretório no script que corresponde a onde o seu pipeline espera os dados de saída da etapa. Na maioria dos casos, um argumento de entrada definirá o diretório de saída e, em seguida, você criará o diretório explicitamente. Use `os.makedirs(args.output_dir, exist_ok=True)` para criar o diretório de saída. Consulte o [tutorial](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) para obter um exemplo de script de pontuação que mostra esse padrão de design. |
| Bugs de dependência | Se você tiver desenvolvido e testado com scripts localmente, mas encontrar problemas de dependência ao executar em uma computação remota no pipeline, verifique se as dependências e as versões do ambiente de computação correspondem ao ambiente de teste. |
| Erros ambíguos com destinos de computação | Excluir e recriar destinos de computação pode resolver determinados problemas com destinos de computação. |
| O pipeline não está Reutilizando as etapas | A reutilização de etapa é habilitada por padrão, mas certifique-se de que você não a desabilitou em uma etapa de pipeline. Se a reutilização estiver desabilitada, o parâmetro `allow_reuse` na etapa será definido como `False`. |
| O pipeline está sendo executado desnecessariamente | Para garantir que as etapas sejam executadas apenas novamente quando os dados ou scripts subjacentes forem alterados, desassocie os diretórios para cada etapa. Se você usar o mesmo diretório de origem para várias etapas, poderá ocorrer uma reexecutação desnecessária. Use o parâmetro `source_directory` em um objeto Step de pipeline para apontar para seu diretório isolado para essa etapa e verifique se você não está usando o mesmo caminho de `source_directory` para várias etapas. |

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>Depuração e solução de problemas no designer de Azure Machine Learning (versão prévia)

Esta seção fornece uma visão geral de como solucionar problemas de pipelines no designer.
Para pipelines criados no designer, você pode encontrar os **arquivos de log** na página de criação ou na página de detalhes de execução do pipeline.

### <a name="access-logs-from-the-authoring-page"></a>Acessar logs da página de criação

Ao enviar uma execução de pipeline e permanecer na página de criação, você pode encontrar os arquivos de log gerados para cada módulo.

1. Selecione qualquer módulo na tela de criação.
1. No painel Propriedades, vá para a guia **logs** .
1. Selecione o arquivo de log `70_driver_log.txt`

    ![Criando logs de módulo de página](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>Acessar logs de execuções de pipeline

Você também pode encontrar os arquivos de log de execuções específicas na página de detalhes de execução do pipeline nas seções **pipelines** ou **experimentos** .

1. Selecione uma execução de pipeline criada no designer.
    ](./media/how-to-debug-pipelines/pipelinerun-04.png) página de execução de pipeline ![
1. Selecione qualquer módulo no painel de visualização.
1. No painel Propriedades, vá para a guia **logs** .
1. Selecione o arquivo de log `70_driver_log.txt`

## <a name="next-steps"></a>Passos seguintes

* Consulte a referência do SDK para obter ajuda com o pacote [azureml-pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e o pacote [azureml-pipelines-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) .

* Consulte a lista de [exceções de designer e códigos de erro](algorithm-module-reference/designer-error-codes.md).
