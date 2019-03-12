---
title: Integração de mapa de serviço com o System Center Operations Manager | Documentos da Microsoft
description: O Mapa de Serviços é uma solução no Azure que deteta componentes da aplicação em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Este artigo aborda o uso do mapa de serviço para criar automaticamente diagramas de aplicação distribuída no Operations Manager.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: magoedte
ms.openlocfilehash: 878abe79754c3286b77aef744d1bc4973a8af841
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57729401"
---
# <a name="service-map-integration-with-system-center-operations-manager"></a>Integração de mapa de serviço com o System Center Operations Manager

O Mapa de Serviço deteta automaticamente componentes de aplicações em sistemas Windows e Linux e mapeia a comunicação entre serviços. Mapa de serviço permite-lhe ver os servidores a forma como considerá-los, como sistemas interconectados que fornecem serviços críticos. Mapa de serviço mostra ligações entre servidores, processos e as portas em qualquer arquitetura ligado a TCP, sem qualquer configuração necessária para além da instalação de um agente. Para obter mais informações, consulte a [documentação de mapa de serviço]( service-map.md).

Com esta integração entre o mapa de serviço e o System Center Operations Manager, pode criar automaticamente diagramas de aplicação distribuída no Operations Manager que se baseiam os mapas de dependência dinâmico no mapa de serviço.

## <a name="prerequisites"></a>Pré-requisitos
* Um grupo de gestão do Operations Manager (2012 R2 ou posterior) que está a gerir um conjunto de servidores.
* Uma área de trabalho do Log Analytics com a solução mapa de serviço ativada.
* Um conjunto de servidores (pelo menos um) que estão a ser geridos pelo Operations Manager e o envio de dados no mapa do serviço. Servidores Windows e Linux são suportadas.
* Um principal de serviço com acesso à subscrição do Azure que estão associado com a área de trabalho do Log Analytics. Para obter mais informações, aceda a [criar um principal de serviço](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Instalar o pacote de gestão do mapa de serviço
Ativar a integração entre o Operations Manager e o mapa de serviço através da importação de agrupamento de pacote de gestão Microsoft.SystemCenter.ServiceMap (Microsoft.SystemCenter.ServiceMap.mpb). Pode baixar o agrupamento de pacote de gestão do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=55763). O pacote contém os seguintes pacotes de gestão:
* Vistas de aplicativo de mapa de serviço da Microsoft
* Mapa de serviço do Microsoft System Center interno
* Substituições de mapa de serviço do Microsoft System Center
* Mapa de serviço do Microsoft System Center

## <a name="configure-the-service-map-integration"></a>Configurar a integração de mapa de serviço
Depois de instalar o pacote de gestão do mapa de serviço, um novo nó **mapa de serviço**, é apresentado em **Operations Management Suite** no **administração** painel.

>[!NOTE]
>[Operations Management Suite foi uma coleção de serviços](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) incluídos que o Log Analytics, que agora faz parte dos [do Azure Monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md).

Para configurar a integração de mapa de serviço, faça o seguinte:

1. Para abrir o Assistente de configuração, na **descrição geral do mapa de serviço** painel, clique em **adicionar área de trabalho**.  

    ![Painel de descrição geral do mapa de serviço](media/service-map-scom/scom-configuration.png)

2. Na **configuração da ligação** janela, introduza o nome do inquilino ou o ID, o ID da aplicação (também conhecido como o nome de utilizador ou ID de cliente) e a palavra-passe do principal de serviço e, em seguida, clique em **próxima**. Para obter mais informações, vá para criar um principal de serviço.

    ![A janela de configuração da ligação](media/service-map-scom/scom-config-spn.png)

3. Na **seleção de subscrição** janela, selecione a subscrição do Azure, o grupo de recursos do Azure (aquele que contém a área de trabalho do Log Analytics) e a área de trabalho do Log Analytics e, em seguida, clique em **próxima**.

    ![A área de trabalho de configuração do Operations Manager](media/service-map-scom/scom-config-workspace.png)

4. Na **seleção de grupo de computadores** janela, escolher os grupos de máquina de mapa de serviço que pretende sincronizar para o Operations Manager. Clique em **Adicionar/remover grupos de máquinas**, selecione os grupos da lista de **grupos de máquinas disponíveis**e clique em **Add**.  Quando tiver terminado de selecionar os grupos, clique em **Ok** para concluir.

    ![Os grupos de máquina de configuração do Gestor de operações](media/service-map-scom/scom-config-machine-groups.png)

5. Na **seleção de servidor** janela, configurar o grupo de servidores de mapa de serviço com os servidores que pretende sincronizar entre o Operations Manager e o mapa de serviço. Clique em **Adicionar/remover servidores**.   

    Para a integração criar um diagrama de aplicação distribuída para um servidor, tem de ser o servidor:

    * Gerido pelo Operations Manager
    * Gerido pelo mapa de serviço
    * Listado no grupo de servidores de mapa de serviço

    ![O grupo de configuração do Operations Manager](media/service-map-scom/scom-config-group.png)

