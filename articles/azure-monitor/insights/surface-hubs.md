---
title: Monitorizar os Surface Hubs com o Log Analytics do Azure | Documentos da Microsoft
description: Utilize a solução do Surface Hub para controlar o estado de funcionamento do Surface Hubs e compreender como estão sendo usados.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: a2cedb95469f97b564aaad2baa0ff7c784d67bf8
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52960996"
---
# <a name="monitor-surface-hubs-with-log-analytics-to-track-their-health"></a>Monitorizar o Surface Hubs com o Log Analytics para controlar o respetivo estado de funcionamento

![Símbolo do Surface Hub](./media/surface-hubs/surface-hub-symbol.png)

Este artigo descreve como pode utilizar a solução do Surface Hub no Log Analytics para monitorizar dispositivos Microsoft Surface Hub. O log Analytics ajuda a monitorizar o estado de funcionamento do Surface Hubs, bem como a compreender como estão sendo usados.

Cada Surface Hub tem o Microsoft Monitoring Agent instalado. Seu através do agente que pode enviar dados de sua Surface Hub para o Log Analytics. Ficheiros de registo são lidos a partir do Surface Hubs e são, em seguida, são enviados para o Log Analytics. Problemas como servidores de estado offline, o calendário não sincronizar, ou se a conta do dispositivo for não é possível iniciar sessão no Skype são apresentados no dashboard do Surface Hub no Log Analytics. Ao utilizar os dados no dashboard, pode identificar dispositivos que não estão em execução ou que estão a ter outros problemas e potencialmente aplicar correções para os problemas detetados.

## <a name="install-and-configure-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes informações para instalar e configurar a solução. Para gerir os Hubs de superfície do Log Analytics, precisará do seguinte:

* R [subscrição do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) nível que irá suportar o número de dispositivos que pretende monitorizar. Preços do log Analytics variam consoante o número de dispositivos é inscritos e a quantidade de dados-processos. Vai querer ter isto em consideração ao planejar sua implementação do Surface Hub.

Em seguida, irá adicionar uma área de trabalho do Log Analytics existente ou crie um novo. Encontrará instruções detalhadas para utilizando um dos métodos tenha [criar uma área de trabalho do Log Analytics no portal do Azure](../../azure-monitor/learn/quick-create-workspace.md). Uma vez que a área de trabalho do Log Analytics é configurada, existem duas formas de inscrever os dispositivos Surface Hub:

* Automaticamente através do Intune
* Manualmente através de **definições** no seu dispositivo Surface Hub.

## <a name="set-up-monitoring"></a>Configurar a monitorização
Pode monitorizar o estado de funcionamento e a atividade do seu Hub de superfície de utilizar o Log Analytics. Pode inscrever o Surface Hub com o Intune ou localmente usando **definições** no Surface Hub.

## <a name="connect-surface-hubs-to-log-analytics-through-intune"></a>Ligar os Surface Hubs ao Log Analytics através do Intune
Precisará do ID e chave da área de trabalho para a área de trabalho do Log Analytics que irão gerir o Surface Hubs. Pode aceder das definições da área de trabalho no portal do Azure.

O Intune é um produto da Microsoft que lhe permite gerir centralmente as definições de configuração do Log Analytics que são aplicadas a um ou mais dos seus dispositivos. Siga estes passos para configurar os seus dispositivos através do Intune:

1. Iniciar sessão no Intune.
2. Navegue para **configurações** > **ligado a origens**.
3. Criar ou editar uma política baseada no modelo de Surface Hub.
4. Navegue para a secção de informações operacionais do Azure da política e adicione o Log Analytics *ID da área de trabalho* e *chave de área de trabalho* à política.
5. Guarde a política.
6. Associe a política com o grupo adequado de dispositivos.

   ![Política do Intune](./media/surface-hubs/intune.png)

Em seguida, o Intune sincroniza as definições do Log Analytics com os dispositivos no grupo de destino, inscrevê-los na sua área de trabalho do Log Analytics.

## <a name="connect-surface-hubs-to-log-analytics-using-the-settings-app"></a>Ligar o Surface Hubs ao Log Analytics a utilizar a aplicação de definições
Precisará do ID e chave da área de trabalho para a área de trabalho do Log Analytics que irão gerir o Surface Hubs. Pode aceder das configurações para a área de trabalho do Log Analytics no portal do Azure.

Se não utilizar o Intune para gerir o seu ambiente, pode inscrever dispositivos através do manualmente **definições** em cada Surface Hub:

1. A partir do seu Surface Hub, abra **definições**.
2. Introduza as credenciais de administrador do dispositivo quando lhe for pedido.
3. Clique em **este dispositivo**e o sob **monitorização**, clique em **configurar definições do Log Analytics**.
4. Selecione **ativar a monitorização**.
5. Na caixa de diálogo de configurações do Log Analytics, escreva o Log Analytics **ID da área de trabalho** e escreva o **chave de área de trabalho**.  
   ![settings](./media/surface-hubs/settings.png)
6. Clique em **OK** para concluir a configuração.

É apresentada uma mensagem de confirmação a informá-lo ou não a configuração foi aplicada com êxito no dispositivo. Caso tenha sido, é apresentada uma mensagem a indicar que o agente ligado com êxito para o Log Analytics. O dispositivo, em seguida, começa a enviar dados para o Log Analytics, onde pode ver e agir sobre ele.

## <a name="monitor-surface-hubs"></a>Surface Hubs monitor
Monitorização do Surface Hubs utilizar o Log Analytics é muito parecida com a monitorização de todos os outros dispositivos inscritos.

1. Inicie sessão no Portal do Azure.
2. Navegue para a área de trabalho do Log Analytics e selecione **descrição geral**.
2. Clique no mosaico do Surface Hub.
3. É apresentado o estado de funcionamento do seu dispositivo.

   ![Painel do Surface Hub](./media/surface-hubs/surface-hub-dashboard.png)

Pode criar [alertas](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) com base em pesquisas de registos existentes ou personalizados. Utilizar os dados que do Log Analytics recolhe a partir de seu Surface Hubs, pode procurar problemas e alerta nas condições que definem para os seus dispositivos.

## <a name="next-steps"></a>Passos Seguintes
* Uso [pesquisas de registos no Log Analytics](../../azure-monitor/log-query/log-query-overview.md) para ver os dados detalhados do Surface Hub.
* Crie [alertas](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) para ser notificado quando ocorrer problemas no seu Surface Hubs.
