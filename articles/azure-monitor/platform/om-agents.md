---
title: Connect Operations Manager to Azure Monitor [ Monitor De Ligação] Microsoft Docs
description: Para manter o seu investimento existente no System Center Operations Manager e utilizar as capacidades expandidas com o Log Analytics, pode integrar o Operations Manager com a área de trabalho.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 92b6737f48d8d8704f461c9adac92284b323b05f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373263"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Conectar Gerente de Operações ao Monitor Azure

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Para manter o seu investimento existente no [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807) e utilizar capacidades alargadas com o Monitor Azure, pode integrar o Gestor de Operações com o seu espaço de trabalho Log Analytics. Isto permite aproveitar as oportunidades de login no Monitor Azure, continuando a utilizar o Gestor de Operações para:

* Monitorizar o estado de funcionamento dos serviços de TI com o Operations Manager
* Manter a integração com as soluções ITSM que suportam a gestão de incidentes e problemas
* Gerir o ciclo de vida dos agentes implementados nas máquinas virtuais IaaS no local e na cloud pública que o utilizador monitoriza com o Operations Manager

Integrar-se com o System Center Operations Manager acrescenta valor à sua estratégia de operações de serviço, utilizando a rapidez e eficiência do Monitor Azure na recolha, armazenamento e análise de dados de registo do Gestor de Operações. As consultas de registo do Azure Monitor ajudam a correlacionar e a trabalhar no sentido de identificar as falhas dos problemas e de surgir recorrências em apoio ao seu processo de gestão de problemas existente. A flexibilidade do motor de consulta para examinar dados de desempenho, evento e alerta, com painéis de instrumentos ricos e capacidades de reporte para expor estes dados de forma significativa, demonstra a força que o Azure Monitor traz ao Complementar Diretor de Operações.

Os agentes que reportam ao grupo de gestão do Gestor de Operações recolhem dados dos seus servidores com base nas [fontes](agent-data-sources.md) de dados do Log Analytics e nas soluções que permitiu no seu espaço de trabalho. Dependendo das soluções ativadas, os seus dados são enviados diretamente de um servidor de gestão do Gestor de Operações para o serviço, ou devido ao volume de dados recolhidos no sistema gerido pelo agente, são enviados diretamente do agente para um espaço de trabalho de Log Analytics. O servidor de gestão reencaminha os dados diretamente para o serviço; estes nunca são escritos na base de dados operacional ou do armazém de dados. Quando um servidor de gestão perde conectividade com o Monitor Azure, ele cacheos os dados localmente até que a comunicação seja restabelecida. Se o servidor de gestão estiver offline devido à manutenção planeada ou à paragem não planeada, outro servidor de gestão do grupo de gestão retoma a conectividade com o Monitor Azure.  

O diagrama seguinte mostra a ligação entre os servidores de gestão e os agentes de um grupo de gestão de gestão de operações do System Center e do Monitor Azure, incluindo a direção e as portas.

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

Se as suas políticas de segurança de TI não permitirem que os computadores da sua rede se conectem à Internet, os servidores de gestão podem ser configurados para se ligarem ao portal Log Analytics para receber informações de configuração e enviar dados recolhidos dependendo das soluções habilitado. Para obter mais informações e passos sobre como configurar o seu grupo de gestão de Gestor de Operações para comunicar através de um portal de Log Analytics para o Monitor Azure, consulte [connect computers to Azure Monitor utilizando o gateway Log Analytics](../../azure-monitor/platform/gateway.md).  

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, reveja os seguintes requisitos.

