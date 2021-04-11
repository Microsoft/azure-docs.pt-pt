---
title: 'Tutorial: Integrar porta de entrada NAT com um equilibrador de carga pública - Portal Azure'
titleSuffix: Virtual Network NAT
description: Neste tutorial, aprenda a integrar um gateway NAT de Rede Virtual com um Balancer de carga pública utilizando o portal Azure.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/19/2021
ms.custom: template-tutorial
ms.openlocfilehash: 8382dd10536a8c0475444d0cdff30340ad124e9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104722808"
---
# <a name="tutorial-integrate-a-nat-gateway-with-a-public-load-balancer-using-the-azure-portal"></a>Tutorial: Integrar uma porta DE NAT com um equilibrador de carga pública utilizando o portal Azure

Neste tutorial, você vai aprender a integrar um gateway NAT com um equilibrador de carga pública.

Por predefinição, um Balançador de Carga Padrão Azure está seguro. A conectividade de saída é explicitamente definida permitindo o SNAT de saída (Tradução de Endereços de Rede de Origem). O SNAT está ativado numa regra de equilíbrio de carga ou regras de saída. 

A integração do gateway NAT substitui a necessidade de regras de saída para o SNAT de saída de backend pool. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um equilibrador de carga Azure
> * Criar duas máquinas virtuais para o pool backend do Balançador de Carga Azure
> * Criar um portal NAT
> * Validar a conectividade de saída das máquinas virtuais no pool de backend do balançador de carga

## <a name="prerequisites"></a>Pré-requisitos

Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-load-balancer"></a>Criar um balanceador de carga

Nesta secção, irá criar um Balanceador de Carga Standard Azure. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Criar um recurso**. 
3. Na caixa de pesquisa, insira o **balançador de carga**. Selecione **o balanceador de carga** nos resultados da pesquisa.
4. Na página **do balançador de carga,** selecione **Criar**.
5. Na página do **balanceador de carga,** ou selecione as seguintes informações: 

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | **Detalhes do projeto** |   |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | **Selecione Criar novo** e insira **TutorPubLBNAT-rg** na caixa de texto. </br> Selecione **OK**.|
    | **Detalhes da instância** |   |
    | Name                   | Insira **o myLoadBalancer**                                   |
    | Region         | Selecione **(EUA) Leste DOS** EUA .                                        |
    | Tipo          | Selecione **Público**.                                        |
    | SKU           | Deixe o **padrão padrão**. |
    | Escalão de serviço          | Deixe o **padrão Regional**. |
    | **Endereço IP público** |   |
    | Endereço IP público | Selecione **Criar novo**. </br> Se tiver um IP público existente que gostaria de utilizar, selecione **Utilizar a existência**. |
    | Nome do endereço IP público | **Insira o myPublicIP-LB** na caixa de texto.|
    | Zona de disponibilidade | Selecione **Zona redundante** para criar um equilibrador de carga resistente. Para criar um equilibrador de carga zonal, selecione uma zona específica a partir de 1, 2 ou 3 |
    | Adicionar um endereço IPv6 público | Selecione **Não**. </br> Para obter mais informações sobre endereços IPv6 e balanceador de carga, consulte [o que é o IPv6 para a Rede Virtual Azure?](../virtual-network/ipv6-overview.md)  |
    | Preferência de encaminhamento | Deixe o padrão da **rede Microsoft**. </br> Para obter mais informações sobre a preferência de encaminhamento, consulte [o que é a preferência de encaminhamento (pré-visualização)?](../virtual-network/routing-preference-overview.md) |

6. Aceite as predefinições para as definições restantes e, em seguida, selecione **Review + create**.

7. No **separador 'Rever + criar',** selecione **Criar.**

## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta secção, configura:

* Ajustes do balançador de carga para um pool de endereço de backend.
* Uma sonda de saúde.
* Uma regra do equilíbrio de carga.

### <a name="create-a-backend-pool"></a>Criar um conjunto de back-ends

Um conjunto de endereços de backend contém os endereços IP dos (NICs) virtuais ligados ao equilibrador de carga. 

Crie o pool de endereços de backend **myBackendPool** para incluir máquinas virtuais para equilibrar o tráfego de internet.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, em seguida, selecione **myLoadBalancer** na lista de recursos.

2. Em **Definições**, selecione **pools backend** e, em seguida, selecione **Adicionar**.

3. Na página de **piscina de backend,** para nome, escreva **myBackendPool,** como o nome da sua piscina de backend e, em seguida, selecione **Add**.

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

O equilibrador de carga monitoriza o estado da sua aplicação com uma sonda de saúde. 

A sonda de saúde adiciona ou remove VMs do equilibrador de carga com base na sua resposta aos controlos de saúde. 

Crie uma sonda de estado de funcionamento com o nome **myHealthProbe**, para monitorizar o estado de funcionamento das VMs.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, em seguida, selecione **myLoadBalancer** na lista de recursos.

