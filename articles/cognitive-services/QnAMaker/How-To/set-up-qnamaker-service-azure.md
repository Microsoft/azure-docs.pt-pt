---
title: Configurar um serviço de QnA Maker-QnA Maker
titleSuffix: Azure Cognitive Services
description: Antes de criar qualquer QnA Maker base de dados de conhecimento, você deve primeiro configurar um serviço de QnA Maker no Azure. Qualquer pessoa com autorização para criar novos recursos em uma assinatura pode configurar um serviço de QnA Maker.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 6d52062561e3f08a214f3e191706583edc844786
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794219"
---
# <a name="manage-qna-maker-resources"></a>Gerenciar QnA Maker recursos

Antes de criar qualquer QnA Maker base de dados de conhecimento, você deve primeiro configurar um serviço de QnA Maker no Azure. Qualquer pessoa com autorização para criar novos recursos em uma assinatura pode configurar um serviço de QnA Maker.

## <a name="types-of-keys-in-qna-maker"></a>Tipos de chaves no QnA Maker

Seu serviço de QnA Maker lida com dois tipos de chaves: **chaves de assinatura** e chaves de ponto de **extremidade**.

![Gestão de chaves](../media/qnamaker-how-to-key-management/key-management.png)

|Nome|Localização|Objetivo|
|--|--|--|
|Chave de subscrição|[Portal do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|Essas chaves são usadas para acessar as [APIs do serviço de gerenciamento de QnA Maker](https://go.microsoft.com/fwlink/?linkid=2092179). Essas APIs permitem que você edite as perguntas e respostas em sua base de dados de conhecimento e publique sua base de dados de conhecimento. Essas chaves são criadas quando você cria um novo serviço de QnA Maker.<br><br>Localize essas chaves no recurso **Serviços cognitivas** na página **chaves** .|
|Chave do ponto de extremidade|[Portal de QnA Maker](http://www.qnamaker.ai)|Essas chaves são usadas para acessar o ponto de extremidade da base de dados de conhecimento publicado para obter uma resposta para uma pergunta de usuário. Normalmente, você usa esse ponto de extremidade em seu bot de chat ou no código do aplicativo cliente que se conecta ao serviço de QnA Maker. Essas chaves são criadas quando você publica sua base de dados de conhecimento QnA Maker.<br><br>Localize essas chaves na página **configurações de serviço** . Localize essa página no menu do usuário no canto superior direito da página no menu suspenso.|

## <a name="create-a-new-qna-maker-service"></a>Criar um novo serviço de QnA Maker

Este procedimento cria os recursos do Azure necessários para gerenciar o conteúdo da base de dados de conhecimento. Depois de concluir essas etapas, você encontrará as chaves de _assinatura_ na página **chaves** do recurso no portal do Azure.

1. Entre no portal do Azure e [crie um recurso de QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) .

1. Selecione **criar** depois de ler os termos e condições:

    ![Criar um novo serviço de QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. Em **QnA Maker**, selecione as camadas e regiões apropriadas:

    ![Criar um novo serviço de QnA Maker-tipo de preço e regiões](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * No campo **nome** , insira um nome exclusivo para identificar este QnA Maker serviço. Esse nome também identifica o ponto de extremidade QnA Maker ao qual suas bases de dados de conhecimento serão associadas.
    * Escolha a **assinatura** sob a qual o recurso de QnA Maker será implantado.
    * Selecione o **tipo de preço** para os serviços de gerenciamento de QnA Maker (portal e APIs de gerenciamento). Veja [mais detalhes sobre os preços de SKU](https://aka.ms/qnamaker-pricing).
    * Crie um novo **grupo de recursos** (recomendado) ou use um existente para implantar esse QnA Maker recurso. QnA Maker cria vários recursos do Azure. Ao criar um grupo de recursos para manter esses recursos, você pode facilmente localizar, gerenciar e excluir esses recursos pelo nome do grupo de recursos.
    * Selecione um **local do grupo de recursos**.
    * Escolha o **tipo de preço de pesquisa** do serviço de pesquisa cognitiva do Azure. Se a opção camada gratuita não estiver disponível (aparece esmaecida), isso significa que você já tem um serviço gratuito implantado por meio de sua assinatura. Nesse caso, você precisará começar com a camada básica. Consulte [detalhes de preços do Azure pesquisa cognitiva](https://azure.microsoft.com/pricing/details/search/).
    * Escolha o **local de pesquisa** onde você deseja que os índices de pesquisa cognitiva do Azure sejam implantados. As restrições sobre onde os dados do cliente devem ser armazenados ajudarão a determinar o local escolhido para o Azure Pesquisa Cognitiva.
    * No campo **nome do aplicativo** , insira um nome para sua instância de serviço de Azure app.
    * Por padrão, o serviço de aplicativo assume como padrão a camada padrão (S1). Você pode alterar o plano após a criação. Saiba mais sobre os [preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/).
    * Escolha o **local do site** em que o serviço de aplicativo será implantado.

        > [!NOTE]
        > O **local de pesquisa** pode ser diferente do **local do site**.

    * Escolha se deseja ou não habilitar **Application insights**. Se **Application insights** estiver habilitado, o QnA Maker coletará a telemetria no tráfego, nos logs de chat e nos erros.
    * Escolha o **local do Application insights** no qual o recurso de Application insights será implantado.
    * Para medidas de economia de custo, você pode [compartilhar](#share-existing-services-with-qna-maker) alguns, mas não todos os recursos do Azure criados para QnA Maker. 

1. Depois que todos os campos forem validados, selecione **criar**. O processo pode levar alguns minutos para ser concluído.

1. Após a conclusão da implantação, você verá os seguintes recursos criados em sua assinatura:

   ![O recurso criou um novo serviço de QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

    O recurso com o tipo de _Serviços cognitivas_ tem suas chaves de _assinatura_ .

## <a name="find-subscription-keys-in-the-azure-portal"></a>Localizar chaves de assinatura no portal do Azure

Você pode exibir e redefinir suas chaves de assinatura do portal do Azure, em que você criou o recurso de QnA Maker.

1. Vá para o recurso de QnA Maker na portal do Azure e selecione o recurso que tem o tipo de _Serviços cognitivas_ :

    ![Lista de recursos do QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Vá para **as chaves**:

    ![Chave de subscrição](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>Localizar chaves de ponto de extremidade no portal de QnA Maker

O ponto de extremidade está na mesma região que o recurso porque as chaves de ponto de extremidade são usadas para fazer uma chamada para a base de dados de conhecimento.

As chaves de ponto de extremidade podem ser gerenciadas no [portal de QnA Maker](https://qnamaker.ai).

1. Entre no portal de [QnA Maker](https://qnamaker.ai), acesse seu perfil e, em seguida, selecione **configurações de serviço**:

    ![Chave do ponto de extremidade](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Exibir ou redefinir suas chaves:

    ![Gerenciador de chaves de ponto de extremidade](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Atualize suas chaves se você acreditar que elas foram comprometidas. Isso pode exigir alterações correspondentes no seu aplicativo cliente ou código de bot.

## <a name="share-existing-services-with-qna-maker"></a>Compartilhar serviços existentes com o QnA Maker

QnA Maker cria vários recursos do Azure. Para reduzir o gerenciamento e beneficiar-se do compartilhamento de custos, use a tabela a seguir para entender o que você pode e não consegue compartilhar:

|Serviço|Partilhar|Razão|
|--|--|--|
|Serviços Cognitivos|X|Não é possível por design|
|Plano do App Service|✔|Espaço em disco fixo alocado para um plano do serviço de aplicativo. Se outros aplicativos que compartilham o mesmo plano do serviço de aplicativo usarem um espaço em disco significativo, a instância do serviço de aplicativo do QnAMaker encontrará problemas.|
|Serviço de Aplicações|X|Não é possível por design|
|Application Insights|✔|Pode ser compartilhado|
|Serviço de pesquisa|✔|1. `testkb` é um nome reservado para o serviço QnAMaker; Ele não pode ser usado por outras pessoas.<br>2. o mapa de sinônimos pelo nome `synonym-map` é reservado para o serviço QnAMaker.<br>3. o número de bases de dados de conhecimento publicadas é limitado pela camada de serviço de pesquisa. Se houver índices livres disponíveis, outros serviços poderão usá-los.|

Saiba mais sobre o serviço de [aplicativo](../../../app-service/index.yml) e o [serviço de pesquisa](../../../search/index.yml).

### <a name="using-a-single-search-service"></a>Usando um único serviço de pesquisa

Se você criar um serviço QnA e suas dependências (como pesquisa) por meio do portal, um serviço de pesquisa será criado para você e vinculado ao serviço de QnA Maker. Depois que esses recursos forem criados, você poderá atualizar a configuração do serviço de aplicativo para usar um serviço de pesquisa existente anteriormente e remover o que você acabou de criar.

Se você criar um serviço QnA por meio de modelos de Azure Resource Manager, poderá criar todos os recursos e controlar a criação do serviço de aplicativo para usar um serviço de pesquisa existente.

## <a name="upgrade-qna-maker"></a>Atualizar QnA Maker

|Atualizar|Razão|
|--|--|
|[Atualizar](#upgrade-qna-maker-sku) o SKU de gerenciamento de QnA Maker|Você deseja ter mais perguntas e respostas em sua base de dados de conhecimento.|
|[Atualizar](#upgrade-app-service) o SKU do serviço de aplicativo|Sua base de dados de conhecimento precisa atender a mais solicitações do aplicativo cliente, como um bot de chat.|
|[Atualizar](#upgrade-the-azure-cognitive-search-service) o Serviço de Pesquisa Cognitiva do Azure|Você planeja ter muitas bases de dados de conhecimento.|


### <a name="upgrade-qna-maker-sku"></a>Atualizar QnA Maker SKU

Quando você quiser ter mais perguntas e respostas em sua base de dados de conhecimento, além da sua camada atual, atualize seu tipo de preço do QnA Maker Service.

Para atualizar o SKU de gerenciamento de QnA Maker:

1. Vá para o recurso de QnA Maker no portal do Azure e selecione **tipo de preço**.

    ![QnA Maker recurso](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Escolha a SKU apropriada e pressione **selecionar**.

    ![Preços de QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>Atualizar serviço de aplicativo

 Quando sua base de dados de conhecimento precisar atender a mais solicitações de seu aplicativo cliente, atualize seu tipo de preço do serviço de aplicativo.

Você pode [escalar verticalmente](https://docs.microsoft.com/azure/app-service/manage-scale-up) ou escalar horizontalmente o serviço de aplicativo.

Vá para o recurso serviço de aplicativo no portal do Azure e selecione a opção **escalar verticalmente** ou **escalar** horizontalmente, conforme necessário.

![Escala do serviço de aplicativo QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Atualizar o serviço de Pesquisa Cognitiva do Azure

Se você planeja ter muitas bases de dados de conhecimento, atualize seu tipo de preço do serviço Pesquisa Cognitiva do Azure.

No momento, não é possível realizar uma atualização in-loco da SKU do Azure Search. No entanto, você pode criar um novo recurso de Azure Search com a SKU desejada, restaurar os dados para o novo recurso e vinculá-los ao QnA Maker Stack. Para tal, siga estes passos:

1. Crie um novo recurso de Azure Search no portal do Azure e selecione o SKU desejado.

    ![QnA Maker recurso do Azure Search](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Restaure os índices do seu recurso original do Azure Search para o novo. Consulte o [código de exemplo de restauração de backup](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Depois que os dados forem restaurados, vá para o novo recurso do Azure Search, selecione **chaves**e anote o **nome** e a **chave de administração**:

    ![QnA Maker chaves do Azure Search](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Para vincular o novo recurso de Azure Search à pilha de QnA Maker, vá para a instância do serviço de aplicativo QnA Maker.

    ![Instância do serviço de aplicativo QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Selecione **configurações do aplicativo** e modifique as configurações nos campos **AzureSearchName** e **AzureSearchAdminKey** da etapa 3.

    ![QnA Maker configuração do serviço de aplicativo](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Reinicie a instância do serviço de aplicativo.

    ![Reinicialização da instância do serviço de aplicativo QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>Obter as atualizações de tempo de execução mais recentes

O tempo de execução do QnAMaker faz parte da instância do serviço de Azure App que é implantada quando você [cria um serviço QnAMaker](./set-up-qnamaker-service-azure.md) no portal do Azure. As atualizações são feitas periodicamente para o tempo de execução. A instância do serviço de aplicativo QnA Maker está no modo de atualização automática após a versão da extensão do site de abril de 2019 (versão 5 +). Essa atualização foi projetada para cuidar do tempo de inatividade ZERO durante as atualizações.

Você pode verificar sua versão atual em https://www.qnamaker.ai/UserSettings. Se sua versão for anterior à versão 5. x, você deverá reiniciar o serviço de aplicativo para aplicar as atualizações mais recentes:

1. Vá para o serviço QnAMaker (grupo de recursos) no [portal do Azure](https://portal.azure.com).

    ![QnAMaker grupo de recursos do Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selecione a instância do serviço de aplicativo e abra a seção **visão geral** .

    ![Instância do serviço de aplicativo QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Reinicie o serviço de aplicativo. O processo de atualização deve ser concluído em alguns segundos. Quaisquer aplicativos dependentes ou bots que usam esse serviço QnAMaker não estarão disponíveis para os usuários finais durante esse período de reinicialização.

    ![Reinicialização da instância do serviço de aplicativo QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="management-service-region"></a>Região do serviço de gerenciamento

O serviço de gerenciamento do QnA Maker é usado somente para o portal de QnA Maker e para o processamento de dados inicial. Esse serviço está disponível apenas na região oeste dos EUA. Nenhum dado do cliente é armazenado neste serviço oeste dos EUA.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o serviço de [aplicativo](../../../app-service/index.yml) e o [serviço de pesquisa](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Criar e publicar uma base de dados de conhecimento](../Quickstarts/create-publish-knowledge-base.md)
