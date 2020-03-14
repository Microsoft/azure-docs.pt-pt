---
title: Compreender os conectores de Proxy de aplicações do Azure AD | Documentos da Microsoft
description: Abrange as noções básicas sobre os conectores de Proxy de aplicações do Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c2036bf9995725e4bbef44e4c039f8336eb81a0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79244293"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Compreender os conectores de Proxy de aplicações do Azure AD

Os conectores são o que possibilitam a Proxy de aplicação do Azure AD. São simples, fáceis de implantar e manter, e super poderosos. Este artigo aborda os quais os conectores são, como eles funcionam e algumas sugestões sobre como otimizar a sua implementação.

## <a name="what-is-an-application-proxy-connector"></a>O que é um conector de Proxy de aplicações?

Os conectores são agentes leves que residem no local e facilitam a ligação de saída para o serviço de Proxy de aplicações. Conectores tem de ser instalados num servidor do Windows que tenha acesso à aplicação de back-end. Pode organizar conectores em grupos de conector, com cada grupo de processamento de tráfego para aplicações específicas.

## <a name="requirements-and-deployment"></a>Requisitos e Implantações.

Para implementar o Proxy de aplicações com êxito, terá de, pelo menos, um conector, mas recomendamos dois ou mais para maior resiliência. Instale o conector numa máquina que executa o Windows Server 2012 R2 ou mais tarde. O conector precisa de comunicar com o serviço Proxy de Aplicação e as aplicações no local que publica.

### <a name="windows-server"></a>Servidor do Windows
É necessário um servidor com o Windows Server 2012 R2 ou posterior, no qual pode instalar o conector de Proxy de aplicações. O servidor precisa de se ligar aos serviços de Procuração de Aplicações em Azure e às aplicações no local que está a publicar.

O windows server tem de ter o TLS 1.2, ativado antes de instalar o conector do Proxy de aplicações. Para ativar o TLS 1.2 no servidor:

1. Defina as seguintes chaves de registo:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Reinicie o servidor