6. Opcional: Selecione o agrupamento de recursos do servidor de gestão para comunicar com o Log Analytics e, em seguida, clique em **adicionar área de trabalho**.

    ![O agrupamento de recursos de configuração do Gestor de operações](media/service-map-scom/scom-config-pool.png)

    Poderá demorar um minuto para configurar e registar a área de trabalho do Log Analytics. Depois de estar configurada, o Operations Manager inicia a primeira sincronização de mapa de serviço.

    ![O agrupamento de recursos de configuração do Gestor de operações](media/service-map-scom/scom-config-success.png)


## <a name="monitor-service-map"></a>Mapa de serviço do monitor
Depois que estiver ligada a área de trabalho do Log Analytics, uma nova pasta, o mapa de serviço, é apresentada na **monitorização** painel da consola do Operations Manager.

![O painel de monitorização do Operations Manager](media/service-map-scom/scom-monitoring.png)

A pasta de mapa de serviço tem quatro nós:
* **Alertas ativos**: Apresenta uma lista de todos os alertas ativos sobre a comunicação entre o Operations Manager e o mapa de serviço.  Tenha em atenção que estes alertas não são do Log Analytics alerta a ser sincronizado com o Operations Manager.

* **Servidores**: Apresenta uma lista de servidores monitorizados que estão configurados para sincronizar a partir do mapa de serviço.

    ![O painel de servidores de monitorização do Operations Manager](media/service-map-scom/scom-monitoring-servers.png)

* **As vistas de dependência do grupo de máquina**: Apresenta uma lista de todos os grupos de computadores que são sincronizados a partir de mapa de serviço. Pode clicar em qualquer grupo para ver o diagrama de aplicação distribuída.

    ![O diagrama de aplicação distribuída do Operations Manager](media/service-map-scom/scom-group-dad.png)

* **Modos de exibição de dependência de servidor**: Apresenta uma lista de todos os servidores que são sincronizados a partir de mapa de serviço. Pode clicar em qualquer servidor para ver o diagrama de aplicação distribuída.

    ![O diagrama de aplicação distribuída do Operations Manager](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Editar ou eliminar a área de trabalho
Pode editar ou eliminar a área de trabalho configurada através da **descrição geral do mapa de serviço** painel (**administração** painel > **Operations Management Suite**  >  **Mapa de serviço**).

>[!NOTE]
>[Operations Management Suite foi uma coleção de serviços](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) incluídos que o Log Analytics, que agora faz parte dos [do Azure Monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md).

Pode configurar apenas uma área de trabalho do Log Analytics por agora.

![O painel da área de trabalho do Operations Manager editar](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Configurar regras e substituições
Uma regra _Microsoft.SystemCenter.ServiceMapImport.Rule_, é criado para obter periodicamente informações de mapa de serviço. Para alterar os tempos de sincronização, pode configurar substituições da regra (**Authoring** painel > **regras** > **Microsoft.SystemCenter.ServiceMapImport.Rule**) .

![A janela de propriedades substituições do Operations Manager](media/service-map-scom/scom-overrides.png)

* **Ativado**: Ativar ou desativar as atualizações automáticas.
* **IntervalMinutes**: Repor o tempo entre as atualizações. O intervalo predefinido é de uma hora. Se pretender sincronizar os mapas de servidor com mais frequência, pode alterar o valor.
* **TimeoutSeconds**: Repor o período de tempo antes do pedido exceder o tempo limite.
* **TimeWindowMinutes**: Repor a janela de tempo para consultar dados. A predefinição é uma janela de 60 minutos. O valor máximo permitido pelo mapa de serviço é 60 minutos.

## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos

O design atual apresenta as seguintes limitações e problemas:
* Só pode ligar a uma única área de trabalho do Log Analytics.
* Embora possa adicionar servidores para o grupo de servidores de mapa de serviço manualmente através da **Authoring** painel, o maps para esses servidores não são sincronizadas imediatamente.  Eles serão sincronizados de mapa de serviço durante o próximo ciclo de sincronização.
* Se fizer alterações para os diagramas de aplicação distribuída criada pelo pacote de gestão, essas alterações provavelmente serão substituídas na próxima sincronização com o mapa de serviço.

## <a name="create-a-service-principal"></a>Criar um principal de serviço
Para obter documentação oficial do Azure sobre a criação de um serviço principal, consulte:
* [Criar um principal de serviço com o PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Criar um principal de serviço com a CLI do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Criar um principal de serviço com o portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Comentários
Tem comentários para nós sobre o mapa de serviço ou esta documentação? Visite nosso [página do Uservoice](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), onde pode sugerir funcionalidades ou votar em sugestões existentes.
