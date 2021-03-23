---
title: Configure o Microsoft OPC Publisher
description: Neste tutorial, aprende-se a configurar a Editora OPC em modo autónomo.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 4d4f9c90fd96365216480164f29f08fad92eb9d0
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787806"
---
# <a name="tutorial-configure-the-opc-publisher"></a>Tutorial: Configure a Editora OPC

Neste tutorial contém informações sobre a configuração do Editor OPC. Várias interfaces podem ser usadas para configurá-la.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure o Editor OPC através do Ficheiro de Configuração
> * Configure o Editor OPC através de argumentos de linha de comando
> * Configure o Editor OPC através de métodos diretos IoT Hub
> * Configure o Editor OPC através do microservice REST baseado na nuvem

## <a name="configuring-security"></a>Configuração de Segurança

O IoT Edge fornece ao OPC Publisher a sua configuração de segurança para aceder automaticamente ao IoT Hub. O OPC Publisher também pode funcionar como um recipiente Docker autónomo especificando uma cadeia de ligação do dispositivo para aceder ao Hub IoT através do `dc` parâmetro da linha de comando. Um dispositivo para ioT Hub pode ser criado e a sua cadeia de ligação recuperada através do portal Azure.

Para aceder aos ativos ativados pela OPC UA, os certificados X.509 e as suas chaves privadas associadas são utilizados pela OPC UA. Isto chama-se autenticação de aplicações OPC UA e além da autenticação do utilizador da OPC UA. A OPC Publisher utiliza uma loja de certificados baseada em sistemas de ficheiros para gerir todos os certificados de aplicação. Durante o arranque, a OPC Publisher verifica se existe um certificado que pode usar nestas lojas de certificados e cria um novo certificado auto-assinado e nova chave privada associada se não houver nenhum. Os certificados auto-assinados fornecem uma autenticação fraca, uma vez que não são assinados por uma Autoridade de Certificados de confiança, mas pelo menos a comunicação ao ativo ativado pela OPC UA pode ser encriptada desta forma.

A segurança está ativada no ficheiro de configuração através da `"UseSecurity": true,` bandeira. O ponto final mais seguro disponível nos servidores OPC UA a que o OPC Publisher deve ligar-se é automaticamente selecionado.
Por predefinição, a OPC Publisher utiliza a autenticação anónima do utilizador (adicional à autenticação da aplicação acima descrita). No entanto, o OPC Publisher também suporta a autenticação do utilizador utilizando o nome de utilizador e a palavra-passe. Isto pode ser especificado através da interface de configuração DA API REST (descrita abaixo) ou do ficheiro de configuração da seguinte forma:
```
"OpcAuthenticationMode": "UsernamePassword",
"OpcAuthenticationUsername": "usr",
"OpcAuthenticationPassword": "pwd",
```
Além disso, a versão 2.5 e abaixo da OPC Publisher encriptam o nome de utilizador e a palavra-passe no ficheiro de configuração. A versão 2.6 e acima suporta apenas o nome de utilizador e a palavra-passe em texto simples. Isto será melhorado na próxima versão da OPC Publisher.

Para persistir na configuração de segurança do OPC Publisher através de reinícios, o certificado e a chave privada localizada no diretório da loja de certificados devem ser mapeados para o sistema de ficheiros os anfitriões IoT Edge. Consulte "Especificar opções de criação de recipientes no portal Azure" acima.

## <a name="configuration-via-configuration-file"></a>Configuração via Ficheiro de Configuração

