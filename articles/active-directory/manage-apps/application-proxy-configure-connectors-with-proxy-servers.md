---
title: Trabalhe com os servidores proxy existentes no local e a AD Azure [ Microsoft Docs
description: Cobre como trabalhar com servidores proxy existentes no local.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0aafb971ca1ce812a68045f7d0c0c2ab7f532133
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80877393"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Trabalhar com servidores proxy existentes no local

Este artigo explica como configurar os conectores proxy do Azure Ative Directory (Azure AD) para trabalhar com servidores proxy de saída. Destina-se a clientes com ambientes de rede que tenham proxies existentes.

Começamos por olhar para estes principais cenários de implantação:

* Configure os conectores para contornar os seus proxies de saída no local.
* Configure os conectores para utilizar um proxy de saída para aceder ao Proxy de Aplicação AD Azure.
* Configure utilizando um proxy entre o conector e a aplicação backend.

Para obter mais informações sobre como funcionam os conectores, consulte [conectores de proxy de aplicação ad azure](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Contornar proxies de saída

Os conectores têm componentes de SO subjacentes que fazem pedidos de saída. Estes componentes tentam automaticamente localizar um servidor proxy na rede utilizando o Web Proxy Auto-Discovery (WPAD).

Os componentes DO Tentam localizar um servidor proxy através da realização de um pedido de dNS para wpad.domainsuffix. Se o lookup resolver em DNS, um pedido HTTP é então feito para o endereço IP para wpad.dat. Este pedido torna-se o script de configuração proxy no seu ambiente. O conector utiliza este script para selecionar um servidor proxy de saída. No entanto, o tráfego do conector ainda pode não passar, devido às configurações adicionais necessárias no proxy.

Pode configurar o conector para contornar o seu proxy no local para garantir que utiliza conectividade direta com os serviços Azure. Recomendamos esta abordagem, desde que a sua política de rede o permita, porque significa que tem menos uma configuração para manter.

Para desativar a utilização de proxy de saída para o conector, edite o ficheiro C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config e adicione a secção *system.net* mostrada nesta amostra de código:

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

Para garantir que o serviço de atualização do Conector também contorna o proxy, faça uma alteração semelhante ao ficheiro ApplicationProxyConnectorUpdaterService.exe.config. Este ficheiro está localizado no C:\Program Files\Microsoft AAD App Proxy Updateor.

Certifique-se de que faz cópias dos ficheiros originais, caso precise de voltar aos ficheiros predefinidos .config.

## <a name="use-the-outbound-proxy-server"></a>Utilize o servidor proxy de saída

Alguns ambientes exigem que todo o tráfego de saída passe por um proxy de saída, sem exceção. Como resultado, contornar o proxy não é uma opção.

Pode configurar o tráfego do conector para passar pelo proxy de saída, como mostra o seguinte diagrama:

 ![Configurar o tráfego de conector para passar por um proxy de saída para o Proxy de Aplicação AD Azure](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

Como resultado de ter apenas tráfego de saída, não há necessidade de configurar o acesso de entrada através das suas firewalls.

> [!NOTE]
> Application Proxy não suporta a autenticação a outros proxies. As contas de serviço de rede de conector/atualizadores devem poder ligar-se ao proxy sem serem desafiadas para autenticação.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Passo 1: Configure o conector e os serviços conexos para passar pelo proxy de saída

Se o WPAD estiver ativado no ambiente e configurado adequadamente, o conector descobre automaticamente o servidor proxy de saída e tenta utilizá-lo. No entanto, pode configurar explicitamente o conector para passar por um proxy de saída.

Para tal, edite o ficheiro C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config e adicione a secção *system.net* mostrada nesta amostra de código. Altere o *proxyserver:8080* para refletir o nome do servidor de procuração local ou endereço IP, e a porta que está a ouvir. O valor deve ter o prefixo http:// mesmo que esteja a utilizar um endereço IP.

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

Em seguida, configure o serviço de atualização do Conector para utilizar o proxy fazendo uma alteração semelhante ao ficheiro C:\Program Files\Microsoft AAD Application Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Passo 2: Configure o proxy para permitir que o tráfego do conector e serviços conexos fluam através

Há quatro aspetos a ter em conta no proxy de saída:

* Regras de saída de proxy
* Autenticação por procuração
* Portas proxy
* Inspeção TLS

#### <a name="proxy-outbound-rules"></a>Regras de saída de proxy

Permitir o acesso aos seguintes URLs:

| do IdP | Como é usado |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Comunicação entre o conector e o serviço de nuvem application Proxy |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | O conector utiliza estes URLs para verificar certificados |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>*.microsoftonline.com<br>*.microsoftonline-p.com<br>*.msauth.net<br>*.msauthimages.net<br>*.msecnd.net<br>*.msftauth.net<br>*.msftauthimages.net<br>*.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctdl.windowsupdate.com:80 | O conector utiliza estes URLs durante o processo de registo. |

Se a sua firewall ou proxy lhe permitir configurar listas \*de \*DNS, pode permitir ligações a .msappproxy.net e .servicebus.windows.net. Caso contrário, tem de permitir o acesso às gamas IP do [Azure DataCenter](https://www.microsoft.com/download/details.aspx?id=41653). As gamas IP são atualizadas todas as semanas.

Se não conseguir permitir a conectividade por parte da FQDN e precisar de especificar intervalos IP, utilize estas opções:

* Permita o acesso de saída do conector a todos os destinos.
* Permitir o acesso de saída do conector a todas as gamas IP do centro de [dados Azure](https://www.microsoft.com//download/details.aspx?id=41653). O desafio com a utilização da lista de gamas IP do Centro de Dados Do Azure é que é atualizado semanalmente. É necessário implementar um processo para garantir que as suas regras de acesso sejam atualizadas em conformidade. Apenas a utilização de um subconjunto dos endereços IP pode fazer com que a sua configuração se rompa.

#### <a name="proxy-authentication"></a>Autenticação por procuração

A autenticação por procuração não é suportada atualmente. A nossa recomendação atual é permitir o acesso anónimo ao conector anónimo aos destinos da Internet.

#### <a name="proxy-ports"></a>Portas proxy

O conector faz ligações baseadas em TLS de saída utilizando o método CONNECT. Este método essencialmente estabelece um túnel através do proxy de saída. Configure o servidor proxy para permitir a escavação nas portas 443 e 80.

> [!NOTE]
> Quando o Ônibus de serviço passa por HTTPS, utiliza a porta 443. No entanto, por padrão, o Service Bus tenta ligações TCP diretas e recua para HTTPS apenas se a conectividade direta falhar.

#### <a name="tls-inspection"></a>Inspeção TLS

Não utilize a inspeção TLS para o tráfego do conector, pois causa problemas ao tráfego do conector. O conector utiliza um certificado para autenticar o serviço de procuração de aplicações, podendo esse certificado ser perdido durante a inspeção do TLS.

## <a name="configure-using-a-proxy-between-the-connector-and-backend-application"></a>Configure utilizando um proxy entre o conector e a aplicação backend
A utilização de um representante avançado para a comunicação para a aplicação backend pode ser um requisito especial em alguns ambientes.
Para o permitir, siga os próximos passos:

### <a name="step-1-add-the-required-registry-value-to-the-server"></a>Passo 1: Adicione o valor de registo necessário ao servidor
1. Para permitir a utilização do proxy predefinido, `UseDefaultProxyForBackendRequests = 1` adicione o seguinte valor de registo (DWORD) à chave de registo de configuração do Conector localizada em "HKEY_LOCAL_MACHINE\Software\Microsoft\Microsoft AAD App Proxy Conector".

### <a name="step-2-configure-the-proxy-server-manually-using-netsh-command"></a>Passo 2: Configure manualmente o servidor proxy utilizando o comando netsh
1.  Ativar a política de grupo Faça as definições de procuração por máquina. Isto encontra-se em: Configuração do computador\Policies\Modelos Administrativos\Componentes do Windows\Internet Explorer. Isto tem de ser definido em vez de ter esta política definida por utilizador.
2.  Executar `gpupdate /force` no servidor ou reiniciar o servidor para garantir que utiliza as definições de política do grupo atualizados.
3.  Lance um pedido de comando elevado `control inetcpl.cpl`com direitos de administração e entre .
4.  Configure as definições de procuração necessárias. 

Estas definições fazem com que o conector utilize o mesmo proxy dianteiro para a comunicação ao Azure e à aplicação backend. Se o conector à comunicação Azure não necessitar de procuração para a frente ou de um proxy avançado diferente, pode configurar isto com a modificação do ficheiro ApplicationProxyConnectorService.exe.config, conforme descrito nas secções Bypass proxies de saída ou utilizar o servidor proxy de saída.

O serviço de atualização do conector também utilizará o proxy da máquina. Este comportamento pode ser alterado modificando o ficheiro ApplicationProxyConnectorUpdaterService.exe.config.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Problemas de procuração de conector de resolução de problemas e problemas de conectividade de serviço

Agora deviaver todo o tráfego a fluir pela procuração. Se tiver problemas, as seguintes informações de resolução de problemas devem ajudar.

A melhor maneira de identificar e resolver problemas de conectividade do conector é obter uma captura de rede enquanto inicia o serviço de conector. Aqui ficam algumas dicas rápidas sobre a captura e filtragem de vestígios de rede.

Pode utilizar a ferramenta de monitorização à sua escolha. Para efeitos deste artigo, utilizámos o Microsoft Message Analyzer. Pode [descarregá-lo a partir da Microsoft.](https://www.microsoft.com/download/details.aspx?id=44226)

Os exemplos seguintes são específicos do Analisador de Mensagens, mas os princípios podem ser aplicados a qualquer ferramenta de análise.

### <a name="take-a-capture-of-connector-traffic"></a>Faça uma captura do tráfego de conector

Para a resolução inicial de problemas, execute os seguintes passos:

1. A partir de services.msc, pare o serviço de Conector proxy de aplicação ad ad azure.

   ![Serviço de Conector proxy de aplicação da AD Azure em serviços.msc](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Executar O Analisador de Mensagens como administrador.
1. Selecione **Iniciar vestígios locais**.
1. Inicie o serviço de conector proxy de aplicação ad ida e volta.
1. Pare a captura da rede.

   ![Screenshot mostra o botão de captura da rede Stop](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Verifique se o tráfego do conector contorna os proxies de saída

Se configurar o conector Proxy de Aplicação para contornar os servidores proxy e ligar-se diretamente ao serviço Proxy de Aplicação, pretende procurar na captura de rede tentativas de ligação TCP falhadas.

Utilize o filtro Analisador de Mensagens para identificar estas tentativas. Introduza `property.TCPSynRetransmit` na caixa de filtro e selecione **Aplicar**.

Um pacote SYN é o primeiro pacote enviado para estabelecer uma ligação TCP. Se este pacote não devolver uma resposta, o SYN é retentado. Pode utilizar o filtro anterior para ver quaisquer SYNs retransmitidos. Em seguida, pode verificar se estas SYNs correspondem a qualquer tráfego relacionado com o conector.

Se espera que o conector efetua ligações diretas aos serviços Azure, as respostas SynRetransmit na porta 443 são uma indicação de que tem um problema de rede ou firewall.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Verifique se o tráfego do conector utiliza proxies de saída

Se configurar o tráfego de conector Proxy de Aplicação para passar pelos servidores proxy, pretende procurar ligações https falhadas ao seu representante.

Para filtrar a captura da rede `(https.Request or https.Response) and tcp.port==8080` para estas tentativas de ligação, introduza no filtro Message Analyzer, substituindo 8080 pela sua porta de serviço proxy. Selecione **Aplicar** para ver os resultados do filtro.

O filtro anterior mostra apenas os pedidos e respostas hTTPs de/para a porta proxy. Está à procura dos pedidos connect que mostram comunicação com o servidor proxy. Após o sucesso, obtém-se uma resposta HTTP OK (200).

Se vir outros códigos de resposta, como o 407 ou o 502, isso significa que o representante está a exigir autenticação ou não permite o tráfego por outra razão. Neste momento, envolves a tua equipa de suporte ao servidor proxy.

## <a name="next-steps"></a>Passos seguintes

* [Compreender os conectores de procuração de aplicação da AD Azure](application-proxy-connectors.md)
* Se tiver problemas com problemas de conectividade de conector, faça a sua pergunta no [fórum azure Ative Diretório](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) ou crie um bilhete com a nossa equipa de apoio.
