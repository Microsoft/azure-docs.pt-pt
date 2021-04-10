---
title: Instale o agente C# no dispositivo Windows
description: Saiba como instalar o Defender para agente IoT em dispositivos Windows de 32 ou 64 bits.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: b5efc9ce675fa068f378cdc2bdd5a077d3437a93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778768"
---
# <a name="deploy-a-defender-for-iot-c-based-security-agent-for-windows"></a>Implementar um Defensor para ioT C#agente de segurança para windows

Este guia explica como instalar o Defender para o agente de segurança baseado em IoT C#no Windows.

Neste guia, ficará a saber como:

- Instalar
- Verificar a implementação
- Desinstalar o agente
- Resolução de problemas

## <a name="prerequisites"></a>Pré-requisitos

Para outras plataformas e sabores de agente, consulte [Escolha o agente de segurança certo.](how-to-deploy-agent.md)

1. Direitos de administração locais sobre a máquina que pretende instalar.

1. [Crie um defender-ioT-micro-agente](quickstart-create-security-twin.md) para o dispositivo.

## <a name="installation"></a>Instalação

Para instalar o agente de segurança, utilize o seguinte fluxo de trabalho:

1. Instale o Defender para o agente IoT Windows C# no dispositivo. Descarregue a versão mais recente para a sua máquina do [repositório](https://github.com/Azure/Azure-IoT-Security-Agent-CS)IoT GitHub .

1. Extrair o conteúdo da embalagem e navegar para a pasta /Instalar.

1. Abrir o Windows PowerShell como administrador.
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

    Para obter mais informações sobre os parâmetros de autenticação, consulte [Como configurar a autenticação.](concept-security-agent-authentication-methods.md)

Este script faz as seguintes ações:

* Instala pré-requisitos.
* Adiciona um utilizador de serviço (com insusição de insusição interativa).
* Instala o agente como **um Serviço de Sistema**.
* Configura o agente com os parâmetros de autenticação fornecidos.

Para obter ajuda extra, utilize o comando Get-Help em PowerShell.

Get-Help exemplo:    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Verificar o estado de implantação

Verifique o estado de implantação do agente executando:

```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Desinstalar o agente

Para desinstalar o agente:

1. Executar o seguinte script PowerShell com o parâmetro **do modo definido** para **Desinstalar**.

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ```

## <a name="troubleshooting"></a>Resolução de problemas

Se o agente não iniciar, ligue o registo (a sessão está *desligada* por defeito) para obter mais informações.

Para ligar o registo:

1. Abra o ficheiro de configuração (General.config) para edição utilizando um editor de ficheiros padrão.

1. Editar os seguintes valores:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/>
   <add key="diagnosticVerbosityLevel" value="Some" />
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Recomendamos que desligue **o** registo após a resolução de problemas. Deixar o registo **no** aumento do tamanho do ficheiro de registo e do uso de dados.

1. Reinicie o agente executando a seguinte linha de comando PowerShell ou comando:

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

1. Reveja o ficheiro de registo para obter mais informações sobre a falha. O ficheiro de registo estaria presente no diretório de trabalho onde executamos o guião. 

   Localização do ficheiro de registo: `.\IoTAgentLog.log`

## <a name="next-steps"></a>Passos seguintes

* Leia o Defender para o serviço IoT [Visão geral](overview.md)
* Saiba mais sobre o Defender for IoT [Architecture](architecture.md)
* Ativar o [serviço](quickstart-onboard-iot-hub.md)
* Leia as [FAQ](resources-frequently-asked-questions.md)
* Compreender [alertas](concept-security-alerts.md)
