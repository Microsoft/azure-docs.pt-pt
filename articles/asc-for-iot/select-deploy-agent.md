---
title: Selecione e implementar um ASC para pré-visualização do IoT agente | Documentos da Microsoft
description: Saiba mais sobre como selecionar e implementar o ASC para agentes de segurança de IoT em dispositivos IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 5ce583fe98acb7e0a4aaeb720cb2d5ed5eebb9e3
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541966"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Selecione e implementar um agente de segurança no seu dispositivo IoT

> [!IMPORTANT]
> ASC para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


ASC para IoT fornece arquiteturas de referência para os agentes de segurança que monitorizar e recolher dados de dispositivos IoT.

Agentes de segurança são desenvolvidas como projetos de código-fonte aberto e estão disponíveis dois tipos: <br> [C](https://aka.ms/iot-security-github-c), e [ C# ](https://aka.ms/iot-security-github-cs).

## <a name="supported-platforms-for-asc-for-iot-agents"></a>Plataformas suportadas para o ASC para os agentes de IoT

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


## <a name="which-flavor-should-i-use"></a>Que versão devo utilizar?

Considere as seguintes perguntas em relação aos seus dispositivos IoT:

- Está usando _Windows Server_ ou _Windows IoT Core_? 

    Uso [ASC para instalação de IoT para Windows com o C#-agente de segurança baseado no](tutorial-deploy-windows-cs.md).

- Está a utilizar uma distribuição do Linux com o x86 arquitetura? 

    Uso [ASC para a instalação de IoT para Linux com o agente de segurança baseada em C](tutorial-deploy-linux-c.md).
- Está a utilizar uma distribuição do Linux com o x64 arquitetura?

    Pode usar os dois tipos. <br>
    [ASC para a instalação de IoT para Linux com o agente de segurança baseada em C](tutorial-deploy-linux-c.md) e/ou [ASC para instalação de IoT para Linux com o C#-agente de segurança baseado no](tutorial-deploy-linux-cs.md).

Os dois tipos tem o mesmo conjunto de funcionalidades e suportam as opções de configuração semelhante. O agente de segurança baseada em C tem um espaço de memória inferior.


## <a name="next-steps"></a>Passos Seguintes
- [Descrição geral](overview.md)
- [Agentes de segurança](security-agent-architecture.md)
- [Métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md)
- [ASC para IoT FAQ](resources-frequently-asked-questions.md)