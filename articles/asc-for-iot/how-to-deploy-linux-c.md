---
title: Guia para instalar e implantar o agente C do Linux da central de segurança do Azure para o agente de IoT | Microsoft Docs
description: Saiba como instalar a central de segurança do Azure para agente de IoT em Linux de 32 bits e 64 bits.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 3fd4287c6dd1cc42f419cfa6b252c1d276d1d5a5
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597236"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Implantar a central de segurança do Azure para o agente de segurança baseado em IoT C para Linux

Este guia explica como instalar e implantar a central de segurança do Azure para o agente de segurança baseado em IoT C no Linux.

Neste guia, ficará a saber como: 
> [!div class="checklist"]
> * Instalar
> * Verificar a implementação
> * Desinstalar o agente
> * Resolução de problemas 

## <a name="prerequisites"></a>Pré-requisitos

Para outras plataformas e tipos de agente, consulte [escolher o agente de segurança certo](how-to-deploy-agent.md).

1. Para implantar o agente de segurança, são necessários direitos de administrador local no computador em que você deseja instalar o (sudo).

1. [Crie um módulo de segurança](quickstart-create-security-twin.md) para o dispositivo.

## <a name="installation"></a>Instalação 

Para instalar e implantar o agente de segurança, use o seguinte fluxo de trabalho:


1. Baixe a versão mais recente em seu computador do [GitHub](https://aka.ms/iot-security-github-c).

1. Extraia o conteúdo do pacote e navegue até a pasta _/install_ .

1. Adicione permissões em execução ao **script InstallSecurityAgent** executando o seguinte comando:
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Em seguida, execute: 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   Consulte [como configurar a autenticação](concept-security-agent-authentication-methods.md) para obter mais informações sobre parâmetros de autenticação.

Esse script executa a seguinte função:

1. Instala os pré-requisitos.

2. Adiciona um usuário de serviço (com logon interativo desabilitado).

3. Instala o agente como um **daemon** -presume que o dispositivo usa **sistema** para gerenciamento de serviços.

4. Configura o agente com os parâmetros de autenticação fornecidos. 

Para obter ajuda adicional, execute o script com o parâmetro – Help: 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>Desinstalar o agente

Para desinstalar o agente, execute o script com o parâmetro –-Uninstall:

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>Resolução de problemas
Verifique o status da implantação executando:

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>Passos Seguintes
- Leia a [visão geral](overview.md) da central de segurança do Azure para serviços de IOT
- Saiba mais sobre a [arquitetura](architecture.md) da central de segurança do Azure para IOT
- Habilitar o [serviço](quickstart-onboard-iot-hub.md)
- Leia as [perguntas frequentes](resources-frequently-asked-questions.md)
- Entender os [alertas de segurança](concept-security-alerts.md)