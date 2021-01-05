---
title: Trabalhar com o Defender para APIs IoT
description: Utilize uma API rest externa para aceder aos dados descobertos por sensores e consolas de gestão e realizar ações com esses dados.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.topic: reference
ms.service: azure
ms.openlocfilehash: d49aa50b1b8843dfb5c3d32983ff0bb129543bb0
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97843675"
---
# <a name="defender-for-iot-sensor-and-management-console-apis"></a>Defender para APIs de sensor e consola de ioT

Utilize uma API rest externa para aceder aos dados descobertos por sensores e consolas de gestão e realizar ações com esses dados.

As ligações estão seguras sobre a SSL.

## <a name="getting-started"></a>Introdução

Em geral, quando estiver a utilizar uma API externa no Azure Defender para sensor IoT ou consola de gestão no local, precisa de gerar um token de acesso. Não são necessários tokens para APIs de autenticação que utilize no sensor e na consola de gestão no local.

Para gerar um símbolo:

1. Na janela **Definições do Sistema,** selecione **Tokens de acesso**.
  
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-tokens.png" alt-text="Screenshot das janelas de Definições do Sistema que destacam o botão Tokens de Acesso.":::

2. **Selecione Gerer novo símbolo**.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/new-token.png" alt-text="Selecione o botão para gerar um novo token.":::

3. Descreva o propósito do novo token e selecione **Next**.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-name.png" alt-text="Gere um novo símbolo e insira o nome da integração que lhe está associada.":::

4. O símbolo de acesso aparece. Copie-o, porque não voltará a ser exibido.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-code.png" alt-text="Copie o seu símbolo de acesso para a sua integração.":::

5. Selecione **Concluir**. Os tokens que cria aparecem na caixa de diálogo **Access Tokens.**
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-token-window.png" alt-text="Screenshot da caixa de diálogo de Tokens de Ativos com fichas cheias":::

   **Usado** indica a última vez que uma chamada externa com este símbolo foi recebida.

   Se **o N/A** for apresentado no campo **Usado** para este token, a ligação entre o sensor e o servidor ligado não está a funcionar.

6. Adicione um cabeçalho HTTP intitulado **Autorização** ao seu pedido e desloque o seu valor para o token que gerou.

## <a name="sensor-api-specifications"></a>Especificações da API do sensor

Esta secção descreve as seguintes APIs sensores:

- /api/v1/dispositivos

- /api/v1/dispositivos/conexões

- /api/v1/dispositivos/cves

- /api/v1/alertas

- /api/v1/eventos

- /api/v1/reports/vulnerabilidades/dispositivos

- /api/v1/reports/vulnerabilidades/segurança

- /api/v1/reports/vulnerabilidades/operacional

- /api/external/autenticação/validação

- /external/autenticação/set_password

- /external/autenticação/set_password_by_admin

### <a name="retrieve-device-information"></a>Recuperar informações sobre dispositivos

Utilize esta API para solicitar uma lista de todos os dispositivos detetados por um sensor Defender para IoT.

#### <a name="apiv1devices"></a>/api/v1/dispositivos

#### <a name="method"></a>Método

**GET**

Solicita uma lista de todos os dispositivos detetados pelo Defender para o sensor IoT.

#### <a name="query-parameters"></a>Parâmetros de consulta

- **autorizado :** Filtrar apenas dispositivos autorizados e não autorizados.

  **Exemplos:**

  `/api/v1/devices?authorized=true`

  `/api/v1/devices?authorized=false`

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo de resposta

Matriz de objetos JSON que representam dispositivos.

#### <a name="device-fields"></a>Campos de dispositivos

| Nome | Tipo | Pode ser nulo | Lista de valores |
|--|--|--|--|
| **id** | Numérico | No | - |
| **ipAddresses** | Matriz JSON | Yes | Endereços IP (pode ser mais do que um endereço no caso de endereços de internet ou um dispositivo com NICs duplos) |
| **nome** | String | No | - |
| **tipo** | String | No | Desconhecido, Estação de Engenharia, PLC, HMI, Historiador, Controlador de Domínio, Servidor DB, Ponto de Acesso Sem Fios, Router, Switch, Servidor, Estação de Trabalho, Câmara IP, Impressora, Firewall, Estação terminal, Gateway VPN, Internet ou Multicast e Transmissão |
| **macAddresses** | Matriz JSON | Yes | Endereços MAC (pode ser mais do que um endereço no caso de um dispositivo com NICs duplos) |
| **sistema operativo** | String | Yes | - |
| **engenhariaStation** | Booleano | No | Verdadeiro ou falso |
| **scanner** | Booleano | No | Verdadeiro ou falso |
| **autorizado** | Booleano | No | Verdadeiro ou falso |
| **fornecedor** | String | Yes | - |
| **protocolos** | Matriz JSON | Yes | Objeto de protocolo |
| **firmware** | Matriz JSON | Yes | Objeto firmware |

#### <a name="protocol-fields"></a>Campos de protocolo

| Nome | Tipo | Pode ser nulo | Lista de valores |
|--|--|--|--|
| **Nome** | String | No |  |
| **Endereços** | Matriz JSON | Yes | Valores mestres ou numéricos |

#### <a name="firmware-fields"></a>Campos de firmware

| Nome | Tipo | Pode ser nulo | Lista de valores |
|--|--|--|--|
| **série** | String | No | N/A, ou o valor real |
| **modelo** | String | No | N/A, ou o valor real |
| **firmwareVersion** | Double (Duplo) | No | N/A, ou o valor real |
| **AdicionalData** | String | No | N/A, ou o valor real |
| **móduloAddress** | String | No | N/A, ou o valor real |
| **rack** | String | No | N/A, ou o valor real |
| **slot** | String | No | N/A, ou o valor real |
| **endereço** | String | No | N/A, ou o valor real |

#### <a name="response-example"></a>Exemplo de resposta

```rest
[

    {
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        }
    
    ],
    
    "id": 79,
    
    "macAddresses": null,
    
    "authorized": true,
    
    "ipAddresses": [
    
        "10.4.14.102"
    
    ],
    
    "engineeringStation": false,
    
    "type": "PLC",
    
    "operatingSystem": null,
    
    "protocols": [
    
        {
        
            "addresses": [],
            
            "id": 62,
            
            "name": "Omron FINS"
        
        }
    
    ],
    
    "scanner": false
    
}

]
```

### <a name="retrieve-device-connection-information"></a>Recuperar informações de ligação do dispositivo

Utilize esta API para solicitar uma lista de todas as ligações por dispositivo.

#### <a name="apiv1devicesconnections"></a>/api/v1/dispositivos/conexões

#### <a name="method"></a>Método

**GET**

#### <a name="query-parameters"></a>Parâmetros de consulta

Se não definir os parâmetros de consulta, todas as ligações do dispositivo são devolvidas.

**Exemplo:**

`/api/v1/devices/connections`

- **deviceId**: Filtrar por um ID específico do dispositivo, para visualizar as suas ligações.

  **Exemplo:**

  `/api/v1/devices/<deviceId>/connections`

- **lastActiveInMinutes**: Período de tempo a partir de agora para trás, por minuto, durante o qual as ligações estavam ativas.

  **Exemplo:**

  `/api/v1/devices/2/connections?lastActiveInMinutes=20`

- **descoberto Antes :** Filtrar apenas as ligações detetadas antes de um tempo específico (em milissegundos, UTC).

  **Exemplo:**

  `/api/v1/devices/2/connections?discoveredBefore=<epoch>`

- **descobertos Depois :** Filtrar apenas as ligações detetadas após um determinado período (em milissegundos, UTC).

  **Exemplo:**

  `/api/v1/devices/2/connections?discoveredAfter=<epoch>`

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo de resposta

