---
title: Tutorial - Implementar cluster vSphere em Azure
description: Aprenda a implementar um cluster vSphere em Azure utilizando a Azure VMWare Solution (AVS)
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 6a192454367f2e6ca071e9cfe0a9f1a94868cbdb
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82977709"
---
# <a name="tutorial-deploy-an-avs-private-cloud-in-azure"></a>Tutorial: Implementar uma nuvem privada AVS em Azure

A Azure VMware Solution (AVS) dá-lhe a capacidade de implantar um cluster vSphere em Azure. O destacamento inicial mínimo é de três anfitriões. Anfitriões adicionais podem ser adicionados um de cada vez, até um máximo de 16 anfitriões por cluster. 

Uma vez que o AVS não lhe permitirá gerir a sua nuvem privada com o seu vCenter no local no lançamento, terá de realizar uma configuração adicional e ligação a uma instância local vCenter, rede virtual e muito mais. Estes procedimentos e pré-requisitos conexos serão abordados nesta série tutorial.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma cloud privada do AVS
> * Verifique a nuvem privada implantada

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Direitos administrativos adequados e permissão para criar uma nuvem privada.
- Certifique-se de que tem a rede adequada configurada conforme descrito no Tutorial: Lista de [verificação de rede](tutorial-network-checklist.md).

## <a name="register-the-resource-provider"></a>Registar o fornecedor de recursos

Para utilizar a Solução Azure VMWare, tem primeiro de registar o fornecedor de recursos. O exemplo seguinte regista o fornecedor de recursos com a sua subscrição.

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Para obter formas adicionais de registar o fornecedor de recursos, consulte os fornecedores e tipos de [recursos do Azure.](../azure-resource-manager/management/resource-providers-and-types.md)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Criar uma Cloud Privada

Pode criar uma nuvem privada AVS utilizando o [portal Azure](#azure-portal) ou utilizando o [Azure CLI](#azure-cli).

### <a name="azure-portal"></a>Portal do Azure

No portal Azure, selecione **+ Crie um novo recurso.** No **Search the Marketplace** tipo `Azure VMware Solution`de caixa de texto Marketplace , e selecione **Azure VMware Solution** da lista. Na janela **Azure VMware Solution,** selecione **Create**

No separador **Basics,** introduza valores para os campos. A tabela seguinte mostra uma lista detalhada das propriedades.

| Campo   | Valor  |
| ---| --- |
| **Subscrição** | A subscrição que pretende utilizar para a implementação.|
| **Grupo de recursos** | O grupo de recursos para os seus recursos privados na nuvem. |
| **Localização** | Selecione um local, como **o leste de nós.**|
| **Nome do recurso** | O nome da sua nuvem privada AVS. |
| **SKU** | Selecione o valor SKU seguinte: AV36 |
| **Anfitriões** | Este é o número de anfitriões para adicionar ao aglomerado privado de nuvens. O valor padrão é 3. Este valor pode ser aumentado ou reduzido após a implantação.  |
| **senha de administração vCenter** | Introduza uma senha de administrador de nuvem. |
| **Senha de gerente NSX-T** | Introduza uma senha de administrador NSX-T. |
| **Bloco de endereços** | Introduza um bloco de endereçoIP para a rede CIDR para a nuvem privada. Um exemplo é, 10.175.0.0/22. |

:::image type="content" source="./media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="criar uma nuvem privada" border="true":::

Uma vez terminado, selecione **Review + Create**. No ecrã seguinte verifique as informações introduzidas. Se a informação estiver correta, selecione **Criar**.

> [!NOTE]
> Este passo leva cerca de duas horas. 

### <a name="azure-cli"></a>CLI do Azure

Em alternativa, pode utilizar o Azure CLI para criar uma nuvem privada AVS em Azure. Para isso pode utilizar a Casca de Nuvem Azure, os seguintes passos mostram-lhe como fazê-lo.

#### <a name="open-azure-cloud-shell"></a>Abrir o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a https://shell.azure.com/bash. Selecione **Copiar** para copiar os blocos de código, cole-o na Cloud Shell e prima **Enter** para o executar.

#### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* na localização *oriental:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Criar uma cloud privada

Para criar uma nuvem privada AVS deve fornecer um nome de grupo de recursos, um nome para a nuvem privada, uma localização, o tamanho do cluster


|Propriedade  |Descrição  |
|---------|---------|
|Nome do Grupo de Recursos     | O nome do grupo de recursos para o que está a implantar a nuvem privada.        |
|Nome da nuvem privada     | O nome da nuvem privada.        |
|Localização     | O local que é usado para a nuvem privada         |
|Tamanho do cluster     | Do tamanho do aglomerado. O valor mínimo é 3.         |
|Bloco de rede     | A gama CIDR para usar para a nuvem privada. Recomenda-se que seja único a partir do seu ambiente nas instalações, bem como do seu ambiente Azure.        |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22
```

## <a name="verify-deployment-was-successful"></a>Verificar que a implantação foi bem sucedida

Navegue para o grupo de recursos que criou e selecione a sua nuvem privada, quando a implementação estiver concluída, verá o seguinte ecrã e verá o estado do **Sucesso**.

:::image type="content" source="./media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Validar a nuvem privada implantada" border="true":::

## <a name="delete-a-private-cloud"></a>Eliminar uma cloud privada

Se tiver uma nuvem privada AVS que verificou que já não precisa, pode apagá-la. Ao eliminar uma nuvem privada, todos os clusters juntamente com todos os seus componentes são eliminados.

Para isso, navegue para a sua nuvem privada no portal Azure e selecione **Delete**. Na página de confirmação, confirme com o nome da nuvem privada e selecione **Sim**.

> [!CAUTION]
> Apagar a nuvem privada é uma operação irreversível. Uma vez eliminada a nuvem privada, os dados não podem ser recuperados, uma vez que encerra todas as cargas de trabalho, componentes e destrói todos os dados e configurações de configuração privadas da nuvem, incluindo endereços IP públicos. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma cloud privada do AVS
> * Verificado a Nuvem Privada implantada

Continue para o próximo tutorial para aprender a criar uma rede virtual para uso com a sua nuvem privada como parte da criação de gestão local para os seus clusters de nuvem privada.

> [!div class="nextstepaction"]
> [Criar uma Rede Virtual](tutorial-configure-networking.md)
