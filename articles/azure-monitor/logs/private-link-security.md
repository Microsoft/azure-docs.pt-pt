---
title: Utilizar o Azure Private Link para se ligar em segurança a redes do Azure Monitor
description: Utilizar o Azure Private Link para se ligar em segurança a redes do Azure Monitor
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 76c6d7caf3c63779e12443304688192f7311720a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594568"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Utilizar o Azure Private Link para se ligar em segurança a redes do Azure Monitor

[O Azure Private Link](../../private-link/private-link-overview.md) permite-lhe ligar de forma segura os serviços Azure PaaS à sua rede virtual utilizando pontos finais privados. Para muitos serviços, basta configurar um ponto final por recurso. No entanto, o Azure Monitor é uma constelação de diferentes serviços interligados que trabalham em conjunto para monitorizar as suas cargas de trabalho. Como resultado, construímos um recurso chamado Azure Monitor Private Link Scope (AMPLS). O AMPLS permite-lhe definir os limites da sua rede de monitorização e ligar-se à sua rede virtual. Este artigo abrange quando usar e como configurar um Azure Monitor Private Link Scope.

## <a name="advantages"></a>Vantagens

Com Link Privado pode:

- Ligue-se privadamente ao Azure Monitor sem abrir qualquer acesso público à rede
- Certifique-se de que os seus dados de monitorização só são acedidos através de redes privadas autorizadas
- Impedir a exfiltração de dados das suas redes privadas definindo recursos específicos do Azure Monitor que se conectam através do seu ponto final privado
- Ligue de forma segura a sua rede privada no local ao Azure Monitor utilizando o ExpressRoute e o Private Link
- Mantenha todo o tráfego dentro da rede de espinha dorsal do Microsoft Azure

