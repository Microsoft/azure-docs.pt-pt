---
title: Início rápido executar um bloco de notas no seu próprio servidor de bloco de notas
titleSuffix: Azure Machine Learning service
description: Introdução ao serviço Azure Machine Learning. Utilize o seu próprio servidor de bloco de notas local para experimentar a sua área de trabalho.  A área de trabalho é o bloco fundamental na cloud que utilizar para testar, preparar e implementar modelos de aprendizagem automática.
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
ms.reviewer: sgilley
author: sdgilley
ms.author: sgilley
ms.date: 03/21/2019
ms.custom: seodec18
ms.openlocfilehash: 8bb601ac5c1ed8e9ca6d2027fe1b774a938e1f0a
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58362412"
---
# <a name="quickstart-use-your-own-notebook-server-to-get-started-with-azure-machine-learning"></a>Início rápido: Utilizar o seu próprio servidor de bloco de notas para começar a utilizar com o Azure Machine Learning

Neste artigo, vai utilizar seu próprio servidor de bloco de notas para executar o código que tem sessão iniciado a [área de trabalho do Azure Machine Learning serviço](concept-azure-machine-learning-architecture.md). A área de trabalho é o bloco fundamental na cloud que utilizar para testar, preparar e implementar modelos de aprendizagem automática com o Machine Learning.

Este início rápido utiliza o seu próprio ambiente de Python e o servidor de bloco de notas do Jupyter. Para um guia de introdução sem instalação, consulte [início rápido: Utilizar um servidor de bloco de notas com base na cloud para começar a utilizar com o Azure Machine Learning](quickstart-run-cloud-notebook.md) 

Ver uma vídeo versão deste início rápido:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

Neste início rápido, irá:

* Escrever código que regista os valores dentro da área de trabalho.
* Ver os valores registados na área de trabalho.

Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

## <a name="prerequisites"></a>Pré-requisitos

* Um servidor de bloco de notas do Python 3.6 com o SDK do Azure Machine Learning instalado
* Uma área de trabalho do serviço do Azure Machine Learning
* Um ficheiro de configuração da área de trabalho (**aml_config/config.json** ).

Obter todos os estes pré-requisitos da [criar uma área de trabalho do serviço do Azure Machine Learning](setup-create-workspace.md#portal).


## <a name="use-the-workspace"></a>Utilizar a área de trabalho

Criar um script ou iniciar um bloco de notas no mesmo diretório do ficheiro de configuração de área de trabalho. Execute esse código que utiliza as APIs básicas do SDK para controlar execuções da experimentação.

1. Crie uma experimentação na área de trabalho.
1. Inicie a sessão de um valor único para a experimentação.
1. Inicie a sessão de uma lista de valores para a experimentação.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]

## <a name="view-logged-results"></a>Ver resultados registados

Quando a execução for concluída, pode ver a execução da experimentação no portal do Azure. Para imprimir um URL que navega para os resultados para a última execução, utilize o seguinte código:

```python
print(run.get_portal_url())
```

Utilize a ligação para ver os valores registados no portal do Azure no seu browser.

![Valores com sessão iniciada no portal do Azure](./media/quickstart-run-local-notebook/logged-values.png)

## <a name="clean-up-resources"></a>Limpar recursos 

>[!IMPORTANT]
>Pode usar os recursos que criou aqui como pré-requisitos para outros tutoriais de Machine Learning e referência artigos.

Se não planeja usar os recursos que criou neste artigo, eliminá-los para evitar incorrer em quaisquer custos.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, criou os recursos que necessários para experimentar e implementar modelos. Executou o código num bloco de notas e explorou o histórico de execução para o código na sua área de trabalho na cloud.

> [!div class="nextstepaction"]
> [Tutorial: Preparar um modelo de classificação de imagem](tutorial-train-models-with-aml.md)

Também pode explorar [mais avançadas exemplos no GitHub](https://aka.ms/aml-notebooks) ou visualizar a [guia de utilizador do SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
