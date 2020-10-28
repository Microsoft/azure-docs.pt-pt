---
title: Trabalhar com servidores proxy existentes no local e diretório ativo Azure
description: Cobre como trabalhar com servidores proxy existentes no local com o Azure Ative Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/07/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperfq2
ms.openlocfilehash: 81a735966b2a0ebdd7c8fcd9e9aa467d68aac354
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792757"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Trabalhar com servidores proxy existentes no local

Este artigo explica como configurar o Azure Ative Directory (Azure AD) Os conectores de procuração de aplicação para trabalhar com servidores de procuração de saída. Destina-se a clientes com ambientes de rede que tenham proxies existentes.

Começamos por olhar para estes principais cenários de implantação:

* Configure os conectores para contornar os seus proxies de saída no local.
* Configure os conectores para usar um representante de saída para aceder ao Azure AD Application Proxy.
* Configure utilizando um proxy entre o conector e a aplicação backend.

Para obter mais informações sobre como funcionam os conectores, consulte [os conectores Proxy da aplicação AD Azure.](application-proxy-connectors.md)

## <a name="bypass-outbound-proxies"></a>Bypass proxies de saída

Os conectores têm componentes de SO subjacentes que fazem pedidos de saída. Estes componentes tentam automaticamente localizar um servidor proxy na rede utilizando o Web Proxy Auto-Discovery (WPAD).

Os componentes de SO tentam localizar um servidor proxy realizando uma procura de DNS para o sufixo de wpad.domains. Se a procura se resolver em DNS, é então feito um pedido HTTP para o endereço IP para wpad.dat. Este pedido torna-se o script de configuração proxy no seu ambiente. O conector utiliza este script para selecionar um servidor de procuração de saída. No entanto, o tráfego do conector pode ainda não passar, devido às definições de configuração adicionais necessárias no proxy.

Pode configurar o conector para contornar o seu representante no local para garantir que utiliza conectividade direta aos serviços Azure. Recomendamos esta abordagem, desde que a sua política de rede o permita, porque significa que tem menos uma configuração para manter.

Para desativar a utilização de procuração de saída para o conector, edite o ficheiro C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config e adicione a secção *system.net* mostrada nesta amostra de código:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>
    <defaultProxy enabled="false"></defaultProxy>
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Para garantir que o serviço Connector Updater também ignora o proxy, faça uma alteração semelhante ao ficheiro ApplicationProxyConnectorUpdaterService.exe.config. Este ficheiro está localizado em C:\Program Files\Microsoft AAD App Proxy Connector Updater.

Certifique-se de fazer cópias dos ficheiros originais, caso precise de reverter para os ficheiros padrão .config.

## <a name="use-the-outbound-proxy-server"></a>Use o servidor de procuração de saída

Alguns ambientes exigem que todo o tráfego de saída passe por um representante de saída, sem exceção. Como resultado, contornar o proxy não é uma opção.

