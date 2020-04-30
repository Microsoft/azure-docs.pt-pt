---
title: Instale agente C# no dispositivo Windows
description: Saiba como instalar o Azure Security Center para o agente IoT em dispositivos Windows de 32 ou 64 bits.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 4d7d2f0a423a50f85160a856480eaa973be7e2b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537615"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Implementar um agente de segurança baseado em C# do Centro de Segurança do Azure para IoT para o Windows

Este guia explica como instalar o Centro de Segurança Azure para o agente de segurança baseado em IoT C#no Windows.

Neste guia, ficará a saber como:

> [!div class="checklist"]
> * Instalar
> * Verificar a implementação
> * Desinstalar o agente
> * Resolução de problemas

## <a name="prerequisites"></a>Pré-requisitos

Para outras plataformas e sabores de agente, consulte [Escolha o agente de segurança certo](how-to-deploy-agent.md).

1. Direitos de administração locais sobre a máquina que deseja instalar.

1. [Crie um módulo de segurança](quickstart-create-security-twin.md) para o dispositivo.

## <a name="installation"></a>Instalação

Para instalar o agente de segurança, utilize o seguinte fluxo de trabalho:

1. Instale o Centro de Segurança Azure para ioT Windows C# no dispositivo. Descarregue a versão mais recente para a sua máquina a partir do Centro de Segurança Azure para [o repositório](https://github.com/Azure/Azure-IoT-Security-Agent-CS)IoT GitHub .

1. Extrair o conteúdo da embalagem e navegar para a pasta /Instalar.

1. Abra o Windows PowerShell como Administrador.
1. Adicione permissões de execução ao script InstallSecurityAgent executando:

    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```

    em seguida, executar:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```

    Por exemplo:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```

    Para mais informações sobre os parâmetros de autenticação, consulte [Como configurar a autenticação](concept-security-agent-authentication-methods.md).

Este guião faz as seguintes ações:

* Instala pré-requisitos.
* Adiciona um utilizador de serviço (com sinal interativo em desativado).
* Instala o agente como serviço de **sistema.**
* Configura o agente com os parâmetros de autenticação fornecidos.

Para obter ajuda adicional, utilize o comando Get-Help no PowerShell.

Exemplo de get-help:```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Verificar o estado de implantação

Verifique o estado de implantação do agente executando:

```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Desinstalar o agente

Para desinstalar o agente:

1. Execute o seguinte script PowerShell com o parâmetro **do modo -modo** definido para **desinstalar**.

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ```

## <a name="troubleshooting"></a>Resolução de problemas

Se o agente não conseguir iniciar, ligue a exploração madeireira (a exploração está *desligada* por defeito) para obter mais informações.

Para ligar a exploração madeireira:

1. Abra o ficheiro de configuração (General.config) para edição utilizando um editor de ficheiros padrão.

1. Editar os seguintes valores:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/>
   <add key="diagnosticVerbosityLevel" value="Some" />
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Recomendamos que desligue **o** registo após a resolução de problemas. Deixar o registo **em** aumentos do tamanho do ficheiro de registo e utilização de dados.

1. Reiniciar o agente executando a seguinte PowerShell ou linha de comando:

    **Powershell**

     ```
     Restart-Service "ASC IoT Agent"
     ```

   ou

    **CMD**

     ```
     sc.exe stop "ASC IoT Agent"
     sc.exe start "ASC IoT Agent"
     ```

1. Reveja o ficheiro de registo para obter mais informações sobre a falha. O ficheiro de registo estaria presente no diretório de trabalho onde executamos o guião. 

   Localização do ficheiro de registo:`.\IoTAgentLog.log`

## <a name="next-steps"></a>Passos seguintes

* Leia o Centro de Segurança Azure para a [visão geral](overview.md) do serviço IoT
* Saiba mais sobre o Azure Security Center for IoT [Architecture](architecture.md)
* Ativar o [serviço](quickstart-onboard-iot-hub.md)
* Leia as [FAQ](resources-frequently-asked-questions.md)
* Compreender [alertas](concept-security-alerts.md)
