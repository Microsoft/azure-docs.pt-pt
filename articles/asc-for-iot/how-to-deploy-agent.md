---
title: Selecione e implemente agentes de segurança
description: Saiba como selecionar e implementar o Azure Security Center para agentes de segurança IoT em dispositivos IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 4253e496aca4cbf71fab7ef0ee388b1356df5c16
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84456798"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Selecione e coloque um agente de segurança no seu dispositivo IoT

O Azure Security Center for IoT fornece arquiteturas de referência para agentes de segurança que monitorizam e recolhem dados de dispositivos IoT.
Para saber mais, consulte [a arquitetura de referência do agente de segurança.](security-agent-architecture.md)

Os agentes são desenvolvidos como projetos de código aberto, e estão disponíveis em dois sabores: <br> [C](https://aka.ms/iot-security-github-c), e [C#](https://aka.ms/iot-security-github-cs).

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Compare sabores de agente de segurança
> * Descubra plataformas de agentes apoiados
> * Escolha o sabor do agente certo para a sua solução

## <a name="understand-security-agent-options"></a>Compreender as opções do agente de segurança

Cada Centro de Segurança Azure para o sabor do agente de segurança IoT oferece o mesmo conjunto de funcionalidades, e suporta opções de configuração semelhantes.

O agente de segurança baseado em C tem uma pegada de memória mais baixa, e é a escolha ideal para dispositivos com menos recursos disponíveis.

|     | Agente de segurança baseado em C | Agente de segurança baseado em C# |
| --- | ----------- | --------- |
| Fonte aberta | Disponível sob [licença do MIT](https://en.wikipedia.org/wiki/MIT_License) no [GitHub](https://aka.ms/iot-security-github-c) | Disponível sob [licença do MIT](https://en.wikipedia.org/wiki/MIT_License) no [GitHub](https://aka.ms/iot-security-github-cs) |
| Linguagem de programação    | C | C# |
| Plataformas suportadas do Windows? | Não | Sim |
| Pré-requisitos do Windows | --- | [WMI](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| Apoiou plataformas Linux? | Sim, x64 e x86 | Sim, apenas x64 |
| Pré-requisitos do Linux | libunwind8, libcurl3, uuid-runtime, auditado, audispd-plugins | libunwind8, libcurl3, uuid-runtime, auditado, audispd-plugins, sudo, netstat, iptables |
| Pegada de disco | 10,5 MB | 90 MB |
| Pegada de memória (em média) | 5,5 MB | 33 MB |
| [Autenticação](concept-security-agent-authentication-methods.md) no IoT Hub | Sim | Sim |
| Recolha [de](how-to-agent-configuration.md#supported-security-events) dados de segurança | Sim | Sim |
| Agregação de eventos | Sim | Sim |
| Configuração remota através do [módulo de segurança twin](concept-security-module.md) | Sim | Sim |
|

## <a name="security-agent-installation-guidelines"></a>Diretrizes de instalação de agentes de segurança

Para **o Windows**: O script de instalação SecurityAgent.ps1 deve ser executado a partir de uma janela PowerShell do administrador.

Para **Linux:** O InstallSecurityAgent.sh deve ser executado como super-portador. Recomendamos a pré-fixação do comando de instalação com "sudo".

## <a name="choose-an-agent-flavor"></a>Escolha um sabor de agente

Responda às seguintes perguntas sobre os seus dispositivos IoT para selecionar o agente correto:

- Está a utilizar _o Windows Server_ ou o Windows _IoT Core?_

    [Implementar um agente de segurança baseado em C#para o Windows](how-to-deploy-windows-cs.md).

- Está a usar uma distribuição Linux com arquitetura x86?

    [Implementar um agente de segurança baseado em C para o Linux.](how-to-deploy-linux-c.md)

- Está a usar uma distribuição Linux com arquitetura x64?

    Ambos os sabores de agente podem ser usados. <br>
    [Implementar um agente de segurança baseado em C para linux](how-to-deploy-linux-c.md) e/ou [implementar um agente de segurança baseado em C#para o Linux](how-to-deploy-linux-cs.md).

Ambos os sabores do agente oferecem o mesmo conjunto de funcionalidades e suportam opções de configuração semelhantes.
Consulte a [comparação do agente de segurança](how-to-deploy-agent.md#understand-security-agent-options) para saber mais.

## <a name="supported-platforms"></a>Plataformas suportadas

A lista a seguir inclui todas as plataformas suportadas atualmente.

|Centro de Segurança Azure para agente IoT |Sistema Operativo |Arquitetura |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |    X64|
|C|Ubuntu 18.04 |    x64, ARMv7|
|C|Debian 9 |    x64, x86|
|C#|Ubuntu 16.04     |X64|
|C#|Ubuntu 18.04    |x64, ARMv7|
|C#|Debian 9    |X64|
|C#|Windows Server 2016|    x64|
|C#|Windows 10 IoT Core, construa 17763    |X64|
|

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre opções de configuração, continue a guiar como fazer para configurar o agente.
> [!div class="nextstepaction"]
> [Configuração do agente como guiar](./how-to-agent-configuration.md)