* O Azure Monitor apenas suporta o System Center Operations Manager 2016 ou mais tarde, O Gestor de Operações 2012 SP1 UR6 ou posteriormente, e O Gestor de Operações 2012 R2 UR2 ou mais tarde. Foi adicionado suporte de proxy ao Operations Manager 2012 SP1 UR7 e ao Operations Manager 2012 R2 UR3.
* Integrar o System Center Operations Manager 2016 com a nuvem do Governo dos EUA requer um pacote de gestão de Advisor atualizado incluído com Update Rollup 2 ou mais tarde. System Center Operations Manager 2012 R2 requer um pacote de gestão advisor atualizado incluído com Rollup atualizado 3 ou posterior.
* Todos os agentes do Operations Manager têm de cumprir os requisitos mínimos de suporte. Certifique-se de que os agentes estão na atualização mínima, caso contrário a comunicação do agente Windows pode falhar e gerar erros no registo de eventos do Gestor de Operações.
* Uma área de trabalho do Log Analytics. Para mais informações, reveja a visão geral do espaço de [trabalho do Log Analytics](design-logs-deployment.md). 
* Autentica-se a Azure com uma conta que é membro do papel de Colaborador de [Log Analytics.](manage-access.md#manage-access-using-azure-permissions)

* Regiões Apoiadas - Apenas as seguintes regiões azure são apoiadas pelo System Center Operations Manager para se conectarem a um espaço de trabalho log Analytics:
    - E.U.A. Centro-Oeste
    - Sudeste da Austrália
    - Europa Ocidental
    - E.U.A. Leste
    - Ásia Sudeste
    - Leste do Japão
    - Sul do Reino Unido
    - Índia Central
    - Canadá Central
    - E.U.A.Oeste 2

>[!NOTE]
>As recentes alterações às APIs do Azure impedirão que os clientes consigam configurar com sucesso a integração entre o seu grupo de gestão e o Monitor Azure pela primeira vez. Para os clientes que já integraram o seu grupo de gestão com o serviço, não é afetado a menos que precise reconfigurar a sua ligação existente.  
>Foi lançado um novo pacote de gestão para as seguintes versões do Diretor de Operações:
> - Para o System Center Operations Manager 2019, este pacote de gestão está incluído com os meios de origem e instalado durante a criação de um novo grupo de gestão ou durante uma atualização.
>- O pacote de gestão 1801 do Gestor de Operações também é aplicável ao Gestor de Operações 1807.
>- Para System Center Operations Manager 1801, baixe o pacote de gestão a partir [daqui.](https://www.microsoft.com/download/details.aspx?id=57173)
>- Para system center 2016 - Gestor de Operações, baixe o pacote de gestão a partir [daqui.](https://www.microsoft.com/download/details.aspx?id=57172)  
>- Para System Center Operations Manager 2012 R2, baixe o pacote de gestão a partir [daqui.](https://www.microsoft.com/download/details.aspx?id=57171)  


### <a name="network"></a>Rede

As informações abaixo listam as informações de proxy e configuração de firewall necessárias para que o agente do Gestor de Operações, servidores de gestão e consola de operações se comuniquem com o Monitor Azure. O tráfego de cada componente está a sair da sua rede para o Monitor Azure.

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
|**Consola de Gestor de Operações para O Monitor Azure**|||  
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

Para garantir a segurança dos dados em trânsito para o Monitor Azure, encorajamo-lo vivamente a configurar o agente e o grupo de gestão para utilizar pelo menos a Segurança da Camada de Transporte (TLS) 1.2. As versões mais antigas da Camada de Tomadas TLS/Secure (SSL) revelaram-se vulneráveis e, embora ainda trabalhem para permitir a retrocompatibilidade, não são **recomendadas**. Para obter informações adicionais, reveja o [envio de dados de forma segura utilizando o TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12).

## <a name="connecting-operations-manager-to-azure-monitor"></a>Gestor de Operações de Ligação ao Monitor Azure

Realize esta série de passos para configurar o grupo de gestão do Operations Manager para se ligar a uma das suas áreas de trabalho do Log Analytics.

Durante o registo inicial do seu grupo de gestão de Gestão de Operações com um espaço de trabalho log Analytics, a opção de especificar a configuração de procuração para o grupo de gestão não está disponível na consola de Operações.  O grupo de gestão tem de ser registado com êxito no serviço para esta opção ficar disponível.  Para contornar isto, é necessário atualizar a configuração de proxy do sistema utilizando o Netsh no sistema a sua consola de funcionamento de operações para configurar a integração e todos os servidores de gestão do grupo de gestão.  

1. Abra uma linha de comandos elevada.
   a. Vá para **Iniciar** e digite **cmd**.
   b. Clique no comando **de modo** a partir de um pedido e selecione Executar como administrador**.
1. Introduza o seguinte comando e prima **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Depois de completar os seguintes passos para integrar com o Monitor Azure, pode remover a configuração executando `netsh winhttp reset proxy` e, em seguida, utilizar a opção de **servidor proxy Configure** na consola operações para especificar o servidor de gateway proxy ou Log Analytics.

1. Na consola do Operations Manager, selecione a área de trabalho **Administração**.
1. Expanda o nó Operations Management Suite e clique em **Ligação**.
1. Clique na ligação **Registar no Operations Management Suite**.
1. Na página **Assistente de Inclusão do Operations Management Suite: Autenticação**, introduza o endereço de e-mail ou o número de telefone e a palavra-passe da conta de administrador associada à sua subscrição do OMS e clique em **Iniciar sessão**.

   >[!NOTE]
   >O nome da Suite de Gestão de Operações foi retirado.

1. Depois de autenticado com sucesso, na suite de gestão de operações Assistente de **embarque: Selecione workspace** page, é solicitado a selecionar o seu inquilino, subscrição e espaço de trabalho Log Analytics. Se tiver mais do que uma área de trabalho, selecione a área de trabalho que pretende registar com o grupo de gestão do Operations Manager na lista pendente e clique em **Seguinte**.

   > [!NOTE]
   > O Operations Manager suporta apenas uma área de trabalho do Log Analytics de cada vez. A ligação e os computadores que estavam registados no Monitor Azure com o espaço de trabalho anterior são removidos do Monitor Azure.
   >
   >
1. Na página **Assistente de Inclusão do Operations Management Suite: Resumo**, confirme as definições, verifique se estão corretas e clique em **Criar**.
1. Na página **Assistente de Inclusão do Operations Management Suite: Concluir**, clique em **Fechar**.

### <a name="add-agent-managed-computers"></a>Adicionar computadores geridos por agentes

Depois de configurar a integração com o seu espaço de trabalho Log Analytics, apenas estabelece uma ligação com o serviço, não são recolhidos dados dos agentes que reportam ao seu grupo de gestão. Isto só acontecerá depois de configurar quais computadores específicos geridos por agentes recolhem dados de registo para o Monitor Azure. Pode selecionar os objetos de computador individualmente ou pode selecionar um grupo que contenha objetos de computador do Windows. Não pode selecionar um grupo que contenha instâncias de outra classe, tais como discos lógicos ou bases de dados SQL.

1. Abra a consola do Operations Manager e selecione a área de trabalho de **Administração**.
1. Expanda o nó Operations Management Suite e clique em **Ligação**.
1. Clique na ligação **Adicionar um Computador/Grupo** sob o cabeçalho Ações, no lado direito do painel.
1. Na caixa de diálogo **Pesquisa no Computador**, pode procurar os computadores ou os grupos monitorizados pelo Operations Manager. Selecione computadores ou grupos, incluindo o Servidor de Gestão de Gestão de Gestão de Operações para embarcar para o Monitor Azure, clique em **Adicionar**, e, em seguida, clique em **OK**.

Pode ver os computadores e os grupos configurados para recolher dados a partir do nó Computadores Geridos no Operations Management Suite na área de trabalho **Administração** da Consola de operações. Aqui, pode adicionar ou remover computadores e grupos, conforme necessário.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Configurar as definições do proxy na Consola de operações

Execute os seguintes passos se um servidor de procuração interna estiver entre o grupo de gestão e o Monitor Azure. Estas configurações são geridas centralmente a partir do grupo de gestão e distribuídas para sistemas geridos por agentes que estão incluídos no âmbito para recolher dados de registo para o Monitor Azure.  Tal é benéfico para quando determinadas soluções ignoram o servidor de gestão e enviam dados diretamente para o serviço.

1. Abra a consola do Operations Manager e selecione a área de trabalho de **Administração**.
1. Expanda o Operations Management Suite e clique em **Ligações**.
1. Na vista Ligação OMS, clique em **Configurar Servidor Proxy**.
1. Na página **Assistente do Operations Management Suite: Servidor Proxy**, selecione **Utilizar um servidor proxy para aceder ao Operations Management Suite**, escreva o URL com o número da porta, por exemplo, http://corpproxy:80 e, em seguida, clique em **Concluir**.

Se o seu servidor proxy necessitar de autenticação, execute os seguintes passos para configurar credenciais e configurações que precisam de se propagar para computadores geridos que reportam ao Azure Monitor no grupo de gestão.

1. Abra a consola do Operations Manager e selecione a área de trabalho de **Administração**.
1. Em **Configuração RunAs**, selecione **Perfis**.
1. Abra o perfil **System Center Advisor Run As Profile Proxy**.
1. No Assistente do Perfil Run As, clique em Adicionar para utilizar uma conta Run As. Pode criar uma [conta Run As](https://technet.microsoft.com/library/hh321655.aspx) ou utilizar uma existente. Esta conta tem de ter permissões suficientes para passar através do servidor proxy.
1. Para configurar a conta a gerir, escolha **Uma classe, um grupo ou um objeto selecionado**, clique em **Selecionar...** e, em seguida, clique em **Grupo...** para abrir a caixa **Pesquisa de Grupo**.
1. Pesquise e, em seguida, selecione **Microsoft System Center Advisor Monitoring Server Group**. Clique em **OK** depois de selecionar o grupo para fechar a caixa **Pesquisa de Grupo**.
1. Clique em **OK** para fechar a caixa **Adicionar uma conta Run As**.
1. Clique em **Guardar** para concluir o assistente e guardar as alterações.

Após a criação da ligação e configurar quais os agentes que irão recolher e reportar dados de registo ao Monitor Azure, a seguinte configuração é aplicada no grupo de gestão, não necessariamente por ordem:

* É criada a Conta Run As **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate**. A conta é associada ao perfil Run As **Blob do Perfil Run As do Microsoft System Center Advisor** e é direcionada para duas classes – **Servidor de Recolha** e **Grupo de Gestão do Operations Manager**.
* São criados dois conectores.  O primeiro chama-se **Microsoft.SystemCenter.Advisor.DataConnector** e é configurado automaticamente com uma subscrição que reencaminha todos os alertas gerados de instâncias de todas as classes do grupo de gestão para o Monitor Azure. O segundo conector é **advisor Connector,** que é responsável pela comunicação com o Monitor Azure e partilha de dados.
* Os agentes e os grupos que selecionou para recolher dados no grupo de gestão são adicionados ao **Grupo de Servidores de Monitorização do Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Atualizações dos pacotes de gestão

Após a configuração ser concluída, o grupo de gestão do Gestor de Operações estabelece uma ligação com o Monitor Azure. O servidor de gestão é sincronizado com o serviço Web e recebe informações de configuração atualizadas sob a forma de pacotes de gestão para as soluções ativadas que se integram com o Operations Manager. O Gestor de Operações verifica as atualizações destes pacotes de gestão e descarrega-os automaticamente e importa-os quando estão disponíveis. Existem duas regras específicas que controlam este comportamento:

* **Microsoft.SystemCenter.Advisor.MPUpdate** - Atualiza os pacotes de gestão base Do Monitor Do Azure. Por predefinição, é executada a cada 12 horas.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** – Atualiza os pacotes de gestão de soluções ativadas na sua área de trabalho. Por predefinição, é executada a cada cinco (5) minutos.

Pode anular estas duas regras para impedir o descarregamento automático, desativando-as, ou modificando a frequência da frequência com que o servidor de gestão sincroniza com o Monitor Azure para determinar se um novo pacote de gestão está disponível e deve ser descarregado. Siga os passos em [Como Substituir uma Regra ou um Monitor](https://technet.microsoft.com/library/hh212869.aspx) para modificar o parâmetro **Frequência** por um valor em segundos, para alterar a agenda de sincronização ou modificar o parâmetro **Ativada** para desativar as regras. Escolha as substituições para todos os objetos da classe do Grupo de Gestão do Operations Manager.

Para continuar a seguir o processo de controlo de alterações existente para controlar as libertações de pacotes de gestão no seu grupo de gestão de produção, pode desativar as regras e capacitá-las em momentos específicos em que as atualizações são permitidas. Se tiver um grupo de desenvolvimento ou de gestão de QA no seu ambiente e este tiver conetividade à Internet, poderá configurar esse grupo de gestão com uma área de trabalho do Log Analytics para suportar este cenário. Isto permite-lhe rever e avaliar os lançamentos iterativos dos pacotes de gestão Do Monitor Azure antes de os lançar no seu grupo de gestão de produção.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Mudar de um grupo do Operations Manager para uma nova Área de Trabalho do Log Analytics

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
1. No portal do Azure, clique em **Mais serviços**, que se encontra no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Log Analytics** e, em seguida, crie uma área de trabalho.  
1. Abra a consola do Operations Manager com uma conta que seja um membro da função Administradores do Operations Manager e selecione a área de trabalho **Administração**.
1. Expandir o Log Analytics e selecionar **Ligações**.
1. Selecione a ligação **Reconfigurar o Operation Management Suite** no meio do painel.
1. Siga o Assistente de Acesso de **Log Analytics** e introduza o endereço de e-mail ou número de telefone e a palavra-passe da conta de administrador que está associada ao seu novo espaço de trabalho do Log Analytics.

   > [!NOTE]
   > A página **Assistente de Inclusão do Operations Management Suite: Selecionar Área de Trabalho** apresenta a área de trabalho existente que está a ser utilizada.
   >
   >

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Validar Integração de Gestor de Operações com o Monitor Azure

Existem algumas formas diferentes de verificar se a integração do Azure Monitor para o Gestor de Operações é bem sucedida.

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

## <a name="remove-integration-with-azure-monitor"></a>Remover integração com o Monitor Azure

Quando já não necessitar da integração entre o grupo de gestão do Operations Manager e a área de trabalho do Log Analytics, existem vários passos necessários para remover corretamente a ligação e a configuração no grupo de gestão. O procedimento seguinte faz com que atualize o seu espaço de trabalho Log Analytics eliminando a referência do seu grupo de gestão, eliminando os conectores Do Monitor Azure e, em seguida, elimine pacotes de gestão que suportem a integração com o serviço.  

Os pacotes de gestão para as soluções que permitiu que se integrem com o Gestor de Operações e os pacotes de gestão necessários para apoiar a integração com o Azure Monitor não podem ser facilmente eliminados do grupo de gestão. Isto porque alguns dos pacotes de gestão do Azure Monitor têm dependências de outros pacotes de gestão relacionados. Para eliminar pacotes de gestão com dependências noutros pacotes de gestão, transfira o script [remover um pacote de gestão com dependências](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) do Centro de Scripts do TechNet.  

1. Abra a Shell de Comandos do Operations Manager com uma conta que seja um membro da função Administradores do Operations Manager.

    > [!WARNING]
    > Verifique que não tem quaisquer pacotes de gestão personalizados com a palavra Assistente ou PacoteInteligente no nome antes de continuar. Caso contrário, os seguintes passos vão eliminá-los do grupo de gestão.
    >

1. Na shell de comandos, escreva `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. A seguir, escreva `Get-SCOMManagementPack -name "*IntelligencePack*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Para remover quaisquer pacotes de gestão restantes que tenham dependências noutros pacotes de gestão do System Center Advisor, utilize o script *RecursiveRemove.ps1* que transferiu do Centro de Scripts da TechNet anteriormente.  

    > [!NOTE]
    > O passo para remover os pacotes de gestão do Advisor com o PowerShell não eliminará automaticamente os pacotes internos do Microsoft System Center Advisor ou do Microsoft System Center Advisor.  Não tente eliminá-los.  
    >  

1. Abra a consola de Operações do Operations Manager com uma conta que seja um membro da função Administradores do Operations Manager.
1. Em **Administração**, selecione o nó **Pacotes de Gestão** e, na caixa **Procurar:** , escreva **Assistente** e verifique se os seguintes pacotes de gestão ainda estão a ser importados no grupo de gestão:

   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Interno

1. No portal Azure, clique no azulejo **Definições.**
1. Selecione **Fontes Ligadas**.
1. Na tabela sob a secção Gestor de Operações do System Center, deve ver o nome do grupo de gestão que pretende remover do espaço de trabalho. Sob a coluna **Últimos Dados**, clique em **Remover**.  

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

No futuro, se planeia reconectar o seu grupo de gestão a um espaço de trabalho de Log Analytics, precisa de reimportar o ficheiro de pacote de gestão `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb`. Consoante a versão do System Center Operations Manager implementada no seu ambiente, pode encontrar este ficheiro na seguinte localização:

* No suporte de dados de origem na pasta `\ManagementPacks` do System Center 2016 – Operations Manager e superior.
* No rollup de atualizações mais recentes aplicado ao grupo de gestão. Para o Gestor de Operações 2012, a pasta de origem é `%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` e para 2012 R2, está localizada em `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`.

## <a name="next-steps"></a>Passos seguintes

Para adicionar funcionalidade e recolher dados, consulte [soluções Add Azure Monitor a partir da Galeria soluções](../../azure-monitor/insights/solutions.md).
