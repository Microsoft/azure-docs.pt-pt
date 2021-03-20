---
title: Identidade configurada - Azure Event Grid IoT Edge | Microsoft Docs
description: Configurar a identidade do módulo de grelha de eventos
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 2418c8518bbf298a102d4f29b4a973f6121de2eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171691"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Identidade de configuração para o módulo de Grade de Eventos

Este artigo mostra como configurar a identidade para Grid on Edge. Por predefinição, o módulo 'Grade de Evento' apresenta o seu certificado de identidade configurado pelo daemon de segurança IoT. A Event Grid on Edge apresenta o seu certificado de identidade com as suas chamadas de saída quando entrega eventos. Um assinante pode então validar o módulo 'Grade de Eventos' que enviou o evento antes de aceitar.

Consulte o guia [de segurança e autenticação](security-authentication.md) para todas as configurações possíveis.

## <a name="always-present-identity-certificate"></a>Sempre apresentar certificado de identidade
Aqui está uma configuração de exemplo para apresentar sempre um certificado de identidade em chamadas de saída. 

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
