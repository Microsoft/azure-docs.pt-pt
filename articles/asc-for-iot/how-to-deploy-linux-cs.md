---
title: Guia para instalar e implantar o Linux C# agente do Centro de segurança do Azure para pré-visualização do IoT | Documentos da Microsoft
description: Saiba como instalar o agente do IoT no Centro de segurança do Azure no Linux de 32 bits e 64 bits.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 4a53bcf77696d3aa53a4a404bfacd6f6d468885b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357898"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Implementar o Centro de segurança do Azure para IoT C#-com base em agente de segurança para Linux

> [!IMPORTANT]
> Centro de segurança do Azure para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este guia explica como instalar e implementar o Centro de segurança do Azure (ASC) para IoT C#-com base em agente de segurança no Linux.

Neste guia, ficará a saber como: 
> [!div class="checklist"]
> * Instalar
> * Verificar a implementação
> * Desinstalar o agente
> * Resolução de problemas 

## <a name="prerequisites"></a>Pré-requisitos

Para outras plataformas e tipos de agente, consulte [escolha o agente de segurança corretos](how-to-deploy-agent.md).

1. Para implementar o agente de segurança, os direitos de administrador local são necessários na máquina que pretende instalar em. 

1. [Criar um módulo de segurança](quickstart-create-security-twin.md) para o dispositivo.

## <a name="installation"></a>Instalação 

Para implementar o agente de segurança, faça o seguinte:

1. Transferir a versão mais recente para o computador do [Github](https://aka.ms/iot-security-github-cs).

1. Extraia o conteúdo do pacote e navegue para o _/Install_ pasta.

1. Adicionar permissões em execução para o **InstallSecurityAgent script** através da execução `chmod +x InstallSecurityAgent.sh` 

1. Em seguida, execute: 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   Ver [como configurar a autenticação](concept-security-agent-authentication-methods.md) para obter mais informações sobre parâmetros de autenticação.

Este script faz o seguinte:

- Instala os pré-requisitos.

- Adiciona um utilizador de serviço (com o início de sessão interativo desativado).

- Instala o agente como um **Daemon** -isso supõe que o dispositivo utiliza **systemd** para a gestão de serviço.

- Configura **sudoers** para permitir que o agente executar determinadas tarefas como raiz.

- Configura o agente com os parâmetros de autenticação fornecido.


Para obter ajuda adicional, execute o script com o parâmetro – ajuda: `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Desinstalar o agente

Para desinstalar o agente, execute o script com o parâmetro – u: `./InstallSecurityAgent.sh -u`. 

> [!NOTE]
> Desinstalação não remove os pré-requisitos em falta que foram instalados durante a instalação.

## <a name="troubleshooting"></a>Resolução de problemas  

1. Verificar o estado de implementação ao executar:

    `systemctl status ASCIoTAgent.service`

2. Ative o registo.  
   Se o agente não conseguir iniciar, ative o registo para obter mais informações.

   Ative o registo ao:

   1. Abra o ficheiro de configuração para edição em qualquer editor de Linux:

        `vi /var/ASCIoTAgent/General.config`

   1. Edite os seguintes valores: 

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" /> 
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```
       O **logFilePath** valor é configurável. 

       > [!NOTE]
       > Recomendamos que ativar registo **desativar** depois de concluída a resolução de problemas. Deixando o registo **no** aumentos de utilização de dados e o tamanho do ficheiro de registo.

   1. Reinicie o agente ao executar:

       `systemctl restart ASCIoTAgent.service`

   1. Ver o ficheiro de registo para obter mais informações sobre a falha.  

       Localização do ficheiro de registo é: `/var/ASCIoTAgent/IotAgentLog.log`

       Alterar o caminho de localização do ficheiro, de acordo com o nome que escolheu para o **logFilePath** no passo 2. 

## <a name="next-steps"></a>Passos Seguintes

- Leia o ASC para o serviço de IoT [descrição geral](overview.md)
- Saiba mais sobre o ASC para IoT [arquitetura](architecture.md)
- Ativar o [serviço](quickstart-onboard-iot-hub.md)
- Leia o [FAQ](resources-frequently-asked-questions.md)
- Compreender [alertas](concept-security-alerts.md)