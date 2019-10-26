---
title: Monitorar hubs de superfície com Azure Monitor | Microsoft Docs
description: Use a solução Surface Hub para acompanhar a integridade dos hubs de superfície e entender como eles estão sendo usados.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2018
ms.openlocfilehash: 7ced5f678b9f8b2d4aa073a984276f41b8b7c4b9
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900623"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>Monitorar os hubs de superfície com Azure Monitor para controlar sua integridade

![Símbolo de Surface Hub](./media/surface-hubs/surface-hub-symbol.png)

Este artigo descreve como você pode usar a solução Surface Hub no Azure Monitor para monitorar dispositivos Microsoft Surface Hub. A solução ajuda a acompanhar a integridade de seus hubs de superfície, bem como a entender como eles estão sendo usados.

Cada Surface Hub tem o Microsoft Monitoring Agent instalado. Seu por meio do agente que você pode enviar dados de seu Surface Hub para um espaço de trabalho Log Analytics no Azure Monitor. Os arquivos de log são lidos em seus hubs de superfície e, em seguida, são enviados para Azure Monitor. Problemas como servidores offline, o calendário não está sincronizando ou se a conta do dispositivo não puder fazer logon no Skype são mostradas no painel Surface Hub no Azure Monitor. Usando os dados no painel, você pode identificar os dispositivos que não estão em execução ou que estão tendo outros problemas e, potencialmente, aplicar correções para os problemas detectados.

## <a name="install-and-configure-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes informações para instalar e configurar a solução. Para gerenciar seus Surface hubs no Azure Monitor, você precisará do seguinte:

* Um nível de [assinatura log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) que dará suporte ao número de dispositivos que você deseja monitorar. Log Analytics preços varia de acordo com o número de dispositivos registrados e a quantidade de dados que ele processa. Você desejará levar isso em consideração ao planejar sua distribuição de Surface Hub.

Em seguida, você adicionará um espaço de trabalho Log Analytics existente ou criará um novo. Instruções detalhadas para usar qualquer um dos métodos estão em [criar um log Analytics espaço de trabalho no portal do Azure](../learn/quick-create-workspace.md). Depois que o espaço de trabalho do Log Analytics estiver configurado, há duas maneiras de registrar seus dispositivos Surface Hub:

* Automaticamente por meio do Intune
* Manualmente por meio **das configurações** em seu dispositivo Surface Hub.

## <a name="set-up-monitoring"></a>Configurar o monitoramento
Você pode monitorar a integridade e a atividade de seu Surface Hub usando Azure Monitor. Você pode registrar o Surface Hub usando o Intune ou localmente usando **as configurações** na Surface Hub.

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>Conectar os hubs de superfície a Azure Monitor por meio do Intune
Você precisará da ID do espaço de trabalho e da chave do espaço de trabalho para o Log Analytics espaço de trabalho que gerenciará os hubs de superfície. Você pode obtê-los nas configurações do espaço de trabalho no portal do Azure.

O Intune é um produto da Microsoft que permite que você gerencie centralmente as definições de configuração de Log Analytics espaço de trabalho que são aplicadas a um ou mais dos seus dispositivos. Siga estas etapas para configurar seus dispositivos por meio do Intune:

1. Entre no Intune.
2. Navegue até **configurações** > **fontes conectadas**.
3. Crie ou edite uma política com base no modelo de Surface Hub.
4. Navegue até a seção insights operacionais do Azure da política e adicione a ID do *espaço* de trabalho log Analytics e a *chave do espaço de trabalho* à política.
5. Salve a política.
6. Associe a política ao grupo de dispositivos apropriado.

   ![Política do Intune](./media/surface-hubs/intune.png)

Em seguida, o Intune sincroniza as configurações de Log Analytics com os dispositivos no grupo de destino, registrando-as em seu espaço de trabalho Log Analytics.

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>Conectar os hubs de superfície a Azure Monitor usando o aplicativo de configurações
Você precisará da ID do espaço de trabalho e da chave do espaço de trabalho para o Log Analytics espaço de trabalho que gerenciará os hubs de superfície. Você pode obter as configurações do espaço de trabalho Log Analytics no portal do Azure.

Se você não usar o Intune para gerenciar seu ambiente, poderá registrar dispositivos manualmente por meio de **configurações** em cada Surface Hub:

1. Em seu Surface Hub, abra **configurações**.
2. Insira as credenciais de administrador do dispositivo quando solicitado.
3. Clique **neste dispositivo**e, em **monitoramento**, clique em **definir configurações de log Analytics**.
4. Selecione **habilitar monitoramento**.
5. Na caixa de diálogo Configurações de Log Analytics, digite a **ID do espaço de trabalho** log Analytics e digite a chave do espaço de **trabalho**.  
   configurações de ![](./media/surface-hubs/settings.png)
6. Clique em **OK** para concluir a configuração.

Uma confirmação é exibida informando se a configuração foi aplicada com êxito ao dispositivo. Se foi, será exibida uma mensagem informando que o agente se conectou com êxito ao Azure Monitor. Em seguida, o dispositivo começa a enviar dados para Azure Monitor onde você pode exibir e agir sobre ele.

## <a name="monitor-surface-hubs"></a>Monitorar hubs de superfície
Monitorar seus hubs de superfície usando Azure Monitor é muito parecido com o monitoramento de qualquer outro dispositivo registrado.

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Quando você clica no bloco Surface Hub, a integridade do dispositivo é exibida.

   ![Painel do Surface Hub](./media/surface-hubs/surface-hub-dashboard.png)

Você pode criar [alertas](../platform/alerts-overview.md) com base em pesquisas de log existentes ou personalizadas. Usando os dados Azure Monitor coletas de seus Surface hubs, você pode procurar problemas e alertar sobre as condições que você define para seus dispositivos.

## <a name="next-steps"></a>Passos seguintes
* Use [consultas de log em Azure monitor](../log-query/log-query-overview.md) para exibir dados detalhados de Surface Hub.
* Crie [alertas](../platform/alerts-overview.md) para notificá-lo quando ocorrerem problemas com os hubs de superfície.
