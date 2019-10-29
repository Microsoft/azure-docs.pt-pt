---
title: Configurar identidade-IoT Edge de grade de eventos do Azure | Microsoft Docs
description: Configurar a identidade do módulo de grade de eventos
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 336b6157128468169264d6ffa9564da4d9338aae
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992447"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Configurar identidade para o módulo de grade de eventos

Este artigo fornece exemplos das possíveis configurações de identidade para um módulo de grade de eventos. Por padrão, o módulo de grade de eventos apresentará seu certificado de identidade conforme configurado pelo daemon de segurança de IoT. Um certificado de identidade é apresentado pelo módulo de grade de eventos em suas chamadas de saída, ou seja, quando ele fornece eventos. Um assinante para um evento de grade de eventos pode optar por validar que é realmente o módulo de grade de eventos que enviou o evento antes de aceitar o evento.

Consulte o guia de [segurança e autenticação](security-authentication.md) para todas as configurações possíveis.

## <a name="always-present-identity-certificate"></a>Sempre apresentar certificado de identidade
Aqui está um exemplo de configuração para sempre apresentar um certificado de identidade em chamadas de saída. 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Não apresentar certificado de identidade
Aqui está um exemplo de configuração para não apresentar um certificado de identidade em chamadas de saída. 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=false"
  ]
}
 ```
