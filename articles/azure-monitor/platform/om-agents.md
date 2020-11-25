---
title: Connect Operations Manager ao Azure Monitor / Microsoft Docs
description: Para manter o seu investimento existente no System Center Operations Manager e utilizar as capacidades expandidas com o Log Analytics, pode integrar o Operations Manager com a área de trabalho.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/24/2020
ms.openlocfilehash: 2a4f24da51b9e9e78c3df3e7d1437a380306e300
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95975582"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Connect Operations Manager ao Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Para manter o investimento existente no Gestor de [Operações do System Center](/system-center/scom/key-concepts?view=sc-om-1807) e utilizar capacidades estendidas com o Azure Monitor, pode integrar o Gestor de Operações com o seu espaço de trabalho Log Analytics. Isto permite-lhe aproveitar as oportunidades de registos no Azure Monitor, enquanto continua a utilizar o Gestor de Operações para:

* Monitorizar o estado de funcionamento dos serviços de TI com o Operations Manager
* Manter a integração com as soluções ITSM que suportam a gestão de incidentes e problemas
* Gerir o ciclo de vida dos agentes implementados nas máquinas virtuais IaaS no local e na cloud pública que o utilizador monitoriza com o Operations Manager

A integração com o Gestor de Operações do System Center acrescenta valor à sua estratégia de operações de serviço utilizando a rapidez e eficiência do Azure Monitor na recolha, armazenamento e análise de dados de registo do Gestor de Operações. As consultas de registo do Azure Monitor ajudam a correlacionar e a trabalhar na identificação das falhas de problemas e na resolução de recorrências em apoio ao seu processo de gestão de problemas existente. A flexibilidade do motor de consulta para examinar dados de desempenho, evento e alerta, com painéis de instrumentos ricos e capacidades de reporte para expor estes dados de forma significativa, demonstra a força que o Azure Monitor traz no complemento gestor de operações.

Os agentes que reportam ao grupo de gestão do Gestor de Operações recolhem dados dos seus servidores com base nas fontes de [dados](agent-data-sources.md) do Log Analytics e nas soluções que ativou no seu espaço de trabalho. Dependendo das soluções ativadas, os seus dados são enviados diretamente de um servidor de gestão de Gestor de Operações para o serviço, ou devido ao volume de dados recolhidos no sistema gerido pelo agente, são enviados diretamente do agente para um espaço de trabalho do Log Analytics. O servidor de gestão reencaminha os dados diretamente para o serviço; estes nunca são escritos na base de dados operacional ou do armazém de dados. Quando um servidor de gestão perde a conectividade com o Azure Monitor, cache os dados localmente até que a comunicação seja restabelecida. Se o servidor de gestão estiver offline devido à manutenção planeada ou a uma paragem não planeada, outro servidor de gestão do grupo de gestão retoma a conectividade com o Azure Monitor.  

O diagrama seguinte mostra a ligação entre os servidores de gestão e os agentes num grupo de gestão de operações do System Center Operations Manager e do Azure Monitor, incluindo a direção e as portas.

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

Se as suas políticas de segurança informática não permitirem que os computadores da sua rede se conectem à Internet, os servidores de gestão podem ser configurados para se ligarem ao gateway do Log Analytics para receber informações de configuração e enviar dados recolhidos dependendo das soluções ativadas. Para obter mais informações e etapas sobre como configurar o seu grupo de gestão de Gestor de Operações para comunicar através de uma porta de entrada do Log Analytics para o Azure Monitor, consulte [os computadores Connect para O Azure Monitor utilizando o gateway Log Analytics](./gateway.md).  

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, reveja os seguintes requisitos.

