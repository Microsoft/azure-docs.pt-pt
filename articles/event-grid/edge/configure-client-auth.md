---
title: Configure a autenticação do cliente das chamadas recebidas - Azure Event Grid IoT Edge [ Microsoft Docs
description: Configure os protocolos DaPI expostos pela Grelha de Eventos na Borda IoT.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3363db4557dd19e8d72747ccd62bb535abb7b1e2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76841796"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Configurar a autenticação do cliente das chamadas recebidas

Este guia dá exemplos das possíveis configurações de autenticação do cliente para o módulo De Rede de Eventos. O módulo Event Grid suporta dois tipos de autenticação do cliente:

* Assinatura de acesso partilhado (SAS) baseada na chave
* Com base em certificados

Consulte o guia [de segurança e autenticação](security-authentication.md) para todas as configurações possíveis.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Ativar a autenticação do cliente com base em certificados, sem certificados auto-assinados

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Ativar a autenticação do cliente com base em certificados, permitir certificados auto-assinados

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Detete a **propriedade inbound__clientAuth__clientCert__allowUnknownCA** **apenas em** ambientes de teste, pois você normalmente pode usar certificados auto-assinados. Para cargas de trabalho de produção, recomendamos que você detetete esta propriedade em **falsos** certificados de uma autoridade de certificados (CA).

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Ativar a autenticação do cliente baseado em certificados e sas-key

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=true",
    "inbound__clientAuth__sasKeys__key1=<some-secret1-here>",
    "inbound__clientAuth__sasKeys__key2=<some-secret2-here>",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>A autenticação do cliente baseada na chave SAS permite que um módulo de borda não IoT faça operações de gestão e tempo de funcionação, assumindo, claro, que as portas API estão acessíveis fora da rede IoT Edge.
