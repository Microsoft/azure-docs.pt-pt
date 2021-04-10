---
title: 'Tutorial: Criar uma porta de entrada NAT - Portal Azure'
titlesuffix: Azure Virtual Network NAT
description: Neste tutorial, aprenda a criar e validar um gateway NAT utilizando o portal Azure.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 82b5892b027627871e5492e3c6cd3776a923632b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102553447"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal"></a>Tutorial: Criar uma porta de entrada NAT utilizando o portal Azure

Este tutorial mostra-lhe como utilizar o serviço Azure Virtual Network NAT. Você vai criar uma porta de entrada NAT para fornecer conectividade de saída para uma máquina virtual em Azure. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma rede virtual
> * Cria uma máquina virtual.
> * Crie um gateway NAT e associe-se à rede virtual.
> * Ligue-se à máquina virtual e verifique o endereço IP DO NAT.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="virtual-network"></a>Rede virtual

Antes de implementar um VM e poder utilizar o seu gateway NAT, precisamos de criar o grupo de recursos e a rede virtual.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No canto superior esquerdo do ecrã, selecione **Criar um recurso > Redes > Rede virtual** ou procure **Rede virtual** na caixa de pesquisa.

3. Selecione **Criar**.

4. Na **Criação de rede virtual,** insira ou selecione esta informação no **separador Básicos:**

    | **Definição**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscrição     | selecione a subscrição do Azure                                  |
    | Grupo de Recursos   | Selecione **Criar novo**. </br> Insira **o myResourceGroupNAT**. </br> Selecione **OK**. |
    | **Detalhes da instância** |                                                                 |
    | Name             | Insira **myVNet**                                    |
    | Region           | Selecione **(Europa) Europa Ocidental** |

5. Selecione o separador **endereços IP** ou selecione o botão **Seguinte: Endereços IP** na parte inferior da página.

6. No separador **endereços IP,** introduza estas informações:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Espaço de endereço IPv4 | Insira **10.1.0.0/16** |

7. **Selecione + Adicionar a sub-rede**. 

8. Na **sub-rede Editar,** insira esta informação:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Nome da sub-rede | Insira **mySubnet** |
    | Intervalo de endereços da sub-rede | Insira **10.1.0.0/24** |

9. Selecione **Adicionar**.

10. Selecione o separador **Segurança.**

11. Em **BastionHost**, selecione **Enable**. Insira esta informação:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Nome de bastião | Insira **myBastionHost** |
    | Espaço de endereço AzureBastionSubnet | Insira **10.1.1.0/24** |
    | Endereço IP Público | Selecione **Criar novo**. </br> Para **nome,** insira **myBastionIP**. </br> Selecione **OK**. |

12. Selecione o **separador 'Rever +' ou** selecionar o botão **'Rever +' criar.**

13. Selecione **Criar**.

## <a name="nat-gateway"></a>NAT Gateway

Você pode usar um ou mais recursos de endereço IP público, prefixos IP públicos, ou ambos. Adicionaremos um recurso IP público e um recurso de gateway NAT.

1. No lado superior esquerdo do ecrã, selecione **Criar um recurso > Networking > gateway NAT** ou procurar por gateway **NAT** na caixa de pesquisa.

2. Selecione **Criar**. 

3. No **Portal de Tradução de Endereços de Rede (NAT),** insira ou selecione estas informações no **separador Básicos:**

    | **Definição**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscrição     | Selecione a sua subscrição do Azure.                                  |
    | Grupo de Recursos   | Selecione **myResourceGroupNAT**. |
    | **Detalhes da instância** |                                                                 |
    | Name             | Insira **myNATgateway**                                    |
    | Region           | Selecione **(Europa) Europa Ocidental**  |
    | Zona de Disponibilidade | Selecione **Nenhuma**. |
    | Tempo de 20 minutos (minutos) | Insira **10**. |

4. Selecione o **separador IP outbound** ou selecione o botão **IP de saída seguinte** na parte inferior da página.

5. No **separador IP outbound,** insira ou selecione as seguintes informações:

    | **Definição** | **Valor** |
    | ----------- | --------- |
    | Endereços IP públicos | **Selecione Criar um novo endereço IP público**. </br> Em **Nome,** insira **o myPublicIP**. </br> Selecione **OK**. |