Matriz de objetos JSON que representam ligações do dispositivo.

#### <a name="fields"></a>Campos

| Nome | Tipo | Pode ser nulo | Lista de valores |
|--|--|--|--|
| **firstDeviceId** | Numérico | No | - |
| **secondDeviceId** | Numérico | No | - |
| **lastSeen** | Numérico | No | Época (UTC) |
| **descoberto** | Numérico | No | Época (UTC) |
| **portos** | Matriz de números | No | - |
| **protocolos** | Matriz JSON | No | Campo de protocolo |

#### <a name="protocol-field"></a>Campo de protocolo

| Nome | Tipo | Pode ser nulo | Lista de valores |
|--|--|--|--|
| **nome** | String | No | - |
| **comandos** | Matriz de cadeias | No | - |

#### <a name="response-example"></a>Exemplo de resposta

```rest
[

    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 22,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
    
        "protocols": [
        
        {
        
            name: "modbus",
            
            commands: [
            
                "Read Coils"
        
            ]
        
        },
    
        {
        
            name: "ams",
            
            commands: [
            
                "AMS Write"
        
            ]
        
        },
    
        {
        
            name: "http",
            
            commands: [
        
            ]
        
        }
    
    ]
    
    },
    
    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 23,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
        
        "protocols": [
        
            {
            
                name: "s7comm",
                
                commands: [
                
                    "Download block",
                    
                    "Upload"
            
                ]
            
            }
        
        ]
    
    }

]
```

### <a name="retrieve-information-on-cves"></a>Recuperar informações sobre CVEs

Utilize esta API para solicitar uma lista de todos os CVEs conhecidos descobertos em dispositivos na rede.

#### <a name="apiv1devicescves"></a>/api/v1/dispositivos/cves

#### <a name="method"></a>Método

**GET**

#### <a name="query-parameters"></a>Parâmetros de consulta

Por padrão, esta API fornece a lista de todos os IPs do dispositivo com CVEs, até 100 CVEs com pontuação superior para cada endereço IP.

**Exemplo:**

`/api/v1/devices/cves`

- **deviceId**: Filtrar por um endereço IP específico do dispositivo, para obter até 100 CVEs com pontuação superior identificados nesse dispositivo específico.

  **Exemplo:**

  `/api/v1/devices/<ipAddress>/cves`

- **top**: Quantos CVEs com melhor pontuação para recuperar para cada endereço IP do dispositivo.

  **Exemplo:**

  `/api/v1/devices/cves?top=50`

  `/api/v1/devices/<ipAddress>/cves?top=50`

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo de resposta

Matriz de objetos JSON que representam CVEs identificados em endereços IP.

#### <a name="fields"></a>Campos

| Nome | Tipo | Pode ser nulo | Lista de valores |
|--|--|--|--|
| **cveId** | String | No | - |
| **ipAddress** | String | No | Endereço IP |
| **pontuação** | String | No | 0.0 - 10.0 |
| **atacarVector** | String | No | Rede, Rede Adjacente, Local ou Física |
| **descrição** | String | No | - |

#### <a name="response-example"></a>Exemplo de resposta

```rest
[

    {
    
        "cveId": "CVE-2007-0099",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Race condition in the msxml3 module in Microsoft XML Core
        
        Services 3.0, as used in Internet Explorer 6 and other
        
        applications, allows remote attackers to execute arbitrary
        
        code or cause a denial of service (application crash) via many
        
        nested tags in an XML document in an IFRAME, when synchronous
        
        document rendering is frequently disrupted with asynchronous
        
        events, as demonstrated using a JavaScript timer, which can
        
        trigger NULL pointer dereferences or memory corruption, aka
        
        \"MSXML Memory Corruption Vulnerability.\""
    
    },
    
    {
    
        "cveId": "CVE-2009-1547",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Unspecified vulnerability in Microsoft Internet Explorer 5.01
        
        SP4, 6, 6 SP1, and 7 allows remote attackers to execute
        
        arbitrary code via a crafted data stream header that triggers
        
        memory corruption, aka \"Data Stream Header Corruption
        
        Vulnerability.\""
    
    }

]
```

### <a name="retrieve-alert-information"></a>Recuperar informações de alerta

Utilize esta API para solicitar uma lista de todos os alertas detetados pelo Sensor Defender para IoT.

#### <a name="apiv1alerts"></a>/api/v1/alertas

#### <a name="method"></a>Método

**GET**

#### <a name="query-parameters"></a>Parâmetros de consulta

- **estado**: Para filtrar apenas alertas manuseados ou não manipulados.

  **Exemplo:**

  `/api/v1/alerts?state=handled`

- **a partir do Tempo**: Para filtrar alertas criados a partir de um tempo específico (em milissegundos, UTC).

  **Exemplo:**

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime**: Para filtrar alertas criados apenas antes de um tempo específico (em milissegundos, UTC).

  **Exemplo:**

  `/api/v1/alerts?toTime=<epoch>`

- **tipo**: Filtrar alertas por um tipo específico. Tipos existentes para filtrar por: novos dispositivos inesperados, desconexões.

  **Exemplo:**

  `/api/v1/alerts?type=disconnections`

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo de resposta

Matriz de objetos JSON que representam alertas.

#### <a name="alert-fields"></a>Campos de alerta

| Nome | Tipo | Pode ser nulo | Lista de valores |
|--|--|--|--|
| **ID** | Numérico | No | - |
| **Hora** | Numérico | No | Época (UTC) |
| **título** | String | No | - |
| **Mensagem** | String | No | - |
| **severidade** | String | No | Aviso, Menor, Major ou Crítico |
| **motor** | String | No | Violação de protocolo, violação de políticas, malware, anomalia ou operacional |
| **fonteDevice** | Numérico | Yes | ID do Dispositivo |
| **destinoDevice** | Numérico | Yes | ID do Dispositivo |
| **informação adicional** | Objeto de informação adicional | Yes | - |

#### <a name="additional-information-fields"></a>Campos de informação adicionais

| Nome | Tipo | Pode ser nulo | Lista de valores |
|--|--|--|--|
| **descrição** | String | No | - |
| **informação** | Matriz JSON | Não | String |

#### <a name="response-example"></a>Exemplo de resposta

```rest
[

    {
    
        "engine": "Policy Violation",
        
        "severity": "Major",
        
        "title": "Internet Access Detected",
        
        "additionalinformation": {
        
            "information": [
            
                "170.60.50.201 over port BACnet (47808)"
            
            ],
            
            "description": "External Addresses"
        
        },
    
        "sourceDevice": null,
        
        "destinationDevice": null,
        
        "time": 1509881077000,
        
        "message": "Device 192.168.0.13 tried to access an external IP address which is an address in the Internet and is not allowed by policy. It is recommended to notify the security officer of the incident.",
        
        "id": 1
    
    },
    
    {
    
        "engine": "Protocol Violation",
        
        "severity": "Major",
        
        "title": "Illegal MODBUS Operation (Exception Raised by Master)",
        
        "sourceDevice": 3,
        
        "destinationDevice": 4,
        
        "time": 1505651605000,
        
        "message": "A MODBUS master 192.168.110.131 attempted to initiate an illegal operation.\nThe operation is considered to be illegal since it incorporated function code \#129 which should not be used by a master.\nIt is recommended to notify the security officer of the incident.",
        
        "id": 2,
        
        "additionalInformation": null,
    
    }

]

```

### <a name="retrieve-timeline-events"></a>Recuperar eventos de linha do tempo

Utilize esta API para solicitar uma lista de eventos reportados à linha temporal do evento.

