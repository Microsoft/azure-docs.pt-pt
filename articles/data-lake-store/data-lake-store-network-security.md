---
title: Segurança de rede no Azure Data Lake Storage Gen1 | Microsoft Docs
description: Compreender a integração da rede virtual como funciona na geração 1 de armazenamento do Azure Data Lake
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2018
ms.author: elsung
ms.openlocfilehash: 557f331136726110db2a19e20b9d2f5f971ad384
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772913"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1"></a>Integração da rede virtual de geração 1 de armazenamento do Azure Data Lake

Este artigo apresenta a integração da rede virtual de geração 1 de armazenamento do Azure Data Lake. Com a integração da rede virtual, pode configurar as suas contas para que aceitem o tráfego apenas de redes virtuais e sub-redes específicas. 

Esta funcionalidade ajuda a proteger a sua conta do Data Lake Storage de ameaças externas.

A integração da rede virtual para o Data Lake Storage Gen1 utiliza a segurança do ponto final de serviço da rede virtual entre a sua rede virtual e o Azure Active Directory (Azure AD) para gerar afirmações de segurança adicionais no token de acesso. Essas afirmações são, posteriormente, utilizadas para autenticar a rede virtual na conta do Data Lake Storage Gen1 e permitir o acesso.

> [!NOTE]
> A utilização destas capacidades não tem custos adicionais. A sua conta é faturada à taxa padrão do Data Lake Storage Gen1. Para obter mais informações, veja os [preços](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable). Relativamente a todos os outros serviços do Azure que utiliza, veja os [preços](https://azure.microsoft.com/pricing/#product-picker).

## <a name="scenarios-for-virtual-network-integration-for-data-lake-storage-gen1"></a>Cenários para a integração da rede virtual para o Data Lake Storage Gen1

Com a integração da rede virtual do Data Lake Storage Gen1, pode limitar o acesso à sua conta do Data Lake Storage Gen1 a partir de redes virtuais e sub-redes específicas. Quando a conta estiver limitada à sub-rede da rede virtual especificada, as outras redes virtuais/VMs no Azure deixam de ter permissão de acesso. Em termos funcionais, a integração da rede virtual do Data Lake Storage Gen1 proporciona o mesmo cenário dos [pontos finais de serviço da rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). As secções abaixo descrevem algumas diferenças. 

![Diagrama de cenário da integração da rede virtual do Data Lake Storage Gen1](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> As regras da firewall de IP existentes podem ser utilizadas como complemento às regras da rede virtual, de modo a permitir o acesso também a partir de redes no local. 

## <a name="optimal-routing-with-data-lake-storage-gen1-virtual-network-integration"></a>Encaminhamento ideal com a integração da rede virtual do Data Lake Storage Gen1

Uma das principais vantagens dos pontos finais de serviço da rede virtual é o [encaminhamento ideal](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview#key-benefits) a partir da sua rede virtual. Pode efetuar a mesma otimização de rota para as contas do Data Lake Storage Gen1. Utilize as [rotas definidas pelo utilizador](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) seguintes da rede virtual para a conta do Data Lake Storage Gen1.

**Endereço IP público do Data Lake Storage** – utilize o endereço IP público das suas contas do Data Lake Storage Gen1. Para identificar os endereços IP das contas do Data Lake Storage Gen1, [resolva os nomes DNS](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-connectivity-from-vnets#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity) das suas contas. Crie uma entrada separada para cada endereço.

    ```azurecli
    # Create a route table for your resource group.
    az network route-table create --resource-group $RgName --name $RouteTableName
    
    # Create route table rules for Data Lake Storage public IP addresses.
    # There's one rule per Data Lake Storage public IP address. 
    az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet
    
    # Update the virtual network, and apply the newly created route table to it.
    az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
    ```

## <a name="data-exfiltration-from-the-customer-virtual-network"></a>Exfiltração de dados da rede virtual do cliente

Para além de proteger as contas do Data Lake Storage para acesso a partir da redeVirtual, também poderá estar interessado em garantir que não existem exfiltrações a uma conta não autorizada.

Utilize uma solução de firewall na rede virtual para filtrar o tráfego de saída com base no URL da conta de destino. Permita o acesso apenas a contas do Data Lake Storage Gen1 aprovadas.

Algumas opções disponíveis são:
- [Firewall do Azure](https://docs.microsoft.com/azure/firewall/overview): [Implementar e configurar uma firewall do Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) da rede virtual. Proteja o tráfego de saída do Data Lake Storage e limite-o ao URL conhecido e aprovado.
- [Aplicação virtual de rede](https://azure.microsoft.com/solutions/network-appliances/) firewall: O administrador pode permitir a utilização de apenas alguns fornecedores de firewall comercial. Utilize uma solução de firewall de aplicação virtual de rede que esteja disponível no Azure Marketplace para realizar a mesma função.

> [!NOTE]
> A utilização de firewalls no caminho dos dados introduz um salto adicional no caminho. Pode afetar o desempenho da rede relativamente à troca de dados completa. A disponibilidade de débito e a latência da ligação podem ser afetadas. 

## <a name="limitations"></a>Limitações

- Clusters do HDInsight que foram criados antes do suporte de integração da rede virtual de geração 1 de armazenamento do Data Lake estava disponível tem de ser recriadas para suportar esta nova funcionalidade.
 
- Quando cria um cluster do HDInsight novo e seleciona uma conta do Data Lake Storage Gen1 com a integração de rede virtual ativada, o processo falha. Primeiro, desative a regra da rede virtual. No painel **Firewall and virtual networks** (Firewall e redes virtuais) da conta do Data Lake Storage, selecione **Allow access from all networks and services** (Permitir acesso a partir de todas as redes e serviços). Em seguida, criar o cluster de HDInsight antes de finalmente Reabilitar a regra de rede virtual ou anule a seleção **permitir o acesso a partir de todas as redes e serviços**. Para obter mais informações, veja a secção [Exceções](##Exceptions).

- Integração da rede virtual do Data Lake Storage Gen1 não funciona com o [geridos identidades para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
  
- Os dados de ficheiros e pastas na conta do Data Lake Storage Gen1 com a rede virtual ativada não estão acessíveis a partir do portal. Esta restrição inclui acesso a partir de uma VM que esteja dentro da rede virtual, bem como atividades como a utilização do Data Explorer. As atividades de gestão da conta continuam a funcionar. Os dados de ficheiros e pastas na conta do Data Lake Storage com a rede virtual ativada estão acessíveis a partir de todos os recursos externos ao portal. Esses recursos incluem acesso de SDK, scripts do PowerShell e outros serviços do Azure quando não tenham origem no portal. 

## <a name="configuration"></a>Configuração

### <a name="step-1-configure-your-virtual-network-to-use-an-azure-ad-service-endpoint"></a>Passo 1: Configurar a rede virtual para utilizar um ponto de extremidade de serviço do Azure AD

1.  Aceda ao portal do Azure e inicie sessão na sua conta.
 
2.  [Criar uma nova rede virtual](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)na sua subscrição. Em alternativa, pode aceder a uma rede virtual já existente. A rede virtual tem de estar na mesma região que a conta do Data Lake Storage Gen1.
 
3.  No painel **Virtual network** (Rede virtual), selecione **Service endpoints** (Pontos finais de serviço).
 
4.  Selecione **Add** (Adicionar) para adicionar um ponto final de serviço novo.

    ![Adicionar um ponto final de serviço de rede virtual](media/data-lake-store-network-security/config-vnet-1.png)

5.  Selecione **Microsoft.AzureActiveDirectory** como o serviço do ponto final.

     ![Selecionar o ponto final de serviço Microsoft.AzureActiveDirectory](media/data-lake-store-network-security/config-vnet-2.png)

6.  Selecione as sub-redes nas quais pretende permitir a conectividade. Selecione **Adicionar**.

    ![Selecionar a sub-rede](media/data-lake-store-network-security/config-vnet-3.png)

7.  A adição do ponto final de serviço pode demorar até 15 minutos. Quando for adicionado, aparece na lista. Confirme que aparece e que estão configurados todos os detalhes.
 
    ![Adição bem-sucedida do ponto final de serviço](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-virtual-network-or-subnet-for-your-data-lake-storage-gen1-account"></a>Passo 2: Configurar a sub-rede para a sua conta do Data Lake Storage Gen1 ou rede virtual permitido

1.  Depois de configurar a rede virtual, [crie uma conta do Azure Data Lake Storage Gen1 nova](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account) na sua subscrição. Em alternativa, pode aceder a uma conta já existente. A conta do Data Lake Storage Gen1 tem de estar na mesma região que a rede virtual.
 
2.  Selecione **Firewall and virtual networks** (Firewall e redes virtuais).

    > [!NOTE]
    > Se não vir **Firewall and virtual networks** (Firewall e redes virtuais) nas definições, termine sessão no portal. Feche o browser e limpe a cache. Reinicie a máquina e repita.

       ![Adicionar uma regra de rede virtual à conta do Data Lake Storage](media/data-lake-store-network-security/config-adls-1.png)

3.  Selecione **Redes selecionadas**.
 
4.  Selecione **+ Adicionar rede virtual existente**.

    ![Adicionar rede virtual existente](media/data-lake-store-network-security/config-adls-2.png)

5.  Selecione as redes virtuais e as sub-redes que vão ter permissão de conectividade. Selecione **Adicionar**.

    ![Escolher a rede virtual e as sub-redes](media/data-lake-store-network-security/config-adls-3.png)

6.  Confirme que as redes virtuais e as sub-redes aparecem corretamente na lista. Selecione **Guardar**.

    ![Guardar a regra nova](media/data-lake-store-network-security/config-adls-4.png)

    > [!NOTE]
    > Depois de guardar, as definições podem demorar até cinco minutos a entrar em vigor.

7.  [Opcional] Na página **Firewall and virtual networks** (Firewall e redes virtuais), na secção **Firewall**, pode permitir a conectividade a partir de endereços IP específicos. 

## <a name="exceptions"></a>Exceções
Pode permitir a conectividade a partir de serviços e VMs do Azure que estejam fora das redes virtuais selecionadas. No painel **Firewall and virtual networks** (Firewall e redes virtuais), na área **Exceptions** (Exceções), escolha de entre duas opções:
 
- **Permitir que todos os serviços do Azure possam aceder a esta conta do Data Lake Storage Gen1**. Esta opção permite que todos os serviços do Azure, como o Azure Data Factory, os Hubs de Eventos do Azure e todas as VMs do Azure, comuniquem com a conta do Data Lake Storage.

- **Permitir que o Azure Data Lake Analytics aceda a esta conta do Data Lake Storage Gen1**. Esta opção permite a conectividade do Data Lake Analytics a esta conta do Data Lake Storage. 

  ![Exceções de firewall e de rede virtual](media/data-lake-store-network-security/firewall-exceptions.png)

Recomendamos que mantenha estas exceções desativadas. Ative-as apenas se precisar de conectividade a partir desses serviços externos à sua rede virtual.
