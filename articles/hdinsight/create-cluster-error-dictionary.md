---
title: Criar um dicionário de erros de cluster
description: Saiba como criar um dicionário de erros de cluster.
ms.reviewer: hrasheed
author: karkrish
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: troubleshooting
ms.date: 11/19/2019
ms.author: v-todmc
ms.openlocfilehash: ee9ed5374b12c3130d952770a4be6a759e37339a
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156867"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight: erros de criação de cluster

Este artigo descreve as resoluções para erros que podem surgir durante a criação de clusters.

> [!NOTE]
> Os três primeiros erros descritos neste artigo são erros de validação. Eles podem ocorrer quando um produto Azure HDInsight usa a classe **CsmDocument_2_0** .

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Código de erro: falha na validação de DeploymentDocument ' CsmDocument_2_0 '

### <a name="error"></a>Erro

"O local da ação de script não pode ser acessado URI:\<URL da ação de SCRIPT\>"

#### <a name="error-message"></a>Mensagem de erro

"O servidor remoto retornou um erro: (404) não encontrado."

### <a name="cause"></a>Causa

O serviço HDInsight não pode acessar a URL de ação de script que você forneceu como parte da solicitação criar cluster. O serviço recebe a mensagem de erro anterior ao tentar acessar a ação de script.

### <a name="resolution"></a>Resolução

- Para uma URL HTTP ou HTTPS, verifique a URL tentando ir para ela em uma janela do navegador Incognito.
- Para uma URL WASB, verifique se o script existe na conta de armazenamento que você atribuiu na solicitação. Verifique também se a chave de armazenamento desta conta de armazenamento está correta.
- Para uma URL ADLS, certifique-se de que o script existe na conta de armazenamento.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Código de erro: falha na validação de DeploymentDocument ' CsmDocument_2_0 '

### <a name="error"></a>Erro

"O local da ação de script não pode ser acessado URI: \<SCRIPT_ACTION_URL\>"

#### <a name="error-message"></a>Mensagem de erro

"O URI de script fornecido \<SCRIPT_URI\> está em ADLS, mas este cluster não tem uma entidade de armazenamento do data Lake

### <a name="cause"></a>Causa

O serviço HDInsight não pode acessar a URL de ação de script que você forneceu como parte da solicitação criar cluster. O serviço recebe a mensagem de erro anterior ao tentar acessar a ação de script.

### <a name="resolution"></a>Resolução

Adicione a conta Azure Data Lake Storage Gen 1 correspondente ao cluster. Além disso, adicione a entidade de serviço que acessa a conta Data Lake Storage Gen 1 ao cluster.

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Código de erro: falha na validação de DeploymentDocument ' CsmDocument_2_0 '

### <a name="error"></a>Erro

"O tamanho da VM '\<CUSTOMER_SPECIFIED_VM_SIZE\>' fornecido na solicitação é inválido ou não tem suporte para a função '\<\>de função '. Os valores válidos são: \<VALID_VM_SIZE_FOR_ROLE\>".

### <a name="cause"></a>Causa

O tamanho da máquina virtual especificado não é permitido para a função. Esse erro pode ocorrer porque o valor do tamanho da VM não funciona conforme o esperado ou não é adequado para a função do computador.

### <a name="resolution"></a>Resolução

A mensagem de erro lista os valores válidos para o tamanho da VM. Selecione um desses valores e tente a solicitação criar cluster novamente.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Código de erro: InvalidVirtualNetworkId  

### <a name="error"></a>Erro

"O VirtualNetworkId não é válido. VirtualNetworkId '\<USER_VIRTUALNETWORKID\>' * "

### <a name="cause"></a>Causa

O valor de **VirtualNetworkId** especificado durante a criação do cluster não está no formato correto.

### <a name="resolution"></a>Resolução

Verifique se os valores de **VirtualNetworkId** e sub-rede estão no formato correto. Para obter o valor de **VirtualNetworkId** :

1. Aceda ao portal do Azure.
1. Selecione sua rede virtual.
1. Selecione o item de menu **Propriedades** . O valor da propriedade **ResourceId** é o valor **VirtualNetworkId** .

Aqui está um exemplo de uma ID de rede virtual:

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>Código de erro: CustomizationFailedErrorCode

### <a name="error"></a>Erro

"Falha na implantação do cluster devido a um erro na ação de script personalizado. Ações com falha: \<SCRIPT_NAME\>, acesse a interface do usuário do Ambari para depurar ainda mais a falha. "

### <a name="cause"></a>Causa

O script personalizado que você forneceu durante a solicitação criar cluster é executado depois que o cluster é implantado com êxito. Esse código de erro indica que um erro surgiu durante a execução do script personalizado chamado \<SCRIPT_NAME\>.

### <a name="resolution"></a>Resolução