#### <a name="apiv1events"></a>/api/v1/eventos

#### <a name="method"></a>Método

**GET**

#### <a name="query-parameters"></a>Parâmetros de consulta

- **minutesTimeFrame**: Prazo a partir de agora para trás, por minuto, em que os eventos foram relatados.

  **Exemplo:**

  `/api/v1/events?minutesTimeFrame=20`

- **tipo**: Filtrar a lista de eventos por um tipo específico.

  **Exemplos:**

  `/api/v1/events?type=DEVICE_CONNECTION_CREATED`

  `/api/v1/events?type=REMOTE_ACCESS&minutesTimeFrame`

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo de resposta

Matriz de objetos JSON que representam alertas.

#### <a name="event-fields"></a>Campos de eventos

| Nome | Tipo | Pode ser nulo | Lista de valores |
|--|--|--|--|--|
| **timetamp** | Numérico | No | Época (UTC) |
| **título** | String | No | - |
| **severidade** | String | No | INFO, AVISO OU ALERTA |
| **proprietário** | String | Yes | Se o evento foi criado manualmente, este campo incluirá o nome de utilizador que criou o evento |
| **conteúdo** | String | No | - |

#### <a name="response-example"></a>Exemplo de resposta

```rest
[

    {
    
        "severity": "INFO",
        
        "title": "Back to Normal",
        
        "timestamp": 1504097077000,
        
        "content": "Device 10.2.1.15 was found responsive, after being suspected as disconnected",
        
        "owner": null,
        
        "type": "BACK_TO_NORMAL"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504096909000,
        
        "content": "Device 10.2.1.15 is suspected to be disconnected (unresponsive).",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504094446000,
        
        "content": "A DNP3 Master 10.2.1.14 attempted to initiate a request which is not allowed by policy.\nThe policy indicates the allowed function codes, address ranges, point indexes and time intervals.\nIt is recommended to notify the security officer of the incident.",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "NOTICE",
        
        "title": "PLC Program Update",
        
        "timestamp": 1504094344000,
        
        "content": "Program update detected, sent from 10.2.1.25 to 10.2.1.14",
        
        "owner": null,
        
        "type": "PROGRAM_DEVICE"
    
    }

]

```

### <a name="retrieve-vulnerability-information"></a>Recuperar informações sobre vulnerabilidades

Utilize esta API para solicitar resultados de avaliação de vulnerabilidade para cada dispositivo.

#### <a name="apiv1reportsvulnerabilitiesdevices"></a>/api/v1/reports/vulnerabilidades/dispositivos

#### <a name="method"></a>Método

**GET**

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo de resposta

Matriz de objetos JSON que representam dispositivos avaliados.

O objeto do dispositivo contém:

- Dados gerais

- Pontuação de avaliação

- Vulnerabilidades

#### <a name="device-fields"></a>Campos de dispositivos

| Nome | Tipo | Pode ser nulo | Lista de valores |
|--|--|--|--|
| **nome** | String | No | - |
| **ipAddresses** | Matriz JSON | No | - |
| **securityScore** | Numérico | No | - |
| **fornecedor** | String | Yes |  |
| **firmwareVersion** | String | Yes | - |
| **modelo** | String | Yes | - |
| **isWirelessAccessPoint** | Booleano | No | Verdadeiro ou falso |
| **sistema operativo** | Objeto do sistema operativo | Yes | - |
| **vulnerabilidades** | Objeto de vulnerabilidades | Yes | - |

#### <a name="operating-system-fields"></a>Campos do sistema operativo

| Nome | Tipo | Pode ser nulo | Lista de valores |
|--|--|--|--|
| **Nome** | String | Yes | - |
| **Tipo** | String | Yes | - |
| **Versão** | String | Yes | - |
| **últimaversão** | String | Yes | - |

#### <a name="vulnerabilities-fields"></a>Campos de vulnerabilidades
 
| Nome | Tipo | Pode ser nulo | Lista de valores |
|--|--|--|--|
| **antiVírus** | Matriz JSON | Yes | Nomes antivírus |
| **palavras simplesTextPass** | Matriz JSON | Yes | Objetos de senha |
| **remotoAccess** | Matriz JSON | Yes | Objetos de acesso remoto |
| **isBackupServer** | Booleano | No | Verdadeiro ou falso |
| **openPorts** | Matriz JSON | Yes | Objetos de porta abertos |
| **isEngineeringStation** | Booleano | No | Verdadeiro ou falso |
| **isKnownScanner** | Booleano | No | Verdadeiro ou falso |
| **cves** | Matriz JSON | Yes | Objetos CVE |
| **isunauthorized** | Booleano | No | Verdadeiro ou falso |
| **malwareDesdedetectado** | Booleano | No | Verdadeiro ou falso |
| **fracoAufercção** | Matriz JSON | Yes | Aplicações detetadas que estão a usar a autenticação fraca |

#### <a name="password-fields"></a>Campos de palavra-passe

| Nome | Tipo | Pode ser nulo | Lista de valores |
|--|--|--|--|
| **palavra-passe** | String | No | - |
| **protocolo** | String | No | - |
| **força** | String | No | Muito fraco, fraco, médio ou forte |

#### <a name="remote-access-fields"></a>Campos de acesso remoto

| Nome | Tipo | Pode ser nulo | Lista de valores |
|--|--|--|--|
| **porto** | Numérico | No | - |
| **transporte** | String | No | TCP ou UDP |
| **cliente** | String | No | Endereço IP |
| **clienteSoftware** | String | No | SSH, VNC, desktop remoto ou espectador de equipa |

#### <a name="open-port-fields"></a>Campos portuários abertos

| Nome | Tipo | Pode ser nulo | Lista de valores |
|--|--|--|--|
| **porto** | Numérico | No | - |
| **transporte** | String | No | TCP ou UDP |
| **protocolo** | String | Yes | - |
| **isConflictingWithFirewall** | Booleano | No | Verdadeiro ou falso |

#### <a name="cve-fields"></a>Campos CVE

| Nome | Tipo | Pode ser nulo | Lista de valores |
|--|--|--|--|
| **ID** | String | No | - |
| **pontuação** | Numérico | No | Double (Duplo) |
| **descrição** | String | No | - |

#### <a name="response-example"></a>Exemplo de resposta

```rest
[

    {
    
        "name": "IED \#10",
        
        "ipAddresses": ["10.2.1.10"],
        
        "securityScore": 100,
        
        "vendor": "ABB Switzerland Ltd, Power Systems",
        
        "firmwareVersion": null,
        
        "model": null,
        
        "operatingSystem": {
        
            "name": "ABB Switzerland Ltd, Power Systems",
            
            "type": "abb",
            
            "version": null,
            
            "latestVersion": null
        
        },
        
        "vulnerabilities": {
            
        "antiViruses": [
            
        "McAfee"
            
        ],
            
        "plainTextPasswords": [
            
            {
            
                "password": "123456",
                
                "protocol": "HTTP",
                
                "lastSeen": 1462726930471,
                
                "strength": "Very Weak"
            
            }
            
        ],
            
        "remoteAccess": [
            
            {
            
                "port": 5900,
                
                "transport": "TCP",
                
                "clientSoftware": "VNC",
                
                "client": "10.2.1.20"
            
            }
            
        ],
            
        "isBackupServer": true,
            
        "openedPorts": [
            
            {
            
                "port": 445,
                
                "transport": "TCP",
                
                "protocol": "SMP Over IP",
                
                "isConflictingWithFirewall": false
            
            },
            
            {
            
                "port": 80,
                
                "transport": "TCP",
                
                "protocol": "HTTP",
                
                "isConflictingWithFirewall": false
            
            }
            
        ],
            
        "isEngineeringStation": false,
            
        "isKnownScanner": false,
            
        "cves": [
            
            {
            
                "id": "CVE-2015-6490",
                
                "score": 10,
                
                "description": "Frosty URL - Stack-based buffer overflow on Allen-Bradley MicroLogix 1100 devices before B FRN 15.000 and 1400 devices through B FRN 15.003 allows remote attackers to execute arbitrary code via unspecified vectors"
            
            },
            
            {
            
                "id": "CVE-2012-6437",
                
                "score": 10,
                
                "description": "MicroLogix 1100 and 1400 do not properly perform authentication for Ethernet firmware updates, which allows remote attackers to execute arbitrary code via a Trojan horse update image"
            
            },
            
            {
            
                "id": "CVE-2012-6440",
                
                "score": 9.3,
                
                "description": "MicroLogix 1100 and 1400 allows man-in-the-middle attackers to conduct replay attacks via HTTP traffic."
        
            }
        
        ],
        
        "isUnauthorized": false,
        
        "malwareIndicationsDetected": true
        
        }
    
    }

]

```

