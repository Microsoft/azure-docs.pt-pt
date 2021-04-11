---
title: 'Tutorial: Criar um equilibrador de carga transversal utilizando o portal Azure'
titleSuffix: Azure Load Balancer
description: Começa com este tutorial implantando um Equilibrador de Carga Azure com o portal Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/24/2021
ms.openlocfilehash: 65d85f51afef36aa618868e2fda1d2bbf583ea21
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221131"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-the-azure-portal"></a>Tutorial: Criar um balanceador de carga azure cross-region usando o portal Azure

Um equilibrador de carga transversal garante que um serviço está disponível globalmente em várias regiões de Azure. Se uma região falhar, o tráfego é encaminhado para o próximo e mais próximo e saudável equilibrador regional de carga.  

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar balanceador de carga entre regiões.
> * Crie uma piscina de backend contendo dois equilibradores de carga regionais.
> * Crie uma regra de balanceador de carga.
> * Teste o equilibrador de carga.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!IMPORTANT]
> Cross-region Azure Load Balancer está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure.
- Dois  balançadores de carga sku Azure standard com piscinas de backend implantadas em duas regiões Azure diferentes.
    - Para obter informações sobre a criação de um balanceador de carga padrão regional e máquinas virtuais para piscinas de backend, consulte [Quickstart: Criar um equilibrador de carga público para carregar VMs de equilíbrio utilizando o portal Azure](quickstart-load-balancer-standard-public-portal.md).
        - Apeia o nome dos equilibradores de carga, máquinas virtuais e outros recursos em cada região com um **-R1** e **-R2**. 

## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure

[Inicie sessão](https://portal.azure.com) no portal do Azure.

## <a name="create-cross-region-load-balancer"></a>Criar balanceador de carga entre regiões

Nesta secção, você vai criar um balanceador de carga transversal e endereço IP público.

1. Selecione **Criar um recurso**. 
2. Na caixa de pesquisa, insira o **balançador de carga**. Selecione **o balanceador de carga** nos resultados da pesquisa.
3. Na página **do balançador de carga,** selecione **Criar**.
4. No separador Básicos da página **'Criar balançador** de carga', insira ou selecione as **seguintes** informações: 

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | **Selecione Criar novo** e **introduza CreateCRLBTutorial-rg** na caixa de texto.|
    | Name                   | Insira **myLoadBalancer-CR**                                   |
    | Region         | Selecione **(EUA) West US**.                                        |
    | Tipo          | Selecione **Público**.                                        |
    | SKU           | Deixe o padrão da **Norma**. |
    | Escalão de serviço           | Selecione **Global** |
    | Endereço IP público | Selecione **Criar novo**.|
    | Nome do endereço IP público | Digite **myPublicIP-CR** na caixa de texto.|
    | Preferência de encaminhamento| Selecione **a rede Microsoft**. </br> Para obter mais informações sobre a preferência de encaminhamento, consulte [o que é a preferência de encaminhamento (pré-visualização)?](../virtual-network/routing-preference-overview.md) |

    > [!NOTE]
    > O balanceador de cargas da região transversal só pode ser implantado nas seguintes regiões de **origem: Leste dos EUA 2, Eua Ocidental, Europa Ocidental, Sudeste Asiático, EUA Central, Norte da Europa, Ásia Oriental.** Para obter mais informações, veja **https://aka.ms/homeregionforglb**.


3. Aceite as predefinições para as definições restantes e, em seguida, selecione **Review + create**.

4. No **separador 'Rever + criar',** selecione **Criar.**   

    :::image type="content" source="./media/tutorial-cross-region-portal/create-cross-region.png" alt-text="Criar um equilibrador de carga transversal" border="true":::

## <a name="create-backend-pool"></a>Criar conjunto de back-end

Nesta secção, você adicionará dois equilibradores de carga padrão regionais ao pool de backend do equilibrador de carga cross-region.

> [!IMPORTANT]
> Para completar estes passos, certifique-se de que dois equilibradores de carga regionais com pools de backend foram implantados na sua subscrição.  Para obter mais informações, **[consulte, Quickstart: Criar um balanceador de carga pública para carregar VMs de equilíbrio utilizando o portal Azure](quickstart-load-balancer-standard-public-portal.md)**.

Crie o pool de endereços de backend **myBackendPool-CR** para incluir os equilibradores de carga padrão regionais.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, em seguida, selecione **myLoadBalancer-CR** da lista de recursos.

2. Em **Definições**, selecione **pools backend** e, em seguida, selecione **Adicionar**.

3. Na página **de pool de backend,** para o nome, escreva **myBackendPool-CR**.

4. Selecione **Adicionar**.

4. Selecione **myBackendPool-CR**.

5. Nos **balançadores de carga**, selecione a caixa de puxar para baixo sob o **balançador de carga**.

5. Selecione **myLoadBalancer-R1,** ou o nome do seu equilibrador de carga na região 1.

6. Selecione a caixa de puxar para baixo sob **a configuração FRONTend IP**. Escolha **LoadBalancerFrontEnd**.

7. Repita os passos 4-6 para adicionar **o myLoadBalancer-R2**.

8. Selecione **Adicionar**.

    :::image type="content" source="./media/tutorial-cross-region-portal/add-to-backendpool.png" alt-text="Adicione equilibradores de carga regionais ao backendpool" border="true":::

## <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Nesta secção, irá criar uma regra do balançador de carga:

* Chamado **myHTTPRule.**
* Na frontend chamada **LoadBalancerFrontEnd**.
* Escuta no **Porto 80**.
* Direciona o tráfego equilibrado para o backendback chamado **myBackendPool-CR** na **Porta 80**.

    > [!NOTE]
    > O porto frontend deve corresponder ao porto de backend e ao porto frontal dos balançadores de carga regionais na piscina de backend.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, em seguida, selecione **myLoadBalancer-CR** da lista de recursos.

2. Em **Definições**, selecione **regras de equilíbrio de carga** e, em seguida, selecione **Adicionar**.

3. Utilize estes valores para configurar a regra de equilíbrio de carga:
    
    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira **myHTTPRule**. |
    | Versão IP | Selecione **IPv4** |
    | Endereço IP frontend | **Selecione LoadBalancerFrontEnd** |
    | Protocolo | Selecione **TCP**. |
    | Porta | Insira **80**.|
    | Porta de back-end | Insira **80**. |
    | Conjunto de back-end | Selecione **myBackendPool**.|
    | Sonda de estado de funcionamento | Selecione **myHealthProbe**. |
    | Tempo de 20 minutos (minutos) | Mova o slider para **15**. |
    | Reset TCP | Selecione **Ativado**. |

4. Deixe o resto dos predefinidos e, em seguida, selecione **OK**.

    :::image type="content" source="./media/tutorial-cross-region-portal/create-lb-rule.png" alt-text="Criar regra do balançador de carga" border="true":::

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Nesta secção, você vai testar o equilibrador de carga cross-region. Você vai ligar ao endereço IP público em um navegador web.  Você vai parar as máquinas virtuais em uma das piscinas regionais de backend balancer de carga e observar o failover.

1. Encontre o endereço IP público para o balançador de carga no ecrã **de visão geral.** Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, em seguida, selecione **myPublicIP-CR**.

2. Copie o endereço IP público e cole-o na barra de endereço do browser. A página predefinida do servidor Web do IIS é apresentada no browser.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-1.png" alt-text="Testar o balanceador de carga" border="true":::

3. Pare as máquinas virtuais na piscina de backend de um dos esquilibradores de carga regionais.

4. Refresque o navegador web e observe o failover da ligação ao outro equilibrador regional de carga.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-2.png" alt-text="Equilibramento de carga de teste após falha" border="true":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos, o balanceador de carga e todos os recursos relacionados. 

Para tal, selecione o grupo de recursos **CreateCRLBTutorial-rg** que contém os recursos e, em seguida, **selecione Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

* Criou um equilibrador de carga entre regiões.
* Adicionou equilibradores de carga regionais ao pool de backend do equilibrador de carga entre regiões.
* Criou uma regra de equilíbrio de carga.
* Testei o equilibrador de carga.

Para obter mais informações sobre o equilibrador de carga entre regiões, consulte:
> [!div class="nextstepaction"]
> [Balanceador de carga transversal (Pré-visualização)](cross-region-overview.md)
