---
title: Crie um cluster azure Data Explorer & DB na sua rede virtual
description: Neste artigo, aprende-se a criar um cluster Azure Data Explorer na sua rede virtual.
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: 99c32122fc48aaea7428fa559d7289713849f34e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548904"
---
# <a name="create-an-azure-data-explorer-cluster-in-your-virtual-network"></a>Crie um cluster Azure Data Explorer na sua rede virtual

O Azure Data Explorer suporta a implementação de um cluster numa subrede na sua rede virtual (VNet). Esta capacidade permite-lhe aceder ao cluster em privado a partir da sua rede virtual Azure ou no local, aceder a recursos como O Hub de Eventos e Armazenamento dentro da sua rede virtual, e restringir o tráfego de entrada e saída.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-network-security-group-nsg"></a>Criar o Grupo de Segurança da Rede (NSG)

[Os Grupos de Segurança da Rede (NSG)](/azure/virtual-network/security-overview) fornecem a capacidade de controlar o acesso à rede dentro de um VNet. O Azure Data Explorer pode ser acedido com dois pontos finais HTTPs (443) e TDS (1433). As seguintes regras de NSG devem ser configuradas para permitir o acesso a estes pontos finais para gestão, monitorização e bom funcionamento do seu cluster.

Para criar o grupo de segurança da rede:

1. Selecione o **+ Criar um** botão de recurso no canto superior esquerdo do portal.
1. Pesquisa por *Network Security Group*.
1. No Grupo de **Segurança da Rede**, na parte inferior do ecrã, selecione **Criar**.
1. Na janela do grupo de **segurança da rede Create,** preencha as seguintes informações.

   ![Criar formulário NSG](media/vnet-create-cluster-portal/network-security-group.png)

    **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Subscrição | A sua subscrição | Selecione a subscrição do Azure que quer utilizar para o cluster.|
    | Grupo de recursos | O seu grupo de recursos | Utilize um grupo de recursos existente ou crie um novo grupo de recursos. |
    | Nome | AzureDataExplorerNsg | Escolha um nome que identifique o seu Grupo de Segurança de Rede (NSG) no grupo de recursos.  |
    | Região | *E.U.A. Oeste* | Selecione a região que melhor satisfaz as suas necessidades.
    | | | |

1. Selecione **Review + crie** para rever os detalhes do cluster e **crie** para fornecer o cluster.

1. Quando a implementação estiver concluída, selecione **Ir para o recurso**.

    ![Ir para recurso](media/vnet-create-cluster-portal/notification-nsg.png)

1. No separador de regras de **segurança De entrada,** selecione **+Adicionar**.
1. Na janela de regra de **segurança adicionar,** preencha as seguintes informações.

    ![Criar formulário de regra de entrada NSG](media/vnet-create-cluster-portal/nsg-inbound-rule.png)

    **Definição** | **Valor sugerido** 
    |---|---|
    | Origem | Marca de serviço
    | Etiqueta de serviço de origem | AzureDataExplorerManagement
    | Intervalo de portas de origem | *
    | Destino | VirtualNetwork
    | Intervalos de portas de destino | *
    | Protocolo | TCP
    | Ação | Permitir
    | Prioridade | 100
    | Nome | AllowAzureDataExplorerManagement
    | | |
    
1. Repita os dois passos anteriores para todas as dependências de entrada e saída de acordo com [as dependências para a implantação](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)da VNet . Em alternativa, as regras de saída podem ser substituídas por uma regra única para permitir a *Internet* para as portas 443 e 80.
    
    As regras do NSG para dependências de entrada e saída devem ser assim:

    ![Regras do NSG](media/vnet-create-cluster-portal/nsg-rules.png)

## <a name="create-public-ip-addresses"></a>Criar endereços IP públicos

Para criar os endereços IP públicos de consulta (Motor):

1. Selecione o **+ Criar um** botão de recurso no canto superior esquerdo do portal.
1. Pesquisa por *Network Security Group*.
1. Sob **o endereço IP público**, na parte inferior do ecrã, selecione **Criar**.
1. No painel de **endereços IP público Criar,** preencha as seguintes informações.
   
   ![Criar formulário IP público](media/vnet-create-cluster-portal/public-ip-blade.png)

    **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Versão IP | IPv4 | Selecione a versão IP. Só apoiamos o IPv4.|
    | Sku | Standard | Exigimos o ponto final **do Standard** para consulta (Motor) URI. |
    | Nome | motor-pip | Escolha um nome que identifique o seu endereço IP público no grupo de recursos.
    | Subscrição | A sua subscrição | Selecione a subscrição Azure que pretende utilizar para o seu IP público.|
    | Grupo de recursos | O seu grupo de recursos | Utilize um grupo de recursos existente ou crie um novo grupo de recursos. |
    | Localização | *E.U.A. Oeste* | Selecione a região que melhor satisfaz as suas necessidades.
    | | | |

