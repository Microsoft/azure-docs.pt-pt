---
title: Conectar Operations Manager ao Azure Monitor | Microsoft Docs
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
ms.date: 08/13/2019
ms.author: magoedte
ms.openlocfilehash: 4b426fbc1d1b3eeed2321f86bb51c9c5d705adb4
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035622"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Conectar Operations Manager ao Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Para manter seu investimento existente em [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807) e usar recursos estendidos com o Azure monitor, você pode integrar Operations Manager ao seu espaço de trabalho do log Analytics. Isso permite que você aproveite as oportunidades de logs em Azure Monitor enquanto continua a usar Operations Manager para:

* Monitorizar o estado de funcionamento dos serviços de TI com o Operations Manager
* Manter a integração com as soluções ITSM que suportam a gestão de incidentes e problemas
* Gerir o ciclo de vida dos agentes implementados nas máquinas virtuais IaaS no local e na cloud pública que o utilizador monitoriza com o Operations Manager

A integração com o System Center Operations Manager agrega valor à sua estratégia de operações de serviço usando a velocidade e a eficiência de Azure Monitor na coleta, no armazenamento e na análise de dados de log de Operations Manager. Azure Monitor consultas de log ajudam a correlacionar e trabalhar para identificar as falhas de problemas e identificando recorrências no suporte do processo de gerenciamento de problemas existente. A flexibilidade do mecanismo de consulta para examinar dados de desempenho, eventos e alertas, com painéis avançados e recursos de relatório para expor esses dados de maneiras significativas, demonstra a força que o Azure Monitor traz para complementar Operations Manager.

Os agentes que se reportam para o grupo de gerenciamento de Operations Manager coletam dados de seus servidores com base nas [fontes log Analytics de dados](agent-data-sources.md) e soluções que você habilitou em seu espaço de trabalho. Dependendo das soluções habilitadas, seus dados são enviados diretamente de um servidor de gerenciamento de Operations Manager para o serviço ou devido ao volume de dados coletados no sistema gerenciado por agente, são enviados diretamente do agente para um espaço de trabalho Log Analytics. O servidor de gestão reencaminha os dados diretamente para o serviço; estes nunca são escritos na base de dados operacional ou do armazém de dados. Quando um servidor de gerenciamento perde a conectividade com Azure Monitor, ele armazena os dados em cache localmente até que a comunicação seja restabelecida. Se o servidor de gerenciamento estiver offline devido à manutenção planejada ou à interrupção não planejada, outro servidor de gerenciamento no grupo de gerenciamento retomará a conectividade com Azure Monitor.  

O diagrama a seguir mostra a conexão entre os servidores de gerenciamento e os agentes em um System Center Operations Manager grupo de gerenciamento e Azure Monitor, incluindo a direção e as portas.   

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

Se suas políticas de segurança de ti não permitirem que computadores em sua rede se conectem à Internet, os servidores de gerenciamento poderão ser configurados para se conectarem ao gateway de Log Analytics para receber informações de configuração e enviar os dados coletados dependendo das soluções habilitado. Para obter mais informações e etapas sobre como configurar seu grupo de gerenciamento de Operations Manager para se comunicar por meio de um gateway de Log Analytics para Azure Monitor, consulte [conectar computadores ao Azure monitor usando o gateway log Analytics](../../azure-monitor/platform/gateway.md).  

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar, examine os requisitos a seguir.

