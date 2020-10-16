---
title: Integre a gestão de atualização de automação do Azure com o Gestor de Configuração de Pontos Finais da Microsoft
description: Este artigo diz como configurar o Gestor de Configuração de Pontos Finais do Microsoft com a Gestão de Atualização para implementar atualizações de software para clientes gestores.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 09c8ef818428157c7de8c3a87bcce8a7b80e62ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88245915"
---
# <a name="integrate-update-management-with-microsoft-endpoint-configuration-manager"></a>Integre a gestão de atualização com o Gestor de Configuração de Endpoint da Microsoft

Os clientes que investiram no Microsoft Endpoint Configuration Manager para gerir PCs, servidores e dispositivos móveis também dependem da sua força e maturidade na gestão de atualizações de software como parte do seu ciclo de gestão de atualização de software (SUM).

Pode reportar e atualizar servidores geridos do Windows criando e pré-encenar as implementações de atualizações de software no Microsoft Endpoint Configuration Manager e obter o estado detalhado das implementações de atualizações concluídas utilizando [a Gestão de Atualização](update-mgmt-overview.md). Se utilizar o Microsoft Endpoint Configuration Manager para atualizar relatórios de conformidade, mas não para gerir as implementações da atualização com os seus servidores Windows, pode continuar a reportar ao Microsoft Endpoint Configuration Manager enquanto as atualizações de segurança são geridas com a Azure Automation Update Management.

>[!NOTE]
>Embora a Update Management suporte a avaliação e correção de atualizações do Windows Server 2008 R2, não suporta clientes geridos pelo Microsoft Endpoint Configuration Manager que executam este sistema operativo.

## <a name="prerequisites"></a>Pré-requisitos

* Tem de ter [a Azure Automation Update Management](update-mgmt-overview.md) adicionada à sua conta de Automação.
* Os servidores do Windows atualmente geridos pelo seu ambiente de Gestor de Configuração de Endpoint do Microsoft também precisam de reportar ao espaço de trabalho do Log Analytics que também tem a Gestão de Atualização ativada.
* Esta funcionalidade está ativada na versão atual do Microsoft Endpoint Configuration Manager 1606 e superior. Para integrar o site da administração central do Microsoft Endpoint Configuration Manager ou um site primário autónomo com registos e coleções de importação do Monitor Azure, reveja [os registos do Connect Configuration Manager para os registos do Azure Monitor](../../azure-monitor/platform/collect-sccm.md).  
* Os agentes do Windows devem estar configurados para comunicar com um servidor de Serviços de Atualização do Windows Server (WSUS) ou ter acesso ao Microsoft Update se não receberem atualizações de segurança do Gestor de Configuração do Microsoft Endpoint.

A forma como gere os clientes hospedados no Azure IaaS com o ambiente existente do Microsoft Endpoint Configuration Manager depende principalmente da ligação que tem entre os datacenters Azure e a sua infraestrutura. Esta ligação afeta quaisquer alterações de design que possa ter de fazer à infraestrutura do Microsoft Endpoint Configuration Manager e custos relacionados para suportar as alterações necessárias. Para compreender quais as considerações de planeamento que precisa de avaliar antes de continuar, reveja [Configuration Manager no Azure - Perguntas mais frequentes](/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="manage-software-updates-from-microsoft-endpoint-configuration-manager"></a>Gerir atualizações de software a partir do Microsoft Endpoint Configuration Manager

Execute os seguintes passos se continuar a gerir as implementações de atualização do Microsoft Endpoint Configuration Manager. O Azure Automation conecta-se ao Microsoft Endpoint Configuration Manager para aplicar atualizações aos computadores clientes ligados ao seu espaço de trabalho Log Analytics. O conteúdo da atualização está disponível a partir da cache do computador do cliente como se a implementação fosse gerida pelo Microsoft Endpoint Configuration Manager.

1. Crie uma implementação de atualização de software a partir do site de nível superior na sua hierarquia microsoft Endpoint Configuration Manager utilizando o processo descrito nas [atualizações de software implementar](/configmgr/sum/deploy-use/deploy-software-updates). A única definição que tem de ser configurada de forma diferente de uma implementação padrão é a opção **Não instalar atualizações de software** para controlar o comportamento de transferência do pacote de implementação. Este comportamento é gerido na Gestão de Atualização, criando uma implementação de atualização programada no passo seguinte.

2. Na Azure Automation, selecione **Update Management**. Crie uma nova implementação seguindo os passos descritos na [Criação de uma Implementação de Atualização](update-mgmt-deploy-updates.md#schedule-an-update-deployment) e selecione **grupos importados** no **dropdown tipo** para selecionar a coleção adequada do Microsoft Endpoint Configuration Manager. Tenha em consideração os seguintes pontos importantes:

    a. Se uma janela de manutenção for definida na coleção selecionada do Microsoft Endpoint Configuration Manager, os membros da coleção honram-na em vez da definição **de Duração** definida na implementação programada.

    b. Os membros da coleção de alvos devem ter uma ligação à Internet (diretamente, através de um servidor proxy ou através do gateway Log Analytics).

Depois de completar a implementação da atualização através da Azure Automation, os computadores-alvo que são membros do grupo informático selecionado irão instalar atualizações na hora programada a partir da cache do cliente local. Pode [ver o estado de implementação da atualização](update-mgmt-deploy-updates.md#check-deployment-status) para monitorizar os resultados da implementação.

## <a name="manage-software-updates-from-azure-automation"></a>Gerir atualizações de software da Azure Automation

Para gerir atualizações para VMs do Windows Server que são clientes do Microsoft Endpoint Configuration Manager, é necessário configurar a política do cliente para desativar a funcionalidade de Gestão de Atualização de Software para todos os clientes geridos pela Update Management. Por predefinição, as definições de cliente visam todos os dispositivos na hierarquia. Para obter mais informações sobre esta definição de política e como configurá-la, [reveja como configurar as definições](/configmgr/core/clients/deploy/configure-client-settings)do cliente no Gestor de Configuração .

Depois de executar esta alteração de configuração, cria uma nova implementação seguindo os passos descritos na [Criação de uma Implementação de Atualização](update-mgmt-deploy-updates.md#schedule-an-update-deployment) e seleciona **grupos importados** no **drop-down tipo** para selecionar a coleção adequada do Gestor de Configuração de Pontos finais do Microsoft.

## <a name="next-steps"></a>Passos seguintes

Para configurar um calendário de integração, consulte [Agendar uma implementação de atualização](update-mgmt-deploy-updates.md#schedule-an-update-deployment).
