---
title: Gerir uma conta do Video Indexer
titlesuffix: Azure Media Services
description: Este artigo mostra como gerir uma conta do Video Indexer ligada ao Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 1515a026ae297a960f220a97449d2258c0b75e58
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553610"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Gerir uma conta do Video Indexer ligada ao Azure

Este artigo demonstra como gerir uma conta do Video Indexer que está ligada à sua subscrição do Azure e uma conta de Media Services do Azure.

> [!NOTE]
> Tem de ser o titular de conta do Video Indexer fazer conta ajustamentos de configuração, abordados neste tópico.

## <a name="prerequisites"></a>Pré-requisitos

Ligue a sua conta do Video Indexer para o Azure, conforme descrito em [ligado ao Azure](connect-to-azure.md). 

Lembre-se de que siga [pré-requisitos](connect-to-azure.md#prerequisites) e reveja [considerações](connect-to-azure.md#considerations) no artigo.

## <a name="examine-account-settings"></a>Examinar as definições da conta

Esta seção examina as definições da sua conta do Video Indexer.

Para ver as definições:

1. Clique no ícone do utilizador no canto superior direito e selecione **definições**.

    ![Definições](./media/manage-account-connected-to-azure/select-settings.png)

2. Sobre o **definições** página, selecione a **conta** separador.

Se a sua conta do indexador de vídeos está ligada ao Azure, consulte o seguinte:

* O nome da conta de Media Services do Azure subjacente.
* O número de tarefas de indexação em execução e em fila.
* O número e tipo de unidades reservadas alocado.

Se a conta tem de alguns ajustes, será apresentado relevantes erros e avisos sobre a configuração da conta a **definições** página. As mensagens contêm links para exatas locais no portal do Azure em que precisa de fazer alterações. Para obter mais informações, consulte a [erros e avisos](#errors-and-warnings) secção que se segue.

## <a name="auto-scale-reserved-units"></a>Unidades reservada de dimensionamento automático

O **definições** página permite-lhe definir o dimensionamento automático de suporte de dados de unidades reservadas (UR). Se a opção estiver **no**, pode alocar o número máximo de RUs e não se esqueça de que o indexador de vídeo termina/começa RUs automaticamente. Com esta opção, não tenha despesas com dinheiro extra para o tempo de inatividade, mas também não espere para indexação tarefas a concluir muito tempo quando a carga de indexação é elevada.

Dimensionamento automático não dimensiona abaixo 1 RU ou acima do limite predefinido de conta de Media Services. Para aumentar o limite, crie um pedido de serviço. Para obter informações sobre as quotas e limitações e como abrir um pedido de suporte, consulte [Quotas e limitações](../../media-services/previous/media-services-quotas-and-limitations.md).

![Inscrever-se](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Erros e avisos

Se a conta tem de alguns ajustes, verá relevantes erros e avisos sobre a configuração da conta a **definições** página. As mensagens contêm links para exatas locais no portal do Azure em que precisa de fazer alterações. Esta secção fornece mais detalhes sobre as mensagens de aviso e erro.

* Event Grid

    Tem de registar o fornecedor de recursos de EventGrid no portal do Azure. Na [portal do Azure](https://portal.azure.com/), aceda à **subscrições** > [subscrição] > **ResourceProviders** > **Microsoft.EventGrid**. Se não estiver da **registada** de estado, clique em **registar**. Demora alguns minutos para se registar. 

* Ponto final de Transmissão em fluxo

    Certifique-se a conta de serviços de suporte de dados subjacente tem a predefinição **ponto final de transmissão em fluxo** num estado iniciado. Caso contrário, não será capaz de ver vídeos desta conta dos serviços de multimédia ou em Video Indexer.

* Unidades Reservadas de Multimédia 

    Tem de alocar unidades reservadas de multimédia no seu recurso de serviço de multimédia a vídeos do índice. Para otimizar o desempenho indexação, é recomendado alocar, pelo menos, 10 unidades reservadas de S3. Para obter informações sobre preços, consulte a secção de FAQ do [preços de serviços de multimédia](https://azure.microsoft.com/pricing/details/media-services/) página.   

## <a name="next-steps"></a>Passos Seguintes

Pode interagir programaticamente com a sua conta de avaliação e/ou com as suas contas do indexador de vídeo que estiver ligadas ao azure ao seguir as instruções em: [Utilizar APIs](video-indexer-use-apis.md).

Deve usar o mesmo utilizador do Azure AD que utilizou quando ligar ao Azure.
