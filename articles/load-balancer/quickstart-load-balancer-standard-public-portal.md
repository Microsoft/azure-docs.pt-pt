---
title: 'Quickstart: Criar um equilibrador de carga pública - portal Azure'
titleSuffix: Azure Load Balancer
description: Este quickstart mostra como criar um equilibrador de carga utilizando o portal Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: eb23f1e703c2e447c484ccb366914cb4b23c5bf7
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536556"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Quickstart: Criar um equilibrador de carga para carregar VMs de equilíbrio utilizando o portal Azure

Começa com o Azure Load Balancer utilizando o portal Azure para criar um equilibrador de carga pública e três máquinas virtuais.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

---

# <a name="option-1-default-create-a-load-balancer-standard-sku"></a>[Opção 1 (padrão): Criar um equilibrador de carga (Standard SKU)](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Recomenda-se o balanceador de carga SKU standard para cargas de trabalho de produção.  Para obter mais informações sobre skus, consulte **[skus de balançadores de carga Azure.](skus.md)**

Nesta secção, cria-se um equilibrador de carga que equilibra as máquinas virtuais. 

Pode criar um equilibrador de carga pública ou um equilibrador interno de carga. 

Quando cria um equilibrador de carga público, cria um novo endereço IP público configurado como frontend (denominado **LoadBalancerFrontend** por padrão) para o equilibrador de carga.

1. No lado superior esquerdo do ecrã, selecione **Criar um**  >  equilibrador de carga de**rede de**recursos  >  **Load Balancer**.

2. No separador Básicos da página **'Criar balançador** de carga', insira ou selecione as **seguintes** informações: 

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | **Selecione Criar novo** e insira o **myResourceGroupLB** na caixa de texto.|
    | Name                   | Insira **o myLoadBalancer**                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Tipo          | Selecione **Público**.                                        |
    | SKU           | Selecione **Standard** |
    | Endereço IP público | Selecione **Criar novo**. Se tiver um IP público existente que gostaria de utilizar, selecione **Utilizar a existência**. |
    | Nome do endereço IP público | Digite **o myPublicIP** na caixa de texto.|
    | Zona de disponibilidade | Selecione **Zona redundante** para criar um equilibrador de carga resistente. Para criar um equilibrador de carga zonal, selecione uma zona específica a partir de 1, 2 ou 3 |
    | Adicionar um endereço IPv6 público | Selecione **Não**. </br> Para obter mais informações sobre endereços IPv6 e balanceador de carga, consulte [o que é o IPv6 para a Rede Virtual Azure?](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)  |

3. Aceite as predefinições para as definições restantes e, em seguida, selecione **Review + create**.

4. No **separador 'Rever + criar',** selecione **Criar.**   
    
    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png" alt-text="Criar um balanceador de carga standard" border="true":::
 
## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta secção, configura:

* Ajustes do balançador de carga para um pool de endereço de backend.
* Uma sonda de saúde.
* Uma regra do balançador de carga e uma regra de saída automática.

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
    | Criar regras implícitas de saída | Selecione **Sim**. </br> Para obter mais informações e configuração avançada de regras de saída, consulte: </br> [Ligações de saída no Azure](load-balancer-outbound-connections.md) </br> [Configure as regras de equilíbrio e saída de carga no Balanceador de Carga Padrão utilizando o portal Azure](configure-load-balancer-outbound-portal.md)

4. Deixe o resto dos predefinidos e, em seguida, selecione **OK**.

## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta secção, pode:

* Crie uma rede virtual
* Crie três máquinas virtuais para o pool de backend do equilibrador de carga.
* Instale o IIS nas máquinas virtuais para testar o equilibrador de carga.

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Nesta secção, substituirá os parâmetros nos passos com as informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Europa Ocidental      |
| **\<IPv4-address-space>**   | 10.1.0.0\16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Os SKUs IP públicos e os SKUs do balançador de carga devem coincidir. Para o balanceador de carga padrão, utilize VMs com endereços IP padrão no pool de backend. 

Nesta secção, você vai criar três VMs (**myVM1,** **myVM2** e **myVM3**) com um endereço IP público padrão em três zonas diferentes **(Zona 1,** **Zona 2**e **Zona 3).** 

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
    | Confirmar palavra-passe | Senha de reentrada |

3. Selecione o **separador 'Rede'** ou selecione **Seguinte: Discos**e, em seguida, **seguinte: Networking**.
  