* Azure Monitor só dá suporte a System Center Operations Manager 2016 ou posterior, Operations Manager 2012 SP1 UR6 ou posterior e Operations Manager 2012 R2 UR2 ou posterior. Foi adicionado suporte de proxy ao Operations Manager 2012 SP1 UR7 e ao Operations Manager 2012 R2 UR3.
* A integração do System Center Operations Manager 2016 com a nuvem do governo dos EUA requer um pacote de gerenciamento do Advisor atualizado incluído no pacote cumulativo de atualizações 2 ou posterior. O System Center Operations Manager 2012 R2 requer um pacote de gerenciamento do Advisor atualizado incluído com o pacote cumulativo de atualizações 3 ou posterior.
* Todos os agentes do Operations Manager têm de cumprir os requisitos mínimos de suporte. Verifique se os agentes estão na atualização mínima, caso contrário, a comunicação do agente do Windows pode falhar e gerar erros no log de eventos do Operations Manager.
* Uma área de trabalho do Log Analytics. Para obter mais informações, examine [log Analytics visão geral do espaço de trabalho](design-logs-deployment.md). 
* Você se autentica no Azure com uma conta que seja membro da [função colaborador de log Analytics](manage-access.md#manage-access-using-azure-permissions).

* Regiões com suporte – somente as seguintes regiões do Azure têm suporte pelo System Center Operations Manager para se conectar a um espaço de trabalho do Log Analytics:
    - EUA Centro-Oeste
    - Sudeste da Austrália
    - Europa Ocidental
    - East US
    - Sudeste Asiático
    - Leste do Japão
    - Reino Unido Sul
    - Índia Central
    - Canadá Central
    - EUA Oeste 2

>[!NOTE]
>As alterações recentes nas APIs do Azure impedirão que os clientes consigam configurar a integração com êxito entre o grupo de gerenciamento e Azure Monitor pela primeira vez. Para os clientes que já tiverem integrado o grupo de gerenciamento com o serviço, você não será afetado, a menos que precise reconfigurar a conexão existente.  
>Um novo pacote de gerenciamento foi lançado para as seguintes versões do Operations Manager:
> - Para System Center Operations Manager 2019, o pacote de gerenciamento é fornecido com o Operations Manager Build.
>- Operations Manager pacote de gerenciamento 1801 também é aplicável para Operations Manager 1807.
>- Para System Center Operations Manager 1801, baixe o pacote de gerenciamento [aqui](https://www.microsoft.com/download/details.aspx?id=57173).
>- Para o System Center 2016-Operations Manager, baixe o pacote de gerenciamento [aqui](https://www.microsoft.com/download/details.aspx?id=57172).  
>- Para System Center Operations Manager 2012 R2, baixe o pacote de gerenciamento [aqui](https://www.microsoft.com/download/details.aspx?id=57171).  


### <a name="network"></a>Rede

As informações abaixo listam as informações de configuração de proxy e firewall necessárias para que o agente de Operations Manager, os servidores de gerenciamento e o console de operações se comuniquem com Azure Monitor. O tráfego de cada componente é de saída de sua rede para Azure Monitor.   

|Resource | Número da porta| Inspeção de HTTP Direto|  
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
|**Operations Manager Console para Azure Monitor**|||  
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

Para garantir a segurança dos dados em trânsito para Azure Monitor, é altamente recomendável que você configure o agente e o grupo de gerenciamento para usar pelo menos o protocolo TLS (segurança de camada de transporte) 1,2. As versões mais antigas do TLS/Secure Sockets Layer (SSL) foram encontradas vulneráveis e enquanto trabalham ainda atualmente para permitir a compatibilidade com versões anteriores, estão **não recomendada**. Para obter mais informações, consulte [enviar dados de forma segura através de TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12).

## <a name="connecting-operations-manager-to-azure-monitor"></a>Conectando Operations Manager ao Azure Monitor

Realize esta série de passos para configurar o grupo de gestão do Operations Manager para se ligar a uma das suas áreas de trabalho do Log Analytics.

Durante o registro inicial de seu grupo de gerenciamento de Operations Manager com um espaço de trabalho Log Analytics, a opção de especificar a configuração de proxy para o grupo de gerenciamento não está disponível no console de operações.  O grupo de gestão tem de ser registado com êxito no serviço para esta opção ficar disponível.  Para contornar isso, você precisa atualizar a configuração de proxy do sistema usando o netsh no sistema que executa o console de operações do para configurar a integração e todos os servidores de gerenciamento no grupo de gerenciamento.  

1. Abra uma linha de comandos elevada.
   a. Aceda a **começar** e escreva **cmd**.
   b. Com o botão direito **linha de comandos** e selecionar executar como administrador * *.
1. Introduza o seguinte comando e prima **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Depois de concluir as etapas a seguir para integrar com o Azure monitor, você pode remover a `netsh winhttp reset proxy` configuração executando o e, em seguida, usar a opção **Configurar servidor proxy** no console de operações para especificar o proxy ou o servidor de gateway de log Analytics .

1. Na consola do Operations Manager, selecione a área de trabalho **Administração**.
1. Expanda o nó Operations Management Suite e clique em **Ligação**.
1. Clique na ligação **Registar no Operations Management Suite**.
1. No assistente **de integração do Operations Management Suite: Página** de autenticação, insira o endereço de email ou o número de telefone e a senha da conta de administrador que está associada à sua assinatura do OMS e clique em **entrar**.

   >[!NOTE]
   >O nome do Operations Management Suite foi desativado.

1. Depois de ser autenticado com êxito, no assistente **de integração do Operations Management Suite: Selecione a** página do espaço de trabalho, você será solicitado a selecionar seu locatário do Azure, sua assinatura e o espaço de trabalho log Analytics. Se tiver mais do que uma área de trabalho, selecione a área de trabalho que pretende registar com o grupo de gestão do Operations Manager na lista pendente e clique em **Seguinte**.

   > [!NOTE]
   > O Operations Manager suporta apenas uma área de trabalho do Log Analytics de cada vez. A conexão e os computadores que foram registrados para Azure Monitor com o espaço de trabalho anterior são removidos do Azure Monitor.
   >
   >
1. No assistente **de integração do Operations Management Suite: Página** de resumo, confirme suas configurações e, se estiverem corretas, clique em **criar**.
1. No assistente **de integração do Operations Management Suite: Concluir** , clique em **Fechar**.

### <a name="add-agent-managed-computers"></a>Adicionar computadores geridos por agentes

Depois de configurar a integração com seu espaço de trabalho do Log Analytics, ele estabelece apenas uma conexão com o serviço, nenhum dado é coletado dos agentes subordinados ao seu grupo de gerenciamento. Isso só acontecerá depois que você configurar quais computadores gerenciados por agente específicos coletam dados de log para Azure Monitor. Pode selecionar os objetos de computador individualmente ou pode selecionar um grupo que contenha objetos de computador do Windows. Não pode selecionar um grupo que contenha instâncias de outra classe, tais como discos lógicos ou bases de dados SQL.

1. Abra a consola do Operations Manager e selecione a área de trabalho de **Administração**.
1. Expanda o nó Operations Management Suite e clique em **Ligação**.
1. Clique na ligação **Adicionar um Computador/Grupo** sob o cabeçalho Ações, no lado direito do painel.
1. Na caixa de diálogo **Pesquisa no Computador**, pode procurar os computadores ou os grupos monitorizados pelo Operations Manager. Selecione computadores ou grupos, incluindo o servidor de gerenciamento de Operations Manager para carregar Azure Monitor, clique em **Adicionar**e, em seguida, clique em **OK**.

Pode ver os computadores e os grupos configurados para recolher dados a partir do nó Computadores Geridos no Operations Management Suite na área de trabalho **Administração** da Consola de operações. Aqui, pode adicionar ou remover computadores e grupos, conforme necessário.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Configurar as definições do proxy na Consola de operações

Execute as etapas a seguir se um servidor proxy interno estiver entre o grupo de gerenciamento e Azure Monitor. Essas configurações são gerenciadas centralmente do grupo de gerenciamento e distribuídas para sistemas gerenciados por agente que estão incluídos no escopo para coletar dados de log para Azure Monitor.  Tal é benéfico para quando determinadas soluções ignoram o servidor de gestão e enviam dados diretamente para o serviço.

1. Abra a consola do Operations Manager e selecione a área de trabalho de **Administração**.
1. Expanda o Operations Management Suite e clique em **Ligações**.
1. Na vista Ligação OMS, clique em **Configurar Servidor Proxy**.
1. No **assistente do Operations Management Suite: Servidor** proxy, selecione **usar um servidor proxy para acessar o Operations Management Suite**e digite a URL com o número da porta, por exemplo, http://corpproxy:80 e clique em **concluir**.

Se o servidor proxy exigir autenticação, execute as etapas a seguir para configurar as credenciais e as configurações que precisam ser propagadas para os computadores gerenciados que os relatórios Azure Monitor no grupo de gerenciamento.

1. Abra a consola do Operations Manager e selecione a área de trabalho de **Administração**.
1. Em **Configuração RunAs**, selecione **Perfis**.
1. Abra o perfil **System Center Advisor Run As Profile Proxy**.
1. No Assistente do Perfil Run As, clique em Adicionar para utilizar uma conta Run As. Pode criar uma [conta Run As](https://technet.microsoft.com/library/hh321655.aspx) ou utilizar uma existente. Esta conta tem de ter permissões suficientes para passar através do servidor proxy.
1. Para configurar a conta a gerir, escolha **Uma classe, um grupo ou um objeto selecionado**, clique em **Selecionar...** e, em seguida, clique em **Grupo...** para abrir a caixa **Pesquisa de Grupo**.
1. Pesquise e, em seguida, selecione **Microsoft System Center Advisor Monitoring Server Group**. Clique em **OK** depois de selecionar o grupo para fechar a caixa **Pesquisa de Grupo**.
1. Clique em **OK** para fechar a caixa **Adicionar uma conta Run As**.
1. Clique em **Guardar** para concluir o assistente e guardar as alterações.

Depois que a conexão é criada e você configura quais agentes coletarão e relatarão dados de log para Azure Monitor, a configuração a seguir será aplicada no grupo de gerenciamento, não necessariamente na ordem:

* É criada a Conta Run As **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate**. A conta é associada ao perfil Run As **Blob do Perfil Run As do Microsoft System Center Advisor** e é direcionada para duas classes – **Servidor de Recolha** e **Grupo de Gestão do Operations Manager**.
* São criados dois conectores.  O primeiro é chamado de **Microsoft. SystemCenter. Advisor.** dataconnecter e é configurado automaticamente com uma assinatura que encaminha todos os alertas gerados de instâncias de todas as classes no grupo de gerenciamento para Azure monitor. O segundo conector é o **Advisor Connector**, que é responsável por se comunicar com Azure monitor e compartilhar dados.
* Os agentes e os grupos que selecionou para recolher dados no grupo de gestão são adicionados ao **Grupo de Servidores de Monitorização do Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Atualizações dos pacotes de gestão

Após a conclusão da configuração, o grupo de gerenciamento Operations Manager estabelece uma conexão com Azure Monitor. O servidor de gestão é sincronizado com o serviço Web e recebe informações de configuração atualizadas sob a forma de pacotes de gestão para as soluções ativadas que se integram com o Operations Manager. Operations Manager verifica se há atualizações desses pacotes de gerenciamento e os importa automaticamente quando estão disponíveis. Existem duas regras específicas que controlam este comportamento:

* **Microsoft. SystemCenter. Advisor. MPUpdate** -atualiza os pacotes de gerenciamento de Azure monitor base. Por predefinição, é executada a cada 12 horas.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** – Atualiza os pacotes de gestão de soluções ativadas na sua área de trabalho. Por predefinição, é executada a cada cinco (5) minutos.

Você pode substituir essas duas regras para impedir o download automático desabilitando-os ou modificar a frequência com que o servidor de gerenciamento é sincronizado com Azure Monitor para determinar se um novo pacote de gerenciamento está disponível e deve ser baixado. Siga os passos em [Como Substituir uma Regra ou um Monitor](https://technet.microsoft.com/library/hh212869.aspx) para modificar o parâmetro **Frequência** por um valor em segundos, para alterar a agenda de sincronização ou modificar o parâmetro **Ativada** para desativar as regras. Escolha as substituições para todos os objetos da classe do Grupo de Gestão do Operations Manager.

Para continuar seguindo o processo de controle de alterações existente para controlar as versões do pacote de gerenciamento em seu grupo de gerenciamento de produção, você pode desabilitar as regras e habilitá-las durante horários específicos quando as atualizações forem permitidas. Se tiver um grupo de desenvolvimento ou de gestão de QA no seu ambiente e este tiver conetividade à Internet, poderá configurar esse grupo de gestão com uma área de trabalho do Log Analytics para suportar este cenário. Isso permite que você examine e avalie as versões iterativas dos pacotes de gerenciamento do Azure Monitor antes de liberá-los para o grupo de gerenciamento de produção.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Mudar de um grupo do Operations Manager para uma nova Área de Trabalho do Log Analytics

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
1. No portal do Azure, clique em **Mais serviços**, que se encontra no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Log Analytics** e, em seguida, crie uma área de trabalho.  
1. Abra a consola do Operations Manager com uma conta que seja um membro da função Administradores do Operations Manager e selecione a área de trabalho **Administração**.
1. Expanda Log Analytics e selecione **conexões**.
1. Selecione a ligação **Reconfigurar o Operation Management Suite** no meio do painel.
1. Siga o **Assistente de integração de log Analytics** e insira o endereço de email ou o número de telefone e a senha da conta de administrador que está associada ao seu novo espaço de trabalho do log Analytics.

   > [!NOTE]
   > O **assistente de integração do Operations Management Suite: A página** selecionar espaço de trabalho apresenta o espaço de trabalho existente que está em uso.
   >
   >

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Validar a integração de Operations Manager com o Azure Monitor

Há algumas maneiras diferentes de verificar se Azure Monitor Operations Manager integração foi bem-sucedida.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Para confirmar a integração no portal do Azure

1. No portal do Azure, clique em **Mais serviços**, que se encontra no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada.
1. Na lista de áreas de trabalho do Log Analytics, selecione a área de trabalho aplicável.  
1. Selecione **Definições avançadas**, **Origens Ligadas** e, em seguida, selecione **System Center**.
1. Na tabela na secção System Center Operations Manager, deve ver o nome do grupo de gestão listado com o número de agentes e o estado quando os dados foram recebidos pela última vez.

   ![oms-settings-connectedsources](./media/om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>Para confirmar a integração na consola de Operações

1. Abra a consola do Operations Manager e selecione a área de trabalho de **Administração**.
1. Selecione **Pacotes de Gestão** e, na caixa de texto **Procurar:** , digite **Assistente** ou **Inteligência**.
1. Consoante as soluções que tiver ativado, pode ver um pacote de gestão correspondente listado nos resultados da pesquisa.  Por exemplo, se tiver ativado a solução Gestão de Alertas, o pacote de gestão Microsoft System Center Advisor Alert Management será apresentado na lista.
1. Na vista **Monitorização**, navegue até à vista **Operations Management Suite\Estado de Funcionamento**.  Selecione um servidor de Gestão no painel **Estado do Servidor de Gestão** e, no painel **Vista de Detalhes**, confirme se o valor da propriedade **URI do serviço de Autenticação** corresponde ao ID da Área de Trabalho do Log Analytics.

   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-azure-monitor"></a>Remover a integração com o Azure Monitor

Quando já não necessitar da integração entre o grupo de gestão do Operations Manager e a área de trabalho do Log Analytics, existem vários passos necessários para remover corretamente a ligação e a configuração no grupo de gestão. O procedimento a seguir atualiza seu espaço de trabalho Log Analytics excluindo a referência do seu grupo de gerenciamento, exclui os conectores de Azure Monitor e, em seguida, exclui os pacotes de gerenciamento que dão suporte à integração com o serviço.  

Pacotes de gerenciamento para as soluções que você habilitou que se integram com Operations Manager e os pacotes de gerenciamento necessários para dar suporte à integração com Azure Monitor não podem ser facilmente excluídos do grupo de gerenciamento. Isso ocorre porque alguns dos pacotes de gerenciamento Azure Monitor têm dependências em outros pacotes de gerenciamento relacionados. Para eliminar pacotes de gestão com dependências noutros pacotes de gestão, transfira o script [remover um pacote de gestão com dependências](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) do Centro de Scripts do TechNet.  

1. Abra a Shell de Comandos do Operations Manager com uma conta que seja um membro da função Administradores do Operations Manager.

    > [!WARNING]
    > Verifique que não tem quaisquer pacotes de gestão personalizados com a palavra Assistente ou PacoteInteligente no nome antes de continuar. Caso contrário, os seguintes passos vão eliminá-los do grupo de gestão.
    >

1. Na shell de comandos, escreva `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. A seguir, escreva `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Para remover quaisquer pacotes de gestão restantes que tenham dependências noutros pacotes de gestão do System Center Advisor, utilize o script *RecursiveRemove.ps1* que transferiu do Centro de Scripts da TechNet anteriormente.  

    > [!NOTE]
    > A etapa para remover os pacotes de gerenciamento do Advisor com o PowerShell não excluirá automaticamente os pacotes de gerenciamento internos do Microsoft System Center Advisor ou do Microsoft System Center Advisor.  Não tente excluí-los.  
    >  

1. Abra a consola de Operações do Operations Manager com uma conta que seja um membro da função Administradores do Operations Manager.
1. Em **Administração**, selecione o nó **Pacotes de Gestão** e, na caixa **Procurar:** , escreva **Assistente** e verifique se os seguintes pacotes de gestão ainda estão a ser importados no grupo de gestão:

   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Interno

1. No portal do Azure, clique no bloco **configurações** .
1. Selecione **fontes conectadas**.
1. Na tabela na seção System Center Operations Manager, você deve ver o nome do grupo de gerenciamento que deseja remover do espaço de trabalho. Sob a coluna **Últimos Dados**, clique em **Remover**.  

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

No futuro, se você planeja reconectar seu grupo de gerenciamento a um espaço de trabalho log Analytics, você precisará importar novamente o `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` arquivo do pacote de gerenciamento. Consoante a versão do System Center Operations Manager implementada no seu ambiente, pode encontrar este ficheiro na seguinte localização:

* No suporte de dados de origem na pasta `\ManagementPacks` do System Center 2016 – Operations Manager e superior.
* No rollup de atualizações mais recentes aplicado ao grupo de gestão. Para Operations Manager 2012, a pasta de origem `%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` é e para 2012 R2, ela está localizada `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`em.

## <a name="next-steps"></a>Passos Seguintes

Para adicionar funcionalidade e coletar dados, consulte [Adicionar soluções de Azure monitor do Galeria de soluções](../../azure-monitor/insights/solutions.md).