### <a name="retrieve-security-vulnerabilities"></a>Recuperar vulnerabilidades de segurança

Utilize esta API para solicitar resultados de uma avaliação geral da vulnerabilidade. Esta avaliação fornece informações sobre o nível de segurança do seu sistema.

Esta avaliação baseia-se em informações gerais da rede e do sistema e não numa avaliação específica do dispositivo.

#### <a name="apiv1reportsvulnerabilitiessecurity"></a>/api/v1/reports/vulnerabilidades/segurança

#### <a name="method"></a>Método

**GET**

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo de resposta

Objeto JSON que representa resultados avaliados. Cada chave pode ser anulada. Caso contrário, conterá um objeto JSON com teclas não au nulas.

### <a name="result-fields"></a>Campos de resultados

**Chaves**

**Devices não autorizados**

| Nome do campo | Tipo | Lista de valores |
| ---------- | ---- | -------------- |
| **endereço** | String | Endereço IP |
| **nome** | String | - |
| **firstDetectionTime** | Numérico | Época (UTC) |
| lastSeen | Numérico | Época (UTC) |

**ilegalTrafficByFirewallRules**

| Nome do campo | Tipo | Lista de valores |
| ---------- | ---- | -------------- |
| **servidor** | String | Endereço IP |
| **cliente** | String | Endereço IP |
| **porto** | Numérico | - |
| **transporte** | String | TCP, UDP ou ICMP |

**fracas Regras de Fogo**

| Nome do campo | Tipo | Lista de valores |
| ---------- | ---- | -------------- |
| **fontes** | JSON conjunto de fontes. Cada fonte pode estar em qualquer um dos quatro formatos. | "Any", "endereço ip (Anfitrião)", "do ip-to ip (RANGE)", "endereço ip, máscara de sub-rede (REDE)" |
| **destinos** | JSON conjunto de destinos. Cada destino pode estar em qualquer um dos quatro formatos. | "Any", "endereço ip (Anfitrião)", "do ip-to ip (RANGE)", "endereço ip, máscara de sub-rede (REDE)" |
| **portos** | JSON matriz de portas em qualquer um dos três formatos | "Qualquer", "porta (protocolo, se detetado)", "de porto para porto (protocolo, se detetado)" |

**acessoPonts**

| Nome do campo | Tipo | Lista de valores |
| ---------- | ---- | -------------- |
| **macAddress** | String | Endereço MAC |
| **fornecedor** | String | Nome do fornecedor |
| **ipAddress** | String | Endereço IP, ou N/A |
| **nome** | String | Nome do dispositivo, ou N/A |
| **sem fios** | String | Não, suspeito, ou sim. |

**conexõesBetweenSubnets**

| Nome do campo | Tipo | Lista de valores |
| ---------- | ---- | -------------- |
| **servidor** | String | Endereço IP |
| **cliente** | String | Endereço IP |

**industrialMalwareIndicators**

| Nome do campo | Tipo | Lista de valores |
| ---------- | ---- | -------------- |
| **hora de deteção** | Numérico | Época (UTC) |
| **alertaMessage** | String | - |
| **descrição** | String | - |
| **dispositivos** | Matriz JSON | Nomes do dispositivo | 

**internetConsions**

| Nome do campo | Tipo | Lista de valores |
| ---------- | ---- | -------------- |
| **internaAddress** | String | Endereço IP |
| **autorizado** | Booleano | Sim ou Não | 
| **exterioresas** | Matriz JSON | Endereço IP |

#### <a name="response-example"></a>Exemplo de resposta

```rest
{

    "unauthorizedDevices": [
    
        {
        
            "address": "10.2.1.14",
            
            "name": "PLC \#14",
            
            "firstDetectionTime": 1462645483000,
            
            "lastSeen": 1462645495000,
        
        }
    
    ],
    
    "redundantFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ],
    
    "connectionsBetweenSubnets": [
    
        {
        
            "server": "10.2.1.22",
            
            "client": "170.39.2.0"
        
        }
    
    ],
    
    "industrialMalwareIndications": [
    
        {
        
            "detectionTime": 1462645483000,
            
            "alertMessage": "Suspicion of Malicious Activity (Regin)",
            
            "description": "Suspicious network activity was detected. Such behavior might be attributed to the Regin malware.",
            
            "addresses": [
            
                "10.2.1.4",
                
                "10.2.1.5"
            
            ]
        
        }
    
    ],
    
    "illegalTrafficByFirewallRules": [
    
        {
        
            "server": "10.2.1.7",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.8",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.9",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        }
    
    ],
    
    "internetConnections": [
    
        {
        
            "internalAddress": "10.2.1.1",
            
            "authorized": "Yes",
            
            "externalAddresses": ["10.2.1.2",”10.2.1.3”]
        
        }
    
    ],
    
    "accessPoints": [
    
        {
        
            "macAddress": "ec:08:6b:0f:1e:22",
            
            "vendor": "TP-LINK TECHNOLOGIES",
            
            "ipAddress": "173.194.112.22",
            
            "name": "Enterprise AP",
            
            "wireless": "Yes"
        
        }
    
    ],
    
    "weakFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ]

}

```

### <a name="retrieve-operational-vulnerabilities"></a>Recuperar vulnerabilidades operacionais

Utilize esta API para solicitar resultados de uma avaliação geral da vulnerabilidade. Esta avaliação fornece informações sobre o estado operacional da sua rede. Baseia-se em informações gerais de rede e sistema e não numa avaliação específica do dispositivo.

#### <a name="apiv1reportsvulnerabilitiesoperational"></a>/api/v1/reports/vulnerabilidades/operacional

#### <a name="method"></a>Método

**GET**

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo de resposta

Objeto JSON que representa resultados avaliados. Cada chave contém uma série de resultados JSON.

#### <a name="result-fields"></a>Campos de resultados

**Chaves**

**backupServer**

| Nome do campo | Tipo | Lista de valores |
|--|--|--|
| **fonte** | String | Endereço IP |
| **destino** | String | Endereço IP |
| **porto** | Numérico | - |
| **transporte** | String | TCP ou UDP |
| **backupMaximalInterval** | String | - |
| **lastSeenBackup** | Numérico | Época (UTC) |

**ipNetworks**

| Nome do campo | Tipo | Lista de valores |
|--|--|--|
| **endereço** s | Numérico | - |
| **rede** | String | Endereço IP |
| **mask** | String | Máscara de sub-rede |

**protocoloProblems**

