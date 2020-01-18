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
ms.openlocfilehash: 8d118170de01c7685ac9dba65c7e22cefb6d4829
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263168"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-your-own-virtual-network"></a>Início rápido: criar um espaço de trabalho Azure Databricks em sua própria rede virtual

A implantação padrão do Azure Databricks cria uma nova rede virtual que é gerenciada pelo databricks. Este guia de início rápido mostra como criar um Azure Databricks espaço de trabalho em sua própria rede virtual em vez disso. Você também cria um cluster Apache Spark dentro desse espaço de trabalho. 

Para obter mais informações sobre por que você pode optar por criar um Azure Databricks espaço de trabalho em sua própria rede virtual, consulte [implantar Azure Databricks na sua rede virtual do Azure (injeção de VNet)] (/databricks/Administration-Guide/Cloud-Configurations/Azure/vnet-Inject).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/databricks/).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

> [!Note]
> Este tutorial não pode ser executado usando a **assinatura de avaliação gratuita do Azure**.
> Se você tiver uma conta gratuita, vá para seu perfil e altere sua assinatura para pré- **pago**. Para obter mais informações, veja [Conta gratuita do Azure](https://azure.microsoft.com/free/). Em seguida, [remova o limite de gastos](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)e [solicite um aumento de cota](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) para vCPUs em sua região. Ao criar seu espaço de trabalho Azure Databricks, você pode selecionar o tipo de preço de **avaliação (Premium-14 dias gratuitos DBUs)** para dar ao espaço de trabalho o acesso Premium Azure Databricks DBUs por 14 dias.

## <a name="create-a-virtual-network"></a>Criar rede virtual

1. No menu do portal do Azure, selecione **Criar um recurso**. Em seguida, selecione **redes > rede virtual**.

    ![Criar uma rede virtual no portal do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-portal.png)

2. Em **criar rede virtual**, aplique as seguintes configurações: 

    |Definição|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Subscrição|\<A sua subscrição\>|Selecione a subscrição do Azure que pretende utilizar.|
    |Grupo de recursos|databricks-início rápido|Selecione **criar novo** e insira um novo nome de grupo de recursos para sua conta.|
    |Nome|databricks-início rápido|Selecione um nome para sua rede virtual.|
    |Região|\<Selecione a região mais próxima dos seus utilizadores\>|Selecione uma localização geográfica na qual você pode hospedar sua rede virtual. Use o local mais próximo de seus usuários.|

    ![Noções básicas para uma rede virtual no portal do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Selecione **Avançar: endereços IP >** e aplique as configurações a seguir. Em seguida, selecione **revisar + criar**.
    
    |Definição|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Espaço de endereço IPv4|10.2.0.0/16|O intervalo de endereços da rede virtual na notação CIDR. O intervalo CIDR deve estar entre/16 e/24|
    |Nome da sub-rede|predefinição|Selecione um nome para a sub-rede padrão em sua rede virtual.|
    |Intervalo de Endereços da Sub-rede|10.2.0.0/24|O intervalo de endereços da sub-rede na notação CIDR. Ele deve estar contido no espaço de endereço da rede virtual. O intervalo de endereços de uma sub-rede que está em uso não pode ser editado.|

    ![Definir configurações de IP para uma rede virtual no portal do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-ip-config.png)

4. Na guia **revisar + criar** , selecione **criar** para implantar a rede virtual. Depois que a implantação for concluída, navegue até sua rede virtual e selecione **espaço de endereço** em **configurações**. Na caixa que diz *Adicionar intervalo de endereços adicional*, insira `10.179.0.0/16` e selecione **salvar**.

    ![Espaço de endereço de rede virtual do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Criar uma área de trabalho do Azure Databricks

1. No menu do portal do Azure, selecione **Criar um recurso**. Em seguida, selecione **Analytics > databricks**.

    ![Criar um espaço de trabalho Azure Databricks no portal do Azure](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-portal.png)

2. Em **serviço Azure Databricks**, aplique as seguintes configurações:

    |Definição|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Nome da área de trabalho|databricks-início rápido|Selecione um nome para seu espaço de trabalho Azure Databricks.|
    |Subscrição|\<A sua subscrição\>|Selecione a subscrição do Azure que pretende utilizar.|
    |Grupo de recursos|databricks-início rápido|Selecione o mesmo grupo de recursos que você usou para a rede virtual.|
    |Localização|\<Selecione a região mais próxima dos seus utilizadores\>|Escolha o mesmo local da sua rede virtual.|
    |Escalão de Preço|Escolha entre Standard ou Premium.|Para obter mais informações sobre tipos de preço, consulte a [página de preços do databricks](https://azure.microsoft.com/pricing/details/databricks/).|

    ![Criar um Azure Databricks noções básicas do espaço de trabalho](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Depois de terminar de inserir as configurações na página **noções básicas** , selecione **avançar: rede >** e aplique as seguintes configurações:

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

1. Retorne ao serviço de Azure Databricks e selecione **Iniciar espaço de trabalho** na página **visão geral** .

2. Selecione **clusters** >  **+ criar cluster**. Em seguida, crie um nome de cluster, como *databricks-QuickStart-cluster*, e aceite as configurações padrão restantes. Selecione **Create Cluster** (Criar Cluster).

    ![Criar Azure Databricks cluster](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. Depois que o cluster estiver em execução, retorne ao grupo de recursos gerenciados no portal do Azure. Observe as novas máquinas virtuais, discos, endereço IP e interfaces de rede. Uma interface de rede é criada em cada uma das sub-redes públicas e privadas com endereços IP.  

    ![Azure Databricks grupo de recursos gerenciado após a criação do cluster](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Retorne ao seu espaço de trabalho Azure Databricks e selecione o cluster que você criou. Em seguida, navegue até a guia **executores** na página **da interface do usuário do Spark** . Observe que os endereços para o driver e os executores estão no intervalo de sub-rede privada. Neste exemplo, o driver é 10.179.0.6 e executores são 10.179.0.4 e 10.179.0.5. Seus endereços IP podem ser diferentes.

    ![Azure Databricks executores da interface do usuário do Spark](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Limpar recursos

Depois de ler o artigo, pode terminar o cluster. Para tal, na área de trabalho do Azure Databricks, no painel esquerdo, selecione **Clusters**. Para o cluster que quer terminar, mova o cursor sobre o botão de reticências na coluna **Ações** e selecione o ícone **Terminar**. Isso interrompe o cluster.

Se não terminar manualmente o cluster, este irá parar automaticamente, desde que tenha selecionado a **terminar após \_ \_ minutos de inatividade** caixa de verificação ao criar o cluster. Nesse caso, o cluster para automaticamente se tiver estado inativo durante o período de tempo especificado.

Se você não quiser reutilizar o cluster, poderá excluir o grupo de recursos criado na portal do Azure.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você criou um cluster Spark no Azure Databricks implantado em uma rede virtual. Avance para o próximo artigo para saber como consultar um contêiner SQL Server do Docker do Linux na rede virtual usando JDBC em um notebook Azure Databricks.  

> [!div class="nextstepaction"]
>[Consultar um SQL Server contêiner do Docker do Linux em uma rede virtual de um notebook Azure Databricks](vnet-injection-sql-server.md)
