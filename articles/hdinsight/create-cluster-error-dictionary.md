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
ms.openlocfilehash: e537014f741196024c24dd6ee98af0d8af2e1b31
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483066"
---
# <a name="hdinsight-cluster-creation-errors"></a>HDInsight: erros de criação de cluster

Este artigo descreve as resoluções para erros encontrados durante a criação de clusters. 

> [!NOTE]
> Os três primeiros erros na lista abaixo ocorrem devido a erros de validação quando a classe CsmDocument_2_0 é usada por um produto HDInsight.



## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Código de erro: falha na validação de DeploymentDocument ' CsmDocument_2_0 '

### <a name="error-script-action-location-cannot-be-accessed-uriscript-action-url"></a>**Erro**: o local da ação de script não pode ser acessado URI:\<URL de ação de script\>

**Mensagem de erro: "o servidor remoto retornou um erro: (404) não encontrado."**

### <a name="cause"></a>Causa
A URL de ação de script fornecida como parte da solicitação de criação de cluster não é acessível do serviço HDInsight. Recebemos o "ErrorMessage" quando tentamos acessar a ação de script.

### <a name="resolution"></a>Resolução 
  - Para uma URL http/https, você pode verificar tentando abrir a URL em uma janela do navegador Incognito. 
  - Para uma URL WASB, verifique se o script existe na conta de armazenamento que é fornecida na solicitação. Verifique se a chave de armazenamento desta conta de armazenamento é precisa. 
  - Para obter uma URL ADLS, verifique se o script existe na conta de armazenamento.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Código de erro: falha na validação de DeploymentDocument ' CsmDocument_2_0 '

### <a name="error-script-action-location-cannot-be-accessed-uri-script-action-url"></a>**Erro**: o local da ação de script não pode ser acessado URI: \<URL de ação de script\>

**Mensagem de erro: o URI de script especificado \<URI de SCRIPT\> está em ADLS, mas este cluster não tem uma entidade de armazenamento do data Lake**

### <a name="cause"></a>Causa
A URL de ação de script fornecida como parte da solicitação de criação de cluster não é acessível do serviço HDInsight. Recebemos o "ErrorMessage" quando tentamos acessar a ação de script. 

### <a name="resolution"></a>Resolução

Certifique-se de que a conta Azure Data Lake Store Gen 1 correspondente seja adicionada ao cluster. A entidade de serviço usada para acessar a conta Azure Data Lake Store Gen 1 também é adicionada ao cluster. 

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Código de erro: falha na validação de DeploymentDocument ' CsmDocument_2_0 '

### <a name="error-vm-size-customer-specified-vm-size-provided-in-the-request-is-invalid-or-not-supported-for-role-role-valid-values-are-valid-vm-size-for-role"></a>**Erro**: o tamanho da vm '\<tamanho da VM especificado pelo cliente\>' fornecido na solicitação é inválido ou não tem suporte para a função '\<\>de função '. Os valores válidos são: \<tamanho de VM válido para\>de função.

### <a name="cause"></a>Causa
Os tamanhos de VM especificados pelo cliente não são permitidos para a função. Isso pode ser verdadeiro porque o valor do tamanho da VM não está funcionando conforme o esperado ou não é adequado para a função do computador. 

### <a name="resolution"></a>Resolução
A mensagem de erro lista os valores válidos para o tamanho da VM. Selecione um desses valores válidos e tente a solicitação criar cluster novamente. 

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Código de erro: InvalidVirtualNetworkId  

### <a name="error-the-virtualnetworkid-is-not-valid-virtualnetworkid-user_virtualnetworkid"></a>**Erro**: o VirtualNetworkId não é válido. VirtualNetworkId '\<USER_VIRTUALNETWORKID\>' *

### <a name="cause"></a>Causa
O valor de **VirtualNetworkId** especificado durante a criação do cluster não está no formato correto. 

### <a name="resolution"></a>Resolução
Verifique se **VirtualNetworkId** e sub-rede estão no formato correto. Para obter o valor de **VirtualnetworkId** , vá para o portal do Azure, selecione sua rede virtual e, em seguida, selecione **Propriedades** no menu. A propriedade **ResourceId** é o valor **VirtualNetworkId** . 

Um exemplo de uma ID de rede virtual é: 

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet" 

---

## <a name="error-code-customizationfailederrorcode"></a>Código de erro: CustomizationFailedErrorCode

