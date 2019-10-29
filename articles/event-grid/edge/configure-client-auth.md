---
title: Configurar a autenticação de cliente de chamadas de entrada-IoT Edge de grade de eventos do Azure | Microsoft Docs
description: Configure os protocolos de API expostos pela grade de eventos em IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: b5456130e89bf77e2c2ba41880323e38f6b27f4c
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992512"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Configurar a autenticação de cliente de chamadas de entrada

Este guia fornece exemplos das possíveis configurações de autenticação de cliente para o módulo de grade de eventos. O módulo de grade de eventos dá suporte a dois tipos de autenticação de cliente:-

* Baseada em chave de SAS (assinatura de acesso compartilhado)
* com base em certificado

Consulte o guia de [segurança e autenticação](security-authentication.md) para todas as configurações possíveis.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Habilitar a autenticação de cliente baseada em certificado, não há certificados autoassinados

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Habilitar a autenticação de cliente baseada em certificado, permitir certificados autoassinados

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Defina a propriedade de **entrada: clientAuth: clientCert: allowUnknownCA** como **true** em ambientes de teste, pois você normalmente pode usar certificados autoassinados. Para cargas de trabalho de produção, recomendamos que você defina essa propriedade como **false** e certificados de uma autoridade de certificação (CA).

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Habilitar a autenticação de cliente baseada em certificado e na chave SAS

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=true",
    "inbound:clientAuth:sasKeys:key1=<some-secret1-here>",
    "inbound:clientAuth:sasKeys:key2=<some-secret2-here>",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>A autenticação de cliente baseada em chave SAS permite que um módulo sem IoT Edge faça gerenciamento e operações de tempo de execução supondo que as portas de API estejam acessíveis fora da rede IoT Edge.
