---
title: Azure CDN ponto final multi-origem
description: Começa com o Ponto Final azure CDN múltiplas origens.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: how-to
ms.date: 5/8/2020
ms.author: allensu
ms.openlocfilehash: 628a284335b7285a2ee989511b130fb7d4d3cde6
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597878"
---
# <a name="azure-cdn-endpoint-multi-origin"></a>Azure CDN ponto final multi-origem

Com o apoio de múltiplas origens, você pode estabelecer redundância global e eliminar o tempo de inatividade escolhendo múltiplas origens dentro de um ponto final de CDN Azure. O despedimento proporcionado por multi-origem espalha o risco, examinando a saúde de cada origem e falhando se necessário. Para criar multi-origens, instale um ou mais grupos de origem. Cada grupo de origem é uma coleção de uma ou mais origens que podem ter cargas de trabalho semelhantes.

> [!NOTE]
> Atualmente esta funcionalidade só está disponível a partir do Azure CDN da Microsoft. 

## <a name="create-the-origin-group"></a>Criar o grupo de origem

1. Inscreva-se no [portal Azure](https://portal.azure.com)

2. Selecione o seu perfil Azure CDN e, em seguida, selecione o ponto final a configurar para multi-origem.

3. Selecione **Origem** sob **definições** na configuração do ponto final:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-1.png" alt-text="Multi-origem Azure CDN" border="true":::

4. Para permitir a multi-origem, precisa de pelo menos um grupo de origem. Selecione **Create Origin Group**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-2.png" alt-text="Multi-origem Azure CDN" border="true":::

5. Na configuração do **Grupo Add Origin,** introduza ou selecione as seguintes informações:

   | Definição           | Valor                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Nome do grupo de origem | Insira um nome para o seu grupo de origem.                                   |
   | Caminho da sonda        | O caminho na origem que é usado para determinar a saúde. |
   | Intervalo da sonda    | Selecione um intervalo de sonda de 1, 2 ou 4 minutos.                        |
   | Protocolo de sonda    | Selecione **HTTP** ou **HTTPS**.                                         |
   | Método de sonda      | Selecione **Cabeça** ou **Obter**.                                           |
    
   :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-3.png" alt-text="Multi-origem Azure CDN" border="true":::

6. Selecione **Adicionar**.

7. Para escolher o grupo de origem predefinido, **selecione Grupo Origem Configuração:**

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-4.png" alt-text="Multi-origem Azure CDN" border="true":::

8. Selecione o seu grupo de origem na caixa de pull-down e selecione **OK**.

## <a name="add-multiple-origins"></a>Adicionar múltiplas origens

1. Nas definições de origem para o seu ponto final, selecione **+ Create Origin**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-5.png" alt-text="Multi-origem Azure CDN" border="true":::

2. Introduza ou selecione as seguintes informações na configuração **Add Origin:**

   | Definição           | Valor                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Tipo de Origem | Selecione **Armazenamento,** **Serviço de Nuvem,** **Web App,** ou **origem personalizada.**                                   |
   | Nome de anfitrião da origem        | Selecione ou introduza o seu nome de anfitrião de origem.  O drop-down lista todas as origens disponíveis do tipo especificado na definição anterior. Se selecionou a **origem personalizada** como tipo de origem, introduza o domínio do seu servidor de origem do cliente. |
   | Cabeçalho de anfitrião de origem    | Introduza o cabeçalho do anfitrião que pretende que o Azure CDN envie a cada pedido ou deixe o padrão.                        |
   | Porto HTTP   | Insira a porta HTTP.                                         |
   | Porto HTTPS     | Insira a porta HTTPS.                                           |
   | Prioridade    | Introduza um número entre 1 e 5.       |
   | Peso      | Insira um número entre 1 e 1000.   |

    > [!NOTE]
    > Quando uma origem é criada dentro de um grupo de origem, deve ser-lhe dada uma prioridade e um peso. Se um grupo de origem tiver apenas uma origem, então a prioridade padrão e o peso são definidos como 1. O tráfego é encaminhado para as origens prioritárias mais elevadas se a origem for saudável. Se uma origem for determinada como insalubre, as ligações são desviadas para outra origem por ordem de prioridade. Se duas origens tiverem a mesma prioridade, então o tráfego é distribuído de acordo com o peso especificado para a origem 

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-6.png" alt-text="Multi-origem Azure CDN" border="true":::

3. Selecione **Adicionar**.

4. **Selecione A origem do Configure** para definir o caminho de origem para todas as origens:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-7.png" alt-text="Multi-origem Azure CDN" border="true":::

5. Selecione **OK**.

## <a name="configure-origins-and-origin-group-settings"></a>Configurar origens e configurações do grupo de origem

Uma vez que você tem várias origens e um grupo de origem, você pode adicionar ou remover as origens em diferentes grupos. As origens dentro do mesmo grupo devem servir cargas de trabalho semelhantes. O tráfego será distribuído nestas origens com base no seu estado saudável, prioridade e valor de peso. 

1. Nas definições de origem do ponto final do CDN Azure, **selecione Grupo Origem Configuração:**

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Multi-origem Azure CDN" border="true":::

2. Selecione o grupo de origem que pretende configurar na caixa de pull-down e selecione **OK**.

3. No **grupo origem update**, selecione + Adicionar **Origem**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-9.png" alt-text="Multi-origem Azure CDN" border="true":::

4. Selecione a origem a adicionar ao grupo na caixa de puxar para baixo e selecione **OK**.

5. Verifique se a origem foi adicionada ao grupo e, em seguida, **selecione Guardar:**

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-10.png" alt-text="Multi-origem Azure CDN" border="true":::

## <a name="remove-origin-from-origin-group"></a>Remover a origem do grupo de origem

1. Nas definições de origem do ponto final do CDN Azure, **selecione Grupo Origem Configuração:**

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Multi-origem Azure CDN" border="true":::

2. Selecione o grupo de origem que pretende configurar na caixa de pull-down e selecione **OK**.

3. Para remover uma origem do grupo de origem, selecione o ícone do caixote do lixo ao lado da origem e selecione **Guardar:**

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-11.png" alt-text="Multi-origem Azure CDN" border="true":::

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, ativou a multi-origem de Azure CDN.

Para obter mais informações sobre o Azure CDN e os outros serviços Azure mencionados neste artigo, consulte:

* [CDN do Azure](./cdn-overview.md)
* [Compare a funcionalidade de produto Azure CDN](./cdn-features.md)
