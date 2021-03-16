---
title: 'ML Studio (clássico): Migrar para Azure Machine Learning - Reconstruir experiência'
description: Reconstruir experiências do Estúdio (clássico) em Azure Machine Learning designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: bb944cb034fdd7cc51648314154a654bc1265533
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103565426"
---
# <a name="rebuild-a-studio-classic-experiment-in-azure-machine-learning"></a>Reconstruir uma experiência de Estúdio (clássico) em Azure Machine Learning

Neste artigo, aprende-se a reconstruir uma experiência studio (clássica) em Azure Machine Learning. Para obter mais informações sobre a migração do Studio (clássico), consulte [o artigo de visão geral da migração.](migrate-overview.md)

As experiências de estúdio **(clássicas)** são semelhantes a **oleodutos** em Azure Machine Learning. No entanto, nos oleodutos Azure Machine Learning são construídos no mesmo back-end que alimenta o SDK. Isto significa que você tem duas opções para o desenvolvimento de machine learning: o designer de arrastar e largar ou os SDKs de código..

Para obter mais informações sobre a construção de oleodutos com o SDK, consulte [o que são os oleodutos Azure Machine Learning](../concept-ml-pipelines.md#building-pipelines-with-the-python-sdk).


## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Uma área de trabalho do Azure Machine Learning. Criar um espaço de trabalho para [aprendizagem automática Azure.](../how-to-manage-workspace.md#create-a-workspace)
- Uma experiência de estúdio (clássico) para migrar.
- [Faça o upload do seu conjunto de dados](migrate-register-dataset.md) para Azure Machine Learning.

## <a name="rebuild-the-pipeline"></a>Reconstruir o oleoduto

Depois de [migrar o seu conjunto de dados para a Azure Machine Learning,](migrate-register-dataset.md)está pronto para recriar a sua experiência.

Em Azure Machine Learning, o gráfico visual é chamado de projeto de **gasoduto**. Nesta secção, você recria a sua experiência clássica como um rascunho de pipeline.

1. Vá ao estúdio Azure Machine Learning[(ml.azure.com)](https://ml.azure.com)
1. No painel de navegação  esquerdo, selecione > **Designer Módulos pré-construídos** ![ Screenshot mostrando como criar um novo projeto de pipeline.](../media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Reconstruir manualmente a sua experiência com módulos de design.
    
    Consulte a [tabela de mapeamento de módulos](migrate-overview.md#studio-classic-and-designer-module-mapping) para encontrar módulos de substituição. Muitos dos módulos mais populares do Studio (clássico) têm versões idênticas no designer.

    > [!Important]
    > Se a sua experiência utilizar o módulo Execut R Script, tem de executar passos adicionais para migrar a sua experiência. Para obter mais informações, consulte [os módulos Migrate R Script](migrate-execute-r-script.md).

1. Ajuste os parâmetros.
    
    Selecione cada módulo e ajuste os parâmetros no painel de definições do módulo para a direita. Utilize os parâmetros para recriar a funcionalidade da sua experiência Studio (clássica). Para obter mais informações sobre cada módulo, consulte a referência do [módulo.](../algorithm-module-reference/module-reference.md)

## <a name="submit-a-run-and-check-results"></a>Submeter uma execução e verificar resultados

Depois de recriar a sua experiência studio (clássica), é hora de submeter uma corrida de **pipeline**.

Uma corrida de gasoduto executa um **alvo de computação** ligado ao seu espaço de trabalho. Pode definir um alvo de computação predefinido para todo o pipeline, ou pode especificar alvos de cálculo numa base por módulo.

Uma vez que se submete uma fuga a partir de um projeto de oleoduto, transforma-se num **gasoduto**. Cada corrida de gasodutos é gravada e registada no Azure Machine Learning.

Para definir um alvo de computação padrão para todo o oleoduto:
1. Selecione o ícone **gear** ![ gear ícone no designer ao lado do nome do ](../media/tutorial-designer-automobile-price-train-score/gear-icon.png) pipeline.
1. **Selecione Selecione o alvo do cálculo**.
1. Selecione um cálculo existente ou crie um novo cálculo seguindo as instruções no ecrã.

Agora que o seu alvo de cálculo está definido, pode submeter uma corrida de gasoduto:

1. Na parte superior da tela, **selecione Enviar por favor.**
1. Selecione **Criar novo** para criar uma nova experiência.
    
    Experiências organizam gasodutos semelhantes juntos. Se executar um oleoduto várias vezes, pode selecionar a mesma experiência para execuções sucessivas. Isto é útil para registar e rastrear.
1. Insira um nome de experiência. Em seguida, **selecione Enviar .**

    A primeira corrida pode demorar até 20 minutos. Uma vez que as definições de computação padrão têm um tamanho mínimo de nó de 0, o designer deve alocar recursos depois de estar inativo. As sucessivas corridas demoram menos tempo, uma vez que os nós já estão atribuídos. Para acelerar o tempo de funcionamento, pode criar um sistema de computação com um tamanho mínimo de nó de 1 ou maior.

Após o final da execução, pode verificar os resultados de cada módulo:

1. Clique com o botão direito no módulo cuja saída pretende ver.
1. **Selecione visualizar,** **ver saída** ou **ver registo**.

    - **Visualizar**: Visualizar o conjunto de dados de resultados.
    - **Ver Saída**: Abra um link para o local de armazenamento da saída. Use isto para explorar ou descarregar a saída. 
    - **Ver Registo**: Ver registos de controlador e sistema. Utilize o **70_driver_log** para ver informações relacionadas com o seu script submetido ao utilizador, tais como erros e exceções.

> [!IMPORTANT]
> Os módulos de design usam pacotes Python de código aberto, em comparação com os pacotes C# em Studio (clássico). Como resultado, a saída do módulo pode variar ligeiramente entre o designer e o Studio (clássico). 


## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a reconstruir uma experiência studio (clássica) em Azure Machine Learning. O próximo passo é [reconstruir os serviços web em Azure Machine Learning.](migrate-rebuild-web-service.md)


Veja os outros artigos na série de migração Studio (clássica):

1. [Visão geral da migração](migrate-overview.md).
1. [Conjunto de dados migrar](migrate-register-dataset.md).
1. **Reconstruir um pipeline de treino studio (clássico).**
1. [Reconstruir um serviço web Studio (clássico).](migrate-rebuild-web-service.md)
1. [Integre um serviço web Azure Machine Learning com aplicações para clientes.](migrate-rebuild-integrate-with-client-app.md)
1. [Migrar Executar O Guião R](migrate-execute-r-script.md).