| Nome do campo | Tipo | Lista de valores |
|--|--|--|
| **protocolo** | String | - |
| **endereços** | Matriz JSON | Endereços IP |
| **alerta** | String | - |
| **reportTime** | Numérico | Época (UTC) |

**protocoloDataVolumes**

| Nome do campo | Tipo | Lista de valores |
|--|--|--|
| protocolo | String | - |
| volume | String | "número de volume MB" |

**desconexões**

| Nome do campo | Tipo | Lista de valores |
|--|--|--|
| **ativoAddress** | String | Endereço IP |
| **nome de ativo** | String | - |
| **última Hora da Deteção** | Numérico | Época (UTC) |
| **backToNormalTime** | Numérico | Época (UTC) |     

#### <a name="response-example"></a>Exemplo de resposta

```rest
{

    "backupServer": [
    
        {
        
            "backupMaximalInterval": "1 Hour, 29 Minutes",
            
            "source": "10.2.1.22",
            
            "destination": "170.39.2.14",
            
            "port": 10000,
            
            "transport": "TCP",
            
            "lastSeenBackup": 1462645483000
        
        }
    
    ],

    "ipNetworks": [
    
        {
        
            "addresses": "21",
            
            "network": "10.2.1.0",
            
            "mask": "255.255.255.0"
        
        },
        
        {
        
            "addresses": "3",
            
            "network": "170.39.2.0",
            
            "mask": "255.255.255.0"
        
        }
    
    ],

    "protocolProblems": [
    
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.7",
                
                "10.2.1.8"
            
            ],
            
            "alert": "Illegal DNP3 Operation",
            
            "reportTime": 1462645483000
        
        },
        
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.15"
            
            ],
            
            "alert": "Master Requested an Application Layer Confirmation",
            
            "reportTime": 1462645483000
        
        }
        
    ],

    "protocolDataVolumes": [
    
        {
        
            "protocol": "MODBUS (502)",
            
            "volume": "21.07 MB"
        
        },
        
        {
        
            "protocol": "SSH (22)",
            
            "volumn": "0.001 MB"
        
        }
    
    ],
    
    "disconnections": [
    
        {
        
            "assetAddress": "10.2.1.3",
            
            "assetName": "PLC \#3",
            
            "lastDetectionTime": 1462645483000,
            
            "backToNormalTime": 1462645484000
        
        }
    
    ]

}

```

### <a name="validate-user-credentials"></a>Validar credenciais de utilizador

Utilize esta API para validar um Defender para nome de utilizador IoT e senha. Todas as funções de utilizador defender para IoT podem funcionar com a API.

Você não precisa de um Defender para o token de acesso IoT para usar esta API.

#### <a name="apiexternalauthenticationvalidation"></a>/api/external/autenticação/validação

#### <a name="method"></a>Método

**POST**

#### <a name="request-type"></a>Tipo de pedido

**JSON**

#### <a name="query-parameters"></a>Parâmetros de consulta

| **Nome** | **Tipo** | **Pode ser nulo** |
|--|--|--|
| **nome de utilizador** | String | No |
| **palavra-passe** | String | No |

#### <a name="request-example"></a>Exemplo de pedido

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!"

}

```

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo de resposta

Cadeia de mensagens com os detalhes do estado da operação:

- **Sucesso - msg**: Autenticação conseguiu

- **Falha - erro**: Validação de Credenciais Falhou

#### <a name="response-example"></a>Exemplo de resposta

```rest
response:

{

    "msg": "Authentication succeeded."

}

```

### <a name="change-password"></a>Alterar palavra-passe

Utilize esta API para permitir que os utilizadores alterem as suas próprias palavras-passe. Todas as funções de utilizador defender para IoT podem funcionar com a API. Você não precisa de um Defender para o token de acesso IoT para usar esta API.

#### <a name="externalauthenticationset_password"></a>/external/autenticação/set_password

#### <a name="method"></a>Método

**POST**

#### <a name="request-type"></a>Tipo de pedido

**JSON**

#### <a name="request-example"></a>Exemplo de pedido

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo de resposta

Cadeia de mensagens com os detalhes do estado da operação:

- **Sucesso – MSG**: A palavra-passe foi substituída

- **Falha – erro**: Falha de autenticação do utilizador

- **Falha – erro**: Palavra-passe não corresponde à política de segurança

#### <a name="response-example"></a>Exemplo de resposta

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Campos de dispositivos

| **Nome** | **Tipo** | **Pode ser nulo** |
|--|--|--|
| **nome de utilizador** | String | No |
| **palavra-passe** | String | No |
| **new_password** | String | No |

### <a name="user-password-update-by-system-admin"></a>Atualização da palavra-passe do utilizador por administração do sistema

Utilize esta API para permitir que os administradores do sistema alterem palavras-passe para utilizadores especificados. As funções de utilizador do administrador do Defender para IoT podem funcionar com a API. Você não precisa de um Defender para o token de acesso IoT para usar esta API.

#### <a name="externalauthenticationset_password_by_admin"></a>/external/autenticação/set_password_by_admin

#### <a name="method"></a>Método

**POST**

#### <a name="request-type"></a>Tipo de pedido

**JSON**

#### <a name="request-example"></a>Exemplo de pedido

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo de resposta

Cadeia de mensagens com os detalhes do estado da operação:

- **Sucesso – MSG**: A palavra-passe foi substituída

- **Falha – erro**: Falha de autenticação do utilizador

- **Falha – erro**: O utilizador não existe

- **Falha – erro**: Palavra-passe não corresponde à política de segurança

- **Falha – erro**: O utilizador não tem as permissões para alterar a palavra-passe

#### <a name="response-example"></a>Exemplo de resposta

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>Campos de dispositivos

| **Nome** | **Tipo** | **Pode ser nulo** |
|--|--|--|
| **admin_username** | String | No |
| **admin_password** | String | No |
| **nome de utilizador** | String | No |
| **new_password** | String | No |

## <a name="on-premises-management-console-api-specifications"></a>Especificações de API de consola de gestão no local

Esta secção descreve as seguintes APIs de gestão de consolas no local:

- **/external/v1/alertas/<UUID>**

- **Exclusões de alerta (Janela de Manutenção)**

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="A janela de exclusão de alerta, mostrando regras ativas.":::

Defina condições em que os alertas não serão enviados. Por exemplo, definir e atualizar os tempos de paragem e início, dispositivos ou sub-redes que devem ser excluídos ao desencadear alertas, ou Defender para motores IoT que devem ser excluídos. Por exemplo, durante uma janela de manutenção, é melhor parar a entrega de todos os alertas, exceto para alertas de malware em dispositivos críticos.

As APIs que define aqui aparecem na janela **de exclusões** de alerta da consola de gestão no local como uma regra de exclusão apenas de leitura.

#### <a name="externalv1maintenancewindow"></a>/external/v1/manutençãoWindow

- **/externo/autenticação/validação**

- **Exemplo de resposta**

- **resposta:**

```rest
{
    "msg": "Authentication succeeded."
}

