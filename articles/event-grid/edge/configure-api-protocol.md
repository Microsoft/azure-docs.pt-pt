---
title: Configure protocolos API - Azure Event Grid IoT Edge | Microsoft Docs
description: Saiba mais sobre as possíveis configurações de protocolo de um módulo de Grade de Eventos.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: a9cf6088201ffeaed76d99a9b211e5bcd1ea139a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91322585"
---
# <a name="configure-event-grid-api-protocols"></a>Configure protocolos API de grelha de eventos

Este guia dá exemplos das possíveis configurações de protocolo de um módulo de Grade de Eventos. O módulo Event Grid expõe a API para as suas operações de gestão e tempo de execução. A tabela seguinte captura os protocolos e portas.

| Protocolo | Porta | Description |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | Desligado por defeito. Útil apenas durante os testes. Não é adequado para cargas de trabalho de produção.
| HTTPS | 4438 | Predefinição

Consulte o guia [de segurança e autenticação](security-authentication.md) para todas as configurações possíveis.

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>Expor HTTPS a Módulos IoT na mesma rede de borda

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>Ativar HTTPS a outros módulos IoT e cargas de trabalho não-IoT

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> A secção **PortBindings** permite mapear portas internas para os portos do hospedeiro do contentor. Esta função permite chegar ao módulo De Grelha de Evento saindo do exterior da rede de contentores IoT Edge, se o dispositivo de borda IoT for acessível publicamente.

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>Expor HTTP e HTTPS a módulos IoT na mesma rede de borda

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>Ativar HTTP e HTTPS para outros módulos IoT e cargas de trabalho não-IoT

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ],
      "5888/tcp": [
        {
          "HostPort": "5888"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> Por predefinição, cada Módulo IoT faz parte do tempo de funcionação IoT Edge criado pela rede de pontes. Permite que diferentes módulos IoT na mesma rede se comuniquem entre si. **PortBindings** permite mapear uma porta interna de contentores na máquina hospedeira, permitindo assim que qualquer pessoa possa aceder à porta do módulo Desabinar do Módulo de Eventos a partir do exterior.

>[!IMPORTANT]
> Embora as portas possam ser acessíveis fora da rede IoT Edge, a autenticação do cliente impõe quem é realmente autorizado a fazer chamadas para o módulo.
