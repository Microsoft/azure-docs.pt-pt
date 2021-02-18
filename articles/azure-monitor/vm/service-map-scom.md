---
title: Integre o Monitor Azure para o mapa de VMs com o gestor de operações | Microsoft Docs
description: O Azure Monitor para VMs descobre automaticamente componentes de aplicações em sistemas Windows e Linux e mapeia a comunicação entre serviços. Este artigo discute a utilização da funcionalidade Mapa para criar automaticamente diagramas de aplicações distribuídos no Gestor de Operações.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: 0722a1806cc94102f92045c78850d96ed9890d02
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100613068"
---
# <a name="integrate-system-center-operations-manager-with-azure-monitor-for-vms-map-feature"></a>Integrar gestor de operações do Centro de Sistema com Monitor Azure para funcionalidade de Mapa de VMs

No Azure Monitor para VMs, pode ver os componentes de aplicações descobertos nas máquinas virtuais Windows e Linux (VMs) que funcionam no Azure ou no seu ambiente. Com esta integração entre a funcionalidade Mapa e o Gestor de Operações do Centro de Sistema, pode criar automaticamente diagramas de aplicações distribuídos em Gestor de Operações que se baseiam nos mapas de dependência dinâmica no Azure Monitor para VMs. Este artigo descreve como configurar o seu grupo de gestão de Operações do System Center para suportar esta funcionalidade.

