---
title: Compreenda os conectores Proxy da aplicação AD Azure / Microsoft Docs
description: Saiba mais sobre os conectores Azure AD Application Proxy.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 923b83b388b58313e9613f0f8b71f266dcbeb028
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92282130"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Compreenda os conectores Proxy de aplicação AD Azure

Os conectores são o que torna possível o Proxy de Aplicação AD Azure. São simples, fáceis de implementar e manter, e super poderosos. Este artigo discute o que são os conectores, como funcionam e algumas sugestões de como otimizar a sua implementação.

## <a name="what-is-an-application-proxy-connector"></a>O que é um conector Application Proxy?

Os conectores são agentes leves que se sentam no local e facilitam a ligação de saída ao serviço Application Proxy. Os conectores devem ser instalados num Servidor windows que tenha acesso à aplicação backend. Pode organizar conectores em grupos de conector, com cada grupo a manusear o tráfego para aplicações específicas.

## <a name="requirements-and-deployment"></a>Requisitos e implantação

Para implementar o Proxy de aplicação com sucesso, precisa de pelo menos um conector, mas recomendamos dois ou mais para uma maior resiliência. Instale o conector numa máquina que executa o Windows Server 2012 R2 ou mais tarde. O conector necessita de comunicar com o serviço Application Proxy e com as aplicações no local que publica.

### <a name="windows-server"></a>Windows Server
Precisa de um servidor que execute o Windows Server 2012 R2 ou mais tarde no qual pode instalar o conector Proxy da aplicação. O servidor precisa de se ligar aos serviços de Procuração de Aplicações em Azure e às aplicações no local que está a publicar.

O servidor do windows necessita de ter o TLS 1.2 ativado antes de instalar o conector Application Proxy. Para ativar o TLS 1.2 no servidor:

1. Definir as seguintes chaves de registo:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Reiniciar o servidor

