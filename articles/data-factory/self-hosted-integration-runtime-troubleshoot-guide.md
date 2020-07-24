---
title: Resolução de problemas de integração auto-acolagem na Azure Data Factory
description: Saiba como resolver problemas de integração auto-hospedados na Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 07/19/2020
ms.author: abnarain
ms.openlocfilehash: 521756081db938e749849e6f3630dbd60700d24f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87023891"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Resolução de problemas de integração auto-acolagem

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de resolução de problemas para o tempo de integração auto-hospedado na Azure Data Factory.

## <a name="gather-self-hosted-integration-runtime-logs-from-azure-data-factory"></a>Recolha registos de tempo de execução de integração auto-hospedados da Azure Data Factory

Para atividades falhadas em execução em IR /ID Partilhado, a Azure Data Factory suporta a visualização e o upload de registos de erros. Pode seguir os passos abaixo para obter a identificação do relatório de erro e, em seguida, inserir o ID do relatório para localizar questões conhecidas relacionadas.

1. Ir para a página **de execuções de atividades.**

1. Sob a coluna **ERROR,** clique abaixo do botão.

    ![A atividade executa a página](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

1. Verá registos relacionados para a execução da atividade falhada. Clique no botão **Enviar registos** para obter mais assistência.

    ![Enviar registos](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png)

1. Pode escolher registos que pretende enviar. Para *o IR auto-hospedado,* pode carregar registos relacionados com a atividade falhada ou todos os registos no nó IV auto-hospedado. Para *o IR Partilhado,* só é possível carregar registos relacionados com a atividade falhada.

    ![Escolha registos](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. Quando os registos estiverem a ser enviados, mantenha um registo do ID do relatório se precisar de mais assistência para resolver o problema.

    ![Carregar registos](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> Os pedidos de visualização e upload de registos serão executados em todas as instâncias ir auto-hospedadas online. Por favor, certifique-se de que todas as instâncias de IR auto-hospedadas estão on-line no caso de quaisquer registos em falta. 


## <a name="common-errors-and-resolutions"></a>Erros comuns e resoluções

### <a name="error-message"></a>Mensagem de erro: 

`Self-hosted integration runtime can't connect to cloud service`

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

### <a name="error-message"></a>Mensagem de erro: 
`Self-hosted integration runtime node/ logical SHIR is in Inactive/ "Running (Limited)" state`

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


## <a name="troubleshoot-connectivity-issue"></a>Problema de conectividade resolução de problemas

### <a name="troubleshoot-connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>Problema de conectividade de resolução de problemas entre a infra-acolagem de IR e data factory ou ir auto-hospedado e fonte de dados/pia

Para resolver o problema de conectividade da rede, deve saber [recolher os vestígios da rede,](#how-to-collect-netmon-trace)entender como usá-lo e [analisar o traço netmon](#how-to-analyze-netmon-trace) antes de aplicar as Ferramentas Netmon em casos reais a partir de IR auto-hospedado.

Às vezes, quando resoluçmos os problemas de conectividade, tais como abaixo de um entre o IR auto-hospedado e a Fábrica de Dados: 

![O pedido HTTP falhou](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Ou o que se encontra entre o IR auto-alojado e a fonte de dados/pia, que encontraremos os seguintes erros:

**Mensagem de erro:**
`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

**Mensagem de erro:**
`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

**Resolução:** Ao encontrar questões acima, consulte as seguintes instruções para resolver mais problemas:

Pegue o traço de netmon e analise mais.
- Em primeiro lugar, pode configurar o filtro para ver qualquer reset do servidor para o lado do cliente. A partir do exemplo abaixo, pode ver o lado do servidor é o servidor Data Factory.

    ![Servidor de fábrica de dados](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- Quando receber o pacote de reset, pode encontrar a conversa seguindo o TCP.

    ![Encontrar conversa](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- Em seguida, pode obter a conversão entre o servidor cliente e o servidor Data Factory abaixo, removendo o filtro.

    ![Obter conversa](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- Com base nos vestígios de netmon recolhidos, podemos dizer que o total de TTL (TimeToLive) é de 64. De acordo com os **Valores limite de TTL e hop predefinidos** mencionados [neste artigo](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) (como extraído abaixo), podemos ver que é o Sistema Linux que reinicia a embalagem e causa a desconexão.

    Os valores padrão de TTL e Limite de Salto variam entre diferentes sistemas operativos, aqui estão os padrão para alguns:
    - Kernel Linux 2.4 (cerca de 2001): 255 para TCP, UDP e ICMP
    - Kernel Linux 4.10 (2015): 64 para TCP, UDP e ICMP
    - Windows XP (2001): 128 para TCP, UDP e ICMP
    - Windows 10 (2015): 128 para TCP, UDP e ICMP
    - Windows Server 2008: 128 para TCP, UDP e ICMP
    - Windows Server 2019 (2018): 128 para TCP, UDP e ICMP
    - macOS (2001): 64 para TCP, UDP e ICMP

    ![TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    No entanto, é mostrado como 61 em vez de 64 em exemplo, porque quando o pacote de rede chega ao destino, precisa de passar por diferentes lúpulos como routers/dispositivos de rede. O número de routers/dispositivos de rede será deduzido na TTL final.
    Neste caso, podemos ver que o Reset pode ser enviado do Linux System com TTL 64.

- Temos de verificar o quarto salto do Auto-alojado IR para confirmar de onde o dispositivo de reset pode vir.
 
    *Pacote de rede do Linux System A com TTL 64 -> B TTL 64 Menos 1 = 63 -> C TTL 63 Menos 1 = 62 -> TTL 62 Menos 1 = 61 IR auto-hospedado*

- Na situação ideal, o TTL será 128, o que significa que o Windows System está a executar a nossa Fábrica de Dados. Como mostrado a seguir, *128 - 107 = 21 lúpulo*, o que significa que 21 lúpulos para o pacote foram enviados da Data Factory para o AUTO-hospedado IR durante o aperto de mão TCP 3.
 
    ![TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Por isso, tens de envolver a equipa de rede para verificar qual é o quarto salto do IR auto-hospedado. Se for a firewall como Sistema Linux, verifique quaisquer registos sobre o porquê desse dispositivo reiniciar a embalagem após o aperto de mão TCP 3. No entanto, se não tiver a certeza de onde fazer a investigação, tente obter os vestígios de netmon do IR e firewall auto-hospedados durante o tempo problemático para descobrir que dispositivo pode redefinir este pacote e causar a desconexão. Neste caso, também precisa de envolver a sua equipa de rede para seguir em frente.

### <a name="how-to-collect-netmon-trace"></a>Como recolher vestígios de netmon

1.  Descarregue as Ferramentas Netmon a partir [deste website](https://www.microsoft.com/en-sg/download/details.aspx?id=4865)e instale-as na sua Máquina de Servidor (qualquer que seja o servidor que tenha o problema) e no Cliente (como IR auto-hospedado).

2.  Criar uma pasta, por exemplo, no seguinte caminho: *D:\netmon*. Certifique-se de que tem espaço suficiente para guardar o tronco.

3.  Capture o IP e a Informação portuária. 
    1. Inicie uma solicitação CMD.
    2. Selecione Executar como administrador e executar o seguinte comando:
       
        ```
        Ipconfig /all >D:\netmon\IP.txt
        netstat -abno > D:\netmon\ServerNetstat.txt
        ```

4.  Capture o Traço Netmon (pacote de rede).
    1. Inicie uma solicitação CMD.
    2. Selecione Executar como administrador e executar o seguinte comando:
        
        ```
        cd C:\Program Files\Microsoft Network Monitor 3
        ```
    3. Pode utilizar três comandos diferentes para capturar a página da rede:
        - Opção A: Comando ficheiro RoundRobin (isto irá capturar apenas um ficheiro e substituir registos antigos).

            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.cap:200M
            ```         
        - Opção B: Comando ficheiro acorrentado (isto criará um novo ficheiro se forem alcançados 200 MB).
        
            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.chn:200M
            ```          
        - Opção C: Comando de ficheiro agendado.

            ```
            nmcap /network * /capture /StartWhen /Time 10:30:00 AM 10/28/2011 /StopWhen /Time 11:30:00 AM 10/28/2011 /file D:\netmon\ServerConnection.chn:200M
            ```  

5.  Pressione **Ctrl+C** para parar de capturar o vestígio de Netmon.
 
> [!NOTE]
> Se só conseguir recolher os vestígios de netmon na máquina do cliente, por favor obtenha o endereço IP do servidor para ajudá-lo a analisar os vestígios.

### <a name="how-to-analyze-netmon-trace"></a>Como analisar vestígios de netmon

Quando se tenta teletar **8.8.8.8.888** com vestígios de netmon acima recolhidos, é suposto ver abaixo o rastreio:

![traço netmon 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![traço netmon 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Isto significa que não foi possível fazer a ligação TCP ao lado do servidor **8.8.8.8** com base na porta **888**, pelo que vê aí dois pacotes adicionais **SynReTransmit.** Uma vez que a Fonte **SELF-HOST2** não conseguiu estabelecer ligação a **8.8.8.8** no primeiro pacote, continuará a fazer a ligação.

> [!TIP]
> - Pode clicar **Load Filter**em  ->  **endereços de filtro padrão de filtro**de carga  ->  **Addresses**  ->  **endereços IPv4**.
> - Inserir **IPv4.Endereço == 8.8.8.8** como filtro e clique em **Aplicar**. Depois disso, só verá a comunicação da máquina local para o destino **8.8.8.8**.

![endereços de filtro 1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![endereços de filtro 2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

Abaixo o exemplo mostra como seria um bom cenário. 

- Se a Telnet **8.8.8.8 53** estiver a funcionar bem sem qualquer problema, pode ver o aperto de mão TCP 3 acontecer, e a sessão termina com um aperto de mão TCP 4.

    ![bom exemplo de cenário 1](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![bom exemplo de cenário 2](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- Com base no aperto de mão TCP 3 acima, pode ver abaixo o fluxo de trabalho:

    ![Fluxo de trabalho de aperto de mão TCP 3](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- O aperto de mão TCP 4 para terminar a sessão e o seu fluxo de trabalho serão mostrados como seguintes:

    ![Aperto de mão TCP 4](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![Fluxo de trabalho de aperto de mão TCP 4](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda na resolução de problemas, experimente os seguintes recursos:

*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Pedidos de recursos da Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&Uma página de perguntas](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Stack overflow forum para data factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre a Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guia de desempenho de fluxos de dados de mapeamento](concepts-data-flow-performance.md)