Para mais informações, consulte [os Principais Benefícios do Link Privado.](../../private-link/private-link-overview.md#key-benefits)

## <a name="how-it-works"></a>Como funciona

O Azure Monitor Private Link Scope (AMPLS) liga pontos finais privados (e os VNets em que estão contidos) a um ou mais recursos do Monitor Azure - Log Analytics workspaces e componentes de Insights de Aplicação.

![Diagrama de topologia de recursos básicos](./media/private-link-security/private-link-basic-topology.png)

* O Ponto Final Privado no seu VNet permite-lhe chegar aos pontos finais do Azure Monitor através de IPs privados da piscina da sua rede, em vez de utilizar para os IPs públicos destes pontos finais. Isto permite-lhe continuar a utilizar os seus recursos Azure Monitor sem abrir o seu VNet para tráfego de saída não ressarcedo. 
* O tráfego desde o Private Endpoint até aos recursos do Azure Monitor passará pela espinha dorsal do Microsoft Azure e não será encaminhado para redes públicas. 
* Pode configurar cada um dos seus espaços de trabalho ou componentes para permitir ou negar a ingestão e consultas de redes públicas. Isso fornece uma proteção ao nível dos recursos, para que possa controlar o tráfego a recursos específicos.

> [!NOTE]
> Um único recurso Azure Monitor pode pertencer a vários AMPLSs, mas não é possível ligar um único VNet a mais de um AMPLS. 

## <a name="planning-your-private-link-setup"></a>Planejando a sua configuração de Link Privado

Antes de configurar a configuração do Azure Monitor Private Link, considere a sua topologia de rede e, especificamente, a sua topologia de encaminhamento DENS. 

### <a name="the-issue-of-dns-overrides"></a>A questão dos dnss sobrepõe-se
Alguns serviços do Azure Monitor utilizam pontos finais globais, o que significa que servem pedidos direcionados para qualquer espaço de trabalho/componente. Alguns exemplos são o ponto final de ingestão de Insights de Aplicação, e o ponto final de consulta tanto de Insights de Aplicação como de Log Analytics.

Quando configura uma ligação Private Link, o seu DNS é atualizado para mapear os pontos finais do Azure Monitor para endereços IP privados a partir da gama IP do VNet. Esta alteração substitui qualquer mapeamento anterior destes pontos finais, que podem ter implicações significativas, revistos abaixo. 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Azure Monitor Private Link aplica-se a todos os recursos do Azure Monitor - é Tudo ou Nada
Uma vez que alguns pontos finais do Azure Monitor são globais, é impossível criar uma ligação private link para um componente específico ou espaço de trabalho. Em vez disso, quando configurar um Link Privado para um único componente de Insights de Aplicação ou espaço de trabalho Log Analytics, os seus registos DNS são atualizados para **todos os** componentes do Application Insights. Qualquer tentativa de ingerir ou consultar um componente passará pelo Link Privado e, possivelmente, falhará. No que diz respeito ao Log Analytics, os pontos finais de ingestão e configuração são específicos do espaço de trabalho, o que significa que a configuração de ligação privada só se aplicará aos espaços de trabalho especificados. A ingestão e a configuração de outros espaços de trabalho serão direcionadas para os pontos finais do Log Analytics públicos predefinidos.

![Diagrama de DNS sobrepõe-se num único VNet](./media/private-link-security/dns-overrides-single-vnet.png)

Isso é verdade não só para um VNet específico, mas para todos os VNets que partilham o mesmo servidor DNS (ver [a emissão de DNS overrides).](#the-issue-of-dns-overrides) Assim, por exemplo, o pedido de ingestão de registos para qualquer componente de Insights de Aplicação será sempre enviado através da rota Private Link. Os componentes que não estão ligados aos AMPLS falharão na validação do Link Privado e não passarão.

> [!NOTE]
> Para concluir: Uma vez configurada uma ligação private link a um único recurso, aplica-se aos recursos do Azure Monitor em toda a sua rede. Para os recursos de Insights de Aplicação, isso é "Tudo ou Nada". Isso significa que deve adicionar todos os recursos de Insights de Aplicação na sua rede aos seus AMPLS, ou nenhum deles.
> 
> Para lidar com os riscos de exfiltração de dados, a nossa recomendação é adicionar todos os recursos de Application Insights e Log Analytics aos seus AMPLS e bloquear o tráfego das suas redes o máximo possível.

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Azure Monitor Private Link aplica-se a toda a sua rede
Algumas redes são compostas por vários VNets. Se os VNets usarem o mesmo servidor DNS, eles sobrepõem-se aos mapeamentos dns uns dos outros e possivelmente quebrarão a comunicação uns dos outros com o Azure Monitor (ver [A emissão de sobreposições de DNS).](#the-issue-of-dns-overrides) Em última análise, apenas o último VNet será capaz de comunicar com o Azure Monitor, uma vez que o DNS irá mapear os pontos finais do Azure Monitor para iPs privados desta gama de VNets (que pode não ser alcançável a partir de outros VNets).

![Diagrama de DNS sobrepõe-se em vários VNets](./media/private-link-security/dns-overrides-multiple-vnets.png)

No diagrama acima, o VNet 10.0.1.x liga-se pela primeira vez ao AMPLS1 e mapeia os pontos finais globais do Azure Monitor para os IPs da sua gama. Mais tarde, o VNet 10.0.2.x liga-se ao AMPLS2 e substitui o mapeamento de DNS dos *mesmos pontos finais globais* com iPs da sua gama. Uma vez que estes VNets não são espreitados, o primeiro VNet agora não consegue chegar a estes pontos finais.

> [!NOTE]
> Para concluir: A configuração do AMPLS afeta todas as redes que partilham as mesmas zonas de DNS. Para evitar a sobredição dos mapeamentos de ponto final do DNS uns dos outros, o melhor é configurar um único Ponto Final Privado numa rede vigiada (como um Hub VNet), ou separar as redes ao nível do DNS (exemplo inimigo, utilizando reencaminhadores DNS ou servidores DNS separados inteiramente).

### <a name="hub-spoke-networks"></a>Redes de fala-de-hub
As topologias fala-ambientais podem evitar a emissão de sobreposições de DNS, estabelecendo uma Ligação Privada no hub (principal) VNet, em vez de configurar separadamente um Link Privado para cada VNet. Esta configuração faz sentido especialmente se os recursos do Azure Monitor utilizados pelos VNets falados forem partilhados. 

![Hub-and-spoke-single-PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> Pode intencionalmente preferir criar Links Privados separados para os seus VNets falados, por exemplo, para permitir que cada VNet aceda a um conjunto limitado de recursos de monitorização. Nesses casos, pode criar um Ponto Final Privado e AMPLS dedicados para cada VNet, mas também deve verificar se não partilham as mesmas zonas dns para evitar substituições de DNS.


### <a name="consider-limits"></a>Considere limites

Conforme listado em [Restrições e limitações,](#restrictions-and-limitations)o objeto AMPLS tem uma série de limites, mostrados na topologia abaixo:
* Cada VNet liga-se a apenas **1** objeto AMPLS.
* O AMPLS B está ligado a pontos finais privados de dois VNet2 e VNet3, utilizando 2 das 10 possíveis ligações private endpoint.
* O AMPLS A liga-se a dois espaços de trabalho e a um componente application Insight, utilizando 3 das 50 possíveis ligações de recursos do Azure Monitor.
* O espaço de trabalho2 liga-se aos AMPLS A e AMPLS B, utilizando 2 das 5 possíveis ligações AMPLS.

![Diagrama dos limites ampls](./media/private-link-security/ampls-limits.png)


## <a name="example-connection"></a>Ligação de exemplo

Comece por criar um recurso Azure Monitor Private Link Scope.

1. Vá para **criar um recurso** no portal Azure e procure o **Azure Monitor Private Link Scope**.

   ![Encontre o âmbito de ligação privada do monitor Azure](./media/private-link-security/ampls-find-1c.png)

2. Selecione **criar**.
3. Escolha um Grupo de Subscrição e Recursos.
4. Dê um nome aos AMPLS. É melhor usar um nome significativo e claro, como "AppServerProdTelem".
5. Selecione **Rever + Criar**. 

   ![Criar âmbito de ligação privada do Monitor Azure](./media/private-link-security/ampls-create-1d.png)

6. Deixe passar a validação e, em seguida, **selecione Criar**.

### <a name="connect-azure-monitor-resources"></a>Ligar recursos do Monitor Azure

Ligue os recursos do Monitor Azure (log analytics e componentes de Insights de Aplicação) aos seus AMPLS.

1. No seu âmbito de ligação privada Azure Monitor, selecione **Recursos monitores Azure** no menu à esquerda. Selecione o botão **Adicionar.**
2. Adicione o espaço de trabalho ou o componente. A seleção do botão **Adicionar** traz um diálogo onde pode selecionar os recursos do Azure Monitor. Pode navegar através das suas subscrições e grupos de recursos, ou pode escrever o seu nome para filtrar até elas. Selecione o espaço de trabalho ou componente e **selecione Aplicar** para adicioná-los ao seu âmbito.

    ![Screenshot de selecionar um âmbito UX](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> Eliminar os recursos do Monitor Azure requer que os desligue primeiro de quaisquer objetos AMPLS a que estejam ligados. Não é possível apagar recursos ligados a um AMPLS.

### <a name="connect-to-a-private-endpoint"></a>Ligar-se a um ponto final privado

Agora que tem recursos ligados ao seu AMPLS, crie um ponto final privado para ligar a nossa rede. Pode fazer esta tarefa no [portal Azure Private Link center,](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)ou dentro do seu Azure Monitor Private Link Scope, como feito neste exemplo.

1. No seu recurso de âmbito, selecione **as ligações Private Endpoint** no menu de recursos à esquerda. Selecione **Private Endpoint** para iniciar o processo de criação de ponto final. Também pode aprovar ligações que foram iniciadas no centro de ligação privada aqui, selecionando-as e selecionando **Aprovar.**

    ![Screenshot de Private Endpoint Connections UX](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Escolha a subscrição, o grupo de recursos e o nome do ponto final, e a região em que deve viver. A região tem de ser a mesma região a que o VNet a que a liga.

3. Selecione **Seguinte: Recurso**. 

4. No ecrã de Recursos,

   a. Escolha a **Subscrição** que contém o seu recurso Azure Monitor Private Scope. 

   b. Para **o tipo de recurso,** escolha **Microsoft.insights/privateLinkScopes**. 

   c. A partir da entrega de **recursos,** escolha o seu âmbito de Ligação Privada que criou anteriormente. 

   d. Selecione **Seguinte: Configuração >**.
      ![Screenshot de selecionar Criar Ponto Final Privado](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. No painel de configuração,

   a.    Escolha a **rede virtual** e **a sub-rede** que pretende ligar aos recursos do Monitor Azure. 
 
   b.    Escolha **Sim** para **Integrar com a zona privada de DNS,** e deixe-o criar automaticamente uma nova Zona Privada de DNS. As zonas de DNS reais podem ser diferentes das mostradas na imagem abaixo. 
   > [!NOTE]
   > Se escolher **Não** e preferir gerir os registos DNS manualmente, primeiro complete a configuração do seu Link Privado - incluindo este Ponto final privado e a configuração AMPLS. Em seguida, configure o DNS de acordo com as instruções em [Configuração do DNS do Ponto Final Privado do Azure](../../private-link/private-endpoint-dns.md). Não crie registos vazios como preparação da configuração da Ligação Privada. Os registos DNS criados podem substituir as definições existentes e afetar a conectividade com o Azure Monitor.
 
   c.    Selecione **Rever + criar**.
 
   d.    Deixe a validação passar. 
 
   e.    Selecione **Criar**. 

    ![Screenshot de detalhes de Private Endpoint selecionados.](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Criou agora um novo ponto final privado que está ligado a este AMPLS.

## <a name="review-and-validate-your-private-link-setup"></a>Reveja e valide a sua configuração de Link Privado

### <a name="reviewing-your-endpoints-dns-settings"></a>Rever as definições de DNS do seu Endpoint
O Ponto Final Privado que criou deverá agora ter quatro zonas DNS configuradas:

[![Screenshot das zonas privadas de DNS endpoint.](./media/private-link-security/private-endpoint-dns-zones.png)](./media/private-link-security/private-endpoint-dns-zones-expanded.png#lightbox)

* privatelink-monitor-azure-com
* privatelink-oms-opinsights-azure-com
* privatelink-ods-opinsights-azure-com
* privatelink-agentesvc-azure-automation-net

> [!NOTE]
> Cada uma destas zonas mapeia pontos finais específicos do Azure Monitor para iPs privados a partir do conjunto de IPs do VNet. Os endereços IP mostrados nas imagens abaixo são apenas exemplos. A sua configuração deve, em vez disso, mostrar IPs privados da sua própria rede.

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-monitor-azure-com
Esta zona abrange os pontos finais globais utilizados pelo Azure Monitor, o que significa que estes pontos finais servem pedidos considerando todos os recursos, e não um específico. Esta zona deve ter pontos finais mapeados para:
* `in.ai` - (Ponto final de ingestão de Insights de Aplicação, verá uma entrada global e regional
* `api` - Insights de Aplicações e Ponto final da API do Log Analytics
* `live` - Application Insights live metrics endpoint
* `profiler` - Ponto final do profiler de perfis de aplicações
* `snapshot`- Application Insights snapshots ponto final [ ![ Screenshot do monitor-azure-com da zona privada de DNS.](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>privatelink-oms-opinsights-azure-com
Esta zona cobre o mapeamento específico do espaço de trabalho para os pontos finais do OMS. Deve ver uma entrada para cada espaço de trabalho ligado aos AMPLS ligados a este Ponto Final Privado.
[![Screenshot da zona privada dns-opinsights-azure-com.](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>privatelink-ods-opinsights-azure-com
Esta zona cobre o mapeamento específico do espaço de trabalho para pontos finais ODS - o ponto final de ingestão de Log Analytics. Deve ver uma entrada para cada espaço de trabalho ligado aos AMPLS ligados a este Ponto Final Privado.
[![Screenshot da zona privada dns-opinsights-azure-com.](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>privatelink-agentesvc-azure-automation-net
Esta zona cobre o mapeamento específico do espaço de trabalho para os pontos finais de automatização do serviço do agente. Deve ver uma entrada para cada espaço de trabalho ligado aos AMPLS ligados a este Ponto Final Privado.
[![Screenshot do agente privado da zona DNS svc-azure-automation-net.](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

### <a name="validating-you-are-communicating-over-a-private-link"></a>Validando que está a comunicar através de um Link Privado
* Para validar os seus pedidos são agora enviados através do Private Endpoint e para os pontos finais ip-mapeados privados, pode revê-los com um rastreio de rede para ferramentas, ou mesmo o seu navegador. Por exemplo, ao tentar consultar o seu espaço de trabalho ou aplicação, certifique-se de que o pedido é enviado para o IP privado mapeado para o ponto final da API, neste exemplo é *172.17.0.9*.

    Nota: Alguns navegadores podem utilizar outras definições de DNS (ver [definições de DNS](#browser-dns-settings)do navegador). Certifique-se de que as definições de DNS se aplicam.

* Para garantir que o seu espaço de trabalho ou componente não esteja a receber pedidos de redes públicas (não ligados através da AMPLS), desacorde as bandeiras de ingestão pública do recurso para *Não,* tal como explicado na [Gestão do acesso a partir de âmbitos de ligações privadas.](#manage-access-from-outside-of-private-links-scopes)

* A partir de um cliente na sua rede protegida, utilize `nslookup` para qualquer um dos pontos finais listados nas suas zonas de DNS. Deve ser resolvido pelo seu servidor DNS para os IPs privados mapeados em vez dos IPs públicos utilizados por padrão.


## <a name="configure-log-analytics"></a>Configurar o Log Analytics

Aceda ao portal do Azure. No menu de recursos do espaço de trabalho Log Analytics, há um item chamado Isolamento de **Rede** no lado esquerdo. Você pode controlar dois estados diferentes deste menu.

![Isolamento da Rede LA](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Âmbitos de ligação privada do Monitor Azure conectados
Todos os âmbitos ligados ao espaço de trabalho aparecem neste ecrã. A ligação aos âmbitos (AMPLSs) permite que o tráfego de rede a partir da rede virtual ligada a cada AMPLS chegue a este espaço de trabalho. Criar uma ligação por aqui tem o mesmo efeito que a definição no âmbito, como fizemos na [ligação dos recursos do Monitor Azure.](#connect-azure-monitor-resources) Para adicionar uma nova ligação, **selecione Adicionar** e selecione o Azure Monitor Private Link Scope. **Selecione Aplicar** para conectá-lo. Note que um espaço de trabalho pode ligar-se a 5 objetos AMPLS, como mencionado em [Restrições e limitações](#restrictions-and-limitations). 

### <a name="manage-access-from-outside-of-private-links-scopes"></a>Gerir o acesso a partir de fora dos âmbitos de ligações privadas
As definições na parte inferior desta página controlam o acesso a partir de redes públicas, o que significa que as redes não estão ligadas através dos âmbitos listados acima. Definição **Permitir o acesso à rede pública para ingestão** a **Nenhum** bloco de ingerência de troncos de máquinas fora dos âmbitos ligados. Definição **Permitir o acesso à rede pública para consultas** **sem** consultas de blocos provenientes de máquinas fora dos âmbitos. Isso inclui consultas executadas através de livros de trabalho, dashboards, experiências de cliente baseadas em API, insights no portal Azure, e muito mais. Experiências que correm fora do portal Azure, e que os dados de consulta log analytics também têm que estar em execução dentro do VNET ligado a privados.

### <a name="exceptions"></a>Exceções
Restringir o acesso como explicado acima não se aplica ao Gestor de Recursos Azure e, portanto, tem as seguintes limitações:
* O acesso aos dados - ao mesmo tempo que bloqueia/permite consultas de redes públicas aplica-se à maioria das experiências do Log Analytics, algumas experiências consultam dados através do Azure Resource Manager e, portanto, não poderão consultar dados a menos que as definições de Private Link sejam aplicadas também ao Gestor de Recursos (funcionalidade que está a chegar em breve). Exemplos são soluções Azure Monitor, Workbooks e Insights e o conector LogicApp.
* Gestão do espaço de trabalho - As alterações na configuração do espaço de trabalho e nas alterações de configuração (incluindo ligar ou desligar estas definições de acesso) são geridas pelo Azure Resource Manager. Restringir o acesso à gestão do espaço de trabalho utilizando as funções, permissões, controlos de rede e auditoria adequados. Para mais informações, consulte [as Funções, Permissões e Segurança do Monitor Azure.](../roles-permissions-security.md)

> [!NOTE]
> Os registos e métricas enviados para um espaço de trabalho através [de Definições de Diagnóstico](../essentials/diagnostic-settings.md) passam por um canal privado seguro da Microsoft e não são controlados por estas definições.

### <a name="log-analytics-solution-packs-download"></a>Pacotes de soluções Log Analytics descarregam

Para permitir que o Agente Desafiá-lo descarregue pacotes de soluções, adicione os nomes de domínio totalmente qualificados apropriados à sua lista de autorizações de firewall. 


| Ambiente em nuvem | Recursos do Agente | Portas | Direção |
|:--|:--|:--|:--|
|Azure Público     | scadvisorcontent.blob.core.windows.net         | 443 | Saída
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Saída
|Azure China 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Saída

## <a name="configure-application-insights"></a>Configurar o Application Insights

Aceda ao portal do Azure. No seu recurso componente Azure Monitor Application Insights, encontra-se um menu de **isolamento de rede** no lado esquerdo. Você pode controlar dois estados diferentes deste menu.

![Isolamento da Rede AI](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Em primeiro lugar, pode ligar este recurso Application Insights aos âmbitos de ligação privada do Monitor Azure a que tem acesso. **Selecione Adicionar** e selecione o **Âmbito de Ligação Privada Azure Monitor**. Selecione Aplicar para conectá-lo. Todos os telescópios conectados aparecem neste ecrã. A realização desta ligação permite que o tráfego de rede nas redes virtuais conectadas atinja este componente, e tem o mesmo efeito que ligá-lo do âmbito que fizemos na [ligação dos recursos do Monitor Azure.](#connect-azure-monitor-resources) 

Em segundo lugar, pode controlar como este recurso pode ser alcançado a partir de fora dos âmbitos de ligação privados (AMPLS) listados anteriormente. Se definir **Permita o acesso à rede pública para ingestão** a **Nº,** então as máquinas ou SDKs fora dos âmbitos ligados não podem enviar dados para este componente. Se definir **Permitir o acesso à rede pública para consultas** a **No,** então as máquinas fora dos âmbitos não podem aceder aos dados neste recurso Application Insights. Esses dados incluem acesso a registos APM, métricas e o fluxo de métricas ao vivo, bem como experiências construídas em cima, tais como livros de trabalho, dashboards, experiências de clientes baseadas em consultas com API, insights no portal Azure, e muito mais. 

> [!NOTE]
> As experiências de consumo não-portal também devem ser executadas no VNET de ligação privada que inclui as cargas de trabalho monitorizadas.

Você precisará adicionar recursos que hospedam as cargas de trabalho monitorizadas para o link privado. Por exemplo, consulte [utilizar pontos finais privados para a aplicação web Azure](../../app-service/networking/private-endpoint.md).

Restringir o acesso desta forma só se aplica aos dados do recurso Application Insights. No entanto, as alterações de configuração, incluindo ligar ou desligar estas definições de acesso, são geridas pelo Azure Resource Manager. Portanto, deve restringir o acesso ao Gestor de Recursos usando as funções, permissões, controlos de rede e auditorias apropriados. Para mais informações, consulte [as Funções, Permissões e Segurança do Monitor Azure.](../roles-permissions-security.md)

> [!NOTE]
> Para garantir totalmente os Insights de Aplicação baseados no espaço de trabalho, é necessário bloquear tanto o acesso ao recurso Application Insights como o espaço de trabalho subjacente ao Log Analytics.
>
> Os diagnósticos ao nível do código (profiler/debugger) precisam que forneça a [sua própria conta de armazenamento](../app/profiler-bring-your-own-storage.md) para suportar uma ligação privada.

### <a name="handling-the-all-or-nothing-nature-of-private-links"></a>Lidar com a natureza tudo ou nada das ligações privadas
Como explicado no [Planeamento da sua configuração private link,](#planning-your-private-link-setup)a criação de um Link Privado mesmo para um único recurso afeta todos os recursos do Azure Monitor nessas redes e noutras redes que partilham o mesmo DNS. Este comportamento pode tornar o seu processo de embarque desafiante. Considere as seguintes opções:

* Tudo em - a abordagem mais simples e segura é adicionar todos os seus componentes de Insights de Aplicação ao AMPLS. Para componentes que deseja ainda aceder a partir de outras redes, deixe as bandeiras "Permitir o acesso público à Internet para ingestão/consulta" definidas como Sim (o padrão).
* Isolar as redes - se estiver (ou puder alinhar-se) utilizando vnets faladores, siga a orientação na [topologia da rede de fala-de-hub em Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Em seguida, configurar as definições de ligação privada separadas nos VNets de spoke relevantes. Certifique-se de separar as zonas DNS também, uma vez que a partilha de zonas DNS com outras redes de raios-DNS irá causar [sobreposições de DNS](#the-issue-of-dns-overrides).
* Utilize zonas DNS personalizadas para aplicações específicas - esta solução permite-lhe aceder a componentes selecionados de Application Insights em vez de um Link Privado, mantendo todo o tráfego sobre as rotas públicas.
    - Crie uma [zona de DNS privada personalizada,](../../private-link/private-endpoint-dns.md)e dê-lhe um nome único, como internal.monitor.azure.com
    - Crie um AMPLS e um Private Endpoint, e opte por **não** se integrar automaticamente com DNS privados
    - Vá a Private Endpoint -> Configuração DNS e reveja o mapeamento sugerido de FQDNs.
    - Escolha adicionar configuração e escolha a zona de internal.monitor.azure.com que acabou de criar
    - Adicionar registos para a screenshot acima ![ da zona de DNS configurada](./media/private-link-security/private-endpoint-global-dns-zone.png)
    - Vá ao seu componente Application Insights e copie a sua [Cadeia de Ligação.](../app/sdk-connection-string.md)
    - As aplicações ou scripts que desejem chamar este componente sobre uma Ligação Privada devem utilizar a cadeia de ligação com o EndpointSuffix=internal.monitor.azure.com
* Mapeia pontos finais através de ficheiros de anfitriões em vez de DNS - para ter um acesso de Ligação Privada apenas a partir de uma máquina/VM específico na sua rede:
    - Crie um AMPLS e um Private Endpoint, e opte por **não** se integrar automaticamente com DNS privados 
    - Configure os registos acima A de uma máquina que executa a aplicação no ficheiro dos anfitriões


## <a name="use-apis-and-command-line"></a>Use APIs e linha de comando

Pode automatizar o processo descrito anteriormente utilizando modelos de Gestor de Recursos Azure, REST e interfaces de linha de comando.

Para criar e gerir os âmbitos de ligação privados, utilize o [REST API](/rest/api/monitor/private%20link%20scopes%20(preview)) ou [o Azure CLI (az monitor-link-scope)](/cli/azure/monitor/private-link-scope).

Para gerir o acesso à rede, utilize as bandeiras `[--ingestion-access {Disabled, Enabled}]` e nos espaços de trabalho do Log `[--query-access {Disabled, Enabled}]` [Analytics](/cli/azure/monitor/log-analytics/workspace) ou nos componentes do [Application Insights](/cli/azure/ext/application-insights/monitor/app-insights/component).

## <a name="collect-custom-logs-and-iis-log-over-private-link"></a>Colete registos personalizados e registo do IIS sobre o Link Privado

As contas de armazenamento são utilizadas no processo de ingestão de registos personalizados. Por predefinição, são utilizadas contas de armazenamento geridas pelo serviço. No entanto, para ingerir registos personalizados em links privados, deve utilizar as suas próprias contas de armazenamento e associá-las ao espaço de trabalho log Analytics. Consulte mais detalhes sobre como configurar tais contas utilizando a [linha de comando](/cli/azure/monitor/log-analytics/workspace/linked-storage).

Para obter mais informações sobre como trazer a sua própria conta de armazenamento, consulte [as contas de armazenamento do cliente para ingestão de registos](private-storage.md)

## <a name="restrictions-and-limitations"></a>Restrições e limitações

### <a name="ampls"></a>AMPLS
O objeto AMPLS tem uma série de limites que deve considerar ao planear a configuração do seu Link Privado:

* Um VNet só pode ligar-se a 1 objeto AMPLS. Isto significa que o objeto AMPLS deve fornecer acesso a todos os recursos do Monitor Azure a que o VNet deve ter acesso.
* Um recurso Azure Monitor (componente Workspace ou Application Insights) pode ligar-se a 5 AMPLSs no máximo.
* Um objeto AMPLS pode ligar-se ao máximo de 50 recursos do Monitor Azure.
* Um objeto AMPLS pode ligar-se a 10 Pontos Finais Privados no máximo.

Consulte [os limites](#consider-limits) para uma revisão mais profunda destes limites.

### <a name="agents"></a>Agentes

As versões mais recentes dos agentes Windows e Linux devem ser utilizadas para suportar a ingestão segura nos espaços de trabalho do Log Analytics. Versões mais antigas não podem carregar dados de monitorização numa rede privada.

**Agente do Windows do Log Analytics**

Utilize a versão 10.20.18038.0 ou posterior do agente Log Analytics.

**Agente do Linux do Log Analytics**

Utilize a versão 1.12.25 ou posterior do agente. Se não puder, erguia os seguintes comandos no seu VM.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Portal do Azure

Para utilizar experiências do portal Azure Monitor, como Application Insights e Log Analytics, é necessário permitir que as extensões do portal Azure e do Azure Monitor sejam acessíveis nas redes privadas. Adicione [etiquetas](../../firewall/service-tags.md) de serviço **AzureActiveDirectory**, **AzureResourceManager**, **AzureFrontDoor.FirstParty** e **AzureFrontdoor.Frontend** ao seu Grupo de Segurança de Rede.

### <a name="querying-data"></a>Consultar dados
O [ `externaldata` operador](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor) não é suportado por um Link Privado, pois lê dados de contas de armazenamento, mas não garante que o armazenamento seja acedido em privado.

### <a name="programmatic-access"></a>Acesso programático

Para utilizar o REST API, [CLI](/cli/azure/monitor) ou PowerShell com O Azure Monitor em redes [privadas,](../../virtual-network/service-tags-overview.md)adicione as tags de serviço **AzureActiveDirectory** e **AzureResourceManager** à sua firewall.  

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Application Insights SDK descarrega a partir de uma rede de entrega de conteúdos

Embrulhe o código JavaScript no seu script para que o navegador não tente descarregar código a partir de um CDN. Um exemplo é dado no [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="browser-dns-settings"></a>Configurações de DNS do navegador

Se estiver a ligar-se aos seus recursos do Azure Monitor através de uma Ligação Privada, o tráfego para estes recursos deve passar pelo ponto final privado que está configurado na sua rede. Para ativar o ponto final privado, atualize as definições de DNS como explicado no [Connect para um ponto final privado](#connect-to-a-private-endpoint). Alguns navegadores usam as suas próprias definições de DNS em vez das que define. O navegador pode tentar ligar-se aos pontos finais públicos do Azure Monitor e contornar totalmente o Link Privado. Verifique se as definições dos seus navegadores não se sobrepõem ou cache as definições de DNS antigas. 

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [armazenamento privado](private-storage.md)