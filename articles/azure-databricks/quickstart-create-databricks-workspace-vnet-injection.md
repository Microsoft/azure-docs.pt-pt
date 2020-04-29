---
title: Crie um espaço de trabalho Azure Databricks na sua própria Rede Virtual quickstart
description: Este artigo descreve como implantar os Azure Databricks na sua rede virtual.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 63dd1b4d9396d340dd17a7afb92ff9c38a2b38b3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132664"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-your-own-virtual-network"></a>Quickstart: Crie um espaço de trabalho Azure Databricks na sua própria Rede Virtual

A implementação padrão de Azure Databricks cria uma nova rede virtual que é gerida por Databricks. Este quickstart mostra como criar um espaço de trabalho Azure Databricks na sua própria rede virtual. Também cria um aglomerado de Faíscas Apache dentro desse espaço de trabalho. 

Para obter mais informações sobre o porquê de optar por criar um espaço de trabalho Azure Databricks na sua própria rede virtual, consulte a Implantação de [Tijolos de Dados Azure na sua Rede Virtual Azure (Injeção VNet)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://azure.microsoft.com/free/databricks/)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

> [!Note]
> Este tutorial não pode ser realizado utilizando **a assinatura de teste gratuito do Azure.**
> Se tiver uma conta gratuita, vá ao seu perfil e altere a sua subscrição para **pay-as-you-go**. Para obter mais informações, veja [Conta gratuita do Azure](https://azure.microsoft.com/free/). Em seguida, [remova o limite de gastos](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit), e [solicite um aumento](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) de quota para vCPUs na sua região. Ao criar o seu espaço de trabalho Azure Databricks, pode selecionar o nível de preços **Trial (Premium - 14 dias grátis)** para dar ao espaço de trabalho acesso a DBUs de dados Premium Azure gratuitos durante 14 dias.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. A partir do menu do portal Azure, selecione **Criar um recurso**. Em seguida, selecione **Rede > rede virtual**.

    ![Criar uma rede virtual no portal Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-portal.png)

2. Em **criar rede virtual,** aplique as seguintes definições: 

    |Definição|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Subscrição|\<A sua subscrição\>|Selecione a subscrição do Azure que pretende utilizar.|
    |Grupo de recursos|databricks-quickstart|Selecione **Criar Novo** e introduza um novo nome de grupo de recursos para a sua conta.|
    |Nome|databricks-quickstart|Selecione um nome para a sua rede virtual.|
    |Região|\<Selecione a região mais próxima dos seus utilizadores\>|Selecione uma localização geográfica onde possa hospedar a sua rede virtual. Utilize a localização mais próxima dos seus utilizadores.|

    ![Básicos para uma rede virtual no portal Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Selecione **Seguinte: Endereços IP >** e aplique as seguintes definições. Em seguida, selecione **Rever + criar**.
    
    |Definição|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Espaço de endereçoI4|10.2.0.0/16|A gama de endereços da rede virtual na notação CIDR. A gama CIDR deve estar entre /16 e /24|
    |Nome da sub-rede|predefinição|Selecione um nome para a sub-rede predefinida na sua rede virtual.|
    |Intervalo de Endereços da Sub-rede|10.2.0.0/24|O intervalo de endereços da sub-rede na notação CIDR. Deve ser contido pelo espaço de endereço da rede virtual. A gama de endereços de uma sub-rede que está em uso não pode ser editada.|

    ![Detete configurações IP para uma rede virtual no portal Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-ip-config.png)

4. No **separador Review + criar,** selecione **Criar** para implementar a rede virtual. Assim que a implementação estiver concluída, navegue para a sua rede virtual e selecione **o espaço 'Endereço'** em **Definições**. Na caixa que diz Adicionar intervalo `10.179.0.0/16` de endereço *adicional,* insira e selecione **Guardar**.

    ![Espaço de endereço de rede virtual Azure](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Criar uma área de trabalho do Azure Databricks

1. A partir do menu do portal Azure, selecione **Criar um recurso**. Em seguida, selecione **Analytics > Databricks**.

    ![Criar um espaço de trabalho Azure Databricks no portal Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-portal.png)

2. No **serviço Azure Databricks,** aplique as seguintes definições:

    |Definição|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Nome da área de trabalho|databricks-quickstart|Selecione um nome para o seu espaço de trabalho Azure Databricks.|
    |Subscrição|\<A sua subscrição\>|Selecione a subscrição do Azure que pretende utilizar.|
    |Grupo de recursos|databricks-quickstart|Selecione o mesmo grupo de recursos utilizado para a rede virtual.|
    |Localização|\<Selecione a região mais próxima dos seus utilizadores\>|Escolha o mesmo local que a sua rede virtual.|
    |Escalão de Preço|Escolha entre Standard ou Premium.|Para obter mais informações sobre os níveis de preços, consulte a página de [preços dos Databricks](https://azure.microsoft.com/pricing/details/databricks/).|

    ![Criar um espaço de trabalho Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Depois de terminar de introduzir definições na página **Basics,** selecione **Next: Networking >** e aplique as seguintes definições:

    |Definição|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Implemente o espaço de trabalho dos Tijolos de Dados Azure na sua Rede Virtual (VNet)|Sim|Esta definição permite-lhe implementar um espaço de trabalho Azure Databricks na sua rede virtual.|
    |Rede Virtual|databricks-quickstart|Selecione a rede virtual criada na secção anterior.|
    |Nome da sub-rede pública|sub-rede pública|Utilize o nome de subnet público predefinido.|
    |Gama CIDR de Subnet Pública|10.179.64.0/18|Utilize uma gama CIDR até ao intervalo,incluindo /26.|
    |Nome de sub-rede privada|sub-rede privada|Utilize o nome de sub-rede privado predefinido.|
    |Gama CIDR subnet privada|10.179.0.0/18|Utilize uma gama CIDR até ao intervalo,incluindo /26.|

    ![Adicione informações VNet ao espaço de trabalho azure Databricks no portal Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-vnet-config.png)

3. Uma vez concluída a implantação, navegue para o recurso Azure Databricks. Note que o epeering virtual da rede está desativado. Note também o grupo de recursos e o grupo de recursos geridos na página geral. 

    ![Visão geral dos Azure Databricks no portal Azure](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    O grupo de recursos geridos não é modificável e não é utilizado para criar máquinas virtuais. Só é possível criar máquinas virtuais no grupo de recursos que gere.

    ![Grupo de recursos geridos pela Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

    Quando uma implantação do espaço de trabalho falha, o espaço de trabalho ainda é criado num estado falhado. Eliminar o espaço de trabalho falhado e criar um novo espaço de trabalho que resolva os erros de implementação. Ao eliminar o espaço de trabalho falhado, o grupo de recursos geridos e quaisquer recursos implantados com sucesso também são eliminados.

## <a name="create-a-cluster"></a>Criar um cluster

> [!NOTE]
> Para utilizar uma conta gratuita para criar o cluster do Azure Databricks, antes de criar o cluster, aceda ao seu perfil e altere a subscrição para **pay as you go**. Para obter mais informações, veja [Conta gratuita do Azure](https://azure.microsoft.com/free/).

1. Volte ao serviço Azure Databricks e selecione **Launch Workspace** na página **'Overview'.**

2. Selecione **Clusters** > **+ Criar Cluster**. Em seguida, crie um nome de cluster, como *databricks-quickstart-cluster,* e aceite as definições padrão restantes. Selecione **Criar Cluster**.

    ![Criar cluster de tijolos de dados Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. Assim que o cluster estiver em funcionamento, volte ao grupo de recursos geridos no portal Azure. Note as novas máquinas virtuais, discos, endereço IP e interfaces de rede. Uma interface de rede é criada em cada uma das subredes públicas e privadas com endereços IP.  

    ![Azure Databricks gerido grupo de recursos após criação de cluster](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Volte ao espaço de trabalho do Seu Azure Databricks e selecione o cluster que criou. Em seguida, navegue para o separador **Executores** na página **Spark UI.** Note que os endereços para o condutor e os executores estão na gama de sub-rede privada. Neste exemplo, o condutor é de 10.179.0.6 e os executores são 10.179.0.4 e 10.179.0.5. Os seus endereços IP podem ser diferentes.

    ![Executores de UI de Cedede de Databricks Azur](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Limpar recursos

Depois de ler o artigo, pode terminar o cluster. Para tal, na área de trabalho do Azure Databricks, no painel esquerdo, selecione **Clusters**. Para o cluster que quer terminar, mova o cursor sobre o botão de reticências na coluna **Ações** e selecione o ícone **Terminar**. Isto para o aglomerado.

Se não terminar manualmente o cluster, para automaticamente, desde que selecione o **Terminate após \_ \_ minutos de** caixa de verificação de inatividade enquanto cria o cluster. Nesse caso, o cluster para automaticamente se tiver estado inativo durante o período de tempo especificado.

Se não pretender reutilizar o cluster, pode eliminar o grupo de recursos que criou no portal Azure.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um cluster Spark em Azure Databricks que implementou para uma rede virtual. Avance para o próximo artigo para saber consultar um recipiente SQL Server Linux Docker na rede virtual usando JDBC a partir de um bloco de notas Azure Databricks.  

> [!div class="nextstepaction"]
>[Consultar um contentor de Docker de Linux do SQL Server numa rede virtual a partir de um bloco de notas do Azure Databricks](vnet-injection-sql-server.md)