4. No separador Networking, selecione ou introduza:

    | Definição | Valor |
    |-|-|
    | **Interface de rede** |  |
    | Rede virtual | **myVNet** |
    | Subrede | **myBackendSubnet** |
    | IP público | Aceite o padrão do **myVM-ip**. </br> IP será automaticamente um SKU IP padrão na Zona 1. |
    | Grupo de segurança de rede NIC | Selecione **Avançado**|
    | Configure grupo de segurança de rede | Selecione **Criar novo**. </br> No **grupo de segurança**de rede Create , insira **o myNSG** em **Nome**. </br> De acordo com **as regras de entrada**, selecione **+Adicione uma regra de entrada**. </br> Nas **gamas portuárias Destination**, insira **80**. </br> Sob **Prioridade**, insira **100**. </br> Em **Nome**, insira **myHTTPRule** </br> Selecione **Adicionar** </br> Selecione **OK** |
    | **Balanceamento de carga**  |
    | Colocar esta máquina virtual por trás de uma solução de equilíbrio de carga existente? | Selecione **Sim** |
    | **Definições de equilíbrio de carga** |
    | Opções de equilíbrio de carga | Selecione **o equilíbrio da carga Azure** |
    | Selecione um equilibrador de carga | Selecione **myLoadBalancer**  |
    | Selecione uma piscina de backend | Selecione **myBackendPool** |

5. Selecione o separador **Gestão** ou selecione **'Gestão**  >  **Seguinte'.**

6. No separador **Gestão,** selecione ou introduza:
    
    | Definição | Valor |
    |-|-|
    | **Monitorização** |  |
    | Diagnósticos de arranque. | Selecione **Off** |
   
7. Selecione **Rever + criar**. 
  
8. Reveja as definições e, em seguida, **selecione Criar**.

9. Siga os passos 1 a 8 para criar dois VMs adicionais com os seguintes valores e todas as outras definições iguais ao **myVM1**:

    | Definição | VM 2| VM 3|
    | ------- | ----- |---|
    | Name |  **myVM2** |**myVM3**|
    | Zona de disponibilidade | **2** |**3**|
    | Grupo de segurança de rede | Selecione o **myNSG** existente| Selecione o **myNSG** existente|


# <a name="option-2-create-a-load-balancer-basic-sku"></a>[Opção 2: Criar um equilibrador de carga (SKU Básico)](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Recomenda-se o balanceador de carga SKU standard para cargas de trabalho de produção.  Para obter mais informações sobre skus, consulte **[skus de balançadores de carga Azure.](skus.md)**

Nesta secção, cria-se um equilibrador de carga que equilibra as máquinas virtuais. 

Pode criar um equilibrador de carga pública ou um equilibrador interno de carga. 

Quando cria um equilibrador de carga público, cria um novo endereço IP público configurado como frontend (denominado **LoadBalancerFrontend** por padrão) para o equilibrador de carga.

1. No lado superior esquerdo do ecrã, selecione **Criar um**  >  equilibrador de carga de**rede de**recursos  >  **Load Balancer**.

2. No separador Básicos da página **'Criar balançador** de carga', insira ou selecione as **seguintes** informações: 

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | **Selecione Criar novo** e **digite o myResourceGroupLB** na caixa de texto.|
    | Name                   | Insira **o myLoadBalancer**                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Tipo          | Selecione **Público**.                                        |
    | SKU           | Selecione **Basic** |
    | Endereço IP público | Selecione **Criar novo**. Se tiver um IP público existente que gostaria de utilizar, selecione **Utilizar a existência**. |
    | Nome do endereço IP público | Digite **o myPublicIP** na caixa de texto.|
    | Atribuição | Selecione **Dynamic** |
    | Adicionar um endereço IPv6 público | Selecione **Não**. </br> Para obter mais informações sobre endereços IPv6 e balanceador de carga, consulte [o que é o IPv6 para a Rede Virtual Azure?](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)  |

3. Aceite as predefinições para as definições restantes e, em seguida, selecione **Review + create**.

4. No **separador 'Rever + criar',** selecione **Criar.**   

    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-basic-load-balancer.png" alt-text="Criar um equilibrador de carga básico" border="true":::

## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta secção, configura:

