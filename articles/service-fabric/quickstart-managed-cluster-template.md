---
title: Implementar um cluster gerido por Tecido de Serviço (pré-visualização) utilizando o Gestor de Recursos Azure
description: Saiba como criar um cluster gerido por Tecido de Serviço com um modelo de Gestor de Recursos Azure
ms.topic: quickstart
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: 079e8e3e39bd306332abb43fa2ae1f8cc04e305b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91410506"
---
# <a name="quickstart-deploy-a-service-fabric-managed-cluster-preview-with-an-azure-resource-manager-template"></a>Quickstart: Implementar um cluster gerido por tecido de serviço (pré-visualização) com um modelo de Gestor de Recursos Azure

Os clusters geridos pela Service Fabric são uma evolução do modelo de recursos de cluster Azure Service Fabric que dinamiza a sua experiência de implementação e gestão de clusters. Os clusters geridos por tecido de serviço são um recurso totalmente encapsulado que lhe permite implantar um único recurso de cluster de Tecido de Serviço em vez de ter de implantar todos os recursos subjacentes que compõem um cluster de Tecido de Serviço. Este artigo descreve como implementar um cluster gerido por Tecido de Serviço para testes em Azure usando um modelo de Gestor de Recursos Azure (modelo ARM).

O cluster SKU básico de três nó implantado neste tutorial destina-se apenas a ser utilizado para fins instrutivos (em vez de cargas de trabalho de produção). Para mais informações, consulte  [o cluster gerido pela Service Fabric SKUs](overview-managed-cluster.md#service-fabric-managed-cluster-skus).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este arranque rápido:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste arranque rápido é de [Azure Samples - Modelos de cluster de tecido de serviço.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Basic-SKU-1-NT)

## <a name="create-a-client-certificate"></a>Criar um certificado de cliente

Os clusters geridos pela Service Fabric utilizam um certificado de cliente como chave para o controlo de acessos. Se já tem um certificado de cliente que gostaria de usar para controlar o acesso ao seu cluster, pode saltar este passo.

Se precisar de criar um novo certificado de cliente, siga os passos [definidos e recupere um certificado do Azure Key Vault](../key-vault/certificates/quick-create-portal.md).

Tome nota da impressão digital do certificado, uma vez que esta será necessária para implantar o modelo no passo seguinte.

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo.

      [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fservice-fabric-cluster-templates%2Fmaster%2FSF-Managed-Basic-SKU-1-NT%2Fazuredeploy.json)

2. Selecione ou introduza os seguintes valores

    Para este arranque rápido, forneça os seus próprios valores para os seguintes parâmetros do modelo:

    * **Assinatura**: Selecione uma subscrição Azure.
    * **Grupo de Recursos**: Selecione **Criar novo**. Introduza um nome único para o grupo de recursos, como *o myResourceGroup,* em seguida, escolha **OK**.
    * **Localização**: Selecione uma localização, como **eastus2**. As regiões apoiadas para a pré-visualização de clusters geridos por tecidos de serviço `centraluseuap` incluem, , , , e `eastus2euap` `eastasia` `northeurope` `westcentralus` `eastus2` .
    * **Nome do cluster**: Introduza um nome único para o seu cluster, como *o mysfcluster*.
    * **Nome de utilizador Admin**: Introduza um nome para o administrador a utilizar para PDR nos VMs subjacentes no cluster.
    * **Admin Password**: Introduza uma palavra-passe para o administrador a ser utilizado para RDP nos VMs subjacentes no cluster.
    * **Impressão digital do Certificado do Cliente**: Forneça a impressão digital do certificado de cliente que gostaria de utilizar para aceder ao seu cluster. Se não tiver um certificado, siga [o conjunto e recupere um certificado](../key-vault/certificates/quick-create-portal.md) para criar um certificado auto-assinado.
    * **Nome do tipo nó**: Introduza um nome único para o seu tipo de nó, tal como *nt1*.
    * **Concordo com os termos e condições acima indicados:** Verifique esta caixa para concordar. 

3. Selecione **Comprar**.

4. Leva alguns minutos para o seu cluster de tecido de serviço gerido para implementar. Aguarde que a colocação termine com sucesso antes de passar para os próximos passos.

## <a name="validate-the-deployment"></a>Validar a implementação

### <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Assim que a implementação estiver concluída, encontre o valor do Explorador de Tecidos de Serviço na saída e abra o endereço num navegador web para ver o seu cluster no Service Fabric Explorer. Quando solicitado para um certificado, utilize o certificado para o qual a impressão digital do cliente foi fornecida no modelo.

> [!NOTE]
> Pode encontrar a saída da implementação no Portal Azure no separador de implementações do grupo de recursos.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos para o seu cluster gerido por Tecido de Serviço. Para eliminar o grupo de recursos através do portal:

1. Insira o nome do seu grupo de recursos na caixa *de Pesquisa* no topo do portal. Quando vir o grupo de recursos utilizado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Na caixa **ESCREVA O NOME DO GRUPO DE RECURSOS:**, escreva o nome do grupo de recursos e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, implementou um cluster de tecido de serviço gerido. Para saber mais sobre como escalar um cluster, consulte:

> [!div class="nextstepaction"]
> [Dimensione um cluster gerido de tecido de serviço](tutorial-managed-cluster-scale.md)
