---
ms.openlocfilehash: ffd17f7a641e1481aa4c88f8b2eb12ec11fa7d8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60599404"
---
Fluentd é um recoletor de dados de código-fonte aberto para o registo unificado. O `Fluentd` definições gerir ligação do contentor para um [Fluentd](https://www.fluentd.org) server. O contêiner inclui um provedor de log Fluentd, que permite que o contentor escrever os registos e, opcionalmente, dados de métrica para um servidor de Fluentd.

A tabela seguinte descreve as definições de configuração suportadas ao abrigo do `Fluentd` secção.

| Nome | Tipo de dados | Descrição |
|------|-----------|-------------|
| `Host` | Cadeia | O endereço IP ou nome de anfitrião DNS do servidor Fluentd. |
| `Port` | Número inteiro | A porta do servidor Fluentd.<br/> O valor predefinido é 24224. |
| `HeartbeatMs` | Número inteiro | O intervalo de pulsação, em milissegundos. Se nenhum tráfego de evento foram enviado antes de expira este intervalo, é enviado um heartbeat para o servidor de Fluentd. O valor predefinido é 60000 milissegundos (1 minuto). |
| `SendBufferSize` | Número inteiro | O espaço de memória intermédia rede, em bytes, alocado para operações de envio. O valor predefinido é 32768 bytes (32 kilobytes). |
| `TlsConnectionEstablishmentTimeoutMs` | Número inteiro | O tempo limite, em milissegundos, para estabelecer uma ligação de SSL/TLS com o servidor de Fluentd. O valor predefinido é 10000 milissegundos (10 segundos).<br/> Se `UseTLS` é definido como FALSO, este valor é ignorado. |
| `UseTLS` | Booleano | Indica se o contentor deve utilizar SSL/TLS para comunicar com o servidor de Fluentd. O valor predefinido é false. |