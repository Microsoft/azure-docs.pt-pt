---
title: Configurar um serviço QnA Maker - QnA Maker
titleSuffix: Azure Cognitive Services
description: Antes de poder criar quaisquer bases de dados de conhecimento do QnA Maker, primeiro tem de configurar um serviço QnA Maker no Azure. Qualquer pessoa com autorização para criar novos recursos numa subscrição pode configurar o serviço QnA Maker.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 4bc40c0d4d44ea4dd809f59965ec5d1107be8541
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439775"
---
# <a name="create-a-qna-maker-service"></a>Criar um serviço QnA Maker

Antes de poder criar quaisquer bases de dados de conhecimento do QnA Maker, primeiro tem de configurar um serviço QnA Maker no Azure. Qualquer pessoa com autorização para criar novos recursos numa subscrição pode configurar o serviço QnA Maker.

## <a name="create-a-new-service"></a>Criar um novo serviço

Este procedimento implementa alguns recursos do Azure. Juntos, esses recursos gerenciar o conteúdo de base de dados de conhecimento e fornecem capacidades de responder a pergunta, embora um ponto de extremidade.

1. Inicie sessão no portal do Azure e [criar um QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) recursos.

1. Selecione **criar** depois de ler os termos e condições.

    ![Criar um novo serviço QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. Na **QnA Maker**, selecione as camadas apropriadas e regiões.

    ![Criar um novo serviço QnA Maker - regiões e escalão de preço](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Preencher o **nome** com um nome exclusivo para identificar este serviço QnA Maker. Este nome também identifica o ponto de extremidade do QnA Maker para que as bases de dados de conhecimento será associados.
    * Escolha o **subscrição** em que o recurso do QnA Maker será implementado.
    * Selecione o **escalão de preço** para os serviços de gestão do QnA Maker (portal e APIs de gestão). Ver [aqui](https://aka.ms/qnamaker-pricing) para obter detalhes sobre os preços dos SKUs.
    * Criar uma nova **grupo de recursos** (recomendado) ou utilize um já existente na qual implementar este recurso do QnA Maker. A ferramenta QnA Maker cria vários recursos do Azure; Quando cria um grupo de recursos para manter estes recursos, pode encontrar facilmente, gerir e eliminar estes recursos com o nome do grupo de recursos.
    * Selecione um **localização do grupo de recursos**.
    * Escolha o **pesquisa escalão de preço** do serviço Azure Search. Se vir a opção de escalão gratuito a cinzento, significa que já tem um escalão gratuito do Azure Search implementado na sua subscrição. Nesse caso, terá de começar com o escalão básico do Azure Search. Ver detalhes dos preços do Azure search [aqui](https://azure.microsoft.com/pricing/details/search/).
    * Escolha o **localização de pesquisa** onde pretende que os dados do Azure Search a serem implantados. Restrições na qual os dados do cliente devem ser armazenados informará o local escolhido para o Azure Search.
    * Dê um nome ao seu serviço de aplicações **nome da aplicação**.
    * Por predefinição, o serviço de aplicações por predefinição para o escalão (S1) standard. Pode alterar o plano após a criação. Ver mais detalhes de preços do App service [aqui](https://azure.microsoft.com/pricing/details/app-service/).
    * Escolha o **localização do site** onde o serviço de aplicações será implementado.

        > [!NOTE]
        > A localização de pesquisa pode ser diferente da localização de Web site.

    * Escolha se pretende ativar **Application Insights** ou não. Se **Application Insights** é ativada, a ferramenta QnA Maker recolhe telemetria no tráfego, logs de bate-papo e erros.
    * Escolha o **localização de informações da aplicação** onde o recurso do Application Insights será implementado.
    * Para obter medidas de economia de custo, pode [partilhar](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker) mas nem todos os recursos do Azure criados para o QnA Maker. 

1. Depois de todos os campos são validados, pode selecionar **criar** para iniciar a implementação destes serviços na sua subscrição. Irá demorar alguns minutos a concluir.

1. Depois de concluída a implementação, verá os seguintes recursos criados na sua subscrição.

    ![Recurso criado um novo serviço QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="region-of-management-service"></a>Região do serviço de gestão

O serviço de gestão do QnA Maker, utilizada apenas para o portal & para processamento de dados inicial, está disponível apenas na região E.U.A. oeste. Não existem dados de cliente são armazenados neste serviço E.U.A. oeste.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar e publicar uma base de dados de conhecimento](../Quickstarts/create-publish-knowledge-base.md)
