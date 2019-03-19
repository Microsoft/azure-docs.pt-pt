---
title: Compreender os conectores de Proxy de aplicações do Azure AD | Documentos da Microsoft
description: Abrange as noções básicas sobre os conectores de Proxy de aplicações do Azure AD.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: celested
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f96a67fecead65af28a975267590ebb08c715d3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57853017"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Compreender os conectores de Proxy de aplicações do Azure AD

Os conectores são o que possibilitam a Proxy de aplicação do Azure AD. Eles são super poderosa, simples e fácil de implantar e manter. Este artigo aborda os quais os conectores são, como eles funcionam e algumas sugestões sobre como otimizar a sua implementação. 

## <a name="what-is-an-application-proxy-connector"></a>O que é um conector de Proxy de aplicações?

Os conectores são agentes leves que residem no local e facilitam a ligação de saída para o serviço de Proxy de aplicações. Conectores tem de ser instalados num servidor do Windows que tenha acesso à aplicação de back-end. Pode organizar conectores em grupos de conector, com cada grupo de processamento de tráfego para aplicações específicas.

## <a name="requirements-and-deployment"></a>Requisitos e Implantações.

Para implementar o Proxy de aplicações com êxito, terá de, pelo menos, um conector, mas recomendamos dois ou mais para maior resiliência. Instale o conector no Windows Server 2012 R2 ou 2016 numa máquina. O conector tem de comunicar com o serviço de Proxy de aplicações e as aplicações no local que publicar. 

### <a name="windows-server"></a>Servidor do Windows
É necessário um servidor com o Windows Server 2012 R2 ou posterior, no qual pode instalar o conector de Proxy de aplicações. O servidor tem de ligar a serviços de Proxy de aplicações no Azure e as aplicações no local que está a publicar.

O windows server tem de ter o TLS 1.2, ativado antes de instalar o conector do Proxy de aplicações. Os conectores existentes com versões anteriores ao 1.5.612.0 irão continuar a trabalhar em versões anteriores do TLS até indicações em contrário. Para ativar o TLS 1.2:

1. Defina as seguintes chaves de registo:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

2. Reinicie o servidor


