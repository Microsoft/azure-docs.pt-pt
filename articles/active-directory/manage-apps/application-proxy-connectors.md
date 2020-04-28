---
title: Compreender os conectores de procuração de aplicação da AD Azure [ Microsoft Docs
description: Cobre o básico sobre conectores de procuração de aplicação ad azure.
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
ms.openlocfilehash: 7f1b8b9af8f90629d087246edf0cb3426bd9b66c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81406835"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Compreender os conectores de procuração de aplicação da AD Azure

Os conectores são o que torna possível a procuração de aplicação ad-ad azure. São simples, fáceis de implantar e manter, e super poderosos. Este artigo discute quais são os conectores, como funcionam e algumas sugestões para otimizar a sua implementação.

## <a name="what-is-an-application-proxy-connector"></a>O que é um conector proxy de aplicação?

Os conectores são agentes leves que se sentam no local e facilitam a ligação de saída ao serviço proxy de aplicação. Os conectores devem ser instalados num Servidor do Windows que tenha acesso à aplicação backend. Pode organizar conectores em grupos de conectores, com cada grupo a manusear o tráfego para aplicações específicas.

## <a name="requirements-and-deployment"></a>Requisitos e implantação

Para implementar o Application Proxy com sucesso, precisa de pelo menos um conector, mas recomendamos dois ou mais para uma maior resiliência. Instale o conector numa máquina que executa o Windows Server 2012 R2 ou mais tarde. O conector precisa de comunicar com o serviço Proxy de Aplicação e as aplicações no local que publica.

### <a name="windows-server"></a>Windows server
Precisa de um servidor que execute o Windows Server 2012 R2 ou mais tarde no qual possa instalar o conector Proxy da aplicação. O servidor precisa de se ligar aos serviços de Procuração de Aplicações em Azure e às aplicações no local que está a publicar.

O servidor do Windows precisa de ter TLS 1.2 ativado antes de instalar o conector Proxy da Aplicação. Para ativar o TLS 1.2 no servidor:

1. Detete as seguintes teclas de registo:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Reiniciar o servidor

Para obter mais informações sobre os requisitos de rede para o servidor de conector, consulte Iniciar com procuração de [aplicação e instalar um conector](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Manutenção

Os conectores e o serviço cuidam de todas as tarefas de alta disponibilidade. Podem ser adicionados ou removidos dinamicamente. Cada vez que um novo pedido chega é encaminhado para um dos conectores que está atualmente disponível. Se um conector não estiver temporariamente disponível, não responde a este tráfego.

Os conectores são apátridas e não têm dados de configuração na máquina. Os únicos dados que armazenam são as definições para ligar o serviço e o seu certificado de autenticação. Quando se ligam ao serviço, retiram todos os dados de configuração necessários e refrescam-nos a cada dois minutos.

Os conectores também pesquisam o servidor para saber se existe uma versão mais recente do conector. Se um for encontrado, os conectores atualizam-se.

Pode monitorizar os seus conectores a partir da máquina em que estão a funcionar, utilizando o registo de eventos e os contadores de desempenho. Ou pode ver o seu estado a partir da página de Procuração de Aplicações do portal Azure:

![Exemplo: Conectores de proxy de aplicação da AD Azure](./media/application-proxy-connectors/app-proxy-connectors.png)

Não é preciso apagar manualmente conectores que não sejam utilizados. Quando um conector está em funcionamento, permanece ativo à medida que se liga ao serviço. Os conectores não utilizados são marcados como _inativos_ e são removidos após 10 dias de inatividade. No entanto, se pretender desinstalar um conector, desinstale tanto o serviço DeConector como o serviço Updater a partir do servidor. Reinicie o computador para remover completamente o serviço.

## <a name="automatic-updates"></a>Atualizações automáticas

A Azure AD fornece atualizações automáticas para todos os conectores que implementa. Enquanto o serviço de Atualização do Coector proxy da aplicação estiver em funcionamento, os seus conectores atualizam-se automaticamente. Se não vir o serviço de Atualização do Conector no seu servidor, precisa de [reinstalar o conector](application-proxy-add-on-premises-application.md) para obter quaisquer atualizações.

Se não quiser esperar que uma atualização automática chegue ao seu conector, pode fazer uma atualização manual. Vá à página de [descarregamento do conector](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) no servidor onde o conector está localizado e selecione **Download**. Este processo inicia uma atualização para o conector local.

Para os inquilinos com vários conectores, as atualizações automáticas visam um conector de cada vez em cada grupo para evitar o tempo de inatividade no seu ambiente.

Pode experimentar tempo de inatividade quando o seu conector atualizar se:
  
- Só tem um conector, recomendamos que instale um segundo conector e [crie um grupo de conector](application-proxy-connector-groups.md). Isto evitará o tempo de inatividade e proporcionará uma maior disponibilidade.  
- Um conector estava no meio de uma transação quando a atualização começou. Embora a transação inicial se perca, o seu navegador deve rejulgar automaticamente a operação ou poderá atualizar a sua página. Quando o pedido é ressentido, o tráfego é encaminhado para um conector de reserva.

Para ver informações sobre versões previamente lançadas e quais as alterações que incluem, consulte o Histórico de Lançamento da [Versão Proxy de Aplicação](application-proxy-release-version-history.md).

## <a name="creating-connector-groups"></a>Criação de grupos de conector

Os grupos de conectores permitem-lhe atribuir conectores específicos para servir aplicações específicas. Pode agrupar vários conectores e, em seguida, atribuir cada aplicação a um grupo.

Os grupos de conector facilitam a gestão de grandes implantações. Também melhoram a latência para os inquilinos que têm candidaturas alojadas em diferentes regiões, porque você pode criar grupos de conector baseados em localização para servir apenas aplicações locais.

Para saber mais sobre grupos de conectores, consulte [Publicar aplicações em redes e locais separados utilizando grupos de conector](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Planeamento de capacidade

É importante certificar-se de que planeou capacidade suficiente entre conectores para lidar com o volume de tráfego esperado. Recomendamos que cada grupo de conectores tenha pelo menos dois conectores para fornecer alta disponibilidade e escala. Ter três conectores é ideal para o caso de precisar de servir uma máquina em qualquer ponto.

Em geral, quanto mais utilizadores tiveres, maior será a máquina de que vais precisar. Abaixo está uma tabela que dá um esboço do volume e da latência esperada que diferentes máquinas podem manusear. Note que tudo se baseia em transações esperadas por segundo (TPS) e não pelo utilizador, uma vez que os padrões de utilização variam e não podem ser usados para prever a carga. Haverá também algumas diferenças com base no tamanho das respostas e no tempo de resposta à aplicação de backend - tamanhos de resposta maiores e tempos de resposta mais lentos resultarão num TPS Max mais baixo. Recomendamos também que tenha máquinas adicionais para que a carga distribuída através das máquinas forneça sempre um tampão amplo. A capacidade extra irá garantir que você tem alta disponibilidade e resiliência.

|Núcleos|RAM|Latência Esperada (MS)-P99|Max TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\*Esta máquina utilizou uma definição personalizada para elevar alguns dos limites de ligação padrão para além das definições recomendadas .NET. Recomendamos a realização de um teste com as definições predefinidas antes de contactar o suporte para alterar este limite para o seu inquilino.

> [!NOTE]
> Não há muita diferença no TPS máximo entre 4, 8 e 16 máquinas de núcleo. A principal diferença entre estes está na latência esperada.
>
> Esta tabela também se centra no desempenho esperado de um conector baseado no tipo de máquina em que está instalada. Isto é separado dos limites de estrangulamento do serviço application Proxy, ver [limites de serviço e restrições](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="security-and-networking"></a>Segurança e networking

Os conectores podem ser instalados em qualquer lugar da rede que lhes permita enviar pedidos para o serviço Proxy de Aplicação. O importante é que o computador que executa o conector também tenha acesso às suas aplicações. Pode instalar conectores dentro da sua rede corporativa ou numa máquina virtual que funciona na nuvem. Os conectores podem funcionar dentro de uma rede de perímetro, também conhecida como zona desmilitarizada (DMZ), mas não é necessário porque todo o tráfego está de saída para que a sua rede permaneça segura.

Os conectores só enviam pedidos de saída. O tráfego de saída é enviado para o serviço de Procuração de Aplicações e para as aplicações publicadas. Não é preciso abrir portos de entrada porque o tráfego flui para os dois lados uma vez que uma sessão é estabelecida. Também não tem de configurar o acesso de entrada através das suas firewalls.

Para obter mais informações sobre a configuração das regras de firewall de saída, consulte [O Trabalho com os servidores proxy existentes no local](application-proxy-configure-connectors-with-proxy-servers.md).

## <a name="performance-and-scalability"></a>Desempenho e escalabilidade

A escala para o serviço Proxy de Aplicação é transparente, mas a escala é um fator para conectores. Tens de ter conectores suficientes para lidar com o tráfego máximo. Uma vez que os conectores são apátridas, não são afetados pelo número de utilizadores ou sessões. Em vez disso, respondem ao número de pedidos e ao seu tamanho de carga útil. Com o tráfego web padrão, uma máquina média pode lidar com alguns milhares de pedidos por segundo. A capacidade específica depende das características exatas da máquina.

O desempenho do conector está ligado à CPU e à rede. O desempenho do CPU é necessário para a encriptação e desencriptação do TLS, enquanto o networking é importante para obter uma conectividade rápida com as aplicações e o serviço online em Azure.

Em contraste, a memória é menos um problema para os conectores. O serviço online cuida de grande parte do processamento e de todo o tráfego não autenticado. Tudo o que pode ser feito na nuvem é feito na nuvem.

Se, por qualquer motivo, o conector ou a máquina ficarem indisponíveis, o tráfego começará a ir para outro conector do grupo. Esta resiliência é também a razão pela qual recomendamos ter múltiplos conectores.

Outro fator que afeta o desempenho é a qualidade da ligação em rede entre os conectores, incluindo:

- **O serviço online**: Ligações lentas ou de alta latência ao serviço Proxy de Aplicação em Azure influenciam o desempenho do conector. Para obter o melhor desempenho, ligue a sua organização ao Azure com a Rota expresso. Caso contrário, certifique-se de que as ligações ao Azure são manuseadas da forma mais eficiente possível.
- **Aplicações de backend**: Em alguns casos, existem proxies adicionais entre o conector e as aplicações de backend que podem abrandar ou impedir ligações. Para resolver este cenário, abra um navegador a partir do servidor de conector e tente aceder à aplicação. Se executar os conectores em Azure, mas as aplicações estão no local, a experiência pode não ser o que os seus utilizadores esperam.
- **Os controladores**de domínio : Se os conectores executarem um único sinal (SSO) utilizando a Delegação Limitada kerberos, contactam os controladores de domínio antes de enviarem o pedido para o backend. Os conectores têm uma cache de bilhetes Kerberos, mas num ambiente movimentado a capacidade de resposta dos controladores de domínio pode afetar o desempenho. Esta questão é mais comum para conectores que funcionam em Azure mas comunicam com controladores de domínio que estão no local.

Para obter mais informações sobre a otimização da sua rede, consulte considerações de topologia da rede ao utilizar o Proxy de Aplicação de [Diretório Ativo Azure](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Adesão ao domínio

Os conectores podem funcionar numa máquina que não é unida ao domínio. No entanto, se pretender um único sinal (SSO) para aplicações que utilizem autenticação integrada do Windows (IWA), precisa de uma máquina unida para o domínio. Neste caso, as máquinas de conector devem ser unidas a um domínio que possa realizar a Delegação Limitada [kerberos](https://web.mit.edu/kerberos) em nome dos utilizadores para as aplicações publicadas.

Os conectores também podem ser unidos a domínios ou florestas que tenham uma confiança parcial, ou a controladores de domínio apenas de leitura.

## <a name="connector-deployments-on-hardened-environments"></a>Implantações de conector em ambientes endurecidos

Normalmente, a implementação do conector é simples e não requer uma configuração especial. No entanto, existem algumas condições únicas que devem ser consideradas:

- As organizações que limitam o tráfego de saída devem [abrir portas obrigatórias.](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)
- Máquinas compatíveis com FIPS podem ser obrigadas a alterar a sua configuração para permitir que os processos de conector gerem e armazenem um certificado.
- As organizações que bloqueiam o seu ambiente com base nos processos que emitem os pedidos de networking têm de se certificar de que ambos os serviços de conector estão habilitados a aceder a todas as portas e IPs necessários.
- Em alguns casos, os proxies avançados de saída podem quebrar a autenticação do certificado bidirecional e fazer com que a comunicação falhe.

## <a name="connector-authentication"></a>Autenticação do conector

Para fornecer um serviço seguro, os conectores têm de autenticar em direção ao serviço, e o serviço tem de autenticar em direção ao conector. Esta autenticação é feita utilizando certificados de cliente e servidor quando os conectores iniciam a ligação. Desta forma, o nome de utilizador e a palavra-passe do administrador não são armazenados na máquina do conector.

Os certificados utilizados são específicos do serviço de procuração de aplicações. São criados durante o registo inicial e são automaticamente renovados pelos conectores a cada dois meses.

Após a primeira renovação bem sucedida do certificado, o serviço de conector proxy de aplicação da AD Azure (Serviço de Rede) não tem autorização para remover o antigo certificado da loja de máquinas local. Se o certificado tiver expirado ou não for mais utilizado pelo serviço, pode eliminá-lo com segurança.

Para evitar problemas com a renovação do certificado, certifique-se de que a comunicação da rede do conector para os [destinos documentados](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment) está ativada.

Se um conector não estiver ligado ao serviço durante vários meses, os seus certificados podem estar desatualizados. Neste caso, desinstale e reinstale o conector para acionar o registo. Pode executar os seguintes comandos PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```
Para saber mais sobre como verificar o certificado e problemas de resolução de problemas consulte Verificar Máquina e suporte de [componentes de backend para certificado fidedigno proxy](application-proxy-connector-installation-problem.md#verify-machine-and-backend-components-support-for-application-proxy-trust-certificate)de aplicação .

## <a name="under-the-hood"></a>Os bastidores

Os conectores baseiam-se no Proxy da Aplicação Web do Windows Server, pelo que possuem a maioria das mesmas ferramentas de gestão, incluindo registos de eventos do Windows

![Gerir registos de eventos com o Espectador de Eventos](./media/application-proxy-connectors/event-view-window.png)

e contadores de desempenho do Windows.

![Adicione contadores ao conector com o Monitor de Desempenho](./media/application-proxy-connectors/performance-monitor.png)

Os conectores têm registos de administração e sessão. Os registos de administração incluem eventos-chave e seus erros. Os registos da sessão incluem todas as transações e os seus detalhes de processamento.

Para ver os registos, vá ao Espectador de Eventos, abra o menu **'Ver'** e ative **registos analíticos e depurados**. Em seguida, permita-lhes começar a recolher eventos. Estes registos não aparecem no Proxy da Aplicação Web no Windows Server 2012 R2, uma vez que os conectores são baseados numa versão mais recente.

Pode examinar o estado do serviço na janela dos Serviços. O conector é composto por dois Serviços Windows: o conector real e o atualização. Ambos devem correr a toda a hora.

 ![Exemplo: Janela de serviços mostrando serviços azure ad local](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Passos seguintes

- [Publicar aplicações em redes e locais separados utilizando grupos de conector](application-proxy-connector-groups.md)
- [Trabalhar com servidores proxy existentes no local](application-proxy-configure-connectors-with-proxy-servers.md)
- [Erro de procuração de aplicação de resolução de problemas e erro de conector](application-proxy-troubleshoot.md)
- [Como instalar silenciosamente o Conector proxy de aplicação ad azure](application-proxy-register-connector-powershell.md)
