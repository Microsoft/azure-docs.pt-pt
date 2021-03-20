---
title: Configuração do balanceador de carga só de saída
titleSuffix: Azure Load Balancer
description: Com este artigo, aprenda sobre como criar um equilibrador de carga interno com NAT de saída
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2020
ms.author: allensu
ms.openlocfilehash: ee264a22de5ce094e8a4c1335ace77cbbba49270
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94694998"
---
# <a name="outbound-only-load-balancer-configuration"></a>Configuração do balanceador de carga só de saída

Utilize uma combinação de equilibradores de carga padrão internos e externos para criar conectividade de saída para VMs atrás de um equilibrador de carga interno. 

Esta configuração fornece NAT de saída para um cenário interno de balanceador de carga, produzindo uma configuração "apenas de saída" para o seu pool de backend.


<p align="center">
  <img src="./media/egress-only/load-balancer-egress-only.png" alt="Figure depicts a egress only load balancer configuration." width="500" title="Rede Virtual NAT">
</p>


<!-- 
:::image type="content" source="./media/egress-only/load-balancer-egress-only.png" alt-text="Figure depicts a egress only load balancer configuration" border="true":::
-->


*Figura: Egress apenas configuração do balançador de carga*

Os passos necessários são:

1. Crie uma rede virtual com um hospedeiro de bastião.
2. Crie uma máquina virtual com apenas um IP privado.
3. Crie os equilibradores de carga padrão internos e públicos.
4. Adicione piscinas de backend aos equilibradores de carga e coloque o VM em cada piscina.
5. Ligue-se ao seu VM através do anfitrião do bastião e:
    1. Conectividade de saída de teste, 
    2. Configure uma regra de saída sobre o equilibrador de carga pública.
    3. Reteste conectividade de saída.

## <a name="create-virtual-network-and-virtual-machine"></a>Criar rede virtual e máquina virtual

Criar uma rede virtual com duas sub-redes:

* Sub-rede primária
* Sub-rede de bastião

Crie uma máquina virtual na nova rede virtual.

### <a name="create-the-virtual-network"></a>Criar a rede virtual

