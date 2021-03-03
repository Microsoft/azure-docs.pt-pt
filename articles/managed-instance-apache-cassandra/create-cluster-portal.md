---
title: Quickstart - Criar exemplo gerido azure para o aglomerado Apache Cassandra a partir do portal Azure
description: Este quickstart mostra como criar um Azure Managed Instance para o aglomerado Apache Cassandra usando o portal Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: a05769c66c4b13de5c7197ef5612d64781574987
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101749003"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal-preview"></a>Quickstart: Criar uma instância gerida azure para o cluster Apache Cassandra a partir do portal Azure (Preview)
 
A azure Managed Instance for Apache Cassandra fornece operações automatizadas de implantação e escala para centros de dados apache cassandra geridos de código aberto, acelerando cenários híbridos e reduzindo a manutenção contínua.

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este quickstart demonstra como usar o portal Azure para criar um Azure Managed Instance para o cluster Apache Cassandra.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>Criar um cluster de instâncias gerido

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. A partir da barra de pesquisa, procure **por Caso Gerido para Apache Cassandra** e selecione o resultado.

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="Procura por Exemplo Gerido para Apache Cassandra." lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. Selecione **Create Managed Instance para o botão de cluster Apache Cassandra.**

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="Criar o cluster." lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. A partir do **exemplo de Criar Gerido para Apache Cassandra,** insira os seguintes detalhes:

   * **Subscrição** - A partir do drop-down, selecione a sua subscrição Azure.
   * **Grupo de Recursos**- Especifique se pretende criar um novo grupo de recursos ou utilizar um existente. Um grupo de recursos é um contentor que detém recursos relacionados para uma solução do Azure. Para mais informações, consulte o artigo geral [do Grupo de Recursos Azure.](../azure-resource-manager/management/overview.md)
   * **Nome do cluster** - Introduza um nome para o seu cluster.
   * **Localização** - Localização para onde o seu cluster será implantado.
   * **SKU** - O tipo de SKU para o seu cluster.
   * **Não, não, não, não. de nós**- Número de nós num aglomerado. Estes nós funcionam como réplicas para os seus dados.
   * **Senha de administração inicial de Cassandra** - Palavra-passe que é usada para criar o cluster.
   * **Confirme a palavra-passe de Cassandra** - Reenterça a sua senha.

    > [!NOTE]
    > Durante a pré-visualização pública, você pode criar o cluster de instâncias geridos nas regiões *leste dos EUA, Oeste dos EUA, Leste dos EUA 2, West US 2, Central EUA, Norte da Europa, Europa Ocidental, Sudeste Asiático e Austrália Leste.*

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="Preencha o formulário de agrupamento de criação." lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

1. Em seguida, selecione o **separador 'Rede'.**

1. No painel **de rede,** escolha o nome **de Rede Virtual** e **sub-rede.** Pode selecionar uma Rede Virtual existente ou criar uma nova.

   :::image type="content" source="./media/create-cluster-portal/networking.png" alt-text="Configure detalhes de rede." lightbox="./media/create-cluster-portal/networking.png" border="true":::

1. Se criou um novo VNet no último passo, salte para o passo 9. Se selecionou um VNet existente, antes de criar o seu cluster, tem de aplicar algumas permissões especiais à Rede Virtual e à sub-rede. Para tal, tem de obter o ID de recursos para a sua Rede Virtual existente. Executar o seguinte comando em [Azure CLI:](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)

   ```azurecli-interactive
   # get the resource ID of the Virtual Network
   az network vnet show -n <VNet_name> -g <Resource_Group_Name> --query "id" --output tsv

1. Now apply the special permissions by using the `az role assignment create` command. Replace `<Resource ID>` with the output of previous command:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope <Resource ID>
   ```

   > [!NOTE]
   > Os `assignee` `role` valores e valores no comando anterior são princípio de serviço fixo e identificadores de função, respectivamente.

1. Agora que terminou com networking, clique em **'Revisão +**  >  **Criar'**

    > [!NOTE]
    > Pode levar até 15 minutos para o cluster ser criado.

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="Reveja o resumo para criar o cluster." lightbox="./media/create-cluster-portal/review-create.png" border="true":::


1. Após a implementação ter terminado, verifique o seu grupo de recursos para ver o cluster de instâncias gerido recentemente criado:

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="Página geral após a criação do cluster." lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. Para navegar pelos nosdes de cluster, navegue até ao painel de Rede Virtual que utilizou para criar o cluster e abrir o painel **de visão geral** para os visualizar:

   :::image type="content" source="./media/create-cluster-portal/resources.png" alt-text="Veja os recursos do cluster." lightbox="./media/create-cluster-portal/resources.png" border="true":::



## <a name="connecting-to-your-cluster"></a>Ligação ao seu cluster

Azure Managed Instance for Apache Cassandra não cria nós com endereços IP públicos, por isso, para se ligar ao seu recém-criado cluster Cassandra, terá de criar outro recurso dentro do VNet. Isto pode ser uma aplicação, ou uma Máquina Virtual com a ferramenta de consulta de código aberto da Apache [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) instalada. Pode utilizar um [modelo](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) para implementar uma Máquina Virtual Ubuntu. Quando estiver implantado, utilize o SSH para ligar à máquina e instale o CQLSH utilizando os comandos abaixo:

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar este agrupamento de instâncias gerido, elimine-o com os seguintes passos:

1. A partir do menu à esquerda do portal Azure, selecione **Grupos de Recursos**.
1. A partir da lista, selecione o grupo de recursos que criou para este arranque rápido.
1. No painel **de visão geral** do grupo de recursos, selecione **Delete resource group**.
1. Na janela seguinte, insira o nome do grupo de recursos para eliminar e, em seguida, **selecione Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a criar um Azure Managed Instance para o aglomerado Apache Cassandra usando o portal Azure. Pode agora começar a trabalhar com o cluster:

> [!div class="nextstepaction"]
> [Implementar um Cluster de Faíscas Apache Gerido com Tijolos de Dados Azure](deploy-cluster-databricks.md)
