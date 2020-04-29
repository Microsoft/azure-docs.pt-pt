---
title: Tempo de integração auto-hospedado em Azure Data Factory
description: Saiba como resolver problemas de integração auto-hospedada problemas de tempo de funcionação na Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: f298b331d53eb8bab67a6f99194065dc5f889236
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414879"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Tempo de execução de integração auto-anfitriã

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de resolução de problemas para o tempo de execução da integração auto-hospedada na Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Erros comuns e resoluções

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Mensagem de erro: O tempo de execução de integração auto-hospedado não pode ligar-se ao serviço de nuvem

![Problema de conexão de INFRAVERMELHOS auto-hospedado](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Causa 

O tempo de execução de integração auto-hospedado não pode ligar-se ao serviço data Factory (backend). Este problema é normalmente causado por definições de rede na firewall.

#### <a name="resolution"></a>Resolução

1. Verifique se o serviço de tempo de funcionamento da integração está em execução.
    
   ![Estatuto de funcionamento do serviço de infravermelhos auto-hospedado](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Se o serviço estiver em execução, vá para o passo 3.

1. Se não houver procuração configurada no tempo de execução de integração auto-hospedado (que é a definição padrão), execute o seguinte comando PowerShell na máquina onde o tempo de execução de integração auto-hospedado é instalado:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > O URL de serviço pode variar, dependendo da localização da Sua Fábrica de Dados. Pode encontrar o URL de serviço em **Ligações ADF UI** > **Tempos** > de**integração** > Editar**urLs**de serviço de visualização de**nóes** > de > **infravermelhos auto-hospedados**.
            
    Segue-se a resposta esperada:
            
    ![Resposta de comando PowerShell](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Se não receber a resposta esperada, utilize um dos seguintes métodos conforme adequado à sua situação:
            
    * Se receber uma mensagem de "nome remoto não poderia ser resolvido", existe um problema do Sistema de Nome de Domínio (DNS). Contacte a sua equipa de rede para corrigir este problema.
    * Se receber uma mensagem "ssl/tls cert não é de https://wu2.frontend.clouddatahub.net/ confiança", verifique se o certificado é de confiança na máquina e, em seguida, instale o certificado público utilizando o Certificate Manager. Esta ação deve atenuar a questão.
    * Vá ao visualizador **do Windows** > **Event (logs)** > **Aplicações e Serviços Logs** > **Integração Prazo** de Integração e verifique se há qualquer falha causada por DNS, uma regra de firewall ou definições de rede da empresa. (Se encontrar tal falha, feche à força a ligação.) Como todas as empresas têm configurações de rede personalizadas, contacte a sua equipa de rede para resolver estes problemas.

1. Se o "proxy" tiver sido configurado no tempo de execução de integração auto-hospedado, verifique se o seu servidor proxy pode aceder ao ponto final do serviço. Para um comando de amostra, consulte [PowerShell, web requests e proxies](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
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

Segue-se a resposta esperada:
            
![Resposta de comando Powershell 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Considerações de procuração:
> *    Verifique se o servidor proxy precisa de ser colocado na lista de Destinatários Seguros. Em caso afirmativo, certifique-se de que [estes domínios](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) estão na lista de Destinatários Seguros.
> *    Verifique se o certificado TLS/SSL "wu2.frontend.clouddatahub.net/" é confiado no servidor proxy.
> *    Se estiver a utilizar a autenticação do Diretório Ativo no proxy, altere a conta de serviço para a conta de utilizador que pode aceder ao proxy como "Serviço de Tempo de Execução de Integração".

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Mensagem de erro: O nó de tempo de funcionamento da integração auto-hospedada/ sHIR lógico está em estado Inativo/ "Running (Limited)"

#### <a name="cause"></a>Causa 

O nó de tempo de execução integrado auto-hospedado pode ter um estatuto **inativo,** como mostra a seguinte imagem:

![Nó inativo auto-hospedado ir](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Este comportamento ocorre quando os nós não conseguem comunicar uns com os outros.

#### <a name="resolution"></a>Resolução

1. Inicie sessão no VM hospedado no nó. No âmbito de **Aplicações e Serviços,** > **integração**de registos, abra o Espectador de Eventos e filtre todos os registos de erro.

1. Verifique se um registo de erro contém o seguinte erro: 
    
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
        
1.    Check whether the error log contains the following:

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