1. [Inicie sessão](https://portal.azure.com) no portal do Azure.

2. No canto superior esquerdo do ecrã, selecione **Criar um recurso > Redes > Rede virtual** ou procure **Rede virtual** na caixa de pesquisa.

2. Na **Criação de rede virtual,** insira ou selecione esta informação no **separador Básicos:**

    | **Definição**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscrição     | selecione a subscrição do Azure                                  |
    | Grupo de Recursos   | Selecione **Criar novo**. </br> Insira **o myResourceGroupLB**. </br> Selecione **OK**. |
    | **Detalhes da instância** |                                                                 |
    | Name             | Insira **myVNet**                                    |
    | Region           | Selecione **East US 2** |

3. Selecione o separador **endereços IP** ou selecione o botão **Seguinte: Endereços IP** na parte inferior da página.

4. No separador **endereços IP,** introduza estas informações:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Espaço de endereço IPv4 | Insira **10.1.0.0/16** |

5. No **nome da sub-rede,** selecione a palavra **predefinição**.

6. Na **sub-rede Editar,** insira esta informação:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Nome da sub-rede | Insira **myBackendSubnet** |
    | Intervalo de endereços da sub-rede | Insira **10.1.0.0/24** |

7. Selecione **Guardar**.

8. Selecione o separador **Segurança.**

9. Em **BastionHost**, selecione **Enable**. Insira esta informação:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Nome de bastião | Insira **myBastionHost** |
    | Espaço de endereço AzureBastionSubnet | Insira **10.1.1.0/24** |
    | Endereço IP Público | Selecione **Criar novo**. </br> Para **nome,** insira **myBastionIP**. </br> Selecione **OK**. |


8. Selecione o **separador 'Rever +' ou** selecionar o botão **'Rever +' criar.**

9. Selecione **Criar**.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. No lado superior esquerdo do portal, selecione **Criar uma** máquina Virtual  >  **compute de** recurso  >  . 
   
2. Na **Criação de uma máquina virtual,** escreva ou selecione os valores no **separador Básicos:**

    | Definição | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscrição | selecione a subscrição do Azure |
    | Grupo de Recursos | Selecione **myResourceGroupLB** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **o myVM** |
    | Region | Selecione **East US 2** |
    | Opções de Disponibilidade | Selecione **Não é necessário um despedimento de infraestrutura** |
    | Imagem | Selecione **o Centro de Dados 2019 do Windows Server 2019** |
    | Instância do Azure Spot | Selecione **Não** |
    | Tamanho | Escolha o tamanho VM ou tome a definição predefinida |
    | **Conta de administrador** |  |
    | Nome de utilizador | Insira um nome de utilizador |
    | Palavra-passe | Introduza uma senha |
    | Confirmar palavra-passe | Reintroduza a palavra-passe |
    | **Regras portuárias de entrada** |  |
    | Portas de entrada públicas | Selecione **Permitir portas selecionadas** |
    | Selecione as portas de entrada | Selecione **RDP (3389)** |

3. Selecione o **separador 'Rede'** ou selecione **Seguinte: Discos** e, em seguida, **seguinte: Networking**.
  
4. No separador Networking, selecione ou introduza:

    | Definição | Valor |
    |-----|------------|
    | **Interface de rede** |  |
    | Rede virtual | **myVNet** |
    | Sub-rede | **myBackendSubnet** |
    | IP público | Selecione **Nenhuma**. |
    | Grupo de segurança de rede NIC | Selecione **Nenhum**|
    | Colocar esta máquina virtual por trás de uma solução de equilíbrio de carga existente? | Selecione **Não** |
   
5. Selecione o separador **Gestão** ou selecione **'Gestão**  >  **Seguinte'.**

6. No separador **Gestão,** selecione ou introduza:
    
    | Definição | Valor |
    |-|-|
    | **Monitorização** |  |
    | Diagnósticos de arranque. | Selecione **Off** |
   
7. Selecione **Rever + criar**. 
  
8. Reveja as definições e, em seguida, **selecione Criar**.

## <a name="create-load-balancers-and-test-connectivity"></a>Criar equilibradores de carga e testar conectividade

Utilize o portal Azure para criar:

* Balanceador de carga interno
* Balanceador de carga público
 
Adicione o seu VM criado à piscina de backend de cada um.  Em seguida, configurará uma configuração para permitir apenas a conectividade de saída a partir do seu VM, testando antes e depois.

### <a name="create-internal-load-balancer"></a>Criar equilibrador de carga interno

1. No lado superior esquerdo do ecrã, selecione **Criar um**  >  equilibrador de carga de **rede de** recursos  >  .

2. No separador Básicos da página **'Criar balançador** de carga', insira ou selecione as **seguintes** informações: 

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | Selecione **myResourceGroupLB** criado no passo anterior.|
    | Name                   | Insira **o meu InternalLoadBalancer**                                   |
    | Region         | Selecione **E.U.A. Leste 2**.                                        |
    | Tipo          | Selecione **Interno**.                                        |
    | SKU           | Selecione **Standard** |
    | Rede virtual | Selecione **myVNet** criado no passo anterior. |
    | Sub-rede  | Selecione **myBackendSubnet** criado no passo anterior. |
    | Atribuição de endereços IP | Selecione **Dynamic**. |

3. Aceite as predefinições para as definições restantes e, em seguida, selecione **Review + create**.

4. No **separador 'Rever + criar',** selecione **Criar.**   

### <a name="create-public-load-balancer"></a>Criar equilibrador de carga pública

1. No lado superior esquerdo do ecrã, selecione **Criar um**  >  equilibrador de carga de **rede de** recursos  >  .

2. No separador Básicos da página **'Criar balançador** de carga', insira ou selecione as **seguintes** informações: 

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | **Selecione Criar novo** e insira o **myResourceGroupLB** na caixa de texto.|
    | Name                   | Insira **myPublicLoadBalancer**                                   |
    | Region         | Selecione **E.U.A. Leste 2**.                                        |
    | Tipo          | Selecione **Público**.                                        |
    | SKU           | Selecione **Standard** |
    | Endereço IP público | Selecione **Criar novo**. |
    | Nome do endereço IP público | **Insira o myFrontendIP** na caixa de texto.|
    | Zona de disponibilidade | Selecione **Zona redundante** |
    | Adicionar um endereço IPv6 público | Selecione **Não**. |

3. Aceite as predefinições para as definições restantes e, em seguida, selecione **Review + create**.

4. No **separador 'Rever + criar',** selecione **Criar.**   
   
### <a name="create-internal-backend-address-pool"></a>Criar piscina interna de endereços de backend

Crie a piscina de endereços **backend myInternalBackendPool**:

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, em seguida, selecione o **myInternalLoadBalancer** da lista de recursos.

2. Em **Definições**, selecione **pools backend** e, em seguida, selecione **Adicionar**.

3. Na página **de pool de backend,** para nome, escreva **myInternalBackendPool,** como o nome para a sua piscina de backend.
 
4. Na **rede Virtual,** selecione **myVNet**.

5. Em **máquinas Virtuais,** selecione **Adicionar** e escolha **o myVM**.
 
6. selecionar **Adicionar**.

### <a name="create-public-backend-address-pool"></a>Criar piscina de endereços de backend público

Crie a piscina de endereços de backend **myPublicBackendPool**:

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, em seguida, selecione **myPublicLoadBalancer** da lista de recursos.

2. Em **Definições**, selecione **pools backend** e, em seguida, selecione **Adicionar**.

3. Na página **de piscina de backend,** para nome, escreva **myPublicBackendPool,** como o nome para a sua piscina de backend.

4. Na **rede Virtual,** selecione **myVNet**.
 
5. Em **máquinas Virtuais,** selecione **Adicionar** e escolha **o myVM**.
 
6. selecionar **Adicionar**.

### <a name="test-connectivity-before-outbound-rule"></a>Testar conectividade antes da regra de saída

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**, e, em seguida, da lista de recursos, selecione **myVM** que está localizado no grupo de recursos **myResourceGroupLB.**

2. Na página **'Vista Geral',** selecione **Connect** e, em seguida, **Bastion**.

4. Introduza o nome de utilizador e a palavra-passe introduzidos durante a criação de VM.

5. Selecione **Ligar**.

6. Abra o Internet Explorer.

7. **https://whatsmyip.org** Insira na barra de endereços.

8. A ligação deve falhar. Por predefinição, o balanceador de carga público padrão [não permite o tráfego de saída sem uma regra de saída definida](load-balancer-overview.md#securebydefault).
 
### <a name="create-a-public-load-balancer-outbound-rule"></a>Criar uma regra de saída do equilibrador de carga pública

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, em seguida, selecione **myPublicLoadBalancer** da lista de recursos.

2. Em **Definições**, selecione **as regras de saída** e, em seguida, selecione **Adicionar**.

3. Utilize estes valores para configurar as regras de saída:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Introduza **o meu OutboundRule**. |
    | Endereço IP frontend | Selecione **LoadBalancerFrontEnd**.|
    | Tempo de 20 minutos (minutos) | Mova o slider para **15 minutos**.|
    | TCP Reset | Selecione **Ativado**.|
    | Conjunto de back-end | Selecione **myPublicBackendPool**.| |
    | Dotação portuária -> Dotação portuária | Selecione **Utilize o número predefinido das portas de saída** |

4. Selecione **Adicionar**.

### <a name="test-connectivity-after-outbound-rule"></a>Testar conectividade após regra de saída

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**, e, em seguida, da lista de recursos, selecione **myVM** que está localizado no grupo de recursos **myResourceGroupLB.**

2. Na página **'Vista Geral',** selecione **Connect** e, em seguida, **Bastion**.

4. Introduza o nome de utilizador e a palavra-passe introduzidos durante a criação de VM.

5. Selecione **Ligar**.

6. Abra o Internet Explorer.

7. **https://whatsmyip.org** Insira na barra de endereços.

8. A ligação deve ter sucesso.

9. O endereço IP apresentado deve ser o endereço IP frontend do **myPublicLoadBalancer**.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos, carregue balanceadores, VM e todos os recursos relacionados. 

Para tal, selecione o grupo de recursos **myResourceGroupLB** e, em seguida, **selecione Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma configuração "apenas de saída" com uma combinação de equilibradores de carga público e interno.  

Esta configuração permite-lhe carregar o equilíbrio do tráfego interno que chega à sua piscina de backend, evitando ainda quaisquer ligações de entrada pública.

- Saiba mais sobre [o Equilibrador de Carga Azure](load-balancer-overview.md).
- Saiba mais [sobre as ligações de saída em Azure.](load-balancer-outbound-connections.md)
- Perguntas [de faq](load-balancer-faqs.md)do balançador de carga .
- Saiba mais sobre [Azure Bastion](../bastion/bastion-overview.md)