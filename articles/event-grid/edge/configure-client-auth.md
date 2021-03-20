---
title: Configurar a autenticação do cliente de chamadas recebidas - Azure Event Grid IoT Edge | Microsoft Docs
description: Saiba mais sobre as possíveis configurações de autenticação do cliente para o módulo 'Grade de Eventos'.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 9525da4204e270d033f0c2354318bd71874eaf54
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91290777"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Configurar a autenticação do cliente de chamadas recebidas

Este guia dá exemplos das possíveis configurações de autenticação do cliente para o módulo 'Grade de Eventos'. O módulo 'Grade de Eventos' suporta dois tipos de autenticação do cliente:

* Assinatura de acesso partilhado (SAS) baseada em chaves
* Com base em certificados

Consulte o guia [de segurança e autenticação](security-authentication.md) para todas as configurações possíveis.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Permitir a autenticação do cliente baseada em certificados, sem certificados auto-assinados

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

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Ativar a autenticação do cliente baseada em certificados, permitir certificados auto-assinados

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
>Descreva a propriedade **inbound__clientAuth__clientCert__allowUnknownCA** **apenas** em ambientes de teste, pois normalmente pode utilizar certificados auto-assinados. Para cargas de trabalho de produção, recomendamos que você desemende este imóvel em **falso** e certificados de uma autoridade de certificados (CA).

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Ativar a autenticação do cliente baseada em certificados e sas-key

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
>A autenticação do cliente baseada em chaves SAS permite que um módulo de borda não IoT faça operações de gestão e tempo de execução assumindo, naturalmente, que as portas API são acessíveis fora da rede IoT Edge.
