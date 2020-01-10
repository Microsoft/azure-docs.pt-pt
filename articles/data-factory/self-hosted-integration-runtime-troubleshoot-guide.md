---
title: Solucionar problemas de tempo de execução de integração auto-hospedado no Azure Data Factory
description: Saiba como solucionar problemas de tempo de execução de integração auto-hospedados no Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: b8492e8934c782451fb77d5a0ff56b96c34c9a00
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439876"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Solucionar problemas de tempo de execução de integração auto-hospedado

Este artigo explora métodos comuns de solução de problemas para o tempo de execução de integração auto-hospedado no Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Erros e resoluções comuns

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Mensagem de erro: o tempo de execução de integração auto-hospedado não pode se conectar ao serviço de nuvem

![Problema de conexão de IR via hospedagem interna](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Causa 

O tempo de execução de integração auto-hospedado não pode se conectar ao serviço de Data Factory (backend). Esse problema é normalmente causado pelas configurações de rede no firewall.

#### <a name="resolution"></a>Resolução

1. Verifique se o serviço de tempo de execução de integração está em execução.
    
   ![Status de execução do serviço IR auto-hospedado](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Se o serviço estiver em execução, vá para a etapa 3.

1. Se não houver nenhum proxy configurado no tempo de execução de integração auto-hospedado (que é a configuração padrão), execute o seguinte comando do PowerShell no computador em que o tempo de execução de integração auto-hospedado está instalado:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > A URL do serviço pode variar, dependendo de seu local de Data Factory. Você pode encontrar a URL do serviço **na interface do usuário do ADF** > **conexões** > **tempos de execução de integração** > editar os **nós** **de > ir hospedados internamente** > **exibir URLs de serviço**.
            
    A seguir está a resposta esperada:
            
    ![Resposta de comando do PowerShell](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Se você não receber a resposta esperada, use um dos seguintes métodos conforme apropriado para sua situação:
            
    * Se você receber uma mensagem "o nome remoto não pôde ser resolvido", há um problema de DNS (sistema de nomes de domínio). Entre em contato com sua equipe de rede para corrigir esse problema.
    * Se você receber uma mensagem "certificado SSL/TLS não confiável", verifique se o certificado para https://wu2.frontend.clouddatahub.net/ é confiável no computador e, em seguida, instale o certificado público usando o Gerenciador de certificados. Essa ação deve mitigar o problema.
    * Vá para o Visualizador de eventos do **Windows** >  **(logs)**  > **logs de aplicativos e serviços** > **Integration Runtime** e verifique se há falhas causadas pelo DNS, por uma regra de firewall ou por configurações de rede da empresa. (Se você encontrar uma falha, feche a conexão forçosamente.) Como cada empresa tem configurações de rede personalizadas, entre em contato com sua equipe de rede para solucionar esses problemas.

1. Se "proxy" tiver sido configurado no tempo de execução de integração auto-hospedado, verifique se o servidor proxy pode acessar o ponto de extremidade de serviço. Para obter um comando de exemplo, consulte [PowerShell, solicitações da Web e proxies](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

A seguir está a resposta esperada:
            
![Resposta do comando do PowerShell 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Considerações sobre proxy:
> * Verifique se o servidor proxy precisa ser colocado na lista de destinatários seguros. Nesse caso, verifique se [esses domínios](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) estão na lista de destinatários seguros.
> * Verifique se o certificado TLS/SSL "wu2.frontend.clouddatahub.net/" é confiável no servidor proxy.
> * Se você estiver usando Active Directory autenticação no proxy, altere a conta de serviço para a conta de usuário que pode acessar o proxy como "serviço Integration Runtime".

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Mensagem de erro: o nó do tempo de execução de integração auto-hospedado/SHIR lógico está no estado inativo/"em execução (limitado)"

#### <a name="cause"></a>Causa 

O nó de tempo de execução integrado auto-hospedado pode ter um status **inativo** , conforme mostrado na seguinte captura de tela:

![Nó IR para hospedagem interna inativa](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Esse comportamento ocorre quando os nós não podem se comunicar entre si.

#### <a name="resolution"></a>Resolução

1. Faça logon na VM hospedada em nó. Em **logs de aplicativos e serviços** > **Integration Runtime**, abra Visualizador de eventos e filtre todos os logs de erros.

1. Verifique se um log de erros contém o seguinte erro: 
    
    ```System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
        
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
               
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
               
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
       
1. If you see this error, run the following on a command line: 

   **telnet 10.2.4.10 8060**.
1. If you receive the following error, contact your IT department for help with fixing this issue. After you can successfully telnet, contact Microsoft Support if you still have issues with the integrative runtime node status.
        
   ![Command-line error](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1.  Check whether the error log contains the following:

    ```Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)```
    
1. To resolve the issue, try one or both of the following methods:
    - Put all the nodes in the same domain.
    - Add the IP to host mapping in all the hosted VM's host files.


## Next steps

For more help with troubleshooting, try the following resources:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory feature requests](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Mapping data flows performance guide](concepts-data-flow-performance.md)