Para obter mais informações sobre os requisitos de rede para o servidor do conector, consulte [Começar com o Application Proxy e instalar um conector](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Manutenção

Os conectores e o serviço cuidam de todas as tarefas de alta disponibilidade. Podem ser adicionados ou removidos dinamicamente. Cada vez que um novo pedido chega é encaminhado para um dos conectores que está atualmente disponível. Se um conector não estiver temporariamente disponível, não responde a este tráfego.

Os conectores são apátridas e não têm dados de configuração na máquina. Os únicos dados que armazenam são as definições para a ligação do serviço e o seu certificado de autenticação. Quando se ligam ao serviço, retiram todos os dados de configuração necessários e atualizam-no a cada dois minutos.

Os conectores também pesquisam o servidor para saber se existe uma versão mais recente do conector. Se um for encontrado, os conectores atualizam-se sozinhos.

Pode monitorizar os seus conectores a partir da máquina em que estão a funcionar, utilizando os contadores de registo de eventos e de desempenho. Ou pode ver o seu estado a partir da página de Procuração de Aplicações do portal Azure:

![Exemplo: Conectores Proxy de aplicação AD AZure](./media/application-proxy-connectors/app-proxy-connectors.png)

Não é preciso eliminar manualmente os conectores que não são bem-de-usar. Quando um conector está em funcionamento, permanece ativo à medida que se liga ao serviço. Os conectores não reutilizados são marcados como _inativos_ e são removidos após 10 dias de inatividade. No entanto, se pretender desinstalar um conector, desinstale o serviço Desinsector e o serviço Updater a partir do servidor. Reinicie o computador para remover completamente o serviço.

## <a name="automatic-updates"></a>Atualizações automáticas

O Azure AD fornece atualizações automáticas para todos os conectores que implementa. Enquanto o serviço de atualização do conector de aplicação proxy estiver em funcionamento, os conectores atualizam-se automaticamente. Se não vir o serviço de atualização do Conector no seu servidor, tem de [reinstalar o seu conector](application-proxy-add-on-premises-application.md) para obter quaisquer atualizações.

Se não quiser esperar que uma atualização automática chegue ao seu conector, pode fazer uma atualização manual. Aceda à [página de descarregamento](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) do conector no servidor onde o seu conector está localizado e selecione **Download**. Este processo inicia uma atualização para o conector local.

Para os inquilinos com vários conectores, as atualizações automáticas direcionam-se para um conector de cada grupo para evitar tempo de inatividade no seu ambiente.

Poderá experimentar tempo de inatividade quando o seu conector atualizar se:
  
- Só tem um conector que recomendamos instalar um segundo conector e [criar um grupo de conector](application-proxy-connector-groups.md). Isto evitará o tempo de inatividade e proporcionará uma maior disponibilidade.  
- Um conector estava no meio de uma transação quando a atualização começou. Embora a transação inicial esteja perdida, o seu navegador deve repetir automaticamente a operação ou pode atualizar a sua página. Quando o pedido é ressentido, o tráfego é encaminhado para um conector de reserva.

Para ver informações sobre versões previamente lançadas e que alterações incluem, consulte [o Histórico de Lançamento da Versão Proxy da Aplicação](application-proxy-release-version-history.md).

## <a name="creating-connector-groups"></a>Criação de grupos de conector

Os grupos de conector permitem-lhe atribuir conectores específicos para servir aplicações específicas. Pode agrupar uma série de conectores e, em seguida, atribuir cada aplicação a um grupo.

Os grupos de conector facilitam a gestão de grandes implementações. Também melhoram a latência para inquilinos que têm aplicações hospedadas em diferentes regiões, porque você pode criar grupos de conector baseados em localização para servir apenas aplicações locais.

Para saber mais sobre grupos de conectores, consulte [publicar aplicações em redes e locais separados utilizando grupos de conectores](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Planeamento de capacidade

É importante certificar-se de que planeou capacidade suficiente entre os conectores para lidar com o volume de tráfego esperado. Recomendamos que cada grupo de conector tenha pelo menos dois conectores para fornecer alta disponibilidade e escala. Ter três conectores é ótimo no caso de precisar de servir uma máquina em qualquer ponto.

Em geral, quanto mais utilizadores tiver, maior será uma máquina. Abaixo está uma tabela que dá um esboço do volume e da latência esperada que diferentes máquinas podem manusear. Note que tudo se baseia em transações esperadas por segundo (TPS) e não pelo utilizador, uma vez que os padrões de utilização variam e não podem ser usados para prever a carga. Haverá também algumas diferenças com base no tamanho das respostas e no tempo de resposta da aplicação backend - tamanhos de resposta maiores e tempos de resposta mais lentos resultarão num Max TPS mais baixo. Recomendamos também que se disponibilizem máquinas adicionais para que a carga distribuída através das máquinas forneça sempre um tampão amplo. A capacidade extra irá garantir que você tem alta disponibilidade e resiliência.

|Núcleos|RAM|Latência esperada (MS)-P99|Max TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\* Esta máquina utilizou uma definição personalizada para elevar alguns dos limites de ligação predefinidos para além das definições recomendadas .NET. Recomendamos que faça um teste com as definições predefinidoras antes de contactar o suporte para que este limite seja alterado para o seu inquilino.

> [!NOTE]
> Não há muita diferença no TPS máximo entre 4, 8 e 16 máquinas nucleares. A principal diferença entre estes é a latência esperada.
>
> Esta tabela também se centra no desempenho esperado de um conector baseado no tipo de máquina em que está instalada. Isto é separado dos limites de estrangulamento do serviço Application Proxy, ver [limites de serviço e restrições](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="security-and-networking"></a>Segurança e networking

Os conectores podem ser instalados em qualquer lugar da rede que lhes permita enviar pedidos para o serviço Application Proxy. O importante é que o computador que executa o conector também tenha acesso às suas apps. Pode instalar conectores dentro da sua rede corporativa ou numa máquina virtual que funciona na nuvem. Os conectores podem funcionar dentro de uma rede de perímetro, também conhecida como uma zona desmilitarizada (DMZ), mas não é necessário porque todo o tráfego está de saída para que a sua rede permaneça segura.

Os conectores só enviam pedidos de saída. O tráfego de saída é enviado para o serviço Application Proxy e para as aplicações publicadas. Não é preciso abrir portas de entrada porque o tráfego flui para os dois lados assim que uma sessão é estabelecida. Também não tem de configurar o acesso de entrada através das suas firewalls.

Para obter mais informações sobre a configuração das regras de firewall de saída, consulte [Trabalhar com servidores proxy existentes no local](application-proxy-configure-connectors-with-proxy-servers.md).

## <a name="performance-and-scalability"></a>Desempenho e escalabilidade

A escala para o serviço Application Proxy é transparente, mas a escala é um fator para os conectores. É preciso ter conectores suficientes para lidar com o tráfego máximo. Uma vez que os conectores são apátridas, não são afetados pelo número de utilizadores ou sessões. Em vez disso, respondem ao número de pedidos e ao seu tamanho de carga útil. Com o tráfego web padrão, uma máquina média pode lidar com alguns milhares de pedidos por segundo. A capacidade específica depende das características exatas da máquina.

O desempenho do conector está ligado por CPU e networking. O desempenho do CPU é necessário para encriptação e desencriptação TLS, enquanto o networking é importante para obter uma conectividade rápida com as aplicações e o serviço online em Azure.

Em contraste, a memória é menos um problema para os conectores. O serviço online cuida de grande parte do processamento e de todo o tráfego não autenticado. Tudo o que pode ser feito na nuvem é feito na nuvem.

Se, por qualquer motivo, o conector ou a máquina não estiverem disponíveis, o tráfego começará a ir para outro conector do grupo. Esta resiliência é também a razão pela qual recomendamos ter múltiplos conectores.

Outro fator que afeta o desempenho é a qualidade da ligação em rede entre os conectores, incluindo:

- **O serviço online**: As ligações lentas ou de alta latência ao serviço Application Proxy em Azure influenciam o desempenho do conector. Para obter o melhor desempenho, ligue a sua organização ao Azure com a Rota Expressa. Caso contrário, certifique-se de que as ligações ao Azure são manuseadas da forma mais eficiente possível.
- Aplicações de backend : Em **alguns casos,** existem proxies adicionais entre o conector e as aplicações de backend que podem retardar ou impedir ligações. Para resolver este cenário, abra um navegador a partir do servidor de conector e tente aceder à aplicação. Se executar os conectores em Azure mas as aplicações estão no local, a experiência pode não ser o que os seus utilizadores esperam.
- **Os controladores de domínio**: Se os conectores efetuam um único sinal de sinalização (SSO) utilizando a Delegação Restrita Kerberos, eles contactam os controladores de domínio antes de enviar o pedido para o backend. Os conectores têm uma cache de bilhetes Kerberos, mas num ambiente movimentado a capacidade de resposta dos controladores de domínio pode afetar o desempenho. Esta questão é mais comum para os conectores que funcionam em Azure mas comunicam com controladores de domínio que estão no local.

Para obter mais informações sobre a otimização da sua rede, consulte [considerações de topologia da Rede ao utilizar o Azure Ative Directory Application Proxy](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Adesão de domínio

Os conectores podem funcionar numa máquina que não esteja ligada ao domínio. No entanto, se pretender um único sinal de entrada (SSO) para aplicações que utilizem autenticação integrada do Windows (IWA), precisa de uma máquina de união de domínios. Neste caso, as máquinas de conector devem ser unidas a um domínio que possa executar a Delegação Constrangida [kerberos](https://web.mit.edu/kerberos) em nome dos utilizadores para as aplicações publicadas.

Os conectores também podem ser unidos a domínios em florestas que têm uma confiança parcial, ou a controladores de domínio apenas de leitura.

## <a name="connector-deployments-on-hardened-environments"></a>Implementações de conector em ambientes endurecidos

Normalmente, a colocação do conector é simples e não requer uma configuração especial. No entanto, existem algumas condições únicas que devem ser consideradas:

- As organizações que limitam o tráfego de saída devem [abrir os portos necessários.](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)
- As máquinas compatíveis com o FIPS podem ser necessárias para alterar a sua configuração para permitir que os processos do conector gerem e armazenem um certificado.
- As organizações que bloqueiam o seu ambiente com base nos processos que emitem os pedidos de networking têm de se certificar de que ambos os serviços de conector estão habilitados a aceder a todas as portas e IPs necessários.
- Em alguns casos, os proxies avançados de saída podem quebrar a autenticação de certificado bidirecional e fazer com que a comunicação falhe.

## <a name="connector-authentication"></a>Autenticação do conector

Para fornecer um serviço seguro, os conectores têm de autenticar em direção ao serviço, e o serviço tem de autenticar em direção ao conector. Esta autenticação é feita utilizando certificados de cliente e servidor quando os conectores iniciam a ligação. Desta forma, o nome de utilizador e a palavra-passe do administrador não são armazenados na máquina de conector.

Os certificados utilizados são específicos do serviço Application Proxy. São criados durante o registo inicial e são automaticamente renovados pelos conectores a cada dois meses.

Após a primeira renovação bem sucedida do certificado, o serviço de conector de aplicação AZure AD (Serviço de Rede) não tem autorização para remover o certificado antigo da loja de máquinas local. Se o certificado tiver expirado ou não for mais utilizado pelo serviço, pode eliminá-lo com segurança.

Para evitar problemas com a renovação do certificado, certifique-se de que a comunicação da rede do conector para os [destinos documentados](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment) está ativada.

Se um conector não estiver ligado ao serviço durante vários meses, os seus certificados podem estar desatualizados. Neste caso, desinstale e reinstale o conector para acionar o registo. Pode executar os seguintes comandos PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector -EnvironmentName "AzureCloud"
```

Para o governo, `-EnvironmentName "AzureUSGovernment"` use. Para mais detalhes, consulte [o Agente de Instalação para a Nuvem do Governo Azure](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud).

Para saber mais sobre como verificar o certificado e problemas de resolução de problemas consulte [o suporte de componentes de verificação da Máquina e de backend para o certificado de confiança de Procuração de Aplicação](application-proxy-connector-installation-problem.md#verify-machine-and-backend-components-support-for-application-proxy-trust-certificate).

## <a name="under-the-hood"></a>Os bastidores

Os conectores são baseados no Proxy de aplicação web do Windows Server, por isso têm a maioria das mesmas ferramentas de gestão, incluindo Registos de Eventos do Windows

![Gerir registos de eventos com o Espectador de Eventos](./media/application-proxy-connectors/event-view-window.png)

e contadores de desempenho do Windows.

![Adicione contadores ao conector com o Monitor de Desempenho](./media/application-proxy-connectors/performance-monitor.png)

Os conectores têm registos **de administração** e **sessão.** O registo **de administração** inclui eventos-chave e os seus erros. O registo **session** inclui todas as transações e os seus dados de processamento.

Para ver os registos, abra o **Espectador de Eventos** e vá a **Aplicações e Serviços Regista**o  >  **Microsoft**  >  **Conector AadApplicationProxy**  >  **Connector**. Para tornar visível o registo **de Sessão** no menu **Ver,** selecione **Mostrar Registos Analíticos e Debug**. O registo **de Sessão** é normalmente utilizado para a resolução de problemas e é desativado por padrão. Capacite-o a começar a recolher eventos e desativá-lo quando já não for necessário.

Pode examinar o estado do serviço na janela dos Serviços. O conector é composto por dois Serviços Windows: o conector real e o atualizador. Ambos devem correr a toda a hora.

 ![Exemplo: Janela de serviços mostrando serviços Azure AD local](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Passos seguintes

- [Publicar aplicações em redes e locais separados utilizando grupos de conector](application-proxy-connector-groups.md)
- [Trabalhar com servidores proxy existentes no local](application-proxy-configure-connectors-with-proxy-servers.md)
- [Erros de procuração e conector de aplicação de resolução de problemas](application-proxy-troubleshoot.md)
- [Como instalar silenciosamente o Conector Proxy da Aplicação AD AZure](application-proxy-register-connector-powershell.md)
