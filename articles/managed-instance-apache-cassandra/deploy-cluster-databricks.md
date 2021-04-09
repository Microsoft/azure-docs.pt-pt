---
title: Quickstart - Implementar um Cluster de Faíscas Apache Gerido com Tijolos de Dados Azure
description: Este quickstart mostra como implementar um Cluster de Faíscas Apache Gerido com Azure Databricks usando o portal Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: fd0d5c5b73ae1fb1eae7f38a22913018555ebe11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748966"
---
# <a name="quickstart-deploy-a-managed-apache-spark-cluster-preview-with-azure-databricks"></a>Quickstart: Implementar um Cluster de Faíscas Apache Gerido (Pré-visualização) com Azure Databricks

A azure Managed Instance for Apache Cassandra fornece operações automatizadas de implantação e escala para centros de dados apache cassandra geridos de código aberto, acelerando cenários híbridos e reduzindo a manutenção contínua.

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este quickstart demonstra como usar o portal Azure para criar um cluster Apache Spark totalmente gerido dentro da Rede Virtual Azure do seu Azure Managed Instance para o aglomerado Apache Cassandra. Irá criar o cluster Spark em Azure Databricks. Mais tarde pode criar ou anexar cadernos ao cluster, ler dados de diferentes fontes de dados e analisar insights.

Também pode aprender mais com instruções detalhadas sobre [a implementação de databricks Azure na sua Rede Virtual Azure (Injeção de Rede Virtual)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

## <a name="create-an-azure-databricks-cluster"></a>Criar um cluster Azure Databricks

Siga estes passos para criar um cluster Azure Databricks numa Rede Virtual que tem a Azure Managed Instance para Apache Cassandra:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. No navegador esquerdo, localize **grupos de recursos** e navegue para o seu grupo de recursos que contém a Rede Virtual onde a sua instância gerida é implantada.

1. Abra o recurso **rede virtual** e tome nota do **espaço Address:**

    :::image type="content" source="./media/deploy-cluster-databricks/virtual-network-address-space.png" alt-text="Obtenha o espaço de endereço da sua Rede Virtual." border="true":::

1. A partir do grupo de recursos, **selecione Adicionar** e procure por **Azure Databricks** no campo de pesquisa:

    :::image type="content" source="./media/deploy-cluster-databricks/databricks.png" alt-text="Procure por Azure Databricks." border="true":::

1. Selecione **Criar** para criar uma conta Azure Databricks:

    :::image type="content" source="./media/deploy-cluster-databricks/databricks-create.png" alt-text="Crie uma conta Azure Databricks." border="true":::

1. Preencha os seguintes valores:

   * **Nome do espaço de** trabalho - Forneça um nome para o seu espaço de trabalho Databricks.
   * **Região** - Certifique-se de selecionar a mesma região que a sua Rede Virtual.
   * **Nível de Preços** - Escolha entre Standard, Premium ou Trial. Para obter mais informações sobre estes escalões, veja [Página de preços do Databricks](https://azure.microsoft.com/pricing/details/databricks/).

    :::image type="content" source="./media/deploy-cluster-databricks/select-name.png" alt-text="Preencha o nome do espaço de trabalho, região e nível de preços para a conta Databricks." border="true":::

1. Em seguida, selecione o **separador 'Rede'** e preencha os seguintes detalhes:

   * **Implementar espaço de trabalho Azure Databricks na sua Rede Virtual (VNet)** - Selecione **Sim**.
   * **Rede Virtual** - A partir do dropdown, escolha a Rede Virtual onde existe a sua instância gerida.
   * **Nome da sub-rede pública** - Introduza um nome para a sub-rede pública.
   * **Gama CIDR sub-rede pública** - Introduza um intervalo IP para a sub-rede pública.
   * **Nome da sub-rede privada** - Introduza um nome para a sub-rede privada.
   * **Gama PRIVADA SUB-Rede CIDR** - Introduza um intervalo IP para a sub-rede privada.

   Para evitar colisões de alcance, certifique-se de que seleciona gamas mais altas. Se necessário, utilize uma [calculadora de sub-redes visual](https://www.fryguy.net/wp-content/tools/subnets.html) para dividir os intervalos:

   :::image type="content" source="./media/deploy-cluster-databricks/subnet-calculator.png" alt-text="Utilize a calculadora de sub-rede virtual." border="true":::

   A imagem que se segue mostra detalhes de exemplo no painel de rede:

   :::image type="content" source="./media/deploy-cluster-databricks/subnets.png" alt-text="Especificar nomes de sub-redes públicas e privadas." border="true":::

1. Selecione **Rever e criar** e, em seguida, **criar** para implantar o espaço de trabalho.

1. **Lançar espaço de trabalho** depois de criado.

1. Será redirecionado para o portal do Azure Databricks. A partir do portal, selecione **Novo Cluster**.

1. No **painel de aglomerados Novo,** aceite valores predefinidos para todos os campos que não os seguintes campos:

   * **Nome do Cluster** - Introduza um nome para o cluster.
   * **Databricks Versão runtime** - Selecione Scala 2.11 ou versão inferior que é suportada pelo Conector Cassandra.

    :::image type="content" source="./media/deploy-cluster-databricks/spark-cluster.png" alt-text="Selecione a versão de tempo de execução databricks e o Cluster de Faíscas." border="true":::

1. Expandir **Opções Avançadas** e adicionar a seguinte configuração. Certifique-se de substituir os IPs e credenciais do nó:

    ```java
    spark.cassandra.connection.host <node1 IP>,<node 2 IP>, <node IP>
    spark.cassandra.auth.password cassandra
    spark.cassandra.connection.port 9042
    spark.cassandra.auth.username cassandra
    spark.cassandra.connection.ssl.enabled true
    ```

1. A partir do separador **Bibliotecas,** instale a versão mais recente do conector de faíscas para a Cassandra *e* reinicie o cluster:

    :::image type="content" source="./media/deploy-cluster-databricks/connector.png" alt-text="Instale o conector Cassandra." border="true":::

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar este agrupamento de instâncias gerido, elimine-o com os seguintes passos:

1. A partir do menu à esquerda do portal Azure, selecione **Grupos de Recursos**.
1. A partir da lista, selecione o grupo de recursos que criou para este arranque rápido.
1. No painel **de visão geral** do grupo de recursos, selecione **Delete resource group**.
3. Na janela seguinte, insira o nome do grupo de recursos para eliminar e, em seguida, **selecione Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a criar um cluster Apache Spark totalmente gerido dentro da Rede Virtual do seu aglomerado Azure Managed Instance para Apache Cassandra. Em seguida, pode aprender a gerir os recursos do cluster e datacenter: 

> [!div class="nextstepaction"]
> [Gerir a Azure Managed Instance para os recursos da Apache Cassandra utilizando o Azure CLI](manage-resources-cli.md)

