---
title: Configurar um serviço de QnA Maker-QnA Maker
titleSuffix: Azure Cognitive Services
description: Antes de poder criar quaisquer bases de dados de conhecimento do QnA Maker, primeiro tem de configurar um serviço QnA Maker no Azure. Qualquer pessoa com autorização para criar novos recursos numa subscrição pode configurar o serviço QnA Maker.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: bec154a7e437621f377c503642b186cf166016cc
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195314"
---
# <a name="manage-qna-maker-resources"></a>Gerenciar QnA Maker recursos

Antes de poder criar quaisquer bases de dados de conhecimento do QnA Maker, primeiro tem de configurar um serviço QnA Maker no Azure. Qualquer pessoa com autorização para criar novos recursos numa subscrição pode configurar o serviço QnA Maker.

## <a name="types-of-keys-in-qna-maker"></a>Tipos de chaves no QnA Maker

O serviço QnA Maker lida com dois tipos de chaves, **chaves de subscrição** e **chaves de ponto final**.

![Gestão de chaves](../media/qnamaker-how-to-key-management/key-management.png)

|Name|Location|Objetivo|
|--|--|--|
|Chave de Subscrição|[Azure portal](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|Essas chaves são usadas para acessar as [APIs do serviço de gerenciamento de QnA Maker](https://go.microsoft.com/fwlink/?linkid=2092179). Essas APIs permitem que você edite as perguntas e respostas em sua base de dados de conhecimento e publique sua base de dados de conhecimento. Essas chaves são criadas quando você cria um novo serviço de QnA Maker.<br><br>Localize essas chaves no recurso de **Serviços cognitivas** , na página **chaves** .|
|Chave do ponto de extremidade|[Portal de QnA Maker](http://www.qnamaker.ai)|Essas chaves são usadas para acessar o ponto de extremidade da base de dados de conhecimento publicado para obter uma resposta para uma pergunta de usuário. Normalmente, você usa esse ponto de extremidade em seu bot de chat ou código de aplicativo cliente que se conecta ao serviço de QnA Maker. Essas chaves são criadas quando você publica sua base de dados de conhecimento QnA Maker.<br><br>Localize essas chaves na página **configurações do serviço** . Localize essa página na parte superior, menu do usuário direito como uma opção na lista suspensa.|

## <a name="create-a-new-qna-maker-service"></a>Criar um novo serviço QnA Maker

Este procedimento cria os recursos do Azure necessários para gerenciar o conteúdo da base de dados de conhecimento. Ao concluir essas etapas, você encontrará as chaves de _assinatura_ na página **chaves** do recurso no portal do Azure.

1. Entre no portal do Azure e [crie um recurso de QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) .

1. Selecione **criar** depois de ler os termos e condições.

    ![Criar um novo serviço QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. Na **QnA Maker**, selecione as camadas apropriadas e regiões.

    ![Criar um novo serviço QnA Maker - regiões e escalão de preço](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Preencher o **nome** com um nome exclusivo para identificar este serviço QnA Maker. Este nome também identifica o ponto de extremidade do QnA Maker para que as bases de dados de conhecimento será associados.
    * Escolha o **subscrição** em que o recurso do QnA Maker será implementado.
    * Selecione o **tipo de preço** para os serviços de gerenciamento de QnA Maker (portal e APIs de gerenciamento). Ver [aqui](https://aka.ms/qnamaker-pricing) para obter detalhes sobre os preços dos SKUs.
    * Criar uma nova **grupo de recursos** (recomendado) ou utilize um já existente na qual implementar este recurso do QnA Maker. QnA Maker cria vários recursos do Azure; ao criar um grupo de recursos para manter esses recursos, você pode facilmente localizar, gerenciar e excluir esses recursos pelo nome do grupo de recursos.
    * Selecione um **local do grupo de recursos**.
    * Escolha o **pesquisa escalão de preço** do serviço Azure Search. Se vir a opção de escalão gratuito a cinzento, significa que já tem um escalão gratuito do Azure Search implementado na sua subscrição. Nesse caso, terá de começar com o escalão básico do Azure Search. Ver detalhes dos preços do Azure search [aqui](https://azure.microsoft.com/pricing/details/search/).
    * Escolha o **localização de pesquisa** onde pretende que os dados do Azure Search a serem implantados. Restrições na qual os dados do cliente devem ser armazenados informará o local escolhido para o Azure Search.
    * Dê um nome ao seu serviço de aplicações **nome da aplicação**.
    * Por predefinição, o serviço de aplicações por predefinição para o escalão (S1) standard. Pode alterar o plano após a criação. Ver mais detalhes de preços do App service [aqui](https://azure.microsoft.com/pricing/details/app-service/).
    * Escolha o **localização do site** onde o serviço de aplicações será implementado.

        > [!NOTE]
        > A localização de pesquisa pode ser diferente da localização de Web site.

    * Escolha se pretende ativar **Application Insights** ou não. Se **Application Insights** é ativada, a ferramenta QnA Maker recolhe telemetria no tráfego, logs de bate-papo e erros.
    * Escolha o **localização de informações da aplicação** onde o recurso do Application Insights será implementado.
    * Para medidas de economia de custo, você pode [compartilhar](#share-existing-services-with-qna-maker) alguns, mas não todos os recursos do Azure criados para QnA Maker. 

1. Depois que todos os campos forem validados, selecione **criar**. Pode levar alguns minutos para ser concluído.

1. Depois de concluída a implementação, verá os seguintes recursos criados na sua subscrição.

    ![Recurso criado um novo serviço QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

    O recurso com o tipo _cognitiva Services_ tem suas chaves de _assinatura_ .

## <a name="find-subscription-keys-in-azure-portal"></a>Localizar chaves de assinatura no portal do Azure

Você pode exibir e redefinir suas chaves de assinatura, que editam o do portal do Azure em que você criou o recurso de QnA Maker. 

1. Vá para o recurso de QnA Maker no portal do Azure e selecione o recurso com o tipo _Serviços cognitivas_.

    ![Lista de recursos do QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Aceda a **chaves**.

    ![Chave de subscrição](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-qna-maker-portal"></a>Localizar chaves de ponto de extremidade no portal QnA Maker

Chaves de ponto de extremidade podem ser geridas a partir da [portal do QnA Maker](https://qnamaker.ai).

1. Faça logon no [portal de QnA Maker](https://qnamaker.ai), acesse seu perfil e clique em **configurações de serviço**.

    ![Chave de ponto final](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Ver ou repor as suas chaves.

    ![Gestor de chave de ponto final](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Atualize as suas chaves se achar que as mesmas foram comprometidas. Isso pode exigir alterações correspondentes no seu aplicativo cliente ou código de bot.

## <a name="share-existing-services-with-qna-maker"></a>Compartilhar serviços existentes com o QnA Maker

QnA Maker cria vários recursos do Azure. Para reduzir o gerenciamento e beneficiar-se do compartilhamento de custos, use a tabela a seguir para entender o que você pode e não consegue compartilhar:

|Serviço|Partilhar|Reason|
|--|--|--|
|Serviços Cognitivos|X|Não é possível por design|
|Plano do serviço de aplicações|✔|Espaço em disco fixo alocado para um plano do serviço de aplicativo. Se outros aplicativos, compartilhando o mesmo plano do serviço de aplicativo, usarem um espaço em disco significativo, o serviço de aplicativo do QnAMaker será executado em problemas.|
|Serviço de aplicações|X|Não é possível por design|
|Application Insights|✔|Pode ser compartilhado|
|Serviço de pesquisa|✔|1. `testkb` é um nome reservado para o serviço QnAMaker, ele não pode ser usado por outros.<br>2. O mapa de sinônimos `synonym-map` pelo nome é reservado para o serviço QnAMaker.<br>3. O número de KBs publicados é limitado pela camada de serviço de pesquisa. Se houver índices livres disponíveis, outros serviços poderão usá-lo.|

### <a name="using-a-single-search-service"></a>Usando um único serviço de pesquisa

Se você criar um serviço QnA e suas dependências (como pesquisa) por meio do portal, um serviço de pesquisa será criado para você e vinculado ao serviço de QnA Maker. Depois que esses recursos forem criados, você poderá atualizar a configuração do serviço de aplicativo para usar um serviço de pesquisa já existente e remover o serviço de pesquisa recém-criado.

Se você criar um serviço QnA por meio de modelos de Azure Resource Manager, poderá criar todos os recursos e controlar a criação do serviço de aplicativo para usar um serviço de pesquisa existente. 

## <a name="upgrade-qna-maker"></a>Atualizar QnA Maker

|Actualizar|Reason|
|--|--|
|[Atualizar](#upgrade-qna-maker-sku) o SKU de gerenciamento de QnA Maker|Você precisa ter mais perguntas e respostas em sua base de dados de conhecimento.|
|[Atualizar](#upgrade-app-service) o SKU do serviço de aplicativo|Sua base de dados de conhecimento precisa atender a mais solicitações de seu aplicativo cliente, como um bot de chat.|
|[Atualizar](#upgrade-azure-search-service) o Serviço de Azure Search|Você planeja ter muitas bases de dados de conhecimento.|


### <a name="upgrade-qna-maker-sku"></a>Atualizar QnA Maker SKU

Quando precisar ter mais perguntas e respostas em sua base de dados de conhecimento, além da sua camada atual, atualize seu tipo de preço do QnA Maker Service. 

Para atualizar o SKU de gerenciamento de QnA Maker:

1. Vá para o recurso de QnA Maker no portal do Azure e selecione **tipo de preço**.

    ![QnA Maker recurso](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Escolha a SKU apropriada e pressione **selecionar**.

    ![Preços de QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>Atualizar serviço de aplicativo

 Quando sua base de dados de conhecimento precisar atender a mais solicitações de seu aplicativo cliente, atualize seu tipo de preço do serviço de aplicativo.

Você pode [escalar](https://docs.microsoft.com/azure/app-service/manage-scale-up) ou reduzir verticalmente o serviço de aplicativo.

Vá para o recurso serviço de aplicativo no portal do Azure e selecione as opções **escalar** vertical ou reduzir conforme necessário.

![Escala do serviço de aplicativo QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-azure-search-service"></a>Atualizar Azure Search serviço

Quando você planeja ter muitas bases de dados de conhecimento, atualize seu tipo de preço do Azure Search Service. 

No momento, não é possível executar uma atualização in-loco do SKU do Azure Search. No entanto, você pode criar um novo recurso de Azure Search com a SKU desejada, restaurar os dados para o novo recurso e vinculá-los ao QnA Maker Stack.

1. Crie um novo recurso de Azure Search no portal do Azure e escolha a SKU desejada.

    ![QnA Maker recurso do Azure Search](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Restaure os índices do seu recurso original do Azure Search para o novo. Consulte o código de exemplo de restauração de backup [aqui](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Depois que os dados forem restaurados, vá para o novo recurso do Azure Search, selecione **chaves**e anote o **nome** e a **chave de administração**.

    ![QnA Maker chaves do Azure Search](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Para vincular o novo recurso de Azure Search à pilha de QnA Maker, vá para o serviço de aplicativo QnA Maker.

    ![QnA Maker appservice](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Selecione **configurações do aplicativo** e substitua os campos **AzureSearchName** e **AzureSearchAdminKey** da etapa 3.

    ![QnA Maker configuração de appservice](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Reinicie o serviço de aplicações.

    ![Reinicialização de QnA Maker appservice](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-latest-runtime-updates"></a>Obter atualizações de tempo de execução mais recentes

O tempo de execução do QnAMaker faz parte do serviço Azure App implantado quando você [cria um serviço QnAMaker](./set-up-qnamaker-service-azure.md) no portal do Azure. As atualizações são feitas periodicamente o tempo de execução. QnA Maker serviço de aplicativo está no modo de atualização automática após a versão da extensão de site de abril de 2019 (versão 5 +). Isso já foi projetado para cuidar do tempo de inatividade ZERO durante as atualizações. 

Você pode verificar a versão atual em https://www.qnamaker.ai/UserSettings. Se sua versão for anterior à versão 5. x, você deverá reiniciar o serviço de aplicativo para aplicar as atualizações mais recentes.

1. Aceda ao seu serviço de QnAMaker (grupo de recursos) [portal do Azure](https://portal.azure.com)

    ![Grupo de recursos do Azure do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Clique na aplicação do serviço e abra a secção de descrição geral

     ![Serviço de aplicações do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Reinicie o serviço de aplicações. Ele deve ser concluído dentro de alguns segundos. Quaisquer aplicativos dependentes ou bots que usam esse serviço QnAMaker não estarão disponíveis para os usuários finais durante esse período de reinicialização.

    ![Reiniciar o serviço de aplicações QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="management-service-region"></a>Região do serviço de gerenciamento

O serviço de gerenciamento do QnA Maker, usado somente para o portal de QnA Maker & para processamento de dados inicial, está disponível apenas no oeste dos EUA. Nenhum dado do cliente é armazenado neste serviço oeste dos EUA.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar e publicar uma base de dados de conhecimento](../Quickstarts/create-publish-knowledge-base.md)
