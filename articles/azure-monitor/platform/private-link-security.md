---
title: Utilize o Link Privado Azure para ligar as redes de forma segura ao Monitor Azure
description: Utilize o Link Privado Azure para ligar as redes de forma segura ao Monitor Azure
author: nkiest
ms.author: nikiest
ms.topic: conceptual
ms.date: 05/20/2020
ms.subservice: ''
ms.openlocfilehash: ddd34295bfe64fdd336d8b237482b45f02e30201
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/24/2020
ms.locfileid: "83816718"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Utilize o Link Privado Azure para ligar as redes de forma segura ao Monitor Azure

> [!IMPORTANT]
> Neste momento, deve **solicitar o acesso** à utilização desta capacidade. Pode candidatar-se ao acesso através do formulário de [inscrição](https://aka.ms/AzMonPrivateLinkSignup).


[O Azure Private Link](../../private-link/private-link-overview.md) permite-lhe ligar de forma segura os serviços Azure PaaS à sua rede virtual utilizando pontos finais privados. Para muitos serviços, acaba de configurar um ponto final por recurso. No entanto, o Azure Monitor é uma constelação de diferentes serviços interligados que trabalham em conjunto para monitorizar as suas cargas de trabalho. Como resultado, construímos um recurso chamado Alcance de Ligação Privada Do Monitor Azure (AMPLS) que lhe permite definir os limites da sua rede de monitorização e ligar-se à sua rede virtual. Este artigo abrange quando utilizar e como criar um Âmbito de Ligação Privada Do Monitor Azure.

## <a name="advantages"></a>Vantagens

Com Link Privado pode:

- Ligue-se em privado ao Azure Monitor sem abrir qualquer acesso à rede pública
- Certifique-se de que os seus dados de monitorização só são acedidos através de redes privadas autorizadas
- Evite a exfiltração de dados das suas redes privadas definindo recursos específicos do Azure Monitor que se ligam através do seu ponto final privado
- Ligue de forma segura a sua rede privada no local ao Monitor Azure utilizando expressroute e private Link
- Mantenha todo o tráfego dentro da rede de espinha somal do Microsoft Azure

Para mais informações, consulte [os principais benefícios da ligação privada](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Como funciona

O Portal de Ligação Privada Azure Monitor é um recurso de agrupamento para ligar um ou mais pontos finais privados (e, portanto, as redes virtuais em que estão contidos) a um ou mais recursos do Monitor Azure. Os recursos incluem espaços de trabalho Log Analytics e componentes de Insights de Aplicação.

![Diagrama de topologia de recursos](./media/private-link-security/private-link-topology-1.png)

> [!NOTE]
> Um único recurso Do Monitor Azure pode pertencer a múltiplas AMPLSs, mas não é possível ligar um único VNet a mais de um AMPLS. 

## <a name="planning-based-on-your-network"></a>Planeamento baseado na sua rede

Antes de configurar os seus recursos AMPLS, considere os requisitos de isolamento da sua rede. Avalie o acesso das suas redes virtuais à internet pública e as restrições de acesso de cada um dos seus recursos do Monitor Azure (isto é, componentes de Application Insights e espaços de trabalho de Log Analytics).

### <a name="evaluate-which-virtual-networks-should-connect-to-a-private-link"></a>Avaliar quais as redes virtuais que devem ligar-se a um Link Privado

Comece por avaliar quais das suas redes virtuais (VNets) têm acesso restrito à internet. Os VNets que tenham internet gratuita podem não necessitar de um Link Privado para aceder aos seus recursos do Monitor Azure. Os recursos de monitorização a que os seus VNets se ligam podem restringir o tráfego de entrada e exigir uma ligação de Ligação Privada (seja para ingestão de logou ou consulta). Nesses casos, mesmo um VNet que tenha acesso à internet pública precisa de se ligar a esses recursos através de um Link Privado, e através de um AMPLS.

### <a name="evaluate-which-azure-monitor-resources-should-have-a-private-link"></a>Avaliar quais os recursos do Azure Monitor devem ter uma Ligação Privada

Reveja cada um dos seus recursos do Monitor Azure:

- O recurso deve permitir a ingestão de registos de recursos localizados apenas em VNets específicos?
- O recurso só deve ser consultado por clientes localizados em VNETs específicos?

Se a resposta a qualquer uma destas questões for sim, estabeleça as restrições como explicadas na [Configuração](#configure-log-analytics) dos espaços de trabalho do Log Analytics e [na configuração](#configure-application-insights) dos componentes de Insights de Aplicação e associe esses recursos a um único ou vários AMPLS(s). As redes virtuais que devem aceder a estes recursos de monitorização precisam de ter um Ponto Final Privado que se conectem com o AMPLS relevante.
Lembre-se – pode ligar os mesmos espaços de trabalho ou aplicação a múltiplas AMPLS, para permitir que sejam alcançados por diferentes redes.

### <a name="group-together-monitoring-resources-by-network-accessibility"></a>Grupo de acompanhamento conjunto de recursos através da acessibilidade da rede

Uma vez que cada VNet pode ligar-se a apenas um recurso AMPLS, deve agrupar recursos de monitorização que devem ser acessíveis às mesmas redes. A forma mais simples de gerir este agrupamento é criar um AMPLS por VNet e selecionar os recursos para se conectar a essa rede. No entanto, para reduzir recursos e melhorar a gestão, pode querer reutilizar um AMPLS através das redes.

Por exemplo, se as suas redes virtuais internas VNet1 e VNet2 se conectarem aos espaços de trabalho Workspace1 e Workspace2 e aplicação Insights aplicação Insights Application Insights 3, associe os três recursos ao mesmo AMPLS. Se o VNet3 apenas aceder ao Workspace1, criar outro recurso AMPLS, associar o Workspace1 ao mesmo e ligar o VNet3 como mostrado nos seguintes diagramas:

![Diagrama de AMPLS Uma topologia](./media/private-link-security/ampls-topology-a-1.png)

![Diagrama de topologia AMPLS B](./media/private-link-security/ampls-topology-b-1.png)

## <a name="example-connection"></a>Conexão de exemplo

Comece por criar um recurso de Âmbito de Ligação Privada Azure Monitor.

1. Vá criar **um recurso** no portal Azure e procure o Alcance de **Ligação Privada do Monitor Azure.** 
2. Clique **em criar**. 
3. Escolha um Grupo de Subscrição e Recursos. 
4. Dê um nome ao AMPLS. É melhor usar um nome que seja claro para que propósito e limite de segurança o Scope será usado para que alguém não quebre acidentalmente os limites de segurança da rede. Por exemplo, "AppServerProdTelem". 
5. Clique em **Rever + Criar**. 
6. Deixe passar a validação e, em seguida, clique em **Criar**.

## <a name="connect-azure-monitor-resources"></a>Connect Azure Monitor recursos

Pode ligar o seu AMPLS primeiro a pontos finais privados e depois aos recursos do Azure Monitor ou vice-versa, mas o processo de ligação vai mais rápido se começar com os seus recursos do Monitor Azure. Eis como ligamos os espaços de trabalho do Azure Monitor Log Analytics e os componentes de Insights de Aplicação a um AMPLS

1. No seu âmbito de link privado Do Monitor Azure, clique em **Recursos de Monitor Azure** no menu à esquerda. Clique no botão **Adicionar**.
2. Adicione o espaço de trabalho ou componente. Clicar no botão **Adicionar** traz um diálogo onde pode selecionar os recursos do Monitor Azure. Pode navegar através das suas subscrições e grupos de recursos, ou pode escrever em seu nome para filtrar para eles. Selecione o espaço de trabalho ou componente e clique **Em aplicar** para adicioná-los ao seu âmbito.

    ![Screenshot de selecionar um ux de âmbito](./media/private-link-security/ampls-select-2.png)

### <a name="connect-to-a-private-endpoint"></a>Ligar a um ponto final privado

Agora que tem recursos ligados ao seu AMPLS, crie um ponto final privado para ligar a nossa rede. Pode fazer esta tarefa no [portal Azure Private Link center,](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)ou dentro do seu Portal de Ligação Privada Azure Monitor, como feito neste exemplo.

1. No seu recurso de âmbito, clique em **ligações Private Endpoint** no menu de recursos à esquerda. Clique no **Private Endpoint** para iniciar o processo de criação de ponto final. Também pode aprovar ligações que foram iniciadas no centro de Link Privado aqui, selecionando-as e clicando **em Aprovar**.

    ![Screenshot de conexões de ponto final privado UX](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Escolha a subscrição, o grupo de recursos e o nome do ponto final, e a região em que deve viver. A região tem de ser a mesma região da rede virtual a que a ligará.

3. Clique **em seguir: Recurso**. 

4. No ecrã de Recursos,

   a. Escolha a **Subscrição** que contém o seu recurso Alcance Privado do Monitor Azure. 

   b. Para **o tipo de recurso,** escolha **Microsoft.insights/privateLinkScopes**. 

   c. A partir da queda de **recursos,** escolha o seu âmbito de Link Privado que criou anteriormente. 

   d. Clique **em seguir: Configuração >**.
      ![Screenshot de selecionar Criar ponto final privado](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. No painel de configuração,

   a.    Escolha a **rede virtual** e **a subnet** que pretende ligar aos seus recursos do Monitor Azure. 
 
   b.    Escolha **Sim** para **Integrar com a zona privada dNS,** e deixe-o criar automaticamente uma nova Zona Privada dNS. 
 
   c.    Clique em **Rever + criar**.
 
   d.    Deixe a validação passar. 
 
   e.    Clique em **Criar**. 

    ![Screenshot de selecionar Criar Ponto Final Privado2](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Criou agora um novo ponto final privado que está ligado a este âmbito de ligação privada do Monitor Azure.

## <a name="configure-log-analytics"></a>Configure Log Analytics

Aceda ao portal do Azure. No seu recurso de espaço de trabalho Azure Monitor Log Analytics está um item de menu **Network Isolation** no lado esquerdo. Você pode controlar dois estados diferentes deste menu. 

![LA Network Isolation](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

Em primeiro lugar, pode ligar este recurso Log Analytics a quaisquer âmbitos de Link Privado do Monitor Azure a que tenha acesso. Clique em **Adicionar** e selecione o Âmbito de Ligação Privada do Monitor Azure.  Clique **Em Aplicar** para ligá-lo. Todos os âmbitos conectados aparecem neste ecrã. Fazer esta ligação permite que o tráfego de rede nas redes virtuais conectadas chegue a este espaço de trabalho. Fazer a ligação tem o mesmo efeito que ligá-la a partir do âmbito que fizemos na [Connecting Azure Monitor.](#connect-azure-monitor-resources)  

Em segundo lugar, pode controlar como este recurso pode ser alcançado de fora dos âmbitos de ligação privada listados acima. Se definir Permitir o acesso da rede pública para **ingestão** a **Nº**, então as máquinas fora dos âmbitos conectados não podem enviar dados para este espaço de trabalho. Se definir Permitir o acesso da **rede pública a consultas** a **Nº**, então as máquinas fora dos âmbitos não podem aceder a dados neste espaço de trabalho. Esses dados incluem acesso a livros de trabalho, dashboards, experiências de clientes baseadas em API, insights no portal Azure, e muito mais. As experiências que correm fora do portal Azure que consomem dados do Log Analytics também têm de estar a funcionar dentro do VNET ligado ao privado.

Restringir o acesso desta forma apenas se aplica aos dados no espaço de trabalho. As alterações de configuração, incluindo ligar ou desligar estas definições de acesso, são geridas pelo Gestor de Recursos Do Azure. Restringir o acesso ao Gestor de Recursos utilizando as funções, permissões, controlos de rede e auditoria adequados. Para mais informações, consulte [funções, permissões e segurança do Monitor Azure.](roles-permissions-security.md)

> [!NOTE]
> Os registos e métricas enviados para um espaço de trabalho através de [Definições](diagnostic-settings.md) de Diagnóstico passam por cima de um canal privado seguro da Microsoft e não são controlados por estas definições.

## <a name="configure-application-insights"></a>Configure insights de aplicação

Aceda ao portal do Azure. No seu recurso componente De aplicação Azure Monitor Insights está um item de menu **Isolamento de rede** no lado esquerdo. Você pode controlar dois estados diferentes deste menu.

![Isolamento da Rede AI](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Em primeiro lugar, pode ligar este recurso Application Insights aos âmbitos de Link Privado do Monitor Azure a que tem acesso. Clique em **Adicionar** e selecione o Âmbito de **Ligação Privada do Monitor Azure**. Clique Em Aplicar para ligá-lo. Todos os âmbitos conectados aparecem neste ecrã. Fazer esta ligação permite que o tráfego de rede nas redes virtuais conectadas atinja este componente. Fazer a ligação tem o mesmo efeito que ligá-la a partir do âmbito que fizemos na [Connecting Azure Monitor.](#connect-azure-monitor-resources) 

Em segundo lugar, pode controlar como este recurso pode ser alcançado de fora dos âmbitos de ligação privada listados anteriormente. Se definir Permitir o acesso da rede pública para **ingestão** a **No**, então máquinas ou SDKs fora dos âmbitos conectados não podem enviar dados para este componente. Se definir Permitir o acesso da **rede pública a consultas** a **No**, então as máquinas fora dos âmbitos não podem aceder a dados neste recurso Desinformação de Aplicação. Esses dados incluem acesso a registos, métricas e o fluxo de métricas ao vivo, bem como experiências construídas em cima, como livros de trabalho, dashboards, experiências de clientes baseadas em API, insights no portal Azure, e muito mais. 

Note que as experiências de consumo não-portal também têm de estar a funcionar dentro do VNET ligado ao privado que inclui as cargas de trabalho monitorizadas. 

Você precisará adicionar recursos que hospedam as cargas de trabalho monitorizadas no link privado. Aqui está a [documentação](https://docs.microsoft.com/azure/app-service/networking/private-endpoint) para como fazer isso para os Serviços de Aplicações.

Restringir o acesso desta forma apenas se aplica aos dados do recurso Application Insights. As alterações de configuração, incluindo ligar ou desligar estas definições de acesso, são geridas pelo Gestor de Recursos Do Azure. Em vez disso, restringir o acesso ao Gestor de Recursos utilizando as funções, permissões, controlos de rede e auditoria adequados. Para mais informações, consulte [funções, permissões e segurança do Monitor Azure.](roles-permissions-security.md)

> [!NOTE]
> Para garantir totalmente os Insights de Aplicação baseados no espaço de trabalho, é necessário bloquear tanto o acesso ao recurso Application Insights como o espaço de trabalho subjacente ao Log Analytics.

> [!NOTE]
> Os diagnósticos de nível de código (profiler/debugger) atualmente não suportam private link.

## <a name="use-apis-and-command-line"></a>Utilize APIs e linha de comando

Pode automatizar o processo descrito anteriormente utilizando modelos de Gestor de Recursos Azure e interfaces de linha de comando.

Para criar e gerir os âmbitos de ligação privada, utilize [az monitor de ligação privada- âmbito](https://docs.microsoft.com/cli/azure/monitor/private-link-scope?view=azure-cli-latest). Utilizando este comando, pode criar âmbitos, associar espaços de trabalho de Log Analytics e componentes de Insights de Aplicação e adicionar/remover/aprovar pontos finais privados.

Para gerir o acesso à rede, utilize as bandeiras e os espaços de trabalho do Log Analytics ou componentes de Insights de `[--ingestion-access {Disabled, Enabled}]` `[--query-access {Disabled, Enabled}]` [Aplicação](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest). [Log Analytics workspaces](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest)

## <a name="collect-custom-logs-over-private-link"></a>Recolher registos personalizados sobre link privado

As contas de armazenamento são utilizadas no processo de ingestão de registos personalizados. Por predefinição, são utilizadas contas de armazenamento geridas pelo serviço. No entanto, para ingerir registos personalizados em links privados, deve utilizar as suas próprias contas de armazenamento e associá-las ao espaço de trabalho do Log Analytics. Consulte mais detalhes sobre como configurar tais contas utilizando a [linha de comando](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace/linked-storage?view=azure-cli-latest).

Para obter mais informações sobre o fornecimento da sua própria conta de armazenamento, consulte [as contas de armazenamento do Cliente para ingestão de loging](private-storage.md)

## <a name="restrictions-and-limitations"></a>Restrições e limitações

### <a name="agents"></a>Agentes

As versões mais recentes dos agentes Windows e Linux devem ser utilizadas em redes privadas para permitir a ingestão de telemetria segura para espaços de trabalho log Analytics. Versões mais antigas não podem carregar dados de monitorização numa rede privada.

**Agente do Windows do Log Analytics**

Utilize a versão do agente Log Analytics 18.20.18038.0 ou posterior.

**Agente do Linux do Log Analytics**

Utilize a versão 1.12.25 do agente ou mais tarde. Se não puder, faça os seguintes comandos no seu VM.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Portal do Azure

Para utilizar experiências do portal Azure Monitor, como Application Insights e Log Analytics, é necessário permitir que as extensões do portal Azure e do Monitor Azure sejam acessíveis nas redes privadas. Adicione as etiquetas de serviço **AzureActiveDirectory,** **AzureResourceManager,****AzureFrontDoor.FirstParty e **AzureFrontdoor.Frontend** [service tags](../../firewall/service-tags.md) à sua firewall.

### <a name="programmatic-access"></a>Acesso programático

Para utilizar o REST API, [CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest) ou PowerShell com o Monitor Azure em redes privadas, adicione as [etiquetas](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)de serviço**AzureActiveDirectory** e **AzureResourceManager** à sua firewall.  

A adição destas tags permite-lhe realizar ações como consultar dados de registo, criar e gerir espaços de trabalho do Log Analytics e componentes de IA.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Aplicação Insights SDK downloads de uma rede de entrega de conteúdos

Agrega o código JavaScript no seu script para que o navegador não tente descarregar código a partir de um CDN. Um exemplo é fornecido no [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="log-analytics-solution-download"></a>Download de solução Log Analytics

Para permitir que o Agente Log Analytics descarregue pacotes de soluções, adicione os nomes de domínio completos apropriados à sua lista de permitir a sua firewall. 


| Ambiente de nuvem | Recursos do Agente | Portas | Direção |
|:--|:--|:--|:--|
|Azure Público     | scadvisor.blob.core.windows.net         | 443 | Saída
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Saída
|Azure China 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Saída

