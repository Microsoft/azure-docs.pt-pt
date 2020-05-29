---
title: Resolução de problemas de integração auto-acolagem na Azure Data Factory
description: Saiba como resolver problemas de integração auto-hospedados na Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: f27132eb21d245d0d26de910abba088ba3b8efde
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84170980"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Resolução de problemas de integração auto-acolagem

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de resolução de problemas para o tempo de integração auto-hospedado na Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Erros comuns e resoluções

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Error message: O tempo de integração auto-hospedado não pode ligar-se ao serviço na nuvem

![Problema de conexão IV auto-hospedado](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Causa 

O tempo de integração auto-hospedado não pode ligar-se ao serviço Data Factory (backend). Este problema é normalmente causado por definições de rede na firewall.

#### <a name="resolution"></a>Resolução

1. Verifique se o serviço de execução de integração está em execução.
    
   ![Estado de funcionamento do serviço de IR auto-hospedado](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Se o serviço estiver funcionando, vá para o passo 3.

1. Se não houver procuração configurada no tempo de funcionamento da integração auto-hospedada (que é a definição padrão), executar o seguinte comando PowerShell na máquina onde o tempo de execução de integração auto-hospedado é instalado:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > O URL de serviço pode variar, dependendo da localização da sua Data Factory. Pode encontrar o URL de serviço em **ADF UI**  >  **Connections**  >  **Integrationtimes**  >  EditE URLs de Serviço de Ver Urls de serviço de imposições**auto-hospedadas**  >  **Nodes**  >  **View Service URLs**.
            
    Segue-se a resposta esperada:
            
    ![Resposta ao comando PowerShell](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Se não receber a resposta esperada, utilize um dos seguintes métodos adequados à sua situação:
            
    * Se receber uma mensagem "Nome remoto não pode ser resolvido", existe um problema do Sistema de Nome de Domínio (DNS). Contacte a sua equipa de rede para corrigir este problema.
    * Se receber uma mensagem "ssl/tls cert não é confiável", verifique se o certificado para https://wu2.frontend.clouddatahub.net/ se confiar na máquina e, em seguida, instale o certificado público utilizando o Certificate Manager. Esta ação deve atenuar a questão.
    * Aceda ao visualizador **do Windows**  >  **Event (registos)**  >  **Aplicações e serviços**  >  **Integração Desacurecida** e verifique se há falhas causadas por DNS, uma regra de firewall ou definições de rede da empresa. (Se encontrar tal falha, feche à força a ligação.) Como todas as empresas têm configurações de rede personalizadas, contacte a sua equipa de rede para resolver estes problemas.

1. Se o "proxy" tiver sido configurado no tempo de funcionação da integração auto-hospedada, verifique se o seu servidor proxy pode aceder ao ponto final do serviço. Para obter um comando de amostra, consulte [PowerShell, pedidos web e proxies](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
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
            
![Resposta ao comando Powershell 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Considerações de procuração:
> *    Verifique se o servidor proxy precisa de ser colocado na lista de destinatários seguros. Em caso afirmativo, certifique-se de que [estes domínios](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) estão na lista de destinatários seguros.
> *    Verifique se o certificado TLS/SSL "wu2.frontend.clouddatahub.net/" é fidedigno no servidor proxy.
> *    Se estiver a utilizar a autenticação ative directory no proxy, altere a conta de serviço para a conta de utilizador que possa aceder ao proxy como "Serviço de Tempo de Execução de Integração".

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Error message: Self-hosted integration runtime node/ logical SHIR is inative/ "Running (Limited)"

#### <a name="cause"></a>Causa 

O nó de tempo de execução integrado auto-hospedado pode ter um estado **inativo,** como mostra a seguinte imagem:

![Nó iv inativo auto-hospedado](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Este comportamento ocorre quando os nós não conseguem comunicar uns com os outros.

#### <a name="resolution"></a>Resolução

1. Faça login no VM hospedado no nó. No tempo de execução de integração de **aplicações e serviços,**  >  **Integration Runtime**abra o Visualizador de Eventos e filtre todos os registos de erro.

1. Verifique se um registo de erro contém o seguinte erro: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Se vir este erro, corram o seguinte numa linha de comando: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Se receber o seguinte erro, contacte o seu departamento de TI para obter ajuda para corrigir este problema. Depois de conseguir fazer telnet com sucesso, contacte o Microsoft Support se ainda tiver problemas com o estado do nó de tempo de execução integrador.
        
   ![Erro da linha de comando](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Verifique se o registo de erros contém o seguinte:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Para resolver a questão, experimente um ou ambos os seguintes métodos:
    - Coloque todos os nós no mesmo domínio.
    - Adicione o IP para hospedar o mapeamento em todos os ficheiros anfitriões do VM hospedado.


## <a name="next-steps"></a>Próximos passos

Para obter mais ajuda na resolução de problemas, experimente os seguintes recursos:

*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Pedidos de recursos da Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&Uma página de perguntas](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Stack overflow forum para data factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre a Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guia de desempenho de fluxos de dados de mapeamento](concepts-data-flow-performance.md)
