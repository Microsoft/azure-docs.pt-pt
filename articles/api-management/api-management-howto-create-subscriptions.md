---
title: Criar subscrições na Azure API Management Microsoft Docs
description: Saiba como criar subscrições na Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: f8b2238eb0fab9aeeb42d11b4176c0d681b5f8e5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "70073529"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Criar subscrições na Gestão de API do Azure

Quando publica APIs através da Azure API Management, é fácil e comum garantir o acesso a essas APIs utilizando teclas de subscrição. As aplicações do cliente que precisam de consumir as APIs publicadas devem incluir uma chave de subscrição válida em pedidos HTTP quando fazem chamadas para essas APIs. Para obter uma chave de subscrição para aceder a APIs, é necessária uma subscrição. Para obter mais informações sobre subscrições, consulte [Subscrições na Azure API Management](api-management-subscriptions.md).

Este artigo percorre os passos para a criação de subscrições no portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para tomar as medidas neste artigo, os pré-requisitos são os seguintes:

+ [Criar uma instância de Gestão da API.](get-started-create-service-instance.md)
+ Compreender [subscrições na API Management.](api-management-subscriptions.md)

## <a name="create-a-new-subscription"></a>Criar uma nova subscrição

1. Selecione **Subscrições** no menu à esquerda.
2. Selecione **Adicionar subscrição**.
3. Fornecer um nome da subscrição e selecionar o âmbito.
4. Opcionalmente, escolha se a subscrição deve ser associada a um utilizador.
5. Selecione **Guardar**.

![Assinaturas flexíveis](./media/api-management-subscriptions/flexible-subscription.png)

Depois de criar a subscrição, são fornecidas duas teclas API para aceder às APIs. Uma chave é primária, e uma é secundária. 

## <a name="next-steps"></a>Passos seguintes
Obtenha mais informações sobre a Gestão da API:

+ Aprenda [outros conceitos](api-management-terminology.md) na API Management.
+ Siga os [nossos tutoriais](import-and-publish.md) para saber mais sobre a API Management.
+ Consulte a nossa [página de PERGUNTAS Frequentes](api-management-faq.md) para obter questões comuns.