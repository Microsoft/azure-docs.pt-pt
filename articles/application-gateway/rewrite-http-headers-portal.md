---
title: Reescreva os cabeçalhos de solicitação e resposta HTTP com o Gateway de aplicação do Azure - portal do Azure | Documentos da Microsoft
description: Saiba como utilizar o portal do Azure para configurar um Gateway de aplicação do Azure para reescrever os cabeçalhos HTTP nas solicitações e respostas, passando por meio do gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/10/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: e144214a58f9fe383cf4edd878554792d9d6a6f9
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947155"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Reescreva os cabeçalhos de solicitação e resposta HTTP com o Gateway de aplicação do Azure - portal do Azure

Este artigo descreve como utilizar o portal do Azure para configurar uma [SKU do Gateway de aplicação v2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) instância reescreva os cabeçalhos HTTP em solicitações e respostas.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Tem de ter uma instância SKU do Gateway de aplicação v2 para concluir os passos neste artigo. Reescrever cabeçalhos não é suportada no SKU do v1. Se não tiver o SKU de v2, crie uma [SKU do Gateway de aplicação v2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) instância antes de começar.

## <a name="create-required-objects"></a>Criar objetos necessários

Para configurar a reescrita de cabeçalho HTTP, terá de concluir estes passos.

1. Crie os objetos que são necessários para a reescrita de cabeçalho HTTP:

   - **Ação de reescrever**: Utilizado para especificar o pedido e campos de cabeçalho de solicitação que pretende reescrever e o novo valor para os cabeçalhos. Pode associar uma ou mais condições com uma ação de reescrita de regravação.

   - **Condição de reescrever**: Uma configuração opcional. Condições de reescrita avaliam o conteúdo dos pedidos de HTTP (S) e as respostas. A ação de reescrita ocorrerá se o pedido de HTTP (S) ou resposta corresponde a condição de regravação.

     Se associar mais do que uma condição uma ação, a ação ocorre apenas quando todas as condições são cumpridas. Em outras palavras, a operação é uma operação lógica AND.

   - **Regra de reescrever**: Contém vários de reescrita de ação / reescrever combinações de condição.

   - **Sequência de regra**: Ajuda a determinar a ordem na qual as regras de reescrita executar. Esta configuração é útil quando tem várias regras de reescrita de um conjunto de regravação. Uma regra de reescrita que tem um valor de seqüência de regra inferior é executado primeira. Se atribuir o mesmo valor de seqüência de regra para duas regras de reescrita, a ordem de execução é determinística.

   - **Conjunto de reescrever**: Contém várias regras de reescrita que serão associadas uma regra de encaminhamento do pedido.

2. Anexe a reescrita definida como uma regra de roteamento. A configuração de reescrita está ligada ao serviço de escuta de origem através da regra de encaminhamento. Quando utiliza uma regra básica de encaminhamento, a configuração de reescrita de cabeçalho está associada um serviço de escuta de origem e é uma reescrita de cabeçalho global. Quando utiliza uma regra de encaminhamento baseado no caminho, a configuração de reescrita de cabeçalho é definida no mapa do caminho do URL. Nesse caso, ele só se aplica a área de caminho específico de um site.

Pode criar vários conjuntos de reescrita de cabeçalho HTTP e aplicar a cada reescrita definida como vários serviços de escuta. Mas pode aplicar-se apenas um reescrever definido como um serviço de escuta específico.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/) com a sua conta do Azure.

## <a name="configure-header-rewrite"></a>Configurar a Reescrita do cabeçalho

Neste exemplo, vamos modificar um URL de redirecionamento reescrevendo o cabeçalho de localização na resposta HTTP enviada por um aplicativo de back-end.

1. Selecione **todos os recursos**e, em seguida, selecione o gateway de aplicação.

2. Selecione **reescreve** no painel esquerdo.

3. Selecione **reescrever conjunto**:

   ![Adicionar conjunto de reescrever](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Forneça um nome para o conjunto de reescrita e associá-lo a uma regra de encaminhamento:

   - Introduza o nome para a reescrita definido no **nome** caixa.
   - Selecione uma ou mais das regras listadas na **associados a regras de encaminhamento** lista. Pode selecionar apenas as regras que ainda não tiverem sido associadas a outros conjuntos de regravação. As regras que já foram associadas com outros conjuntos de reescrita são desativadas.
   - Selecione **Seguinte**.
   
     ![Adicionar nome e a associação](media/rewrite-http-headers-portal/name-and-association.png)

5. Crie uma regra de reescrita:

   - Selecione **Adicionar regra de reescrita**.

     ![Adicionar regra de reescrita](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Introduza um nome para a regra de reescrita no **nome da regra de reescrita** caixa. Introduza um número no **regra sequência** caixa.

     ![Adicionar nome da regra de reescrita](media/rewrite-http-headers-portal/rule-name.png)

6. Neste exemplo, podemos irá reescrever o cabeçalho location apenas quando ela contém uma referência para azurewebsites.net. Para tal, adicione uma condição para avaliar se o cabeçalho de localização na resposta contém azurewebsites.net:

   - Selecione **adicionar condição** e, em seguida, selecione a caixa que contém o **se** instruções para expandi-la.

     ![Adicionar uma condição](media/rewrite-http-headers-portal/add-condition.png)

   - Na **o tipo da variável para verificar** lista, selecione **cabeçalho de HTTP**.

   - Na **tipo de cabeçalho** lista, selecione **resposta**.

   - Uma vez neste exemplo, estiver a avaliar o cabeçalho de localização, que é um cabeçalho comuns, selecione **cabeçalho comum** sob **nome do cabeçalho**.

   - Na **cabeçalho comum** lista, selecione **localização**.

   - Sob **diferencia maiúsculas de minúsculas**, selecione **não**.

   - Na **operador** lista, selecione **igual (=)**.

   - Introduza um padrão de expressão regular. Neste exemplo, vamos utilizar o padrão `(https?):\/\/.*azurewebsites\.net(.*)$`.

   - Selecione **OK**.

     ![Configurar um se condição](media/rewrite-http-headers-portal/condition.png)

7. Adicione uma ação para reescrever o cabeçalho de localização:

   - Na **tipo de ação** lista, selecione **definir**.

   - Na **tipo de cabeçalho** lista, selecione **resposta**.

   - Sob **nome do cabeçalho**, selecione **cabeçalho comum**.

   - Na **cabeçalho comum** lista, selecione **localização**.

   - Introduza o valor de cabeçalho. Neste exemplo, vamos utilizar `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` como o valor de cabeçalho. Este valor irá substituir *azurewebsites.net* com *contoso.com* no cabeçalho location.

   - Selecione **OK**.

     ![Adicionar uma ação](media/rewrite-http-headers-portal/action.png)

8. Selecione **criar** para criar a reescrita conjunto:

   ![Selecione Criar](media/rewrite-http-headers-portal/create.png)

9. Abre a vista de conjunto de Regravação. Certifique-se de que o conjunto de reescrita que criou é, na lista de conjuntos de reescrita de:

   ![Reescrever a vista de conjunto](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como configurar alguns casos de utilização comuns, consulte [cabeçalho comum reescrever cenários](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).