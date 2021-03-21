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
ms.date: 01/14/2021
ms.author: juliako
ms.openlocfilehash: 25025efe5254442c4862563ce329cfae69e7d033
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98898448"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Gerir uma conta do Video Indexer ligada ao Azure

Este artigo demonstra como gerir uma conta de Video Indexer que está ligada à sua subscrição Azure e a uma conta Azure Media Services.

> [!NOTE]
> Tem de ser o proprietário da conta do Video Indexer para fazer ajustes de configuração de conta discutidos neste tópico.

## <a name="prerequisites"></a>Pré-requisitos

Ligue a sua conta de Indexer de Vídeo ao Azure, conforme descrito no [Connected to Azure](connect-to-azure.md).

Certifique-se de seguir [Pré-Requisitos](connect-to-azure.md#prerequisites-for-connecting-to-azure) e rever [considerações](connect-to-azure.md#azure-media-services-considerations) no artigo.

## <a name="examine-account-settings"></a>Examinar as definições da conta

Esta secção examina as definições da sua conta de Indexer de Vídeo.

Para visualizar as definições:

1. Clique no ícone do utilizador no canto superior direito e selecione **Definições**.

    ![Definições no Indexador de Vídeo](./media/manage-account-connected-to-azure/select-settings.png)

2. Na página **Definições,** selecione o separador **'Contas'.**

Se a sua conta de Indexer de Vídeos estiver ligada ao Azure, veja as seguintes coisas:

* O nome da conta subjacente da Azure Media Services.
* O número de postos de trabalho a funcionar e em fila.
* O número e o tipo de unidades reservadas atribuídas.

Se a sua conta necessitar de alguns ajustes, verá erros e avisos relevantes sobre a configuração da sua conta na página **Definições.** As mensagens contêm links para locais exatos no portal Azure onde é necessário fazer alterações. Para mais informações, consulte a secção [de erros e avisos](#errors-and-warnings) que se segue.

## <a name="repair-the-connection-to-azure"></a>Reparar a ligação ao Azure

Na **ligação Azure Media Services** da sua página [de Indexer](https://www.videoindexer.ai/) de Vídeo, é-lhe pedido que forneça valores para as seguintes definições:

|Definições|Descrição|
|---|---|
|ID de assinatura Azure|O ID de subscrição pode ser recuperado a partir do portal Azure. Clique em **Todos os serviços** no painel esquerdo e procure por "subscrições". Selecione **Subscrições** e escolha o ID desejado na lista das suas subscrições.|
|Nome do grupo de recursos Azure Media Services|O nome do grupo de recursos em que criou a conta dos Serviços de Comunicação Social.|
|ID da Aplicação|O ID de aplicação AZure (com permissões para a conta de Serviços de Mídia especificadas) que criou para esta conta de Indexer de Vídeo. <br/><br/>Para obter o ID da aplicação, navegue para o portal Azure. Na conta dos Serviços de Comunicação Social, escolha a sua conta e vá ao **API Access.** Selecione **Connect to Media Services API com a app AD principal do serviço.**  ->   Copie os parâmetros relevantes.|
|Chave de aplicação|A chave de aplicação AD AZure associada à sua conta de Serviços de Mídia que especificou acima. <br/><br/>Para obter a chave da aplicação, navegue para o portal Azure. Na conta dos Serviços de Comunicação Social, escolha a sua conta e vá ao **API Access.** Selecione **Connect to Media Services API com o principal serviço** Gerir os  ->    ->  **certificados de aplicação & segredos.** Copie os parâmetros relevantes.|

## <a name="autoscale-reserved-units"></a>Dimensionar automaticamente unidades reservadas

A página **Definições** permite-lhe definir a autoscalagem das unidades reservadas aos meios de comunicação (RU). Se a opção estiver **On,** pode alocar o número máximo de RUs e certificar-se de que o Indexante de Vídeo para/inicia automaticamente as RUs. Com esta opção, não se paga dinheiro extra pelo tempo inativo, mas também não se espera que os trabalhos de indexação completem muito tempo quando a carga de indexação é elevada.

A autoescala não escala abaixo de 1 RU ou acima do limite padrão da conta Serviços de Comunicação. Para aumentar o limite, crie um pedido de serviço. Para obter informações sobre quotas e limitações e como abrir um bilhete de apoio, consulte [Quotas e limitações.](../../media-services/previous/media-services-quotas-and-limitations.md)

![Unidades reservadas automáticas Indexador de Vídeo](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Erros e avisos

Se a sua conta necessitar de alguns ajustes, vê erros e avisos relevantes sobre a configuração da sua conta na página **Definições.** As mensagens contêm links para locais exatos no portal Azure onde é necessário fazer alterações. Esta secção dá mais detalhes sobre o erro e as mensagens de aviso.

* EventGrid

    Tem de registar o fornecedor de recursos EventGrid utilizando o portal Azure. No [portal Azure,](https://portal.azure.com/)vá a **Subscrições** > [subscrição] > **ResourceProviders**  >  **Microsoft.EventGrid**. Se não estiver no estado **registado,** **selecione Registar-se**. Leva alguns minutos para se registar.

* Ponto final de transmissão em fluxo

    Certifique-se de que a conta subjacente aos Serviços de Comunicação social tem o **ponto final de streaming** padrão num estado iniciado. Caso contrário, não pode ver vídeos desta conta dos Media Services ou no Video Indexer.

* Unidades reservadas de multimédia

    Tem de alocar Unidades Reservadas de Mídia no seu recurso Media Service para indexar vídeos. Para um melhor desempenho de indexação, recomenda-se alocar pelo menos 10 Unidades Reservadas S3. Para obter informações sobre preços, consulte a secção faq da página [de preços dos Serviços de Mídia.](https://azure.microsoft.com/pricing/details/media-services/)

## <a name="next-steps"></a>Passos seguintes

Pode interagir programáticamente com a sua conta experimental ou contas de Indexer de Vídeo que estão ligadas ao Azure seguindo as instruções em: [Use APIs](video-indexer-use-apis.md).

Utilize o mesmo utilizador AZure AD que usou ao ligar ao Azure.
