---
title: Configure a autenticação de subscritores do webhook - Azure Event Grid IoT Edge ! Microsoft Docs
description: Configurar a autenticação de subscritor do webhook
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 101dcae5870322878cec48098f2efae32cc68c14
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76841735"
---
# <a name="configure-webhook-subscriber-authentication"></a>Configurar a autenticação de subscritor do webhook

Este guia dá exemplos das possíveis configurações de subscritores do Webhook para um módulo de Grade de Eventos. Por padrão, apenas os pontos finais HTTPS são aceites para assinantes webhook. O módulo 'Grade de Eventos' rejeitará se o assinante apresentar um certificado auto-assinado.

## <a name="allow-only-https-subscriber"></a>Permitir apenas assinante HTTPS

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Permitir assinante HTTPS com certificado auto-assinado

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Descreva a propriedade `outbound__webhook__allowUnknownCA` `true` apenas em ambientes de teste, pois normalmente pode utilizar certificados auto-assinados. Para cargas de trabalho de produção, recomendamos que sejam **falsas.**

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>Permitir o assinante HTTPS mas ignorar validação de certificado

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=true",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Desaprova a propriedade `outbound__webhook__skipServerCertValidation` `true` apenas em ambientes de teste, uma vez que pode não estar a apresentar um certificado que precisa de ser autenticado. Para cargas de trabalho de produção recomendamos que sejam definidos como **falsos**

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>Permitir http e HTTPS com certificados auto-assinados

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=false",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Desaprote a propriedade `outbound__webhook__httpsOnly` `false` apenas em ambientes de teste, pois é possível que queira apresentar primeiro um assinante HTTP. Para cargas de trabalho de produção recomendamos que sejam definidos como **verdadeiros**
