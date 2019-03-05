---
title: Implementar o agente local | Documentos da Microsoft
description: Implemente o agente local para a validação de pilha do Azure como um serviço.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: c7bf766dc0f73b37fc7de537537c7a885a50c30a
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57339577"
---
# <a name="deploy-the-local-agent"></a>Implementar o agente local

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Saiba como utilizar a validação como um agente local do serviço (VaaS) para verificar se o seu hardware. O agente local tem de ser implementado na solução do Azure Stack que está a ser validada antes de executar testes de validação.

> [!Note]  
> Deve certificar-se de que a máquina em que o agente local está em execução não a perder o acesso de out-vinculado à internet. Esta máquina tem de estar acessível apenas aos utilizadores que têm autorização para utilizar VaaS em nome do seu inquilino.

Para implementar o agente local:

1. Instalar o agente local
2. Executar verificações de sanidade
3. Execute o agente local

## <a name="download-and-start-the-local-agent"></a>Transferir e iniciar o agente local

Transferi o agente para um computador que cumpra os pré-requisitos no Centro de dados que não faz parte do sistema do Azure Stack, mas que tem acesso a todos os pontos finais do Azure Stack.

### <a name="machine-prerequisites"></a>Pré-requisitos de máquina

Verifique se o seu computador cumpre os seguintes critérios:

- Acesso a todos os pontos de extremidade do Azure Stack
- .NET 4.6 e o PowerShell 5.0 instalado
- Pelo menos 8 GB de RAM
- Processadores de núcleo do mínimo de 8
- Espaço em disco mínimo de 200 GB
- Conectividade de rede estável à internet

O Azure Stack é o sistema em teste. A máquina não deve fazer parte do Azure Stack ou alojado na cloud do Azure Stack.

### <a name="download-and-install-the-agent"></a>Transfira e instale o agente

1. Abra o Windows PowerShell numa linha de elevada no computador que irá utilizar para executar os testes.
2. Execute o seguinte comando para transferir o agente local:

    ```PowerShell
    Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.latest.nupkg" -outfile "OnPremAgent.zip"
    Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent -Force
    Set-Location VaaSOnPremAgent\lib\net46
    ```

3. Execute o seguinte comando para instalar as dependências do agente local:

    ```PowerShell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

    **Parâmetros**

    | Parâmetro | Descrição |
    | --- | --- |
    | aadServiceAdminUser | O utilizador de administrador global do seu inquilino do Azure AD. Por exemplo pode ser vaasadmin@contoso.onmicrosoft.com. |
    | aadServiceAdminPassword | A palavra-passe para o utilizador de administrador global. |
    | AadTenantId | ID de inquilino do Azure AD para a conta do Azure registado com a validação como um serviço. |
    | ExternalFqdn | Pode obter o nome de domínio completamente qualificado do arquivo de configuração. Para obter instruções, consulte [parâmetros comuns do fluxo de trabalho na validação de pilha do Azure como um serviço](azure-stack-vaas-parameters.md). |
    | Região | A região do seu inquilino do Azure AD. |

O comando transfere uma imagem de repositório (PIR) de imagem pública (VHD do SO) e a cópia de um armazenamento de Blobs do Azure para o armazenamento do Azure Stack.

![Transferir a pré-requisitos](media/installingprereqs.png)

> [!Note]
> Se estiver a ter baixa velocidade de rede ao transferir estas imagens, transferi-los separadamente para uma partilha local e especificar o parâmetro **- LocalPackagePath** *FileShareOrLocalPath*. Pode encontrar mais orientações sobre seu download do PIR na secção [identificador de conectividade de rede lenta](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) dos [resolver problemas de validação como um serviço](azure-stack-vaas-troubleshoot.md).

## <a name="checks-before-starting-the-tests"></a>Verificações antes de iniciar os testes

Os testes de executam ações remotas. A máquina que executa os testes têm de ter acesso aos pontos finais do Azure Stack, caso contrário, os testes não funcionará. Se estiver a utilizar o agente local VaaS, utilize a máquina em que o agente será executado. Pode verificar que o seu computador tem acesso aos pontos finais do Azure Stack, executando as seguintes verificações:

1. Verifique se o URI de Base pode ser contatado. Abra uma linha de comandos ou shell do bash e execute o seguinte comando, substituindo `<EXTERNALFQDN>` com o FQDN externo do seu ambiente:

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Abra um browser e aceda a `https://adminportal.<EXTERNALFQDN>` para verificar que o Portal de MAS pode ser contactado.

3. Valores de nome e palavra-passe de administrador indicou quando criou a aprovação de teste do serviço de início de sessão com o Azure AD.

4. Verificar o estado de funcionamento do sistema ao executar o **teste AzureStack** cmdlet do PowerShell conforme descrito na [executar um teste de validação para o Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test). Corrija todos os avisos e erros antes de iniciar qualquer teste.

## <a name="run-the-agent"></a>Execute o agente

1. Abra o Windows PowerShell numa linha de comandos elevada.

2. Execute o seguinte comando:

    ```PowerShell
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ```

      **Parâmetros**  
    | Parâmetro | Descrição |
    | --- | --- |
    | VaaSUserId | ID de utilizador utilizada para iniciar sessão Portal do VaaS (por exemplo, UserName@Contoso.com) |
    | VaaSTenantId | ID de inquilino do Azure AD para a conta do Azure registado com a validação como um serviço. |

    > [!Note]  
    > Quando executa o agente, o atual diretório de trabalho tem de ser a localização do anfitrião de motor de tarefa executável, **Microsoft.VaaSOnPrem.TaskEngineHost.exe.**

Se não vir os erros relatados, em seguida, o agente local foi concluída com êxito. O texto de exemplo seguinte é apresentada na janela da consola.

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![Introdução ao agente](media/startedagent.png)

Um agente é identificado exclusivamente pelo respetivo nome. Por predefinição, utiliza o nome de nome (FQDN) de domínio completamente qualificado da máquina do onde foi iniciado. Deve minimizar a janela para evitar qualquer seleciona acidentais na janela de como alterar o foco interrompe todas as outras ações.

## <a name="next-steps"></a>Passos Seguintes

- [Resolver problemas de validação como um serviço](azure-stack-vaas-troubleshoot.md)
- [Validação como um conceito de principais de serviço](azure-stack-vaas-key-concepts.md)
- [Quickstart: Utilize a validação como um portal de serviço para agendar o seu primeiro teste](azure-stack-vaas-schedule-test-pass.md)