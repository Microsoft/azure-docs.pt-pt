---
title: Integrar o mapa de Azure Monitor para VMs com Operations Manager | Microsoft Docs
description: Azure Monitor para VMs descobre automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Este artigo aborda o uso do recurso de mapa para criar automaticamente diagramas de aplicativos distribuídos no Operations Manager.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: a8d0e0980b0d3634626bc1332e8219bdca7fbe01
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75402628"
---
# <a name="integrate-system-center-operations-manager-with-azure-monitor-for-vms-map-feature"></a>Integrar System Center Operations Manager com o recurso de mapa de Azure Monitor para VMs

No Azure Monitor para VMs, você pode exibir componentes de aplicativos descobertos em VMs (máquinas virtuais) do Windows e Linux que são executadas no Azure ou no seu ambiente. Com essa integração entre o recurso de mapa e System Center Operations Manager, você pode criar automaticamente diagramas de aplicativo distribuídos em Operations Manager que se baseiam nos mapas de dependência dinâmica no Azure Monitor para VMs. Este artigo descreve como configurar seu grupo de gerenciamento de System Center Operations Manager para dar suporte a esse recurso.

>[!NOTE]
>Se você já tiver implantado Mapa do Serviço, poderá exibir seus mapas no Azure Monitor para VMs, que inclui recursos adicionais para monitorar a integridade e o desempenho da VM. O recurso de mapa do Azure Monitor para VMs destina-se a substituir a solução de Mapa do Serviço autônoma. Para saber mais, confira [Azure monitor para VMs visão geral](vminsights-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um grupo de gerenciamento de System Center Operations Manager (2012 R2 ou posterior).
* Um espaço de trabalho Log Analytics configurado para dar suporte a Azure Monitor para VMs.
* Uma ou mais máquinas virtuais do Windows e Linux ou computadores físicos que são monitorados pelo Operations Manager e enviando dados para seu espaço de trabalho do Log Analytics. Os servidores Linux que se reportam a um grupo de gerenciamento de Operations Manager precisam ser configurados para se conectar diretamente ao Azure Monitor. Para obter mais informações, consulte a visão geral em [coletar dados de log com o agente de log Analytics](../platform/log-analytics-agent.md).
* Uma entidade de serviço com acesso à assinatura do Azure associada ao espaço de trabalho Log Analytics. Para obter mais informações, acesse [criar uma entidade de serviço](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Instalar o pacote de gerenciamento do Mapa do Serviço

Você habilita a integração entre Operations Manager e o recurso de mapa importando o pacote de gerenciamento Microsoft. SystemCenter. ServiceMap (Microsoft. SystemCenter. ServiceMap. MPB). Você pode baixar o pacote de gerenciamento do [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=55763). O pacote contém os seguintes pacotes de gerenciamento:

* Exibições do aplicativo Microsoft Mapa do Serviço
* Microsoft System Center Mapa do Serviço interno
* Substituições do Microsoft System Center Mapa do Serviço
* Mapa do Serviço do Microsoft System Center

## <a name="configure-integration"></a>Configurar a integração

Depois de instalar o pacote de gerenciamento do Mapa do Serviço, um novo nó, **mapa do serviço**, será exibido em **Operations Management Suite** no painel **Administração** do console de operações do Operations Manager.

>[!NOTE]
>[O Operations Management Suite foi uma coleção de serviços](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) que incluíam log Analytics, agora faz parte do [Azure monitor](../overview.md).

Para configurar a integração do mapa do Azure Monitor para VMs, faça o seguinte:

1. Para abrir o assistente de configuração, no painel **visão geral do mapa do serviço** , clique em **adicionar espaço de trabalho**.  

    ![Painel de visão geral do Mapa do Serviço](media/service-map-scom/scom-configuration.png)

2. Na janela **configuração de conexão** , insira o nome ou a ID do locatário, a ID do aplicativo (também conhecida como nome de usuário ou ClientID) e a senha da entidade de serviço e clique em **Avançar**. Para obter mais informações, acesse criar uma entidade de serviço.

    ![A janela de configuração de conexão](media/service-map-scom/scom-config-spn.png)

3. Na janela **seleção de assinatura** , selecione a assinatura do Azure, o grupo de recursos do Azure (aquele que contém o espaço de trabalho log Analytics) e log Analytics espaço de trabalho e clique em **Avançar**.

    ![O espaço de trabalho de configuração do Operations Manager](media/service-map-scom/scom-config-workspace.png)

4. Na janela **seleção de grupo de computadores** , escolha quais mapa do serviço grupos de computadores que você deseja sincronizar com Operations Manager. Clique em **Adicionar/remover grupos de computadores**, escolha grupos na lista de **grupos de computadores disponíveis**e clique em **Adicionar**.  Quando terminar de selecionar grupos, clique em **OK** para concluir.

    ![Os grupos de computadores de configuração de Operations Manager](media/service-map-scom/scom-config-machine-groups.png)

5. Na janela **seleção de servidor** , você configura o grupo de servidores mapa do serviço com os servidores que você deseja sincronizar entre Operations Manager e o recurso de mapa. Clique em **Adicionar/remover servidores**.

    Para que a integração crie um diagrama de aplicativo distribuído para um servidor, o servidor deve ser:

   * Monitorado por Operations Manager
   * Configurado para relatar para o espaço de trabalho Log Analytics configurado com Azure Monitor para VMs
   * Listado no grupo de servidores Mapa do Serviço

     ![O grupo de configuração Operations Manager](media/service-map-scom/scom-config-group.png)

6. Opcional: selecione o pool de recursos de todos os servidores de gerenciamento para se comunicar com Log Analytics e, em seguida, clique em **adicionar espaço de trabalho**.

    ![O pool de recursos de configuração do Operations Manager](media/service-map-scom/scom-config-pool.png)

    Pode levar um minuto para configurar e registrar o espaço de trabalho Log Analytics. Depois de configurado, Operations Manager inicia a primeira sincronização do mapa.

    ![O pool de recursos de configuração do Operations Manager](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>Monitorar a integração

Depois que o espaço de trabalho do Log Analytics estiver conectado, uma nova pasta, Mapa do Serviço, será exibida no painel **monitoramento** do console de operações do Operations Manager.

![O painel Monitoramento de Operations Manager](media/service-map-scom/scom-monitoring.png)

A pasta Mapa do Serviço tem quatro nós:

* **Alertas ativos**: lista todos os alertas ativos sobre a comunicação entre Operations Manager e Azure monitor.  

  >[!NOTE]
  >Esses alertas não são Log Analytics alertas sincronizados com o Operations Manager, eles são gerados no grupo de gerenciamento com base em fluxos de trabalho definidos no pacote de gerenciamento do Mapa do Serviço.

* **Servidores**: lista os servidores monitorados que estão configurados para sincronização de Azure monitor para VMs recurso de mapa.

    ![O painel Operations Manager servidores de monitoramento](media/service-map-scom/scom-monitoring-servers.png)

* **Exibições de dependência do grupo de computadores**: lista todos os grupos de computadores que são sincronizados a partir do recurso de mapa. Você pode clicar em qualquer grupo para exibir seu diagrama de aplicativo distribuído.

    ![O diagrama de aplicativo distribuído Operations Manager](media/service-map-scom/scom-group-dad.png)

* **Exibições de dependência do servidor**: lista todos os servidores que são sincronizados a partir do recurso de mapa. Você pode clicar em qualquer servidor para exibir seu diagrama de aplicativo distribuído.

    ![O diagrama de aplicativo distribuído Operations Manager](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Editar ou excluir o espaço de trabalho

Você pode editar ou excluir o espaço de trabalho configurado por meio do painel de **visão geral mapa do serviço** (painel**Administração** > **operations Management Suite** > **mapa do serviço**).

>[!NOTE]
>[O Operations Management Suite foi uma coleção de serviços](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) que incluíam log Analytics, que agora faz parte do [Azure monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md).

Você pode configurar apenas um espaço de trabalho Log Analytics nesta versão atual.

![O painel Operations Manager editar espaço de trabalho](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Configurar regras e substituições

Uma regra, *Microsoft. SystemCenter. ServiceMapImport. Rule*, busca periodicamente informações de Azure monitor para VMs recurso de mapa. Para modificar o intervalo de sincronização, você pode substituir a regra e modificar o valor do parâmetro **IntervalMinutes**.

![A Operations Manager substitui a janela Propriedades](media/service-map-scom/scom-overrides.png)

* **Habilitado**: habilitar ou desabilitar atualizações automáticas.
* **IntervalMinutes**: especifica o tempo entre as atualizações. O intervalo padrão é de uma hora. Se você quiser sincronizar mapas com mais frequência, poderá alterar o valor.
* **TimeoutSeconds**: especifica o período de tempo antes que a solicitação expire.
* **TimeWindowMinutes**: especifica a janela de tempo para consultar dados. O padrão é 60 minutos, que é o intervalo máximo permitido.

## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos

O design atual apresenta os seguintes problemas e limitações:

* Você só pode se conectar a um único espaço de trabalho Log Analytics.
* Embora você possa adicionar servidores ao grupo de servidores Mapa do Serviço manualmente por meio do painel **criação** , os mapas desses servidores não são sincronizados imediatamente. Eles serão sincronizados de Azure Monitor para VMs recurso de mapa durante o próximo ciclo de sincronização.
* Se você fizer alterações nos diagramas de aplicativo distribuído criados pelo pacote de gerenciamento, essas alterações provavelmente serão substituídas na próxima sincronização com Azure Monitor para VMs.

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Para obter a documentação oficial do Azure sobre a criação de uma entidade de serviço, consulte:

* [Criar uma entidade de serviço usando o PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Criar uma entidade de serviço usando CLI do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Criar uma entidade de serviço usando o portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Comentários
Você tem algum comentário para nós sobre a integração com Azure Monitor para VMs recurso de mapa ou esta documentação? Visite nossa [página de voz do usuário](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), onde você pode sugerir recursos ou votar em sugestões existentes.
