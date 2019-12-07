---
title: Gerenciar uma conta de Video Indexer
titleSuffix: Azure Media Services
description: Este artigo demonstra como gerenciar uma conta de Video Indexer conectada ao Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 4f8491e31747eda9cbe8689ba7db3026df0ff3ad
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892775"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Gerenciar uma conta de Video Indexer conectada ao Azure

Este artigo demonstra como gerenciar uma conta de Video Indexer que está conectada à sua assinatura do Azure e uma conta dos serviços de mídia do Azure.

> [!NOTE]
> Você precisa ser o proprietário da conta de Video Indexer para fazer ajustes de configuração de conta discutidos neste tópico.

## <a name="prerequisites"></a>Pré-requisitos

Conecte sua conta do Video Indexer ao Azure, conforme descrito em [conectado ao Azure](connect-to-azure.md). 

Certifique-se de seguir os [pré-requisitos](connect-to-azure.md#prerequisites) e examine as [considerações](connect-to-azure.md#considerations) no artigo.

## <a name="examine-account-settings"></a>Examinar configurações de conta

Esta seção examina as configurações de sua conta de Video Indexer.

Para exibir as configurações:

1. Clique no ícone de usuário no canto superior direito e selecione **configurações**.

    ![Definições](./media/manage-account-connected-to-azure/select-settings.png)

2. Na página **configurações** , selecione a guia **conta** .

Se sua conta do indexador de vídeos estiver conectada ao Azure, você verá o seguinte:

* O nome da conta de serviços de mídia do Azure subjacente.
* O número de trabalhos de indexação em execução e na fila.
* O número e o tipo de unidades reservadas alocadas.

Se sua conta precisar de alguns ajustes, você verá erros relevantes e avisos sobre a configuração da sua conta na página **configurações** . As mensagens contêm links para os locais exatos em portal do Azure onde você precisa fazer alterações. Para obter mais informações, consulte a seção [erros e avisos](#errors-and-warnings) a seguir.

## <a name="auto-scale-reserved-units"></a>Unidades reservadas de dimensionamento automático

A página **configurações** permite que você defina o dimensionamento automático de ru (unidades reservadas de mídia). Se a opção estiver **ativada**, você poderá alocar o número máximo de RUs e certificar-se de que Video indexer para de confirmar/iniciar RUs automaticamente. Com essa opção, você não paga dinheiro extra por tempo ocioso, mas também não espera que os trabalhos de indexação sejam concluídos há muito tempo quando a carga de indexação é alta.

O dimensionamento automático não é dimensionado abaixo de 1 RU ou acima do limite padrão da conta dos serviços de mídia. Para aumentar o limite, crie uma solicitação de serviço. Para obter informações sobre cotas e limitações e como abrir um tíquete de suporte, consulte [cotas e limitações](../../media-services/previous/media-services-quotas-and-limitations.md).

![Inscrição](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Erros e avisos

Se sua conta precisar de alguns ajustes, você verá erros relevantes e avisos sobre a configuração da sua conta na página **configurações** . As mensagens contêm links para os locais exatos em portal do Azure onde você precisa fazer alterações. Esta seção fornece mais detalhes sobre as mensagens de erro e de aviso.

* Event Grid

    Você precisa registrar o provedor de recursos EventGrid usando o portal do Azure. No [portal do Azure](https://portal.azure.com/), acesse **assinaturas** > [subscription] > **ResourceProviders** > **Microsoft. EventGrid**. Se não estiver no estado **registrado** , clique em **registrar**. É preciso registrar alguns minutos. 

* Ponto de extremidade de streaming

    Verifique se a conta de serviços de mídia subjacente tem o **ponto de extremidade de streaming** padrão em um estado iniciado. Caso contrário, você não poderá assistir a vídeos desta conta de serviços de mídia ou em Video Indexer.

* Unidades Reservadas de Multimédia 

    Você deve alocar unidades reservadas de mídia em seu recurso de serviço de mídia para indexar vídeos. Para obter um desempenho de indexação ideal, é recomendável alocar pelo menos 10 unidades reservadas S3. Para obter informações sobre preços, consulte a seção perguntas frequentes da página de [preços dos serviços de mídia](https://azure.microsoft.com/pricing/details/media-services/) .   

## <a name="next-steps"></a>Passos seguintes

Você pode interagir de forma programática com sua conta de avaliação e/ou com suas contas de Video Indexer que estão conectadas ao Azure seguindo as instruções em: [usar APIs](video-indexer-use-apis.md).

Você deve usar o mesmo usuário do Azure AD usado ao conectar-se ao Azure.
