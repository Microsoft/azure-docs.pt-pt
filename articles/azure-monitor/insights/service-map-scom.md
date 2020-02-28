---
title: Integrar o Monitor Azure para o Mapa de VMs com o Gestor de Operações  Microsoft Docs
description: O Monitor Azure para VMs descobre automaticamente componentes de aplicações nos sistemas Windows e Linux e mapeia a comunicação entre serviços. Este artigo discute a utilização da funcionalidade Map para criar automaticamente diagramas de aplicações distribuídos no Gestor de Operações.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: 112413720f969474369555a74bc89846666e2ef9
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663458"
---
# <a name="integrate-system-center-operations-manager-with-azure-monitor-for-vms-map-feature"></a>Integrar o Gestor de Operações do Centro de Sistemacom o Monitor Azure para a funcionalidade do Mapa de VMs

No Azure Monitor para VMs, pode ver componentes de aplicações descobertos em máquinas virtuais Windows e Linux (VMs) que funcionam em Azure ou no seu ambiente. Com esta integração entre a funcionalidade Map e o System Center Operations Manager, pode criar automaticamente diagramas de aplicação distribuídos no Gestor de Operações que são baseados nos mapas dinâmicos de dependência no Monitor Azure para VMs. Este artigo descreve como configurar o seu grupo de gestão de Gestão de Operações do System Center para suportar esta funcionalidade.