```

#### <a name="change-password"></a>Alterar palavra-passe

Utilize esta API para permitir que os utilizadores alterem as suas próprias palavras-passe. Todas as funções de utilizador defender para IoT podem funcionar com a API. Você não precisa de um Defender para o token de acesso IoT para usar esta API.

- **/external/autenticação/set_password**

#### <a name="user-password-update-by-system-admin"></a>Atualização da palavra-passe do utilizador por administração do sistema

Utilize esta API para permitir que os administradores do sistema alterem palavras-passe para utilizadores específicos. Defender as funções de utilizador de administração IoT pode funcionar com a API. Você não precisa de um Defender para o token de acesso IoT para usar esta API.

- **/external/autenticação/set_password_by_admin**

### <a name="retrieve-device-information"></a>Recuperar informações sobre dispositivos

Esta API solicita uma lista de todos os dispositivos detetados pelo Defender para sensores IoT que estejam ligados a uma consola de gestão no local.

- **/external/v1/dispositivos**

#### <a name="method"></a>Método

**GET**

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo de resposta

Matriz de objetos JSON que representam dispositivos.

#### <a name="query-parameters"></a>Parâmetros de consulta

- **autorizado :** Filtrar apenas dispositivos autorizados e não autorizados.

- **siteId**: Filtrar apenas dispositivos relacionados com sites específicos.

- **zoneId**: Filtrar apenas dispositivos relacionados com zonas específicas. [1](#1)

- **sensorid**: Filtrar apenas os dispositivos detetados por sensores específicos. [1](#1)

###### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="1">1</a> *Pode não ter o local e a zona de identificação. Se for esse o caso, consultar todos os dispositivos para recuperar o local e o ID da zona.*

#### <a name="query-parameters-example"></a>Exemplo de parâmetros de consulta

`/external/v1/devices?authorized=true`

`/external/v1/devices?authorized=false`

`/external/v1/devices?siteId=1,2`

`/external/v1/devices?zoneId=5,6`

`/external/v1/devices?sensorId=8`

#### <a name="device-fields"></a>Campos de dispositivos

| Nome | Tipo | Pode ser nulo | Lista de valores |
|--|--|--|--|
| **sensorid** | Numérico | No | - |
| **zoneId** | Numérico | Yes | - |
| **siteId** | Numérico | Yes | - |
| **ipAddresses** | Matriz JSON | Yes | Endereços IP (pode ser mais do que um endereço no caso de endereços de internet ou um dispositivo com NICs duplos) |
| **nome** | String | No | - |
| **tipo** | String | No | Desconhecido, Estação de Engenharia, PLC, HMI, Historiador, Controlador de Domínio, Servidor DB, Ponto de Acesso Sem Fios, Router, Switch, Servidor, Estação de Trabalho, Câmara IP, Impressora, Firewall, Estação terminal, Gateway VPN, Internet ou Multicast e Transmissão |
| **macAddresses** | Matriz JSON | Yes | Endereços MAC (pode ser mais do que um endereço no caso de um dispositivo com NICs duplos) |
| **sistema operativo** | String | Yes | - |
| **engenhariaStation** | Booleano | No | Verdadeiro ou falso |
| **scanner** | Booleano | No | Verdadeiro ou falso |
| **autorizado** | Booleano | No | Verdadeiro ou falso |
| **fornecedor** | String | Yes | - |
| **Protocolos** | Matriz JSON | Yes | Objeto de protocolo |
| **firmware** | Matriz JSON | Yes | Objeto firmware |

#### <a name="protocol-fields"></a>Campos de protocolo

| Nome | Tipo | Pode ser nulo | Lista de valores |
|--|--|--|--|
| Nome | String | No | - |
| Endereços | Matriz JSON | Yes | Valores mestres ou numéricos |

#### <a name="firmware-fields"></a>Campos de firmware

| Nome | Tipo | Pode ser nulo | Lista de valores |
|--|--|--|--|
| **série** | String | No | N/A, ou o valor real |
| **modelo** | String | No | N/A, ou o valor real |
| **firmwareVersion** | Double (Duplo) | No | N/A, ou o valor real |
| **AdicionalData** | String | No | N/A, ou o valor real |
| **móduloAddress** | String | No | N/A, ou o valor real |
| **rack** | String | No | N/A, ou o valor real |
| **slot** | String | No | N/A, ou o valor real |
| **endereço** | String | No | N/A, ou o valor real |

#### <a name="response-example"></a>Exemplo de resposta

```rest
[

    {
    
    "sensorId": 7,
    
    "zoneId": 1,
    
    "siteId": 1,
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    }

],

"id": 79,

"macAddresses": null,

"authorized": true,

"ipAddresses": [

    "10.4.14.102"

],

"engineeringStation": false,

"type": "PLC",

"operatingSystem": null,

"protocols": [

    {
    
        "addresses": [],
        
        "id": 62,
        
        "name": "Omron FINS"
    
    }

],

"scanner": false

}

]
```

### <a name="retrieve-alert-information"></a>Recuperar informações de alerta

Utilize esta API para recuperar todos ou alertas filtrados de uma consola de gestão no local.

#### <a name="externalv1alerts"></a>/external/v1/alertas

#### <a name="method"></a>Método

**GET**

#### <a name="query-parameters"></a>Parâmetros de consulta

- **estado**: Para filtrar apenas alertas manuseados e não manipulados.

  **Exemplo:**

  `/api/v1/alerts?state=handled`

- **a partir do Tempo**: Para filtrar alertas criados a partir de um tempo específico (em milissegundos, UTC).

  **Exemplo:**

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime**: Para filtrar alertas criados apenas antes de um tempo específico (em milissegundos, UTC).

  **Exemplo:**

  `/api/v1/alerts?toTime=<epoch>`

- **siteId**: O local onde o alerta foi descoberto. [2](#2)

- **zoneId**: A zona em que o alerta foi descoberto. [2](#2)

- **sensor**: O sensor no qual o alerta foi descoberto.

##### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="2">2</a> *Pode não ter o local e a zona de identificação. Se for esse o caso, consultar todos os dispositivos para recuperar o local e o ID da zona.*

#### <a name="alert-fields"></a>Campos de alerta

| Nome | Tipo | Pode ser nulo | Lista de valores |
|--|--|--|--|
| **ID** | Numérico | No | - |
| **Hora** | Numérico | No | Época (UTC) |
| **título** | String | No | - |
| **Mensagem** | String | No | - |
| **severidade** | String | No | Aviso, Menor, Major ou Crítico |
| **motor** | String | No | Violação de protocolo, violação de políticas, malware, anomalia ou operacional |
| **fonteDevice** | Numérico | Yes | ID do Dispositivo |
| **destinoDevice** | Numérico | Yes | ID do Dispositivo |
| **informação adicional** | Objeto de informação adicional | Yes | - |

#### <a name="additional-information-fields"></a>Campos de informação adicionais

| Nome | Tipo | Pode ser nulo | Lista de valores |
|--|--|--|--|
| **descrição** | String | No | - |
| **informação** | Matriz JSON | Não | String |

#### <a name="response-example"></a>Exemplo de resposta

```rest
[

    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Traffic Detected on sensor Interface",
        
        "additionalInformation": null,
        
        "sourceDevice": 0,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808245000,
        
        "sensorId": 1,
        
        "message": "The sensor resumed detecting network traffic on ens224.",
        
        "destinationDevice": 0,
        
        "id": 1,
        
        "severity": "Warning"
    
    },
    
    {
    
        "engine": "Anomaly",
        
        "handled": false,
        
        "title": "Address Scan Detected",
        
        "additionalInformation": null,
        
        "sourceDevice": 4,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808260000,
        
        "sensorId": 1,
        
        "message": "Address scan detected.\nScanning address: 10.10.10.22\nScanned subnet: 10.11.0.0/16\nScanned addresses: 10.11.1.1, 10.11.20.1, 10.11.20.10, 10.11.20.100, 10.11.20.2, 10.11.20.3, 10.11.20.4, 10.11.20.5, 10.11.20.6, 10.11.20.7...\nIt is recommended to notify the security officer of the incident.",
        
        "destinationDevice": 0,
        
        "id": 2,
        
        "severity": "Critical"
    
    },
    
    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Suspicion of Unresponsive MODBUS Device",
        
        "additionalInformation": null,
        
        "sourceDevice": 194,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808285000,
        
        "sensorId": 1,
        
        "message": "Outstation device 10.13.10.1 (Protocol Address 53) seems to be unresponsive to MODBUS requests.",
        
        "destinationDevice": 0,
        
        "id": 3,
        
        "severity": "Minor"
    
    }
  
]
```

### <a name="qradar-alerts"></a>Alertas QRadar

A integração do QRadar com o Defender para IoT ajuda-o a identificar os alertas gerados pelo Defender para IoT e a realizar ações com estes alertas. O QRadar recebe os dados do Defender para IoT e, em seguida, contacta a componente pública da consola de gestão de API no local.

Para enviar os dados descobertos pelo Defender para IoT para o QRadar, defina uma regra de encaminhamento no sistema Defender para IoT e selecione a opção de tratamento de **alerta de suporte remoto.**

:::image type="content" source="media/references-work-with-defender-for-iot-apis/edit-forwarding-rules.png" alt-text="Edite as regras de encaminhamento para corresponder às suas necessidades.":::

Quando seleciona esta opção durante o processo de configuração das regras de encaminhamento, os seguintes campos adicionais aparecem no QRadar:

- **UUID**: Identificador de alerta único, como 1-1555245116250.

- **Local**: O local onde o alerta foi descoberto.

- **Zona**: Zona onde o alerta foi descoberto.

Exemplo da carga útil enviada à QRadar:

```
<9>May 5 12:29:23 sensor_Agent LEEF:1.0|CyberX|CyberX platform|2.5.0|CyberX platform Alert|devTime=May 05 2019 15:28:54 devTimeFormat=MMM dd yyyy HH:mm:ss sev=2 cat=XSense Alerts title=Device is Suspected to be Disconnected (Unresponsive) score=81 reporter=192.168.219.50 rta=0 alertId=6 engine=Operational senderName=sensor Agent UUID=5-1557059334000 site=Site zone=Zone actions=handle dst=192.168.2.2 dstName=192.168.2.2 msg=Device 192.168.2.2 is suspected to be disconnected (unresponsive).
```

#### <a name="externalv1alertsltuuidgt"></a>/external/v1/alerts/ &lt; UUID&gt;

#### <a name="method"></a>Método

**PUT**

#### <a name="request-type"></a>Tipo de pedido

**JSON**

#### <a name="request-content"></a>Conteúdo do pedido

Objeto JSON que representa a ação a executar no alerta que contém o UUID.

#### <a name="action-fields"></a>Campos de ação

| Nome | Tipo | Pode ser nulo | Lista de valores |
|--|--|--|--|
| **ação** | String | No | manusear ou manusearAndLearn |

#### <a name="request-example"></a>Exemplo de pedido

```rest
{
    "action": "handle"
}

