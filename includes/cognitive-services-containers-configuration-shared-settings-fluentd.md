---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b08516b35a864eae6d15c4c5c928f0550c64c239
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001202"
---
Fluentd é um colecionador de dados de código aberto para registos de registos unificados. As `Fluentd` definições gerem a ligação do contentor a um servidor [Fluentd.](https://www.fluentd.org) O recipiente inclui um fornecedor de registo fluente, que permite ao seu recipiente escrever registos e, opcionalmente, dados métricos para um servidor Fluentd.

A tabela seguinte descreve as definições de configuração suportadas na `Fluentd` secção.

| Name | Tipo de dados | Descrição |
|------|-----------|-------------|
| `Host` | String | O endereço IP ou o nome de anfitrião DNS do servidor Fluentd. |
| `Port` | Número inteiro | A porta do servidor Fluentd.<br/> O valor predefinido é 24224. |
| `HeartbeatMs` | Número inteiro | O intervalo do batimento cardíaco, em milissegundos. Se não tiver sido enviado tráfego de eventos antes de este intervalo expirar, é enviado um batimento cardíaco para o servidor Fluentd. O valor predefinido é de 60000 milissegundos (1 minuto). |
| `SendBufferSize` | Número inteiro | O espaço tampão da rede, em bytes, atribuído para operações de envio. O valor predefinido é de 32768 bytes (32 quilobytes). |
| `TlsConnectionEstablishmentTimeoutMs` | Número inteiro | O tempo limite, em milissegundos, para estabelecer uma ligação SSL/TLS com o servidor Fluentd. O valor predefinido é de 10000 milissegundos (10 segundos).<br/> Se `UseTLS` for definido como falso, este valor é ignorado. |
| `UseTLS` | Booleano | Indica se o recipiente deve utilizar SSL/TLS para comunicar com o servidor Fluentd. O valor predefinido é false. |