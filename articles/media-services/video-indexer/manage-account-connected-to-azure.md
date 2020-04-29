---
title: Gerir uma conta de Indexer de Vídeo
titleSuffix: Azure Media Services
description: Saiba como gerir uma conta de Indexer de Vídeo ligada ao Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/16/2019
ms.author: juliako
ms.openlocfilehash: 5b9ec1c04dd90af9d9380a1f4d30386c8f9cc3ed
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79499663"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Gerir uma conta de Indexer de Vídeo ligada ao Azure

Este artigo demonstra como gerir uma conta de Indexer de Vídeo que está ligada à sua subscrição Azure e a uma conta Azure Media Services.

> [!NOTE]
> Tem de ser o dono da conta do Indexer de Vídeo para fazer ajustes de configuração de conta discutidos neste tópico.

## <a name="prerequisites"></a>Pré-requisitos

Ligue a sua conta de Indexer de Vídeo ao Azure, conforme descrito em [Connected to Azure](connect-to-azure.md).

Certifique-se de seguir [os pré-requisitos](connect-to-azure.md#prerequisites) e rever [considerações](connect-to-azure.md#considerations) no artigo.

## <a name="examine-account-settings"></a>Examinar as definições da conta

Esta secção examina as definições da sua conta De Indexer de Vídeo.

Para ver as definições:

1. Clique no ícone do utilizador no canto superior direito e selecione **Definições**.

    ![Definições no Indexante de Vídeo](./media/manage-account-connected-to-azure/select-settings.png)

2. Na página **Definições,** selecione o separador **Conta.**

Se a sua conta De Indexer de Vídeos estiver ligada ao Azure, vê as seguintes coisas:

* O nome da conta subjacente da Azure Media Services.
* O número de postos de trabalho indexados em execução e em fila.
* O número e o tipo de unidades reservadas atribuídas.

Se a sua conta necessitar de alguns ajustes, verá erros e avisos relevantes sobre a configuração da sua conta na página **Definições.** As mensagens contêm links para locais exatos no portal Azure onde é necessário fazer alterações. Para mais informações, consulte a secção de [erros e advertências](#errors-and-warnings) que se segue.

## <a name="repair-the-connection-to-azure"></a>Reparar a ligação ao Azure

Na **ligação Update ao** diálogo Azure Media Services da sua página do Indexer de [Vídeo,](https://www.videoindexer.ai/) é-lhe pedido que forneça valores para as seguintes definições:

|Definição|Descrição|
|---|---|
|ID de subscrição azure|O ID de subscrição pode ser recuperado do portal Azure. Clique em **Todos os serviços** do painel esquerdo e procure por "subscrições". Selecione **Subscrições** e escolha o ID desejado na lista das suas subscrições.|
|Nome do grupo de recursos azure Media Services|O nome do grupo de recursos em que criou a conta de Media Services.|
|ID da aplicação|O ID de aplicação Azure AD (com permissões para a conta de Serviços de Media especificado) que criou para esta conta De Indexer de Vídeo. <br/><br/>Para obter o ID da aplicação, navegue para o portal Azure. Na conta media Services, escolha a sua conta e vá ao **Acesso API**. Selecione **Connect to Media Services API com** -> a**app Azure AD**principal do serviço . Copie os parâmetros relevantes.|
|Chave de aplicação|A chave de aplicação Azure AD associada à sua conta de Serviços de Media que especificou acima. <br/><br/>Para obter a chave da aplicação, navegue para o portal Azure. Na conta media Services, escolha a sua conta e vá ao **Acesso API**. Selecione **Ligar à Media Services API com o principal serviço** -> Gerir certificados de**aplicação** -> **& segredos**. Copie os parâmetros relevantes.|

## <a name="autoscale-reserved-units"></a>Unidades reservadas à escala automática

A página **Definições** permite-lhe definir a automatização de unidades reservadas aos meios de comunicação (RU). Se a opção estiver **on**, pode alocar o número máximo de RUs e certifique-se de que o Indexer de Vídeo para/inicia automaticamente as RUs. Com esta opção, você não paga dinheiro extra por tempo inativo, mas também não espera que os empregos indexantes completem muito tempo quando a carga de indexação é alta.

A escala automática não escala abaixo de 1 RU ou acima do limite padrão da conta de Serviços de Media. Para aumentar o limite, crie um pedido de serviço. Para obter informações sobre quotas e limitações e como abrir um bilhete de apoio, consulte [Quotas e limitações.](../../media-services/previous/media-services-quotas-and-limitations.md)

![Indexador de vídeo de unidades reservadas de escala automática](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Erros e advertências

Se a sua conta necessitar de alguns ajustes, vê erros e avisos relevantes sobre a configuração da sua conta na página **Definições.** As mensagens contêm links para locais exatos no portal Azure onde é necessário fazer alterações. Esta secção dá mais detalhes sobre o erro e mensagens de aviso.

* EventGrid

    Tem de registar o fornecedor de recursos EventGrid utilizando o portal Azure. No [portal Azure,](https://portal.azure.com/)vá a **Subscrições** > [subscrição] > **ResourceProviders** > **Microsoft.EventGrid**. Caso contrário no estado **registado,** selecione **Registar**. Leva alguns minutos para se registar.

* Ponto final de transmissão em fluxo

    Certifique-se de que a conta subjacente aos Serviços de Media tem o **ponto final** de streaming padrão num estado iniciado. Caso contrário, não pode ver vídeos desta conta de Media Services ou no Video Indexer.

* Unidades reservadas aos meios de comunicação

    Deve atribuir Unidades Reservadas de Mídia no seu recurso media service para indexar vídeos. Para um ótimo desempenho de indexação, é aconselhável alocar pelo menos 10 Unidades S3 Reservadas. Para obter informações sobre preços, consulte a secção DE PERGUNTAS FAQ da página de preços dos [Serviços de Media.](https://azure.microsoft.com/pricing/details/media-services/)

## <a name="next-steps"></a>Passos seguintes

Pode interagir programáticamente com a sua conta de teste ou contas do Indexer de Vídeo que estão ligadas ao Azure seguindo as instruções em: [Utilize APIs](video-indexer-use-apis.md).

Utilize o mesmo utilizador da AD Azure que utilizou quando ligar ao Azure.