Para obter mais informações sobre os requisitos de rede para o servidor de conector, consulte Iniciar com procuração de [aplicação e instalar um conector](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Manutenção

Os conectores e o serviço de cuidam de todas as tarefas de elevada disponibilidade. Eles podem ser adicionados ou removidos dinamicamente. Sempre que uma nova solicitação chegar é encaminhado para um dos conectores que está atualmente disponível. Se um conector temporariamente não estiver disponível, ele não responde a este tráfego.

Os conectores são sem monitoração de estado e não tem nenhum dado de configuração na máquina. Os únicos dados que armazenam são as definições para ligar o serviço e seu certificado de autenticação. Quando se ligam ao serviço, eles extrair todos os dados de configuração necessárias e atualização-la cada alguns minutos.

Conectores também sonde o servidor para descobrir se existe uma versão mais recente do conector. Se for encontrado um, os conectores atualizam a próprios.

Pode monitorizar os conectores da máquina em que são executadas, utilizando o registo de eventos e contadores de desempenho. Ou pode ver o respetivo estado da página de Proxy de aplicações do portal do Azure:

![Exemplo: Conectores de proxy de aplicação da AD Azure](./media/application-proxy-connectors/app-proxy-connectors.png)

Não tem de eliminar manualmente os conectores que não são utilizados. Quando um conector está em execução, permanece ativa como se liga ao serviço. Os conectores não utilizados são marcados como _inativos_ e são removidos após 10 dias de inatividade. Se pretender desinstalar um conector, desinstale no entanto, o serviço de conectores e o serviço Atualizador do servidor. Reinicie o computador para remover completamente o serviço.

## <a name="automatic-updates"></a>Atualizações automáticas

O Azure AD fornece as atualizações automáticas para todos os conectores que implementar. Desde que o serviço Atualizador do conector de Proxy de aplicação está em execução, os conectores atualizar automaticamente. Se não vir o serviço de Atualização do Conector no seu servidor, precisa de [reinstalar o conector](application-proxy-add-on-premises-application.md) para obter quaisquer atualizações.

Se não quiser esperar que uma atualização automática chegue ao seu conector, pode fazer uma atualização manual. Vá à página de [descarregamento do conector](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) no servidor onde o conector está localizado e selecione **Download**. Este processo inicia-se uma atualização para o conector local.

Inquilinos com múltiplos conectores, as atualizações automáticas como destino um conector numa altura em cada grupo para impedir que o tempo de inatividade no seu ambiente.

Poderá notar um período de indisponibilidade quando o seu conector atualiza se:
  
- Só tem um conector, recomendamos que instale um segundo conector e [crie um grupo de conector](application-proxy-connector-groups.md). Isto evitará o tempo de inatividade e proporcionará uma maior disponibilidade.  
- Um conector foi no meio de uma transação quando a atualização começou. Embora a transação inicial é perdida, o navegador automaticamente deve repetir a operação ou pode atualizar sua página. Quando o pedido é reenviado, o tráfego é encaminhado para um conector de cópia de segurança.

Para ver informações sobre versões previamente lançadas e quais as alterações que incluem, consulte o Histórico de Lançamento da [Versão Proxy de Aplicação](application-proxy-release-version-history.md).

## <a name="creating-connector-groups"></a>Criar grupos de conector

Grupos de conectores permitem-lhe atribuir conectores específicos para servir aplicativos específicos. Pode agrupar vários conectores e, em seguida, atribua cada aplicativo a um grupo.

Grupos de conectores tornam mais fácil de gerenciar grandes Implantações. Eles também melhorem latência para inquilinos que tenham aplicações alojadas em regiões diferentes, uma vez que pode criar grupos com base na localização do conector para servir de aplicações apenas locais.

Para saber mais sobre grupos de conectores, consulte [Publicar aplicações em redes e locais separados utilizando grupos de conector](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Planeamento da capacidade

É importante certificar-se de que planeou capacidade suficiente entre conectores para lidar com o volume de tráfego esperado. Recomendamos que cada grupo de conectores tenha pelo menos dois conectores para fornecer alta disponibilidade e escala. Ter três conectores é ideal para o caso de precisar de servir uma máquina em qualquer ponto.

Em geral, quanto mais utilizadores tiveres, maior será a máquina de que vais precisar. Abaixo está uma tabela que dá um esboço do volume e da latência esperada que diferentes máquinas podem manusear. Note que tudo se baseia em transações esperadas por segundo (TPS) e não pelo utilizador, uma vez que os padrões de utilização variam e não podem ser usados para prever a carga. Haverá também algumas diferenças com base no tamanho das respostas e no tempo de resposta à aplicação de backend - tamanhos de resposta maiores e tempos de resposta mais lentos resultarão num TPS Max mais baixo. Recomendamos também que tenha máquinas adicionais para que a carga distribuída através das máquinas forneça sempre um tampão amplo. A capacidade extra irá garantir que você tem alta disponibilidade e resiliência.

|Núcleos|RAM|Era esperado latência (MS)-P99|TPS máx.|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\* Esta máquina utilizou uma definição personalizada para elevar alguns dos limites de ligação padrão para além das definições recomendadas .NET. Recomendamos executar um teste com as predefinições antes de contactar o suporte para resolver este limite foi alterado para o seu inquilino.

> [!NOTE]
> Não há muita diferença nos TPS máximos entre 4, 8 e 16 máquinas de núcleo. É a principal diferença entre aqueles na latência esperada.
>
> Esta tabela também se centra no desempenho esperado de um conector baseado no tipo de máquina em que está instalada. Isto é separado dos limites de estrangulamento do serviço application Proxy, ver [limites de serviço e restrições](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="security-and-networking"></a>Rede e de segurança

Conectores podem ser instalados em qualquer lugar da rede que permita que sejam enviar pedidos para o serviço de Proxy de aplicações. O que é importante é que o computador que executa o conector também tem acesso às suas aplicações. Pode instalar os conectores dentro da sua rede empresarial ou numa máquina virtual que é executado na cloud. Os conectores podem funcionar dentro de uma rede de perímetro, também conhecida como zona desmilitarizada (DMZ), mas não é necessário porque todo o tráfego está de saída para que a sua rede permaneça segura.

Conectores só enviam solicitações de saída. O tráfego de saída é enviado para o serviço de Proxy da aplicação e para as aplicações publicadas. Não tem de abrir portas porque o tráfego flui ambos os sentidos depois de uma sessão é estabelecida. Também não tem de configurar o acesso de entrada através das suas firewalls.

Para obter mais informações sobre a configuração das regras de firewall de saída, consulte [O Trabalho com os servidores proxy existentes no local](application-proxy-configure-connectors-with-proxy-servers.md).

## <a name="performance-and-scalability"></a>Desempenho e escalabilidade

Escala para o serviço de Proxy de aplicações é transparente, mas o dimensionamento é um fator para os conectores. Tem de ter suficiente conectores para processar o tráfego de pico. Uma vez que os conectores são apátridas, não são afetados pelo número de utilizadores ou sessões. Em vez disso, eles respondem ao número de pedidos e seu tamanho de payload. Com o tráfego da web padrão, uma máquina média pode processar alguns milhares pedidos por segundo. A capacidade específica depende as características de computador exata.

O desempenho do conector está vinculado à CPU e de rede. Desempenho de CPU é necessário para encriptação SSL e desencriptação, enquanto o sistema de rede é importante para obter conectividade rápida para as aplicações e o serviço online no Azure.

Por outro lado, a memória é menor de um problema para os conectores. O serviço online se encarrega de grande parte do processamento e todo o tráfego não autenticado. Tudo o que pode ser feito na cloud é feito na cloud.

Se por qualquer motivo que o conector ou máquina fica indisponível, o tráfego começará a vai outro conector do grupo. Este resiliência também é por que motivo Recomendamos ter vários conectores.

Outro fator que afeta o desempenho é a qualidade do sistema de rede entre os conectores, incluindo:

- **O serviço online**: Ligações lentas ou de alta latência ao serviço Proxy de Aplicação em Azure influenciam o desempenho do conector. Para obter o melhor desempenho, ligue a sua organização para o Azure com o Express Route. Caso contrário, tem a sua equipa de rede, certifique-se de que as ligações para o Azure são processadas mais eficientemente possível.
- **Aplicações de backend**: Em alguns casos, existem proxies adicionais entre o conector e as aplicações de backend que podem abrandar ou impedir ligações. Para resolver problemas neste cenário, abra um browser a partir do servidor do conector e tente aceder à aplicação. Se executar os conectores no Azure, mas os aplicativos estão no local, a experiência poderá não ser o que os usuários esperam.
- **Os controladores**de domínio : Se os conectores executarem um único sinal (SSO) utilizando a Delegação Limitada kerberos, contactam os controladores de domínio antes de enviarem o pedido para o backend. Os conectores tenham uma cache de tíquetes Kerberos, mas num ambiente ocupado a capacidade de resposta dos controladores de domínio pode afetar o desempenho. Este problema é mais comum para os conectores que executam no Azure, mas se comunicar com os controladores de domínio que estão no local.

Para obter mais informações sobre a otimização da sua rede, consulte considerações de topologia da rede ao utilizar o Proxy de Aplicação de [Diretório Ativo Azure](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Associação a domínios

Conectores podem ser executados num computador que não está associado ao domínio. No entanto, se desejar início de sessão único (SSO) para aplicações que utilizam a autenticação integrada do Windows (IWA), precisa de uma máquina associados a um domínio. Neste caso, as máquinas de conector devem ser unidas a um domínio que possa realizar a Delegação Limitada [kerberos](https://web.mit.edu/kerberos) em nome dos utilizadores para as aplicações publicadas.

Conectores também podem ser associados a domínios ou florestas que tenham uma relação de confiança parcial ou a controladores de domínio só de leitura.

## <a name="connector-deployments-on-hardened-environments"></a>Implementações de conector em ambientes protegidas

Normalmente, a implementação do conector é simples e não requer nenhuma configuração especial. No entanto, existem algumas condições exclusivas que devem ser consideradas:

- As organizações que limitam o tráfego de saída devem [abrir portas obrigatórias.](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)
- Poderão ser necessário máquinas compatíveis com FIPS para alterar suas configurações para permitir que os processos de conector gerar e armazenar um certificado.
- As organizações que bloquear o seu ambiente com base nos processos que emitem os pedidos de rede tem de certificar-se de que ambos os serviços de conector estão ativados para aceder a todas as portas necessárias e IPs.
- Em alguns casos, os proxies de encaminhamento de saída podem interromper a autenticação de certificado bidirecional e fazer com que a comunicação efetuar a ativação.

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

Para ver os registos, vá ao Espectador de Eventos, abra o menu **'Ver'** e ative **registos analíticos e depurados**. Em seguida, ativá-las iniciar a recolha de eventos. Estes registos não aparecem no Proxy de aplicações Web no Windows Server 2012 R2, como os conectores baseiam-se uma versão mais recente.

Pode examinar o estado do serviço na janela de serviços. O conector é composto por dois Serviços Windows: o conector real e o atualização. Ambos tem de executar o tempo todo.

 ![Exemplo: Janela de serviços mostrando serviços azure ad local](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Passos Seguintes

- [Publicar aplicações em redes e locais separados utilizando grupos de conector](application-proxy-connector-groups.md)
- [Trabalhar com servidores proxy existentes no local](application-proxy-configure-connectors-with-proxy-servers.md)
- [Erro de procuração de aplicação de resolução de problemas e erro de conector](application-proxy-troubleshoot.md)
- [Como instalar silenciosamente o Conector proxy de aplicação ad azure](application-proxy-register-connector-powershell.md)
