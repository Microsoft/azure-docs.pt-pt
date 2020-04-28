---
title: Reescrever cabeçalhos de pedido e resposta HTTP no portal - Gateway de aplicação azure
description: Saiba como usar o portal Azure para configurar um Portal de Aplicação Azure para reescrever os cabeçalhos HTTP nos pedidos e respostas que passam pelo portal
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: b90736b3ed1c1f69488fde4a386cf215d751c362
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74012862"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Reescrever http pedido e cabeçalhos de resposta com Portal de Aplicação Azure - Portal Azure

Este artigo descreve como usar o portal Azure para configurar uma instância [de Gateway v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) para reescrever os cabeçalhos HTTP em pedidos e respostas.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Você precisa ter uma instância de Application Gateway v2 SKU para completar os passos neste artigo. Reescrever cabeçalhos não é suportado no V1 SKU. Se não tiver o V2 SKU, crie uma instância [SKU De Gateway v2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) antes de começar.

## <a name="create-required-objects"></a>Criar objetos necessários

Para configurar a reescrita do cabeçalho HTTP, é necessário completar estes passos.

1. Criar os objetos necessários para reescrever o cabeçalho HTTP:

   - **Reescrever ação**: Utilizado para especificar o pedido e solicitar campos de cabeçalho que pretende reescrever e o novo valor para os cabeçalhos. Pode associar uma ou mais condições de reescrita com uma ação de reescrita.

   - **Condição de reescrita**: Uma configuração opcional. As condições de reescrita avaliam o conteúdo dos pedidos e respostas http(S). A ação de reescrita ocorrerá se o pedido ou resposta HTTP(S) corresponder à condição de reescrita.

     Se associar mais do que uma condição a uma ação, a ação ocorre apenas quando todas as condições são satisfeitas. Por outras palavras, a operação é uma operação lógica e operacional.

   - **Regra de reescrita**: Contém múltiplas combinações de condições de reescrita/ reescrita.

   - **Sequência de regras**: Ajuda a determinar a ordem em que as regras de reescrita executam. Esta configuração é útil quando se tem várias regras de reescrita num conjunto de reescrita. Uma regra de reescrita que tem um valor de sequência de regras inferior corre primeiro. Se atribuir o mesmo valor de sequência de regras a duas regras de reescrita, a ordem de execução não é determinista.

   - **Reescrever conjunto**: Contém múltiplas regras de reescrita que serão associadas a uma regra de encaminhamento de pedidos.

2. Fixe a reescrita definida numa regra de encaminhamento. A configuração de reescrita é fixada ao ouvinte de origem através da regra de encaminhamento. Quando se utiliza uma regra básica de encaminhamento, a configuração de reescrita do cabeçalho está associada a um ouvinte de origem e é uma reescrita global do cabeçalho. Quando utiliza uma regra de encaminhamento baseada no caminho, a configuração de reescrita do cabeçalho é definida no mapa do caminho do URL. Nesse caso, aplica-se apenas à área específica do caminho de um local.

Pode criar vários conjuntos de reescrita de cabeçalho HTTP e aplicar cada conjunto de reescrita a vários ouvintes. Mas só pode aplicar um conjunto de reescrita a um ouvinte específico.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no [portal Azure](https://portal.azure.com/) com a sua conta Azure.

## <a name="configure-header-rewrite"></a>Configurar a reescrita do cabeçalho

Neste exemplo, modificaremos um URL de reorientação reescrevendo o cabeçalho de localização na resposta HTTP enviada por uma aplicação de back-end.

1. **Selecione Todos os recursos**e, em seguida, selecione o gateway da sua aplicação.

2. Selecione **Reescritas** no painel esquerdo.

3. Selecione **reescrever definido**:

   ![Adicionar conjunto de reescrever](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Forneça um nome para o conjunto de reescrita e associe-o a uma regra de encaminhamento:

   - Introduza o nome para o conjunto de reescrita na caixa **nome.**
   - Selecione uma ou mais das regras listadas na lista de regras de **encaminhamento associados.** Só pode selecionar regras que não tenham sido associadas a outros conjuntos de reescrita. As regras que já foram associadas a outros conjuntos de reescrita são diminuídas.
   - Selecione **Seguinte**.
   
     ![Adicionar nome e associação](media/rewrite-http-headers-portal/name-and-association.png)

5. Criar uma regra de reescrita:

   - Selecione **Adicionar regra de reescrever**.

     ![Adicionar regra de reescrever](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Introduza um nome para a regra de reescrever na caixa de nomes de **reescrever.** Introduza um número na caixa de sequência de **regras.**

     ![Adicione o nome da regra de reescrever](media/rewrite-http-headers-portal/rule-name.png)

6. Neste exemplo, reescreveremos o cabeçalho de localização apenas quando contiver uma referência a azurewebsites.net. Para tal, adicione uma condição para avaliar se o cabeçalho de localização na resposta contém azurewebsites.net:

   - **Selecione Adicionar condição** e, em seguida, selecione a caixa contendo as instruções **se** as instruções para expandi-la.

     ![Adicionar uma condição](media/rewrite-http-headers-portal/add-condition.png)

   - No **tipo de variável para verificar lista,** selecione **cabeçalho HTTP**.

   - Na lista do **tipo Header,** selecione **Resposta**.

   - Porque neste exemplo estamos a avaliar o cabeçalho de localização, que é um cabeçalho comum, selecione **cabeçalho comum** sob **o nome header**.

   - Na lista **de cabeçalhos comum,** selecione **Localização**.

   - Em **caso sensível ao caso,** selecione **Nº**.

   - Na lista **do Operador,** selecione **igual (=)**.

   - Introduza um padrão de expressão regular. Neste exemplo, usaremos o `(https?):\/\/.*azurewebsites\.net(.*)$`padrão.

   - Selecione **OK**.

     ![Configurar uma condição se](media/rewrite-http-headers-portal/condition.png)

7. Adicione uma ação para reescrever o cabeçalho de localização:

   - Na lista do **tipo Ação,** selecione **set**.

   - Na lista do **tipo Header,** selecione **Resposta**.

   - Sob **o nome cabeçalho,** selecione **Cabeçalho Comum**.

   - Na lista **de cabeçalhos comum,** selecione **Localização**.

   - Introduza o valor do cabeçalho. Neste exemplo, usaremos `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` como valor cabeçalho. Este valor substituirá *azurewebsites.net* por *contoso.com* no cabeçalho de localização.

   - Selecione **OK**.

     ![Adicionar uma ação](media/rewrite-http-headers-portal/action.png)

8. Selecione **Criar** para criar o conjunto de reescrever:

   ![Selecione Criar](media/rewrite-http-headers-portal/create.png)

9. A vista definida de Reescrita será aberta. Verifique se o conjunto de reescrita que criou está na lista de conjuntos de reescrita:

   ![Reescrever a vista do conjunto](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como configurar alguns casos de uso comum, consulte [cenários comuns](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)de reescrita do cabeçalho .