>[!NOTE]
>Se já implementou o Mapa de Serviço, pode ver os seus mapas no Monitor Azure para VMs, que inclui funcionalidades adicionais para monitorizar a saúde e desempenho da VM. A função Map do Monitor Azure para VMs destina-se a substituir a solução de Mapa de Serviço autónomo. Para saber mais, consulte [o Monitor Azure para visualização geral dos VMs.](vminsights-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Um grupo de gestão de Gestão de Operações do Centro de Sistema (2012 R2 ou mais tarde).
* Um espaço de trabalho de Log Analytics configurado para suportar o Monitor Azure para VMs.
* Uma ou mais máquinas virtuais windows e Linux ou computadores físicos que são monitorizados pelo Gestor de Operações e enviando dados para o seu espaço de trabalho Log Analytics. Os servidores Linux que reportam a um grupo de gestão do Gestor de Operações precisam de ser configurados para ligar diretamente ao Monitor Azure. Para mais informações, reveja a visão geral em Recolher dados de [registo com o agente Log Analytics](../platform/log-analytics-agent.md).
* Um diretor de serviço com acesso à subscrição Azure que está associado ao espaço de trabalho log Analytics. Para mais informações, vá à [Create a service principal](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Instale o pacote de gestão do Mapa de Serviços

Permite a integração entre o Gestor de Operações e a funcionalidade Map importando o pacote de gestão Microsoft.SystemCenter.ServiceMap (Microsoft.SystemCenter.ServiceMap.mpb). Pode baixar o pacote de pacotes de gestão do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=55763). O pacote contém os seguintes pacotes de gestão:

* Vistas de aplicação de mapas de serviço da Microsoft
* Mapa interno do serviço do centro do sistema da Microsoft
* Mapa do serviço do Microsoft System Center Overrides
* Mapa do serviço do Microsoft System Center

## <a name="configure-integration"></a>Configurar a integração

Depois de instalar o pacote de gestão do Mapa de Serviços, um novo nó, O Mapa de **Serviços,** é exibido no âmbito da Suite de Gestão de **Operações** no painel de **Administração** da consola operamanager operations.

>[!NOTE]
>[A Operations Management Suite foi uma coleção de serviços](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) que incluía log Analytics, agora faz parte do [Azure Monitor.](../overview.md)

Para configurar o Monitor Azure para integração do Mapa de VMs, faça o seguinte:

1. Para abrir o assistente de configuração, no painel de visão geral do mapa de **serviço,** clique em **Adicionar espaço de trabalho**.  

    ![Painel de visão geral do mapa do serviço](media/service-map-scom/scom-configuration.png)

2. Na janela de Configuração de **Ligação,** introduza o nome ou ID do inquilino, id da aplicação (também conhecido como nome de utilizador ou id cliente) e palavra-passe do diretor de serviço, e, em seguida, clique em **Seguinte**. Para mais informações, vá à Create a service principal.

    ![A janela de configuração de ligação](media/service-map-scom/scom-config-spn.png)

3. Na janela **de Seleção** de Assinaturas, selecione a subscrição Azure, grupo de recursos Azure (aquele que contém o espaço de trabalho log Analytics) e log Analytics espaço de trabalho, e, em seguida, clique **em Next**.

    ![Espaço de trabalho de configuração do gestor de operações](media/service-map-scom/scom-config-workspace.png)

4. Na janela **machine group selection,** escolha quais os Grupos de Máquinas de Mapas de Serviço que pretende sincronizar para o Gestor de Operações. Clique em **Adicionar/Remover Grupos de Máquinas,** escolha grupos da lista de grupos de **máquinas disponíveis,** e clique em **Adicionar**.  Quando terminar de selecionar grupos, clique em **Ok** para terminar.

    ![Os grupos de máquinas de configuração do gestor de operações](media/service-map-scom/scom-config-machine-groups.png)

5. Na janela de **Seleção** de Servidores, configura o Grupo de Servidores de Mapas de Serviço com os servidores que pretende sincronizar entre o Gestor de Operações e a funcionalidade Map. Clique em **Adicionar/Remover Servidores**.

    Para a integração para construir um diagrama de aplicação distribuído para um servidor, o servidor deve ser:

   * Monitorizado por Gestor de Operações
   * Configurado para reportar ao espaço de trabalho log Analytics configurado com Monitor Azure para VMs
   * Listado no Grupo de Servidores de Mapas de Serviço

     ![O Grupo de Configuração do Gestor de Operações](media/service-map-scom/scom-config-group.png)

6. Opcional: Selecione o Conjunto de Recursos de Todos os Servidores de Gestão para comunicar com o Log Analytics e, em seguida, clique em **Adicionar Espaço de Trabalho**.

    ![O conjunto de recursos de configuração do gestor de operações](media/service-map-scom/scom-config-pool.png)

    Pode levar um minuto para configurar e registar o espaço de trabalho do Log Analytics. Depois de configurado, o Gestor de Operações inicia a primeira sincronização do Mapa.

    ![O conjunto de recursos de configuração do gestor de operações](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>Monitorizar a integração

Após a ligação do espaço de trabalho do Log Analytics, uma nova pasta, O Mapa de Serviço, é exibida no painel de **monitorização** da consola operamanager Operations.

![O painel de monitorização do Gestor de Operações](media/service-map-scom/scom-monitoring.png)

A pasta do Mapa de Serviço tem quatro nódosos:

* **Alertas Ativos**: Lista todos os alertas ativos sobre a comunicação entre o Gestor de Operações e o Monitor Azure.  

  >[!NOTE]
  >Estes alertas não são alertas de Log Analytics sincronizados com o Gestor de Operações, são gerados no grupo de gestão com base em fluxos de trabalho definidos no pacote de gestão do Mapa de Serviços.

* **Servidores**: Lista os servidores monitorizados que estão configurados para sincronizar a partir do Monitor Azure para a funcionalidade Mapa de VMs.

    ![O painel de servidores de monitorização do gestor de operações](media/service-map-scom/scom-monitoring-servers.png)

* **Pontos de vista de dependência**do grupo máquina : Lista todos os grupos de máquinas que estão sincronizados a partir da função Map. Pode clicar em qualquer grupo para ver o diagrama de aplicação distribuído.

    ![O gestor de operações distribuiu o diagrama de aplicação](media/service-map-scom/scom-group-dad.png)

* **Pontos de vista de dependência**do servidor : Lista todos os servidores que estão sincronizados a partir da funcionalidade Map. Pode clicar em qualquer servidor para visualizar o diagrama de aplicação distribuído.

    ![O gestor de operações distribuiu o diagrama de aplicação](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Editar ou eliminar o espaço de trabalho

Pode editar ou eliminar o espaço de trabalho configurado através do painel de visão geral do mapa de **serviços** (Painel de**Administração** > Suite de Gestão de **Operações** > Mapa de **Serviço).**

>[!NOTE]
>[A Operations Management Suite foi uma coleção de serviços](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) que incluía o Log Analytics, que agora faz parte do [Azure Monitor.](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md)

Pode configurar apenas um espaço de trabalho log Analytics neste lançamento atual.

![O painel do espaço de trabalho do gestor de operações edita](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Configurar regras e substituições

Uma regra, *Microsoft.SystemCenter.ServiceMapImport.Rule*, requer periodicamente informações do Monitor Azure para a funcionalidade VMs Map. Para modificar o intervalo de sincronização, pode anular a regra e modificar o valor para o parâmetro **IntervalMinutes**.

![O Gestor de Operações substitui a janela de propriedades](media/service-map-scom/scom-overrides.png)

* **Ativado**: Ativar ou desativar atualizações automáticas.
* **IntervaloS**: Especifica o tempo entre atualizações. O intervalo padrão é de uma hora. Se quiser sincronizar mapas com mais frequência, pode alterar o valor.
* **Tempo limiteSeconds**: Especifica o tempo de duração antes do tempo de pedido.
* **TimeWindowMinutes**: Especifica a janela de tempo para consulta de dados. O padrão é de 60 minutos, que é o intervalo máximo permitido.

## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos

O design atual apresenta as seguintes questões e limitações:

* Só é possível ligar-se a um único espaço de trabalho do Log Analytics.
* Embora possa adicionar servidores ao Grupo de Servidores de Mapas de Serviço manualmente através do painel **de autor,** os mapas desses servidores não são sincronizados imediatamente. Serão sincronizados a partir do Azure Monitor para vMs Map durante o próximo ciclo de sincronização.
* Se fizer alterações nos Diagramas de Aplicação Distribuídas criados pelo pacote de gestão, essas alterações serão provavelmente substituídas na próxima sincronização com o Monitor Azure para VMs.

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Para documentação oficial do Azure sobre a criação de um diretor de serviço, consulte:

* [Criar um principal de serviço utilizando o PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Criar um diretor de serviço utilizando o Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Criar um serviço principal utilizando o portal Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Comentários
Tem algum feedback sobre a integração com o Azure Monitor para a funcionalidade VMs Map ou esta documentação? Visite a nossa [página de Voz do Utilizador,](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)onde pode sugerir funcionalidades ou votar nas sugestões existentes.
