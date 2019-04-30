---
title: Selecione e implementar o Centro de segurança do Azure para pré-visualização do IoT agente | Documentos da Microsoft
description: Saiba mais sobre como selecionar e implementar o Centro de segurança do Azure para os agentes de segurança de IoT em dispositivos IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 8f5a6187b0a651da9dd8de1cb5670a8faffded1a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61358408"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Selecione e implementar um agente de segurança no seu dispositivo IoT

> [!IMPORTANT]
> Centro de segurança do Azure para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Centro de segurança do Azure (ASC) para IoT fornece arquiteturas de referência para os agentes de segurança que monitorizar e recolher dados de dispositivos IoT.
Ver [arquitetura de referência do agente de segurança](security-agent-architecture.md) para saber mais.

Agentes são desenvolvidas como projetos de código-fonte aberto e estão disponíveis dois tipos: <br> [C](https://aka.ms/iot-security-github-c), e [ C# ](https://aka.ms/iot-security-github-cs).

Neste artigo, vai aprender a: 
> [!div class="checklist"]
> * Comparar tipos de agente de segurança
> * Descubra as plataformas de agentes suportados
> * Escolha o sabor de agente correto para a sua solução

## <a name="understand-security-agent-options"></a>Compreender as opções de segurança do agente

Cada ASC para sabor de agente de segurança de IoT oferece o mesmo conjunto de recursos e suporta opções de configuração semelhante. 

O agente de segurança baseada em C tem um espaço de memória inferior e é a escolha ideal para dispositivos com menos recursos disponíveis. 

|     | Agente de segurança baseada em C | C#-com base em agente de segurança |
| --- | ----------- | --------- |
| Código fonte aberto | Disponível em [licença MIT](https://en.wikipedia.org/wiki/MIT_License) no [Github](https://aka.ms/iot-security-github-cs) | Disponível em [licença MIT](https://en.wikipedia.org/wiki/MIT_License) no [Github](https://aka.ms/iot-security-github-c) |
| Linguagem de desenvolvimento    | C | C# |
| Plataformas suportadas do Windows? | Não | Sim |
| Pré-requisitos do Windows | --- | [WMI](https://docs.microsoft.com/en-us/windows/desktop/wmisdk/) |
| Plataformas suportadas do Linux? | Sim, x64 e x86 | Sim, somente x64 |
| Pré-requisitos do Linux | libunwind8, libcurl3, tempo de execução do uuid, auditd, audispd-plug-ins | libunwind8, libcurl3, tempo de execução do uuid, auditd, audispd-plug-ins, sudo, netstat, iptables |
| Requisitos de espaço de disco | 10.5 MB | 90MB |
| Requisitos de espaço de memória (em média) | 5.5 MB | 33MB |
| [Autenticação](concept-security-agent-authentication-methods.md) ao IoT Hub | Sim | Sim |
| Dados de segurança [coleção](how-to-agent-configuration.md#supported-security-events) | Sim | Sim |
| Agregação de eventos | Sim | Sim |
| Configuração remota através de [duplo do módulo de segurança](concept-security-module.md) | Sim | Sim |


## <a name="choose-an-agent-flavor"></a>Escolha um tipo de agente 

Responda às seguintes perguntas sobre os seus dispositivos IoT para selecionar o agente correto:

- Está usando _Windows Server_ ou _Windows IoT Core_? 

    [Implementar um C#-com base em agente de segurança para Windows](how-to-deploy-windows-cs.md).

- Está a utilizar uma distribuição do Linux com o x86 arquitetura? 

    [Implementar um agente de segurança baseada em C para Linux](how-to-deploy-linux-c.md).

- Está a utilizar uma distribuição do Linux com o x64 arquitetura?

    Pode usar qualquer um dos sabor de agente. <br>
    [Implementar um agente de segurança baseada em C para Linux](how-to-deploy-linux-c.md) e/ou [implementar um C#-com base em agente de segurança para Linux](how-to-deploy-linux-cs.md).

Os dois tipos de agente oferecem o mesmo conjunto de funcionalidades e opções de configuração semelhante de suporte.
Ver [comparação de agente de segurança](how-to-deploy-agent.md#understand-security-agent-options) para saber mais.

## <a name="supported-platforms"></a>Plataformas suportadas

A lista seguinte inclui todas as plataformas atualmente suportadas.

|ASC para o agente de IoT |Sistema Operativo |Arquitetura |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   X64|
|C|Ubuntu 18.04 |   X64|
|C|Debian 9 |   x64, x86|
|C#|Ubuntu 16.04    |X64|
|C#|Ubuntu 18.04    |X64|
|C#|Debian 9    |X64|
|C#|Windows Server 2016|    X64|
|C#|Compilação do Windows 10 IoT Core 17763 |X64|

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as opções de configuração, avance para o guia de procedimentos de configuração do agente. 
> [!div class="nextstepaction"]
> [Configuração do agente como guia](./how-to-agent-configuration.md)