```

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo de resposta

Matriz de objetos JSON que representam dispositivos.

#### <a name="response-fields"></a>Campos de resposta


| Nome | Tipo | Pode ser nulo | Descrição |
|--|--|--|--|
| **conteúdo / erro** | String | No | Se o pedido for bem sucedido, a propriedade de conteúdo aparece. Caso contrário, a propriedade de erro aparece. |

#### <a name="possible-content-values"></a>Possíveis valores de conteúdo

| Código de estado | Valor do conteúdo | Descrição |
|--|--|--|
| 200 | O pedido de atualização de alerta terminou com sucesso. | O pedido de atualização terminou com sucesso. Sem comentários. |
| 200 | O alerta já foi tratado **(pega).** | O alerta já foi dado quando foi recebido um pedido de alerta.<br />O alerta continua **a ser tratado.** |
| 200 | O alerta já foi tratado e aprendido **(handleAndLearn).** | O alerta já foi tratado e ficou a saber-se quando foi recebido um pedido de **manuseamento do AndLearn.**<br />O alerta permanece no estado **doAndLearn.** |
| 200 | O alerta já foi tratado (**manuseado).**<br />O punho e a aprendizagem **(handleAndLearn)** foram realizados no alerta. | O alerta já foi dado quando foi recebido um pedido de **manuseamento doAndLearn.**<br />O alerta torna-se **pegaAndLearn**. |
| 200 | O alerta já foi tratado e aprendido **(handleAndLearn).** Pedido de manípulo ignorado. | O alerta já estava **a ser tratado e** a Aprender quando foi recebido um pedido para lidar com o alerta. O alerta permanece **no Cabo Elearn**. |
| 500 | Ação inválida. | A ação que foi enviada não é uma ação válida para realizar no alerta. |
| 500 | Ocorreu um erro inesperado. | Ocorreu um erro inesperado. Para resolver o problema, contacte o Suporte Técnico. |
| 500 | Não foi consegui executar o pedido porque não foi encontrado nenhum alerta para este UUID. | O alerta especificado UUID não foi encontrado no sistema. |

#### <a name="response-example"></a>Exemplo de resposta

**Bem sucedido**

```rest
{
    "content": "Alert update request finished successfully"
}
```

**Sem sucesso**

```rest
{
    "error": "Invalid action"
}
```

### <a name="alert-exclusions-maintenance-window"></a>Exclusões de alerta (janela de manutenção)

Defina condições em que os alertas não serão enviados. Por exemplo, definir e atualizar os tempos de paragem e início, dispositivos ou sub-redes que devem ser excluídos ao desencadear alertas, ou Defender para motores IoT que devem ser excluídos. Por exemplo, durante uma janela de manutenção, é melhor parar a entrega de alerta de todos os alertas, exceto para alertas de malware em dispositivos críticos.

As APIs que define aqui aparecem na janela **de exclusões** de alerta da consola de gestão no local como uma regra de exclusão apenas de leitura.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="A janela 'Exclusões de Alerta', mostrando uma lista de todas as regras de exclusão. ":::

#### <a name="externalv1maintenancewindow"></a>/external/v1/manutençãoWindow

#### <a name="method---post"></a>Método - POST

#### <a name="query-parameters"></a>Parâmetros de consulta

- **ticketId**: Define o ID do bilhete de manutenção nos sistemas do utilizador.

- **ttl**: Define o TTL (tempo de vida), que é a duração da janela de manutenção em minutos. Após o período de tempo definido, o sistema começa automaticamente a enviar alertas.

- **Motores**: Define a partir do qual o motor de segurança para suprimir alertas durante o processo de manutenção:

   - ANOMALIA

   - MALWARE

   - OPERACIONAL

   - POLICY_VIOLATION

   - PROTOCOL_VIOLATION

- **sensorids**: Define a partir do qual o sensor Defender para IoT suprimir alertas durante o processo de manutenção. É o mesmo ID recuperado de /api/v1/eletrodomésticos (GET).

- **sub-redes**: Define a partir de qual sub-rede suprimir alertas durante o processo de manutenção. A sub-rede é enviada no seguinte formato: 192.168.0.0/16.

#### <a name="error-codes"></a>Códigos de erro

- **201 (Criado)**: A ação foi concluída com sucesso.

- **400 (Mau pedido)**: Aparece nos seguintes casos:

   - O parâmetro **ttl** não é numérico ou não é positivo.

   - O parâmetro **das sub-redes** foi definido usando um formato errado.

   - O parâmetro **ticketId** está em falta.

   - O parâmetro do **motor** não corresponde aos motores de segurança existentes.

- **404 (Não Encontrado)**: Um dos sensores não existe.

- **409 (Conflito)**: O bilhete identifica-se a outra janela de manutenção aberta.

- **500 (Erro do Servidor Interno)**: Qualquer outro erro inesperado.

> [!NOTE]
> Certifique-se de que o bilhete não está ligado a uma janela aberta existente. A seguinte regra de exclusão é gerada: Manutenção-{token name}-{ticket ID}.

#### <a name="method---put"></a>Método - PUT

Permite atualizar a duração da janela de manutenção depois de iniciar o processo de manutenção alterando o parâmetro **ttl.** A nova definição de duração substitui a anterior.

Este método é útil quando pretende definir uma duração mais longa do que a duração atualmente configurada.

#### <a name="query-parameters"></a>Parâmetros de consulta

- **ticketId**: Define o ID do bilhete de manutenção nos sistemas do utilizador.

- **ttl**: Define a duração da janela em minutos.

#### <a name="error-code"></a>Código de erro

- **200 (OK)**: A ação foi concluída com sucesso.

- **400 (Mau pedido)**: Aparece nos seguintes casos:

   - O parâmetro **ttl** não é numérico ou não é positivo.

   - O parâmetro **ticketId** está em falta.

   - O parâmetro **ttl** está faltando.

- **404 (Não Encontrado)**: O bilhete de identificação não está ligado a uma janela de manutenção aberta.

- **500 (Erro do Servidor Interno)**: Qualquer outro erro inesperado.

> [!NOTE]
> Certifique-se de que o bilhete está ligado a uma janela aberta existente.

#### <a name="method---delete"></a>Método - EXCLUIR

Fecha uma janela de manutenção existente.

#### <a name="query-parameters"></a>Parâmetros de consulta

- **ticketId**: Regista o ID do bilhete de manutenção nos sistemas do utilizador.

#### <a name="error-code"></a>Código de erro

- **200 (OK)**: A ação foi concluída com sucesso.

- **400 (Mau pedido)**: Falta o parâmetro **ticketId.**

- **404 (Não Encontrado)**: O bilhete de identificação não está ligado a uma janela de manutenção aberta.

- **500 (Erro do Servidor Interno)**: Qualquer outro erro inesperado.

> [!NOTE]
> Certifique-se de que o bilhete está ligado a uma janela aberta existente.

#### <a name="method---get"></a>Método - GET

Recupere um registo de todas as ações abertas, próximas e atualizadas que foram realizadas no sistema durante a manutenção. Pode recuperar um registo apenas para janelas de manutenção que estavam ativas no passado e que foram fechadas.

#### <a name="query-parameters"></a>Parâmetros de consulta

- **fromDate**: Filtra os registos a partir da data predefinida e posteriormente. O formato é 2019-12-30.

- **toDate**: Filtra os registos até à data predefinida. O formato é 2019-12-30.

- **ticketId**: Filtra os registos relacionados com um ID de bilhete específico.

- **tokenName**: Filtra os registos relacionados com um nome simbólico específico.

#### <a name="error-code"></a>Código de erro

- **200 (OK)**: A ação foi concluída com sucesso.

- **400 (Mau Pedido)**: O formato da data está errado.

- **204 (Sem Conteúdo)**: Não há dados a mostrar.

- **500 (Erro do Servidor Interno)**: Qualquer outro erro inesperado.

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo de resposta

Matriz de objetos JSON que representam operações de janela de manutenção.

#### <a name="response-structure"></a>Estrutura de resposta

| Nome | Tipo | Comentário | Pode ser nulo |
|--|--|--|--|
| **dataTime** | String | Exemplo: "2012-04-23T18:25:43.511Z" | não |
| **ticketId** | String | Exemplo: "9a5fe99c-d914-4bda-9332-307384fe40bf" | não |
| **tokenName** | String | - | não |
| **motores** | Matriz da cadeia | - | sim |
| **sensorids** | Matriz da cadeia | - | sim |
| **sub-redes** | Matriz da cadeia | - | sim |
| **ttl** | Numérico | - | sim |
| **operaçãoType** | String | Os valores são "OPEN", "UPDATE" e "CLOSE" | não |

### <a name="authenticate-user-credentials"></a>Autenticar credenciais de utilizador

Utilize esta API para validar as credenciais do utilizador. Todas as funções de utilizador defender para IoT podem funcionar com a API. Você não precisa de um Defender para o token de acesso IoT para usar esta API.

#### <a name="externalauthenticationvalidation"></a>/externo/autenticação/validação

#### <a name="method"></a>Método

**POST**

#### <a name="request-type"></a>Tipo de pedido

**JSON**

#### <a name="request-example"></a>Exemplo de pedido

```rest
request:

