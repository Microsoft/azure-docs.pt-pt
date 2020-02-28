---
title: Monitor Surface Hubs com Monitor Azure [ Monitor Azul ] Microsoft Docs
description: Utilize a solução Surface Hub para acompanhar a saúde dos seus Surface Hubs e compreenda como estão a ser utilizados.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: 7999735bf2d182b2811d01172adcfc89cba27dc8
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77662506"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>Monitor Surface Hubs com Monitor Azure para acompanhar a sua saúde

![Símbolo do Hub de Superfície](./media/surface-hubs/surface-hub-symbol.png)

Este artigo descreve como pode utilizar a solução Surface Hub no Monitor Azure para monitorizar os dispositivos Microsoft Surface Hub. A solução ajuda-o a rastrear a saúde dos seus Surface Hubs, bem como a compreender como estão a ser utilizados.

Cada Surface Hub tem o Microsoft Monitoring Agent instalado. É através do agente que pode enviar dados do seu Surface Hub para um espaço de trabalho de Log Analytics no Monitor Azure. Os ficheiros de registo são lidos a partir dos seus Surface Hubs e são depois enviados para o Monitor Azure. Problemas como o de offline dos servidores, o calendário não sincronizado, ou se a conta do dispositivo não conseguir entrar no Skype são mostrados no painel do Surface Hub no Monitor Azure. Ao utilizar os dados no painel de instrumentos, pode identificar dispositivos que não estejam a funcionar, ou que estejam a ter outros problemas, e potencialmente aplicar correções para os problemas detetados.

## <a name="install-and-configure-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes informações para instalar e configurar a solução. Para gerir os seus Surface Hubs no Monitor Azure, vai precisar do seguinte:

* Um nível de [subscrição do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) que irá suportar o número de dispositivos que pretende monitorizar. Os preços do Log Analytics variam consoante o número de dispositivos inscritos e quantos dados processa. Vai querer levar isto em consideração ao planear o lançamento do Surface Hub.

Em seguida, irá adicionar um espaço de trabalho log analytics existente ou criar um novo. Instruções detalhadas para a utilização de qualquer um dos métodos estão na [Create a Log Analytics espaço de trabalho no portal Azure](../learn/quick-create-workspace.md). Uma vez configurado o espaço de trabalho do Log Analytics, existem duas formas de inscrever os seus dispositivos Surface Hub:

* Automaticamente através de Intune
* Manualmente através **de Definições** no seu dispositivo Surface Hub.

## <a name="set-up-monitoring"></a>Configurar a monitorização
Pode monitorizar a saúde e a atividade do seu Surface Hub utilizando o Monitor Azure. Pode inscrever o Surface Hub utilizando intune, ou localmente, utilizando **definições** no Surface Hub.

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>Ligue os Surface Hubs ao Monitor Azure através do Intune
Você precisará do ID do espaço de trabalho e da chave do espaço de trabalho para o espaço de trabalho Log Analytics que irá gerir os seus Surface Hubs. Pode obtê-los a partir das definições do espaço de trabalho no portal Azure.

Intune é um produto da Microsoft que permite gerir centralmente as definições de configuração do espaço de trabalho Log Analytics que são aplicadas a um ou mais dos seus dispositivos. Siga estes passos para configurar os seus dispositivos através do Intune:

1. Inscreva-se no Intune.
2. Navegar para **Definições** > **Fontes Conectadas**.
3. Crie ou edite uma política baseada no modelo Surface Hub.
4. Navegue na secção Azure Operational Insights da política e adicione a id do espaço de *trabalho* de Log Analytics e *a chave workspace* à política.
5. Guarde a política.
6. Associe a política com o grupo adequado de dispositivos.

   ![Política do Intune](./media/surface-hubs/intune.png)

Intone então as definições de Log Analytics com os dispositivos do grupo alvo, matriculando-as no seu espaço de trabalho Log Analytics.

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>Ligar Surface Hubs ao Monitor Azure utilizando a aplicação Definições
Você precisará do ID do espaço de trabalho e da chave do espaço de trabalho para o espaço de trabalho Log Analytics que irá gerir os seus Surface Hubs. Pode obtê-los a partir das definições para o espaço de trabalho Log Analytics no portal Azure.

Se não utilizar o Intune para gerir o seu ambiente, pode inscrever os dispositivos manualmente através de **Definições** em cada Surface Hub:

1. A partir do surface hub, abra **as definições.**
2. Introduza as credenciais de administração do dispositivo quando solicitado.
3. Clique **neste dispositivo**e na monitorização sob **monitorização,** clique em **Configurar definições de análise**de registo .
4. Selecione Ativar a **monitorização**.
5. No diálogo de definições de Log Analytics, escreva o ID do **espaço de trabalho** de Log Analytics e escreva a Chave **workspace**.  
   ![definições](./media/surface-hubs/settings.png)
6. Clique **em OK** para completar a configuração.

Uma confirmação aparece a dizer-lhe se a configuração foi ou não aplicada com sucesso ao dispositivo. Se fosse, aparece uma mensagem a indicar que o agente ligou-se com sucesso ao Monitor Azure. Em seguida, o dispositivo começa a enviar dados para o Monitor Azure onde pode ver e agir sobre ele.

## <a name="monitor-surface-hubs"></a>Monitor Surface Hubs
Monitorizar os seus Surface Hubs utilizando o Monitor Azure é muito parecido com a monitorização de quaisquer outros dispositivos matriculados.

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Quando clica no azulejo Surface Hub, a saúde do seu dispositivo é exibida.

   ![Painel de instrumentos do Surface Hub](./media/surface-hubs/surface-hub-dashboard.png)

Pode criar [alertas](../platform/alerts-overview.md) com base em pesquisas de registo existentes ou personalizadas. Utilizando os dados que o Azure Monitor recolhe dos seus Surface Hubs, pode procurar problemas e alertar sobre as condições que define para os seus dispositivos.

## <a name="next-steps"></a>Passos seguintes
* Utilize consultas de [registo no Monitor Azure](../log-query/log-query-overview.md) para visualizar dados detalhados do Surface Hub.
* Crie [alertas](../platform/alerts-overview.md) para notificá-lo quando ocorrerem problemas com os seus Surface Hubs.
