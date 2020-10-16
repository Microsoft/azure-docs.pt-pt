---
title: Monitor Surface Hubs com Monitor Azure / Microsoft Docs
description: Utilize a solução Surface Hub para acompanhar a saúde dos seus Surface Hubs e compreenda como estão a ser utilizados.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: 7e3b0f92770b48ef5163846e67940efe80fb669a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90085353"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>Monitorize os Hubs de Superfície com monitor Azure para acompanhar a sua saúde

![Símbolo do cubo de superfície](./media/surface-hubs/surface-hub-symbol.png)

Este artigo descreve como pode utilizar a solução Surface Hub no Azure Monitor para monitorizar os dispositivos do Microsoft Surface Hub. A solução ajuda-o a acompanhar a saúde dos seus Surface Hubs, bem como a compreender como estão a ser utilizados.

Cada Surface Hub tem o Agente de Monitorização da Microsoft instalado. É através do agente que pode enviar dados do seu Surface Hub para um espaço de trabalho Log Analytics no Azure Monitor. Os ficheiros de registo são lidos a partir dos seus Surface Hubs e são depois enviados para o Azure Monitor. Problemas como o offline dos servidores, o calendário não sincronizado, ou se a conta do dispositivo não conseguir entrar no Skype são mostrados no painel de instrumentos Do Surface Hub no Azure Monitor. Ao utilizar os dados no painel de instrumentos, pode identificar dispositivos que não estão em execução, ou que estão a ter outros problemas, e potencialmente aplicar correções para os problemas detetados.

## <a name="install-and-configure-the-solution"></a>Instale e configuure a solução
Utilize as seguintes informações para instalar e configurar a solução. Para gerir os seus Surface Hubs no Azure Monitor, necessitará do seguinte:

* Um nível [de subscrição do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) que irá suportar o número de dispositivos que pretende monitorizar. Os preços do Log Analytics variam consoante o número de dispositivos estão matriculados e quantos dados processa. Você vai querer ter isso em consideração ao planear o lançamento do Surface Hub.

Em seguida, irá adicionar um espaço de trabalho log analytics existente ou criar um novo. Instruções detalhadas para a utilização de qualquer um dos métodos estão no [Criar um espaço de trabalho Log Analytics no portal Azure](../learn/quick-create-workspace.md). Uma vez configurado o espaço de trabalho Log Analytics, existem duas formas de inscrever os seus dispositivos Surface Hub:

* Automaticamente através do Intune
* Manualmente através **de Definições** no seu dispositivo Surface Hub.

## <a name="set-up-monitoring"></a>Configurar a monitorização
Pode monitorizar a saúde e a atividade do seu Surface Hub utilizando o Monitor Azure. Pode inscrever o Surface Hub utilizando o Intune, ou localmente utilizando **Definições** no Surface Hub.

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>Ligue os Hubs de Superfície ao Monitor Azure através do Intune
Você precisará do ID do espaço de trabalho e da chave de espaço de trabalho para o espaço de trabalho Log Analytics que irá gerir os seus Surface Hubs. Pode obtê-las a partir das definições do espaço de trabalho no portal Azure.

Intune é um produto da Microsoft que lhe permite gerir centralmente as definições de configuração do espaço de trabalho do Log Analytics que são aplicadas a um ou mais dos seus dispositivos. Siga estes passos para configurar os seus dispositivos através do Intune:

1. Inscreva-se no Intune.
2. Navegar para **Definições**  >  **Fontes Ligadas**.
3. Criar ou editar uma política baseada no modelo Surface Hub.
4. Navegue para a secção de Insights Operacionais Azure da política e adicione a chave *de espaço de trabalho* do Log Analytics e do espaço de *trabalho* à política.
5. Guarde a política.
6. Associe a política ao grupo de dispositivos apropriado.

   ![Política do Intune](./media/surface-hubs/intune.png)

Intune sincroniza então as definições de Log Analytics com os dispositivos do grupo alvo, inscreveu-os no seu espaço de trabalho Log Analytics.

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>Conecte os Hubs de Superfície ao Monitor Azure utilizando a aplicação Definições
Você precisará do ID do espaço de trabalho e da chave de espaço de trabalho para o espaço de trabalho Log Analytics que irá gerir os seus Surface Hubs. Pode obtê-las a partir das definições para o espaço de trabalho Log Analytics no portal Azure.

Se não utilizar o Intune para gerir o seu ambiente, pode inscrever os dispositivos manualmente através **de Definições** em cada Surface Hub:

1. A partir do seu Surface Hub, abra **as definições**.
2. Introduza as credenciais de administração do dispositivo quando solicitado.
3. Clique **neste dispositivo**, e no **seguinte,** clique em Configurações de Análise de **Registos configurados**.
4. **Selecione Ativar a monitorização**.
5. No diálogo de definições de Log Analytics, digite o **ID** do espaço de trabalho do Log Analytics e digite a **chave do espaço de trabalho**.  
   ![O Screenshot mostra as definições do Microsoft Operations Manager Suite com Enable monitoring selecionados e caixas de texto para ID de espaço de trabalho e chave workspace.](./media/surface-hubs/settings.png)
6. Clique **em OK** para completar a configuração.

Aparece uma confirmação que lhe diz se a configuração foi ou não aplicada com sucesso no dispositivo. Se foi, aparece uma mensagem indicando que o agente está ligado com sucesso ao Azure Monitor. Em seguida, o dispositivo começa a enviar dados para o Azure Monitor, onde pode visualizar e agir sobre ele.

## <a name="monitor-surface-hubs"></a>Monitore os cubos de superfície
Monitorizar os seus Surface Hubs utilizando o Azure Monitor é muito parecido com a monitorização de quaisquer outros dispositivos matriculados.

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Quando clicar no azulejo do Surface Hub, a saúde do seu dispositivo é exibida.

   ![Painel de instrumentos do Surface Hub](./media/surface-hubs/surface-hub-dashboard.png)

Pode criar [alertas](../platform/alerts-overview.md) com base em pesquisas de registos existentes ou personalizadas. Utilizando os dados que o Azure Monitor recolhe dos seus Surface Hubs, pode procurar problemas e alertar sobre as condições que define para os seus dispositivos.

## <a name="next-steps"></a>Passos seguintes
* Utilize [consultas de log no Azure Monitor](../log-query/log-query-overview.md) para visualizar dados detalhados do Surface Hub.
* Crie [alertas](../platform/alerts-overview.md) para o notificar quando ocorrerem problemas com os seus Surface Hubs.