6. Selecione o separador **Sub-rede** ou selecione o botão **Seguinte: Sub-rede** na parte inferior da página.

7. No separador **Subnet,** selecione **o myVNet** na **rede Virtual.**

8. Verifique a caixa ao lado **do mySubnet**.

9. Selecione o **separador 'Rever +' ou** selecione o botão **'Avaliação azul+'** na parte inferior da página.

10. Selecione **Criar**.
    
## <a name="virtual-machine"></a>Máquina virtual

Nesta secção, irá criar uma máquina virtual para testar o gateway NAT e verificar o endereço IP público da ligação de saída.

1. No lado superior esquerdo do portal, selecione **Criar uma** máquina Virtual  >  **compute de** recurso  >  . 

2. Na página **de máquina virtual no** separador **Básicos,** introduza ou selecione as seguintes informações:

    | **Definição** | **Valor** |
    | ----------- | --------- |
    | **Detalhes do projeto** |   |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroupNAT**. |
    | **Detalhes da instância** |   |
    | Nome da máquina virtual | Insira **o myVM**. |
    | Region | Selecione **(Europa) Europa Ocidental**. |
    | Opções de disponibilidade | Não deixe o incumprimento de não ser necessário despedimento. |
    | Imagem | Selecione **o Centro de Dados 2019 do Windows Server - Gen1**. |
    | Tamanho | Selecione **Standard_DS1_v2**. |
    | **Conta de administrador** |   |
    | Nome de utilizador | Introduza um nome de utilizador para a máquina virtual. |
    | Palavra-passe | Introduza uma senha. |
    | Confirmar palavra-passe | Confirme a senha. |
    | **Regras portuárias de entrada** |    |
    | Portas de entrada públicas | Selecione **Nenhuma**. |

3. Selecione o separador **Discos** ou selecione o botão **Seguinte: Discos** na parte inferior da página.

4. Deixe o padrão no **separador Discos.**

5. Selecione o **separador 'Rede'** ou selecione o botão **Seguinte: Botão de rede** na parte inferior da página.

6. No **separador Networking,** introduza ou selecione as seguintes informações:

    | **Definição** | **Valor** |
    | ----------- | --------- |
    | **Interface de rede** |   |
    | Rede virtual | Selecione **myVNet**. |
    | Sub-rede | Selecione **mySubnet**. |
    | IP público | Selecione **Nenhuma**. |
    | Grupo de segurança de rede NIC | Selecione **Básico**. |
    | Portas de entrada públicas | Selecione **Nenhuma**. |

7. Selecione o **separador 'Rever +' ou** selecione o botão **'Avaliação azul+'** na parte inferior da página.

8. Selecione **Criar**.

## <a name="test-nat-gateway"></a>Portal DE TESTE NAT

Nesta secção, vamos testar o portal NAT. Primeiro descobriremos o IP público do portal NAT. Ligaremos-nos então à máquina virtual de teste e verificaremos a ligação de saída através do gateway NAT.
    
1. Encontre o endereço IP público para o gateway NAT no ecrã **de visão geral.** Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, em seguida, selecione **myPublicIP**.

2. Tome nota do endereço IP público:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Descubra o endereço IP público do gateway NAT" border="true":::

3. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**, e, em seguida, a partir da lista de recursos, selecione **myVM** que está localizado no grupo de recursos **myResourceGroupNAT.**

4. Na página **'Vista Geral',** selecione **Connect** e, em seguida, **Bastion**.

5. Selecione o botão **azul Use Bastion.**

6. Introduza o nome de utilizador e a palavra-passe introduzidos durante a criação de VM.

7. Abrir **internet Explorer** no **myTestVM**.

8. **https://whatsmyip.com** Insira na barra de endereços.

9. Verifique se o endereço IP apresentado corresponde ao endereço de gateway NAT indicado no passo anterior:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer mostrando IP externo de saída" border="true":::

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine a rede virtual, a máquina virtual e o gateway NAT com os seguintes passos:

1. A partir do menu da esquerda, selecione **Grupos de Recursos.**

2. Selecione o grupo de recursos **myResourceGroupNAT.**

3. Selecione **Eliminar grupo de recursos**.

4. Introduza **o myResourceGroupNAT** e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a Azure Virtual Network NAT, consulte:
> [!div class="nextstepaction"]
> [Visão geral do NAT da rede virtual](nat-overview.md)
