---
title: Cache conectado da Microsoft dentro de uma borda IoT Azure para configuração de IoT industrial | Microsoft Docs
description: Cache conectado da Microsoft dentro de um Azure IoT Edge para tutorial de configuração de IoT industrial
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 0d70ed8b906c171c001c5bda81a79ca9b65febac
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665035"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-microsoft-connected-cache-within-an-azure-iot-edge-for-industrial-iot-configuration"></a>Amostra de cenário de pré-visualização da Cache conectada da Microsoft: Cache conectado do Microsoft dentro de uma borda IoT Azure para configuração de IoT industrial

As redes de fabrico são frequentemente organizadas em camadas hierárquicas seguindo o modelo de [rede Purdue](https://en.wikipedia.org/wiki/Purdue_Enterprise_Reference_Architecture) (incluído nos padrões [ISA 95](https://en.wikipedia.org/wiki/ANSI/ISA-95) e [ISA 99).](https://www.isa.org/standards-and-publications/isa-standards/isa-standards-committees/isa99) Nestas redes, apenas a camada superior tem conectividade com a nuvem e as camadas inferiores da hierarquia só podem comunicar com camadas adjacentes norte e sul.

Esta amostra GitHub, [Azure IoT Edge for Industrial IoT,](https://github.com/Azure-Samples/iot-edge-for-iiot)implementa o seguinte:

* Rede Purdue simulada em Azure
* Recursos industriais 
* Hierarquia dos portões Azure IoT Edge
  
Estes componentes serão utilizados para adquirir dados industriais e carregá-los de forma segura para a nuvem sem comprometer a segurança da rede. O Microsoft Connected Cache pode ser implementado para suportar o descarregamento de conteúdos em todos os níveis dentro da rede compatível com o ISA 95.

A chave para configurar as implementações da Cache conectada do Microsoft dentro de uma rede compatível com o ISA 95 está a configurar tanto o *proxy* OT como o anfitrião a montante no gateway L3 IoT Edge.

1. Configure as implementações da Cache conectada da Microsoft nos níveis L5 e L4, conforme descrito na amostra de gateway Two-Level Desmioido IoT Edge 
2. A implantação no gateway L3 IoT Edge deve especificar:
   
   * UPSTREAM_HOST - O IP/FQDN do gateway L4 IoT Edge, que a Cache L3 Microsoft Connected solicitará conteúdo.
   * UPSTREAM_PROXY - O IP/FQDN:PORT do servidor proxy OT.

3. O representante da OT deve adicionar o endereço L4 MCC FQDN/IP à lista de admissões.

Para validar que o Microsoft Connected Cache está a funcionar corretamente, execute o seguinte comando no terminal do dispositivo IoT Edge, hospedando o módulo ou qualquer dispositivo na rede.

```bash
    wget "http://<L3 IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```