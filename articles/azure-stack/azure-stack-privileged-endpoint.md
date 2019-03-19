---
title: Utilizar o ponto final com privilégios no Azure Stack | Documentos da Microsoft
description: Mostra como utilizar o ponto de final com privilégios (PEP) no Azure Stack (para um operador do Azure Stack).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 9eb2e8ddde13783eabf3d82173e6a2fa75ec2b06
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082675"
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>Utilizar o ponto final com privilégios no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Como um operador do Azure Stack, deve utilizar o portal de administrador, PowerShell ou APIs do Azure Resource Manager para as tarefas de gestão mais diárias. No entanto, para alguns, menos operações comuns, terá de utilizar o *ponto final com privilégios* (PEP). O PEP é uma consola do PowerShell remota pré-configuradas que lhe fornece apenas o suficiente capacidades para ajudar a executar uma tarefa necessária. O ponto de extremidade usa [JEA de PowerShell (administração Just Enough)](https://docs.microsoft.com/powershell/jea/overview) para expor um conjunto restrito de cmdlets. Para acessar o PEP e invocar o conjunto limitado de cmdlets, é utilizada uma conta de baixo privilégio. Não existem contas de administrador são necessárias. Para segurança adicional, criação de scripts não é permitida.

Pode utilizar o PEP para realizar tarefas como o seguinte:

- Para executar tarefas de nível inferior, como [recolher registos de diagnóstico](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool).
- Para executar muitas tarefas de integração de datacenter de pós-implementação para os sistemas integrados, por exemplo, adicionar reencaminhadores de sistema de nomes de domínio (DNS) após a implementação, configuração de integração do Microsoft Graph, integração de serviços de Federação do Active Directory (AD FS), rotação de certificados, etc.
- Para trabalhar com suporte para obter acesso temporário de alto nível para resolução de problemas detalhada de um sistema integrado.

O PEP e registos de cada ação (o resultado correspondente) que efetuar na sessão do PowerShell. Isso fornece a transparência completa e uma auditoria completa das operações. Pode manter estes ficheiros de registo para auditorias de futuras.

> [!NOTE]
> No Azure Stack Development Kit (ASDK), pode executar alguns dos comandos disponíveis no PEP diretamente a partir de uma sessão do PowerShell no anfitrião do kit de desenvolvimento. No entanto, talvez queira testar algumas operações usando PEP, tais como recolha de registos, porque este é o único método disponível para executar determinadas operações num ambiente de sistemas integrados.

## <a name="access-the-privileged-endpoint"></a>Aceder ao ponto final com privilégios

Aceder a PEP através de uma sessão remota do PowerShell na máquina virtual que aloja o PEP. ASDK, esta máquina virtual tem o nome **AzS-ERCS01**. Se estiver a utilizar um sistema integrado, há três instâncias de PEP, cada execução dentro de uma máquina virtual (*prefixo*-ERCS01, *prefixo*-ERCS02, ou *prefixo*- ERCS03) em anfitriões diferentes para resiliência. 

Antes de iniciar este procedimento para um sistema integrado, certifique-se de que pode acessar o PEP pelo endereço IP ou através de DNS. Após a implementação inicial do Azure Stack, pode acessar o PEP apenas por endereço IP porque a integração do DNS não estiver ainda configurada. O fornecedor do hardware de OEM irá fornecer-lhe com um ficheiro JSON denominado **AzureStackStampDeploymentInfo** que contém os endereços IP de PEP.


> [!NOTE]
> Por motivos de segurança, é necessário se ligar ao PEP apenas a partir de uma máquina de virtual protegida com sobre o anfitrião de ciclo de vida do hardware ou a partir de um computador dedicado e seguro, por exemplo, um [estação de trabalho de acesso privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations). A configuração original do anfitrião de ciclo de vida do hardware não deve ser modificada na sua configuração original, incluindo a instalação de software novo, nem deve ser usado para ligar para o PEP.

1. Estabelece a confiança.

    - Num sistema integrado, execute o seguinte comando a partir de uma sessão elevada do Windows PowerShell para adicionar o PEP como um anfitrião fidedigno na máquina virtual protegida em execução no anfitrião de ciclo de vida de hardware ou a estação de trabalho de acesso privilegiado.

      ```PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ```
    - Se estiver a executar o ASDK, inicie sessão para o anfitrião do kit de desenvolvimento.

2. A máquina virtual protegida em execução no anfitrião de ciclo de vida de hardware ou a estação de trabalho de acesso privilegiado, abra uma sessão do Windows PowerShell. Execute os seguintes comandos para estabelecer uma sessão remota na máquina virtual que aloja o PEP:
 
   - Num sistema integrado:
     ```PowerShell
       $cred = Get-Credential

       Enter-PSSession -ComputerName <IP_address_of_ERCS> `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ```
     O `ComputerName` parâmetro pode ser o endereço IP ou o nome DNS de uma das máquinas virtuais que aloja o PEP. 
   - Se estiver a executar o ASDK:
     
     ```PowerShell
       $cred = Get-Credential

       Enter-PSSession -ComputerName azs-ercs01 `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ``` 
     Quando lhe for pedido, utilize as seguintes credenciais:

     - **Nome de utilizador**: Especifique a conta de CloudAdmin, no formato  **&lt; *domínio do Azure Stack*&gt;\cloudadmin**. (Para ASDK, é o nome de utilizador **azurestack\cloudadmin**.)
     - **palavra-passe**: Introduza a mesma palavra-passe que foi fornecido durante a instalação para a conta de administrador de domínio AzureStackAdmin.

     > [!NOTE]
     > Se não é possível ligar ao ponto final do ERCS, tente passos uma e duas novamente com o endereço IP de uma VM de ERCS para que ainda não tiver já tentou estabelecer ligação.

3. Depois de ligar, a linha de comandos será alterado para **[*nome de endereço IP ou ERCS VM*]: PS >** ou a **[azs-ercs01]: PS >**, dependendo do ambiente. A partir daqui, executar `Get-Command` para ver a lista de cmdlets disponíveis.

   Muitos desses cmdlets destinam-se apenas de ambientes de sistema integrado (por exemplo, os cmdlets relacionados com a integração do Centro de dados). No ASDK, os seguintes cmdlets foram validados:

   - Clear-Host
   - Close-PrivilegedEndpoint
   - Exit-PSSession
   - Get-AzureStackLog
   - Get-AzureStackStampInformation
   - Get-Command
   - Get-FormatData
   - Get-Help
   - Get-ThirdPartyNotices
   - Objeto de medida
   - New-CloudAdminUser
   - Out-Default
   - Remove-CloudAdminUser
   - Select-Object
   - Set-CloudAdminUserPassword
   - Test-AzureStack
   - Stop-AzureStack
   - Get-ClusterLog

## <a name="tips-for-using-the-privileged-endpoint"></a>Dicas para usar o ponto final com privilégios 

Conforme mencionado acima, o PEP é um [PowerShell JEA](https://docs.microsoft.com/powershell/jea/overview) ponto final. Ao fornecer uma camada de segurança forte, um ponto final JEA reduz alguns recursos básicos do PowerShell, por exemplo, a conclusão de separador ou de script. Se tentar qualquer tipo de operação de script, a operação falhar com o erro **ScriptsNotAllowed**. Este comportamento está previsto.

Então, por exemplo, para obter a lista de parâmetros para um determinado cmdlet, execute o seguinte comando:

```PowerShell
    Get-Command <cmdlet_name> -Syntax
```

Em alternativa, pode utilizar o [Import-PSSession](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) cmdlet para importar todos os cmdlets do PEP para a sessão atual no seu computador local. Ao fazê-lo, todos os cmdlets e funções do PEP estão agora disponíveis no seu computador local, juntamente com a conclusão de tabulação e, mais em geral, criação de scripts. 

Para importar a sessão PEP no seu computador local, siga os passos abaixo:

1. Estabelece a confiança.

    -Num sistema integrado, execute o seguinte comando a partir de uma sessão elevada do Windows PowerShell para adicionar o PEP como um anfitrião fidedigno na máquina virtual protegida em execução no anfitrião de ciclo de vida de hardware ou a estação de trabalho de acesso privilegiado.

      ```PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ```
    - Se estiver a executar o ASDK, inicie sessão para o anfitrião do kit de desenvolvimento.

2. A máquina virtual protegida em execução no anfitrião de ciclo de vida de hardware ou a estação de trabalho de acesso privilegiado, abra uma sessão do Windows PowerShell. Execute os seguintes comandos para estabelecer uma sessão remota na máquina virtual que aloja o PEP:
 
   - Num sistema integrado:
     ```PowerShell
       $cred = Get-Credential

       $session = New-PSSession -ComputerName <IP_address_of_ERCS> `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ```
     O `ComputerName` parâmetro pode ser o endereço IP ou o nome DNS de uma das máquinas virtuais que aloja o PEP. 
   - Se estiver a executar o ASDK:
     
     ```PowerShell
      $cred = Get-Credential

      $session = New-PSSession -ComputerName azs-ercs01 `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ``` 
     Quando lhe for pedido, utilize as seguintes credenciais:

     - **Nome de utilizador**: Especifique a conta de CloudAdmin, no formato  **&lt; *domínio do Azure Stack*&gt;\cloudadmin**. (Para ASDK, é o nome de utilizador **azurestack\cloudadmin**.)
     - **palavra-passe**: Introduza a mesma palavra-passe que foi fornecido durante a instalação para a conta de administrador de domínio AzureStackAdmin.

3. Importar a sessão PEP para o seu computador local
    ```PowerShell 
        Import-PSSession $session
    ```
4. Agora, pode utilizar a conclusão de tabulação e fazer a criação de scripts como de costume em sua sessão do PowerShell local com todas as funções e cmdlets do PEP, sem reduzir a postura de segurança do Azure Stack. Divirta-se!


## <a name="close-the-privileged-endpoint-session"></a>Feche a sessão de ponto final com privilégios

 Como mencionado anteriormente, o PEP e registos de cada ação (o resultado correspondente) que efetuar na sessão do PowerShell. Tem de fechar a sessão utilizando o `Close-PrivilegedEndpoint` cmdlet. Este cmdlet corretamente fecha o ponto final e transfere os ficheiros de registo para uma partilha de ficheiro externo de retenção.

Para fechar a sessão de ponto final:

1. Crie uma partilha de ficheiro externo que seja acessível ao PEP. Num ambiente do kit de desenvolvimento, pode criar uma partilha de ficheiros no anfitrião do kit de desenvolvimento.
2. Execute o `Close-PrivilegedEndpoint` cmdlet. 
3. Lhe for pedido para um caminho no qual armazenar o ficheiro de registo de transcrição. Especifique a partilha de ficheiros que criou anteriormente, no formato &#92; &#92; *servername*&#92;*sharename*. Se não especificar um caminho, o cmdlet falhar e a sessão permanece aberta. 

    ![Resultado do cmdlet Close PrivilegedEndpoint que mostra onde especifica o caminho de destino de transcrição](media/azure-stack-privileged-endpoint/closeendpoint.png)

Depois dos ficheiros de registo de transcrição com êxito são transferidos para a partilha de ficheiros, estes são automaticamente eliminados do PEP. 

> [!NOTE]
> Se fechar a sessão PEP utilizando os cmdlets `Exit-PSSession` ou `Exit`, ou apenas fechar a consola do PowerShell, não se transferem os registos de transcrição para uma partilha de ficheiros. Elas permanecem no PEP. Da próxima vez que executar `Close-PrivilegedEndpoint` e incluir uma partilha de ficheiros, os registos de transcrição do anterior sessões também serão transferidos. Não utilize `Exit-PSSession` ou `Exit` para fechar a sessão PEP; utilize `Close-PrivilegedEndpoint` em vez disso.


## <a name="next-steps"></a>Passos Seguintes

[Ferramentas de diagnóstico do Azure Stack](azure-stack-diagnostics.md)
