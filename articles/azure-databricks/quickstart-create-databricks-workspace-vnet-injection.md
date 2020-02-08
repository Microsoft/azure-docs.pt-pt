---
title: Criar um espaço de trabalho Azure Databricks em seu próprio início rápido de rede virtual
description: Este artigo descreve como implantar Azure Databricks em sua rede virtual.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: 046b2da7cdb966192e485ff9f5510eb63c9e0008
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086561"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-your-own-virtual-network"></a>Início rápido: criar um espaço de trabalho Azure Databricks em sua própria rede virtual

A implantação padrão do Azure Databricks cria uma nova rede virtual que é gerenciada pelo databricks. Este guia de início rápido mostra como criar um Azure Databricks espaço de trabalho em sua própria rede virtual em vez disso. Você também cria um cluster Apache Spark dentro desse espaço de trabalho. 

Para obter mais informações sobre o porquê de optar por criar um espaço de trabalho Azure Databricks na sua própria rede virtual, consulte a Implantação de [Tijolos de Dados Azure na sua Rede Virtual Azure (Injeção VNet)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/databricks/).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

> [!Note]
> Este tutorial não pode ser realizado utilizando **a assinatura de teste gratuito do Azure.**
> Se tiver uma conta gratuita, vá ao seu perfil e altere a sua subscrição para **pay-as-you-go**. Para obter mais informações, veja [Conta gratuita do Azure](https://azure.microsoft.com/free/). Em seguida, [remova o limite de gastos](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit), e [solicite um aumento](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) de quota para vCPUs na sua região. Ao criar o seu espaço de trabalho Azure Databricks, pode selecionar o nível de preços **Trial (Premium - 14 dias grátis)** para dar ao espaço de trabalho acesso a DBUs de dados Premium Azure gratuitos durante 14 dias.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. A partir do menu do portal Azure, selecione **Criar um recurso**. Em seguida, selecione **Networking > Rede Virtual**.

    ![Criar uma rede virtual no portal do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-portal.png)

2. Em **criar rede virtual,** aplique as seguintes definições: 

    |Definição|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Subscrição|\<A sua subscrição\>|Selecione a subscrição do Azure que pretende utilizar.|
    |Grupo de recursos|databricks-início rápido|Selecione **Criar Novo** e introduza um novo nome de grupo de recursos para a sua conta.|
    |Nome|databricks-início rápido|Selecione um nome para sua rede virtual.|
    |Região|\<Selecione a região mais próxima dos seus utilizadores\>|Selecione uma localização geográfica na qual você pode hospedar sua rede virtual. Use o local mais próximo de seus usuários.|

    ![Noções básicas para uma rede virtual no portal do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Selecione **Seguinte: Endereços IP >** e aplique as seguintes definições. Em seguida, selecione **Rever + criar**.
    
    |Definição|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Espaço de endereço IPv4|10.2.0.0/16|O intervalo de endereços da rede virtual na notação CIDR. O intervalo CIDR deve estar entre/16 e/24|
    |Nome da sub-rede|predefinição|Selecione um nome para a sub-rede padrão em sua rede virtual.|
    |Intervalo de endereços da sub-rede|10.2.0.0/24|O intervalo de endereços da sub-rede na notação CIDR. Ele deve estar contido no espaço de endereço da rede virtual. O intervalo de endereços de uma sub-rede que está em uso não pode ser editado.|

    ![Definir configurações de IP para uma rede virtual no portal do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-ip-config.png)

4. No **separador Review + criar,** selecione **Criar** para implementar a rede virtual. Assim que a implementação estiver concluída, navegue para a sua rede virtual e selecione **o espaço 'Endereço'** em **Definições**. Na caixa que diz Adicionar intervalo de *endereço adicional,* insira `10.179.0.0/16` e selecione **Guardar**.

    ![Espaço de endereço de rede virtual do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Criar uma área de trabalho do Azure Databricks

1. A partir do menu do portal Azure, selecione **Criar um recurso**. Em seguida, selecione **Analytics > Databricks**.

    ![Criar um espaço de trabalho Azure Databricks no portal do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-portal.png)

2. No **serviço Azure Databricks,** aplique as seguintes definições:

    |Definição|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Nome do espaço de trabalho|databricks-início rápido|Selecione um nome para seu espaço de trabalho Azure Databricks.|
    |Subscrição|\<A sua subscrição\>|Selecione a subscrição do Azure que pretende utilizar.|
    |Grupo de recursos|databricks-início rápido|Selecione o mesmo grupo de recursos que você usou para a rede virtual.|
    |Localização|\<Selecione a região mais próxima dos seus utilizadores\>|Escolha o mesmo local da sua rede virtual.|
    |Escalão de Preço|Escolha entre Standard ou Premium.|Para obter mais informações sobre os níveis de preços, consulte a página de [preços dos Databricks](https://azure.microsoft.com/pricing/details/databricks/).|

    ![Criar um Azure Databricks noções básicas do espaço de trabalho](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Depois de terminar de introduzir definições na página **Basics,** selecione **Seguinte: Networking >** e aplique as seguintes definições:

    |Definição|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Implantar Azure Databricks espaço de trabalho em sua rede virtual (VNet)|Sim|Essa configuração permite que você implante um espaço de trabalho Azure Databricks em sua rede virtual.|
    |Rede Virtual|databricks-início rápido|Selecione a rede virtual que você criou na seção anterior.|
    |Nome da sub-rede pública|sub-rede pública|Use o nome da sub-rede pública padrão.|
    |Intervalo de CIDR da sub-rede pública|10.179.64.0/18|Use um intervalo CIDR até e incluindo/26.|
    |Nome da sub-rede privada|privada-sub-rede|Use o nome da sub-rede privada padrão.|
    |Intervalo de CIDR de sub-rede privada|10.179.0.0/18|Use um intervalo CIDR até e incluindo/26.|

    ![Adicionar informações de VNet ao espaço de trabalho Azure Databricks no portal do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-vnet-config.png)

3. Quando a implantação for concluída, navegue até o recurso Azure Databricks. Observe que o emparelhamento de rede virtual está desabilitado. Observe também o grupo de recursos e o grupo de recursos gerenciados na página Visão geral. 

    ![Azure Databricks visão geral no portal do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    O grupo de recursos gerenciado não é modificável e não é usado para criar máquinas virtuais. Você só pode criar máquinas virtuais no grupo de recursos que você gerencia.

    ![Azure Databricks grupo de recursos gerenciados](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

## <a name="create-a-cluster"></a>Criar um cluster

> [!NOTE]
> Para utilizar uma conta gratuita para criar o cluster do Azure Databricks, antes de criar o cluster, aceda ao seu perfil e altere a subscrição para **pay as you go**. Para obter mais informações, veja [Conta gratuita do Azure](https://azure.microsoft.com/free/).

1. Volte ao serviço Azure Databricks e selecione **Launch Workspace** na página **'Overview'.**

2. Selecione **Clusters** >  **+ Criar Cluster**. Em seguida, crie um nome de cluster, como *databricks-quickstart-cluster,* e aceite as definições padrão restantes. Selecione **Criar Cluster**.

    ![Criar Azure Databricks cluster](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. Depois que o cluster estiver em execução, retorne ao grupo de recursos gerenciados no portal do Azure. Observe as novas máquinas virtuais, discos, endereço IP e interfaces de rede. Uma interface de rede é criada em cada uma das sub-redes públicas e privadas com endereços IP.  

    ![Azure Databricks grupo de recursos gerenciado após a criação do cluster](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Retorne ao seu espaço de trabalho Azure Databricks e selecione o cluster que você criou. Em seguida, navegue para o separador **Executores** na página **Spark UI.** Observe que os endereços para o driver e os executores estão no intervalo de sub-rede privada. Neste exemplo, o driver é 10.179.0.6 e executores são 10.179.0.4 e 10.179.0.5. Seus endereços IP podem ser diferentes.

    ![Azure Databricks executores da interface do usuário do Spark](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Limpar recursos

Depois de ler o artigo, pode terminar o cluster. Para tal, na área de trabalho do Azure Databricks, no painel esquerdo, selecione **Clusters**. Para o cluster que quer terminar, mova o cursor sobre o botão de reticências na coluna **Ações** e selecione o ícone **Terminar**. Isso interrompe o cluster.

Se não terminar manualmente o cluster, para automaticamente, desde que selecione o **Terminate após \_\_ minutos de** caixa de verificação de inatividade enquanto cria o cluster. Nesse caso, o cluster para automaticamente se tiver estado inativo durante o período de tempo especificado.

Se você não quiser reutilizar o cluster, poderá excluir o grupo de recursos criado na portal do Azure.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você criou um cluster Spark no Azure Databricks implantado em uma rede virtual. Avance para o próximo artigo para saber como consultar um contêiner SQL Server do Docker do Linux na rede virtual usando JDBC em um notebook Azure Databricks.  

> [!div class="nextstepaction"]
>[Consulta de um recipiente SQL Server Linux Docker numa rede virtual a partir de um caderno Azure Databricks](vnet-injection-sql-server.md)