Como o script é seu script personalizado, recomendamos que você solucione o problema e execute o script novamente, se necessário. Para solucionar problemas de falha de script, examine os logs na pasta/var/lib/ambari-Agent/*. Ou abra a página **operações** na interface do usuário do amAmbari e selecione a operação **run_customscriptaction** para exibir os detalhes do erro.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Código de erro: InvalidDocumentErrorCode

### <a name="error"></a>Erro

"A versão de esquema do metastore do \<META_STORE_TYPE\> \<METASTORE_MAJOR_VERSION\> no banco de dados \<database_name\> é incompatível com a versão do cluster \<CLUSTER_VERSION do"

### <a name="cause"></a>Causa

O metastore personalizado é incompatível com a versão selecionada do cluster HDInsight. Atualmente, os clusters do HDInsight 4,0 dão suporte apenas à versão 3,0 e posteriores do metastore, enquanto os clusters do HDInsight 3,6 não dão suporte à versão 3,0 e posteriores do metastore.

### <a name="resolution"></a>Resolução

Use somente as versões do metastore com suporte da versão do cluster HDInsight. Se você não especificar um metastore personalizado, o HDInsight criará internamente um metastore e o excluirá após a exclusão do cluster.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Código de erro: FailedToConnectWithClusterErrorCode 

### <a name="error"></a>Erro

"Não é possível conectar ao ponto de extremidade de gerenciamento de cluster para executar a operação de dimensionamento. Verifique se as regras de segurança de rede não estão bloqueando o acesso externo ao cluster e se a interface do usuário do Gerenciador de cluster (Ambari) pode ser acessada com êxito. "

### <a name="cause"></a>Causa

Uma regra de firewall em seu NSG (grupo de segurança de rede) está bloqueando a comunicação do cluster com serviços críticos de integridade e gerenciamento do Azure.

### <a name="resolution"></a>Resolução

Se você planeja usar grupos de segurança de rede para controlar o tráfego de rede, execute as seguintes ações antes de instalar o HDInsight:

- Identifique a região do Azure que você planeja usar para o HDInsight.
- Identifique os endereços IP exigidos pelo HDInsight. Para obter mais informações, veja [Endereços IP de gestão do HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
  - Crie ou modifique os grupos de segurança de rede para a sub-rede na qual você planeja instalar o HDInsight.
  - Para grupos de segurança de rede, permita o tráfego de entrada na porta 443 dos endereços IP. Essa configuração garante que os serviços de gerenciamento do HDInsight possam acessar o cluster de fora da rede virtual.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Código de erro: StoragePermissionsBlockedForMsi  

### <a name="error"></a>Erro

"A identidade gerenciada não tem permissões na conta de armazenamento. Verifique se a função ' proprietário de dados do blob de armazenamento ' está atribuída à identidade gerenciada para a conta de armazenamento. Armazenamento:/subscriptions/\<ID da assinatura\>/resourceGroups/\< nome do grupo de recursos\>/providers/Microsoft.Storage/storageAccounts/\<nome da conta de armazenamento\>, identidade gerenciada:/subscriptions/\<ID da assinatura\>/resourceGroups//\< nome do grupo de recursos\>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/\<nome da identidade gerenciada do usuário\>"

### <a name="cause"></a>Causa

Você não forneceu as permissões necessárias para gerenciar a identidade. A identidade gerenciada atribuída pelo usuário não tem a função colaborador de armazenamento de BLOBs na conta de armazenamento Azure Data Lake Storage Gen2.

### <a name="resolution"></a>Resolução

1. Abra o portal do Azure.
1. Vá para sua conta de armazenamento.
1. Procure em **controle de acesso (iam)** .
1. Certifique-se de que a função de colaborador de dados do blob de armazenamento ou a função de proprietário de dados do blob de armazenamento tenha acesso "atribuído" à identidade gerenciada atribuída pelo usuário para a assinatura.

Para obter mais informações, consulte [configurar permissões para a identidade gerenciada na conta de data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Código de erro: InvalidNetworkSecurityGroupSecurityRules  

### <a name="error"></a>Erro

"As regras de segurança no grupo de segurança de rede/subscriptions/\<SubscriptionId\>/resourceGroups/< nome do grupo de recursos\> padrão/provedores/Microsoft. Network/networkSecurityGroups/\<nome do grupo de segurança de rede\> configurado com a sub-rede/subscriptions/\<SubscriptionId\>/resourceGroups/\<nome do grupo de recursos\> RG-westeurope-vnet-tomtom-default/Providers/Microsoft. Network/virtualNetworks/\<virtual O nome da rede\>/Subnets/\<nome da sub-rede\> não permite a entrada necessária e/ou a conectividade de saída. Para obter mais informações, visite [planejar uma rede virtual para o Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)ou contate o suporte. "

### <a name="cause"></a>Causa

Se os grupos de segurança de rede ou UDRs (rotas definidas pelo usuário) controlam o tráfego de entrada para seu cluster HDInsight, certifique-se de que o cluster pode se comunicar com os serviços críticos de integridade e gerenciamento do Azure.

### <a name="resolution"></a>Resolução

Se você planeja usar grupos de segurança de rede para controlar o tráfego de rede, execute as seguintes ações antes de instalar o HDInsight:

- Identifique a região do Azure que você planeja usar para o HDInsight e crie uma lista segura dos endereços IP para sua região. Para obter mais informações, consulte [serviços de integridade e gerenciamento: regiões específicas](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions).
- Identifique os endereços IP que o HDInsight exige. Para obter mais informações, consulte [endereços IP de gerenciamento do HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
- Crie ou modifique os grupos de segurança de rede para a sub-rede na qual você planeja instalar o HDInsight. Para grupos de segurança de rede, permita o tráfego de entrada na porta 443 dos endereços IP. Essa configuração garante que os serviços de gerenciamento do HDInsight possam acessar o cluster de fora da rede virtual.
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Código de erro: a instalação do cluster falhou ao instalar componentes em um ou mais hosts

###  <a name="error"></a>Erro

"Falha da instalação do cluster ao instalar componentes em um ou mais hosts. Repita sua solicitação. "

### <a name="cause"></a>Causa 

Normalmente, esse erro é gerado quando há um problema transitório ou uma interrupção do Azure.

### <a name="resolution"></a>Resolução

Verifique a página de [status do Azure](https://status.azure.com/status) para qualquer interrupção do Azure que possa afetar a implantação do cluster. Se não houver interrupções, repita a implantação do cluster.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como solucionar erros de criação de cluster, consulte [solucionar problemas de falhas de criação de cluster com o Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails).
