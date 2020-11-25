---
title: Controlo remoto do controlo de acesso a dados - Azure / Microsoft Docs
description: Este artigo fornece informações sobre como pode configurar os controlos de acesso para o explorador de telemetria Time Series Insights no acelerador de solução de monitorização remota
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005967"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>Configure os controlos de acesso para o explorador de telemetria Time Series Insights

Este artigo fornece informações sobre como configurar os controlos de acesso para o explorador de Insights de Séries Temporais no acelerador de solução de monitorização remota. Para permitir que os utilizadores do acelerador de solução acedam ao explorador Time Series Insights, é necessário conceder a cada utilizador acesso aos dados.

As políticas de acesso a dados concedem permissões para emitir consultas de dados, manipular dados de referência no ambiente e partilhar consultas guardadas e perspetivas associadas ao ambiente.

## <a name="grant-data-access"></a>Conceder acesso a dados

Siga estas medidas para conceder acesso aos dados a um utilizadores principal:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Localize o ambiente de Insights de Séries Tempo. Série **de tempo na** caixa de **pesquisa.** Selecione **Time Series Ambiente** nos resultados da pesquisa. 

3. Selecione o seu ambiente do Time Series Insights na lista.

4. Selecione **Políticas de Acesso a Dados** e, em seguida, selecione + **Adicionar**.
    ![Gerir a origem do Time Series Insights - ambiente](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. **Selecione Selecione utilizador**.  Procure o nome de utilizador ou o endereço de e-mail para localizar o utilizador que pretende adicionar. Clique **em Selecionar** para confirmar a seleção. 

    ![Gerir a origem do Time Series Insights - adicionar](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. **Selecione Função selecionada**. Escolha a função de acesso adequada para o utilizador:
   - Selecione **O Contribuinte** se pretender permitir que o utilizador altere dados de referência e partilhe consultas e perspetivas guardadas com outros utilizadores do ambiente. 
   - Caso contrário, selecione **Reader** para permitir dados de consulta do utilizador no ambiente e salvar consultas pessoais (não partilhadas) no ambiente.

     Selecione **Ok** para confirmar a escolha de função.

     ![Gerir a origem do Time Series Insights - selecionar utilizador](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. Selecione **Ok** na página **'Escolha's Role' (Escolha a função de utilizador).**

    ![Gerir a origem do Time Series Insights - selecionar função](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. A página Políticas de Acesso a **Dados** lista os utilizadores e as funções para cada utilizador.

    ![Gerir a origem do Time Series Insights - resultados](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu como os controlos de acesso são concedidos para o explorador de Insights de Séries Temporais no acelerador de solução de monitorização remota.

Para obter mais informações conceptuais sobre o acelerador de solução de monitorização remota, consulte [a arquitetura de monitorização remota](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Para obter mais informações sobre a personalização da solução de Monitorização Remota, consulte [Personalizar e redistribuir um microserviço](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->