2. Em **Definições**, selecione **sondas de saúde** e, em seguida, selecione **Adicionar**.
    
    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira **o myHealthProbe**. |
    | Protocolo | Selecione **TCP**. |
    | Porta | Insira **80**.|
    | Intervalo | Introduza **15** para o número de **intervalos** em segundos entre tentativas de sonda. |
    | Limiar com funcionamento incorreto | Selecione **2** para o número de **falhas de sonda insalubres** ou falhas de sonda consecutivas que devem ocorrer antes de um VM ser considerado insalubre.|
   

3. Deixe o resto as predefinições e **Selecione Adicionar**.

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

É utilizada uma regra de balanceador de carga para definir a forma como o tráfego é distribuído pelas VMs. Você define a configuração IP frontend para o tráfego de entrada e o pool IP backend para receber o tráfego. A origem e a porta de destino estão definidas na regra. 

Nesta secção, irá criar uma regra do balançador de carga:

* Chamado **myHTTPRule.**
* Na frontend chamada **LoadBalancerFrontEnd**.
* Escuta no **Porto 80**.
* Direciona o tráfego equilibrado para o backendpool chamado **myBackendPool** na **Porta 80**.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, em seguida, selecione **myLoadBalancer** na lista de recursos.

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
    | Tempo de 20 minutos (minutos) | Entre **em 15** minutos. |
    | Reset TCP | Selecione **Ativado**. |
    | Tradução de endereços de rede de saída (SNAT) | Selecione **(Recomendado) Utilize regras de saída para fornecer acesso aos membros do pool de backend à internet.** |

4. Deixe o resto dos predefinidos e, em seguida, selecione **OK**.

## <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta secção, irá criar uma rede virtual e uma sub-rede.

1. No canto superior esquerdo do ecrã, selecione **Criar um recurso > Redes > Rede virtual** ou procure **Rede virtual** na caixa de pesquisa.

2. Selecione **Criar**. 

3. Na **Criação de rede virtual,** insira ou selecione esta informação no **separador Básicos:**

    | **Definição**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscrição     | selecione a subscrição do Azure                                  |
    | Grupo de Recursos   | **Selecione TutorPubLBNAT-rg** |
    | **Detalhes da instância** |                                                                 |
    | Name             | Insira **myVNet**                                    |
    | Region           | Selecione **E.U.A. Leste**. |

4. Selecione o separador **endereços IP** ou selecione o botão **Seguinte: Endereços IP** na parte inferior da página.

5. No separador **endereços IP,** introduza estas informações:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Espaço de endereço IPv4 | Insira **10.1.0.0/16** |

6. No **nome da sub-rede,** selecione a palavra **predefinição**.

7. Na **sub-rede Editar,** insira esta informação:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Nome da sub-rede | Insira **myBackendSubnet** |
    | Intervalo de endereços da sub-rede | Insira **10.1.0.0/24** |

8. Selecione **Guardar**.

9. Selecione o separador **Segurança.**

10. Em **BastionHost**, selecione **Enable**. Insira esta informação:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Nome de bastião | Insira **myBastionHost** |
    | Espaço de endereço AzureBastionSubnet | Insira **10.1.1.0/24** |
    | Endereço IP Público | Selecione **Criar novo**. </br> Para **nome,** insira **myBastionIP**. </br> Selecione **OK**. |


11. Selecione o **separador 'Rever +' ou** selecionar o botão **'Rever +' criar.**

12. Selecione **Criar**.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Nesta secção, você vai criar dois VMs (**myVM1** e **myVM2**) em duas zonas diferentes **(Zona 1** e **Zona 2).**

Estes VMs são adicionados ao pool de backend do equilibrador de carga que foi criado anteriormente.

1. No lado superior esquerdo do portal, selecione **Criar uma** máquina Virtual  >  **compute de** recurso  >  . 
   
2. Na **Criação de uma máquina virtual,** escreva ou selecione os valores no **separador Básicos:**

    | Definição | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscrição | selecione a subscrição do Azure |
    | Grupo de Recursos | **Selecione TutorPubLBNAT-rg** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **o myVM1** |
    | Region | Selecione **E.U.A. Leste**. |
    | Opções de Disponibilidade | Selecione **zonas de disponibilidade** |
    | Zona de disponibilidade | Selecione **1** |
    | Imagem | Selecione **o Centro de Dados 2019 do Windows Server 2019** |
    | Instância do Azure Spot | Deixe o padrão |
    | Tamanho | Escolha o tamanho VM ou tome a definição predefinida |
    | **Conta de administrador** |  |
    | Nome de utilizador | Insira um nome de utilizador |
    | Palavra-passe | Introduza uma senha |
    | Confirmar palavra-passe | Reintroduza a palavra-passe |
    | **Regras portuárias de entrada** |  |
    | Portas de entrada públicas | Selecione **Nenhum** |

3. Selecione o **separador 'Rede'** ou selecione **Seguinte: Discos** e, em seguida, **seguinte: Networking**.
  
