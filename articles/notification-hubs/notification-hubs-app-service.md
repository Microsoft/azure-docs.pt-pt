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
ms.openlocfilehash: d6747193b8c82119e45a24e3e4bffc065db14e51
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212169"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integração com as Aplicações Móveis do Serviço de Aplicações

> [!NOTE]
> A Microsoft tem o compromisso de dar suporte total a aplicativos móveis de serviço Azure App, incluindo suporte para a versão mais recente do sistema operacional, correções de bugs, melhorias na documentação e revisões de RP da Comunidade. Observe que a equipe de produto não está investindo atualmente em nenhum novo recurso de trabalho para os aplicativos móveis do Azure. Agradecemos as contribuições da Comunidade a todas as áreas de aplicativos móveis do Azure.

Para facilitar uma experiência totalmente integrada e unificadora em todos os serviços do Azure, as [Aplicações Móveis do Serviço de Aplicações](../app-service-mobile/app-service-mobile-value-prop.md) têm suporte incorporado para notificações push com os Hubs de Notificação. As [Aplicações Móveis do Serviço de Aplicações](../app-service-mobile/app-service-mobile-value-prop.md) oferecem uma plataforma de desenvolvimento de aplicações móveis altamente dimensionável e globalmente disponível para Programadores Empresariais e Integradores de Sistemas, que fornece um conjunto completo de capacidades para programadores móveis.

Os programadores de Mobile Apps podem utilizar Notification Hubs com o fluxo de trabalho seguinte:

1. Obter o identificador PNS do dispositivo
2. Registar o dispositivo nos Hubs de Notificação através da conveniente API de registo do SDK de Cliente das Aplicações Móveis

    > [!NOTE]
    > Tenha em atenção que, por motivos de segurança, as Mobile Apps eliminam todas as etiquetas ao registarem-se. Trabalhar com Notification Hubs diretamente no seu back-end para associar etiquetas a dispositivos.

3. Enviar notificações a partir do seu back-end de aplicação com Notification Hubs

Aqui estão algumas das conveniências que esta integração proporciona a programadores:

- **SDKs de cliente de aplicativos móveis**: Estes SDKs multiplataforma fornecem APIs simples para registar e falar com o Notification Hub ligado automaticamente à aplicação móvel. Os programadores não precisam de analisar a fundo as credenciais dos Notification Hubs e trabalham com um serviço adicional.
  - *Enviar por push para o usuário*: Os SDKs etiquetam automaticamente o dispositivo especificado com ID de utilizador autenticado de Mobile Apps para ativar o push para o cenário do utilizador.
  - *Enviar por push para o dispositivo*: Os SDKs utilizam automaticamente o ID de Instalação das Mobile Apps como GUID para se registarem nos Notification Hubs, poupando aos programadores o trabalho de manterem GUIDs para vários serviços.
- **Modelo de instalação**: As Mobile Apps funcionam com o modelo push mais recente dos Notification Hubs para representar todas as propriedades push associadas a um dispositivo numa instalação JSON que se alinha com os Serviço de Notificações Push e é fácil de utilizar.
- **Flexibilidade**: Os programadores podem sempre optar por trabalhar diretamente com Notification Hubs, mesmo com a integração instalada.
- **Experiência integrada no [portal do Azure](https://portal.azure.com)** : O Push como uma capacidade está representado visualmente em Mobile Apps e os programadores podem trabalhar facilmente com o Notification Hub associado através das Mobile Apps.
