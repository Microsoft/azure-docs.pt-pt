---
title: Guia para instalar e implantar o C# agente do Linux da central de segurança do Azure para IOT | Microsoft Docs
description: Saiba como instalar a central de segurança do Azure para agente de IoT em Linux de 32 bits e 64 bits.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2019
ms.author: mlottner
ms.openlocfilehash: 0d77a1be2a3469282dabb646b02c43e350313ce5
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596295"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Implantar a central de segurança do C# Azure para o agente de segurança baseado em IOT para Linux


Este guia explica como instalar e implantar a central de segurança do Azure para C#o agente de segurança baseado em IOT no Linux.

Neste guia, ficará a saber como: 
> [!div class="checklist"]
> * Instalar
> * Verificar a implementação
> * Desinstalar o agente
> * Resolução de problemas 

## <a name="prerequisites"></a>Pré-requisitos

Para outras plataformas e tipos de agente, consulte [escolher o agente de segurança certo](how-to-deploy-agent.md).

1. Para implantar o agente de segurança, são necessários direitos de administrador local no computador em que você deseja instalar. 

1. [Crie um módulo de segurança](quickstart-create-security-twin.md) para o dispositivo.

## <a name="installation"></a>Instalação 

Para implantar o agente de segurança, use as seguintes etapas:

1. Baixe a versão mais recente em seu computador do [GitHub](https://aka.ms/iot-security-github-cs).

1. Extraia o conteúdo do pacote e navegue até a pasta _/install_ .

1. Adicionar permissões em execução ao **script InstallSecurityAgent** executando`chmod +x InstallSecurityAgent.sh` 

1. Em seguida, execute: 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   para obter mais informações sobre parâmetros de autenticação, consulte [como configurar a autenticação](concept-security-agent-authentication-methods.md).

Esse script executa as seguintes ações:

- Instala os pré-requisitos.

- Adiciona um usuário de serviço (com logon interativo desabilitado).

- Instala o agente como um **daemon** -presume que o dispositivo usa o modelo de implantação clássico do **sistema** .

- Configura os **sudoers** para permitir que o agente execute determinadas tarefas como raiz.

- Configura o agente com os parâmetros de autenticação fornecidos.


Para obter ajuda adicional, execute o script com o parâmetro – Help:`./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Desinstalar o agente

Para desinstalar o agente, execute o script com o parâmetro – u: `./InstallSecurityAgent.sh -u`. 

> [!NOTE]
> A desinstalação não remove os pré-requisitos ausentes que foram instalados durante a instalação.

## <a name="troubleshooting"></a>Resolução de problemas  

1. Verifique o status da implantação executando:

    `systemctl status ASCIoTAgent.service`

2. Habilite o registro em log.  
   Se o agente falhar ao iniciar, ative o registro em log para obter mais informações.

   Ative o registro em log por:

   1. Abra o arquivo de configuração para edição em qualquer editor do Linux:

        `vi /var/ASCIoTAgent/General.config`

   1. Edite os seguintes valores: 

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" /> 
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```
       O valor de **logFilePath** é configurável. 

       > [!NOTE]
       > É recomendável desativar o **logoff após a** conclusão da solução de problemas. Deixar o logon aumenta o tamanho **do** arquivo de log e o uso de dados.

   1. Reinicie o agente executando:

       `systemctl restart ASCIoTAgent.service`

   1. Exiba o arquivo de log para obter mais informações sobre a falha.  

       O local do arquivo de log é:`/var/ASCIoTAgent/IotAgentLog.log`

       Altere o caminho do local do arquivo de acordo com o nome escolhido para o **logFilePath** na etapa 2. 

## <a name="next-steps"></a>Passos Seguintes

- Leia a [visão geral](overview.md) da central de segurança do Azure para serviços de IOT
- Saiba mais sobre a [arquitetura](architecture.md) da central de segurança do Azure para IOT
- Habilitar o [serviço](quickstart-onboard-iot-hub.md)
- Leia as [perguntas frequentes](resources-frequently-asked-questions.md)
- Entender os [alertas](concept-security-alerts.md)