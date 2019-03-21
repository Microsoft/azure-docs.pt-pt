---
title: incluir ficheiro
description: incluir ficheiro
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: include
ms.date: 08/20/2018
ms.author: anshan
ms.custom: include file
ms.openlocfilehash: c9daa86bf36b260001d9969385b9e8a98a8ac0cf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58125137"
---
## <a name="grant-data-access"></a>Conceder acesso a dados

Siga estes passos para conceder acesso a dados para um principal de utilizador:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Localize o seu ambiente do Time Series Insights. Tipo **séries de tempo** no **pesquisa** caixa. Selecione **ambiente de Time Series** nos resultados da pesquisa. 

3. Selecione o seu ambiente do Time Series Insights na lista.

4. Selecione **políticas de acesso de dados**, em seguida, selecione **+ adicionar**.
    ![Gerir a origem do Time Series Insights - ambiente](media/iot-tsi-data-access/getstarted-grant-data-access1.png)

5. Selecione **selecionar utilizador**.  Procure o endereço de e-mail ou nome de utilizador localizar o utilizador que pretende adicionar. Clique em **selecione** para confirmar a seleção. 

    ![Gerir a origem do Time Series Insights - adicionar](media/iot-tsi-data-access/getstarted-grant-data-access2.png)

6. Selecione **selecionar função**. Escolha a função de acesso apropriados para o utilizador:
   - Selecione **contribuinte** se pretender permitir que o utilizador alterar dados de referência e consultas de partilha guardada e perspetivas com outros utilizadores do ambiente. 
   - Caso contrário, selecione **leitor** para permitir que os dados de consulta de utilizador no ambiente e guardar consultas pessoais de (não partilhadas) no ambiente.

     Selecione **Ok** para confirmar a escolha de função.

     ![Gerir a origem do Time Series Insights - selecionar utilizador](media/iot-tsi-data-access/getstarted-grant-data-access3.png)

7. Selecione **Ok** no **selecionar função de utilizador** página.

    ![Gerir a origem do Time Series Insights - selecionar função](media/iot-tsi-data-access/getstarted-grant-data-access4.png)

8. O **políticas de acesso de dados** página lista os utilizadores e funções para cada utilizador.

    ![Gerir a origem do Time Series Insights - resultados](media/iot-tsi-data-access/getstarted-grant-data-access5.png)