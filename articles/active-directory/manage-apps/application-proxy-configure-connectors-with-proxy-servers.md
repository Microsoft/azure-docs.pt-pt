---
title: Trabalho com o existente no local servidores proxy e o Azure AD | Documentos da Microsoft
description: Aborda como trabalhar com servidores de proxy no local existentes.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: celested
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b4cb1f6cc3da5230f510f57a56c7297341f82f3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60293686"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Trabalhar com servidores de proxy no local existentes

Este artigo explica como configurar conectores de Proxy de aplicações do Azure Active Directory (Azure AD) para trabalhar com servidores de proxy de saída. Destina-se para os clientes com ambientes de rede que tenham proxies existentes.

Vamos começar examinando esses cenários de implementação principais:
* Configure conectores para ignorar os proxies de saída no local.
* Configure os conectores para utilizar um proxy de saída para aceder ao Proxy de aplicações do Azure AD.

Para obter mais informações sobre como funcionam os conectores, consulte [conectores de Proxy de aplicações do AD Azure compreender](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Proxies de saída de omissão

Conectores têm componentes do sistema operacional subjacentes que efetuam pedidos de saída. Esses componentes automaticamente tentarem localizar um servidor proxy na rede usando o Proxy Auto-Discovery WPAD (Web).

Os componentes do SO tentam localizar um servidor proxy ao realizar uma pesquisa de DNS para wpad.domainsuffix. Se a pesquisa é resolvido no DNS, um pedido HTTP, em seguida, é feito para o endereço IP para wpad.dat. Este pedido torna-se o script de configuração de proxy no seu ambiente. O conector utiliza este script para selecionar um servidor de proxy de saída. No entanto, o tráfego de conector poderá ainda não passar por, devido às definições de configuração adicionais necessárias no proxy.

Pode configurar o conector para ignorar o proxy no local para se certificar de que utiliza conectividade direta aos serviços do Azure. Recomendamos esta abordagem, enquanto permite que a política de rede, porque significa que tenha uma menor configuração para manter.

Para desativar a utilização de proxy de saída para o conector, edite o ficheiro de C:\Program Files\Microsoft AAD aplicação Proxy Connector\ApplicationProxyConnectorService.exe.config e adicione a *system.net* secção mostrada neste exemplo de código:

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
Para garantir que o serviço Atualizador do Conetor também ignora o proxy, fazer uma alteração similar no arquivo de ApplicationProxyConnectorUpdaterService.exe.config. Este ficheiro está localizado em Atualizador de conector do Proxy de aplicação do C:\Program Files\Microsoft AAD.

Certifique-se de que fazer cópias dos arquivos originais, caso seja necessário reverter para os arquivos. config do padrão.

## <a name="use-the-outbound-proxy-server"></a>Utilizar o servidor de proxy de saída

Alguns ambientes requerem todo o tráfego de saída passar por um proxy de saída, sem exceção. Como resultado, a ignorar o proxy não é uma opção.

Pode configurar o tráfego de conector para percorrer o proxy de saída, conforme mostrado no diagrama seguinte:

 ![Configurar o tráfego de conector de passar por um proxy de saída para o Proxy de aplicações do Azure AD](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

Como resultado de ter apenas o tráfego de saída, não é necessário para configurar o acesso de entrada através de seus firewalls.

>[!NOTE]
>Proxy de aplicações não suporta a autenticação para outros proxies. As contas de serviço de rede do atualizador do conetor devem ser capazes de ligar para o proxy sem sendo desafiados para autenticação.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Passo 1: Configurar o conector e serviços relacionados para ir até o proxy de saída

Se WPAD é ativado no ambiente e configurada corretamente, o conector Deteta automaticamente o servidor de proxy de saída e a tentativa de usá-lo. No entanto, pode configurar explicitamente o conector de passar por um proxy de saída.

Para fazê-lo, edite o ficheiro de C:\Program Files\Microsoft AAD aplicação Proxy Connector\ApplicationProxyConnectorService.exe.config e adicione a *system.net* secção mostrada neste exemplo de código. Alteração *proxyserver:8080* para refletir o nome do servidor local proxy ou endereço IP e a porta que está a escutar. O valor tem de ter o prefixo http://, mesmo se estiver a utilizar um endereço IP.

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

Em seguida, configure o serviço de Atualizador do Conetor para utilizar o proxy ao fazer uma alteração similar para o ficheiro de aplicação de AAD do C:\Program Files\Microsoft Proxy conector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Passo 2: Configurar o proxy para permitir tráfego a partir do conector e serviços relacionados através de

Existem quatro aspetos a considerar no proxy de saída:
* Regras de saída de proxy
* Autenticação de proxy
* Portas de proxy
* Inspeção do SSL

#### <a name="proxy-outbound-rules"></a>Regras de saída de proxy
Permitir o acesso aos seguintes URLs:

| do IdP | Como são utilizadas |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Comunicação entre o conector e o serviço de nuvem do Proxy de aplicações |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | O Azure utiliza estes URLs para verificar os certificados |
| login.windows.net<br>login.microsoftonline.com | O conector utiliza estes URLs durante o processo de registo. |

Se a sua firewall ou proxy permite que listas de permissões de DNS, pode ligações de lista de permissões para \*. msappproxy.net e \*. servicebus.windows.net. Se não, precisa permitir o acesso para o [intervalos de IP de DataCenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653). Os intervalos de IP são atualizados a cada semana.


Se não pode permitir a ligação através do FQDN e tem de especificar os intervalos de IP em vez disso, utilize estas opções:

* Permita o acesso de saída do conector para todos os destinos.
* Permitir o acesso de saída do conector para todos os [intervalos IP do datacenter do Azure](https://www.microsoft.com//download/details.aspx?id=41653). O desafio com utilizando a lista de intervalos IP do datacenter do Azure é o que é atualizada semanalmente. Precisa colocar um processo para assegurar que as regras de acesso são atualizadas em conformidade. Utilizar apenas um subconjunto dos endereços IP pode fazer com que a configuração de quebrar.

#### <a name="proxy-authentication"></a>Autenticação de proxy

Autenticação de proxy não é atualmente suportada. A nossa recomendação atual é permitir o acesso anônimo do conector para os destinos de Internet.

#### <a name="proxy-ports"></a>Portas de proxy

O conector faz ligações de saída baseada em SSL com o método CONNECT. Esse método essencialmente define um túnel através do proxy de saída. Configure o servidor de proxy para permitir que o túnel para as portas 443 e 80.

>[!NOTE]
>Quando o Service Bus é executado através de HTTPS, utiliza a porta 443. No entanto, por predefinição, o Service Bus tenta estabelecer ligações de TCP diretas e retrocede para HTTPS apenas se a falha de conectividade direta.

#### <a name="ssl-inspection"></a>Inspeção do SSL
Não utilize inspeção do SSL para o tráfego de conector, porque faz com problemas para o tráfego de conector. O conector utiliza um certificado para autenticação no serviço de Proxy de aplicações, e esse certificado pode ser perdido durante a inspeção do SSL. 

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Resolver problemas do proxy de conector e problemas de conectividade do serviço
Agora deve ver todo o tráfego que flui através do proxy. Se tiver problemas, devem ajudar as seguintes informações de resolução de problemas.

É a melhor forma de identificar e resolver problemas de conectividade do conector tirar uma captura de rede ao iniciar o serviço de conectores. Eis algumas sugestões rápidas sobre a captura e a filtragem de rastreamentos de rede.

Pode usar a ferramenta de monitoramento da sua preferência. Para os fins deste artigo, utilizamos o Microsoft Message Analyzer. Pode [baixá-lo da Microsoft](https://www.microsoft.com/download/details.aspx?id=44226).

Os exemplos seguintes são específicos para o analisador de mensagens, mas os princípios podem ser aplicados a qualquer ferramenta de análise.

### <a name="take-a-capture-of-connector-traffic"></a>Tirar uma captura de tráfego do conector

Para resolução de problemas inicial, execute os seguintes passos:

1. Em Services. msc, pare o serviço de conector de Proxy de aplicações do Azure AD.

   ![Serviço de conector de Proxy de aplicações do AD do Azure em Services. msc](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

2. Execute o analisador de mensagens como um administrador.
3. Selecione **iniciar o rastreio local**.

   ![Iniciar captura de rede](./media/application-proxy-configure-connectors-with-proxy-servers/start-local-trace.png)

3. Inicie o serviço de conector de Proxy de aplicações do Azure AD.
4. Pare a captura de rede.

   ![Pare a captura de rede](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Verifique se o tráfego de conector ignorará proxies de saída

Se tiver configurado o conector de Proxy da aplicação para ignorar os servidores proxy e ligue-se diretamente para o serviço de Proxy de aplicações, que pretende ver na captura de rede tentativas malsucedidas de ligação de TCP. 

Utilize o filtro de Message Analyzer para identificar estas tentativas. Introduza `property.TCPSynRetransmit` na caixa de filtro e selecione **aplicar**. 

Um pacote SYN é o primeiro pacote enviado para estabelecer uma ligação de TCP. Se este pacote não devolverem uma resposta, o SYN é tentar novamente. Pode utilizar o filtro anterior para ver qualquer SYNs retransmitted. Em seguida, pode verificar se estes SYNs correspondem a qualquer tráfego relacionado com o conector.

Se pretender que o conector para efetuar ligações diretas para os serviços do Azure, as respostas de SynRetransmit na porta 443 são uma indicação de que tem um problema de rede ou firewall.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Verifique se o tráfego de conector utiliza proxies de saída

Se tiver configurado o seu tráfego de conector de Proxy de aplicações para ir até os servidores proxy, deve procurar ligações https com falha para o proxy. 

Para filtrar a captura de rede para essas tentativas de ligação, introduza `(https.Request or https.Response) and tcp.port==8080` no filtro de Message Analyzer, substituindo 8080 com sua porta do serviço de proxy. Selecione **aplicar** para ver os resultados do filtro. 

O filtro anterior mostra apenas os pedidos de HTTPs e respostas de/para a porta de proxy. Procura os pedidos do CONNECT que mostram a comunicação com o servidor de proxy. Após a conclusão bem-sucedida, obtém uma resposta HTTP OK (200).

Se vir que os outros códigos de resposta, como 407 ou 502, que significa que o proxy é que exigem autenticação ou não permitir o tráfego por algum motivo. Neste ponto, interagir com a equipa de suporte de servidor proxy.

## <a name="next-steps"></a>Passos Seguintes

- [Compreender os conectores de Proxy de aplicações do Azure AD](application-proxy-connectors.md)

- Se tiver problemas com problemas de conectividade do conector, peça a sua pergunta no [fórum do Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) ou criar um pedido de suporte com a nossa equipa de suporte.
