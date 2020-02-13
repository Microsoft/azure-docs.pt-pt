---
title: Quickstart - Criar um serviço de Link Privado utilizando o portal Azure
titlesuffix: Azure Private Link
description: Saiba como criar um serviço de Link Privado utilizando o portal Azure neste arranque rápido
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: e316da12345c0bf1ea3682dadb1a7a65f250747b
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191092"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Quickstart: Criar um serviço de Link Privado utilizando o portal Azure

Um serviço Azure Private Link refere-se ao seu próprio serviço que é gerido pela Private Link. Pode dar acesso ao Private Link ao serviço ou recurso que opera por trás do Azure Standard Load Balancer. Os consumidores do seu serviço podem aceder-lhe de forma privada a partir das suas próprias redes virtuais. Neste arranque rápido, aprende-se a criar um serviço Private Link utilizando o portal Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Criar um balanceador de carga interno

Primeiro, criar uma rede virtual. Em seguida, crie um equilibrador de carga interno para utilizar com o serviço Private Link.

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta secção, vai criar uma rede virtual. Também cria a subnet para acolher o equilibrador de carga que acede ao seu serviço Private Link.

1. No lado superior esquerdo do portal, selecione **Criar um recurso** > rede de **rede** > **rede virtual.**

1. No painel de **rede virtual Criar,** insira ou selecione estes valores:

   - **Nome**: Insira **myVNet**.
   - **ResourceGroup**: **Selecione Criar novo,** introduza **o meu ResourceGroupLB,** e selecione **OK**.
   - **Subnet** > **Nome**: Introduza **myBackendSubnet**.

