---
title: Quickstart - Criar um serviço de Link Privado utilizando o portal Azure
titlesuffix: Azure Private Link
description: Saiba como criar um serviço de Link Privado utilizando o portal Azure neste Quickstart
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: 4dd27d3fb4321c2eecf222136376bfd4b49790bb
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027777"
---
# <a name="quickstart-create-a-private-link-service-using-the-azure-portal"></a>Quickstart: Criar um serviço de Link Privado utilizando o portal Azure

O serviço de vínculo privado do Azure é a referência para seu próprio serviço que é alimentado pelo link privado do Azure. O serviço ou recurso que está a operar por trás do Azure Load Balancer pode ser ativado para acesso a Private Link. Os consumidores do seu serviço podem aceder ao serviço de forma privada a partir dos seus próprios VNets. Neste Quickstart, você vai aprender a criar um serviço de ligação privada usando o portal Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Criar um equilibrador de carga interno

Primeiro, crie uma rede virtual, depois um equilibrador de carga interno para usar com o serviço Azure Private Link.

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta secção, irá criar uma Rede Virtual e a subnet para alojar o equilibrista de carga que é utilizado para aceder ao seu serviço Private Link.


1. No lado do canto superior esquerdo do portal, selecione **criar um recurso** > **rede** > **rede Virtual**.
   
1. Na **criar rede virtual** painel, escreva ou selecione estes valores:
   
   - **Nome**: tipo **MyVNet**.
   - **ResourceGroup**: selecione **criar novo**, em seguida, introduza **MyResourceGroupLB**e selecione **OK**. 
   - **Sub-rede** > **nome**: tipo **MyBackendSubnet**.
   
