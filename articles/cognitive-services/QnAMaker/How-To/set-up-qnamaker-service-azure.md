---
title: Crie um serviço QnA Maker - QnA Maker
description: Antes de criar quaisquer bases de conhecimento do QnA Maker, tem primeiro de configurar um serviço QnA Maker em Azure. Qualquer pessoa com autorização para criar novos recursos numa subscrição pode criar um serviço QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: b6ab131c0fa81609b956de53f2b15d445e8979dd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102219272"
---
# <a name="manage-qna-maker-resources"></a>Gerir os recursos do Fabricante QnA

Antes de criar quaisquer bases de conhecimento do QnA Maker, tem primeiro de configurar um serviço QnA Maker em Azure. Qualquer pessoa com autorização para criar novos recursos numa subscrição pode criar um serviço QnA Maker.

Uma compreensão sólida dos seguintes conceitos é útil antes de criar o seu recurso:

* [Recursos do Fabricante QNA](../Concepts/azure-resources.md)
* [Chaves de autoria e publicação](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>Criar um novo serviço QnA Maker

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

Este procedimento cria os recursos Azure necessários para gerir o conteúdo da base de conhecimento. Depois de completar estes passos, encontrará as teclas _de subscrição_ na página **Chaves** para o recurso no portal Azure.

1. Inscreva-se no portal Azure e crie um recurso [QnA Maker.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)

1. Selecione **Criar** depois de ler os termos e condições:

    ![Criar um novo serviço QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. No **QnA Maker,** selecione os níveis e regiões apropriados:

    ![Criar um novo serviço QnA Maker - nível de preços e regiões](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * No campo **Nome,** insira um nome único para identificar este serviço QnA Maker. Este nome também identifica o ponto final do QnA Maker com o que as suas bases de conhecimento estarão associadas.
    * Escolha a **Subscrição** sob a qual o recurso QnA Maker será implantado.
    * Selecione o **nível de preços** para os serviços de gestão do QnA Maker (APIs de portal e gestão). Veja [mais detalhes sobre os preços da SKU.](https://aka.ms/qnamaker-pricing)
    * Crie um novo **grupo de Recursos** (recomendado) ou utilize um existente para implementar este recurso QnA Maker. A QnA Maker cria vários recursos Azure. Quando cria um grupo de recursos para deter estes recursos, pode facilmente encontrar, gerir e eliminar esses recursos pelo nome do grupo de recursos.
    * Selecione uma **localização do grupo de recursos**.
    * Escolha o **nível de preços de pesquisa** do serviço de Pesquisa Cognitiva Azure. Se a opção Free tier não estiver disponível (parece escurecida), significa que já tem um serviço gratuito implantado através da sua subscrição. Nesse caso, terás de começar pelo nível básico. Consulte os [detalhes dos preços da Pesquisa Cognitiva Azure](https://azure.microsoft.com/pricing/details/search/).
    * Escolha o **local de pesquisa** onde deseja que os índices de Pesquisa Cognitiva do Azure sejam implantados. As restrições sobre onde os dados do cliente devem ser armazenados ajudarão a determinar o local que escolher para a Azure Cognitive Search.
    * No campo nome da **App,** insira um nome para a sua instância do Serviço de Aplicações Azure.
    * Por predefinição, o Serviço de Aplicações de Aplicação está em incumprimento do nível padrão (S1). Pode mudar o plano após a criação. Saiba mais sobre [os preços do Serviço de Aplicações.](https://azure.microsoft.com/pricing/details/app-service/)
    * Escolha a **localização** do Site onde o Serviço de Aplicações será implementado.

        > [!NOTE]
        > O **Local de Pesquisa** pode diferir da Localização do **Site.**

    * Escolha se deseja ou não ativar insights de **aplicação**. Se **o Application Insights** estiver ativado, o QnA Maker recolhe telemetria no tráfego, registos de chat e erros.
    * Escolha o **local de insights** da App onde o recurso Application Insights será implementado.
    * Para medidas de poupança de custos, você pode [compartilhar](configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource) alguns, mas não todos os recursos Azure criados para a QnA Maker.

1. Depois de todos os campos serem validados, **selecione Criar.** O processo pode levar alguns minutos para ser concluído.

1. Após a implementação concluída, verá os seguintes recursos criados na sua subscrição:

   ![Recurso criou um novo serviço QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

    O recurso com o tipo _serviços cognitivos_ tem as suas chaves _de subscrição._

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

Este procedimento cria os recursos Azure necessários para gerir o conteúdo da base de conhecimento. Depois de completar estes passos, encontrará as teclas *de subscrição* na página **Chaves** para o recurso no portal Azure.

1. Inscreva-se no portal Azure e crie um recurso [QnA Maker.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)

1. Selecione **Criar** depois de ler os termos e condições:

    ![Criar um novo serviço QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. No **QnA Maker,** verifique a caixa de verificação Gerida (pré-visualização) e selecione os níveis e regiões apropriados:

    ![Criar um novo serviço gerido pela QnA Maker - nível de preços e regiões](../media/qnamaker-how-to-setup-service/enter-qnamaker-v2-info.png)

    * Escolha a **Subscrição** sob a qual o recurso QnA Maker será implantado.
    * Crie um novo **grupo de Recursos** (recomendado) ou utilize um existente para implementar este recurso gerido (Preview) do Criador QnA. QnA Maker gerido (Preview) cria poucos recursos Azure. Quando cria um grupo de recursos para deter estes recursos, pode facilmente encontrar, gerir e eliminar esses recursos pelo nome do grupo de recursos.
    * No campo **Nome,** insira um nome único para identificar este serviço gerido (Preview) gerido pelo QnA Maker. 
    * Escolha a **localização** onde pretende que o serviço (Preview) gerido pelo QnA Maker seja implantado. As APIs de gestão e o ponto final de serviço serão hospedados neste local. 
    * Selecione o **nível de preços** para o serviço gerido (Preview) (Pré-visualização) (grátis para pré-visualização). Veja [mais detalhes sobre os preços da SKU.](https://aka.ms/qnamaker-pricing)
    * Escolha o **local de pesquisa** onde deseja que os índices de Pesquisa Cognitiva do Azure sejam implantados. As restrições sobre onde os dados do cliente devem ser armazenados ajudarão a determinar o local que escolher para a Azure Cognitive Search.
    * Escolha o **nível de preços de pesquisa** do serviço de Pesquisa Cognitiva Azure. Se a opção Free tier não estiver disponível (parece escurecida), significa que já tem um serviço gratuito implantado através da sua subscrição. Nesse caso, terás de começar pelo nível básico. Consulte os [detalhes dos preços da Pesquisa Cognitiva Azure](https://azure.microsoft.com/pricing/details/search/).

1. Depois de todos os campos serem validados, selecione **Review + Create**. O processo pode levar alguns minutos para ser concluído.

1. Após a implementação concluída, verá os seguintes recursos criados na sua subscrição:

    ![Recurso criou um novo serviço QnA Maker gerido (Preview)](../media/qnamaker-how-to-setup-service/resources-created-v2.png)

    O recurso com o tipo _serviços cognitivos_ tem as suas chaves _de subscrição._

---

## <a name="upgrade-azure-resources"></a>Atualizar recursos da Azure

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

### <a name="upgrade-qna-maker-sku"></a>Upgrade QnA Maker SKU

Quando quiser ter mais perguntas e respostas na sua base de conhecimento, para além do seu nível atual, atualize o seu nível de preços de serviço QnA Maker.

Para atualizar a gestão da QnA Maker SKU:

1. Aceda ao seu recurso QnA Maker no portal Azure e **selecione o nível de preços**.

    ![Recurso do Fabricante QnA](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Escolha o SKU apropriado e prima **Select**.

    ![Preços do Fabricante QnA](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)
    
### <a name="upgrade-app-service"></a>Atualizar serviço de aplicativos

Quando a sua base de conhecimentos precisar de servir mais pedidos a partir da sua aplicação de cliente, atualize o seu nível de preços do Serviço de Aplicações.

Pode [escalar](../../../app-service/manage-scale-up.md) ou escalar o Serviço de Aplicações.

Aceda ao recurso Serviço de Aplicações no portal Azure e selecione a opção **Scale up** ou **Scale out** conforme necessário.

![Escala de serviço de aplicativos do Fabricante QnA](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Atualizar o serviço de Pesquisa Cognitiva Azure

Se planeia ter muitas bases de dados de conhecimento, atualize o escalão de preço do serviço Azure Cognitive Search.

Atualmente, não é possível realizar uma atualização no local da pesquisa Azure SKU. No entanto, pode criar um novo recurso de pesquisa Azure com o SKU pretendido, restaurar os dados para o novo recurso e, em seguida, ligá-lo à pilha QnA Maker. Para tal, siga estes passos:

1. Crie um novo recurso de pesquisa Azure no portal Azure e selecione o SKU desejado.

    ![QnA Maker Azure recurso de pesquisa](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Restaurar os índices do seu recurso de pesquisa original Azure para o novo. Veja o [código de exemplo de restauro de cópia de segurança](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Depois de os dados terem sido restaurados, aceda ao seu novo recurso de pesquisa Azure, selecione **Keys,** e escreva o **Nome** e a **tecla Admin**:

    ![Chaves de pesquisa do Fabricante QNA Azure](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Para ligar o novo recurso de pesquisa Azure à stack do QnA Maker, vá à instância do Serviço de Aplicações do Criador de QnA.

    ![QnA Maker App Service Instance](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Selecione **Definições da aplicação** e modifique as definições nos campos **AzureSearchName** e **AzureSearchAdminKey** do passo 3.

    ![Definição do serviço de aplicações do fabricante QnA](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Reinicie a instância do Serviço de Aplicações.

    ![Reiniciar a instância do Serviço de Aplicações do Criador QnA](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)
    
### <a name="inactivity-policy-for-free-search-resources"></a>Política de inatividade para recursos de pesquisa gratuitos

Se não estiver a utilizar um recurso de fabricante QnA, deve remover todos os recursos. Se não remover recursos não utilizados, a sua base de conhecimento deixará de funcionar se criar um recurso de Pesquisa gratuito.

Os recursos de pesquisa gratuita são eliminados após 90 dias sem receber uma chamada da API.
    
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Atualizar o serviço de Pesquisa Cognitiva Azure

Se planeia ter muitas bases de dados de conhecimento, atualize o escalão de preço do serviço Azure Cognitive Search.

Atualmente, não é possível realizar uma atualização no local da pesquisa Azure SKU. No entanto, pode criar um novo recurso de pesquisa Azure com o SKU pretendido, restaurar os dados para o novo recurso e, em seguida, ligá-lo à pilha QnA Maker. Para tal, siga estes passos:

1. Crie um novo recurso de pesquisa Azure no portal Azure e selecione o SKU desejado.

    ![QnA Maker Azure recurso de pesquisa](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Restaurar os índices do seu recurso de pesquisa original Azure para o novo. Veja o [código de exemplo de restauro de cópia de segurança](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Para ligar o novo recurso de pesquisa Azure ao serviço gerido (Preview) do QnA Maker, consulte o tópico abaixo.

### <a name="inactivity-policy-for-free-search-resources"></a>Política de inatividade para recursos de pesquisa gratuitos

Se não estiver a utilizar um recurso de fabricante QnA, deve remover todos os recursos. Se não remover recursos não utilizados, a sua base de conhecimento deixará de funcionar se criar um recurso de Pesquisa gratuito.

Os recursos de pesquisa gratuita são eliminados após 90 dias sem receber uma chamada da API.

---

## <a name="delete-azure-resources"></a>Eliminar recursos do Azure

Se eliminar algum dos recursos Azure utilizados para as suas bases de conhecimento QnA Maker, as bases de conhecimento deixarão de funcionar. Antes de eliminar quaisquer recursos, certifique-se de que exporta as suas bases de conhecimento a partir da página **Definições.**

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [serviço app](../../../app-service/index.yml) e serviço [de pesquisa.](../../../search/index.yml)

> [!div class="nextstepaction"]
> [Saiba como autor com os outros](../index.yml)
