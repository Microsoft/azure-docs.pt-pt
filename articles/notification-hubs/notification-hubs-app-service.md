---
title: Integração de Centros de Notificação com aplicações móveis do serviço de aplicações
description: Saiba como o Azure Notification Hubs funciona com aplicações móveis do Azure App Service.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: ''
ms.assetid: 83132dff-a01d-4b31-a426-b57496852b81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e6c4fb767e6237f390cdb467b35c323f637bebf2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76264157"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integração com as Aplicações Móveis do Serviço de Aplicações

Para facilitar uma experiência totalmente integrada e unificadora em todos os serviços do Azure, as [Aplicações Móveis do Serviço de Aplicações](../app-service-mobile/app-service-mobile-value-prop.md) têm suporte incorporado para notificações push com os Hubs de Notificação. As [Aplicações Móveis do Serviço de Aplicações](../app-service-mobile/app-service-mobile-value-prop.md) oferecem uma plataforma de desenvolvimento de aplicações móveis altamente dimensionável e globalmente disponível para Programadores Empresariais e Integradores de Sistemas, que fornece um conjunto completo de capacidades para programadores móveis.

Os programadores de Mobile Apps podem utilizar Notification Hubs com o fluxo de trabalho seguinte:

1. Obter o identificador PNS do dispositivo
2. Registar o dispositivo nos Hubs de Notificação através da conveniente API de registo do SDK de Cliente das Aplicações Móveis

    > [!NOTE]
    > Tenha em atenção que, por motivos de segurança, as Mobile Apps eliminam todas as etiquetas ao registarem-se. Trabalhar com Notification Hubs diretamente no seu back-end para associar etiquetas a dispositivos.

3. Enviar notificações a partir do seu back-end de aplicação com Notification Hubs

Aqui estão algumas das conveniências que esta integração proporciona a programadores:

- **SDKs de Cliente de Aplicações Móveis**: estes SDKs multiplataforma proporcionam APIs simples para registar e falar automaticamente com o hub de notificação ligado à aplicação móvel. Os programadores não precisam de analisar a fundo as credenciais dos Notification Hubs e trabalham com um serviço adicional.
  - *Enviar para o utilizador*: os SDKs etiquetam automaticamente o dispositivo especificado com o ID de Utilizador autenticado das Aplicações Móveis para permitir o cenário de envio para o utilizador.
  - *Enviar para o dispositivo*: os SDKs utilizam automaticamente o ID de Instalação das Aplicações Móveis como o GUID para se registarem nos Hubs de Notificação, poupando aos programadores o trabalho de manterem GUIDs para vários serviços.
- **Modelo de instalação**: as Aplicações Móveis funcionam com o modelo push mais recente dos Hubs de Notificação para representar todas as propriedades de push associadas a um dispositivo numa instalação JSON que se alinha com os Serviço de Notificações Push e é fácil de utilizar.
- **Flexibilidade**: os programadores podem sempre optar por trabalhar diretamente com os Hubs de Notificação, mesmo com a integração instalada.
- **Experiência integrada no [portal do Azure](https://portal.azure.com)**: o push como capacidade está representado visualmente nas Aplicações Móveis e os programadores podem trabalhar facilmente com o hub de notificação associado através daquelas.
