---
title: Integração com as Aplicações Móveis do Serviço de Aplicações
description: Saiba como os hubs de notificação do Azure funcionam com os aplicativos móveis do serviço Azure App.
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
ms.openlocfilehash: 8c3bc90b282092ede0e924d32b50b67e5c4e22b8
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244506"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integração com as Aplicações Móveis do Serviço de Aplicações

Para facilitar uma experiência totalmente integrada e unificadora em todos os serviços do Azure, as [Aplicações Móveis do Serviço de Aplicações](../app-service-mobile/app-service-mobile-value-prop.md) têm suporte incorporado para notificações push com os Hubs de Notificação. As [Aplicações Móveis do Serviço de Aplicações](../app-service-mobile/app-service-mobile-value-prop.md) oferecem uma plataforma de desenvolvimento de aplicações móveis altamente dimensionável e globalmente disponível para Programadores Empresariais e Integradores de Sistemas, que fornece um conjunto completo de capacidades para programadores móveis.

A Visual Studio App Center oferece uma solução móvel mais moderna para desenvolvedores atualmente. Ele dá suporte a serviços de ponta a ponta e integrados central ao desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **Compilar**, **testar** e **distribuir** serviços para configurar o pipeline de integração e entrega contínua. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso de seus aplicativos usando os serviços de **análise** e **diagnóstico** e se envolver com os usuários usando o serviço de **envio por push** . Os desenvolvedores também podem aproveitar a **autenticação** para autenticar seus usuários e o serviço de **dados** para manter e sincronizar dados de aplicativos na nuvem.

> [!NOTE]
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com o [app Center](https://appcenter.ms/signup?utm_source=NotificationHubs&utm_medium=Azure&utm_campaign=docs) hoje mesmo.

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
- **Experiência integrada no [portal do Azure](https://portal.azure.com)** : o push como capacidade está representado visualmente nas Aplicações Móveis e os programadores podem trabalhar facilmente com o hub de notificação associado através daquelas.
