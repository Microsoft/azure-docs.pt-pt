---
title: Utilize o Link Privado Azure para ligar de forma segura as redes ao Azure Monitor
description: Utilize o Link Privado Azure para ligar de forma segura as redes ao Azure Monitor
author: nkiest
ms.author: nikiest
ms.topic: conceptual
ms.date: 05/20/2020
ms.subservice: ''
ms.openlocfilehash: 47c504d9359779294c4690059d1958614d863e58
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85260887"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Utilize o Link Privado Azure para ligar de forma segura as redes ao Azure Monitor

> [!IMPORTANT]
> Neste momento, deve **solicitar o acesso** para utilizar esta capacidade. Pode solicitar o acesso utilizando o [formulário de inscrição](https://aka.ms/AzMonPrivateLinkSignup).


[O Azure Private Link](../../private-link/private-link-overview.md) permite-lhe ligar de forma segura os serviços Azure PaaS à sua rede virtual utilizando pontos finais privados. Para muitos serviços, basta configurar um ponto final por recurso. No entanto, o Azure Monitor é uma constelação de diferentes serviços interligados que trabalham em conjunto para monitorizar as suas cargas de trabalho. Como resultado, construímos um recurso chamado Azure Monitor Private Link Scope (AMPLS) que lhe permite definir os limites da sua rede de monitorização e ligar-se à sua rede virtual. Este artigo abrange quando usar e como configurar um Azure Monitor Private Link Scope.

## <a name="advantages"></a>Vantagens

Com Link Privado pode:

- Ligue-se privadamente ao Azure Monitor sem abrir qualquer acesso público à rede
- Certifique-se de que os seus dados de monitorização só são acedidos através de redes privadas autorizadas
- Impedir a exfiltração de dados das suas redes privadas definindo recursos específicos do Azure Monitor que se conectam através do seu ponto final privado
- Ligue de forma segura a sua rede privada no local ao Azure Monitor utilizando o ExpressRoute e o Private Link
- Mantenha todo o tráfego dentro da rede de espinha dorsal do Microsoft Azure

Para mais informações, consulte [os Principais Benefícios do Link Privado.](../../private-link/private-link-overview.md#key-benefits)

## <a name="how-it-works"></a>Como funciona

Azure Monitor Private Link Scope é um recurso de agrupamento para ligar um ou mais pontos finais privados (e, portanto, as redes virtuais em que estão contidos) a um ou mais recursos do Azure Monitor. Os recursos incluem espaços de trabalho Log Analytics e componentes application insights.

![Diagrama de topologia de recursos](./media/private-link-security/private-link-topology-1.png)

> [!NOTE]
> Um único recurso Azure Monitor pode pertencer a vários AMPLSs, mas não é possível ligar um único VNet a mais de um AMPLS. 

## <a name="planning-based-on-your-network"></a>Planeamento com base na sua rede

Antes de configurar os seus recursos AMPLS, considere os seus requisitos de isolamento de rede. Avalie o acesso das suas redes virtuais à internet pública e as restrições de acesso de cada um dos seus recursos do Azure Monitor (isto é, componentes de Application Insights e espaços de trabalho Log Analytics).

### <a name="evaluate-which-virtual-networks-should-connect-to-a-private-link"></a>Avaliar quais redes virtuais devem ligar-se a uma Ligação Privada

Comece por avaliar quais das suas redes virtuais (VNets) têm acesso restrito à internet. Os VNets que tenham internet gratuita podem não necessitar de um Link Privado para aceder aos seus recursos do Azure Monitor. Os recursos de monitorização a que os seus VNets se ligam podem restringir o tráfego de entrada e requerem uma ligação de Ligação Privada (seja para ingestão de registos ou consulta). Nesses casos, mesmo um VNet que tenha acesso à internet pública precisa de se conectar a estes recursos através de uma Ligação Privada, e através de um AMPLS.

### <a name="evaluate-which-azure-monitor-resources-should-have-a-private-link"></a>Avaliar quais os recursos do Azure Monitor que devem ter uma ligação privada

Reveja cada um dos seus recursos do Monitor Azure:

- O recurso deve permitir a ingestão de registos a partir de recursos localizados apenas em VNets específicos?
- O recurso deve ser consultado apenas por clientes localizados em VNETs específicos?

Se a resposta a qualquer uma destas perguntas for sim, desafie as restrições como explicado nos espaços de trabalho [do Log Analytics](#configure-log-analytics) e nos componentes de Configuração de Insights de Aplicação e [associe](#configure-application-insights) esses recursos a um ou vários AMPLS(s). As redes virtuais que devem aceder a estes recursos de monitorização precisam de ter um Ponto Final Privado que se conecte aos AMPLS relevantes.
Lembre-se – pode ligar os mesmos espaços de trabalho ou aplicação a múltiplos AMPLS, para permitir que sejam alcançados por diferentes redes.

### <a name="group-together-monitoring-resources-by-network-accessibility"></a>Grupo de monitorização em conjunto por acessibilidade em rede

Uma vez que cada VNet pode ligar-se a apenas um recurso AMPLS, deve agrupar recursos de monitorização que devem ser acessíveis às mesmas redes. A forma mais simples de gerir este agrupamento é criar um AMPLS por VNet e selecionar os recursos para se ligar a essa rede. No entanto, para reduzir recursos e melhorar a gestão, poderá querer reutilizar um AMPLS através das redes.

Por exemplo, se as suas redes virtuais internas VNet1 e VNet2 deverão ligar-se aos espaços de trabalho Workspace1 e Workspace2 e componente de aplicações Insights Application Insights 3, associar os três recursos aos mesmos AMPLS. Se o VNet3 apenas tiver acesso ao Workspace1, criar outro recurso AMPLS, associar o Workspace1 ao mesmo e ligar o VNet3 como mostrado nos seguintes diagramas:

![Diagrama de AMPLS Uma topologia](./media/private-link-security/ampls-topology-a-1.png)

![Diagrama de topologia AMPLS B](./media/private-link-security/ampls-topology-b-1.png)

## <a name="example-connection"></a>Ligação de exemplo

Comece por criar um recurso Azure Monitor Private Link Scope.

1. Vá para **criar um recurso** no portal Azure e procure o **Azure Monitor Private Link Scope**.

   ![Encontre o âmbito de ligação privada do monitor Azure](./media/private-link-security/ampls-find-1c.png)

2. Clique **em criar**.
3. Escolha um Grupo de Subscrição e Recursos.
4. Dê um nome aos AMPLS. É melhor usar um nome que seja claro para que finalidade e fronteira de segurança o Scope será usado para que alguém não quebre acidentalmente os limites de segurança da rede. Por exemplo, "AppServerProdTelem".
5. Clique **em Rever + Criar**. 

   ![Criar âmbito de ligação privada do Monitor Azure](./media/private-link-security/ampls-create-1d.png)

6. Deixe passar a validação e, em seguida, clique em **Criar**.

## <a name="connect-azure-monitor-resources"></a>Ligar recursos do Monitor Azure

Pode ligar os seus AMPLS primeiro a pontos finais privados e depois aos recursos do Azure Monitor ou vice-versa, mas o processo de ligação é mais rápido se começar com os recursos do Azure Monitor. Eis como ligamos os espaços de trabalho do Azure Monitor Log Analytics e os componentes de Insights de Aplicação a um AMPLS

1. No seu âmbito de ligação privada Azure Monitor, clique em **Recursos monitores Azure** no menu à esquerda. Clique no botão **Adicionar**.
2. Adicione o espaço de trabalho ou o componente. Clicar no botão **Adicionar** traz um diálogo onde pode selecionar os recursos do Azure Monitor. Pode navegar através das suas subscrições e grupos de recursos, ou pode escrever o seu nome para filtrar até elas. Selecione o espaço de trabalho ou componente e clique em **Aplicar** para adicioná-los ao seu âmbito.

    ![Screenshot de selecionar um âmbito UX](./media/private-link-security/ampls-select-2.png)

### <a name="connect-to-a-private-endpoint"></a>Ligar-se a um ponto final privado

Agora que tem recursos ligados ao seu AMPLS, crie um ponto final privado para ligar a nossa rede. Pode fazer esta tarefa no [portal Azure Private Link center,](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)ou dentro do seu Azure Monitor Private Link Scope, como feito neste exemplo.

1. No seu recurso de âmbito, clique nas **ligações Private Endpoint** no menu de recursos da esquerda. Clique no **Ponto Final Privado** para iniciar o processo de criação de ponto final. Também pode aprovar ligações que foram iniciadas no centro de Ligação Privada aqui, selecionando-as e clicando **em Aprovar.**

    ![Screenshot de Private Endpoint Connections UX](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Escolha a subscrição, o grupo de recursos e o nome do ponto final, e a região em que deve viver. A região tem de ser a mesma região que a rede virtual a que a vais ligar.

3. Clique **em seguida: Recurso**. 

4. No ecrã de Recursos,

   a. Escolha a **Subscrição** que contém o seu recurso Azure Monitor Private Scope. 

   b. Para **o tipo de recurso,** escolha **Microsoft.insights/privateLinkScopes**. 

   c. A partir da entrega de **recursos,** escolha o seu âmbito de Ligação Privada que criou anteriormente. 

   d. Clique **em seguida: configuração >**.
      ![Screenshot de selecionar Criar Ponto Final Privado](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. No painel de configuração,

   a.    Escolha a **rede virtual** e **a sub-rede** que pretende ligar aos recursos do Monitor Azure. 
 
   b.    Escolha **Sim** para **Integrar com a zona privada de DNS,** e deixe-o criar automaticamente uma nova Zona Privada de DNS. 
 
   c.    Clique em **Rever + criar**.
 
   d.    Deixe a validação passar. 
 
   e.    Clique em **Criar**. 

    ![Screenshot de seleção Criar Ponto Final Privado2](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Criou agora um novo ponto final privado que está ligado a este âmbito de ligação privada do Azure Monitor.

## <a name="configure-log-analytics"></a>Configurar a análise de registo

Aceda ao portal do Azure. No seu recurso de espaço de trabalho Azure Monitor Log Analytics é um item de menu **Isolamento de rede** no lado esquerdo. Você pode controlar dois estados diferentes deste menu. 

![Isolamento da Rede LA](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

Em primeiro lugar, pode ligar este recurso Log Analytics a quaisquer âmbitos de ligação privada do Monitor Azure a que tenha acesso. Clique **em Adicionar** e selecione o Âmbito de Ligação Privada do Monitor Azure.  Clique **em Aplicar** para conectá-lo. Todos os telescópios conectados aparecem neste ecrã. A realização desta ligação permite que o tráfego de rede nas redes virtuais conectadas chegue a este espaço de trabalho. Fazer a ligação tem o mesmo efeito que ligá-la do âmbito que fizemos na [ligação dos recursos do Monitor Azure](#connect-azure-monitor-resources).  

Em segundo lugar, pode controlar como este recurso pode ser alcançado a partir de fora dos âmbitos de ligação privado listados acima. Se definir **Permita o acesso à rede pública para ingestão** a **Nº,** então as máquinas fora dos âmbitos ligados não podem enviar dados para este espaço de trabalho. Se definir Permita o acesso à **rede pública para consultas** a **No,** então as máquinas fora dos âmbitos não podem aceder aos dados neste espaço de trabalho. Esses dados incluem acesso a livros de trabalho, dashboards, experiências de clientes baseadas em API, insights no portal Azure, e muito mais. As experiências que correm fora do portal Azure que consomem dados do Log Analytics também têm de estar a decorrer dentro do VNET de ligação privada.

Restringir o acesso desta forma só se aplica aos dados no espaço de trabalho. As alterações de configuração, incluindo ligar ou desligar estas definições de acesso, são geridas pelo Azure Resource Manager. Restringir o acesso ao Gestor de Recursos utilizando as funções, permissões, controlos de rede e auditoria adequados. Para mais informações, consulte [as Funções, Permissões e Segurança do Monitor Azure.](roles-permissions-security.md)

> [!NOTE]
> Os registos e métricas enviados para um espaço de trabalho através [de Definições de Diagnóstico](diagnostic-settings.md) passam por um canal privado seguro da Microsoft e não são controlados por estas definições.

## <a name="configure-application-insights"></a>Configure insights de aplicação

Aceda ao portal do Azure. No seu recurso componente Azure Monitor Application Insights está um item de menu **Isolamento de rede** no lado esquerdo. Você pode controlar dois estados diferentes deste menu.

![Isolamento da Rede AI](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Em primeiro lugar, pode ligar este recurso Application Insights aos âmbitos de ligação privada do Monitor Azure a que tem acesso. Clique **em Adicionar** e selecione o Âmbito de **Ligação Privada do Monitor Azure**. Clique em Aplicar para conectá-lo. Todos os telescópios conectados aparecem neste ecrã. A realização desta ligação permite que o tráfego de rede nas redes virtuais conectadas atinja este componente. Fazer a ligação tem o mesmo efeito que ligá-la do âmbito que fizemos na [ligação dos recursos do Monitor Azure](#connect-azure-monitor-resources). 

Em segundo lugar, pode controlar como este recurso pode ser alcançado a partir de fora dos âmbitos de ligação privado listados anteriormente. Se definir **Permita o acesso à rede pública para ingestão** a **Nº,** então as máquinas ou SDKs fora dos âmbitos ligados não podem enviar dados para este componente. Se definir **Permitir o acesso à rede pública para consultas** a **Não,** então as máquinas fora dos âmbitos não podem aceder aos dados neste recurso Application Insights. Esses dados incluem acesso a registos APM, métricas e o fluxo de métricas ao vivo, bem como experiências construídas em cima, tais como livros de trabalho, dashboards, experiências de clientes baseadas em consultas com API, insights no portal Azure, e muito mais. 

Note que as experiências de consumo não-portal também têm de ser funcionadas dentro do VNET de ligação privada que inclui as cargas de trabalho monitorizadas. 

Você precisará adicionar recursos que hospedam as cargas de trabalho monitorizadas para o link privado. Aqui está a [documentação](https://docs.microsoft.com/azure/app-service/networking/private-endpoint) para como fazer isto para os Serviços de Aplicações.

Restringir o acesso desta forma só se aplica aos dados do recurso Application Insights. As alterações de configuração, incluindo ligar ou desligar estas definições de acesso, são geridas pelo Azure Resource Manager. Em vez disso, restringir o acesso ao Gestor de Recursos utilizando as funções, permissões, controlos de rede e auditorias apropriados. Para mais informações, consulte [as Funções, Permissões e Segurança do Monitor Azure.](roles-permissions-security.md)

> [!NOTE]
> Para garantir totalmente os Insights de Aplicação baseados no espaço de trabalho, é necessário bloquear tanto o acesso ao recurso Application Insights como o espaço de trabalho subjacente ao Log Analytics.

> [!NOTE]
> Atualmente, os diagnósticos ao nível do código (profiler/debugger) não suportam o Private Link.

## <a name="use-apis-and-command-line"></a>Use APIs e linha de comando

Pode automatizar o processo descrito anteriormente utilizando modelos do Gestor de Recursos Azure e interfaces de linha de comando.

Para criar e gerir os âmbitos de ligação privados, utilize [a az monitorar o âmbito de ligação privada](https://docs.microsoft.com/cli/azure/monitor/private-link-scope?view=azure-cli-latest). Utilizando este comando, pode criar âmbitos, associar espaços de trabalho do Log Analytics e componentes de Insights de Aplicação e adicionar/remover/aprovar pontos finais privados.

Para gerir o acesso à rede, utilize as bandeiras `[--ingestion-access {Disabled, Enabled}]` e nos espaços de trabalho do Log `[--query-access {Disabled, Enabled}]` [Analytics](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest) ou nos componentes do [Application Insights](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest).

## <a name="collect-custom-logs-over-private-link"></a>Colete registos personalizados sobre o Link Privado

As contas de armazenamento são utilizadas no processo de ingestão de registos personalizados. Por predefinição, são utilizadas contas de armazenamento geridas pelo serviço. No entanto, para ingerir registos personalizados em links privados, deve utilizar as suas próprias contas de armazenamento e associá-las ao espaço de trabalho log Analytics. Consulte mais detalhes sobre como configurar tais contas utilizando a [linha de comando](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace/linked-storage?view=azure-cli-latest).

Para obter mais informações sobre como trazer a sua própria conta de armazenamento, consulte [as contas de armazenamento do cliente para ingestão de registos](private-storage.md)

## <a name="restrictions-and-limitations"></a>Restrições e limitações

### <a name="agents"></a>Agentes

As versões mais recentes dos agentes Windows e Linux devem ser utilizadas em redes privadas para permitir a ingestão segura de telemetria nos espaços de trabalho do Log Analytics. As versões mais antigas não podem carregar dados de monitorização numa rede privada.

**Agente do Windows do Log Analytics**

Utilize a versão 10.20.18038.0 ou posterior do agente Log Analytics.

**Agente do Linux do Log Analytics**

Utilize a versão 1.12.25 ou posterior do agente. Se não puder, executar os seguintes comandos no seu VM.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Portal do Azure

Para utilizar experiências do portal Azure Monitor, como Application Insights e Log Analytics, é necessário permitir que as extensões do portal Azure e do Azure Monitor sejam acessíveis nas redes privadas. Adicione [tags](../../firewall/service-tags.md) de serviço **AzureActiveDirectory**, **AzureResourceManager**, **AzureFrontDoor.FirstParty**e **AzureFrontdoor.Frontend** à sua firewall.

### <a name="programmatic-access"></a>Acesso programático

Para utilizar o REST API, [CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest) ou PowerShell com O Azure Monitor em redes [privadas,](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)adicione as tags de serviço**AzureActiveDirectory** e **AzureResourceManager** à sua firewall.  

A adição destas tags permite-lhe executar ações como consultar dados de registo, criar e gerir espaços de trabalho do Log Analytics e componentes de IA.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Application Insights SDK descarrega a partir de uma rede de entrega de conteúdos

Embrulhe o código JavaScript no seu script para que o navegador não tente descarregar código a partir de um CDN. Um exemplo é dado no [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="log-analytics-solution-download"></a>Download de solução Log Analytics

Para permitir que o Agente Desafiá-lo descarregue pacotes de soluções, adicione os nomes de domínio totalmente qualificados apropriados à sua lista de autorizações de firewall. 


| Ambiente em nuvem | Recursos do Agente | Portas | Direção |
|:--|:--|:--|:--|
|Azure Público     | scadvisor.blob.core.windows.net         | 443 | Saída
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Saída
|Azure China 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Saída

### <a name="browser-dns-settings"></a>Configurações de DNS do navegador

Se estiver a ligar-se aos recursos do Monitor Azure através de uma Ligação Privada, o tráfego para estes recursos deve passar pelo ponto final privado que está configurado na sua rede. Para ativar o ponto final privado, atualize as definições de DNS como explicado no [Connect para um ponto final privado](#connect-to-a-private-endpoint). Alguns navegadores usam as suas próprias definições de DNS em vez das que define. O navegador pode tentar ligar-se aos pontos finais públicos do Azure Monitor e contornar totalmente o Link Privado. Verifique se as definições dos seus navegadores não se sobrepõem ou cache as definições de DNS antigas. 

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [armazenamento privado](private-storage.md)
