---
title: 'Tutorial: Pré-requisitos para serviços cognitivos em Azure Synapse Analytics'
description: Saiba como configurar os pré-requisitos para a utilização dos Serviços Cognitivos em Azure Synapse.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 3ab861caca0ef6f58c2c1bc722412774deb725ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98936678"
---
# <a name="tutorial-prerequisites-for-using-cognitive-services-in-azure-synapse-analytics"></a>Tutorial: Pré-requisitos para a utilização de Serviços Cognitivos em Azure Synapse Analytics

Neste tutorial, você vai aprender como configurar os pré-requisitos para usar de forma segura os Serviços Cognitivos Azure em Azure Synapse Analytics.

Este tutorial aborda:
> [!div class="checklist"]
> - Crie um recurso de Serviços Cognitivos como Text Analytics ou Detetor de Anomalias.
> - Armazenar uma chave de autenticação para os recursos dos Serviços Cognitivos como segredos no Cofre da Chave Azure e configurar o acesso para um espaço de trabalho Azure Synapse Analytics.
> - Crie um serviço Azure Key Vault ligado no seu espaço de trabalho Azure Synapse Analytics.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- [Espaço de trabalho Azure Synapse Analytics](../get-started-create-workspace.md) com uma conta de armazenamento Azure Data Lake Gen2 configurada como o armazenamento padrão. Você precisa ser o colaborador de *dados blob* de armazenamento do sistema de ficheiros Azure Data Lake Storage Gen2 com o que trabalha.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-cognitive-services-resource"></a>Criar um recurso dos Serviços Cognitivos

[A Azure Cognitive Services](../../cognitive-services/index.yml) inclui muitos tipos de serviços. Text Analytics and Anomaly Detetor são dois exemplos nos tutoriais do Azure Synapse.

Pode criar um recurso [Text Analytics](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) no portal Azure:

![Screenshot que mostra Text Analytics no portal, com o botão Criar.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

Pode criar um recurso [de Detetor de Anomalias](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) no portal Azure:

![Screenshot que mostra Detetor de Anomalias no portal, com o botão Criar.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

## <a name="create-a-key-vault-and-configure-secrets-and-access"></a>Crie um cofre chave e configuure segredos e acesso

1. Crie um [cofre chave](https://ms.portal.azure.com/#create/Microsoft.KeyVault) no portal Azure.
2. Vá às políticas de Acesso ao **Cofre chave**,  >  e conceda ao espaço de [trabalho Azure Synapse permissãos](../security/synapse-workspace-managed-identity.md) para ler segredos do Azure Key Vault.

   > [!NOTE]
   > Certifique-se de que as mudanças de política são guardadas. Este passo é fácil de perder.

   ![Screenshot que mostra seleções para adicionar uma política de acesso.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Vá ao seu recurso de Serviços Cognitivos. Por exemplo, aceda às chaves **do detetor de**  >  **anomalias e ao ponto final.** Em seguida, copie qualquer uma das duas chaves da área de transferência.

4. Vá ao **Key Vault**  >  **Secret** para criar um novo segredo. Especifique o nome do segredo e, em seguida, cole a chave do passo anterior para o campo **Valor.** Por fim, **selecione Criar**.

   ![Screenshot que mostra seleções para criar um segredo.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

   > [!IMPORTANT]
   > Lembre-se ou anote este nome secreto. Vai usá-lo mais tarde quando ligar aos Serviços Cognitivos do Azure Synapse Studio.

## <a name="create-an-azure-key-vault-linked-service-in-azure-synapse"></a>Criar um serviço azure key vault ligado em Azure Synapse

1. Abra o seu espaço de trabalho no Azure Synapse Studio. 
2. Ir para **gerir**  >  **serviços ligados**. Crie um serviço ligado ao **Azure Key Vault** apontando para o cofre chave que acabou de criar. 
3. Verifique a ligação selecionando o botão **de ligação Teste.** Se a ligação estiver verde, **selecione Criar** e, em seguida, selecione **Publicar tudo** para guardar a sua alteração.

![Screenshot que mostra Azure Key Vault como um novo serviço ligado.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

Está agora pronto para continuar com um dos tutoriais para usar a experiência Azure Cognitive Services no Azure Synapse Studio.

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Análise de sentimento com serviços cognitivos Azure](tutorial-cognitive-services-sentiment.md)
- [Tutorial: Deteção de anomalias com serviços cognitivos Azure](tutorial-cognitive-services-sentiment.md)
- [Tutorial: Modelo de machine learning pontuação em Azure Synapse dedicada SQL Pools](tutorial-sql-pool-model-scoring-wizard.md).
- [Capacidades de Machine Learning no Azure Synapse Analytics](what-is-machine-learning.md)