### <a name="error-cluster-deployment-failed-due-to-an-error-in-the-custom-script-action-failed-actions-script_name-please-go-to-ambari-ui-to-further-debug-the-failure"></a>**Erro**: falha na implantação do cluster devido a um erro na ação de script personalizado. Ações com falha: \<SCRIPT_NAME\>, acesse a interface do usuário do Ambari para depurar ainda mais a falha.

### <a name="cause"></a>Causa
O script personalizado do usuário que foi fornecido durante a solicitação de criação de cluster é executado depois que o cluster é implantado com êxito. Esse código de erro indica que foi encontrado um erro ao executar esse script personalizado com o nome \<SCRIPT_NAME\>.   

### <a name="resolution"></a>Resolução
Como esse é o script personalizado do usuário, os usuários devem solucionar o problema e executar o script novamente, se necessário. Para solucionar problemas de falha de script, examine os logs na pasta/var/lib/ambari-Agent/*. Ou abra a página operações na interface do usuário do amAmbari e selecione a operação **run_customscriptionaction** para exibir os detalhes do erro. 

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Código de erro: InvalidDocumentErrorCode

### <a name="error-the-meta_store_type-metastore-schema-version-metastore_major_version-in-database-database_name-is-incompatible-with-cluster-version-cluster_version"></a>**Erro**: a versão do esquema do \<META_STORE_TYPE\> \<METASTORE_MAJOR_VERSION\> no banco de dados \<database_name\> é incompatível com a versão do cluster \<CLUSTER_VERSION\>

### <a name="cause"></a>Causa
O metastore personalizado é incompatível com a versão selecionada do cluster HDInsight. Atualmente, o cluster HDInsight 4,0 dá suporte apenas à versão 3 do metastore. *x*, e o HDInsight 3,6 não oferece suporte à versão 3 do metastore. *x* ou posterior. 

### <a name="resolution"></a>Resolução
Certifique-se de usar apenas as versões do metastore com suporte em cada versão do cluster HDInsight. Observe que, se um metastore personalizado não for especificado, o HDInsight criará internamente um metastore. No entanto, esse metastore será excluído automaticamente após a exclusão do cluster. 

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Código de erro: FailedToConnectWithClusterErrorCode 

### <a name="error-unable-to-connect-to-cluster-management-endpoint-to-perform-scaling-operation-verify-that-network-security-rules-are-not-blocking-external-access-to-the-cluster-and-that-the-cluster-manager-ambari-ui-can-be-successfully-accessed"></a>**Erro**: não é possível se conectar ao ponto de extremidade de gerenciamento de cluster para executar a operação de dimensionamento. Verifique se as regras de segurança de rede não estão bloqueando o acesso externo ao cluster e se a interface do usuário do Gerenciador de cluster (Ambari) pode ser acessada com êxito.

### <a name="cause"></a>Causa
Você tem uma regra de firewall em seu NSG (grupo de segurança de rede) que está bloqueando a comunicação de cluster com serviços críticos de integridade e gerenciamento do Azure. 

### <a name="resolution"></a>Resolução
Se você planeja usar **grupos de segurança de rede** para controlar o tráfego de rede, execute as seguintes ações antes de instalar o HDInsight: 
  - Identifique a região do Azure que você planeja usar para o HDInsight. 
  - Identifique os endereços IP exigidos pelo HDInsight. Para obter mais informações, veja [Endereços IP de gestão do HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses). 
    - Crie ou modifique os grupos de segurança de rede para a sub-rede na qual você planeja instalar o HDInsight. 
    - **Grupos de segurança de rede:** Permitir tráfego de **entrada** na porta **443** dos endereços IP. Isso garante que os serviços de gerenciamento do HDInsight possam acessar o cluster de fora da rede virtual. 

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Código de erro: StoragePermissionsBlockedForMsi  

### <a name="error-the-managed-identity-does-not-have-permissions-on-the-storage-account-please-verify-that-storage-blob-data-owner-role-is-assigned-to-the-managed-identity-for-the-storage-account-storage-subscriptions-subscription-id-resourcegroups-resource-group-name-providersmicrosoftstoragestorageaccounts-storage-account-name-managed-identity-subscriptions-subscription-id-resourcegroups--resource-group-name-providersmicrosoftmanagedidentityuserassignedidentities-user-managed-identity-name"></a>**Erro**: a identidade gerenciada não tem permissões na conta de armazenamento. Verifique se a função ' proprietário de dados do blob de armazenamento ' está atribuída à identidade gerenciada para a conta de armazenamento. Armazenamento:/subscriptions/\<ID da assinatura\>/resourceGroups/\< nome do grupo de recursos\>/providers/Microsoft.Storage/storageAccounts/\<nome da conta de armazenamento\>, identidade gerenciada:/subscriptions/\<ID da assinatura\>/resourceGroups//\< nome do grupo de recursos\>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/\<nome da identidade gerenciada do usuário\>

### <a name="cause"></a>Causa
As permissões necessárias não foram fornecidas para **gerenciar a identidade.** **A identidade gerenciada atribuída pelo usuário** não tinha a função colaborador de armazenamento de BLOBs na conta de armazenamento ADLS Gen2. 

### <a name="resolution"></a>Resolução

Abra o portal do Azure, vá para sua conta de armazenamento, procure por **iam (controle de acesso)** e certifique-se de que a função de proprietário de dados de blob de armazenamento ou de data blob de armazenamento tenha o acesso "" atribuído "à **identidade gerenciada atribuída pelo usuário** para a assinatura. Para obter mais informações, consulte [configurar permissões para a identidade gerenciada na conta de data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md). 

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Código de erro: InvalidNetworkSecurityGroupSecurityRules  

### <a name="error-the-security-rules-in-the-network-security-group-subscriptionssubscriptionidresourcegroupsresource-group-name-defaultprovidersmicrosoftnetworknetworksecuritygroupsnetwork-security-group-name-configured-with-subnet-subscriptionssubscriptionidresourcegroupsresource-group-name-rg-westeurope-vnet-tomtom-defaultprovidersmicrosoftnetworkvirtualnetworksvirtual-network-namesubnetssubnet-name"></a>**Erro**: as regras de segurança no grupo de segurança de rede/subscriptions/\<subscriptionid >\/resourceGroups/<Resource Group name> padrão/provedores/Microsoft. Network/networkSecurityGroups/\<nome do grupo de segurança de rede\> configurado com a sub-rede/subscriptions/\<subscriptionid >\/resourceGroups/\<de grupo de recursos > RG-westeurope-vnet-TomTom-default\/Providers/Microsoft. Network/virtualNetworks/\<virtual Nome da rede >\/sub-redes/\<nome da sub-rede\> 
Não permite a conectividade de entrada e/ou saída necessária. Para obter mais informações, visite [planejar uma rede virtual para o Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)ou contate o suporte. * *

### <a name="cause"></a>Causa
Se você usar NSGs (grupos de segurança de rede) ou UDRs (rotas definidas pelo usuário) para controlar o tráfego de entrada para o cluster HDInsight, deverá garantir que o cluster possa se comunicar com os serviços críticos de integridade e gerenciamento do Azure.

### <a name="resolution"></a>Resolução
Se você planeja usar **grupos de segurança de rede** para controlar o tráfego de rede, execute as seguintes ações antes de instalar o HDInsight: 
  - Identifique a região do Azure que você planeja usar para o HDInsight e crie uma lista segura dos endereços IP para sua região: [serviços de integridade e gerenciamento: regiões específicas](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions).
  - Identifique os endereços IP exigidos pelo HDInsight. Para obter mais informações, consulte [endereços IP de gerenciamento do HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses). 
  - Crie ou modifique os grupos de segurança de rede para a sub-rede na qual você planeja instalar o HDInsight. **Grupos de segurança de rede**: permitir tráfego de entrada na porta **443** dos endereços IP. Isso garantirá que os serviços de gerenciamento do HDInsight possam acessar o cluster de fora da rede virtual.
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Código de erro: a instalação do cluster falhou ao instalar componentes em um ou mais hosts

###  <a name="errorcluster-setup-failed-to-install-components-on-one-or-more-hosts-please-retry-your-request"></a>**Erro**: a instalação do cluster falhou ao instalar componentes em um ou mais hosts. Repita a solicitação. 

### <a name="cause"></a>Causa 
Normalmente, esse erro é gerado quando há um problema transitório ou há uma interrupção do Azure 

### <a name="resolution"></a>Resolução

Verifique a página de [status do Azure](https://status.azure.com/status) para qualquer interrupção potencial do Azure que possa afetar a implantação do cluster. Se não houver interrupções, repita a implantação do cluster. 

## <a name="next-steps"></a>Próximos Passos

Para obter mais informações sobre como solucionar erros de criação de cluster, consulte [solucionar problemas de falhas de criação de cluster com o Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails).
