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
ms.openlocfilehash: 6afc07f98905469b06622e7829ec4a215b94845e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60716216"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Reescreva os cabeçalhos de solicitação e resposta HTTP com o Gateway de aplicação do Azure - portal do Azure

Este artigo mostra-lhe como utilizar o portal do Azure para configurar uma [SKU do Gateway de aplicação v2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) reescreva os cabeçalhos HTTP nas solicitações e respostas.

> [!IMPORTANT]
> O SKU do gateway de aplicação de dimensionamento automático e com redundância entre zonas está atualmente em pré-visualização pública. Esta pré-visualização é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Tem de ter um v2 de Gateway de aplicação SKU, uma vez que a capacidade de Reescrita do cabeçalho não é suportado para o SKU de v1. Se não tiver o SKU de v2, crie uma [SKU do Gateway de aplicação v2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) antes de começar.

## <a name="what-is-required-to-rewrite-a-header"></a>O que é necessário reescrever um cabeçalho

Para configurar a reescrita de cabeçalho HTTP, precisará para:

1. Crie novos objetos necessários para reescreva os cabeçalhos de http:

   - **Ação de reescrever**: utilizado para especificar o pedido e campos de cabeçalho de solicitação que pretende reescrever e o novo valor que têm de ser reescrito para os cabeçalhos originais. Pode optar por associar uma ou mais de reescrita de condição com uma ação de reescrita.

   - **Condição de reescrever**: É uma configuração opcional. Se for adicionada uma condição de reescrita, avaliará o conteúdo dos pedidos de HTTP (S) e as respostas. A decisão para executar a ação de reescrita associada com a condição de reescrita irão basear-se a solicitação de HTTP (S) ou resposta correspondidos com a condição de regravação. 

     Se mais do que um condições estão associados com uma ação, em seguida, a ação será executado apenas quando todas as condições são cumpridas, ou seja, uma operação lógica AND será efetuada.

   - **Regra de reescrever**: regra de reescrita contém vários de reescrita de ação - reescrever combinações de condição.

   - **Sequência de regra**: ajuda a determinar a ordem na qual as diferentes regras de reescrita são executadas. Isso é útil quando existem várias regras de reescrita de um conjunto de regravação. A regra de reescrita com o menor valor de seqüência de regra é executada pela primeira vez. Se fornecer a mesma seqüência de regra para duas regras de reescrita será determinística, em seguida, a ordem de execução.

   - **Conjunto de reescrever**: contém várias regras de reescrita que serão associadas a uma regra de encaminhamento do pedido.

2. Será solicitado para anexar a reescrita definido com uma regra de roteamento. Isto acontece porque a configuração de reescrita está ligada ao serviço de escuta de origem através da regra de encaminhamento. Quando utilizar uma regra básica de encaminhamento, a configuração de reescrita de cabeçalho está associada um serviço de escuta de origem e é uma reescrita de cabeçalho global. Quando é utilizada uma regra de encaminhamento baseado no caminho, a configuração de reescrita de cabeçalho é definida no mapa do caminho do URL. Por isso, ele só se aplica a área de caminho específico de um site.

Pode criar vários conjuntos de reescrita de cabeçalho de http e cada conjunto de reescrita pode ser aplicado a vários serviços de escuta. No entanto, pode aplicar apenas um reescrever definido como um serviço de escuta específico.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/) com a sua conta do Azure.

## <a name="configure-header-rewrite"></a>Configurar a Reescrita do cabeçalho

Neste exemplo, modificaremos o URL de redirecionamento reescrevendo o cabeçalho de localização na resposta http enviada pela aplicação de back-end. 

1. Selecione **todos os recursos**e, em seguida, selecione o gateway de aplicação.

2. Selecione **reescreve** no menu à esquerda.

3. Clique em **+ reescrever conjunto**. 

   ![Adicionar conjunto de reescrever](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Forneça o nome para o conjunto de reescrita e associá-lo a uma regra de encaminhamento:

   - Introduza o nome da reescrita definido no **nome** caixa de texto.
   - Selecione uma ou mais regras listadas na **associados a regras de encaminhamento** lista. Só é possível selecionar essas regras que não foram associadas com outros conjuntos de regravação. As regras que já foram associadas com outros conjuntos de reescrita estarão indisponíveis.
   - Clique em Seguinte.
   
     ![Adicionar nome e a associação](media/rewrite-http-headers-portal/name-and-association.png)

5. Crie uma regra de reescrita:

   - Clique em **+ Adicionar regra de reescrita**.![ Adicionar regra de reescrita](media/rewrite-http-headers-portal/add-rewrite-rule.png)
   - Forneça um nome para a regra de reescrita na caixa de texto de nome de regra de reescrita e fornecer uma sequência de regra.![Adicionar nome da regra](media/rewrite-http-headers-portal/rule-name.png)

6. Neste exemplo, podemos irá regravar o cabeçalho location apenas quando ele contém uma referência a "azurewebsites.net". Para tal, adicione uma condição para avaliar se o cabeçalho de localização na resposta contém azurewebsites.net:

   - Clique em **+ adicionar condição** e, em seguida, clique na secção com o **se** instruções para expandir o mesmo.![ Adicionar nome da regra](media/rewrite-http-headers-portal/add-condition.png)

   - Selecione **cabeçalho de HTTP** partir o **tipo de variável para verificar** lista pendente. 

   - Selecione **tipo de cabeçalho** como **resposta**.

   - Uma vez que neste exemplo, estamos a avaliar o cabeçalho de localização que seja um cabeçalho comuns, selecione **cabeçalho comum** botão de opção, como o **nome do cabeçalho**.

   - Selecione **localização** partir a **cabeçalho comum** lista pendente.

   - Selecione **não** como o **diferencia maiúsculas de minúsculas** definição.

   - Selecione **igual (=)** partir do **operador** lista pendente.

   - Introduza o padrão de expressão regular. Neste exemplo, utilizamos o padrão `(https?):\/\/.*azurewebsites\.net(.*)$` .

   - Clique em **OK**.

     ![Modificar o cabeçalho de localização](media/rewrite-http-headers-portal/condition.png)

7. Adicione uma ação para reescrever o cabeçalho de localização:

   - Selecione **definir** como o **tipo de ação**.

   - Selecione **resposta** como o **tipo de cabeçalho**.

   - Selecione **cabeçalho comum** como o **nome do cabeçalho**.

   - Selecione **localização** partir a **cabeçalho comum** lista pendente.

   - Introduza o valor de cabeçalho. Neste exemplo, utilizamos `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` como o valor de cabeçalho. Esta ação irá substituir *azurewebsites.net* com *contoso.com* no cabeçalho location.

   - Clique em **OK**.

     ![Modificar o cabeçalho de localização](media/rewrite-http-headers-portal/action.png)

8. Clique em **criar** para criar a reescrita conjunto.

   ![Modificar o cabeçalho de localização](media/rewrite-http-headers-portal/create.png)

9. Será direcionado para a vista de conjunto de Regravação. Certifique-se de que o conjunto de reescrita que criou acima está presente na lista de conjuntos de regravação.

   ![Modificar o cabeçalho de localização](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a configuração necessária para realizar algumas do comum casos de utilização, veja [cabeçalho comum reescrever cenários](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

   
