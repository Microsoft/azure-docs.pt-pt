---
title: Depurar e solucionar problemas de pipelines do Machine Learning
titleSuffix: Azure Machine Learning
description: Depure e solucione problemas de pipelines do Machine Learning no SDK do Azure Machine Learning para Python. Aprenda armadilhas comuns para desenvolver pipelines e dicas para ajudá-lo a depurar seus scripts antes e durante a execução remota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.author: trbye
author: trevorbye
ms.date: 10/03/2019
ms.openlocfilehash: 3df95f88c057fa564078dbf05d5dfa4b26150f6a
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959660"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Depurar e solucionar problemas de pipelines do Machine Learning

Neste artigo, você aprenderá a depurar e solucionar problemas de [pipelines do Machine Learning](concept-ml-pipelines.md) no [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

As seções a seguir fornecem uma visão geral das armadilhas comuns ao criar pipelines e estratégias diferentes para depurar seu código em execução em um pipeline. Use as dicas a seguir quando estiver tendo problemas para fazer com que um pipeline seja executado conforme o esperado. 

## <a name="testing-scripts-locally"></a>Testando scripts localmente

Uma das falhas mais comuns em um pipeline é que um script anexado (script de limpeza de dados, script de pontuação, etc.) não está em execução como pretendido, ou contém erros de tempo de execução no contexto de computação remota que são difíceis de Depurar em seu espaço de trabalho no portal do Azure. 

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

## <a name="debugging-scripts-from-remote-context"></a>Depurando scripts do contexto remoto

O teste de scripts localmente é uma ótima maneira de depurar fragmentos de código principais e lógica complexa antes de começar a criar um pipeline, mas, em algum momento, você provavelmente precisará depurar scripts durante a execução do pipeline propriamente dita, especialmente ao diagnosticar o comportamento que ocorre durante a interação entre as etapas do pipeline. É recomendável liberal o uso de instruções `print()` em seus scripts de etapa para que você possa ver o estado do objeto e os valores esperados durante a execução remota, semelhante a como você depuraria o código JavaScript.

O arquivo de log `70_driver_log.txt` contém: 

* Todas as instruções impressas durante a execução do script
* O rastreamento de pilha para o script 

Para localizar esse e outros arquivos de log no portal, primeiro clique no pipeline em seu espaço de trabalho.

![Página pipelines no portal](./media/how-to-debug-pipelines/pipeline-1.png)

Navegue até a execução do pai do pipeline.

![Execução do pai de pipelines](./media/how-to-debug-pipelines/pipeline-2.png)

Clique na ID de execução para a etapa específica.

![Página pipelines no portal](./media/how-to-debug-pipelines/pipeline-3.png)

Navegue até a guia **logs** . Outros logs incluem informações sobre o processo de compilação da imagem do ambiente e os scripts de preparação de etapa.

![Página pipelines no portal](./media/how-to-debug-pipelines/pipeline-4.png)

> [!TIP]
> Execuções para *pipelines publicados* podem ser encontradas na guia **pipelines** no seu espaço de trabalho no Portal. Execuções para *pipelines não publicados* podem ser encontradas em **experimentos**.

## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

A tabela a seguir contém problemas comuns durante o desenvolvimento de pipeline, com possíveis soluções.

| Problema | Solução possível |
|--|--|
| Não é possível passar dados para o diretório `PipelineData` | Verifique se você criou um diretório no script que corresponde a onde o seu pipeline espera os dados de saída da etapa. Na maioria dos casos, um argumento de entrada definirá o diretório de saída e, em seguida, você criará o diretório explicitamente. Use `os.makedirs(args.output_dir, exist_ok=True)` para criar o diretório de saída. Consulte o [tutorial](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) para obter um exemplo de script de pontuação que mostra esse padrão de design. |
| Bugs de dependência | Se você tiver desenvolvido e testado com scripts localmente, mas encontrar problemas de dependência ao executar em uma computação remota no pipeline, verifique se as dependências e as versões do ambiente de computação correspondem ao ambiente de teste. |
| Erros ambíguos com destinos de computação | Excluir e recriar destinos de computação pode resolver determinados problemas com destinos de computação. |
| O pipeline não está Reutilizando as etapas | A reutilização de etapa é habilitada por padrão, mas certifique-se de que você não a desabilitou em uma etapa de pipeline. Se a reutilização estiver desabilitada, o parâmetro `allow_reuse` na etapa será definido como `False`. |
| O pipeline está sendo executado desnecessariamente | Para garantir que as etapas sejam executadas apenas novamente quando os dados ou scripts subjacentes forem alterados, desassocie os diretórios para cada etapa. Se você usar o mesmo diretório de origem para várias etapas, poderá ocorrer uma reexecutação desnecessária. Use o parâmetro `source_directory` em um objeto Step de pipeline para apontar para seu diretório isolado para essa etapa e verifique se você não está usando o mesmo caminho `source_directory` para várias etapas. |

## <a name="next-steps"></a>Passos seguintes

* Consulte a referência do SDK para obter ajuda com o pacote [azureml-pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e o pacote [azureml-pipelines-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) .

* Siga o [tutorial avançado](tutorial-pipeline-batch-scoring-classification.md) sobre como usar pipelines para pontuação em lote.
