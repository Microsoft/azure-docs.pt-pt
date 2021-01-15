---
title: 'Tutorial: Pré-requisitos para serviços cognitivos em Azure Synapse'
description: Tutorial para a configuração dos pré-requisitos para a utilização dos Serviços Cognitivos em Azure Synapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: eef65db05ab94b5b8de5ff82c2c51dba0730f170
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222178"
---
# <a name="tutorial-pre-requisites-for-using-cognitive-services-in-azure-synapse"></a>Tutorial: Pré-requisitos para a utilização de Serviços Cognitivos em Azure Synapse

Neste tutorial, você vai aprender como configurar os pré-requisitos para alavancar seguramente os Serviços Cognitivos em Azure Synapse.

Este tutorial aborda:
> [!div class="checklist"]
> - Criar recursos de Serviços Cognitivos. Por exemplo, Análise de Texto ou Detetor de Anomalias.
> - Armazenar chave de autenticação para os recursos dos Serviços Cognitivos como segredos no cofre da Chave Azure e configurar o acesso para o espaço de trabalho Azure Synapse.
> - Crie o serviço de abóbada Azure Key ligado no seu espaço de trabalho Azure Synapse Analytics.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- [Espaço de trabalho Azure Synapse Analytics](../get-started-create-workspace.md) com uma conta de armazenamento ADLS Gen2 configurada como o armazenamento predefinido. Tem de ser o colaborador de dados da **Blob** de Armazenamento do sistema de ficheiros ADLS Gen2 com o que trabalha.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/)

## <a name="create-a-cognitive-services-resource"></a>Criar um recurso de serviços cognitivos

[Os Serviços Cognitivos Azure](../../cognitive-services/index.yml) incluem muitos tipos diferentes de serviços. Abaixo estão alguns exemplos que são usados nos tutoriais da Sinapse.

### <a name="create-an-anomaly-detector-resource"></a>Criar um recurso de detetor de anomalias
Criar um [Detetor de Anomalias](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) no portal Azure.

![Criar detetor de anomalias](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

### <a name="create-a-text-analytics-resource"></a>Criar um recurso de Análise de Texto
Crie um recurso [text Analytics](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) no portal Azure.

![Criar análise de texto](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

## <a name="create-key-vault-and-configure-secrets-and-access"></a>Criar Cofre chave e configurar segredos e acesso

1. Crie um [Cofre chave](https://ms.portal.azure.com/#create/Microsoft.KeyVault) no portal Azure.
2. Vá às **políticas key Vault -> Access**, e conceda ao espaço de trabalho [Azure Synapse permissãos](../security/synapse-workspace-managed-identity.md) para ler segredos do Azure Key Vault.

>Certifique-se de que as mudanças de política são guardadas. Este passo é fácil de perder.

![Adicionar política de acesso](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Aceda ao seu recurso de Serviço Cognitivo, por **exemplo, Detetor de Anomalias -> Chaves e Ponto Final**, copie qualquer uma das duas teclas da área de transferência.

4. Vá ao **Key Vault -> Secret** para criar um novo segredo. Especifique o nome do segredo e, em seguida, cole a chave do passo anterior para o campo "Valor". Por fim, clique em **Criar**.

![Criar segredo](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

> Lembre-se ou anote este nome secreto! Irá usá-lo mais tarde quando ligar aos Serviços Cognitivos do Azure Synapse Studio.

## <a name="create-azure-keyvault-linked-service-in-azure-synapse"></a>Criar serviço ligado a Azure Keyvault em Azure Synapse

1. Abra o seu espaço de trabalho no Azure Synapse Studio. Navegue para **gerir serviços ligados >**. Crie um serviço ligado a ab "Azure Key Vault" que indique o Cofre de Chaves que acabámos de criar. Em seguida, verifique a ligação clicando no botão "Test connection" e verificando se está verde. Se alguma coisa funcionar bem, clique em "Criar" primeiro e, em seguida, clique em "Publicar tudo" para guardar a sua alteração.
![Serviço ligado](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

Está agora pronto para continuar com um dos tutoriais para usar a experiência Azure Cognitive Services no Azure Synapse Studio.

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Análise de sentimento com serviços cognitivos Azure](tutorial-cognitive-services-sentiment.md)
- [Tutorial: Deteção de anomalias com serviços cognitivos Azure](tutorial-cognitive-services-sentiment.md)
- [Tutorial: Modelo de machine learning pontuação em Azure Synapse dedicada SQL Pools](tutorial-sql-pool-model-scoring-wizard.md).
- [Capacidades de aprendizagem automática em Azure Synapse Analytics](what-is-machine-learning.md)