1. Selecione **Criar**.

   ![Criar uma rede virtual](../load-balancer/media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-a-standard-load-balancer"></a>Criar um balanceador de carga standard

Utilize o portal para criar um equilibrador de carga interno padrão. O nome e o endereço IP que especifica são automaticamente configurados como a extremidade frontal do equilibrador de carga.

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Rede** > **Balanceador de Carga**.

1. No separador Basics da página **Criar balanceadores** de carga, introduza ou selecione as **seguintes** informações:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | **Subscrição**               | Selecione a sua subscrição.    |
    | **Grupo de recursos**         | Selecione **myResourceGroupLB** da caixa.|
    | **Nome**                   | Introduza **o myLoadBalancer**.                                   |
    | **Região**         | Selecione **E.U.A. Leste 2**.                                        |
    | **Tipo**          | Selecione **Internal**.                                        |
    | **SKU**           | Selecione **Standard**.                          |
    | **Rede virtual**           | Selecione **myVNet**.                          |
    | **Atribuição de endereçoIP**              | Selecione **Static**.   |
    | **Endereço IP privado**|Introduza um endereço que esteja no espaço de endereço da sua rede virtual e sub-rede. Um exemplo é 10.3.0.7.  |

1. Aceite as predefinições para as definições restantes e, em seguida, selecione **Rever + criar**

1. No **separador Review + criar,** selecione **Criar**.

### <a name="create-standard-load-balancer-resources"></a>Criar recursos de equilíbrio de carga padrão

Nesta secção, irá configurar as definições de balanceador de carga de um conjunto de endereços de back-end e de uma sonda de estado de funcionamento. Especifica também as regras do equilibrador de carga.

#### <a name="create-a-back-end-pool"></a>Criar uma piscina de back-end

Um conjunto de endereços de back-end contém os endereços IP dos NICs virtuais ligados ao equilibrador de carga. Esta piscina permite-lhe distribuir o tráfego pelos seus recursos. Crie o conjunto de endereços back-end chamado **myBackendPool** para incluir recursos que equilibrem o tráfego de equilíbrio.

1. Selecione **Todos os Serviços** do menu mais à esquerda.
1. **Selecione Todos os recursos**e, em seguida, selecione **myLoadBalancer** na lista de recursos.
1. Em **Definições**, selecione **Conjuntos de back-end** e, em seguida, selecione **Adicionar**.
1. Na página adicionar uma página de **piscina de backend,** introduza **myBackendPool** como o nome para a sua piscina de back-end e, em seguida, selecione **Adicionar**.

#### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Utilize uma sonda de saúde para permitir que o equilibrador de carga monitorize o estado do recurso. Com base na resposta dos recursos aos controlos de saúde, a sonda de saúde adiciona ou remove os recursos da rotação do equilíbrio de carga.

Criar uma sonda de saúde para monitorizar a saúde dos recursos:

1. **Selecione todos os recursos** no menu mais à esquerda e, em seguida, selecione **myLoadBalancer** da lista de recursos.

1. Em **Definições**, selecione **Sondas de estado de funcionamento** e, em seguida, selecione **Adicionar**.

1. Na página Adicionar uma sonda de **saúde,** introduza ou selecione os seguintes valores:

   - **Nome**: Insira **a minha HealthProbe**.
   - **Protocolo**: Selecione **TCP**.
   - **Porta**: Entrar **80**.
   - **Intervalo**: Insira **15**. Este valor é o número de segundos entre as tentativas de sonda.
   - **Limiar pouco saudável**: Entrar **2**. Este valor é o número de falhas de sonda consecutivas que ocorrem antes de uma máquina virtual ser considerada insalubre.

1. Selecione **OK**.

#### <a name="create-a-load-balancer-rule"></a>Crie uma regra de balanceador de carga

Uma regra do equilibrador de carga define como o tráfego é distribuído aos recursos. A regra define:

- A configuração IP frontal para o tráfego de entrada.
- A piscina IP traseira para receber o tráfego.
- As portas de origem e destino necessárias.

A regra do equilíbrio de carga denominada **myLoadBalancerRule** ouve a porta 80 na extremidade frontal **LoadBalancerFrontEnd.** A regra envia tráfego de rede para a piscina de endereços back-end **myBackendPool** na mesma porta 80.

Para criar uma regra de equilíbrio de carga:

1. **Selecione todos os recursos** no menu mais à esquerda e, em seguida, selecione **myLoadBalancer** da lista de recursos.

1. Em **Definições,** selecione **regras de equilíbrio de carga,** e, em seguida, selecione **Adicionar**.

1. Na página de **regra de equilíbrio de carga adicionar,** introduza ou selecione os seguintes valores se ainda não estiverem presentes:

   - **Nome**: Introduza **myLoadBalancerRule**.
   - **Endereço IP frontend:** Introduza **loadBalancerFrontEnd**.
   - **Protocolo**: Selecione **TCP**.
   - **Porta**: Entrar **80**.
   - **Porta de backend**: Entrar **80**.
   - **Piscina de backend**: Selecione **myBackendPool**.
   - **Sonda de saúde**: Selecione **myHealthProbe**. 

1. Selecione **OK**.

## <a name="create-a-private-link-service"></a>Criar um serviço de Ligação Privada

Nesta secção, cria um serviço Private Link atrás de um equilibrador de carga padrão.

1. Na parte superior esquerda da página no portal Azure, selecione **Criar um recurso** > **Networking** > **Private Link Center (Pré-visualização)** . Também pode utilizar a caixa de pesquisa do portal para procurar o Private Link.

1. No **Private Link Center - Visão geral** > Exponha o seu próprio serviço para que outros possam **ligar,** selecione **Iniciar**.

1. Sob a Criação de um serviço de **ligação privada - Básicos,** insira ou selecione esta informação:

    | Definição           | Valor                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | Detalhes do projeto:  |                                                                              |
    | **Subscrição**      | Selecione a sua subscrição.                                                     |
    | **Grupo de Recursos**    | Selecione **myResourceGroupLB**.                                                    |
    | Detalhes da instância: |                                                                              |
    | **Nome**              | Insira **o myPrivateLinkService**. |
    | **Região**            | Selecione **E.U.A. Leste 2**.                                                        |

1. Selecione **Seguinte: Definições de saída**.

1. Sob a Criação de um serviço de **ligação privada - Definições de saída,** insira ou selecione esta informação:

    | Definição                           | Valor                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | **Balanceador de Carga**                     | Selecione **myLoadBalancer**.                                                           |
    | **Endereço IP frontend do balanceor de carga** | Selecione o endereço IP frontal do **myLoadBalancer**.                                |
    | **Rede virtual NAT source**        | Selecione **myVNet**.                                                                   |
    | **Sub-rede NAT fonte**                 | Selecione **myBackendSubnet**.                                                          |
    | **Ativar proxy TCP proxy v2**               | Selecione **SIM** ou **NÃO** dependendo se a sua aplicação espera um cabeçalho v2 proxy TCP. |
    | **Definições privadas de endereço IP**       | Configure o método de atribuição e o endereço IP para cada IP NAT.                  |

1. Selecione **Seguinte: Segurança de acesso**.

1. Em Criar um serviço de **ligação privada - Segurança de acesso,** selecione **Visibilidade,** e depois escolha **apenas o controlo de acesso baseado em funções**.
  
1. Selecione **Seguinte: Tags** > **Rever + criar** ou escolher o separador Rever + **criar** o separador no topo da página.

1. Reveja as suas informações e selecione **Criar**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar a utilização do serviço Private Link, elimine o grupo de recursos para limpar os recursos utilizados neste arranque rápido.

1. Introduza o **myResourceGroupLB** na caixa de pesquisa na parte superior do portal e selecione **myResourceGroupLB** dos resultados de pesquisa.
1. Selecione **Eliminar grupo de recursos**.
1. Em **TIPO O NOME DO GRUPO DE RECURSOS,** insira o **myResourceGroup**.
1. Selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um equilibrador de carga azure interno e um serviço Private Link. Também pode aprender a [criar um ponto final privado utilizando o portal Azure.](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)
