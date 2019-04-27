---
title: Criar uma área de trabalho do Azure Databricks numa rede Virtual
description: Este artigo descreve como implementar o Azure Databricks à sua rede virtual.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 295b64b10f9f78ca6224d60fb84c6d1310aaa42e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770724"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-a-virtual-network"></a>Início rápido: Criar uma área de trabalho do Azure Databricks numa rede Virtual

Este início rápido mostra como criar uma área de trabalho do Azure Databricks numa rede virtual. Também irá criar um cluster do Apache Spark dentro dessa área de trabalho.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No portal do Azure, selecione **criar um recurso** > **rede** > **rede Virtual**.

2. Sob **criar rede virtual**, aplicar as seguintes definições: 

    |Definição|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Name|databricks-quickstart|Selecione um nome para a rede virtual.|
    |Espaço de endereços|10.1.0.0/16|O intervalo de endereços das redes virtuais na notação CIDR.|
    |Subscrição|\<A sua subscrição\>|Selecione a subscrição do Azure que pretende utilizar.|
    |Grupo de recursos|databricks-quickstart|Selecione **criar novo** e introduza um nome de grupo de recursos novo para a sua conta.|
    |Location|\<Selecione a região mais próxima dos seus utilizadores\>|Selecione uma localização geográfica onde pode alojar a sua rede virtual. Utilize a localização mais próxima dos seus utilizadores.|
    |Nome da sub-rede|predefinição|Selecione um nome para a sub-rede de predefinição na sua rede virtual.|
    |Intervalo de endereços da sub-rede|10.1.0.0/24|O intervalo de endereços da sub-rede na notação CIDR. Tem de estar contido pelo espaço de endereços da rede virtual. Não é possível editar o intervalo de endereços de uma subrede em utilização.|

    ![Criar uma rede virtual no portal do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Assim que a implementação estiver concluída, navegue até à sua rede virtual e selecione **espaço de endereços** sob **definições**. Na caixa que diz *adicionar intervalo de endereços adicionais*, insira `10.179.0.0/16` e selecione **guardar**.

    ![Espaço de endereços de rede virtual do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Criar uma área de trabalho do Azure Databricks

1. No portal do Azure, selecione **criar um recurso** > **análise** > **Databricks**.

2. Sob **serviço Azure Databricks**, aplicar as seguintes definições:

    |Definição|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Nome da área de trabalho|databricks-quickstart|Selecione um nome para a área de trabalho do Azure Databricks.|
    |Subscrição|\<A sua subscrição\>|Selecione a subscrição do Azure que pretende utilizar.|
    |Grupo de recursos|databricks-quickstart|Selecione o mesmo grupo de recursos que utilizou para a rede virtual.|
    |Location|\<Selecione a região mais próxima dos seus utilizadores\>|Escolha a mesma localização que a rede virtual.|
    |Escalão de Preço|Escolha entre o Standard ou Premium.|Para obter mais informações sobre os escalões de preços, consulte a [Databricks página de preços](https://azure.microsoft.com/pricing/details/databricks/).|
    |Implementar o Azure Databricks a área de trabalho na sua rede Virtual|Sim|Esta definição permite-lhe implementar uma área de trabalho do Azure Databricks na sua rede virtual.|
    |Rede Virtual|databricks-quickstart|Selecione a rede virtual que criou na secção anterior.|
    |Nome de sub-rede pública|público-sub-rede|Utilize o nome de sub-rede pública de predefinido.|
    |Intervalo de CIDR de sub-rede público|10.179.64.0/18|Intervalo CIDR para esta sub-rede deve ser entre /18 e /26.|
    |Nome de sub-rede privada|private-subnet|Utilize o nome de sub-rede privado predefinido.|
    |Intervalo de CIDR de sub-rede privada|10.179.0.0/18|Intervalo CIDR para esta sub-rede deve ser entre /18 e /26.|

    ![Criar uma área de trabalho do Azure Databricks no portal do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Assim que a implementação estiver concluída, navegue para o recurso do Azure Databricks. Tenha em atenção que o peering de rede virtual está desabilitado. Observe também o grupo de recursos e o grupo de recursos gerido na página de descrição geral. 

    ![Descrição geral do Azure do Databricks no portal do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    O grupo de recursos gerido contém a localização física da conta de armazenamento (DBFS), trabalho-sg (grupo de segurança de rede), os operadores de vnet (rede virtual). Também é o local onde as máquinas virtuais, o disco, o endereço IP e o interface de rede vão ser criadas. Este grupo de recursos está bloqueado por padrão. No entanto quando um cluster é iniciado na rede virtual, é criada uma Interface de rede entre a vnet de funções de trabalho no grupo de recursos gerido e a rede virtual "hub".

    ![Grupo de recursos gerido do Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

## <a name="create-a-cluster"></a>Criar um cluster

> [!NOTE]
> Para utilizar uma conta gratuita para criar o cluster do Azure Databricks, antes de criar o cluster, aceda ao seu perfil e altere a subscrição para **pay as you go**. Para obter mais informações, veja [Conta gratuita do Azure](https://azure.microsoft.com/free/).

1. Regresse ao seu serviço do Azure Databricks e selecione **iniciar área de trabalho** sobre o **descrição geral** página.

2. Selecione **Clusters** > **+ criar Cluster**. Em seguida, crie um nome de cluster, como *databricks-início rápido-cluster*e aceite as restantes predefinições. Selecione **criar Cluster**.

    ![Criar cluster do Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. Quando o cluster estiver em execução, devolva para o grupo de recursos gerido no portal do Azure. Tenha em atenção as novas máquinas virtuais, discos, endereço IP e interfaces de rede. É criada uma interface de rede em cada uma das sub-redes com endereços IP públicas e privadas.  

    ![Grupo de recursos gerido do Azure Databricks após a criação de cluster](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Regresse à sua área de trabalho do Azure Databricks e selecione o cluster que criou. Em seguida, navegue para o **executores** separador no **interface do Usuário do Spark** página. Tenha em atenção que os endereços para o driver e os executores estão no intervalo de sub-rede privada. Neste exemplo, o driver é 10.179.0.6 e executores são 10.179.0.4 e 10.179.0.5. Os endereços IP podem ser diferentes.

    ![Executores de interface do Usuário do Databricks Spark do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Limpar recursos

Depois de ler o artigo, pode terminar o cluster. Para tal, na área de trabalho do Azure Databricks, no painel esquerdo, selecione **Clusters**. Para o cluster que quer terminar, mova o cursor sobre o botão de reticências na coluna **Ações** e selecione o ícone **Terminar**. Isso deixa o cluster.

Se não terminar manualmente o cluster, este irá parar automaticamente, desde que tenha selecionado a **terminar após \_ \_ minutos de inatividade** caixa de verificação ao criar o cluster. Nesse caso, o cluster para automaticamente se tiver estado inativo durante o período de tempo especificado.

Se não pretender reutilizar o cluster, pode eliminar o grupo de recursos que criou no portal do Azure.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, criou um cluster do Spark no Azure Databricks que implementou a uma rede virtual. Avance para o artigo seguinte para saber como consultar um contentor de Docker do SQL Server Linux na rede virtual usando o JDBC a partir de um bloco de notas do Azure Databricks.  

> [!div class="nextstepaction"]
>[Consultar um contentor de Docker do SQL Server Linux numa rede virtual a partir de um bloco de notas do Azure Databricks](vnet-injection-sql-server.md)
