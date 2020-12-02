---
title: 'Quickstart: Link um espaço de trabalho de aprendizagem de máquinas Azure'
description: Ligue o seu espaço de trabalho da Sinapse a um espaço de trabalho de aprendizagem de máquinas Azure
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: a5d84b936679cdd5ebd5bec4938d6db7fcb641e7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455503"
---
# <a name="quickstart-create-a-new-azure-machine-learning-linked-service-in-synapse"></a>Quickstart: Criar um novo serviço de aprendizagem automática Azure em Synapse

Neste arranque rápido, você vai ligar um espaço de trabalho Azure Synapse Analytics a um espaço de trabalho Azure Machine Learning. A ligação destes espaços de trabalho permite-lhe alavancar o Azure Machine Learning a partir de várias experiências na Synapse.

Por exemplo, esta ligação a um espaço de trabalho de aprendizagem automática Azure permite estas experiências:

- Executar os seus oleodutos Azure Machine Learning como um passo nos seus oleodutos Synapse. Para saber mais, consulte os oleodutos executar a [azure machine learning](/azure/data-factory/transform-data-machine-learning-service).

- Enriqueça os seus dados com previsões trazendo um modelo de aprendizagem automática do registo do modelo Azure Machine Learning e marque o modelo nas piscinas Sinapse SQL. Para mais detalhes, consulte [Tutorial: Assistente de pontuação de modelo de machine learning para piscinas Sinapse SQL](tutorial-sql-pool-model-scoring-wizard.md).

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/).
- [Espaço de trabalho Synapse Analytics](../get-started-create-workspace.md) com uma conta de armazenamento ADLS Gen2 configurada como o armazenamento padrão. Tem de ser o colaborador de dados da **Blob** de Armazenamento do sistema de ficheiros ADLS Gen2 com o que trabalha.
- [Espaço de trabalho de aprendizagem automática Azure.](/azure/machine-learning/how-to-manage-workspace)
- Você precisa de permissões (ou pedido de alguém que tenha permissões) para criar um principal de serviço e segredo que você pode usar para criar o serviço ligado. Note que este diretor de serviço precisa de ser atribuído o papel de contribuinte no espaço de trabalho de aprendizagem de máquinas Azure.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/)

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Este passo vai criar um novo diretor de serviços. Se quiser utilizar um diretor de serviço existente, pode saltar este passo.
1. Abre o Portal do Azure. 

1. Aceda às inscrições da **Azure Ative Directory**  ->  **App**.

1. Clique em **Novo registo**. Em seguida, siga as instruções da UI para registar uma nova aplicação.

1. Depois de o pedido ser registado. Gere um segredo para a aplicação. Aceda ao **certificado** de & Segredo de  ->  **Candidatura.** Clique **em Adicionar segredo** de cliente para gerar um segredo. Guarde o segredo e será usado mais tarde.

   ![Gerar segredo](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00a.png)

1. Crie um diretor de serviço para a aplicação. Vá à **sua visão** geral da sua  ->  **Overview** aplicação e, em seguida, clique em **Criar o principal do serviço**. Em alguns casos, este diretor de serviço é criado automaticamente.

   ![Criar um principal de serviço](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00b.png)

1. Adicione o diretor de serviço como "contribuinte" do espaço de trabalho Azure Machine Learning. Note que isto exigirá ser proprietário do grupo de recursos a que pertence o espaço de trabalho Azure Machine Learning.

   ![Atribuir papel de contribuinte](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00c.png)

## <a name="create-a-linked-service"></a>Criar um serviço ligado

1. No espaço de trabalho da Sinaapse onde pretende criar o novo serviço Azure Machine Learning, vá ao serviço **Management**  ->  **Linked service** Linked, crie um novo serviço ligado com o tipo "Azure Machine Learning".

   ![Criar serviço ligado](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-create-linked-service-00a.png)

2. Preencha o formulário:

   - ID principal do serviço: Este é o ID de **aplicação (cliente)** da Aplicação.
  
     > [!NOTE]
     > Este não é o nome do pedido. Pode encontrar este ID na página geral da aplicação. Deve ser uma longa corda semelhante a esta "81707eac-ab38-406u-8f6c-10ce76a568d5".

   - Chave principal do serviço: O segredo que gerou na secção anterior.

3. Clique em **'Ligar o teste'** para verificar se a configuração está correta. Se o teste de ligação passar, clique em **Guardar**.

   Se o teste de ligação falhar, certifique-se de que o iD principal do serviço e o segredo estão corretos e tente novamente.

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Assistente de pontuação de modelo de machine learning - piscina de SQL dedicada](tutorial-sql-pool-model-scoring-wizard.md)
- [Capacidades de aprendizagem automática em Azure Synapse Analytics](what-is-machine-learning.md)