Para obter mais informações sobre os requisitos de rede para o servidor do conector, consulte [introdução ao Proxy da aplicação e instalar um conector](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Manutenção
Os conectores e o serviço de cuidam de todas as tarefas de elevada disponibilidade. Eles podem ser adicionados ou removidos dinamicamente. Sempre que uma nova solicitação chegar é encaminhado para um dos conectores que está atualmente disponível. Se um conector temporariamente não estiver disponível, ele não responde a este tráfego.

Os conectores são sem monitoração de estado e não tem nenhum dado de configuração na máquina. Os únicos dados que armazenam são as definições para ligar o serviço e seu certificado de autenticação. Quando se ligam ao serviço, eles extrair todos os dados de configuração necessárias e atualização-la cada alguns minutos.

Conectores também sonde o servidor para descobrir se existe uma versão mais recente do conector. Se for encontrado um, os conectores atualizam a próprios.

Pode monitorizar os conectores da máquina em que são executadas, utilizando o registo de eventos e contadores de desempenho. Ou pode ver o respetivo estado da página de Proxy de aplicações do portal do Azure:

 ![Conectores de Proxy de aplicações do AzureAD](./media/application-proxy-connectors/app-proxy-connectors.png)

Não tem de eliminar manualmente os conectores que não são utilizados. Quando um conector está em execução, permanece ativa como se liga ao serviço. Os conectores não utilizados são etiquetados como _Inativos_ e são removidas depois de 10 dias de inatividade. Se pretender desinstalar um conector, desinstale no entanto, o serviço de conectores e o serviço Atualizador do servidor. Reinicie o computador para remover completamente o serviço.

## <a name="automatic-updates"></a>Atualizações automáticas

O Azure AD fornece as atualizações automáticas para todos os conectores que implementar. Desde que o serviço Atualizador do conector de Proxy de aplicação está em execução, os conectores atualizar automaticamente. Se não vir o serviço Atualizador do conector no seu servidor, terá [reinstalar o conector](application-proxy-add-on-premises-application.md) para obter todas as atualizações. 

Se não quiser aguardar que uma atualização automática para o conector, pode fazer uma atualização manual. Vá para o [página de transferência de conector](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) no servidor onde o conector está localizado e selecione **transferir**. Este processo inicia-se uma atualização para o conector local. 

Inquilinos com múltiplos conectores, as atualizações automáticas como destino um conector numa altura em cada grupo para impedir que o tempo de inatividade no seu ambiente. 

Poderá notar um período de indisponibilidade quando o seu conector atualiza se:  
- Tem apenas um conector, recomendamos que instale um segundo conector e [criar um grupo de conectores](application-proxy-connector-groups.md). Isso evitar períodos de indisponibilidade e fornecer uma disponibilidade mais elevada.  
- Um conector foi no meio de uma transação quando a atualização começou. Embora a transação inicial é perdida, o navegador automaticamente deve repetir a operação ou pode atualizar sua página. Quando o pedido é reenviado, o tráfego é encaminhado para um conector de cópia de segurança.

## <a name="creating-connector-groups"></a>Criar grupos de conector

Grupos de conectores permitem-lhe atribuir conectores específicos para servir aplicativos específicos. Pode agrupar vários conectores e, em seguida, atribua cada aplicativo a um grupo. 

Grupos de conectores tornam mais fácil de gerenciar grandes Implantações. Eles também melhorem latência para inquilinos que tenham aplicações alojadas em regiões diferentes, uma vez que pode criar grupos com base na localização do conector para servir de aplicações apenas locais. 

Para saber mais sobre os grupos de conector, veja [publicar aplicações em redes separadas e localizações de utilização de grupos de conector](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Planeamento da Capacidade 

É importante certificar-se de que tenha se planejado capacidade suficiente entre conectores para processar o volume de tráfego esperado. Em geral, quanto mais usuários que tiver, o maior uma máquina será necessário. Segue-se uma tabela, fornecendo uma descrição do volume máquinas diferentes pode processar. Tenha em atenção de que tudo se baseia no esperado transações por segundo (TPS) em vez de por utilizador desde utilização padrões variam e não podem ser utilizados para prever a carga. Também haverá algumas diferenças com base no tamanho das respostas e o tempo de resposta da aplicação de back-end - tamanhos maiores de resposta e tempos de resposta mais lentos irão resultar num TPS máximo inferior. Recomendamos ter máquinas adicionais para que a carga distribuída pelas máquinas é cerca de 50%. A capacidade extra serão Certifique-se de que tem elevada disponibilidade e resiliência.

|Núcleos|RAM|Era esperado latência (MS)-P99|TPS máx.|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\* Esta máquina usada uma definição personalizada para gerar alguns dos limites de conexão padrão, além do .NET, as definições recomendadas. Recomendamos executar um teste com as predefinições antes de contactar o suporte para resolver este limite foi alterado para o seu inquilino.
 
>[!NOTE]
>Não há muita diferença nos TPS máximos entre 4, 8 e 16 máquinas de núcleo. É a principal diferença entre aqueles na latência esperada.  

## <a name="security-and-networking"></a>Rede e de segurança

Conectores podem ser instalados em qualquer lugar da rede que permita que sejam enviar pedidos para o serviço de Proxy de aplicações. O que é importante é que o computador que executa o conector também tem acesso às suas aplicações. Pode instalar os conectores dentro da sua rede empresarial ou numa máquina virtual que é executado na cloud. Conectores podem ser executados dentro de uma zona desmilitarizada (DMZ), mas não é necessário porque todo o tráfego é de saída para que a sua rede se mantém segura.

Conectores só enviam solicitações de saída. O tráfego de saída é enviado para o serviço de Proxy da aplicação e para as aplicações publicadas. Não tem de abrir portas porque o tráfego flui ambos os sentidos depois de uma sessão é estabelecida. Também não tem de configurar o acesso de entrada através de seus firewalls. 

Para obter mais informações sobre como configurar regras de firewall de saída, consulte [funcionam com o existente no local servidores proxy](application-proxy-configure-connectors-with-proxy-servers.md).


## <a name="performance-and-scalability"></a>Desempenho e escalabilidade

Escala para o serviço de Proxy de aplicações é transparente, mas o dimensionamento é um fator para os conectores. Tem de ter suficiente conectores para processar o tráfego de pico. Uma vez que os conectores são sem monitoração de estado, eles não são afetados pelo número de utilizadores ou sessões. Em vez disso, eles respondem ao número de pedidos e seu tamanho de payload. Com o tráfego da web padrão, uma máquina média pode processar alguns milhares pedidos por segundo. A capacidade específica depende as características de computador exata. 

O desempenho do conector está vinculado à CPU e de rede. Desempenho de CPU é necessário para encriptação SSL e desencriptação, enquanto o sistema de rede é importante para obter conectividade rápida para as aplicações e o serviço online no Azure.

Por outro lado, a memória é menor de um problema para os conectores. O serviço online se encarrega de grande parte do processamento e todo o tráfego não autenticado. Tudo o que pode ser feito na cloud é feito na cloud. 

Se por qualquer motivo que o conector ou máquina fica indisponível, o tráfego começará a vai outro conector do grupo. Este resiliência também é por que motivo Recomendamos ter vários conectores.

Outro fator que afeta o desempenho é a qualidade do sistema de rede entre os conectores, incluindo: 

* **O serviço online**: Ligações lentas ou de alta latência para o serviço de Proxy de aplicações no Azure influenciam o desempenho do conector. Para obter o melhor desempenho, ligue a sua organização para o Azure com o Express Route. Caso contrário, tem a sua equipa de rede, certifique-se de que as ligações para o Azure são processadas mais eficientemente possível. 
* **As aplicações de back-end**: Em alguns casos, há proxies adicionais entre o conector e as aplicações de back-end que podem tornar mais lenta ou impedir conexões. Para resolver problemas neste cenário, abra um browser a partir do servidor do conector e tente aceder à aplicação. Se executar os conectores no Azure, mas os aplicativos estão no local, a experiência poderá não ser o que os usuários esperam.
* **Os controladores de domínio**: Se os conectores de efetuar logon único (SSO) com a delegação restrita de Kerberos, eles entre em contato com os controladores de domínio antes de enviar o pedido para o back-end. Os conectores tenham uma cache de tíquetes Kerberos, mas num ambiente ocupado a capacidade de resposta dos controladores de domínio pode afetar o desempenho. Este problema é mais comum para os conectores que executam no Azure, mas se comunicar com os controladores de domínio que estão no local. 

Para obter mais informações sobre como otimizar sua rede, consulte [considerações sobre a topologia de rede ao utilizar o Proxy de aplicações do Azure Active Directory](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Associação a domínios

Conectores podem ser executados num computador que não está associado ao domínio. No entanto, se desejar início de sessão único (SSO) para aplicações que utilizam a autenticação integrada do Windows (IWA), precisa de uma máquina associados a um domínio. Neste caso, as máquinas de conector tem de estar associadas a um domínio que pode realizar [Kerberos](https://web.mit.edu/kerberos) delegação restrita em nome dos utilizadores para as aplicações publicadas.

Conectores também podem ser associados a domínios ou florestas que tenham uma relação de confiança parcial ou a controladores de domínio só de leitura.

## <a name="connector-deployments-on-hardened-environments"></a>Implementações de conector em ambientes protegidas

Normalmente, a implementação do conector é simples e não requer nenhuma configuração especial. No entanto, existem algumas condições exclusivas que devem ser consideradas:

* Tem das organizações que limitam o tráfego de saída [abrir as portas necessárias](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).
* Poderão ser necessário máquinas compatíveis com FIPS para alterar suas configurações para permitir que os processos de conector gerar e armazenar um certificado.
* As organizações que bloquear o seu ambiente com base nos processos que emitem os pedidos de rede tem de certificar-se de que ambos os serviços de conector estão ativados para aceder a todas as portas necessárias e IPs.
* Em alguns casos, os proxies de encaminhamento de saída podem interromper a autenticação de certificado bidirecional e fazer com que a comunicação efetuar a ativação.

## <a name="connector-authentication"></a>Autenticação do conector

Para fornecer um serviço seguro, tem de autenticar para o serviço de conectores e o serviço tem de autenticar para o conector. Esta autenticação é feita usando certificados de cliente e servidor aos conectores de iniciar a ligação. Dessa forma o nome de utilizador e palavra-passe do administrador não são armazenadas na máquina de conector.

Os certificados utilizados são específicos para o serviço de Proxy de aplicações. Eles são criados durante o registo inicial e são automaticamente renovadas pelos conectores a cada dois meses. 

Se um conector não estiver ligado ao serviço para vários meses, seus certificados poderão estar desatualizados. Neste caso, desinstale e reinstale o conector para o registo do acionador. Pode executar os seguintes comandos do PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="under-the-hood"></a>Nos bastidores

Conectores baseiam-se no Proxy de aplicações Web do servidor Windows, pelo que têm a maior parte as mesmas ferramentas de gestão, incluindo registos de eventos do Windows

 ![Gerir registos de eventos com o Visualizador de eventos](./media/application-proxy-connectors/event-view-window.png)

e contadores de desempenho do Windows. 

 ![Adicionar contadores para o conector com o Monitor de desempenho](./media/application-proxy-connectors/performance-monitor.png)

Os conectores tem admin e sessão de registos. Os registos de administrador incluem eventos principais e os erros. Os registos de sessão incluem todas as transações e os respetivos detalhes de processamento. 

Para ver os registos, vá para o Visualizador de eventos, abra a **View** menu e habilite **Mostrar analíticas e registos de depuração**. Em seguida, ativá-las iniciar a recolha de eventos. Estes registos não aparecem no Proxy de aplicações Web no Windows Server 2012 R2, como os conectores baseiam-se uma versão mais recente.

Pode examinar o estado do serviço na janela de serviços. O conector é constituído por dois serviços do Windows: o conector real e o atualizador. Ambos tem de executar o tempo todo.

 ![Local de serviços do AzureAD](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Passos Seguintes


* [Publicar aplicações em redes separadas e localizações através de grupos de conectores](application-proxy-connector-groups.md)
* [Trabalhar com servidores de proxy no local existentes](application-proxy-configure-connectors-with-proxy-servers.md)
* [Resolver erros de Proxy de aplicações e o conector](application-proxy-troubleshoot.md)
* [Como instalar automaticamente o conector de Proxy de aplicações do Azure AD](application-proxy-register-connector-powershell.md)