Pode configurar o tráfego do conector para passar pelo representante de saída, como mostra o seguinte diagrama:

 ![Configurar o tráfego do conector para passar por um representante de saída para a Azure AD Application Proxy](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

Como resultado de ter apenas tráfego de saída, não há necessidade de configurar o acesso de entrada através das suas firewalls.

> [!NOTE]
> Application Proxy não suporta autenticação a outros proxies. As contas de serviço de rede de conector/atualizador devem poder ligar-se ao representante sem serem desafiadas para a autenticação.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Passo 1: Configurar o conector e os serviços conexos para passar pelo representante de saída

Se o WPAD estiver ativado no ambiente e configurado adequadamente, o conector descobre automaticamente o servidor de procuração de saída e tenta utilizá-lo. No entanto, pode configurar explicitamente o conector para passar por um representante de saída.

Para tal, edite o ficheiro C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config e adicione a secção *system.net* mostrada nesta amostra de código. Altere *o proxyserver:8080* para refletir o nome do servidor de procuração local ou endereço IP, e a porta que está a ouvir. O valor deve ter o prefixo http:// mesmo que esteja a utilizar um endereço IP.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Em seguida, configurar o serviço De Atualização do Conector para utilizar o proxy, fazendo uma alteração semelhante ao ficheiro C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config ficheiro.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Passo 2: Configurar o representante para permitir que o tráfego do conector e serviços conexos fluam através

Há quatro aspetos a considerar no representante de saída:

* Regras de saída de procuração
* Autenticação por procuração
* Portas proxy
* Inspeção TLS

#### <a name="proxy-outbound-rules"></a>Regras de saída de procuração

Permitir o acesso aos seguintes URLs:

| URL | Porta |  Como é usado |
| --- | --- | --- |
| &ast;.msappproxy.net<br>&ast;.servicebus.windows.net | 443/HTTPS | Comunicação entre o conector e o serviço de nuvem Proxy de aplicação |
| crl3.digicert.com<br>crl4.digicert.com<br>ocsp.digicert.com<br>crl.microsoft.com<br>oneocsp.microsoft.com<br>ocsp.msocsp.com<br> | 80/HTTP | O conector utiliza estes URLs para verificar os certificados. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>&ast;.microsoftonline.com<br>&ast;.microsoftonline-p.com<br>&ast;.msauth.net<br>&ast;.msauthimages.net<br>&ast;.msecnd.net<br>&ast;.msftauth.net<br>&ast;.msftauthimages.net<br>&ast;.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctldl.windowsupdate.com | 443/HTTPS | O conector utiliza estes URLs durante o processo de registo. |
| ctldl.windowsupdate.com | 80/HTTP | O conector utiliza este URL durante o processo de registo. |

Se a sua firewall ou proxy permitir configurar listas de autorizações DNS, pode permitir ligações a \* .msappproxy.net e \* .servicebus.windows.net.

Se não conseguir permitir a conectividade pela FQDN e precisar de especificar os intervalos IP, utilize estas opções:

* Permitir o acesso de saída do conector a todos os destinos.
* Permitir o acesso de saída do conector a todas as gamas IP do datacenter Azure. O desafio com a utilização da lista de gamas IP do datacenter Azure é que é atualizado semanalmente. É necessário implementar um processo para garantir que as suas regras de acesso sejam atualizadas em conformidade. Apenas a utilização de um subconjunto dos endereços IP pode fazer com que a sua configuração se parta. Para descarregar as mais recentes gamas IP do Azure Data Center, navegue [https://download.microsoft.com](https://download.microsoft.com) e procure "Azure IP Ranges and Service Tags". Certifique-se de selecionar a nuvem relevante. Por exemplo, as gamas IP em nuvem pública podem ser encontradas com "Azure IP Ranges and Service Tags – Public Cloud". A nuvem do Governo dos EUA pode ser encontrada procurando "Azure IP Ranges and Service Tags – US Government Cloud".

#### <a name="proxy-authentication"></a>Autenticação por procuração

A autenticação por procuração não é suportada atualmente. A nossa recomendação atual é permitir o acesso anónimo do conector aos destinos da Internet.

#### <a name="proxy-ports"></a>Portas proxy

O conector faz ligações à saída baseadas em TLS utilizando o método CONNECT. Este método essencialmente estabelece um túnel através do representante de saída. Configure o servidor proxy para permitir a escavação nas portas 443 e 80.

> [!NOTE]
> Quando o Service Bus passa por HTTPS, utiliza a porta 443. No entanto, por padrão, o Service Bus tenta ligações TCP diretas e volta a HTTPS apenas se a conectividade direta falhar.

#### <a name="tls-inspection"></a>Inspeção TLS

Não utilize a inspeção TLS para o tráfego do conector, pois causa problemas ao tráfego do conector. O conector utiliza um certificado para autenticar o serviço de Procuração de Aplicações, e esse certificado pode ser perdido durante a inspeção TLS.

## <a name="configure-using-a-proxy-between-the-connector-and-backend-application"></a>Configure usando um proxy entre o conector e a aplicação backend
A utilização de um representante para a frente para a comunicação para a aplicação backend pode ser um requisito especial em alguns ambientes.
Para o ativar, siga os próximos passos:

### <a name="step-1-add-the-required-registry-value-to-the-server"></a>Passo 1: Adicione o valor de registo necessário ao servidor
1. Para ativar a utilização do representante predefinido, adicione o seguinte valor de registo (DWORD) `UseDefaultProxyForBackendRequests = 1` à chave de registo de configuração do Conector localizada em "HKEY_LOCAL_MACHINE\Software\Microsoft\Microsoft AAD App Proxy Connector".

### <a name="step-2-configure-the-proxy-server-manually-using-netsh-command"></a>Passo 2: Configurar manualmente o servidor proxy usando o comando netsh
1.  Ativar a política de grupo Fazer configurações de procuração por máquina. Isto encontra-se em: Configuração do computador\Políticas\Modelos administrativos\Componentes do Windows\Internet Explorer. Isto tem de ser definido em vez de ter esta política definida por utilizador.
2.  Executar `gpupdate /force` no servidor ou reiniciar o servidor para garantir que utiliza as definições atualizadas da política do grupo.
3.  Lance um pedido de comando elevado com direitos de administração e entre `control inetcpl.cpl` .
4.  Configure as definições de procuração necessárias. 

Estas definições fazem com que o conector utilize o mesmo representante para a frente para a comunicação ao Azure e para a aplicação backend. Se o conector para a comunicação Azure não necessitar de procuração para a frente ou de um representante avançado diferente, pode converde-o com a modificação do ficheiro ApplicationProxyConnectorService.exe.config conforme descrito nas secções Bypass proxies de saída ou use o servidor de procuração de saída.

> [!NOTE]
> Existem várias formas de configurar o proxy da internet no sistema operativo. As definições de procuração configuradas através do NETSH WINHTTP (executar `NETSH WINHTTP SHOW PROXY` para verificar) substituem as definições de procuração configuradas no Passo 2. 

O serviço de atualização do conector também utilizará o proxy da máquina. Este comportamento pode ser alterado modificando o ficheiro ApplicationProxyConnectorUpdaterService.exe.config.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Problemas de procuração de conector de resolução de problemas e problemas de conectividade de serviço

Agora devias ver todo o tráfego a fluir pelo procurador. Se tiver problemas, as seguintes informações de resolução de problemas devem ajudar.

A melhor maneira de identificar e resolver problemas de conectividade do conector é fazer uma captura de rede enquanto inicia o serviço de conector. Aqui ficam algumas dicas rápidas sobre a captura e filtragem de vestígios de rede.

Pode utilizar a ferramenta de monitorização à sua escolha. Para efeitos deste artigo, utilizamos o Microsoft Message Analyzer.

Os exemplos a seguir são específicos do Analisador de Mensagens, mas os princípios podem ser aplicados a qualquer ferramenta de análise.

### <a name="take-a-capture-of-connector-traffic"></a>Faça uma captura do tráfego do conector

Para a resolução inicial de problemas, execute os seguintes passos:

1. A partir de services.msc, pare o serviço de Conector Proxy da aplicação Azure AD.

   ![Serviço de Conector Proxy de aplicação Azure AD em serviços.msc](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Executar o Analisador de Mensagens como administrador.
1. Selecione **Iniciar o rastreio local** .
1. Inicie o serviço de conector de aplicação AD Azure.
1. Parem a captura da rede.

   ![A screenshot mostra o botão de captura da rede Stop](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Verifique se o tráfego do conector contorna os proxies de saída

Se configurar o conector Proxy da aplicação para contornar os servidores proxy e ligar-se diretamente ao serviço Application Proxy, pretende procurar na captura de rede tentativas de ligação TCP falhadas.

Utilize o filtro de analisador de mensagens para identificar estas tentativas. Introduza `property.TCPSynRetransmit` na caixa do filtro e selecione **Aplicar** .

Um pacote SYN é o primeiro pacote enviado para estabelecer uma ligação TCP. Se este pacote não devolver uma resposta, o SYN é re-retribuído. Pode utilizar o filtro anterior para ver quaisquer SYNs retransmitidos. Em seguida, pode verificar se estes SYNs correspondem a qualquer tráfego relacionado com o conector.

Se espera que o conector faça ligações diretas aos serviços Azure, as respostas do SynRetransmit na porta 443 são uma indicação de que tem um problema de rede ou firewall.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Verifique se o tráfego do conector utiliza proxies de saída

Se configurar o tráfego do conector Proxy da aplicação para passar pelos servidores proxy, pretende procurar ligações de https falhadas ao seu representante.

Para filtrar a captura de rede para estas tentativas de ligação, introduza `(https.Request or https.Response) and tcp.port==8080` no filtro Message Analyzer, substituindo 8080 pela porta de serviço proxy. **Selecione Aplicar** para ver os resultados do filtro.

O filtro anterior mostra apenas os pedidos e respostas dos HTTPs para/a partir da porta de procuração. Está à procura dos pedidos CONNECT que mostram comunicação com o servidor proxy. Após o sucesso, obtém-se uma resposta HTTP OK (200).

Se vir outros códigos de resposta, como o 407 ou o 502, isso significa que o representante está a necessitar de autenticação ou não permite o tráfego por outro motivo. Neste momento, contrata a equipa de suporte ao servidor proxy.

## <a name="next-steps"></a>Passos seguintes

* [Compreenda os conectores Proxy de aplicação AD Azure](application-proxy-connectors.md)
* Se tiver problemas com problemas de conectividade do conector, faça a sua pergunta na página de perguntas do [Microsoft Q&A para o Azure Ative Directory](https://docs.microsoft.com/answers/topics/azure-active-directory.html) ou crie um bilhete com a nossa equipa de suporte.
