---
title: Problemas conhecidos e resolução de problemas
titleSuffix: Azure Machine Learning service
description: Obter uma lista dos problemas conhecidos, soluções alternativas e resolução de problemas do serviço Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 3cf71de72a6005c59d76e2d88059a1ae16ec2970
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817478"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Problemas conhecidos e resolução de problemas serviço do Azure Machine Learning

Este artigo ajuda-o, encontre e corrija erros ou falhas encontrados ao utilizar o serviço Azure Machine Learning.

## <a name="sdk-installation-issues"></a>Problemas de instalação do SDK

**Mensagem de erro: Não é possível desinstalar 'PyYAML'**

Azure Machine Learning SDK para Python: PyYAML é um projeto instalado distutils. Por conseguinte, não é possível com precisão determinar quais os ficheiros que pertencem a ela, se houver uma desinstalação parcial. Para continuar a instalação do SDK ao ignorar este erro, utilize:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problemas ao criar a computação do Azure Machine Learning

Há uma chance de rara que alguns usuários que criou a sua área de trabalho do Azure Machine Learning do portal do Azure antes do lançamento de DG poderão não conseguir criar a computação do Azure Machine Learning nessa área de trabalho. Pode emitir um pedido de suporte com o serviço ou criar uma nova área de trabalho através do Portal ou o SDK para desbloquear-se imediatamente.

## <a name="image-building-failure"></a>Falha de criação de imagem

Imagem de criação Falha ao implementar o serviço web. Solução alternativa é adicionar "pynacl = = 1.2.1" como uma dependência de pip Conda ficheiro para a configuração de imagem.

## <a name="deployment-failure"></a>Falha de implementação

Se observar `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`, alterar o SKU para VMs utilizadas na sua implementação para um que tenha mais memória.

## <a name="fpgas"></a>FPGAs
Não será capaz de implementar os modelos no FPGAs até que tiver solicitado e foi aprovada para a quota FPGA. Para pedir acesso, preencha o formulário de pedido de quota: https://aka.ms/aml-real-time-ai

## <a name="databricks"></a>Databricks

Problemas de Databricks e o Azure Machine Learning.

### <a name="failure-when-installing-packages"></a>Falha ao instalar pacotes
Falhas de instalação de Machine Learning SDK do Azure no Databricks quando mais pacotes são instalados. Alguns pacotes e, por exemplo, `psutil`, pode causar conflitos. Para evitar erros de instalação, instale pacotes pela versão de lib congelar. Este problema está relacionado com Databricks e não o Azure Machine Learning SDK do serviço - pode encarar os fatos com outras bibliotecas demasiado. Exemplo:
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
   ```
Em alternativa, pode utilizar scripts de init se manter a enfrentar problemas de instalação com bibliotecas de Python. Essa abordagem não é uma abordagem oficialmente suportada. Pode consultar [este documento](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-ml-run"></a>Cancelar uma execução de ML automatizada
Quando usando automatizadas capacidades de machine learning no Databricks, se pretender cancelar uma execução e inicie uma nova experiência de execução, reinicie o cluster do Azure Databricks.

### <a name="10-iterations-for-automated-ml"></a>> 10 iterações para ML automatizada
Nas definições de ml automatizada, se tiver mais de 10 iterações, defina `show_output` para `False` quando submeter a execução.


## <a name="azure-portal"></a>Portal do Azure
Se vá diretamente para ver a sua área de trabalho a partir de uma ligação de partilha do SDK ou o portal, não será capaz de exibir a página de descrição geral normal com informações de subscrição na extensão. Também não será capaz de alternar para outra área de trabalho. Se precisar de ver a outra área de trabalho, a solução é ir diretamente para o [portal do Azure](https://portal.azure.com) e procure o nome de área de trabalho.

## <a name="diagnostic-logs"></a>Registos de diagnósticos
Por vezes, pode ser útil se pode fornecer informações de diagnóstico quando pedir ajuda.
É aqui onde residem os ficheiros de registo:

## <a name="resource-quotas"></a>Quotas de recursos

Saiba mais sobre o [quotas de recursos](how-to-manage-quotas.md) que poderá encontrar ao trabalhar com o Azure Machine Learning.

## <a name="authentication-errors"></a>Erros de autenticação

Se efetuar uma operação de gestão num destino de computação de uma tarefa remota, receberá um dos seguintes erros:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Por exemplo, receberá um erro se tentar criar ou anexar um destino de computação a partir de um Pipeline de ML, que é enviado para execução remota.

## <a name="get-more-support"></a>Obter suporte mais

Pode submeter pedidos de suporte e obtenha ajuda de suporte técnico, fóruns e muito mais. [Saiba mais...](support-for-aml-services.md)
