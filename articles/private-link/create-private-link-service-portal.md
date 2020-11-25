---
title: Quickstart - Criar um serviço de ligação privada utilizando o portal Azure
titlesuffix: Azure Private Link
description: Saiba como criar um serviço de Ligação Privada utilizando o portal Azure neste arranque rápido
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: 5b7bc8be89068f0d3cf6722c36ae7fd5cc560736
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "96012123"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Quickstart: Criar um serviço de ligação privada utilizando o portal Azure

Um serviço de Ligação Privada Azure refere-se ao seu próprio serviço que é gerido por Private Link. Pode dar acesso ao Link Privado ao serviço ou recurso que opera por trás do Azure Standard Load Balancer. Os consumidores do seu serviço podem aceder-lhe em privado a partir das suas próprias redes virtuais. Neste arranque rápido, aprende-se a criar um serviço de Ligação Privada utilizando o portal Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Criar um balanceador de carga interno

Primeiro, criar uma rede virtual. Em seguida, crie um equilibrador de carga interno para utilizar com o serviço Private Link.

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Nesta secção, vai criar uma rede virtual. Também cria a sub-rede para hospedar o equilibrador de carga que acede ao seu serviço De Ligação Privada.

Nesta secção, deverá substituir os seguintes parâmetros nas etapas com as informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | E.U.A. Leste 2      |
| **\<IPv4-address-space>**   | 10.3.0.0/16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.3.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-standard-load-balancer"></a>Criar um balanceador de carga standard

Utilize o portal para criar um balanceador interno padrão. O nome e o endereço IP que especifique são configurados automaticamente como a extremidade frontal do balançador de carga.

1. No lado superior esquerdo do portal, **selecione Criar um**  >  equilibrador de carga de **rede de** recursos  >  **Load Balancer**.

1. No separador Básicos da página **'Criar balanceador'** insira ou selecione as **seguintes** informações:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | **Subscrição**               | Selecione a sua subscrição.    |
    | **Grupo de recursos**         | Selecione **myResourceGroupLB** da caixa.|
    | **Name**                   | Insira **o myLoadBalancer**.                                   |
    | **Região**         | Selecione **E.U.A. Leste 2**.                                        |
    | **Tipo**          | Selecione **Interno**.                                        |
    | **SKU**           | Selecione **Standard**.                          |
    | **Rede virtual**           | Selecione **myVNet**.                          |
    | **Atribuição de endereços IP**              | Selecione **Estático**.   |
    | **Endereço IP privado**|Insira um endereço que está no espaço de endereço da sua rede virtual e sub-rede. Um exemplo é 10.3.0.7.  |

1. Aceite as predefinições para as definições restantes e, em seguida, selecione **Review + create**

1. No **separador 'Rever + criar',** selecione **Criar.**

### <a name="create-standard-load-balancer-resources"></a>Criar recursos de balanceadores de carga padrão

Nesta secção, irá configurar as definições de balanceador de carga de um conjunto de endereços de back-end e de uma sonda de estado de funcionamento. Também especifica as regras do balançador de carga.

#### <a name="create-a-back-end-pool"></a>Criar uma piscina traseira

Um conjunto de endereços back-end contém os endereços IP dos NICs virtuais ligados ao equilibficador de carga. Esta piscina permite-lhe distribuir tráfego pelos seus recursos. Crie o pool de endereços back-end chamado **myBackendPool** para incluir recursos que carregam o tráfego de equilíbrio.

1. Selecione **Todos os Serviços** do menu mais à esquerda.
1. Selecione **Todos os recursos** e, em seguida, selecione **myLoadBalancer** da lista de recursos.
1. Em **Definições**, selecione **Conjuntos de back-end** e, em seguida, selecione **Adicionar**.
1. Na página **de piscina de backend,** insira **o myBackendPool** como o nome da sua piscina traseira e, em seguida, selecione **Add**.

#### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Utilize uma sonda de saúde para permitir que o balanceador de carga monitorize o estado do recurso. Com base na resposta dos recursos aos controlos de saúde, a sonda de saúde adiciona ou remove dinamicamente os recursos da rotação do balanceador de carga.

Criar uma sonda de saúde para monitorizar a saúde dos recursos:

1. Selecione **todos os recursos** no menu mais à esquerda e, em seguida, selecione **myLoadBalancer** na lista de recursos.

1. Em **Definições**, selecione **Sondas de estado de funcionamento** e, em seguida, selecione **Adicionar**.

1. Na página **'Adicionar uma sonda de saúde',** insira ou selecione os seguintes valores:

   - **Nome**: **Insira o myHealthProbe**.
   - **Protocolo**: Selecione **TCP**.
   - **Porta**: Insira **80**.
   - **Intervalo**: Insira **15**. Este valor é o número de segundos entre tentativas de sonda.
   - **Limiar pouco saudável**: Insira **2**. Este valor é o número de falhas consecutivas da sonda que ocorrem antes de uma máquina virtual ser considerada insalubre.