1. Selecione **Criar**.

   ![Criar rede virtual](../load-balancer/media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-a-standard-load-balancer"></a>Criar um balanceador de carga padrão

Crie um balanceador de carga interno padrão usando o Portal. O nome e endereço IP que cria são automaticamente configurados como front-end de Balanceador de carga.

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Rede** > **Balanceador de Carga**.
   
2. Na guia **noções básicas** da página **criar balanceador de carga** , insira ou selecione as informações a seguir, aceite os padrões para as configurações restantes e, em seguida, selecione **revisar + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | Selecione *MyResourceGroupLB* na caixa de pull-down.|
    | Nome                   | *myLoadBalancer*                                   |
    | Região         | Selecione **E.U.A. Leste 2**.                                        |
    | Tipo          | Selecione **interno**.                                        |
    | SKU           | Selecione **padrão**.                          |
    | Rede virtual           | Selecione *MyVNet*.                          |    
    | Atribuição de endereço IP              | Selecione **Static**.   |
    | Endereço IP privado|Digite um endereço que esteja no espaço de endereço de sua rede virtual e sub-rede, por exemplo *10.3.0.7*.  |

3. Na guia **revisar + criar** , clique em **criar**. 
   

### <a name="create-standard-load-balancer-resources"></a>Criar recursos de balanceador de carga padrão

Nesta seção, você define as configurações do balanceador de carga para um pool de endereços de back-end e uma investigação de integridade e especifica regras de balanceador de carga.

#### <a name="create-a-backend-pool"></a>Criar uma piscina backend

Para distribuir o tráfego aos seus recursos, um conjunto de endereços de backend contém os endereços IP do virtual (NICs) ligados ao Balanceor de Carga. Crie o conjunto de endereços backend *myBackendPool* para incluir recursos para o tráfego de equilíbrio de carga.

1. Selecione **todos os serviços** no menu à esquerda, selecione **todos os recursos**e, em seguida, selecione **myLoadBalancer** na lista de recursos.
2. Em **configurações**, selecione **pools de back-end**e, em seguida, selecione **Adicionar**.
3. Na página **Adicionar um pool de back-end** , em nome, digite *myBackendPool*, como o nome do pool de back-end e, em seguida, selecione **Adicionar**.



#### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Para permitir que o equilibrador de carga monitorize o estado do recurso, utilize uma sonda de saúde. A sonda de saúde adiciona ou remove dinamicamente os recursos da rotação do equilibrador de carga com base na sua resposta aos controlos de saúde. 

**Criar uma sonda de saúde para monitorizar a saúde dos recursos:**

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Sondas de estado de funcionamento** e, em seguida, selecione **Adicionar**.
   
1. Sobre o **adicionar uma sonda de estado de funcionamento** página, escreva ou selecione os seguintes valores:
   
   - **Nome**: tipo **MyHealthProbe**.
   - **Protocolo**: Desmita-se e selecione **TCP**. 
   - **Porta**: tipo **80**. 
   - **Intervalo**: tipo **15**. O intervalo é o número de segundos entre tentativas da sonda.
   - **Limiar de mau estado de funcionamento**: tipo **2**. Este valor é o número de falhas consecutivas da sonda que ocorrem antes de uma VM é considerada em mau estado de funcionamento.
   
1. Selecione **OK**.

#### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra do equilibrador de carga define como o tráfego é distribuído aos recursos. A regra define a configuração de IP Front-end para tráfego de entrada, o conjunto IP de back-end para receber o tráfego e as portas de origem e de destino necessárias. 

A regra de Balanceador de carga com o nome **MyLoadBalancerRule** escuta na porta 80 no front-end **LoadBalancerFrontEnd**. A regra envia o tráfego de rede para o conjunto de endereços de back-end **MyBackendPool**, também na porta 80. 

**Para criar a regra de Balanceador de carga:**

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições,** selecione **regras de equilíbrio de carga,** e, em seguida, selecione **Adicionar**.
   
1. Na página de **regra de equilíbrio de carga Adicionar,** digite ou selecione os seguintes valores, se não estiver já presente:
   
   - **Nome**: tipo **MyLoadBalancerRule**.
   - **Endereço IP de front-end:** tipo **LoadBalancerFrontEnd** se não estiver presente.
   - **Protocolo**: selecione **TCP**.
   - **Porta**: tipo **80**.
   - **Porta de back-end**: tipo **80**.
   - **Conjunto back-end**: selecione **MyBackendPool**.
   - **Sonda de estado de funcionamento**: selecione **MyHealthProbe**. 
   
1. Selecione **OK**.
   
## <a name="create-a-private-link-service"></a>Criar um serviço de vínculo privado

Nesta secção, irá criar um serviço de ligação privada atrás de um equilibrador de carga padrão.

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso** > **rede** > **central de links privado (versão prévia)** . Pode também pesquisar Private Link através da pesquisa do portal.

2. No **Private Link Center - Visão geral,** sobre a opção de "Expor o seu próprio serviço para que outros possam **ligar"** selecione Start.

3. Na **Criar um serviço de ligação privada - Básicos,** insira ou selecione esta informação:

    | Definição           | Valor                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | **Detalhes do projeto:**  |                                                                              |
    | Subscrição      | Selecione a sua subscrição                                                     |
    | Grupo de Recursos    | Selecione *MyResourceGroupLB*                                                     |
    | **DETALHES DA INSTÂNCIA:** |                                                                              |
    | Nome              | Insira *o myPrivateLinkService* |
    | Região            | Selecione *East US 2*                                                         |

4. Selecione **Seguinte: Definições de saída**.

5. Criar **um serviço de ligação privado - Definições de saída, insira**ou selecione esta informação:


    | Definição                           | Valor                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | Load Balancer                     | Selecione *MyLoadBalancer*                                                           |
    | Endereço IP frontend do balanceor de carga | Selecione o endereço IP frontend do MyLoadBalancer                                |
    | Rede virtual NAT source        | Selecione *myVNET*                                                                   |
    | Sub-rede NAT fonte                 | Selecione *myBackendSubnet*                                                          |
    | Ativar proxy TCP proxy v2               | Selecione SIM/NO dependendo se a sua aplicação está à espera do cabeçalho proxy v2 da TCP |
    | Definições privadas de endereço IP       | Configure o método de atribuição e o endereço IP para cada IP NAT                  |

6. Selecione **Seguinte: Segurança de acesso**.

7. Na **Criar um serviço de ligação privada - Segurança de acesso,** insira ou selecione esta informação:

    | Definição                                     | Valor                                                             |
    |---------------------------------------------|-------------------------------------------------------------------|
    | Visibilidade                                  | Escolha *apenas o controlo de acesso baseado em funções*         |
  
8. Selecione **Seguinte: Tags,** em **seguida, Revê + criar** ou escolher o **separador Rever + criar o separador** no topo da página.

9. Reveja as suas informações e selecione **Criar**.

## <a name="clean-up-resources"></a>Limpar recursos 
Quando terminar de usar o serviço de ligação privada, elimine o grupo de recursos para limpar os recursos utilizados neste arranque rápido:

1. Introduza o *myResourceGroupLB* na caixa **de pesquisa** na parte superior do portal e selecione *myResourceGroupLB* a partir dos resultados da pesquisa. 
2. Selecione **Eliminar grupo de recursos**. 
3. Insira MyResource GROUP para **digite o nome do grupo de recursos** e selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um equilibrador de carga azure interno e um serviço de ligação privada. Para saber mais sobre como criar pontos finais privados, consulte [Create Private Endpoints usando o portal Azure](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal).
