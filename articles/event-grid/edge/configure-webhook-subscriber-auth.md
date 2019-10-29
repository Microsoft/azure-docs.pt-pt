---
title: Configurar a autenticação de assinante de webhook-grade de eventos do Azure IoT Edge | Microsoft Docs
description: Configurar a autenticação do assinante do webhook
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 97ed1e2ad84d895e9da0d96cd070e14acb46385d
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992486"
---
# <a name="configure-webhook-subscriber-authentication"></a>Configurar a autenticação do assinante do webhook

Este guia fornece exemplos de configurações possíveis de assinante de webhook para um módulo de grade de eventos. Por padrão, somente pontos de extremidade HTTPS são aceitos para assinantes do webhook. O módulo de grade de eventos será rejeitado se o assinante apresentar um certificado autoassinado.

## <a name="allow-only-https-subscriber"></a>Permitir somente assinante HTTPS

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Permitir assinante HTTPS com certificado autoassinado

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Defina a propriedade `outbound:webhook:allowUnknownCA` como `true` somente em ambientes de teste como você normalmente pode usar certificados autoassinados. Para cargas de trabalho de produção, recomendamos que elas sejam definidas como **false**.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>Permitir assinante HTTPS, mas ignorar validação de certificado

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=true",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Defina a propriedade `outbound:webhook:skipServerCertValidation` como `true` somente em ambientes de teste, pois talvez você não esteja apresentando um certificado que precise ser autenticado. Para cargas de trabalho de produção, recomendamos que elas sejam definidas como **false**

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>Permitir HTTP e HTTPS com certificados autoassinados

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=false",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Defina a propriedade `outbound:webhook:httpsOnly` como `false` apenas em ambientes de teste, como você pode querer abrir um assinante HTTP primeiro. Para cargas de trabalho de produção, recomendamos que elas sejam definidas como **true**
