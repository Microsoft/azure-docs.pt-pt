---
title: Reescrever pedidos HTTP e cabeçalhos de resposta no portal - Azure Application Gateway
description: Saiba como utilizar o portal Azure para configurar um Gateway de aplicação Azure para reescrever os cabeçalhos HTTP nos pedidos e respostas que passam pelo gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: a77476086d6100cbaf49d54791972940cca0644f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708939"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Reescreva os cabeçalhos de pedido e resposta HTTP com gateway de aplicação Azure - portal Azure

Este artigo descreve como usar o portal Azure para configurar uma instância [SKU do Gateway de Aplicação v2](./application-gateway-autoscaling-zone-redundant.md) para reescrever os cabeçalhos HTTP em pedidos e respostas.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Precisa de um exemplo de SKU do Gateway v2 para completar os passos deste artigo. Reescrevendo cabeçalhos não é suportado no V1 SKU. Se não tiver o V2 SKU, crie uma [instância SKU SKU do Gateway v2](./tutorial-autoscale-ps.md) antes de começar.

## <a name="create-required-objects"></a>Criar objetos necessários

Para configurar a reescrita do cabeçalho HTTP, é necessário completar estes passos.

1. Crie os objetos necessários para reescrever o cabeçalho HTTP:

   - **Ação de reescrita**: Usado para especificar os campos de pedido e pedido que pretende reescrever e o novo valor para os cabeçalhos. Pode associar uma ou mais condições de reescrita com uma ação de reescrita.

   - **Condição de reescrita**: Uma configuração opcional. As condições de reescrita avaliam o conteúdo dos pedidos e respostas HTTP(S). A ação de reescrita ocorrerá se o pedido http(S) ou a resposta corresponder à condição de reescrita.

     Se associar mais do que uma condição a uma ação, a ação ocorre apenas quando todas as condições estão reunidas. Por outras palavras, a operação é uma operação lógica.

   - **Regra de reescrita**: Contém combinações de condição de reescrita múltiplas/ reescrita.

   - **Sequência de** regras : Ajuda a determinar a ordem pela qual as regras de reescrita executam. Esta configuração é útil quando tem várias regras de reescrita num conjunto de reescrita. Uma regra de reescrita que tem um valor de sequência de regras mais baixo corre primeiro. Se atribuir o mesmo valor de sequência de regras a duas regras de reescrita, a ordem de execução não é determinística.

   - **Conjunto de reescrita**: Contém várias regras de reescrita que serão associadas a uma regra de encaminhamento de pedidos.

2. Fixe o conjunto de reescrita a uma regra de encaminhamento. A configuração de reescrita é anexada ao ouvinte de origem através da regra de encaminhamento. Quando utiliza uma regra de encaminhamento básico, a configuração de reescrita do cabeçalho está associada a um ouvinte de origem e é uma reescrita global do cabeçalho. Quando utiliza uma regra de encaminhamento baseada no caminho, a configuração de reescrita do cabeçalho é definida no mapa do caminho URL. Nesse caso, aplica-se apenas à área específica do percurso de um sítio.

Pode criar vários conjuntos de reescrita do cabeçalho HTTP e aplicar cada conjunto de reescrita a vários ouvintes. Mas pode aplicar apenas um conjunto de reescrita a um ouvinte específico.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/) com a sua conta do Azure.

## <a name="configure-header-rewrite"></a>Reescrita do cabeçalho de configuração

Neste exemplo, vamos modificar um URL de reorientação reescrevendo o cabeçalho de localização na resposta HTTP enviada por uma aplicação de back-end.

1. Selecione **Todos os recursos** e, em seguida, selecione o seu gateway de aplicações.

2. **Selecione Reescreve** no painel esquerdo.

3. Selecione **Conjunto de reescrita**:

   ![Adicione conjunto de reescrita](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Fornecer um nome para o conjunto de reescrita e associá-lo a uma regra de encaminhamento:

   - Introduza o nome para o conjunto de reescrita na caixa **Nome.**
   - Selecione uma ou mais das regras listadas na lista **de regras de encaminhamento associados.** Só pode selecionar regras que não tenham sido associadas a outros conjuntos de reescrita. As regras que já foram associadas a outros conjuntos de reescritas são diminuídas.
   - Selecione **Seguinte**.
   
     ![Adicionar nome e associação](media/rewrite-http-headers-portal/name-and-association.png)

5. Criar uma regra de reescrita:

   - Selecione **Adicionar regra de reescrita**.

     ![Adicionar regra de reescrita](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Introduza um nome para a regra de reescrita na caixa **de nome de regra de reescrita.** Introduza um número na caixa **de sequência de regras.**

     ![Adicionar nome de regra de reescrita](media/rewrite-http-headers-portal/rule-name.png)

6. Neste exemplo, só reescrevemos o cabeçalho de localização quando contiver uma referência a azurewebsites.net. Para isso, adicione uma condição para avaliar se o cabeçalho de localização na resposta contém azurewebsites.net:

   - **Selecione Adicione a condição** e, em seguida, selecione a caixa que contém as instruções para expandi-la. 

     ![Adicionar uma condição](media/rewrite-http-headers-portal/add-condition.png)

   - No **tipo de variável para verificar** lista, selecione **cabeçalho HTTP**.

   - Na lista de **tipos do cabeçalho,** selecione **Response**.

   - Porque neste exemplo estamos a avaliar o cabeçalho de localização, que é um cabeçalho comum, selecione  **Cabeçalho Comum** sob **o nome cabeçalho**.

   - Na lista **de cabeçalhos comuns,** selecione **Localização**.

   - No **âmbito do caso sensível ao caso,** selecione **Nº**.

   - Na lista **do Operador,** selecione **igual (=)**.

   - Introduza um padrão de expressão regular. Neste exemplo, vamos usar o `(https?)://.*azurewebsites.net(.*)$` padrão.

   - Selecione **OK**.

     ![Configure uma condição de Se](media/rewrite-http-headers-portal/condition.png)

7. Adicione uma ação para reescrever o cabeçalho de localização:

   - Na lista **de tipos de Ação,** selecione **set**.

   - Na lista de **tipos do cabeçalho,** selecione **Response**.

   - No **nome do cabeçalho**, selecione **Common header**.

   - Na lista **de cabeçalhos comuns,** selecione **Localização**.

   - Introduza o valor do cabeçalho. Neste exemplo, usaremos `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` como valor de cabeçalho. Este valor substituirá *azurewebsites.net* por *contoso.com* no cabeçalho de localização.

   - Selecione **OK**.

     ![Adicionar uma ação](media/rewrite-http-headers-portal/action.png)

8. Selecione **Criar** para criar o conjunto de reescrita:

   ![Selecione Criar](media/rewrite-http-headers-portal/create.png)

9. A vista do conjunto Rewrite será aberta. Verifique se o conjunto de reescrita que criou está na lista de conjuntos de reescrita:

   ![Reescrever a vista definida](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como configurar alguns casos de uso comum, consulte [cenários comuns de reescrita do cabeçalho](./rewrite-http-headers.md).