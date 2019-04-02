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
ms.date: 03/29/2019
ms.custom: seodec18
ms.openlocfilehash: d7542909df336555e17aea9b0e680879b25dc17f
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58791750"
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

## <a name="automated-machine-learning"></a>Aprendizagem automática automatizada

Aprendizagem de máquina de fluxo automatizada tensor não suporta a versão de fluxo de tensor 1.13 atualmente. Instalar isso fará com que as dependências de pacote parar de funcionar. Estamos a trabalhar para corrigir este problema numa versão futura. 


## <a name="databricks"></a>Databricks

Problemas de Databricks e o Azure Machine Learning.

### <a name="failure-when-installing-packages"></a>Falha ao instalar pacotes

Instalação do SDK do Machine Learning do Azure falha no Azure Databricks quando mais pacotes são instalados. Alguns pacotes e, por exemplo, `psutil`, pode causar conflitos. Para evitar erros de instalação, instale pacotes congelar a versão da biblioteca. Este problema está relacionado ao Databricks e não para o SDK do serviço do Azure Machine Learning. Podem ocorrer este problema com outras bibliotecas, demasiado. Exemplo:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Em alternativa, pode utilizar scripts de init se manter a enfrentar problemas de instalação com bibliotecas de Python. Essa abordagem não é oficialmente suportada. Para obter mais informações, consulte [scripts de inicialização no âmbito do Cluster](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Cancelar uma execução de aprendizagem automática

Quando utiliza automatizada capacidades de machine learning no Azure Databricks, para cancelar uma execução e inicie uma nova experiência de execução, reinicie o cluster do Azure Databricks.

### <a name="10-iterations-for-automated-machine-learning"></a>> 10 iterações para aprendizagem automatizada

Na máquina automatizada aprendizagem definições, se tiver mais de 10 iterações, defina `show_output` para `False` quando submeter a execução.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Widget para o Azure Machine Learning SDK/automatizada machine learning

O widget do SDK do Azure Machine Learning não é suportado num bloco de notas do Databricks, uma vez que os blocos de notas, não é possível analisar widgets HTML. Pode ver o widget no portal usando este código de Python no seu celular de bloco de notas do Azure Databricks:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Erro de importação: Nenhum módulo com o nome 'pandas.core.indexes'

Se vir este erro quando utiliza automatizada aprendizagem:

1. Execute este comando para instalar dois pacotes no seu cluster do Azure Databricks: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Desanexar e, em seguida, voltar a anexar o cluster para o bloco de notas. 

Se isso não resolver o problema, tente reiniciar o cluster.

## <a name="azure-portal"></a>Portal do Azure

Se vá diretamente para ver a sua área de trabalho a partir de uma ligação de partilha do SDK ou o portal, não será capaz de exibir a página de descrição geral normal com informações de subscrição na extensão. Também não será capaz de alternar para outra área de trabalho. Se precisar de ver a outra área de trabalho, a solução é ir diretamente para o [portal do Azure](https://portal.azure.com) e procure o nome de área de trabalho.

## <a name="diagnostic-logs"></a>Registos de diagnósticos

Por vezes, pode ser útil se pode fornecer informações de diagnóstico quando pedir ajuda. Para ver alguns registos, visite [portal do Azure](https://portal.azure.com) e aceda à sua área de trabalho e selecione **área de trabalho > experimentação > executar > registos**.

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
