---
title: Ligar o Operations Manager para o Azure Monitor | Documentos da Microsoft
description: Para manter o seu investimento existente no System Center Operations Manager e utilizar as capacidades expandidas com o Log Analytics, pode integrar o Operations Manager com a área de trabalho.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 245ef71e-15a2-4be8-81a1-60101ee2f6e6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/23/2018
ms.author: magoedte
ms.openlocfilehash: 3e11c4dc8aa082723223fb998e599aa514febd3b
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369973"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Ligar o Operations Manager para o Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Para manter o investimento existente no [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807) e utilizar as capacidades expandidas com o Azure Monitor, pode integrar o Operations Manager com a sua área de trabalho do Log Analytics. Isso permite a que aproveitar as oportunidades de registos no Azure Monitor e continuar a utilizar o Gestor de operações para:

* Monitorizar o estado de funcionamento dos serviços de TI com o Operations Manager
* Manter a integração com as soluções ITSM que suportam a gestão de incidentes e problemas
* Gerir o ciclo de vida dos agentes implementados nas máquinas virtuais IaaS no local e na cloud pública que o utilizador monitoriza com o Operations Manager

Integração com o System Center Operations Manager adiciona valor para a sua estratégia de operações de serviço utilizando a velocidade e a eficiência do Azure Monitor na recolher, armazenar e analisar dados de registo do Operations Manager. A correlação de ajuda de consultas de registo Monitor do Azure e trabalho para identificar as falhas de problemas e analisar recorrências para oferecer suporte a seu processo de gerenciamento existente do problema. A flexibilidade do motor de consulta para examinar o desempenho, eventos e alerta de dados, com dashboards avançados e capacidades de relatórios para expor estes dados de formas significativas, demonstra a força do Azure Monitor traz complimenting do Operations Manager.

Os agentes que reportam ao grupo de gestão do Operations Manager recolher dados de seus servidores com base no [origens de dados do Log Analytics](agent-data-sources.md) e soluções que ativou na sua área de trabalho. Dependendo de soluções de ativadas, os dados são enviados diretamente a partir de um servidor de gestão do Operations Manager para o serviço ou devido ao volume de dados recolhidos no sistema gerido por agente, são enviados diretamente do agente para uma área de trabalho do Log Analytics. O servidor de gestão reencaminha os dados diretamente para o serviço; estes nunca são escritos na base de dados operacional ou do armazém de dados. Quando um servidor de gestão perde a conectividade com o Azure Monitor, ele coloca em cache os dados localmente até que a comunicação é restabelecida. Se o servidor de gestão estiver offline devido a manutenção planeada ou interrupções não planeadas, outro servidor de gestão no grupo de gestão retoma a conectividade com o Azure Monitor.  

O diagrama seguinte mostra a ligação entre os servidores de gestão e agentes num grupo de gestão do System Center Operations Manager e o Azure Monitor, incluindo as portas e direção.   

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

Se as políticas de segurança de TI não permitir que os computadores na sua rede para ligar à Internet, os servidores de gestão podem ser configurados para ligar ao gateway do Log Analytics para receber informações de configuração e enviar os dados recolhidos consoante as soluções ativado. Para obter mais informações e passos sobre como configurar o grupo de gestão do Operations Manager para comunicar através de um gateway do Log Analytics para o Azure Monitor, consulte [ligar computadores ao utilizar o gateway do Log Analytics do Azure Monitor](../../azure-monitor/platform/gateway.md).  

## <a name="prerequisites"></a>Pré-requisitos 
Antes de começar, reveja os seguintes requisitos.

