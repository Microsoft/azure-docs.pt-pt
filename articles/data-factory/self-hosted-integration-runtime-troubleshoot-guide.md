---
title: Solucionar problemas de tempo de execução de integração auto-hospedado no Azure Data Factory
description: Saiba como solucionar problemas de tempo de execução de integração auto-hospedados no Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: 9adbc3d7d30aeb8c7cb2b89c326ac2b39a2e8d2b
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907285"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Solucionar problemas de tempo de execução de integração auto-hospedado

Este artigo explora métodos comuns de solução de problemas para o tempo de execução de integração auto-hospedado no Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Erros e resoluções comuns

### <a name="error-message-self-hosted-integration-runtime-is-unable-to-connect-to-cloud-service"></a>Mensagem de erro: o tempo de execução de integração auto-hospedado não pode se conectar ao serviço de nuvem.

- **Sintoma**: 

    ![Problema de conexão de IR via hospedagem interna](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

- **Causa**: o tempo de execução de integração auto-hospedado não é capaz de se conectar ao serviço de data Factory (backend). Geralmente, isso não é causado devido a configurações de rede no firewall.

- **Resolução**: 

    1. Verifique se o serviço do Windows "Integration Runtime serviço" está em execução.
    
        ![Status de execução do serviço IR auto-hospedado](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
    2. Se o serviço do Windows, conforme mostrado em [1], estiver em execução, siga as instruções abaixo, conforme apropriado:

        1. Se "proxy" não estiver configurado no tempo de execução de integração auto-hospedado (as configurações padrão não é nenhuma configuração de proxy), execute o comando do PowerShell abaixo no computador em que o tempo de execução de integração auto-hospedado está instalado: 
            
            ```powershell
            (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
            ```
            > [!NOTE] 
            > A URL do serviço pode variar com base em seu local de data factory. Você pode encontrar a URL do serviço em > conexões de interface do usuário do ADF-> tempos de execução de integração-> editar os nós de IR-> hospedados internamente-> exibir URLs de serviço.
            
            Abaixo está a resposta esperada:
            
            ![Resposta de comando do PowerShell](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
            Se a resposta for diferente, siga as instruções abaixo, conforme apropriado:
            
            * Se você receber o erro "o nome remoto não pôde ser resolvido", há um problema com o DNS. Entre em contato com a equipe de rede para obter o problema de resolução de DNS corrigido! 
            * Se você receber o erro "o certificado SSL/TLS não é confiável", verifique se o certificado para "https://wu2.frontend.clouddatahub.net/" é confiável no computador, instale o certificado público usando o Gerenciador de certificados, que deve atenuar esse problema.
            * Verificar o Visualizador de eventos de > do Windows (logs)-> logs de aplicativos e serviços-> Integration Runtime para qualquer falha, principalmente causada pelo DNS, pela regra de firewall e pelas configurações de rede da empresa (a conexão forçada). Para esse problema, entre em contato com sua equipe de rede para obter mais problemas, pois cada empresa tem configurações de rede personalizadas.

        2. Se "proxy" tiver sido configurado no tempo de execução de integração auto-hospedado, verifique se o servidor proxy é capaz de acessar nosso ponto de extremidade de serviço. Para obter um comando de exemplo, consulte [isso](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
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

            Abaixo está a resposta esperada:
            
            ![Resposta do comando do PowerShell 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

            > [!NOTE] 
            > Considerações sobre proxy:
            > * Verifique se o servidor proxy requer a lista de permissões. Nesse caso, tenha [esses domínios](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) na lista de permissões.
            > * Verifique se o certificado TLS/SSL para "wu2.frontend.clouddatahub.net/" é confiável no servidor proxy.
            > * Se você estiver usando a autenticação do Active Directory no proxy, altere a conta de serviço para a conta de usuário que pode acessar o proxy como "serviço Integration Runtime".

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Mensagem de erro: o nó do tempo de execução de integração auto-hospedado/SHIR lógico está no estado inativo/"em execução (limitado)"

- **Causa**: você pode ver o nó ir auto-hospedado no status inativo, conforme mostrado na captura de tela abaixo:

    ![Nó IR para hospedagem interna inativa](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

    Isso acontece quando os nós não conseguem se comunicar entre si. 

- **Resolução**: 

    Faça logon na VM do nó hospedado e abra o modo de exibição de eventos, nos logs de aplicativos e serviços-> Integration Runtime, filtre todos os logs de erros. 

     1. Se o log de erros contiver: 
    
        **Log de erros**: System. ServiceModel. EndpointNotFoundException: não foi possível conectar-se a net. TCP://XXXXXXX.bwld.com: 8060/ExternalService. svc/workermanager. A tentativa de conexão duração por um período de tempo de 00:00:00.9940994. Código de erro TCP 10061: não foi possível estabelecer conexão porque o computador de destino o recusou ativamente 10.2.4.10:8060.  ---> System .net. Sockets. SocketException: não foi possível estabelecer uma conexão porque o computador de destino o recusou ativamente 10.2.4.10:8060
    
           em System .net. Sockets. Socket. doConnect (ponto de extremidade endPointSnapshot, SocketAddress socketAddress)
           
           em System .net. Sockets. Socket. Connect (ponto de extremidade remoteEP)
           
           em System. ServiceModel. Channels. SocketConnectionInitiator. Connect (URI do URI, tempo limite de TimeSpan)
    
        **Solução:** inicie a linha de comando: telnet 10.2.4.10 8060
        
        Se você receber o erro abaixo, entre em contato com a equipe de ti para obter ajuda para corrigir esse problema. Depois que você puder fazer o telnet com êxito, entre em contato com o suporte da Microsoft se ainda tiver problemas para o status do nó IR.
        
        ![Erro de linha de comando](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
     2. Se o log de erros contiver:
     
        **Log de erros:** Não é possível se conectar ao Gerenciador de trabalho: net. TCP://xxxxxx: 8060/ExternalService. svc/não existem entradas DNS para azranlcir01r1 do host. Esse host não é um detalhe de exceção conhecido: System. ServiceModel. EndpointNotFoundException: não existem entradas DNS para o host xxxxx. ---> System .net. Sockets. SocketException: esse host não é conhecido em System .net. DNS. GetAddrInfo (String name) em System .net. DNS. InternalGetHostByName (cadeia de caracteres hostName, Boolean includeIPv6) em System .net. DNS. GetHostEntry (String hostNameOrAddress ) em System. ServiceModel. Channels. DnsCache. resolve (URI Uri)---fim do rastreamento de pilha de exceção interna---rastreamento de pilha do servidor: em System. ServiceModel. Channels. DnsCache. resolve (URI Uri) 
    
        **Solução:** Uma das duas ações abaixo pode ajudar a resolver o problema:
         1. Coloque todos os nós no mesmo domínio.
         2. Adicione o IP ao mapeamento de host em todos os arquivos hosts da VM hospedada.


## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda para solução de problemas, Experimente estes recursos:

*  [Blogue do Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory solicitações de recursos](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow Fórum para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guia de desempenho de fluxos de dados de mapeamento do ADF](concepts-data-flow-performance.md)
