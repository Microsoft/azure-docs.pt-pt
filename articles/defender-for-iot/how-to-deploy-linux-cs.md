---
title: Instale & implementar o agente Linux C#
description: Saiba como instalar e implementar o Defender para o agente de segurança baseado em IoT C#no Linux
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: f2251ef79f568677d6ef09d918cb61ffeba07888
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103494551"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>Implementar o Defender para o agente de segurança baseado em IoT C# para o Linux

Este guia explica como instalar e implantar o Defender para o agente de segurança baseado em IoT C#no Linux.

Neste guia, ficará a saber como:

- Instalar
- Verificar a implementação
- Desinstalar o agente
- Resolução de problemas

## <a name="prerequisites"></a>Pré-requisitos

Para outras plataformas e sabores de agente, consulte [Escolha o agente de segurança certo.](how-to-deploy-agent.md)

1. Para implantar o agente de segurança, são necessários direitos de administração locais na máquina que pretende instalar.

1. [Crie um defender-ioT-micro-agente](quickstart-create-security-twin.md) para o dispositivo.

## <a name="installation"></a>Instalação

Para implantar o agente de segurança, utilize os seguintes passos:

1. Descarregue a versão mais recente para a sua máquina a partir do [GitHub.](https://aka.ms/iot-security-github-cs)

1. Extrair o conteúdo da embalagem e navegar para a pasta _/Instalar._

1. Adicione permissões de execução ao **script InstallSecurityAgent** executando `chmod +x InstallSecurityAgent.sh`

1. Em seguida, executar o seguinte comando com **privilégios de raiz**:

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```

   para obter mais informações sobre parâmetros de autenticação, consulte [Como configurar a autenticação.](concept-security-agent-authentication-methods.md)

Este script executa as seguintes ações:

- Instala pré-requisitos.

- Adiciona um utilizador de serviço (com sinal interativo em desativado).

- Instala o agente como Um **Daemon** - assume que o dispositivo utiliza **sistema para** modelo de implementação clássico.

- Configura **sudoers** para permitir que o agente faça certas tarefas como raiz.

- Configura o agente com os parâmetros de autenticação fornecidos.

Para obter ajuda adicional, execute o script com o parâmetro de ajuda: `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Desinstalar o agente

Para desinstalar o agente, execute o script com o parâmetro -u: `./InstallSecurityAgent.sh -u` .

> [!NOTE]
> Desinstalar não remove quaisquer pré-requisitos em falta que foram instalados durante a instalação.

## <a name="troubleshooting"></a>Resolução de problemas

1. Verifique o estado de implantação executando:

    `systemctl status ASCIoTAgent.service`

1. Ativar o registo.
   Se o agente não iniciar, ligue o registo para obter mais informações.

   Ligue o registo por:

   1. Abra o ficheiro de configuração para edição em qualquer editor do Linux:

        `vi /var/ASCIoTAgent/General.config`

   1. Editar os seguintes valores:

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" />
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```

       O **valor logFilePath** é configurável.

       > [!NOTE]
       > Recomendamos que desligue **o** registo após a resolução de problemas. Deixar o registo **no** aumento do tamanho do ficheiro de registo e do uso de dados.

   1. Reinicie o agente executando:

       `systemctl restart ASCIoTAgent.service`

   1. Consulte o ficheiro de registo para obter mais informações sobre a falha.

       A localização do ficheiro de registo é: `/var/ASCIoTAgent/IotAgentLog.log`

       Altere o caminho de localização do ficheiro de acordo com o nome que escolheu para o **logFilePath** no passo 2.

## <a name="next-steps"></a>Passos seguintes

- Leia o Defender para o serviço IoT [Visão geral](overview.md)
- Saiba mais sobre o Defender for IoT [Architecture](architecture.md)
- Ativar o [serviço](quickstart-onboard-iot-hub.md)
- Leia as [FAQ](resources-frequently-asked-questions.md)
- Compreender [alertas](concept-security-alerts.md)
