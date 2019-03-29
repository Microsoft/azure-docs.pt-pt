---
title: Guia para instalar e implantar o agente Linux C do ASC para pré-visualização do IoT agente | Documentos da Microsoft
description: Saiba como instalar o ASC para o agente de IoT em Linux de 32 bits e 64 bits.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: mlottner
ms.openlocfilehash: 2b18a09e5b79e7b3d3ea837e937397ac92491f9f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619854"
---
# <a name="deploy-asc-for-iot-c-based-security-agent-for-linux"></a>Implementar o ASC para o agente de segurança baseada em C de IoT para Linux

> [!IMPORTANT]
> ASC para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este guia explica como instalar e implementar o ASC para o agente de segurança baseada em C do IoT no Linux.

Neste guia, ficará a saber como: 
> [!div class="checklist"]
> * Instalar
> * Verificar a implementação
> * Desinstalar o agente
> * Resolução de problemas 

## <a name="prerequisites"></a>Pré-requisitos

Para outras plataformas e tipos de agente, consulte [escolha o agente de segurança corretos](how-to-deploy-agent.md).

1. Para implementar o agente de segurança, os direitos de administrador local são necessários na máquina que pretende instalar em (sudo).

1. [Criar um módulo de segurança](quickstart-create-security-twin.md) para o dispositivo.

## <a name="installation"></a>Instalação 

Para instalar e implementar o agente de segurança, faça o seguinte:


1. Transferir a versão mais recente para o computador do [Github](https://aka.ms/iot-security-github-c).

1. Extraia o conteúdo do pacote e navegue para o _/Install_ pasta.

1. Adicionar permissões em execução para o **InstallSecurityAgent script** ao executar o seguinte:
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Em seguida, execute: 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   Ver [como configurar a autenticação](concept-security-agent-authentication-methods.md) para obter mais informações sobre parâmetros de autenticação.

Este script faz o seguinte:

1. Instala os pré-requisitos.

2. Adiciona um utilizador de serviço (com o início de sessão interativo desativado).

3. Instala o agente como um **Daemon** -assume que o dispositivo utiliza **systemd** para a gestão de serviço.

4. Configura o agente com os parâmetros de autenticação fornecidos. 

Para obter ajuda adicional, execute o script com o parâmetro – ajuda: 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>Desinstalar o agente

Para desinstalar o agente, execute o script com o –-desinstalar parâmetro:

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>Resolução de problemas
Verificar o estado de implementação ao executar:

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>Passos Seguintes
- Leia o ASC para o serviço de IoT [descrição geral](overview.md)
- Saiba mais sobre o ASC para IoT [arquitetura](architecture.md)
- Ativar o [serviço](quickstart-onboard-iot-hub.md)
- Leia o [FAQ](resources-frequently-asked-questions.md)
- Compreender [alertas de segurança](concept-security-alerts.md)