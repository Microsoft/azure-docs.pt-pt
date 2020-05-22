---
title: Integrar a Gestão de Atualização de Automação Azure com o Gestor de Configuração do Ponto Final do Windows
description: Este artigo diz como configurar o Microsoft Endpoint Configuration Manager com a Update Management para implementar atualizações de software para clientes gestores.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 798cf3a7a8b94a06331e2f8e27c143b4182b718e
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83735842"
---
# <a name="integrate-update-management-with-windows-endpoint-configuration-manager"></a>Integrar a Gestão de Atualizações com o Gestor de Configuração do Ponto Final do Windows

Os clientes que investiram no Microsoft Endpoint Configuration Manager para gerir Computadores, servidores e dispositivos móveis também dependem da sua força e maturidade na gestão de atualizações de software como parte do seu ciclo de gestão de atualizações de software (SUM).

Pode reportar e atualizar servidores do Windows geridos através da criação e pré-realização de implementações de atualizações de software no Windows Endpoint Configuration Manager e obter o estado detalhado das implementações de atualizações concluídas utilizando a Atualização de [Gestão](automation-update-management.md). Se utilizar o Gestor de Configuração do Windows Endpoint para o relatório de conformidade da atualização, mas não para gerir as implementações de atualizações com os seus servidores Windows, pode continuar a reportar ao gestor de configuração enquanto as atualizações de segurança são geridas com a Azure Automation Update Management.

## <a name="prerequisites"></a>Pré-requisitos

* Tem de adicionar a [Azure Automation Update Management](automation-update-management.md) à sua conta Automation.
* Os servidores Windows atualmente geridos pelo ambiente do Gestor de Configuração do Windows Endpoint também precisam de reportar ao espaço de trabalho do Log Analytics que também tem a Atualização ativada.
* Esta funcionalidade está ativada na versão atual do Windows Endpoint Configuration Manager 1606 e superior. Para integrar o site central da administração do Windows Endpoint, ou um site primário autónomo com registos do Monitor Azure e coleções de importação, reveja o [Connect Configuration Manager para os registos do Monitor Azure.](../azure-monitor/platform/collect-sccm.md)  
* Os agentes do Windows devem ser configurados para comunicar com um servidor do Windows Server Update Services (WSUS) ou ter acesso ao Microsoft Update se não receberem atualizações de segurança do Windows Endpoint Configuration Manager.

A forma como gere os clientes hospedados no Azure IaaS com o seu ambiente de Gestor de Configuração do Windows Endpoint existente depende principalmente da ligação que tem entre os datacenters do Azure e a sua infraestrutura. Esta ligação afeta quaisquer alterações de design que possa ter de fazer à sua infraestrutura de Configuração de Ponto final do Windows e custos relacionados para suportar as alterações necessárias. Para compreender quais as considerações de planeamento que precisa de avaliar antes de continuar, reveja [Configuration Manager no Azure - Perguntas mais frequentes](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="manage-software-updates-from-windows-endpoint-configuration-manager"></a>Gerir atualizações de software do Windows Endpoint Configuration Manager

Execute os seguintes passos se continuar a gerir as implementações de atualizações do Windows Endpoint Configuration Manager. O Azure Automation liga-se ao Windows Endpoint Configuration Manager para aplicar atualizações aos computadores clientes ligados ao seu espaço de trabalho log Analytics. O conteúdo da atualização está disponível a partir do cache do computador do cliente como se a implementação fosse gerida pelo Windows Endpoint Configuration Manager.

1. Crie uma implementação de atualização de software a partir do site de alto nível na hierarquia do Gestor de Configuração do Windows Endpoint utilizando o processo descrito nas atualizações de [software de implementação](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates). A única definição que tem de ser configurada de forma diferente de uma implementação padrão é a opção **Não instalar atualizações de software** para controlar o comportamento de transferência do pacote de implementação. Este comportamento é gerido na Gestão de Atualização, criando uma implementação de atualização programada no próximo passo.

1. Na Automatização Azure, selecione Gestão de **Atualizações.** Crie uma nova implementação seguindo os passos descritos na Criação de [uma Implementação](automation-tutorial-update-management.md#schedule-an-update-deployment) de Atualizações e selecione **grupos importados** no dropdown **do Tipo** para selecionar a coleção apropriada do Gestor de Configuração do Ponto Final do Windows. Tenha em mente os seguintes pontos importantes: a. Se for definida uma janela de manutenção na coleção de dispositivos selecionado do Windows Endpoint Configuration Manager, os membros da coleção honram-na em vez da definição de **Duração** definida na implementação programada.
    b. Os membros da recolha do alvo devem ter uma ligação à Internet (diretamente, através de um servidor proxy ou através do gateway Log Analytics).

Após completar a implementação da atualização através do Azure Automation, os computadores-alvo que são membros do grupo de computador selecionado sairá atualizações no momento programado a partir da sua cache de cliente local. Pode [ver o estado de implementação da atualização](automation-tutorial-update-management.md#view-results-of-an-update-deployment) para monitorizar os resultados da implementação.

## <a name="manage-software-updates-from-azure-automation"></a>Gerir atualizações de software da Azure Automation

Para gerir as atualizações para VMs do Windows Server que são clientes do Windows Endpoint Configuration Manager, é necessário configurar a política do cliente para desativar a funcionalidade de Gestão de Atualizações de Software para todos os clientes geridos pela Update Management. Por predefinição, as definições de cliente visam todos os dispositivos na hierarquia. Para obter mais informações sobre esta definição de política e como configurá-la, reveja [como configurar as definições](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings)do cliente no Gestor de Configuração .

Depois de realizar esta alteração de configuração, cria uma nova implementação seguindo os passos descritos na Criação de [uma Implementação](automation-tutorial-update-management.md#schedule-an-update-deployment) de Atualização e selecione **grupos importados** no **drop-down do Tipo** para selecionar a coleção de Configuração de Ponto final do Windows apropriada.

## <a name="next-steps"></a>Passos seguintes

[Agendar uma implementação de atualizações](automation-tutorial-update-management.md#schedule-an-update-deployment)