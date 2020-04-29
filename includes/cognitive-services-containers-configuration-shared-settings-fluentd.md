---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b08516b35a864eae6d15c4c5c928f0550c64c239
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67712567"
---
Fluentd é um colecionador de dados de código aberto para a exploração madeireira unificada. As `Fluentd` definições gerem a ligação do recipiente a um servidor [Fluente.](https://www.fluentd.org) O recipiente inclui um fornecedor de registos fluente, que permite ao seu recipiente escrever registos e, opcionalmente, dados métricos para um servidor Fluente.

A tabela seguinte descreve as definições de configuração suportadas por baixo da `Fluentd` secção.

| Nome | Tipo de dados | Descrição |
|------|-----------|-------------|
| `Host` | String | O endereço IP ou o nome de anfitrião dNS do servidor Fluentd. |
| `Port` | Número inteiro | A porta do servidor Fluentd.<br/> O valor padrão é 24224. |
| `HeartbeatMs` | Número inteiro | O intervalo do batimento cardíaco, em milissegundos. Se nenhum tráfego de eventos tiver sido enviado antes deste intervalo expirar, um batimento cardíaco é enviado para o servidor Fluentd. O valor predefinido é de 60000 milissegundos (1 minuto). |
| `SendBufferSize` | Número inteiro | O espaço tampão da rede, em bytes, atribuído para operações de envio. O valor predefinido é de 32768 bytes (32 quilobytes). |
| `TlsConnectionEstablishmentTimeoutMs` | Número inteiro | O tempo de tempo, em milissegundos, para estabelecer uma ligação SSL/TLS com o servidor Fluentd. O valor predefinido é de 10000 milissegundos (10 segundos).<br/> Se `UseTLS` for definido como falso, este valor é ignorado. |
| `UseTLS` | Booleano | Indica se o recipiente deve utilizar SSL/TLS para comunicar com o servidor Fluentd. O valor predefinido é false. |