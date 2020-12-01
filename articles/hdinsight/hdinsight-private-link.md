---
title: Secure and isolate Azure HDInsight clusters com Private Link (pré-visualização)
description: Aprenda a isolar os clusters Azure HDInsight numa rede virtual utilizando o Azure Private Link.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: fac26c616c977eedc466f004a9455297ec995fb8
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352546"
---
# <a name="secure-and-isolate-azure-hdinsight-clusters-with-private-link-preview"></a>Secure and isolate Azure HDInsight clusters com Private Link (pré-visualização)

Na arquitetura de [rede virtual padrão](./hdinsight-virtual-network-architecture.md)da Azure HDInsight, o fornecedor de recursos HDInsight (RP) comunica com o cluster utilizando endereços IP públicos. Alguns cenários requerem isolamento total da rede sem uso de endereços IP públicos. Neste artigo, você aprende sobre os controlos avançados que você pode usar para criar um cluster HDInsight privado. Para obter informações sobre como restringir o tráfego de e para o seu cluster sem isolamento total da rede, consulte [o tráfego da rede de controlo em Azure HDInsight](./control-network-traffic.md).

Pode criar clusters HDInsight privados configurando propriedades de rede específicas num modelo Azure Resource Manager (ARM). Existem duas propriedades que você usa para criar clusters hdinsight privados:

* Remova os endereços IP públicos definindo `resourceProviderConnection` para saída.
* Ativar o Azure Private Link e utilizar [pontos de final privados](../private-link/private-endpoint-overview.md) definindo `privateLink` para ativar.

## <a name="remove-public-ip-addresses"></a>Remover endereços IP públicos

Por predefinição, o HDInsight RP utiliza uma ligação *de entrada* ao cluster utilizando IPs públicos. Quando a propriedade da `resourceProviderConnection` rede é definida para *saída,* inverte as ligações com o HDInsight RP de modo que as ligações são sempre iniciadas de dentro do cluster para fora para o RP. Sem uma ligação de entrada, não há necessidade de etiquetas de serviço de entrada ou endereços IP públicos.

Os equilibradores básicos de carga utilizados na arquitetura de rede virtual padrão fornecem automaticamente NAT público (tradução de endereço de rede) para aceder às dependências de saída necessárias, como o HDInsight RP. Se quiser restringir a conectividade de saída à internet pública, pode [configurar uma firewall](./hdinsight-restrict-outbound-traffic.md), mas não é um requisito.

Configurar `resourceProviderConnection` a saída também permite aceder a recursos específicos do cluster, como a Azure Data Lake Storage Gen2 ou metástases externas, utilizando pontos finais privados. Usar pontos finais privados para estes recursos não é mandetory, mas se planeia ter pontos finais privados para estes recursos, tem de configurar os pontos finais privados e as entradas de DNS `before` que cria o cluster HDInsight. Recomendamos que crie e forneça todas as bases de dados sql externas de que necessita, como Apache Ranger, Ambari, Oozie e Hive metastões, no tempo de criação do cluster. A exigência é que todos estes recursos sejam acessíveis a partir do interior da sub-rede do cluster, quer através do seu próprio ponto final privado, quer através do seu próprio ponto final privado.

A utilização de pontos finais privados para a Azure Key Vault não é suportada. Se estiver a utilizar o Cofre da Chave Azure para a encriptação CMK em repouso, o ponto final do Cofre da Chave Azure deve estar acessível a partir da sub-rede HDInsight sem ponto final privado.

O seguinte diagrama mostra como uma potencial arquitetura de rede virtual HDInsight pode parecer quando `resourceProviderConnection` está definida para saída:

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="Diagrama de arquitetura HDInsight usando uma conexão de fornecedor de recursos de saída":::

Depois de criar o seu cluster, deverá configurar uma resolução de DNS adequada. O seguinte registo de DNS (CNAME) é criado na Zona Pública de DNS gerida pelo Azure: `azurehdinsight.net` .

```dns
<clustername>    CNAME    <clustername>-int
```

Para aceder ao cluster utilizando o cluster FQDNs, pode utilizar diretamente os IPs privados do balançador de carga interno ou utilizar a sua própria Zona Privada de DNS para substituir os pontos finais do cluster, conforme apropriado para as suas necessidades. Por exemplo, pode ter uma Zona Privada de DNS, `azurehdinsight.net` . e adicione os seus IPs privados, conforme necessário:

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

## <a name="enable-private-link"></a>Ativar Ligação Privada

