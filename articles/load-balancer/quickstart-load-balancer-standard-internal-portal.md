---
title: 'Quickstart: Criar um equilibrador de carga interno - Portal Azure'
titleSuffix: Azure Load Balancer
description: Este quickstart mostra como criar um equilibrador de carga interno utilizando o portal Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a internal load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: efe4753e78c6dd16fa0b6ce3e98dd21ceae52577
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047816"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Quickstart: Criar um equilibrador de carga interno para carregar VMs de equilíbrio utilizando o portal Azure

Começa com o Azure Load Balancer utilizando o portal Azure para criar um equilibrador de carga interno e duas máquinas virtuais.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

---

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-1-create-internal-load-balancer-standard)

>[!NOTE]
>Recomenda-se o balanceador de carga SKU standard para cargas de trabalho de produção.  Para obter mais informações sobre skus, consulte **[skus de balançadores de carga Azure.](skus.md)**

Nesta secção, cria-se um equilibrador de carga que equilibra as máquinas virtuais. 

Quando cria um equilibrador de carga interno, uma rede virtual é configurada como a rede para o equilibrador de carga. 

Um endereço IP privado na rede virtual é configurado como o frontend (nomeado como **LoadBalancerFrontend** por padrão) para o balançador de carga. 

O endereço IP frontend pode ser **estático** ou **dinâmico.**

## <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta secção, irá criar uma rede virtual e uma sub-rede.

1. No canto superior esquerdo do ecrã, selecione **Criar um recurso > Redes > Rede virtual** ou procure **Rede virtual** na caixa de pesquisa.

2. Na **Criação de rede virtual,** insira ou selecione esta informação no **separador Básicos:**

    | **Definição**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscrição     | selecione a subscrição do Azure                                  |
    | Grupo de Recursos   | Selecione **myResourceGroupLB** |
    | **Detalhes da instância** |                                                                 |
    | Name             | Insira **myVNet**                                    |
    | Região           | Selecione **Europa Ocidental** |

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

## <a name="create-load-balancer"></a>Criar um balanceador de carga

1. No lado superior esquerdo do ecrã, selecione **Criar um**  >  equilibrador de carga de**rede de**recursos  >  **Load Balancer**.

2. No separador Básicos da página **'Criar balançador** de carga', insira ou selecione as **seguintes** informações: 

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | Selecione **myResourceGroupLB** criado no passo anterior.|
    | Nome                   | Insira **o myLoadBalancer**                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Tipo          | Selecione **Interno**.                                        |
    | SKU           | Selecione **Standard** |
    | Rede virtual | Selecione **myVNet** criado no passo anterior. |
    | Sub-rede  | Selecione **myBackendSubnet** criado no passo anterior. |
    | Atribuição de endereços IP | Selecione **Dynamic**. |
    | Zona de disponibilidade | Selecione **Zona redundante** |

3. Aceite as predefinições para as definições restantes e, em seguida, selecione **Review + create**.

4. No **separador 'Rever + criar',** selecione **Criar.**   
    
    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-standard-internal-load-balancer.png" alt-text="Crie um equilibrador de carga interno padrão." border="true":::
 
## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta secção, configura:

* Ajustes do balançador de carga para um pool de endereço de backend.
* Uma sonda de saúde.
* Uma regra do equilíbrio de carga.

### <a name="create-a-backend-pool"></a>Criar um conjunto de back-ends

Um conjunto de endereços de backend contém os endereços IP dos (NICs) virtuais ligados ao equilibrador de carga. 

Crie o pool de endereços de backend **myBackendPool** para incluir máquinas virtuais para equilibrar o tráfego de internet.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** na lista de recursos.

2. Em **Definições**, selecione **pools backend**e, em seguida, selecione **Adicionar**.

3. Na página de **piscina de backend,** para nome, escreva **myBackendPool,** como o nome da sua piscina de backend e, em seguida, selecione **Add**.

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

O equilibrador de carga monitoriza o estado da sua aplicação com uma sonda de saúde. 

A sonda de saúde adiciona ou remove VMs do equilibrador de carga com base na sua resposta aos controlos de saúde. 

Crie uma sonda de estado de funcionamento com o nome **myHealthProbe**, para monitorizar o estado de funcionamento das VMs.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** na lista de recursos.

