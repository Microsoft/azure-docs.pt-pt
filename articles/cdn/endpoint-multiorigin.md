---
title: Azure CDN ponto final multi-origem (Pré-visualização)
description: Começa com o ponto final do Azure CDN, várias origens.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: how-to
ms.date: 9/06/2020
ms.author: allensu
ms.openlocfilehash: 6e433950c04c4494201b090063b17a10e54a4822
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98685776"
---
# <a name="azure-cdn-endpoint-multi-origin"></a>Azure CDN ponto final multi-origem

O apoio multi-origem elimina o tempo de inatividade e estabelece a redundância global. 

Ao escolher múltiplas origens dentro de um ponto final Azure CDN, a redundância fornecida espalha o risco sondando a saúde de cada origem e falhando se necessário.

Configurar um ou mais grupos de origem e escolher um grupo de origem predefinido. Cada grupo de origem é uma coleção de uma ou mais origens que podem levar cargas de trabalho semelhantes.

> [!NOTE]
> Atualmente esta funcionalidade apenas está disponível a partir do Azure CDN da Microsoft. 

## <a name="create-the-origin-group"></a>Criar o grupo de origem

1. Inicie sessão no [portal do Azure](https://portal.azure.com)

2. Selecione o seu perfil Azure CDN e, em seguida, selecione o ponto final a ser configurado para várias origens.

3. Selecione **Origem** em **Definições** na configuração do ponto final:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-1.png" alt-text="Ponto final da CDN" border="true":::

4. Para ativar a origem, precisa de pelo menos um grupo de origem. **Selecione Create Origin Group**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-2.png" alt-text="Definições de origem" border="true":::

5. Na configuração do **Grupo Add Origin,** introduza ou selecione as seguintes informações:

   | Definição           | Valor                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Nome do grupo de origem | Insira um nome para o seu grupo de origem.                                   |
   | Estado da sonda      | Selecione **Ativado**. </br> A Azure CDN irá executar sondas de saúde de diferentes pontos do mundo para determinar a saúde da origem. Não ative se o grupo de origem atual não estiver ativo para evitar custos adicionais.
   | Caminho da sonda        | O caminho na origem que é usado para determinar a saúde. |
   | Intervalo da sonda    | Selecione um intervalo de sonda de 1, 2 ou 4 minutos.                        |
   | Protocolo de sonda    | Selecione **HTTP** ou **HTTPS**.                                         |
   | Método da sonda      | Selecione **Cabeça** ou **Obter**.                                           |
   | Grupo de origem predefinido | Selecione a caixa para definir como grupo de origem predefinido.
    
   :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-3.png" alt-text="Adicionar grupo de origem" border="true":::

6. Selecione **Adicionar**.

## <a name="add-multiple-origins"></a>Adicionar várias origens

1. Nas definições de origem para o seu ponto final, selecione **+ Criar Origem**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-5.png" alt-text="Criar origem" border="true":::

2. Introduza ou selecione as seguintes informações na configuração **Add Origin:**

   | Definição           | Valor                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Nome        | Insira um nome para a origem.        |
   | Tipo de Origem | Selecione **Armazenamento,** **Cloud Service,** **Web App** ou **origem personalizada.**                                   |
   | Nome de anfitrião da origem        | Selecione ou insira o nome de anfitrião de origem.  O drop-down lista todas as origens disponíveis do tipo especificado na definição anterior. Se selecionar **a origem personalizada** como seu tipo de origem, insira o domínio do servidor de origem do seu cliente. |
   | Cabeçalho de anfitrião de origem    | Introduza o cabeçalho do anfitrião que pretende que o Azure CDN envie a cada pedido ou deixe o padrão.                        |
   | Porta HTTP   | Insira a porta HTTP.                                         |
   | Porta HTTPS     | Insira a porta HTTPS.                                           |
   | Prioridade    | Introduza um número entre 1 e 5.       |
   | Peso      | Introduza um número entre 1 e 1000.   |

    > [!NOTE]
    > Quando uma origem é criada dentro de um grupo de origem, deve ser dada prioridade e peso. Se um grupo de origem tiver apenas uma origem, então a prioridade e o peso predefinidos são definidos como 1. O tráfego é encaminhado para as origens prioritárias mais prioritárias se a origem for saudável. Se uma origem for determinada como insalubre, as ligações são desviadas para outra origem na ordem de prioridade. Se duas origens tiverem a mesma prioridade, então o tráfego é distribuído de acordo com o peso especificado para a origem 

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-6.png" alt-text="Adicionar origem adicional" border="true":::

3. Selecione **Adicionar**.

4. Selecione **Configure a origem** para definir o caminho de origem para todas as origens:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-7.png" alt-text="Caminho de origem configurada" border="true":::

5. Selecione **OK**.

## <a name="configure-origins-and-origin-group-settings"></a>Configurar origens e configurações de grupo de origem

Uma vez que tenha várias origens e um grupo de origem, pode adicionar ou remover as origens em diferentes grupos. As origens dentro do mesmo grupo devem servir cargas de trabalho semelhantes. O tráfego será distribuído nestas origens com base no seu estado saudável, prioridade e valor de peso. 

1. Nas definições de origem do ponto final Azure CDN, selecione o nome do grupo de origem que pretende configurar:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Configurar origens e configurações de grupo de origem" border="true":::

2. No **grupo de origem Update**, selecione + **Selecione Origin**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-9.png" alt-text="Atualizar grupo de origem" border="true":::

4. Selecione a origem a adicionar ao grupo na caixa de puxar para baixo e selecione **OK**.

5. Verifique se a origem foi adicionada ao grupo e, em seguida, **selecione Guardar**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-10.png" alt-text="Verificar a origem adicional adicionada ao grupo" border="true":::

## <a name="remove-origin-from-origin-group"></a>Remover a origem do grupo de origem

1. Nas definições de origem do ponto final Azure CDN, selecione o nome do grupo de origem:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Remover a origem do grupo" border="true":::

2. Para remover uma origem do grupo de origem, selecione o ícone do caixote do lixo ao lado da origem e selecione **Guardar:**

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-11.png" alt-text="Atualizar a origem do grupo de origem eliminar a origem" border="true":::

## <a name="override-origin-group-with-rules-engine"></a>Grupo de origem de substituição com motor de regras

Personalize como o tráfego é distribuído a diferentes grupos de origem utilizando o motor de regras padrão.

Distribuir o tráfego a um grupo diferente com base no URL de pedido.

1. No seu ponto final CDN, selecione **o motor Regras** em **Definições**:

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-12.png" alt-text="Motor de regras" border="true":::

2. Selecione **+ Adicionar regra**.

3. Insira um nome para a regra em **Nome**.

4. Selecione **+ Condição,** em seguida, selecione **o caminho URL**.

5. Na **premir** o operador, selecione **Contém**.

6. Em **Valor,** **introduza/imagens.**

7. Selecione **+ Adicionar ação** e, em seguida, selecione a **substituição do grupo Origin**.

8. No **grupo Origin**, selecione o grupo de origem na caixa de puxar para baixo.

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-13.png" alt-text="Regras condições do motor" border="true":::

Para todos os pedidos de entrada se o caminho URL contiver **/imagens,** o pedido será atribuído ao grupo de origem na secção de ação **(grupo myorigin)**. 

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, ativou o ponto final Azure CDN de origem.

Para obter mais informações sobre a Azure CDN e os outros serviços Azure mencionados neste artigo, consulte:

* [CDN do Azure](./cdn-overview.md)
* [Compare a funcionalidade de produto Azure CDN](./cdn-features.md)
