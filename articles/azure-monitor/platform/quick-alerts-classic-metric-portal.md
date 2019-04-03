---
title: Receber uma notificação quando um valor métrico cumpre uma condição
description: Um guia de início rápido para ajudar os utilizadores a criar uma métrica para uma Aplicação Lógica
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: quickstart
ms.date: 02/08/2018
ms.author: ancav
ms.custom: mvc
ms.subservice: alerts
ms.openlocfilehash: f8b9db47684a6dd78302f094d8e670da4a61ab2c
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58849821"
---
# <a name="receive-a-notification-when-a-metric-value-meets-a-condition"></a>Receber uma notificação quando um valor métrico cumpre uma condição

O Azure Monitor disponibiliza métricas para diversos recursos do Azure. Estas métricas são indicadoras do desempenho e do estado de funcionamento desses recursos. Em muitos casos, as métricas identificam um problema com um recurso. Pode criar alertas de métricas para monitorizar comportamento anormal e ser notificado se tal ocorrer. Este Início Rápido descreve a criação de uma Aplicação Lógica, a criação de uma tarefa e a visualização das métricas da aplicação lógica. Em seguida, aborda a criação de um alerta e a receção de uma notificação para uma métrica para o recurso da Aplicação Lógica.

Para obter mais informações sobre as métricas e os alertas de métricas, consulte [Descrição geral das métricas do Azure Monitor](data-platform.md) e [Descrição geral dos alertas do Azure Monitor](alerts-overview.md). 

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-logic-app"></a>Criar uma Aplicação Lógica

1. Clique no botão **Criar um recurso**, no canto superior esquerdo do portal do Azure.

2. Pesquise e selecione **Aplicação Lógica**. Clique no botão **Criar**.

3. Introduza o nome myLogicApp e o grupo de recursos myResourceGroup. Utilize a sua subscrição.  Utilize a localização predefinida. Marque a opção **Afixar ao Dashboard**.  Quando terminar, clique em **Criar**. 

    ![Introduza as informações básicas da aplicação lógica no portal](./media/quick-alerts-classic-metric-portal/create-logic-app-portal.png)  


4. A aplicação lógica deve ser afixada ao dashboard. Navegue para a aplicação lógica clicando na mesma.

5. No painel Aplicação Lógica, selecione o **Estruturador da Aplicação Lógica**

     ![Crie um acionador de periodicidade no estruturador da aplicação lógica no painel do portal](./media/quick-alerts-classic-metric-portal/logic-app-designer.png)  

6. Configure os valores com base no diagrama seguinte.

    ![Configure o acionador da aplicação lógica no painel do portal](./media/quick-alerts-classic-metric-portal/create-logic-app-triggers.png) 

7. No estruturador, selecione o acionador **Periodicidade**.

8. Defina um intervalo de 20 e uma frequência de segundo para garantir que a sua aplicação lógica é acionada a cada 20 segundos.

9. Clique no botão **Novo Passo** e selecione **Adicionar uma ação**.

10. Escolha a opção **HTTP** e selecione **HTTP-HTTP**.

11. Defina o **Método** como POST e o **Uri** para um endereço Web à sua escolha.

12. Clique em **Guardar**.

13. A aplicação lógica pode demorar até cinco minutos a executar as ações.  

## <a name="view-metrics-for-your-logic-app"></a>Ver as métricas da aplicação lógica

1. Clique na opção **Monitorizar** existente no painel de navegação esquerdo.

2. Selecione o separador **Métricas**, preencha as informações de **Subscrição**, **Grupo de Recursos**, **Tipo de Recurso** e **Recurso** para a sua aplicação lógica.

3. Na lista de métricas, escolha **Execuções Falhadas**.

4. Modifique o **Intervalo de tempo** do gráfico para apresentar dados da última hora.

5. Agora, deverá ver um gráfico que representa o número total de execuções iniciadas pela aplicação lógica na última hora. Se não vir nenhum,confirme se esperou, pelo menos, cinco minutos desde o passo acima. Em seguida, atualize o browser. 

    ![Desenhar um gráfico de métricas para o recurso de aplicação lógica](./media/quick-alerts-classic-metric-portal/logic-app-metric-chart.png)

## <a name="create-a-metric-alert-for-your-logic-app"></a>Criar um alerta de métrica para a aplicação lógica

1.  Na parte superior direita do painel métricas, clique no botão **Adicionar alerta de métrica**.

2. Dê o nome "myLogicAppAlert" ao alerta de métrica e forneça uma breve descrição do alerta.

3. Defina a **Condição** do alerta de métrica como "Maior que", defina o **Limiar** como "10" e defina o **Período** como "Nos últimos 5 minutos".

4. Por fim, em **E-mail(s) adicional(is) do administrador**, introduza o seu endereço de e-mail. Este alerta garante que recebe uma mensagem de e-mail quando a aplicação lógica tiver mais de 10 execuções falhadas num período de 5 minutos.

    ![Configurar o alerta da aplicação lógica no painel do portal](./media/quick-alerts-classic-metric-portal/logic-app-metrics-alert-portal.png)

## <a name="receive-metric-alert-notifications-for-your-logic-app"></a>Receber notificações de alerta de métrica para a aplicação lógica
1. Decorridos alguns instantes, deverá receber uma mensagem de e-mail dos "Alertas do Microsoft Azure" para o informar de que o alerta foi "ativado".

2. Navegue para a aplicação lógica e modifique o acionador de periodicidade para um intervalo de 1 e uma frequência de hora.

3. Decorridos alguns minutos, deverá receber um e-mail dos "Alertas do Microsoft Azure" para o informar de que o alerta foi "resolvido".

## <a name="clean-up-resources"></a>Limpar recursos

O conteúdo deste início rápido é aprofundado por outros inícios rápidos desta coleção. Se planeia continuar a trabalhar com inícios rápidos subsequentes ou com os tutoriais, não limpe os recursos criados neste início rápido. Se não pretender continuar, utilize os passos seguintes para eliminar todos os recursos criados por este guia de introdução no portal do Azure.

1. No menu esquerdo no portal do Azure, clique em **Monitorizar**.

2. Selecione o separador **Alertas**, localize o alerta que criou neste guia de início rápido e clique no mesmo.

3. No painel de alerta de métrica, clique em **Eliminar**.

4. No menu esquerdo no portal do Azure, pesquise **Aplicação Lógica** e, em seguida, clique em **Aplicações lógicas**.

5. No painel, clique na aplicação lógica que criou neste início rápido na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu a criar um alerta de métrica para os seus recursos. Para obter mais informações sobre alertas de métricas, clique para aceder à descrição geral dos alertas.

> [!div class="nextstepaction"]
> [Alertas de ação de subscrição do Azure Monitor](./../../azure-monitor/platform/quick-audit-notify-action-subscription.md )