* O Azure Monitor só suporta o System Center Operations Manager 1807, o Operations Manager 1801, o Operations Manager 2016, o Operations Manager 2012 SP1 UR6 ou posterior e o Operations Manager 2012 R2 UR2 ou posterior. Foi adicionado suporte de proxy ao Operations Manager 2012 SP1 UR7 e ao Operations Manager 2012 R2 UR3.
* Todos os agentes do Operations Manager têm de cumprir os requisitos mínimos de suporte. Certifique-se de que agentes estão na mínima atualização, caso contrário a comunicação do agente de Windows pode falhar e gerar erros no registo de eventos do Operations Manager.
* Uma área de trabalho do Log Analytics. Para obter mais informações, consulte [descrição geral de área de trabalho do Log Analytics](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json).
* Autenticar para o Azure com uma conta que seja membro dos [função de Contribuidor do Log Analytics](../../azure-monitor/platform/manage-access.md#manage-accounts-and-users).  

>[!NOTE]
>Alterações recentes às APIs do Azure irão impedir que os clientes a capacidade de configurar com êxito a integração entre o grupo de gestão e o Azure Monitor pela primeira vez. Para os clientes que já tem integrado o respetivo grupo de gestão com o serviço, não são afetados, exceto se precisar de reconfigurar a ligação existente.  
>Foi lançado um novo pacote de gestão para as seguintes versões do Operations Manager:
>  
>* Para o System Center Operations Manager 1801, transferir o pacote de gestão [aqui](https://www.microsoft.com/download/details.aspx?id=57173)  
>* Para o System Center 2016 – Operations Manager, transferir o pacote de gestão [aqui](https://www.microsoft.com/download/details.aspx?id=57172)  
>* Para o System Center Operations Manager 2012 R2, Baixe o pacote de gestão [aqui](https://www.microsoft.com/download/details.aspx?id=57171)  
>
>Esta atualização de pacote de gestão não é aplicável ao System Center Operations Manager 1807, que é uma versão de atualização da versão 1801 e não uma compilação completa do produto.   

### <a name="network"></a>Rede
As informações abaixo lista as informações de configuração de proxy e de firewall necessárias para o agente do Operations Manager, servidores de gestão e consola de operações comunicar com o Azure Monitor. Tráfego a partir de cada componente é de saída a partir da sua rede para o Azure Monitor.   

|Recurso | Número da porta| Inspeção de HTTP Direto|  
|---------|------|-----------------------|  
|**Agente**|||  
|\*.ods.opinsights.azure.com| 443 |Sim|  
|\*.oms.opinsights.azure.com| 443|Sim|  
|\*.blob.core.windows.net| 443|Sim|  
|\*.azure-automation.net| 443|Sim|  
|**Servidor de gestão**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Sim|  
|\*.ods.opinsights.azure.com| 443| Sim|  
|*.azure-automation.net | 443| Sim|  
|**Consola do Operations Manager para o Azure Monitor**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 e 443||  
|\*.microsoft.com| 80 e 443||  
|\*.microsoftonline.com| 80 e 443||  
|\*.mms.microsoft.com| 80 e 443||  
|login.windows.net| 80 e 443||  
|portal.loganalytics.io| 80 e 443||
|api.loganalytics.io| 80 e 443||
|docs.loganalytics.io| 80 e 443||  

### <a name="tls-12-protocol"></a>Protocolo TLS 1.2
Para garantir a segurança dos dados em trânsito para o Azure Monitor, recomendamos que configure o grupo de gestão e agente a utilizar, pelo menos, Transport Layer Security (TLS) 1.2. As versões mais antigas do TLS/Secure Sockets Layer (SSL) foram encontradas vulneráveis e enquanto trabalham ainda atualmente para permitir a compatibilidade com versões anteriores, estão **não recomendada**. Para obter mais informações, consulte [enviar dados de forma segura através de TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

## <a name="connecting-operations-manager-to-azure-monitor"></a>Ligar o Operations Manager para o Azure Monitor
Realize esta série de passos para configurar o grupo de gestão do Operations Manager para se ligar a uma das suas áreas de trabalho do Log Analytics.

Durante o registo inicial do seu grupo de gestão do Operations Manager com uma área de trabalho do Log Analytics, a opção de especificar a configuração de proxy para o grupo de gestão não está disponível na consola de operações.  O grupo de gestão tem de ser registado com êxito no serviço para esta opção ficar disponível.  Para contornar este problema, tem de atualizar a configuração de proxy do sistema usando Netsh no sistema de seu em execução, a consola de operações, para configurar a integração e todos os servidores de gestão no grupo de gestão.  

1. Abra uma linha de comandos elevada.
   a. Aceda a **começar** e escreva **cmd**.
   b. Com o botão direito **linha de comandos** e selecionar executar como administrador * *.
1. Introduza o seguinte comando e prima **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Depois de concluir os passos seguintes para integrar com o Azure Monitor, pode remover a configuração, executando `netsh winhttp reset proxy` e, em seguida, utilizar o **configurar o servidor de proxy** opção na consola de operações para especificar o proxy ou de registo Servidor de gateway de análise. 

1. Na consola do Operations Manager, selecione a área de trabalho **Administração**.
1. Expanda o nó Operations Management Suite e clique em **Ligação**.
1. Clique na ligação **Registar no Operations Management Suite**.
1. Sobre o **Assistente de integração do Operations Management Suite: Autenticação** página, introduza o endereço de e-mail ou o número de telefone e a palavra-passe da conta de administrador que está associada a sua subscrição do OMS e clique em **iniciar sessão**.

   >[!NOTE]
   >O nome do Operations Management Suite foi preterido. 
   
1. Depois que serem autenticadas com êxito, no **Assistente de integração do Operations Management Suite: Selecione a área de trabalho** página, lhe for pedido para selecionar o seu inquilino do Azure, a subscrição e a área de trabalho do Log Analytics. Se tiver mais do que uma área de trabalho, selecione a área de trabalho que pretende registar com o grupo de gestão do Operations Manager na lista pendente e clique em **Seguinte**.
   
   > [!NOTE]
   > O Operations Manager suporta apenas uma área de trabalho do Log Analytics de cada vez. A ligação e os computadores que foram registados para o Azure Monitor com a área de trabalho anterior são removidos do Azure Monitor.
   > 
   > 
1. Sobre o **Assistente de integração do Operations Management Suite: Resumo** página, confirme as suas definições e se eles estiverem corretos, clique em **criar**.
1. Sobre o **Assistente de integração do Operations Management Suite: Concluir** página, clique em **fechar**.

### <a name="add-agent-managed-computers"></a>Adicionar computadores geridos por agentes
Depois de configurar a integração com sua área de trabalho do Log Analytics, apenas estabelece uma ligação com o serviço, não existem dados são recolhidos a partir os agentes que reportam ao grupo de gestão. Isso não acontece até depois de configurar os computadores geridos por agente específicos recolher dados de registo para o Azure Monitor. Pode selecionar os objetos de computador individualmente ou pode selecionar um grupo que contenha objetos de computador do Windows. Não pode selecionar um grupo que contenha instâncias de outra classe, tais como discos lógicos ou bases de dados SQL.

1. Abra a consola do Operations Manager e selecione a área de trabalho de **Administração**.
1. Expanda o nó Operations Management Suite e clique em **Ligação**.
1. Clique na ligação **Adicionar um Computador/Grupo** sob o cabeçalho Ações, no lado direito do painel.
1. Na caixa de diálogo **Pesquisa no Computador**, pode procurar os computadores ou os grupos monitorizados pelo Operations Manager. Selecione computadores ou grupos para carregar para o Azure Monitor, clique em **Add**e, em seguida, clique em **OK**.

Pode ver os computadores e os grupos configurados para recolher dados a partir do nó Computadores Geridos no Operations Management Suite na área de trabalho **Administração** da Consola de operações. Aqui, pode adicionar ou remover computadores e grupos, conforme necessário.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Configurar as definições do proxy na Consola de operações
Execute os seguintes passos, se um servidor proxy interno é entre o grupo de gestão e o Azure Monitor. Estas definições são centralmente gerenciadas do grupo de gestão e distribuídas para sistemas geridos por agentes que estão incluídos no âmbito para recolher dados de registo para o Azure Monitor.  Tal é benéfico para quando determinadas soluções ignoram o servidor de gestão e enviam dados diretamente para o serviço.

1. Abra a consola do Operations Manager e selecione a área de trabalho de **Administração**.
1. Expanda o Operations Management Suite e clique em **Ligações**.
1. Na vista Ligação OMS, clique em **Configurar Servidor Proxy**.
1. No **assistente Operations Management Suite: Servidor proxy** página, selecione **utilizar um servidor proxy para aceder ao Operations Management Suite**, e, em seguida, escreva o URL com o número de porta, por exemplo, http://corpproxy:80 e, em seguida, clique em **concluir** .

Se o servidor proxy requer autenticação, execute os seguintes passos para configurar as credenciais e as definições que têm de ser propagados para computadores geridos que informa ao Azure Monitor, no grupo de gestão.

1. Abra a consola do Operations Manager e selecione a área de trabalho de **Administração**.
1. Em **Configuração RunAs**, selecione **Perfis**.
1. Abra o perfil **System Center Advisor Run As Profile Proxy**.
1. No Assistente do Perfil Run As, clique em Adicionar para utilizar uma conta Run As. Pode criar uma [conta Run As](https://technet.microsoft.com/library/hh321655.aspx) ou utilizar uma existente. Esta conta tem de ter permissões suficientes para passar através do servidor proxy.
1. Para configurar a conta a gerir, escolha **Uma classe, um grupo ou um objeto selecionado**, clique em **Selecionar...** e, em seguida, clique em **Grupo...** para abrir a caixa **Pesquisa de Grupo**.
1. Pesquise e, em seguida, selecione **Microsoft System Center Advisor Monitoring Server Group**. Clique em **OK** depois de selecionar o grupo para fechar a caixa **Pesquisa de Grupo**.
1. Clique em **OK** para fechar a caixa **Adicionar uma conta Run As**.
1. Clique em **Guardar** para concluir o assistente e guardar as alterações.

Depois da ligação é criada e configurar os agentes irão recolher e comunicar dados de registo para o Azure Monitor, a seguinte configuração é aplicada no grupo de gestão, não necessariamente por ordem:

* É criada a Conta Run As **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate**. A conta é associada ao perfil Run As **Blob do Perfil Run As do Microsoft System Center Advisor** e é direcionada para duas classes – **Servidor de Recolha** e **Grupo de Gestão do Operations Manager**.
* São criados dois conectores.  A primeira é denominada **Microsoft.SystemCenter.Advisor.DataConnector** e é automaticamente configurado com uma subscrição que encaminha todos os alertas gerados a partir de instâncias de todas as classes no grupo de gestão para o Azure Monitor. É o segundo conector **Advisor Connector**, que é responsável por comunicar com o Azure Monitor e partilha de dados.
* Os agentes e os grupos que selecionou para recolher dados no grupo de gestão são adicionados ao **Grupo de Servidores de Monitorização do Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Atualizações dos pacotes de gestão
Depois de concluída a configuração, o grupo de gestão do Operations Manager estabelece uma conexão com o Azure Monitor. O servidor de gestão é sincronizado com o serviço Web e recebe informações de configuração atualizadas sob a forma de pacotes de gestão para as soluções ativadas que se integram com o Operations Manager. Gestor de operações verifica a existência de atualizações destes pacotes de gestão e automaticamente transferir e importa-los quando estiverem disponíveis. Existem duas regras específicas que controlam este comportamento:

* **Microsoft.SystemCenter.Advisor.MPUpdate** -atualiza os pacotes de gestão do Azure Monitor bases. Por predefinição, é executada a cada 12 horas.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** – Atualiza os pacotes de gestão de soluções ativadas na sua área de trabalho. Por predefinição, é executada a cada cinco (5) minutos.

Pode substituir estas duas regras para impedir a transferência automática, desativando-los ou modificar a frequência para a frequência com que o servidor de gestão sincroniza com Azure Monitorto determinam se um novo pacote de gestão está disponível e deve ser transferido. Siga os passos em [Como Substituir uma Regra ou um Monitor](https://technet.microsoft.com/library/hh212869.aspx) para modificar o parâmetro **Frequência** por um valor em segundos, para alterar a agenda de sincronização ou modificar o parâmetro **Ativada** para desativar as regras. Escolha as substituições para todos os objetos da classe do Grupo de Gestão do Operations Manager.

Para continue a seguir o processo de controle de alteração existentes para controlar versões de pacote de gestão no grupo de gestão de produção, pode desativar as regras e ativá-las durante horas específicas quando as atualizações são permitidas. Se tiver um grupo de desenvolvimento ou de gestão de QA no seu ambiente e este tiver conetividade à Internet, poderá configurar esse grupo de gestão com uma área de trabalho do Log Analytics para suportar este cenário. Isto permite-lhe rever e avaliar as versões iterativas dos pacotes de gestão do Azure Monitor antes de liberá-los para o grupo de gestão de produção.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Mudar de um grupo do Operations Manager para uma nova Área de Trabalho do Log Analytics
1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
1. No portal do Azure, clique em **Mais serviços**, que se encontra no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Log Analytics** e, em seguida, crie uma área de trabalho.  
1. Abra a consola do Operations Manager com uma conta que seja um membro da função Administradores do Operations Manager e selecione a área de trabalho **Administração**.
1. Expanda o Log Analytics e selecione **ligações**.
1. Selecione a ligação **Reconfigurar o Operation Management Suite** no meio do painel.
1. Siga os **Assistente de ativação do Log Analytics** e introduza o e-mail endereço ou número de telefone e a palavra-passe da conta de administrador que está associada a sua nova área de trabalho do Log Analytics.
   
   > [!NOTE]
   > O **Assistente de integração do Operations Management Suite: Selecione a área de trabalho** página apresenta a área de trabalho existente que está a ser utilizado.
   > 
   > 

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Validar a integração do Operations Manager com o Azure Monitor
Existem algumas formas diferentes, pode verificar que o Azure Monitor para integração do Operations Manager foi concluída com êxito.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Para confirmar a integração no portal do Azure
1. No portal do Azure, clique em **Mais serviços**, que se encontra no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada.
1. Na lista de áreas de trabalho do Log Analytics, selecione a área de trabalho aplicável.  
1. Selecione **Definições avançadas**, **Origens Ligadas** e, em seguida, selecione **System Center**. 
1. Na tabela na secção System Center Operations Manager, deve ver o nome do grupo de gestão listado com o número de agentes e o estado quando os dados foram recebidos pela última vez.
   
   ![oms-settings-connectedsources](./media/om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>Para confirmar a integração na consola de Operações
1. Abra a consola do Operations Manager e selecione a área de trabalho de **Administração**.
1. Selecione **Pacotes de Gestão** e, na caixa de texto **Procurar:**, digite **Assistente** ou **Inteligência**.
1. Consoante as soluções que tiver ativado, pode ver um pacote de gestão correspondente listado nos resultados da pesquisa.  Por exemplo, se tiver ativado a solução Gestão de Alertas, o pacote de gestão Microsoft System Center Advisor Alert Management será apresentado na lista.
1. Na vista **Monitorização**, navegue até à vista **Operations Management Suite\Estado de Funcionamento**.  Selecione um servidor de Gestão no painel **Estado do Servidor de Gestão** e, no painel **Vista de Detalhes**, confirme se o valor da propriedade **URI do serviço de Autenticação** corresponde ao ID da Área de Trabalho do Log Analytics.
   
   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-azure-monitor"></a>Remover a integração com o Azure Monitor
Quando já não necessitar da integração entre o grupo de gestão do Operations Manager e a área de trabalho do Log Analytics, existem vários passos necessários para remover corretamente a ligação e a configuração no grupo de gestão. O procedimento seguinte tem de atualizar a sua área de trabalho do Log Analytics, eliminando a referência do seu grupo de gestão, eliminar os conectores do Azure Monitor e, em seguida, eliminar pacotes de gestão suportar integração com o serviço.  

Pacotes de gestão para as soluções tiver ativado o que se integram com o Operations Manager e os pacotes de gestão necessários para suportar a integração com o Azure Monitor não podem ser facilmente eliminados do grupo de gestão. Isto acontece porque alguns dos pacotes de gestão do Azure Monitor têm dependências de outros pacotes de gerenciamento relacionados. Para eliminar pacotes de gestão com dependências noutros pacotes de gestão, transfira o script [remover um pacote de gestão com dependências](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) do Centro de Scripts do TechNet.  

1. Abra a Shell de Comandos do Operations Manager com uma conta que seja um membro da função Administradores do Operations Manager.
   
    > [!WARNING]
    > Verifique que não tem quaisquer pacotes de gestão personalizados com a palavra Assistente ou PacoteInteligente no nome antes de continuar. Caso contrário, os seguintes passos vão eliminá-los do grupo de gestão.
    > 

1. Na shell de comandos, escreva `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. A seguir, escreva `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Para remover quaisquer pacotes de gestão restantes que tenham dependências noutros pacotes de gestão do System Center Advisor, utilize o script *RecursiveRemove.ps1* que transferiu do Centro de Scripts da TechNet anteriormente.  
 
    > [!NOTE]
    > O passo para remover os pacotes de gestão do Advisor com o PowerShell não irá eliminar automaticamente os pacotes de gestão do Microsoft System Center Advisor ou o Microsoft System Center Advisor interno.  Não tente eliminá-los.  
    >  

1. Abra a consola de Operações do Operations Manager com uma conta que seja um membro da função Administradores do Operations Manager.
1. Em **Administração**, selecione o nó **Pacotes de Gestão** e, na caixa **Procurar:**, escreva **Assistente** e verifique se os seguintes pacotes de gestão ainda estão a ser importados no grupo de gestão:
   
   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Interno

1. No portal do Azure, clique nas **definições** mosaico.
1. Selecione **ligado a origens**.
1. Na tabela na secção System Center Operations Manager, deverá ver o nome do grupo de gestão que pretende remover a área de trabalho. Sob a coluna **Últimos Dados**, clique em **Remover**.  
   
    > [!NOTE]
    > A ligação **Remover** só estará disponível após 14 dias se não existir nenhuma atividade detetada no grupo de gestão ligado.  
    > 

1. Será apresentada uma janela a pedir-lhe para confirmar que pretende prosseguir com a remoção.  Clique em **Sim** para continuar. 

Para eliminar os dois conectores – Microsoft.SystemCenter.Advisor.DataConnector e Advisor Connector, guarde o script do PowerShell abaixo no computador e execute-o com os exemplos seguintes:

```
    .\OM2012_DeleteConnectors.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SystemCenter.Advisor.DataConnector” <ManagementServerName>
```

> [!NOTE]
> O computador no qual executa este script, se não for um servidor de gestão, deve ter uma shell de comandos do Operations Manager instalada, consoante a versão do grupo de gestão.
> 
> 

```
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

No futuro se pretender ligar novamente o seu grupo de gestão para uma área de trabalho do Log Analytics, tem de voltar a importar o `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` ficheiro do pacote de gestão. Consoante a versão do System Center Operations Manager implementada no seu ambiente, pode encontrar este ficheiro na seguinte localização:

* No suporte de dados de origem na pasta `\ManagementPacks` do System Center 2016 – Operations Manager e superior.
* No rollup de atualizações mais recentes aplicado ao grupo de gestão. Para o Operations Manager 2012, a pasta de origem é ` %ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` e, para o 2012 R2, está localizada em `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`.

## <a name="next-steps"></a>Passos Seguintes
Para adicionar funcionalidade e recolher dados, veja [soluções de adicionar o Azure Monitor da Galeria de soluções](../../azure-monitor/insights/solutions.md).


