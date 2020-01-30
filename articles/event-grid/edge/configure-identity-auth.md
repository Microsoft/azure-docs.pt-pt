---
title: Identidade configurada - Azure Event Grid IoT Edge  Microsoft Docs
description: Configurar a identidade do módulo de grelha de eventos
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 0aedeea2a6ad08e1627c2d1a6ebde6c91a4d02d9
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841770"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Configure a identidade para o módulo De Rede de Eventos

Este artigo mostra como configurar a identidade para Grid on Edge. Por padrão, o módulo Event Grid apresenta o seu certificado de identidade configurado pelo daemon de segurança IoT. A Event Grid on Edge apresenta o seu certificado de identidade com as suas chamadas de saída quando entrega eventos. Um assinante pode então validar o módulo De Rede de Eventos que enviou o evento antes de aceitar.

Consulte o guia [de segurança e autenticação](security-authentication.md) para todas as configurações possíveis.

## <a name="always-present-identity-certificate"></a>Sempre presente certificado de identidade
Aqui está uma configuração de exemplo para sempre apresentar um certificado de identidade em chamadas de saída. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Não apresente certidão de identidade
Aqui está uma configuração de exemplo para não apresentar um certificado de identidade em chamadas de saída. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