1. Selecione **OK**.

#### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra do balançador de carga define como o tráfego é distribuído aos recursos. A regra define:

- A configuração IP frontal para o tráfego de entrada.
- O pool IP back-end para receber o tráfego.
- Os portos de origem e destino necessários.

A regra do balanceador de carga chamada **myLoadBalancerRule** ouve a porta 80 na extremidade frontal **LoadBalancerFrontEnd.** A regra envia tráfego de rede para a piscina de endereços back-end **myBackendPool** na mesma porta 80.

Para criar uma regra do balançador de carga:

1. Selecione **todos os recursos** no menu mais à esquerda e, em seguida, selecione **myLoadBalancer** na lista de recursos.

1. Em **Definições**, selecione **regras de equilíbrio de carga e,** em seguida, selecione **Adicionar**.

1. Na página **de regra de equilíbrio de carga** adicionar, insira ou selecione os seguintes valores se ainda não estiverem presentes:

   - **Nome**: **Insira o meu RegraLoadBalancerule**.
   - **Endereço IP frontend:** **Insira LoadBalancerFrontEnd**.
   - **Protocolo**: Selecione **TCP**.
   - **Porta**: Insira **80**.
   - **Porta de backend**: Insira **80**.
   - **Backend pool**: Selecione **myBackendPool**.
   - **Sonda de saúde**: Selecione **myHealthProbe**. 

1. Selecione **OK**.

## <a name="create-a-private-link-service"></a>Criar um serviço de Ligação Privada

Nesta secção, irá criar um serviço de Ligação Privada por trás de um balanceador de carga padrão.

1. Na parte superior esquerda da página no portal Azure, **selecione Criar um** Centro de Ligação Privada de Rede de Recursos  >  **Networking**  >  **(Pré-visualização)**. Também pode utilizar a caixa de pesquisa do portal para procurar o Private Link.

1. In **Private Link Center - Overview**  >  **Expondo o seu próprio serviço para que outros possam ligar,** selecione **Start**.

1. Ao **criar um serviço de ligação privada - Básicos, insira** ou selecione estas informações:

    | Definição           | Valor                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | Detalhes do projeto:  |                                                                              |
    | **Subscrição**      | Selecione a sua subscrição.                                                     |
    | **Grupo de Recursos**    | Selecione **myResourceGroupLB**.                                                    |
    | Detalhes da instância: |                                                                              |
    | **Name**              | Insira **o myPrivateLinkService**. |
    | **Região**            | Selecione **E.U.A. Leste 2**.                                                        |

1. Selecione **Seguinte: Definições de saída**.

1. Sob **criar um serviço de ligação privada - Definições de saída,** introduzir ou selecionar estas informações:

    | Definição                           | Valor                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | **Balanceador de Carga**                     | Selecione **myLoadBalancer**.                                                           |
    | **Endereço IP frontend do balançador de carga** | Selecione o endereço IP frontal do **myLoadBalancer**.                                |
    | **Rede virtual SOURCE NAT**        | Selecione **myVNet**.                                                                   |
    | **Sub-rede NAT de origem**                 | Selecione **myBackendSubnet**.                                                          |
    | **Ativar o proxy v2 da TCP**               | Selecione **SIM** ou **NÃO** dependendo se a sua aplicação espera um cabeçalho V2 de procuração TCP. |
    | **Definições privadas de endereço IP**       | Configure o método de atribuição e o endereço IP para cada IP NAT.                  |

1. Selecione **Seguinte: Segurança de acesso**.

1. No âmbito **da Criação de um serviço de ligação privada - Aceder à segurança,** selecionar **visibilidade** e, em seguida, escolher **apenas o controlo de acesso baseado em funções**.
  
1. **Selecione Seguinte: Tags**  >  **Review + create** or choose the Review + **create** a tab no topo da página.

1. Reveja as suas informações e **selecione Criar**.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar de utilizar o serviço Private Link, elimine o grupo de recursos para limpar os recursos utilizados neste arranque rápido.

1. Introduza o **myResourceGroupLB** na caixa de pesquisa no topo do portal e selecione **o myResourceGroupLB** a partir dos resultados da pesquisa.
1. Selecione **Eliminar grupo de recursos**.
1. No **TIPO O NOME DO GRUPO DE RECURSOS,** insira o **myResourceGroup**.
1. Selecione **Eliminar**.

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, criou um equilibrador de carga Azure interno e um serviço Private Link. Também pode aprender a [criar um ponto final privado utilizando o portal Azure](./create-private-endpoint-portal.md).