4. No separador Networking, selecione ou introduza:

    | Definição | Valor |
    |-|-|
    | **Interface de rede** |  |
    | Rede virtual | **myVNet** |
    | Sub-rede | **myBackendSubnet** |
    | IP público | Selecione **Nenhuma**. |
    | Grupo de segurança de rede NIC | Selecione **Avançado**|
    | Configure grupo de segurança de rede | Selecione **Criar novo**. </br> No **grupo de segurança** de rede Create , insira **o myNSG** em **Nome**. </br> De acordo com **as regras de entrada**, selecione **+Adicione uma regra de entrada**. </br> Nas  **gamas portuárias Destination**, insira **80**. </br> Sob **Prioridade**, insira **100**. </br> Em **Nome**, insira **myHTTPRule** </br> Selecione **Adicionar** </br> Selecione **OK** |
    | **Balanceamento de carga**  |
    | Colocar esta máquina virtual por trás de uma solução de equilíbrio de carga existente? | Selecione a caixa de verificação.|
    | **Definições de equilíbrio de carga** |
    | Opções de equilíbrio de carga | Selecione **o equilibrador de carga Azure** |
    | Selecione um equilibrador de carga | Selecione **myLoadBalancer**  |
    | Selecione uma piscina de backend | Selecione **myBackendPool** |
   
5. Selecione **Rever + criar**. 
  
6. Reveja as definições e, em seguida, **selecione Criar**.

7. Siga os passos 1 a 7 para criar um VM com os seguintes valores e todas as outras definições iguais ao **myVM1**:

    | Definição | VM 2 |
    | ------- | ----- |
    | Name |  **myVM2** |
    | Zona de disponibilidade | **2** |
    | Grupo de segurança de rede | Selecione o **myNSG** existente| 

## <a name="create-nat-gateway"></a>Criar um NAT gateway

Nesta secção, irá criar um gateway NAT e atribuí-lo-á à sub-rede na rede virtual que criou anteriormente.

1. No lado superior esquerdo do ecrã, selecione **Criar um recurso > Networking > gateway NAT** ou procurar por gateway **NAT** na caixa de pesquisa.

2. Selecione **Criar**. 

3. No **Portal de Tradução de Endereços de Rede (NAT),** insira ou selecione estas informações no **separador Básicos:**

    | **Definição**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscrição     | Selecione a sua subscrição do Azure.                                  |
    | Grupo de Recursos   | **Selecione TutorPubLBNAT-rg**. |
    | **Detalhes da instância** |                                                                 |
    | Name             | Insira **myNATGateway**                                    |
    | Region           | Selecione **(EUA) Leste dos EUA**  |
    | Zona de Disponibilidade | Selecione **Nenhuma**. |
    | Tempo de 20 minutos (minutos) | Insira **10**. |

4. Selecione o **separador IP outbound** ou selecione o botão **IP de saída seguinte** na parte inferior da página.

5. No **separador IP outbound,** insira ou selecione as seguintes informações:

    | **Definição** | **Valor** |
    | ----------- | --------- |
    | Endereços IP públicos | **Selecione Criar um novo endereço IP público**. </br> Em **Nome**, insira **myPublicIP-NAT**. </br> Selecione **OK**. |

6. Selecione o separador **Sub-rede** ou selecione o botão **Seguinte: Sub-rede** na parte inferior da página.

7. No separador **Subnet,** selecione **o myVNet** na **rede Virtual.**

8. Verifique a caixa ao lado **do myBackendSubnet**.

9. Selecione o **separador 'Rever +' ou** selecione o botão **'Avaliação azul+'** na parte inferior da página.

10. Selecione **Criar**.

## <a name="test-nat-gateway"></a>Portal DE TESTE NAT

Nesta secção, vamos testar o portal NAT. Primeiro descobriremos o IP público do portal NAT. Ligaremos-nos então à máquina virtual de teste e verificaremos a ligação de saída através do gateway NAT.
    
1. Encontre o endereço IP público para o gateway NAT no ecrã **de visão geral.** Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, em seguida, selecione **myPublicIP**.

2. Tome nota do endereço IP público:

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-public-portal/find-public-ip.png" alt-text="Screenshot descubra o endereço IP público do gateway NAT." border="true":::

3. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**, e, em seguida, na lista de recursos, selecione **myVM1** que está localizado no grupo de recursos **TutorPubLBNAT-rg.**

4. Na página **'Vista Geral',** selecione **Connect** e, em seguida, **Bastion**.

5. Selecione o botão **azul Use Bastion.**

6. Introduza o nome de utilizador e a palavra-passe introduzidos durante a criação de VM.

7. Abrir **internet Explorer** no **myVM1**.

8. **https://whatsmyip.com** Insira na barra de endereços.

9. Verifique se o endereço IP apresentado corresponde ao endereço de gateway NAT indicado no passo anterior:

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-public-portal/my-ip.png" alt-text="Screenshot Internet Explorer mostrando IP de saída externa." border="true":::

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine a rede virtual, a máquina virtual e o gateway NAT com os seguintes passos:

1. A partir do menu da esquerda, selecione **Grupos de Recursos.**

2. Selecione o grupo de recursos **TutorPubLBNAT-rg.**

3. Selecione **Eliminar grupo de recursos**.

4. **Insira tutorPubLBNAT-rg** e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a Azure Virtual Network NAT, consulte:
> [!div class="nextstepaction"]
> [Visão geral do NAT da rede virtual](nat-overview.md)
