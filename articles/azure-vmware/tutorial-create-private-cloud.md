---
title: Tutorial - Implantar o cluster vSphere em Azure
description: Aprenda a implementar um cluster vSphere em Azure usando Azure VMWare Solution (AVS)
ms.topic: tutorial
ms.date: 07/15/2020
ms.openlocfilehash: 4f3b33ea401c62124ae5f8a4c881d86d2f19b40c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079422"
---
# <a name="tutorial-deploy-an-avs-private-cloud-in-azure"></a>Tutorial: Implementar uma nuvem privada AVS em Azure

A Azure VMware Solution (AVS) dá-lhe a capacidade de implantar um cluster vSphere em Azure. A implantação inicial mínima é de três anfitriões. Anfitriões adicionais podem ser adicionados um de cada vez, até um máximo de 16 anfitriões por cluster. 

Como o AVS não permite gerir a sua nuvem privada com o seu vCenter no local no lançamento, é necessária uma configuração adicional e ligação a uma instância vCenter local, rede virtual e mais. Estes procedimentos e pré-requisitos conexos estão abrangidos por este tutorial.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma cloud privada do AVS
> * Verifique a nuvem privada implantada

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Direitos administrativos adequados e permissão para criar uma nuvem privada.
- Certifique-se de que tem a rede adequada configurada como descrito no [Tutorial: Lista de verificação de rede](tutorial-network-checklist.md).

## <a name="register-the-resource-provider"></a>Registar o fornecedor de recursos

Para utilizar o AVS, tem primeiro de registar o fornecedor de recursos com a sua subscrição.

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Para obter formas adicionais de registar o fornecedor de recursos, consulte [os fornecedores e tipos de recursos Azure](../azure-resource-manager/management/resource-providers-and-types.md).


## <a name="create-a-private-cloud"></a>Criar uma Cloud Privada

Pode criar uma nuvem privada AVS utilizando o [portal Azure](#azure-portal) ou utilizando o [CLI Azure](#azure-cli).

### <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. **Selecione Criar um novo recurso.** No **tipo de** caixa de texto `Azure VMware Solution` Marketplace, e selecione **Azure VMware Solution** da lista. Na janela **Azure VMware Solution,** selecione **Criar**

1. No separador **Básicos, insira** valores para os campos. A tabela a seguir lista as propriedades dos campos.

   | Campo   | Valor  |
   | ---| --- |
   | **Subscrição** | A subscrição que pretende utilizar para a implantação.|
   | **Grupo de recursos** | O grupo de recursos para os seus recursos em nuvem privada. |
   | **Localização** | Selecione uma localização, como **a leste de nós.**|
   | **Nome de recurso** | O nome da sua nuvem privada AVS. |
   | **SKU** | Selecione o seguinte valor SKU: AV36 |
   | **Anfitriões** | O número de anfitriões para adicionar ao aglomerado de nuvens privadas. O valor predefinido é 3, que pode ser aumentado ou reduzido após a implementação.  |
   | **vCenter senha de administração** | Introduza uma senha de administrador de nuvem. |
   | **Senha do gestor NSX-T** | Introduza uma senha de administrador NSX-T. |
   | **Bloco de endereços** | Introduza um bloco de endereços IP para a rede CIDR para a nuvem privada, por exemplo, 10.175.0.0/22. |

   :::image type="content" source="./media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="criar uma nuvem privada" border="true":::

1. Uma vez terminado, selecione **Review + Create**. No ecrã seguinte, verifique as informações inseridas. Se a informação estiver correta, **selecione Criar**.

   > [!NOTE]
   > Este passo leva cerca de duas horas. 

1. Verifique se a implementação foi bem sucedida. Navegue para o grupo de recursos que criou e selecione a sua nuvem privada.  Verá o estado de **Sucesso** quando a implantação estiver concluída. 

   :::image type="content" source="./media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Validar a nuvem privada implantada" border="true":::

### <a name="azure-cli"></a>CLI do Azure

Em vez do portal Azure para criar uma nuvem privada AVS, você pode usar o CLI Azure usando a Azure Cloud Shell. É uma concha interativa gratuita com ferramentas Azure comuns pré-instaladas e configuradas para usar com a sua conta. 

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
| **-n** (nome da Nuvem Privada)     | O nome da sua nuvem privada AVS.        |
| **...-localização**     | O local usado para a sua nuvem privada.         |
| **--tamanho do cluster**     | Do tamanho do aglomerado. O valor mínimo é 3.         |
| **-bloco de rede**     | O bloco de rede de endereços IP CIDR para utilizar para a sua nuvem privada. O bloco de endereços não deve sobrepor-se aos blocos de endereços utilizados noutras redes virtuais que se encontram nas suas redes de subscrição e no local.        |
| **--sku** | O valor SKU: AV36 |

```
azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="delete-a-private-cloud-azure-portal"></a>Eliminar uma nuvem privada (portal Azure)

Se tiver uma nuvem privada AVS de que já não precisa, pode eliminá-la. Quando elimina uma nuvem privada, todos os clusters, juntamente com todos os seus componentes, são eliminados.

Para tal, navegue para a sua nuvem privada no portal Azure e selecione **Delete**. Na página de confirmação, confirme com o nome da nuvem privada e selecione **Sim**.

> [!CAUTION]
> Apagar a nuvem privada é uma operação irreversível. Uma vez eliminada a nuvem privada, os dados não podem ser recuperados, uma vez que termina todas as cargas de trabalho, componentes e destrói todos os dados e configurações de nuvem privada, incluindo endereços IP públicos. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma cloud privada do AVS
> * Verificado a Nuvem Privada implantada

Continue até ao próximo tutorial para aprender a criar uma rede virtual para uso com a sua nuvem privada como parte da criação de gestão local para os seus clusters de nuvem privada.

> [!div class="nextstepaction"]
> [Criar uma Rede Virtual](tutorial-configure-networking.md)
