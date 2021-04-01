---
title: Integração de Hubs de Notificações com Aplicações Móveis do Serviço de Aplicações
description: Saiba como funciona o Azure Notification Hubs com aplicações móveis do Azure App Service.
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 08/06/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e7042be7e407e8e0827e142ba6878dfff812e1f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88004058"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integração com as Aplicações Móveis do Serviço de Aplicações

Para facilitar uma experiência perfeita e unificadora em todos os serviços da Azure, [as Aplicações Móveis do Serviço de Aplicações](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) têm suporte incorporado para notificações usando hubs de notificação Azure. [O App Service Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) oferece uma plataforma de desenvolvimento de aplicações móveis altamente escalável e globalmente disponível para desenvolvedores de empresas e integradores de sistemas que traz um rico conjunto de capacidades para os desenvolvedores móveis.

Os desenvolvedores de Aplicações Móveis podem usar Os Centros de Notificação com o seguinte fluxo de trabalho:

1. Manípulo PNS do dispositivo de recuperação.
2. Registar o dispositivo com Os Centros de Notificação utilizando APIs de registo de aplicações móveis do cliente SDK.

    > [!NOTE]
    > Tenha em atenção que, por motivos de segurança, as Mobile Apps eliminam todas as etiquetas ao registarem-se. Trabalhar com Notification Hubs diretamente no seu back-end para associar etiquetas a dispositivos.

3. Envie notificações do seu backend da sua aplicação com os Centros de Notificação.

Algumas vantagens que esta integração proporciona são:

- **Aplicações Móveis Client SDKs**: Estes SDKs multiplataformas fornecem APIs para registo e comunicam com o centro de notificação que está ligado à aplicação móvel. Não precisa de credenciais de Centros de Notificação, nem de trabalhar com um serviço adicional.
  - *Empurre para o utilizador*: Os SDKs marcam automaticamente o dispositivo especificado com um ID autenticado por Aplicações Móveis para ativar o cenário de "empurrar para o utilizador".
  - *Empurre para* o dispositivo : Os SDKs utilizam automaticamente o ID de instalação de aplicações móveis como um GUID para se registarem com os Centros de Notificação, pelo que não há necessidade de manter vários GUIDs de serviço.
- **Modelo de instalação**: As Aplicações Móveis funcionam com o mais recente modelo push hubs de notificações para representar todas as propriedades push associadas a um dispositivo numa instalação JSON que se alinha com os Serviços de Notificação push e é fácil de usar.
- **Flexibilidade**: os programadores podem sempre optar por trabalhar diretamente com os Hubs de Notificação, mesmo com a integração instalada.
- **Experiência integrada no [portal Azure](https://portal.azure.com)**: Push como uma capacidade está representada visualmente em Aplicações Móveis, e os desenvolvedores podem facilmente trabalhar com o centro de notificação associado através de Aplicações Móveis.