2. Em **Definições**, selecione **sondas de saúde**e, em seguida, selecione **Adicionar**.
    
    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira **o myHealthProbe**. |
    | Protocolo | Selecione **HTTP**. |
    | Porta | Insira **80**.|
    | Intervalo | Introduza **15** para o número de **intervalos** em segundos entre tentativas de sonda. |
    | Limiar com funcionamento incorreto | Selecione **2** para o número de **falhas de sonda insalubres** ou falhas de sonda consecutivas que devem ocorrer antes de um VM ser considerado insalubre.|
    | | |

3. Deixe o resto as predefinições e Selecione **OK**.

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

É utilizada uma regra de balanceador de carga para definir a forma como o tráfego é distribuído pelas VMs. Você define a configuração IP frontend para o tráfego de entrada e o pool IP backend para receber o tráfego. A origem e a porta de destino estão definidas na regra. 

Nesta secção, irá criar uma regra do balançador de carga:

* Chamado **myHTTPRule.**
* Na frontend chamada **LoadBalancerFrontEnd**.
* Escuta no **Porto 80**.
* Direciona o tráfego equilibrado para o backendpool chamado **myBackendPool** na **Porta 80**.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** na lista de recursos.

2. Em **Definições**, selecione **regras de equilíbrio de carga**e, em seguida, selecione **Adicionar**.

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
    | Criar regras implícitas de saída | Selecione **Não**.

4. Deixe o resto dos predefinidos e, em seguida, selecione **OK**.