>[!NOTE]
>Se já implementou o Mapa de Serviços, pode ver os seus mapas no Azure Monitor para VMs, que inclui funcionalidades adicionais para monitorizar a saúde e o desempenho do VM. A funcionalidade mapa do Monitor Azure para VMs destina-se a substituir a solução de Mapa de Serviço autónomo. Para saber mais, consulte [o Azure Monitor para obter uma visão geral dos VMs](../vm/vminsights-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um grupo de gestão de Gestor de Operações do System Center (2012 R2 ou posterior).
* Um espaço de trabalho Log Analytics configurado para suportar o Monitor Azure para VMs.
* Uma ou mais máquinas virtuais windows e Linux ou computadores físicos que são monitorizados pelo Gestor de Operações e enviam dados para o seu espaço de trabalho Log Analytics. Os servidores Linux que reportam a um grupo de gestão de Gestores de Operações precisam de ser configurados para se ligarem diretamente ao Azure Monitor. Para obter mais informações, reveja a visão geral no [Recolher dados de registo com o agente Log Analytics](../agents/log-analytics-agent.md).
* Um diretor de serviço com acesso à subscrição Azure que está associado ao espaço de trabalho Log Analytics. Para mais informações, aceda à [Criação de um principal de serviço.](#create-a-service-principal)

## <a name="install-the-service-map-management-pack"></a>Instale o pacote de gestão do Mapa de Serviço

Você ativa a integração entre Gestor de Operações e a funcionalidade Mapa importando o pacote Microsoft.Sysde pacotes de gestão temCenter.ServiceMap (Microsoft.SystemCenter.ServiceMap.mpb). Você pode baixar o pacote de pacotes de gestão do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=55763). O pacote contém os seguintes pacotes de gestão:

* Vistas da aplicação do mapa de serviço da Microsoft
* Mapa de serviço do Microsoft System Center Interno
* Mapa de serviço do Microsoft System Center sobrepõe-se
* Mapa de serviço do Microsoft System Center

## <a name="configure-integration"></a>Integração configure

Depois de instalar o pacote de gestão do Mapa de Serviço, um novo nó, **Mapa de Serviço,** é apresentado no âmbito do **Pacote de Gestão** de Operações no painel de **Administração** da sua consola Operations Manager Operations.

>[!NOTE]
>[Operations Management Suite foi uma coleção de serviços](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) que incluiu Log Analytics, agora faz parte do [Azure Monitor.](../overview.md)

Para configurar o Monitor Azure para a integração do Mapa VMs, faça o seguinte:

1. Para abrir o assistente de configuração, no painel de visão geral do **mapa de serviço,** clique em Adicionar espaço de **trabalho**.  

    ![Painel de visão geral do mapa de serviço](media/service-map-scom/scom-configuration.png)

2. Na janela **de Configuração de Ligação,** insira o nome do inquilino ou ID, ID de aplicação (também conhecido como nome de utilizador ou clienteID) e senha do principal de serviço e, em seguida, clique em **Seguinte**. Para mais informações, aceda à Criação de um diretor de serviço.

    ![A janela de configuração de ligação](media/service-map-scom/scom-config-spn.png)

3. Na janela **De Seleção de Assinaturas,** selecione a subscrição Azure, o grupo de recursos Azure (o que contém o espaço de trabalho Log Analytics) e o espaço de trabalho do Log Analytics e, em seguida, clique em **Seguinte**.

    ![O espaço de trabalho de configuração do gestor de operações](media/service-map-scom/scom-config-workspace.png)

4. Na janela **de seleção do grupo de máquinas,** escolha quais grupos de máquinas de mapa de serviço pretende sincronizar com o Gestor de Operações. Clique em **Adicionar/Remover Grupos de Máquinas,** escolha grupos na lista de grupos de **máquinas disponíveis** e clique em **Adicionar**.  Quando terminar de selecionar grupos, clique em **Ok** para terminar.

    ![Os grupos de máquinas de configuração do gestor de operações](media/service-map-scom/scom-config-machine-groups.png)

5. Na janela **de seleção** do servidor, configura o Grupo de Servidores de Mapas de Serviço com os servidores que pretende sincronizar entre o Gestor de Operações e a funcionalidade Mapa. Clique **em Adicionar/Remover Servidores**.

    Para a integração para construir um diagrama de aplicação distribuído para um servidor, o servidor deve ser:

   * Monitorizado pelo Gestor de Operações
   * Configurado para reportar ao espaço de trabalho Log Analytics configurado com monitor Azure para VMs
   * Listado no Grupo de Servidores de Mapas de Serviço

     ![O Grupo de Configuração do Gestor de Operações](media/service-map-scom/scom-config-group.png)

6. Opcional: Selecione o Pool de Recursos de Todos os Servidores de Gestão para comunicar com o Log Analytics e, em seguida, clique em **Adicionar Espaço de Trabalho**.

    ![Screenshot do ecrã do Pool do Servidor no Adicionar espaço de trabalho da Suite de Gestão de Operações da Microsoft com todos os servidores de gestão selecionados.](media/service-map-scom/scom-config-pool.png)

    Pode levar um minuto para configurar e registar o espaço de trabalho Log Analytics. Depois de configurado, o Gestor de Operações inicia a primeira sincronização do Mapa.

    ![Screenshot do ecrã De conclusão no Add Microsoft Operations Management Suite Workspace confirmando que o Espaço de Trabalho foi adicionado.](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>Monitorização da integração

Após a ligação do espaço de trabalho Do Log Analytics, é apresentada uma nova pasta, Mapa de Serviço, no painel de **monitorização** da consola Operations Manager Operations.

![Painel de Monitorização do Gestor de Operações](media/service-map-scom/scom-monitoring.png)

A pasta Mapa de Serviço tem quatro nóns:

* **Alertas Ativos**: Lista todos os alertas ativos sobre a comunicação entre Gestor de Operações e Monitor Azure.  

  >[!NOTE]
  >Estes alertas não são alertas do Log Analytics sincronizados com o Gestor de Operações, são gerados no grupo de gestão com base nos fluxos de trabalho definidos no pacote de gestão do Mapa de Serviço.

* **Servidores**: Lista os servidores monitorizados configurados para sincronizar a partir do Monitor Azure para a funcionalidade de Mapa de VMs.

    ![O painel de servidores de monitorização do gestor de operações](media/service-map-scom/scom-monitoring-servers.png)

* **Pontos de vista de dependência do grupo de máquinas**: Lista todos os grupos de máquinas que estão sincronizados a partir da funcionalidade Mapa. Pode clicar em qualquer grupo para ver o seu diagrama de aplicação distribuído.

    ![Screenshot do Mapa de Serviço mostrando um diagrama com imagens para cada grupo de máquinas e linhas que indicam as dependências entre eles.](media/service-map-scom/scom-group-dad.png)

* **Visões de dependência do servidor**: Lista todos os servidores que estão sincronizados a partir da funcionalidade Mapa. Pode clicar em qualquer servidor para ver o diagrama de aplicação distribuído.

    ![Screenshot do Mapa de Serviço mostrando um diagrama com imagens para cada servidor e linhas que indicam as dependências entre eles.](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Editar ou apagar o espaço de trabalho

Pode editar ou eliminar o espaço de trabalho configurado através do painel de visão geral do **mapa de serviços** **(painel de administração** > mapa de serviço **da Suite de Gestão de Operações).**  >  

> [!NOTE]
> [O Suíte de Gestão de Operações foi uma coleção de serviços](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) que incluía o Log Analytics, que agora faz parte do [Azure Monitor.](../overview.md)

Pode configurar apenas um espaço de trabalho log Analytics nesta versão atual.

![O painel de trabalho do Gestor de Operações edita o espaço de trabalho](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Configurar regras e sobreposições

Uma regra, *Microsoft.SystemCenter.ServiceMapImport.Rule,* procura periodicamente informações do Azure Monitor para a funcionalidade VMs Map. Para modificar o intervalo de sincronização, pode anular a regra e modificar o valor para o parâmetro **IntervalMinutes**.

![O Gestor de Operações sobrepõe-se à janela de propriedades](media/service-map-scom/scom-overrides.png)

* **Ativado**: Ative ou desative as atualizações automáticas.
* **IntervalosMinutos**: Especifica o tempo entre atualizações. O intervalo predefinido é de uma hora. Se quiser sincronizar mapas com mais frequência, pode alterar o valor.
* **TimeoutSeconds**: Especifica o período de tempo antes do fim do prazo de pedido.
* **TimeWindowMinutes**: Especifica a janela de tempo para consulta de dados. O padrão é de 60 minutos, que é o intervalo máximo permitido.

## <a name="known-issues-and-limitations"></a>Limitações e problemas conhecidos

O desenho atual apresenta as seguintes questões e limitações:

* Só é possível ligar-se a um único espaço de trabalho do Log Analytics.
* Embora possa adicionar servidores ao Grupo de Servidores de Mapas de Serviço manualmente através do painel **de autores,** os mapas para esses servidores não são sincronizados imediatamente. Serão sincronizados a partir do Monitor Azure para a funcionalidade VMs Map durante o próximo ciclo de sincronização.
* Se efecer quaisquer alterações aos Diagramas de Aplicações Distribuídas criadas pelo pacote de gestão, essas alterações serão provavelmente substituídas na próxima sincronização com o Azure Monitor para VMs.

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Para documentação oficial da Azure sobre a criação de um diretor de serviço, consulte:

* [Criar um principal de serviço utilizando o PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Criar um principal de serviço utilizando o Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli)
* [Criar um principal de serviço utilizando o portal Azure](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="suggestions"></a>Sugestões

Tem algum feedback sobre a integração com o Azure Monitor para funcionalidade de Mapa de VMs ou esta documentação? Visite a nossa [página De Voz do Utilizador,](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)onde pode sugerir funcionalidades ou votar nas sugestões existentes.