* O Azure Monitor só suporta o Gestor de Operações do Centro de Sistema 2016 ou posteriormente, Gestor de Operações 2012 SP1 UR6 ou posterior, e Gestor de Operações 2012 R2 UR2 ou mais tarde. Foi adicionado suporte de proxy ao Operations Manager 2012 SP1 UR7 e ao Operations Manager 2012 R2 UR3.
* Integrar o System Center Operations Manager 2016 com a nuvem do Governo dos EUA requer um pacote de gestão de conselheiros atualizado incluído com Update Rollup 2 ou mais tarde. System Center Operations Manager 2012 R2 requer um pacote de gestão de conselheiros atualizado incluído com Update Rollup 3 ou mais tarde.
* Todos os agentes do Operations Manager têm de cumprir os requisitos mínimos de suporte. Certifique-se de que os agentes estão na atualização mínima, caso contrário a comunicação do agente Windows pode falhar e gerar erros no registo de eventos do Gestor de Operações.
* Uma área de trabalho do Log Analytics. Para mais informações, [reveja a visão geral do espaço de trabalho do Log Analytics](design-logs-deployment.md).
* Autentica a Azure com uma conta que é membro da [função Dedudo De Log Analytics](manage-access.md#manage-access-using-azure-permissions).

* Regiões Apoiadas - Apenas as seguintes regiões de Azure são apoiadas pelo Gestor de Operações do Centro de Sistema para se ligarem a um espaço de trabalho log analytics:
    - E.U.A. Centro-Oeste
    - Sudeste da Austrália
    - Europa Ocidental
    - E.U.A. Leste
    - Ásia Sudeste
    - Leste do Japão
    - Sul do Reino Unido
    - Índia Central
    - Canadá Central
    - E.U.A. Oeste 2

>[!NOTE]
>As recentes alterações às APIs do Azure impedirão que os clientes possam configurar com sucesso a integração entre o seu grupo de gestão e o Azure Monitor pela primeira vez. Para clientes que já integraram o seu grupo de gestão com o serviço, não é impactado a menos que precise de reconfigurar a sua ligação existente.  
>Foi lançado um novo pacote de gestão para as seguintes versões do Gestor de Operações:
> - Para o System Center Operations Manager 2019, este pacote de gestão está incluído com os meios de origem e instalado durante a instalação de um novo grupo de gestão ou durante uma atualização.
>- O pacote de gestão Gerente de Operações 1801 também é aplicável ao Gestor de Operações 1807.
>- Para System Center Operations Manager 1801, descarregue o pacote de gestão a partir [daqui.](https://www.microsoft.com/download/details.aspx?id=57173)
>- Para o System Center 2016 - Gestor de Operações, descarregue o pacote de gestão a partir [daqui.](https://www.microsoft.com/download/details.aspx?id=57172)  
>- Para System Center Operations Manager 2012 R2, descarregue o pacote de gestão a partir [daqui.](https://www.microsoft.com/download/details.aspx?id=57171)  


### <a name="network"></a>Rede

As informações abaixo listam as informações de configuração de procuração e firewall necessárias para que o agente do Gestor de Operações, servidores de gestão e consola de operações se comunique com o Azure Monitor. O tráfego de cada componente é de saída da sua rede para o Azure Monitor.

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
|**Consola do Gestor de Operações para o Azure Monitor**|||  
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

Para garantir a segurança dos dados em trânsito para o Azure Monitor, encorajamo-lo a configurar o agente e o grupo de gestão para utilizar pelo menos a Segurança da Camada de Transporte (TLS) 1.2. Versões mais antigas da camada de tomadas TLS/Secure Sockets (SSL) foram consideradas vulneráveis e, embora ainda atualmente trabalhem para permitir retrocompatibilidade, não são **recomendadas**. Para obter informações adicionais, [reveja o envio de dados de forma segura utilizando o TLS 1.2](./data-security.md#sending-data-securely-using-tls-12).

## <a name="connecting-operations-manager-to-azure-monitor"></a>Gestor de Operações de Ligação ao Monitor Azure

Realize esta série de passos para configurar o grupo de gestão do Operations Manager para se ligar a uma das suas áreas de trabalho do Log Analytics.

Durante o registo inicial do seu grupo de gestão de Gestor de Operações com um espaço de trabalho Log Analytics, a opção de especificar a configuração de procuração para o grupo de gestão não está disponível na consola Operações.  O grupo de gestão tem de ser registado com êxito no serviço para esta opção ficar disponível.  Para contornar isto, precisa de atualizar a configuração de procuração do sistema utilizando o Netsh no sistema que executa a consola Operações de integração para configurar a integração, e todos os servidores de gestão no grupo de gestão.  

1. Abra uma linha de comandos elevada.
   a. Ir para **iniciar** e escrever **cmd**.
   b. Clique no **botão direito Pedido** de comando e selecione Executar como administrador**.
1. Introduza o seguinte comando e prima **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Depois de completar os seguintes passos para integrar com o Azure Monitor, pode remover a configuração executando `netsh winhttp reset proxy` e, em seguida, usar a opção **de servidor proxy Configure** na consola Operações para especificar o servidor de gateway proxy ou Log Analytics.

1. Na consola do Operations Manager, selecione a área de trabalho **Administração**.
1. Expanda o nó Operations Management Suite e clique em **Ligação**.
1. Clique na ligação **Registar no Operations Management Suite**.
1. Na página **Assistente de Inclusão do Operations Management Suite: Autenticação**, introduza o endereço de e-mail ou o número de telefone e a palavra-passe da conta de administrador associada à sua subscrição do OMS e clique em **Iniciar sessão**.

   >[!NOTE]
   >O nome da Suite de Gestão de Operações foi retirado.

1. Depois de autenticado com sucesso, no Assistente de Bordo da **Suite de Gestão de Operações: Selecione** a página workspace, é solicitado que selecione o seu inquilino Azure, subscrição e espaço de trabalho Log Analytics. Se tiver mais do que uma área de trabalho, selecione a área de trabalho que pretende registar com o grupo de gestão do Operations Manager na lista pendente e clique em **Seguinte**.

   > [!NOTE]
   > O Operations Manager suporta apenas uma área de trabalho do Log Analytics de cada vez. A ligação e os computadores registados no Azure Monitor com o espaço de trabalho anterior são removidos do Azure Monitor.
   >
   >
1. Na página **Assistente de Inclusão do Operations Management Suite: Resumo**, confirme as definições, verifique se estão corretas e clique em **Criar**.
1. Na página **Assistente de Inclusão do Operations Management Suite: Concluir**, clique em **Fechar**.

### <a name="add-agent-managed-computers"></a>Adicionar computadores geridos por agentes

Depois de configurar a integração com o seu espaço de trabalho Log Analytics, apenas estabelece uma ligação com o serviço, não são recolhidos dados dos agentes que reportam ao seu grupo de gestão. Isto só acontecerá depois de configurar quais os computadores geridos por agentes específicos que recolhem dados de registo para o Azure Monitor. Pode selecionar os objetos de computador individualmente ou pode selecionar um grupo que contenha objetos de computador do Windows. Não pode selecionar um grupo que contenha instâncias de outra classe, tais como discos lógicos ou bases de dados SQL.

1. Abra a consola do Operations Manager e selecione a área de trabalho de **Administração**.
1. Expanda o nó Operations Management Suite e clique em **Ligação**.
1. Clique na ligação **Adicionar um Computador/Grupo** sob o cabeçalho Ações, no lado direito do painel.
1. Na caixa de diálogo **Pesquisa no Computador**, pode procurar os computadores ou os grupos monitorizados pelo Operations Manager. Selecione computadores ou grupos, incluindo o Servidor de Gestão de Gestores de Operações a bordo do Azure Monitor, clique em **Adicionar** e, em seguida, clique em **OK**.

Pode ver os computadores e os grupos configurados para recolher dados a partir do nó Computadores Geridos no Operations Management Suite na área de trabalho **Administração** da Consola de operações. Aqui, pode adicionar ou remover computadores e grupos, conforme necessário.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Configurar as definições do proxy na Consola de operações

Execute os seguintes passos se um servidor de procuração interno estiver entre o grupo de gestão e o Azure Monitor. Estas definições são geridas centralmente a partir do grupo de gestão e distribuídas a sistemas geridos por agentes que estão incluídos no âmbito de recolha de dados de registo para o Azure Monitor.  Tal é benéfico para quando determinadas soluções ignoram o servidor de gestão e enviam dados diretamente para o serviço.

1. Abra a consola do Operations Manager e selecione a área de trabalho de **Administração**.
1. Expanda o Operations Management Suite e clique em **Ligações**.
1. Na vista Ligação OMS, clique em **Configurar Servidor Proxy**.
1. Na página **Assistente do Operations Management Suite: Servidor Proxy**, selecione **Utilizar um servidor proxy para aceder ao Operations Management Suite**, escreva o URL com o número da porta, por exemplo, http://corpproxy:80 e, em seguida, clique em **Concluir**.

Se o seu servidor proxy necessitar de autenticação, execute os seguintes passos para configurar credenciais e configurações que precisam de se propagar para computadores geridos que reportem ao Azure Monitor no grupo de gestão.

1. Abra a consola do Operations Manager e selecione a área de trabalho de **Administração**.
1. Em **Configuração RunAs**, selecione **Perfis**.
1. Abra o perfil **System Center Advisor Run As Profile Proxy**.
1. No Assistente do Perfil Run As, clique em Adicionar para utilizar uma conta Run As. Pode criar uma [conta Run As](/previous-versions/system-center/system-center-2012-R2/hh321655(v=sc.12)) ou utilizar uma existente. Esta conta tem de ter permissões suficientes para passar através do servidor proxy.
1. Para configurar a conta a gerir, escolha **Uma classe, um grupo ou um objeto selecionado**, clique em **Selecionar...** e, em seguida, clique em **Grupo...** para abrir a caixa **Pesquisa de Grupo**.
1. Pesquise e, em seguida, selecione **Microsoft System Center Advisor Monitoring Server Group**. Clique em **OK** depois de selecionar o grupo para fechar a caixa **Pesquisa de Grupo**.
1. Clique **em OK** para fechar a caixa de conta Add a Run **As.**
1. Clique em **Guardar** para concluir o assistente e guardar as alterações.

Após a criação da ligação e configurar quais os agentes que irão recolher e reportar dados de registo ao Azure Monitor, a seguinte configuração é aplicada no grupo de gestão, não necessariamente na ordem:

* É criada a Conta Run As **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate**. A conta é associada ao perfil Run As **Blob do Perfil Run As do Microsoft System Center Advisor** e é direcionada para duas classes – **Servidor de Recolha** e **Grupo de Gestão do Operations Manager**.
* São criados dois conectores.  O primeiro chama-se **Microsoft.SystemCenter.Advisor.DataConnector** e é automaticamente configurado com uma subscrição que encaminha todos os alertas gerados de instâncias de todas as classes do grupo de gestão para o Azure Monitor. O segundo conector é **o Advisor Connector,** que é responsável por comunicar com o Azure Monitor e partilhar dados.
* Os agentes e os grupos que selecionou para recolher dados no grupo de gestão são adicionados ao **Grupo de Servidores de Monitorização do Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Atualizações dos pacotes de gestão

Após a conclusão da configuração, o grupo de gestão do Gestor de Operações estabelece uma ligação com o Azure Monitor. O servidor de gestão é sincronizado com o serviço Web e recebe informações de configuração atualizadas sob a forma de pacotes de gestão para as soluções ativadas que se integram com o Operations Manager. O Gestor de Operações verifica as atualizações destes pacotes de gestão e descarrega-os automaticamente e importa-os quando estão disponíveis. Existem duas regras específicas que controlam este comportamento:

* **Microsoft.SystemCenter.Advisor.MPUpdate** - Atualiza os pacotes de gestão base Azure Monitor. Por predefinição, é executada a cada 12 horas.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** – Atualiza os pacotes de gestão de soluções ativadas na sua área de trabalho. Por predefinição, é executada a cada cinco (5) minutos.

Pode anular estas duas regras para evitar o descarregamento automático desativando-as ou modificar a frequência com que o servidor de gestão sincroniza com o Azure Monitor para determinar se um novo pacote de gestão está disponível e deve ser descarregado. Siga os passos em [Como Substituir uma Regra ou um Monitor](/previous-versions/system-center/system-center-2012-R2/hh212869(v=sc.12)) para modificar o parâmetro **Frequência** por um valor em segundos, para alterar a agenda de sincronização ou modificar o parâmetro **Ativada** para desativar as regras. Escolha as substituições para todos os objetos da classe do Grupo de Gestão do Operations Manager.

Para continuar a seguir o seu processo de controlo de alterações existente para controlar as versões de pacotes de gestão no seu grupo de gestão de produção, pode desativar as regras e capacitá-las durante os momentos específicos em que as atualizações são permitidas. Se tiver um grupo de desenvolvimento ou de gestão de QA no seu ambiente e este tiver conetividade à Internet, poderá configurar esse grupo de gestão com uma área de trabalho do Log Analytics para suportar este cenário. Isto permite-lhe rever e avaliar os lançamentos iterativos dos pacotes de gestão do Azure Monitor antes de os lançar no seu grupo de gestão de produção.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Mudar de um grupo do Operations Manager para uma nova Área de Trabalho do Log Analytics

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
1. No portal do Azure, clique em **Mais serviços**, que se encontra no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Log Analytics** e, em seguida, crie uma área de trabalho.  
1. Abra a consola do Operations Manager com uma conta que seja um membro da função Administradores do Operations Manager e selecione a área de trabalho **Administração**.
1. Expandir o Log Analytics e selecionar **Conexões**.
1. Selecione a ligação **Reconfigurar o Operation Management Suite** no meio do painel.
1. Siga o **Assistente de Bordo** do Log Analytics e introduza o endereço de e-mail ou número de telefone e senha da conta de administrador que está associada ao seu novo espaço de trabalho Log Analytics.

   > [!NOTE]
   > A página **Assistente de Inclusão do Operations Management Suite: Selecionar Área de Trabalho** apresenta a área de trabalho existente que está a ser utilizada.
   >
   >

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Validar a integração do Gestor de Operações com o Azure Monitor

Utilize a seguinte consulta para obter as instâncias ligadas do Gestor de Operações:

```azurepowershell
union *
| where isnotempty(MG)
| where not(ObjectName == 'Advisor Metrics' or ObjectName == 'ManagedSpace')
| summarize LastData = max(TimeGenerated) by lowerCasedComputerName=tolower(Computer), MG, ManagementGroupName
| sort by lowerCasedComputerName asc
```

## <a name="remove-integration-with-azure-monitor"></a>Remover a integração com o Monitor Azure

Quando já não necessitar da integração entre o grupo de gestão do Operations Manager e a área de trabalho do Log Analytics, existem vários passos necessários para remover corretamente a ligação e a configuração no grupo de gestão. O procedimento a seguir fez com que atualizasse o seu espaço de trabalho Log Analytics eliminando a referência do seu grupo de gestão, eliminando os conectores Azure Monitor e, em seguida, eliminando pacotes de gestão que suportam a integração com o serviço.  

Os pacotes de gestão para as soluções que permitiu que se integrem com o Gestor de Operações e os pacotes de gestão necessários para apoiar a integração com o Azure Monitor não podem ser facilmente eliminados do grupo de gestão. Isto porque alguns dos pacotes de gestão do Azure Monitor têm dependências de outros pacotes de gestão relacionados. Para eliminar pacotes de gestão com dependências noutros pacotes de gestão, transfira o script [remover um pacote de gestão com dependências](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) do Centro de Scripts do TechNet.  

1. Abra a Shell de Comandos do Operations Manager com uma conta que seja um membro da função Administradores do Operations Manager.

    > [!WARNING]
    > Verifique que não tem quaisquer pacotes de gestão personalizados com a palavra Assistente ou PacoteInteligente no nome antes de continuar. Caso contrário, os seguintes passos vão eliminá-los do grupo de gestão.
    >

1. Na shell de comandos, escreva `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. A seguir, escreva `Get-SCOMManagementPack -name "*IntelligencePack*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Para remover quaisquer pacotes de gestão restantes que tenham dependências noutros pacotes de gestão do System Center Advisor, utilize o script *RecursiveRemove.ps1* que transferiu do Centro de Scripts da TechNet anteriormente.  

    > [!NOTE]
    > O passo para remover os pacotes de gestão Advisor com PowerShell não eliminará automaticamente os pacotes de gestão interna do Microsoft System Center Advisor ou do Microsoft System Center Advisor Internal.  Não tente eliminá-los.  
    >  

1. Abra a consola de Operações do Operations Manager com uma conta que seja um membro da função Administradores do Operations Manager.
1. Em **Administração**, selecione o nó **Pacotes de Gestão** e, na caixa **Procurar:**, escreva **Assistente** e verifique se os seguintes pacotes de gestão ainda estão a ser importados no grupo de gestão:

   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Interno

1. No portal Azure, clique no azulejo **Definições.**
1. Selecione **Fontes Conectadas**.
1. Na tabela sob a secção Gestor de Operações do Centro de Sistema, deverá ver o nome do grupo de gestão que pretende remover do espaço de trabalho. Sob a coluna **Últimos Dados**, clique em **Remover**.  

    > [!NOTE]
    > A ligação **Remover** só estará disponível após 14 dias se não existir nenhuma atividade detetada no grupo de gestão ligado.  
    >

1. Será apresentada uma janela a pedir-lhe para confirmar que pretende prosseguir com a remoção.  Clique em **Sim** para continuar.

Para eliminar os dois conectores – Microsoft.SystemCenter.Advisor.DataConnector e Advisor Connector, guarde o script do PowerShell abaixo no computador e execute-o com os exemplos seguintes:

```
    .\OM2012_DeleteConnectors.ps1 "Advisor Connector" <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 "Microsoft.SystemCenter.Advisor.DataConnector" <ManagementServerName>
```

> [!NOTE]
> O computador no qual executa este script, se não for um servidor de gestão, deve ter uma shell de comandos do Operations Manager instalada, consoante a versão do grupo de gestão.
>
>

```powershell
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

No futuro, se planeia reconectar o seu grupo de gestão a um espaço de trabalho log Analytics, tem de reexame o ficheiro do pacote de `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` gestão. Consoante a versão do System Center Operations Manager implementada no seu ambiente, pode encontrar este ficheiro na seguinte localização:

* No suporte de dados de origem na pasta `\ManagementPacks` do System Center 2016 – Operations Manager e superior.
* No rollup de atualizações mais recentes aplicado ao grupo de gestão. Para Gestor de Operações 2012, a pasta de origem é `%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` e para 2012 R2, está localizada em `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups` .

## <a name="next-steps"></a>Passos seguintes

Para adicionar funcionalidade e recolher dados, consulte [as soluções Add Azure Monitor da Galeria soluções.](../insights/solutions.md)

