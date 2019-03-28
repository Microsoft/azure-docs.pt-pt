---
title: Instalação do Windows do ASC para pré-visualização do IoT agente | Documentos da Microsoft
description: Saiba mais sobre como instalar o ASC para agente de IoT em dispositivos Windows de 32 bits ou 64 bits.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.service: ascforiot
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: 0b1061cca03ab4465753e6c0b8d3341472007b9c
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541593"
---
# <a name="tutorial-deploy-asc-for-iot-c-based-security-agent-for-windows"></a>Tutorial: Implementar o ASC para IoT C#-com base em agente de segurança para Windows

> [!IMPORTANT]
> ASC para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este tutorial explica como instalar o ASC para IoT C#-com base em agente de segurança no Windows.

Neste tutorial, ficará a saber como: 
> [!div class="checklist"]
> * Instalar
> * Verificar a implementação
> * Desinstalar o agente
> * Resolução de problemas 

## <a name="prerequisites"></a>Pré-requisitos

Para outras plataformas e versões de agente, consulte [escolha o agente de segurança corretos](select-deploy-agent.md).

1. Para implementar o agente de segurança, os direitos de administrador local são necessários na máquina que pretende instalar em. 

1. [Criar um módulo de segurança](quickstart-create-security-twin.md) para o dispositivo.

## <a name="installation"></a>Instalação 

Para instalar o agente de segurança, faça o seguinte:

1. Para instalar o ASC para o agente do IoT no dispositivo, transferir a versão mais recente para o computador do ASC para o repositório do GitHub de IoT (... /.. / versões) pasta.

2. Extraia o conteúdo do pacote e navegue para a pasta de /Install.

3. Abra o Windows PowerShell. 
    1. Adicionar permissões em execução para o script de InstallSecurityAgent através da execução ```Unblock-File .\InstallSecurityAgent.ps1```
    
        e execute:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Ver [como configurar a autenticação](concept-security-agent-authentication-methods.md) para obter mais informações sobre parâmetros de autenticação.

Este script faz o seguinte:

- Instala os pré-requisitos.

- Adiciona um utilizador de serviço (com o início de sessão interativo desativado).

- Instala o agente como um **serviço de sistema**.

- Configura o agente com os parâmetros de autenticação fornecido.


Para obter ajuda adicional, utilize o comando Get-Help no PowerShell <br>Exemplo de Get-Help:  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Verificar o estado de implementação

- Verificar o estado de implementação de agente ao executar:<br>
    ```sc.exe query "ASC IoT Agent" ```

### <a name="uninstall-the-agent"></a>Desinstalar o agente

Para desinstalar o agente:

1. Execute o seguinte script do PowerShell com o **-modo** parâmetro definido como **desinstalação**.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>Resolução de problemas

Se o agente não conseguir iniciar, ativar o registo (o registo é *desativar* por predefinição) para obter mais informações.

Para ativar o registo:

1. Abra o ficheiro de configuração para edição, com um editor de arquivos padrão.

1. Edite os seguintes valores:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Recomendamos que ativar registo **desativar** depois de concluída a resolução de problemas. Deixando o registo **no** aumentos de utilização de dados e o tamanho do ficheiro de registo. 

1. Reinicie o agente ao executar o PowerShell ou a linha de comandos seguintes:

    **PowerShell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   ou

    **CMD**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. Reveja o ficheiro de registo para obter mais informações sobre a falha.

   Localização do ficheiro de registo: `<unpacked_agent_location>/IoTAgentLog.log`


## <a name="next-steps"></a>Passos Seguintes
- Leia o ASC para o serviço de IoT [descrição geral](overview.md)
- Saiba mais sobre o ASC para IoT [arquitetura](architecture.md)
- Ativar o [serviço](quickstart-onboard-iot-hub.md)
- Leia o [FAQ](resources-frequently-asked-questions.md)
- Compreender [alertas](concept-security-alerts.md)