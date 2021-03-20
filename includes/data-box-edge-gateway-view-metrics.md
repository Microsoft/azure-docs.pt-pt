---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 9ac7966538102273b91d6b7f15b90e18ceedd421
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "83779944"
---
Também pode ver as métricas para monitorizar o desempenho do dispositivo e, em alguns casos, para problemas de resolução de problemas do dispositivo.

Tome os seguintes passos no portal Azure para criar um gráfico para métricas de dispositivo selecionados.

1. Para o seu recurso no portal Azure, vá a **Monitoring > Metrics** e selecione **Add metric**.

    ![Adicionar métrica](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. O recurso é automaticamente povoado.  

    ![Recurso atual](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    Para especificar outro recurso, selecione o recurso. No **Selecione uma** lâmina de recurso, selecione a subscrição, o grupo de recursos, o tipo de recurso e o recurso específico para o qual pretende mostrar as métricas e selecione **Apply**.

    ![Escolha outro recurso](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

3. A partir da lista de dropdown, selecione uma métrica para monitorizar o seu dispositivo. Para obter uma lista completa destas métricas, consulte [métricas no seu dispositivo.](#metrics-on-your-device)

4. Quando uma métrica é selecionada a partir da lista de abandono, a agregação também pode ser definida. Agregação refere-se ao valor real agregado ao longo de um período de tempo especificado. Os valores agregados podem ser médios, mínimos ou o valor máximo. Selecione a agregação de Avg, Max ou Min.

    ![Ver gráfico](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. Se a métrica selecionada tiver várias instâncias, então a opção de divisão está disponível. Selecione **Aplicar a divisão** e, em seguida, selecionar o valor pelo qual deseja ver a avaria.

    ![Aplicar divisão](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. Se agora quiser ver a avaria apenas por algumas instâncias, pode filtrar os dados. Por exemplo, neste caso, se pretender ver a produção da rede apenas para as duas interfaces de rede conectadas no seu dispositivo, poderá filtrar essas interfaces. **Selecione Adicionar filtro** e especificar o nome da interface de rede para filtragem.

    ![Adicionar filtro](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. Também pode fixar a tabela no painel de instrumentos para facilitar o acesso.

    ![Afixar ao dashboard](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. Para exportar dados de gráficos para uma folha de cálculo do Excel ou obter um link para o gráfico que pode partilhar, selecione a opção de partilha a partir da barra de comando.

    ![Exportar dados](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)