O Private Link, que é desativado por padrão, requer um vasto conhecimento de networking para configurar as rotas definidas pelo utilizador (UDR) e regras de firewall adequadamente antes de criar um cluster. A utilização desta definição é opcional, mas só está disponível quando a propriedade da `resourceProviderConnection` rede está definida para *saída,* conforme descrito na secção anterior.

Quando `privateLink` estiver definido para *ativar,* são [criados os equilibradores de carga padrão internos](../load-balancer/load-balancer-overview.md) (SLB) e é alojado um Serviço de Ligação Privada Azure para cada SLB. O Serviço de Ligação Privada é o que lhe permite aceder ao cluster HDInsight a partir de pontos finais privados.

Os equilibradores de carga padrão não fornecem automaticamente o [NAT de saída pública,](../load-balancer/load-balancer-outbound-connections.md) como fazem os equilibradores básicos de carga. Tem de fornecer a sua própria solução NAT, como [o Virtual Network NAT](../virtual-network/nat-overview.md) ou uma [firewall,](./hdinsight-restrict-outbound-traffic.md)para dependências de saída. O seu cluster HDInsight ainda precisa de acesso às suas dependências de saída. Se estas dependências de saída não forem permitidas, a criação do cluster pode falhar.

### <a name="prepare-your-environment"></a>Preparar o ambiente

Para o sucesso da criação de serviços de ligação privada, deve desativar explicitamente as políticas de [rede para o serviço de ligações privadas.](../private-link/disable-private-link-service-network-policy.md)

O diagrama que se segue mostra um exemplo da configuração de rede necessária antes de criar um cluster. Neste exemplo, todo o tráfego de saída é [forçado](../firewall/forced-tunneling.md) a Azure Firewall usando UDR e as dependências de saída necessárias devem ser "permitidas" na firewall antes de criar um cluster. Para os clusters de pacotes de segurança empresarial, a conectividade da rede com os Serviços de Domínio do Diretório Ativo Azure pode ser fornecida pelo espremiamento VNet.

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="Diagrama do ambiente de ligação privada antes da criação de clusters":::

Uma vez configurado o networking, pode criar um cluster com ligação de fornecedor de recursos de saída e ligação privada ativada, como mostrado na seguinte figura. Nesta configuração, não existem IPs públicos e o Serviço de Ligação Privada é a provisionado para cada balanceador de carga padrão.

:::image type="content" source="media/hdinsight-private-link/after-cluster-creation.png" alt-text="Diagrama do ambiente de ligação privada após criação de cluster":::

### <a name="access-a-private-cluster"></a>Aceda a um cluster privado

Para aceder a clusters privados, pode utilizar diretamente os IPs privados do balançador de carga interno, ou pode utilizar extensões DE DNS de Ligação Privada e Pontos Finais Privados. Quando a `privateLink` definição estiver definida para ativar, pode criar os seus próprios pontos finais privados e configurar a resolução DNS através de zonas privadas de DNS.

As entradas private link criadas na Zona Pública de DNS gerida pelo Azure `azurehdinsight.net` são as seguintes:

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```

A imagem a seguir mostra um exemplo das entradas privadas de DNS necessárias para aceder ao cluster a partir de uma rede virtual que não é espreitada ou não tem uma linha de visão direta para os equilibradores de carga de cluster. Você pode usar Azure Private Zone para sobrepor `*.privatelink.azurehdinsight.net` FQDNs e resolver para seus próprios endereços IP pontos finais privados.

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="Diagrama de arquitetura de ligação privada":::

## <a name="how-to-create-clusters"></a>Como criar clusters?
### <a name="use-arm-template-properties"></a>Use propriedades do modelo ARM

O seguinte corte de código JSON inclui as duas propriedades de rede que precisa de configurar no seu modelo ARM para criar um cluster HDInsight privado.

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound",
    "privateLink": "Enabled" | "Disabled"
}
```

Para um modelo completo com muitas das funcionalidades de segurança da empresa HDInsight, incluindo private link, consulte [o modelo de segurança da empresa HDInsight](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).

### <a name="use-azure-powershell"></a>Utilizar o Azure Powershell

Para utilizar a powershell veja o exemplo [aqui.](/powershell/module/az.hdinsight/new-azhdinsightcluster?view=azps-5.1.0#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature)

### <a name="use-azure-cli"></a>Utilizar a CLI do Azure
Para utilizar o Azure CLI, veja o exemplo [aqui.](/cli/azure/hdinsight?view=azure-cli-latest#az_hdinsight_create-examples)

## <a name="next-steps"></a>Passos seguintes

* [Pacote de segurança da empresa para Azure HDInsight](enterprise-security-package.md)
* [Informações e orientações gerais de segurança da empresa em Azure HDInsight](./domain-joined/general-guidelines.md)