* Crie uma rede virtual
* Ajustes do balançador de carga para um pool de endereço de backend.
* Uma sonda de saúde.
* Uma regra do equilíbrio de carga.

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Nesta secção, substituirá os parâmetros nos passos com as informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Europa Ocidental      |
| **\<IPv4-address-space>**   | 10.1.0.0\16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

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
    | Caminho | Inserir**/** |
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

* Crie três máquinas virtuais para o pool de backend do equilibrador de carga.
* Crie um conjunto de disponibilidade para as máquinas virtuais.
* Instale o IIS nas máquinas virtuais para testar o equilibrador de carga.

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Os SKUs IP públicos e os SKUs do balançador de carga devem coincidir. Para o balanceador de carga básico, utilize VMs com endereços IP básicos na piscina de backend. 

Nesta secção, você vai criar três VMs (**myVM1,** **myVM2**, e **myVM3**) com um endereço IP público básico.  

Os três VMs serão adicionados a um conjunto de disponibilidade chamado **myAvailabilitySet**.

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
    | Confirmar palavra-passe | Senha de reentrada |

3. Selecione o **separador 'Rede'** ou selecione **Seguinte: Discos**e, em seguida, **seguinte: Networking**.
  
4. No separador Networking, selecione ou introduza:

    | Definição | Valor |
    |-|-|
    | **Interface de rede** |  |
    | Rede virtual | Selecione **myVNet** |
    | Subrede | Selecione **myBackendSubnet** |
    | IP público | Selecione **Criar novo** </br> **Insira o myVM-ip** em nome. </br> Selecione **OK** |
    | Grupo de segurança de rede NIC | Selecione **Avançado**|
    | Configure grupo de segurança de rede | Selecione **Criar novo**. </br> No **grupo de segurança**de rede Create , insira **o myNSG** em **Nome**. </br> De acordo com **as regras de entrada**, selecione **+Adicione uma regra de entrada**. </br> Nas **gamas portuárias Destination**, insira **80**. </br> Sob **Prioridade**, insira **100**. </br> Em **Nome**, insira **myHTTPRule** </br> Selecione **Adicionar** </br> Selecione **OK** |
    | **Balanceamento de carga**  |
    | Colocar esta máquina virtual por trás de uma solução de equilíbrio de carga existente? | Selecione **Não** |
 
5. Selecione o separador **Gestão** ou selecione **'Gestão**  >  **Seguinte'.**

6. No separador **Gestão,** selecione ou introduza:
    | Definição | Valor |
    |-|-|
    | **Monitorização** | |
    | Diagnósticos de arranque. | Selecione **Off** |

7. Selecione **Rever + criar**. 
  
8. Reveja as definições e, em seguida, **selecione Criar**.

9. Siga os passos 1 a 8 para criar dois VMs adicionais com os seguintes valores e todas as outras definições iguais ao **myVM1**:

    | Definição | VM 2| VM 3|
    | ------- | ----- |---|
    | Name |  **myVM2** |**myVM3**|
    | Conjunto de disponibilidade| Selecione **mySIlabilitySet** | Selecione **mySIlabilitySet**|
    | Grupo de segurança de rede | Selecione o **myNSG** existente| Selecione o **myNSG** existente|
---

### <a name="install-iis"></a>Instalar o IIS

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**, e, em seguida, a partir da lista de recursos, selecione **myVM1** que está localizado no grupo de recursos **myResourceGroupLB.**

2. Na página **'Vista Geral',** selecione **Connect** para descarregar o ficheiro RDP para o VM.

3. Abra o ficheiro RDP.

4. Inicie sessão na VM com as credenciais que forneceu durante a criação desta VM.

5. No ambiente de trabalho do servidor, navegue para o **Windows Administrative Tools**Windows > **PowerShell**.

6. Na Janela PowerShell, executar os seguintes comandos para:

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
7. Fechar a sessão RDP com **myVM1**.

8. Repita os passos 1 a 6 para instalar o IIS e o ficheiro iisstart.htm atualizado em **myVM2** e **myVM3**.

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

1. Encontre o endereço IP público para o balançador de carga no ecrã **de visão geral.** Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myPublicIP**.

2. Copie o endereço IP público e cole-o na barra de endereço do browser. A página predefinida do servidor Web do IIS é apresentada no browser.

   ![Servidor Web do IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Para ver o balanceador de carga distribuir tráfego através dos três VMs, pode personalizar a página padrão de cada servidor Web IIS de cada VM e, em seguida, refrescar o seu navegador web a partir da máquina do cliente.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, carregue o Balancer e todos os recursos relacionados. Para tal, selecione o grupo de recursos **myResourceGroupLB** que contém os recursos e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Próximos passos

Neste início rápido, irá:

* Criei um Balanceador de Carga Padrão Azure ou Básico
* Fixado 3 VMs ao equilibrador de carga.
* Configurar a regra de tráfego do balanceador de carga, a sonda de saúde, e depois testou o equilibrador de carga. 

Para saber mais sobre o Azure Load Balancer, continue até [Load Balancer frequently asked questions](load-balancer-faqs.md) [o que é o Equilibr de Carga Azure?](load-balancer-overview.md)

Saiba mais sobre [as zonas de Balancer e Disponibilidade](load-balancer-standard-availability-zones.md)de Carga.
