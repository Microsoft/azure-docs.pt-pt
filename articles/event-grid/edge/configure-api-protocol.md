---
title: Configure protocolos API - Azure Event Grid IoT Edge  Microsoft Docs
description: Configure os protocolos DaPI expostos pela Grelha de Eventos na Borda IoT.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: ''
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 908bc941ee7379de067621e10adf5fd6ee6df559
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841815"
---
# <a name="configure-event-grid-api-protocols"></a>Configure protocolos da Grelha de Eventos API

Este guia dá exemplos das possíveis configurações protocolares de um módulo De Rede de Eventos. O módulo Event Grid expõe a API para as suas operações de gestão e tempo de funcionação. A tabela seguinte captura os protocolos e portas.

| Protocolo | Porta | Descrição |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | Desligado por defeito. Útil apenas durante os testes. Não é adequado para cargas de trabalho de produção.
| HTTPS | 4438 | Predefinição

Consulte o guia [de segurança e autenticação](security-authentication.md) para todas as configurações possíveis.

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>Expor HTTPS a Módulos IoT na mesma rede de bordas

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>Ativar HTTPS a outros módulos IoT e cargas de trabalho não IoT

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
> A secção **PortBindings** permite-lhe mapear portas internas para portas do hospedeiro do contentor. Esta funcionalidade permite chegar ao módulo Da Rede de Eventos de fora da rede de contentores IoT Edge, se o dispositivo de borda IoT for acessível publicamente.

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>Expor HTTP e HTTPS a módulos IoT na mesma rede de bordas

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>Ativar HTTP e HTTPS para outros módulos IoT e cargas de trabalho não IoT

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
> Por padrão, cada Módulo IoT faz parte do tempo de funcionamento do IoT Edge criado pela rede de pontes. Permite que diferentes módulos IoT na mesma rede se comuniquem entre si. **As PortBindings** permitem-lhe mapear uma porta interna de contentores para a máquina hospedeira, permitindo assim que qualquer pessoa possa aceder à porta do módulo Event Grid a partir do exterior.

>[!IMPORTANT]
> Embora as portas possam ser acessíveis fora da rede IoT Edge, a autenticação do cliente aplica quem está realmente autorizado a fazer chamadas para o módulo.
