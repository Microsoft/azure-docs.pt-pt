---
title: Tutorial - Implantar o cluster vSphere em Azure
description: Aprenda a implementar um cluster vSphere em Azure usando Azure VMWare Solution
ms.topic: tutorial
ms.date: 09/07/2020
ms.openlocfilehash: 69a29a459ba283bb34169112ac2fa174ac6a14af
ms.sourcegitcommit: 8791f69d44150767807d215cafc4076f3ed43f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2020
ms.locfileid: "89512382"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud-in-azure"></a>Tutorial: Implementar uma nuvem privada Azure VMware Solution em Azure

A Azure VMware Solution dá-lhe a capacidade de implantar um cluster vSphere em Azure. A implantação inicial mínima é de três anfitriões. Anfitriões adicionais podem ser adicionados um de cada vez, até um máximo de 16 anfitriões por cluster. 

Como a Azure VMware Solution não lhe permite gerir a sua nuvem privada com o seu vCenter no local no lançamento, configuração adicional e ligação a uma instância vCenter local, rede virtual e mais é necessária. Estes procedimentos e pré-requisitos conexos estão abrangidos por este tutorial.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma nuvem privada Azure VMware Solution
> * Verifique a nuvem privada implantada

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Direitos administrativos adequados e permissão para criar uma nuvem privada.
- Certifique-se de que tem a rede adequada configurada como descrito no [Tutorial: Lista de verificação de rede](tutorial-network-checklist.md).

## <a name="register-the-resource-provider"></a>Registar o fornecedor de recursos

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="create-a-private-cloud"></a>Criar uma Cloud Privada

Pode criar uma nuvem privada Azure VMware Solution utilizando o [portal Azure](#azure-portal) ou utilizando o [Azure CLI](#azure-cli).

### <a name="azure-portal"></a>Portal do Azure

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-avs-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>CLI do Azure

Em vez do portal Azure para criar uma nuvem privada Azure VMware Solution, pode utilizar o Azure CLI utilizando a Azure Cloud Shell. É uma concha interativa gratuita com ferramentas Azure comuns pré-instaladas e configuradas para usar com a sua conta. 

#### <a name="open-azure-cloud-shell"></a>Abrir o Azure Cloud Shell

Para abrir a Cloud Shell, selecione **Experimente-a** a partir do canto superior direito de um bloco de código. Também pode lançar cloud Shell num separador de navegador indo para [https://shell.azure.com/bash](https://shell.azure.com/bash) . Selecione **Copy** para copiar os blocos de código, cole-o na Cloud Shell e prima **Enter** para executá-lo.

#### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *este:*

```
azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Criar uma cloud privada

Forneça um nome de grupo de recursos, um nome para a nuvem privada, uma localização, o tamanho do cluster.

| Propriedade  | Descrição  |
| --------- | ------------ |
| **-g** (nome do Grupo de Recursos)     | O nome do grupo de recursos para os seus recursos em nuvem privada.        |
| **-n** (nome da Nuvem Privada)     | O nome da sua nuvem privada Azure VMware Solution.        |
| **...-localização**     | O local usado para a sua nuvem privada.         |
| **--tamanho do cluster**     | Do tamanho do aglomerado. O valor mínimo é 3.         |
| **-bloco de rede**     | O bloco de rede de endereços IP CIDR para utilizar para a sua nuvem privada. O bloco de endereços não deve sobrepor-se aos blocos de endereços utilizados noutras redes virtuais que se encontram nas suas redes de subscrição e no local.        |
| **--sku** | O valor SKU: AV36 |

```
azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="delete-a-private-cloud-azure-portal"></a>Eliminar uma nuvem privada (portal Azure)

Se tiver uma nuvem privada Azure VMware Solution de que já não precisa, pode eliminá-la. Quando elimina uma nuvem privada, todos os clusters, juntamente com todos os seus componentes, são eliminados.

Para tal, navegue para a sua nuvem privada no portal Azure e selecione **Delete**. Na página de confirmação, confirme com o nome da nuvem privada e selecione **Sim**.

> [!CAUTION]
> Apagar a nuvem privada é uma operação irreversível. Uma vez eliminada a nuvem privada, os dados não podem ser recuperados, uma vez que termina todas as cargas de trabalho, componentes e destrói todos os dados e configurações de nuvem privada, incluindo endereços IP públicos. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma nuvem privada Azure VMware Solution
> * Verificado a Nuvem Privada implantada

Continue até ao próximo tutorial para aprender a criar uma rede virtual para uso com a sua nuvem privada como parte da criação de gestão local para os seus clusters de nuvem privada.

> [!div class="nextstepaction"]
> [Criar uma Rede Virtual](tutorial-configure-networking.md)