1. Selecione **Criar** para criar o endereço IP público.

1. Para criar o endereço IP público de ingestão (Gestão de Dados) siga as mesmas instruções e selecione 
    * **Sku**: Básico
    * **Atribuição de endereço IP**: Estática

## <a name="create-virtual-network-and-subnet"></a>Criar rede virtual e sub-rede

Para criar a rede virtual e a sub-rede:

1. Selecione o **+ Criar um** botão de recurso no canto superior esquerdo do portal.
1. Pesquisa por *Rede Virtual*.
1. Em **Rede Virtual**, na parte inferior do ecrã, selecione **Criar**.
1. Na janela **de rede virtual Create,** complete as seguintes informações.

   ![Criar forma de rede virtual](media/vnet-create-cluster-portal/vnet-blade.png)

    **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Subscrição | A sua subscrição | Selecione a subscrição do Azure que quer utilizar para o cluster.|
    | Grupo de recursos | O seu grupo de recursos | Utilize um grupo de recursos existente ou crie um novo grupo de recursos. |
    | Nome | AzureDataExplorerVnet | Escolha um nome que identifique a sua rede virtual no grupo de recursos.
    | Região | *E.U.A. Oeste* | Selecione a região que melhor satisfaz as suas necessidades.
    | | | |

    > [!NOTE]
    > Para cargas de trabalho de produção, planeje o tamanho da sua subnet de acordo com o tamanho da [subnet do plano no seu VNet](/azure/data-explorer/vnet-deployment#plan-subnet-size-in-your-vnet)

1. Selecione **Review + crie** para rever os detalhes do cluster e **crie** para fornecer o cluster.

1. Quando a implementação estiver concluída, selecione **Ir para o recurso**.
1. Vá à lâmina **sub-redes** e selecione a sub-rede **predefinida.**
    
    ![Lâmina de sub-redes](media/vnet-create-cluster-portal/subnets.png)

1. Na sua janela de sub-rede **padrão:**
    1. Selecione o seu Grupo de Segurança de **Rede** a partir do menu suspenso. 
    1. Selecione o nome do seu grupo de segurança de rede, neste **caso, O AzureDataExplorerNsg**. 
    1. **Guardar**

    ![Configurar sub-rede](media/vnet-create-cluster-portal/subnet-nsg.png)

## <a name="create-a-cluster"></a>Criar um cluster

Crie um cluster Azure Data Explorer com um conjunto definido de recursos de computação e armazenamento num grupo de recursos Azure, conforme descrito na [criação](create-cluster-database-portal.md#create-a-cluster)de um cluster .

1. Antes de finalizar a criação de cluster, na janela **de cluster Create a Azure Data Explorer,** selecione o separador **Rede** para fornecer detalhes de rede virtuais utilizando os recursos criados nos separadores anteriores:

   ![Criar forma de vnet cluster](media/vnet-create-cluster-portal/create-cluster-form-vnet.png)

    **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Subscrição | A sua subscrição | Selecione a subscrição Azure que pretende utilizar para recursos de networking.|
    | Rede Virtual | AzureDataExplorerVnet | Escolha a rede virtual criada nos passos anteriores.
    | Subrede | predefinição | Escolha a sub-rede criada nos passos anteriores.
    | Consulta IP Pública | motor-pip | Escolha a consulta pública IP criada nos passos anteriores.
    | Inedição de DADOS IP Público | dm-pip | Escolha a ingestão de IP público criada nos passos anteriores.
    | | | |

1. Selecione **Review + crie** para criar o seu cluster.
1. Quando a implementação estiver concluída, selecione **Ir para o recurso**.

Para implantar o seu cluster Azure Data Explorer na sua rede virtual, utilize o [cluster Deploy Azure Data Explorer no seu](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) modelo VNet Azure Resource Manager.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Implemente o Azure Data Explorer na sua Rede Virtual](vnet-create-cluster-portal.md)