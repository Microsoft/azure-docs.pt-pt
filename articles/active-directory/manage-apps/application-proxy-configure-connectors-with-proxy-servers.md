---
title: Trabalhar com servidores proxy locais existentes e o Azure AD | Microsoft Docs
description: Aborda como trabalhar com servidores proxy locais existentes.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: d305f3354e7b1af6d43f31f0dd5fe9f54ef3e66f
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242276"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Trabalhar com servidores proxy locais existentes

Este artigo explica como configurar os conectores de proxy de aplicativo Azure Active Directory (Azure AD) para trabalhar com servidores proxy de saída. Ele destina-se a clientes com ambientes de rede que têm proxies existentes.

Começamos examinando esses principais cenários de implantação:

* Configure conectores para ignorar seus proxies de saída locais.
* Configure conectores para usar um proxy de saída para acessar Proxy de Aplicativo do AD do Azure.

Para obter mais informações sobre como os conectores funcionam, consulte [entender os conectores de proxy de aplicativo do AD do Azure](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Ignorar proxies de saída

OS conectores têm componentes de sistema operacional subjacentes que fazem solicitações de saída. Esses componentes tentam automaticamente localizar um servidor proxy na rede usando a descoberta automática de proxy da Web (WPAD).

Os componentes do sistema operacional tentam localizar um servidor proxy executando uma pesquisa de DNS para WPAD. domainsuffix. Se a pesquisa for resolvida no DNS, uma solicitação HTTP será feita para o endereço IP para WPAD. dat. Essa solicitação se torna o script de configuração de proxy em seu ambiente. O conector usa esse script para selecionar um servidor proxy de saída. No entanto, o tráfego do conector ainda pode não passar, devido às definições de configuração adicionais necessárias no proxy.

Você pode configurar o conector para ignorar o proxy local para garantir que ele use conectividade direta com os serviços do Azure. Recomendamos essa abordagem, desde que sua política de rede permita isso, porque significa que você tem uma menos configuração para manter.

Para desabilitar o uso do proxy de saída para o conector, edite o arquivo C:\Program Files\Microsoft AAD app proxy Connector\ApplicationProxyConnectorService.exe.config e adicione a seção *System.net* mostrada neste exemplo de código:

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

Para garantir que o serviço de Atualizador do Conector também ignore o proxy, faça uma alteração semelhante no arquivo no arquivo applicationproxyconnectorupdaterservice. exe. config. Esse arquivo está localizado em C:\Arquivos de Programas\microsoft AAD app proxy Atualizador do Conector.

Certifique-se de fazer cópias dos arquivos originais, caso precise reverter para os arquivos. config padrão.

## <a name="use-the-outbound-proxy-server"></a>Usar o servidor proxy de saída

Alguns ambientes exigem que todo o tráfego de saída passe por um proxy de saída, sem exceção. Como resultado, ignorar o proxy não é uma opção.

Você pode configurar o tráfego do conector para passar pelo proxy de saída, conforme mostrado no diagrama a seguir:

 ![Configurando o tráfego do conector para passar por um proxy de saída para o Azure Proxy de Aplicativo do AD](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

Como resultado da existência apenas do tráfego de saída, não há necessidade de configurar o acesso de entrada por meio de firewalls.

> [!NOTE]
> O proxy de aplicativo não dá suporte à autenticação para outros proxies. O conector/contas de serviço de rede do atualizador deve ser capaz de se conectar ao proxy sem ser desafiado para autenticação.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Etapa 1: configurar o conector e os serviços relacionados para passar pelo proxy de saída

Se o WPAD estiver habilitado no ambiente e configurado adequadamente, o conector descobrirá automaticamente o servidor proxy de saída e tentará usá-lo. No entanto, você pode configurar explicitamente o conector para passar por um proxy de saída.

Para fazer isso, edite o arquivo C:\Program Files\Microsoft AAD app proxy Connector\ApplicationProxyConnectorService.exe.config e adicione a seção *System.net* mostrada neste exemplo de código. Altere *ProxyServer: 8080* para refletir o nome do servidor proxy local ou o endereço IP e a porta na qual ele está escutando. O valor deve ter o prefixo http://mesmo se você estiver usando um endereço IP.

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

Em seguida, configure o serviço de Atualizador do Conector para usar o proxy fazendo uma alteração semelhante no arquivo C:\Program Files\Microsoft AAD app proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Etapa 2: configurar o proxy para permitir o tráfego do conector e dos serviços relacionados para fluir

Há quatro aspectos a serem considerados no proxy de saída:

* Regras de saída do proxy
* Autenticação de proxy
* Portas de proxy
* Inspeção SSL

#### <a name="proxy-outbound-rules"></a>Regras de saída do proxy

Permitir acesso às seguintes URLs:

| URL | Como é usado |
| --- | --- |
| \*. msappproxy.net<br>\*. servicebus.windows.net | Comunicação entre o conector e o serviço de nuvem do proxy de aplicativo |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | O Azure usa essas URLs para verificar os certificados |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>*. microsoftonline.com<br>* . microsoftonline-p.com<br>*. msauth.net<br>* . msauthimages.net<br>*. msecnd.net<br>* . msftauth.net<br>*. msftauthimages.net<br>* . PhoneFactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net | O conector usa essas URLs durante o processo de registro. |

Se o firewall ou o proxy permitir que você configure listas de permissões de DNS, você poderá permitir conexões com \*. msappproxy.net e \*. servicebus.windows.net. Caso contrário, você precisará permitir o acesso aos [intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653). Os intervalos de IP são atualizados a cada semana.

Se você não puder permitir a conectividade pelo FQDN e precisar especificar intervalos de IP, use estas opções:

* Permitir o acesso de saída do conector a todos os destinos.
* Permitir o acesso de saída do conector a todos os [intervalos de IP do datacenter do Azure](https://www.microsoft.com//download/details.aspx?id=41653). O desafio de usar a lista de intervalos de IP do datacenter do Azure é que ele é atualizado semanalmente. Você precisa colocar um processo em vigor para garantir que suas regras de acesso sejam atualizadas de acordo. O uso apenas de um subconjunto dos endereços IP pode causar a interrupção da configuração.

#### <a name="proxy-authentication"></a>Autenticação de proxy

Atualmente, não há suporte para autenticação de proxy. Nossa recomendação atual é permitir o acesso anônimo do conector aos destinos da Internet.

#### <a name="proxy-ports"></a>Portas de proxy

O conector faz conexões baseadas em SSL de saída usando o método CONNECT. Esse método essencialmente configura um túnel por meio do proxy de saída. Configure o servidor proxy para permitir o túnel para as portas 443 e 80.

> [!NOTE]
> Quando o barramento de serviço é executado via HTTPS, ele usa a porta 443. No entanto, por padrão, o barramento de serviço tenta conexões TCP diretas e volta para HTTPS somente se a conectividade direta falhar.

#### <a name="ssl-inspection"></a>Inspeção SSL

Não use a inspeção SSL para o tráfego do conector, pois isso causa problemas para o tráfego do conector. O conector usa um certificado para autenticar o serviço de proxy de aplicativo e esse certificado pode ser perdido durante a inspeção SSL.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Solucionar problemas de proxy do conector e problemas de conectividade do serviço

Agora você deve ver todo o tráfego que flui pelo proxy. Se você tiver problemas, as informações de solução de problemas a seguir devem ajudar.

A melhor maneira de identificar e solucionar problemas de conectividade do conector é fazer uma captura de rede ao iniciar o serviço do conector. Aqui estão algumas dicas rápidas sobre como capturar e filtrar rastreamentos de rede.

Você pode usar a ferramenta de monitoramento de sua escolha. Para os fins deste artigo, usamos o Microsoft Message Analyzer. Você pode [baixá-lo da Microsoft](https://www.microsoft.com/download/details.aspx?id=44226).

Os exemplos a seguir são específicos do analisador de mensagem, mas os princípios podem ser aplicados a qualquer ferramenta de análise.

### <a name="take-a-capture-of-connector-traffic"></a>Fazer uma captura do tráfego do conector

Para a solução de problemas inicial, execute as seguintes etapas:

1. Em Services. msc, interrompa o serviço do conector do Azure Proxy de Aplicativo do AD.

   ![Serviço do conector do Azure Proxy de Aplicativo do AD no Services. msc](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Execute o analisador de mensagem como administrador.
1. Selecione **Iniciar rastreamento local**.
1. Inicie o serviço do conector do Azure Proxy de Aplicativo do AD.
1. Pare a captura de rede.

   ![Captura de tela mostra o botão parar captura de rede](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Verifique se o tráfego do conector ignora os proxies de saída

Se você configurou o conector de proxy de aplicativo para ignorar os servidores proxy e se conectar diretamente ao serviço de proxy de aplicativo, você deseja examinar a captura de rede em busca de tentativas de conexão TCP com falha.

Use o filtro do analisador de mensagem para identificar essas tentativas. Insira `property.TCPSynRetransmit` na caixa de filtro e selecione **aplicar**.

Um pacote SYN é o primeiro pacote enviado para estabelecer uma conexão TCP. Se esse pacote não retornar uma resposta, o SYN será tentado novamente. Você pode usar o filtro anterior para ver qualquer SYNs retransmitido. Em seguida, você pode verificar se esses SYNs correspondem a qualquer tráfego relacionado ao conector.

Se você espera que o conector faça conexões diretas com os serviços do Azure, as respostas de SynRetransmit na porta 443 são uma indicação de que você tem um problema de rede ou firewall.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Verificar se o tráfego do conector usa proxies de saída

Se você configurou o tráfego do conector do proxy de aplicativo para passar pelos servidores proxy, convém procurar conexões HTTPS com falha para o proxy.

Para filtrar a captura de rede para essas tentativas de conexão, insira `(https.Request or https.Response) and tcp.port==8080` no filtro do analisador de mensagem, substituindo 8080 pela porta do serviço de proxy. Selecione **aplicar** para ver os resultados do filtro.

O filtro anterior mostra apenas as solicitações e respostas HTTPs de/para a porta do proxy. Você está procurando as solicitações de conexão que mostram a comunicação com o servidor proxy. Após o êxito, você receberá uma resposta HTTP OK (200).

Se você vir outros códigos de resposta, como 407 ou 502, isso significa que o proxy está exigindo autenticação ou não permitindo o tráfego por algum outro motivo. Neste ponto, você envolve a equipe de suporte do servidor proxy.

## <a name="next-steps"></a>Passos seguintes

* [Entender os conectores de Proxy de Aplicativo do AD do Azure](application-proxy-connectors.md)
* Se você tiver problemas com problemas de conectividade de conector, faça sua pergunta no [Fórum de Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) ou crie um tíquete com nossa equipe de suporte.