A forma mais simples de configurar o Editor OPC é através de um ficheiro de configuração. Um ficheiro de configuração de exemplo, bem como documentação sobre o seu formato é fornecido através do ficheiro [`publishednodes.json`](https://raw.githubusercontent.com/Azure/iot-edge-opc-publisher/master/opcpublisher/publishednodes.json) neste repositório.
A sintaxe de ficheiro de configuração mudou ao longo do tempo e a OPC Publisher ainda pode ler formatos antigos, mas converte-os no formato mais recente ao persistir na configuração, feito regularmente de forma automatizada.

Um ficheiro de configuração básica é assim:
```
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

Os ativos da OPC UA otimizam a largura de banda da rede enviando apenas alterações de dados para a OPC Publisher quando os dados foram alterados. Se as alterações de dados precisarem de ser publicadas com mais frequência ou a intervalos regulares, a OPC Publisher suporta um "batimento cardíaco" para cada item de dados configurado que pode ser ativado especificando adicionalmente a chave HeartbeatInterval na configuração do item de dados. O intervalo é especificado em segundos:
```
 "HeartbeatInterval": 3600,
```

Um ativo OPC UA envia sempre o valor atual de um item de dados quando a OPC Publisher se liga pela primeira vez ao mesmo. Para evitar a publicação destes dados no IoT Hub, a tecla SkipFirst pode ser especificada adicionalmente na configuração do item de dados:
```
 "SkipFirst": true,
```

Ambas as definições também podem ser ativadas globalmente através de opções de linha de comando.

## <a name="configuration-via-command-line-arguments"></a>Configuração através de argumentos de linha de comando

Existem vários argumentos de linha de comando que podem ser usados para definir configurações globais para o OPC Publisher. São descritos [aqui.](reference-command-line-arguments.md)


## <a name="configuration-via-the-built-in-opc-ua-server-interface"></a>Configuração através da interface incorporada do servidor UA UA

>[!NOTE] 
> Esta funcionalidade só está disponível na versão 2.5 e abaixo da OPC Publisher.**

A OPC Publisher tem um servidor OPC UA incorporado, em execução na porta 62222. Implementa três métodos OPC UA:

  - PublicarNode
  - Não-publicaNode
  - Obter Nomes Publicados

Esta interface pode ser acedida usando uma aplicação de cliente OPC UA, por exemplo [UA Expert](https://www.unified-automation.com/products/development-tools/uaexpert.html).

## <a name="configuration-via-iot-hub-direct-methods"></a>Configuração através de métodos diretos do hub IoT

>[!NOTE] 
> Esta funcionalidade só está disponível na versão 2.5 e abaixo da OPC Publisher.**

A OPC Publisher implementa os seguintes [métodos IoT Hub Direct Methods,](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-direct-methods)que podem ser chamados a partir de uma aplicação (de qualquer parte do mundo) aproveitando o [IoT Hub Device SDK:](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks)

  - PublicarNodes
  - Não-publicanteNodes
  - UnpublishAllNodes
  - Obter Pontos DeEndpoints Configurados
  - GetConfiguredNodesOnEndpoint
  - GetDiagnosticInfo
  - ObterDiagnosticLog
  - GetDiagnosticStartupLog
  - ExitApplication
  - GetInfo

Fornecemos uma [aplicação de configuração](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) de amostra, bem como uma [aplicação para leitura](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics) de informações de diagnóstico da OPC Publisher open-source, alavancando esta interface.

## <a name="configuration-via-cloud-based-companion-rest-microservice"></a>Configuração via Cloud-based, Companion REST Microservice

>[!NOTE] 
> Esta funcionalidade só está disponível na versão 2.6 ou acima da OPC Publisher.

Um microserviço de companhia baseado na nuvem com interface REST é descrito e disponível [aqui.](https://github.com/Azure/Industrial-IoT/blob/master/docs/services/publisher.md) Pode ser usado para configurar o Editor OPC através de uma interface compatível com OpenAPI, por exemplo através do Swagger.

## <a name="configuration-of-the-simple-json-telemetry-format-via-separate-configuration-file"></a>Configuração do formato de telemetria JSON simples através de Ficheiro de Configuração Separada

>[!NOTE] 
> Esta funcionalidade só está disponível na versão 2.5 e abaixo da OPC Publisher.

O OPC Publisher permite filtrar as partes do formato de telemetria simples e não padronizada através de um ficheiro de configuração separado, que pode ser especificado através da opção da linha de comando tc. Se não for especificado nenhum ficheiro de configuração, o formato completo de telemetria JSON é enviado para o IoT Hub. O formato do ficheiro de configuração de telemetria separado é descrito [aqui](reference-opc-publisher-telemetry-format.md#opc-publisher-telemetry-configuration-file-format).

## <a name="next-steps"></a>Passos seguintes
Agora que configuraste a Editora OPC, o próximo passo é aprender a sintonizar o desempenho e a memória do módulo Edge:

> [!div class="nextstepaction"]
> [Performance e Afinação da Memória](tutorial-publisher-performance-memory-tuning-opc-publisher.md)