{

    "username": "test",

    "password": "Test12345\!"

}
```

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo de resposta

Cadeia de mensagens com os detalhes do estado da operação:

- **Sucesso – msg**: Autenticação conseguiu

- **Falha – erro**: Validação de Credenciais Falhou

#### <a name="device-fields"></a>Campos de dispositivos

| **Nome** | **Tipo** | **Pode ser nulo** |
|--|--|--|
| **nome de utilizador** | String | No |
| **palavra-passe** | String | No |

#### <a name="response-example"></a>Exemplo de resposta

```rest
response:

{

    "msg": "Authentication succeeded."

}
```

### <a name="change-password"></a>Alterar palavra-passe

Utilize esta API para permitir que os utilizadores alterem as suas próprias palavras-passe. Todas as funções de utilizador defender para IoT podem funcionar com a API. Você não precisa de um Defender para o token de acesso IoT para usar esta API.

#### <a name="externalauthenticationset_password"></a>/external/autenticação/set_password

#### <a name="method"></a>Método

**POST**

#### <a name="request-type"></a>Tipo de pedido

**JSON**

#### <a name="request-example"></a>Exemplo de pedido

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo de resposta

Cadeia de mensagens com os detalhes do estado da operação:

- **Sucesso – MSG**: A palavra-passe foi substituída

- **Falha – erro**: Falha de autenticação do utilizador

- **Falha – erro**: Palavra-passe não corresponde à política de segurança

#### <a name="response-example"></a>Exemplo de resposta

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Campos de dispositivos

| **Nome** | **Tipo** | **Pode ser nulo** |
|--|--|--|
| **nome de utilizador** | String | No |
| **palavra-passe** | String | No |
| **new_password** | String | No |

### <a name="user-password-update-by-system-admin"></a>Atualização da palavra-passe do utilizador por administração do sistema

Utilize esta API para permitir que os administradores do sistema alterem palavras-passe para utilizadores especificados. Defender as funções de utilizador de administração IoT pode funcionar com a API. Você não precisa de um Defender para o token de acesso IoT para usar esta API.

#### <a name="externalauthenticationset_password_by_admin"></a>/external/autenticação/set_password_by_admin

#### <a name="method"></a>Método

**POST**

#### <a name="request-type"></a>Tipo de pedido

**JSON**

#### <a name="request-example"></a>Exemplo de pedido

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>Tipo de resposta

**JSON**

#### <a name="response-content"></a>Conteúdo de resposta

Cadeia de mensagens com os detalhes do estado da operação:

- **Sucesso – MSG**: A palavra-passe foi substituída

- **Falha – erro**: Falha de autenticação do utilizador

- **Falha – erro**: O utilizador não existe

- **Falha – erro**: Palavra-passe não corresponde à política de segurança

- **Falha – erro**: O utilizador não tem as permissões para alterar a palavra-passe

#### <a name="response-example"></a>Exemplo de resposta

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>Campos de dispositivos

| **Nome** | **Tipo** | **Pode ser nulo** |
|--|--|--|
| **admin_username** | String | No |
| **admin_password** | String | No |
| **nome de utilizador** | String | No |
| **new_password** | String | No |

## <a name="see-also"></a>Veja também
[Investigar deteções de sensores num inventário de dispositivos](how-to-investigate-sensor-detections-in-a-device-inventory.md) 
 [Investigue todas as deteções de sensores empresariais num inventário de dispositivos](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)
