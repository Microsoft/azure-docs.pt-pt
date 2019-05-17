---
title: Controlo - Azure de acesso de dados de monitorização remota | Documentos da Microsoft
description: Este artigo fornece informações sobre como pode configurar os controlos de acesso para o Explorador do Time Series Insights telemetria do solution Accelerator monitorização remota
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827228"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>Configurar os controlos de acesso para o Explorador de telemetria do Time Series Insights

Este artigo fornece informações sobre como configurar os controlos de acesso para o Explorador do Time Series Insights no solution accelerator monitorização remota. Para permitir que os utilizadores do solution accelerator acessar o Explorador do Time Series Insights, tem de conceder acesso a dados cada usuário.

As políticas de acesso a dados concedem permissões para emitir consultas de dados, manipular dados de referência no ambiente e partilhar consultas guardadas e perspetivas associadas ao ambiente.

## <a name="grant-data-access"></a>Conceder acesso a dados

Siga estes passos para conceder acesso a dados para um principal de utilizador:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Localize o seu ambiente do Time Series Insights. Tipo **séries de tempo** no **pesquisa** caixa. Selecione **ambiente de Time Series** nos resultados da pesquisa. 

3. Selecione o seu ambiente do Time Series Insights na lista.

4. Selecione **políticas de acesso de dados**, em seguida, selecione **+ adicionar**.
    ![Gerir a origem do Time Series Insights - ambiente](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. Selecione **selecionar utilizador**.  Procure o endereço de e-mail ou nome de utilizador localizar o utilizador que pretende adicionar. Clique em **selecione** para confirmar a seleção. 

    ![Gerir a origem do Time Series Insights - adicionar](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. Selecione **selecionar função**. Escolha a função de acesso apropriados para o utilizador:
   - Selecione **contribuinte** se pretender permitir que o utilizador alterar dados de referência e consultas de partilha guardada e perspetivas com outros utilizadores do ambiente. 
   - Caso contrário, selecione **leitor** para permitir que os dados de consulta de utilizador no ambiente e guardar consultas pessoais de (não partilhadas) no ambiente.

     Selecione **Ok** para confirmar a escolha de função.

     ![Gerir a origem do Time Series Insights - selecionar utilizador](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. Selecione **Ok** no **selecionar função de utilizador** página.

    ![Gerir a origem do Time Series Insights - selecionar função](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. O **políticas de acesso de dados** página lista os utilizadores e funções para cada utilizador.

    ![Gerir a origem do Time Series Insights - resultados](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu como os controlos de acesso são concedidos para o Explorador do Time Series Insights no solution accelerator monitorização remota.

Para obter mais informações concetuais sobre o acelerador de solução de monitorização remota, consulte [arquitetura de monitorização remota](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Para obter mais informações sobre como personalizar a solução de monitorização remota, consulte [personalizar e Reimplementar um microsserviço](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->