>[!NOTE]
>As máquinas virtuais no pool de backend não terão conectividade de internet de saída com esta configuração. </br> Para obter mais informações sobre o fornecimento de conectividade de saída, consulte: </br> **[Ligações de saída no Azure](load-balancer-outbound-connections.md)**</br> Opções para fornecer conectividade: </br> **[Configuração do balanceador de carga só de saída](egress-only.md)** </br> **[O que é Virtual Network NAT?](https://docs.microsoft.com/azure/virtual-network/nat-overview)**

## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta secção, pode:

* Crie duas máquinas virtuais para o pool de backend do equilibrador de carga.
* Instale o IIS nas máquinas virtuais para testar o equilibrador de carga.

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Nesta secção, irá criar dois VMs (**myVM1** e **myVM2).**

Estes VMs são adicionados ao pool de backend do equilibrador de carga que foi criado anteriormente.

1. No lado superior esquerdo do portal, selecione **Criar uma**máquina Virtual  >  **compute de**recurso  >  **Virtual machine**. 
   
2. Na **Criação de uma máquina virtual,** escreva ou selecione os valores no **separador Básicos:**

    | Definição | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscrição | selecione a subscrição do Azure |
    | Grupo de Recursos | Selecione **myResourceGroupLB** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **o myVM1** |
    | Região | Selecione **Europa Ocidental** |
    | Opções de Disponibilidade | Selecione **zonas de disponibilidade** |
    | Zona de disponibilidade | Selecione **1** |
    | Imagem | Selecione **o Centro de Dados 2019 do Windows Server 2019** |
    | Instância do Azure Spot | Selecione **Não** |
    | Tamanho | Escolha o tamanho VM ou tome a definição predefinida |
    | **Conta de administrador** |  |
    | Nome de utilizador | Insira um nome de utilizador |
    | Palavra-passe | Introduza uma senha |
    | Confirmar palavra-passe | Reintroduza a palavra-passe |

3. Selecione o **separador 'Rede'** ou selecione **Seguinte: Discos**e, em seguida, **seguinte: Networking**.
  
4. No separador Networking, selecione ou introduza:

    | Definição | Valor |
    |-|-|
    | **Interface de rede** |  |
    | Rede virtual | **myVNet** |
    | Sub-rede | **myBackendSubnet** |
    | IP público | Selecione **Nenhum** |
    | Grupo de segurança de rede NIC | Selecione **Avançado**|
    | Configure grupo de segurança de rede | Selecione **Criar novo**. </br> No **grupo de segurança**de rede Create , insira **o myNSG** em **Nome**. </br> Selecione **OK** |
    | **Balanceamento de carga**  |
    | Colocar esta máquina virtual por trás de uma solução de equilíbrio de carga existente? | Selecione **Sim** |
    | **Definições de equilíbrio de carga** |
    | Opções de equilíbrio de carga | Selecione **o equilíbrio da carga Azure** |
    | Selecione um equilibrador de carga | Selecione **myLoadBalancer**  |
    | Selecione uma piscina de backend | Selecione **myBackendPool** |
   
5. Selecione **Rever + criar**. 
  
6. Reveja as definições e, em seguida, **selecione Criar**.

7. Siga os passos 1 a 8 para criar um VM adicional com os seguintes valores e todas as outras definições iguais ao **myVM1**:

    | Definição | VM 2|
    | ------- | ----- |
    | Nome |  **myVM2** |
    | Zona de disponibilidade | **2** |
    | Grupo de segurança de rede | Selecione o **myNSG** existente|


# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-1-create-internal-load-balancer-basic)

>[!NOTE]
>Recomenda-se o balanceador de carga SKU standard para cargas de trabalho de produção.  Para obter mais informações sobre skus, consulte **[skus de balançadores de carga Azure.](skus.md)**

Nesta secção, cria-se um equilibrador de carga que equilibra as máquinas virtuais. 

Quando cria um equilibrador de carga interno, uma rede virtual é configurada como a rede para o equilibrador de carga. 

Um endereço IP privado na rede virtual é configurado como o frontend (nomeado como **LoadBalancerFrontend** por padrão) para o balançador de carga. 

O endereço IP frontend pode ser **estático** ou **dinâmico.**

## <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta secção, irá criar uma rede virtual e uma sub-rede.

1. No canto superior esquerdo do ecrã, selecione **Criar um recurso > Redes > Rede virtual** ou procure **Rede virtual** na caixa de pesquisa.

2. Na **Criação de rede virtual,** insira ou selecione esta informação no **separador Básicos:**

    | **Definição**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscrição     | selecione a subscrição do Azure                                  |
    | Grupo de Recursos   | Selecione **myResourceGroupLB** |
    | **Detalhes da instância** |                                                                 |
    | Name             | Insira **myVNet**                                    |
    | Região           | Selecione **Europa Ocidental** |

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

## <a name="create-load-balancer"></a>Criar um balanceador de carga

1. No lado superior esquerdo do ecrã, selecione **Criar um**  >  equilibrador de carga de**rede de**recursos  >  **Load Balancer**.

2. No separador Básicos da página **'Criar balançador** de carga', insira ou selecione as **seguintes** informações: 

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | Selecione **myResourceGroupLB** criado no passo anterior.|
    | Nome                   | Insira **o myLoadBalancer**                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Tipo          | Selecione **Interno**.                                        |
    | SKU           | Selecione **Basic** |
    | Rede virtual | Selecione **myVNet** criado no passo anterior. |
    | Sub-rede  | Selecione **myBackendSubnet** criado no passo anterior. |
    | Atribuição de endereços IP | Selecione **Dynamic**. |

3. Aceite as predefinições para as definições restantes e, em seguida, selecione **Review + create**.

4. No **separador 'Rever + criar',** selecione **Criar.**   

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-basic-internal-load-balancer.png" alt-text="Crie um equilibrador de carga interno padrão." border="true":::

## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta secção, configura:

* Ajustes do balançador de carga para um pool de endereço de backend.
* Uma sonda de saúde.
* Uma regra do equilíbrio de carga.

### <a name="create-a-backend-pool"></a>Criar um conjunto de back-ends

Um conjunto de endereços de backend contém os endereços IP dos (NICs) virtuais ligados ao equilibrador de carga. 

Crie o pool de endereços de backend **myBackendPool** para incluir máquinas virtuais para equilibrar o tráfego de internet.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** na lista de recursos.

2. Em **Definições**, selecione **pools backend**e, em seguida, selecione **Adicionar**.

3. Na página De reserva da **piscina,** insira ou selecione:
    
    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira **myBackendPool**. |
    | Rede virtual | Selecione **myVNet**. |
    | Associado a | Selecione **máquinas virtuais** |

4. Selecione **Adicionar**.

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

O equilibrador de carga monitoriza o estado da sua aplicação com uma sonda de saúde. 

A sonda de saúde adiciona ou remove VMs do equilibrador de carga com base na sua resposta aos controlos de saúde. 

Crie uma sonda de estado de funcionamento com o nome **myHealthProbe**, para monitorizar o estado de funcionamento das VMs.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** na lista de recursos.

2. Em **Definições**, selecione **sondas de saúde**e, em seguida, selecione **Adicionar**.
    
    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira **o myHealthProbe**. |
    | Protocolo | Selecione **HTTP**. |
    | Porta | Insira **80**.|
    | Caminho | Inserir **/** |
    | Intervalo | Introduza **15** para o número de **intervalos** em segundos entre tentativas de sonda. |
    | Limiar com funcionamento incorreto | Selecione **2** para o número de **falhas de sonda insalubres** ou falhas de sonda consecutivas que devem ocorrer antes de um VM ser considerado insalubre.|

3. Selecione **OK**.

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

É utilizada uma regra de balanceador de carga para definir a forma como o tráfego é distribuído pelas VMs. Você define a configuração IP frontend para o tráfego de entrada e o pool IP backend para receber o tráfego. A origem e a porta de destino estão definidas na regra. 

Nesta secção, irá criar uma regra do balançador de carga:

* Chamado **myHTTPRule.**
* Na frontend chamada **LoadBalancerFrontEnd**.
* Escuta no **Porto 80**.
* Direciona o tráfego equilibrado para o backendpool chamado **myBackendPool** na **Porta 80**.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** na lista de recursos.

2. Em **Definições**, selecione **regras de equilíbrio de carga**e, em seguida, selecione **Adicionar**.

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
 
4. Deixe o resto dos predefinidos e, em seguida, selecione **OK**.

## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta secção, pode:

* Crie duas máquinas virtuais para o pool de backend do equilibrador de carga.
* Crie um conjunto de disponibilidade para as máquinas virtuais.
* Instale o IIS nas máquinas virtuais para testar o equilibrador de carga.

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Nesta secção, irá criar dois VMs (**myVM1**, e **myVM2).**

Os dois VMs serão adicionados a um conjunto de disponibilidade chamado **myAvailabilitySet**.

Estes VMs são adicionados ao pool de backend do equilibrador de carga que foi criado anteriormente.

1. No lado superior esquerdo do portal, selecione **Criar uma**máquina Virtual  >  **compute de**recurso  >  **Virtual machine**. 
   
2. Na **Criação de uma máquina virtual,** escreva ou selecione os valores no **separador Básicos:**

    | Definição | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscrição | selecione a subscrição do Azure |
    | Grupo de Recursos | Selecione **myResourceGroupLB** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **o myVM1** |
    | Região | Selecione **Europa Ocidental** |
    | Opções de Disponibilidade | Selecione **Conjunto de disponibilidade** |
    | Conjunto de disponibilidade | Selecione **Criar novo**. </br> Insira **o mySilabilitySet** no **nome**. </br> Selecione **OK** |
    | Imagem | **Windows Server 2019 Datacenter** |
    | Instância do Azure Spot | Selecione **Não** |
    | Tamanho | Escolha o tamanho VM ou tome a definição predefinida |
    | **Conta de administrador** |  |
    | Nome de utilizador | Insira um nome de utilizador |
    | Palavra-passe | Introduza uma senha |
    | Confirmar palavra-passe | Reintroduza a palavra-passe |

3. Selecione o **separador 'Rede'** ou selecione **Seguinte: Discos**e, em seguida, **seguinte: Networking**.
  
4. No separador Networking, selecione ou introduza:

    | Definição | Valor |
    |-|-|
    | **Interface de rede** |  |
    | Rede virtual | Selecione **myVNet** |
    | Sub-rede | Selecione **myBackendSubnet** |
    | IP público | Selecione **Nenhum** |
    | Grupo de segurança de rede NIC | Selecione **Avançado**|
    | Configure grupo de segurança de rede | Selecione **Criar novo**. </br> No **grupo de segurança**de rede Create , insira **o myNSG** em **Nome**. </br> Selecione **OK** |
    | **Balanceamento de carga**  |
    | Colocar esta máquina virtual por trás de uma solução de equilíbrio de carga existente? | Selecione **Não** |

5. Selecione **Rever + criar**. 
  
6. Reveja as definições e, em seguida, **selecione Criar**.

7. Siga os passos 1 a 8 para criar um VM adicional com os seguintes valores e todas as outras definições iguais ao **myVM1**:

    | Definição | VM 2 |
    | ------- | ----- |
    | Nome |  **myVM2** |
    | Conjunto de disponibilidade| Selecione **mySIlabilitySet** |
    | Grupo de segurança de rede | Selecione o **myNSG** existente|

### <a name="add-virtual-machines-to-the-backend-pool"></a>Adicione máquinas virtuais à piscina de backend

Os VMs criados nos passos anteriores devem ser adicionados ao pool de backend do **myLoadBalancer**.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** na lista de recursos.

2. Em **Definições**, selecione **backend pools**e, em seguida, selecione **myBackendPool**.

3. Selecione **máquinas virtuais** em **Associado a**.

4. Na secção **máquinas Virtuais,** selecione **+ Adicionar**.

5. Selecione as caixas ao lado **do myVM1** e **myVM2**.

6. Selecione **Adicionar**.

7. Selecione **Guardar**.
---

## <a name="create-test-virtual-machine"></a>Criar máquina virtual de teste

Nesta secção, irá criar um VM chamado **myTestVM**.  Este VM será utilizado para testar a configuração do balançador de carga.

1. No lado superior esquerdo do portal, selecione **Criar uma**máquina Virtual  >  **compute de**recurso  >  **Virtual machine**. 
   
2. Na **Criação de uma máquina virtual,** escreva ou selecione os valores no **separador Básicos:**

    | Definição | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscrição | selecione a subscrição do Azure |
    | Grupo de Recursos | Selecione **myResourceGroupLB** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **o myTestVM** |
    | Região | Selecione **Europa Ocidental** |
    | Opções de Disponibilidade | Selecione **Não é necessário um despedimento de infraestrutura** |
    | Imagem | Selecione **o Centro de Dados 2019 do Windows Server 2019** |
    | Instância do Azure Spot | Selecione **Não** |
    | Tamanho | Escolha o tamanho VM ou tome a definição predefinida |
    | **Conta de administrador** |  |
    | Nome de utilizador | Insira um nome de utilizador |
    | Palavra-passe | Introduza uma senha |
    | Confirmar palavra-passe | Reintroduza a palavra-passe |

3. Selecione o **separador 'Rede'** ou selecione **Seguinte: Discos**e, em seguida, **seguinte: Networking**.
  
4. No separador Networking, selecione ou introduza:

    | Definição | Valor |
    |-|-|
    | **Interface de rede** |  |
    | Rede virtual | **myVNet** |
    | Sub-rede | **myBackendSubnet** |
    | IP público | Selecione **Nenhuma**. |
    | Grupo de segurança de rede NIC | Selecione **Avançado**|
    | Configure grupo de segurança de rede | Selecione **MyNSG** criado no passo anterior.|
       
5. Selecione **Rever + criar**. 
  
6. Reveja as definições e, em seguida, **selecione Criar**.

## <a name="install-iis"></a>Instalar o IIS

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**, e, em seguida, a partir da lista de recursos, selecione **myVM1** que está localizado no grupo de recursos **myResourceGroupLB.**

2. Na página **'Vista Geral',** selecione **Connect**e, em seguida, **Bastion**.

4. Introduza o nome de utilizador e a palavra-passe introduzidos durante a criação de VM.

5. Selecione **Ligar**.

6. No ambiente de trabalho do servidor, navegue para o **Windows Administrative Tools**Windows  >  **PowerShell**.

7. Na Janela PowerShell, executar os seguintes comandos para:

    * Instale o servidor IIS
    * Remova o ficheiro de iisstart.htm predefinido
    * Adicione um novo ficheiro iisstart.htm que exibe o nome do VM:

   ```powershell
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. Feche a sessão de Bastião com **o myVM1**.

9. Repita os passos 1 a 6 para instalar o IIS e o ficheiro iisstart.htm atualizado em **myVM2**.


## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

1. Encontre o endereço IP privado para o balançador de carga no ecrã **de visão geral.** Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer**.

2. Tome nota ou copie o endereço ao lado **do Endereço IP Privado** na **visão geral** do **myLoadBalancer**.

3. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**, e, em seguida, a partir da lista de recursos, selecione **myTestVM** que está localizado no grupo de recursos **myResourceGroupLB.**

4. Na página **'Vista Geral',** selecione **Connect**e, em seguida, **Bastion**.

6. Introduza o nome de utilizador e a palavra-passe introduzidos durante a criação de VM.

7. Abrir **internet Explorer** no **myTestVM**.

8. Introduza o endereço IP a partir do passo anterior na barra de endereços do navegador. A página predefinida do servidor Web do IIS é apresentada no browser.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Crie um equilibrador de carga interno padrão." border="true":::
   
Para ver o balanceador de carga distribuir tráfego em ambos os VMs, pode personalizar a página padrão de cada servidor Web IIS de cada VM e, em seguida, refrescar o seu navegador web a partir da máquina do cliente.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, o balanceador de carga e todos os recursos relacionados. Para tal, selecione o grupo de recursos **myResourceGroupLB** que contém os recursos e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, irá:

* Criei um equilibrador interno padrão Azure ou básico
* Fixado 2 VMs ao equilibrador de carga.
* Configurar a regra de tráfego do balanceador de carga, a sonda de saúde, e depois testou o equilibrador de carga. 

Para saber mais sobre o Azure Load Balancer, continue a.
> [!div class="nextstepaction"]
> [What is Azure Load Balancer?](load-balancer-overview.md) (O que é o Balanceador de Carga do Azure?) 
