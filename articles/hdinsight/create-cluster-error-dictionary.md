---
title: Criar um dicionário de erros de cluster
description: Aprenda a criar um dicionário de erros de cluster.
author: karkrish
ms.author: v-todmc
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/19/2019
ms.openlocfilehash: 979e83c0eeaac4555fc5144bca479f0b5656cd28
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617528"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight: Erros de criação de cluster

Este artigo descreve resoluções para erros que pode encontrar ao criar clusters.

> [!NOTE]
> Os três primeiros erros descritos neste artigo são erros de validação. Podem ocorrer quando um produto Azure HDInsight utiliza a classe **CsmDocument_2_0.**

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Código de erro: ImplementaçãoDocument 'CsmDocument_2_0' falhou na validação

### <a name="error"></a>Erro

"A localização da Ação de Script não pode ser acedida uri:\<URL de AÇÃO SCRIPT\>"

#### <a name="error-message"></a>Mensagem de erro

"O servidor remoto devolveu um erro: (404) Não encontrado."

### <a name="cause"></a>Causa

O serviço HDInsight não pode aceder ao URL de ação de script que forneceu como parte do pedido create cluster. O serviço recebe a mensagem de erro anterior quando tenta aceder à ação do script.

### <a name="resolution"></a>Resolução

- Para um URL HTTP ou HTTPS, verifique o URL tentando ir até ele a partir de uma janela de navegador incógnita.
- Para um URL WASB, certifique-se de que o script existe na conta de armazenamento que você dá no pedido. Certifique-se também de que a chave de armazenamento desta conta de armazenamento está correta.
- Para um URL ADLS, certifique-se de que o script existe na conta de armazenamento.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Código de erro: ImplementaçãoDocument 'CsmDocument_2_0' falhou na validação

### <a name="error"></a>Erro

"A localização da Ação de Script não pode ser acedida a URI: \<SCRIPT_ACTION_URL\>"

#### <a name="error-message"></a>Mensagem de erro

"O roteiro dado URI \<SCRIPT_URI\> está no ADLS, mas este cluster não tem nenhum diretor de armazenamento de dados de lagos"

### <a name="cause"></a>Causa

O serviço HDInsight não pode aceder ao URL de ação de script que forneceu como parte do pedido create cluster. O serviço recebe a mensagem de erro anterior quando tenta aceder à ação do script.

### <a name="resolution"></a>Resolução

Adicione a conta de armazenamento de lagos de dados azure correspondente ao cluster. Adicione também o principal de serviço que acede à conta Data Lake Storage Gen 1 ao cluster.

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Código de erro: ImplementaçãoDocument 'CsmDocument_2_0' falhou na validação

### <a name="error"></a>Erro

"O tamanho do VM '\<CUSTOMER_SPECIFIED_VM_SIZE\>' fornecido no pedido é inválido ou não suportado para o papel '\<ROLE\>'. Valores válidos são: \<VALID_VM_SIZE_FOR_ROLE\>."

### <a name="cause"></a>Causa

O tamanho virtual da máquina que especificou não é permitido para o papel. Este erro pode ocorrer porque o valor do tamanho do VM não funciona como esperado ou não é adequado para o papel do computador.

### <a name="resolution"></a>Resolução

A mensagem de erro lista os valores válidos para o tamanho VM. Selecione um destes valores e tente novamente o pedido de Criar Cluster.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Código de erro: InvalidVirtualNetworkId  

### <a name="error"></a>Erro

"O VirtualNetworkId não é válido. VirtualNetworkId '\<USER_VIRTUALNETWORKID\>'*'

### <a name="cause"></a>Causa

O valor **VirtualNetworkId** que especificou durante a criação do cluster não está no formato correto.

### <a name="resolution"></a>Resolução

Certifique-se de que os valores **virtualNetworkId** e subnet estão no formato correto. Para obter o valor **VirtualNetworkId:**

1. Aceda ao portal do Azure.
1. Selecione a sua rede virtual.
1. Selecione o item do menu **Propriedades.** O valor da propriedade **ResourceID** é o valor **VirtualNetworkId.**

Aqui está um exemplo de uma identificação virtual da rede:

"/subscrições/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>Código de erro: CustomizationFailedErrorCode

### <a name="error"></a>Erro

"A implementação do cluster falhou devido a um erro na ação do script personalizado. Ações Falhadas: \<SCRIPT_NAME\>, Por favor, vá à Ambari UI para desinbugimar ainda mais o fracasso."

### <a name="cause"></a>Causa

O script personalizado que forneceu durante o pedido create cluster é executado após o cluster ser implementado com sucesso. Este código de erro indica que surgiu um erro durante a execução do script personalizado chamado \<SCRIPT_NAME\>.

### <a name="resolution"></a>Resolução

Como o script é o seu script personalizado, recomendamos que se desloque o problema e refaça o script se necessário. Para resolver a falha do script, examine os registos na pasta /var/lib/ambari-agent/* Ou abra a página **operações** na UI Ambari e, em seguida, selecione a operação **run_customscriptaction** para visualizar os detalhes do erro.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Código de erro: InvalidDocumentErrorCode

### <a name="error"></a>Erro

"A \<\> METASTORE_MAJOR_VERSION versão\>de  META_STORE_TYPE META_STORE_TYPE\> Metastore \<na base de dados \<DATABASE_NAME\> é incompatível com a versão de cluster \<CLUSTER_VERSION "

### <a name="cause"></a>Causa

A metaloja personalizada é incompatível com a versão de cluster HDInsight selecionada. Atualmente, os clusters HDInsight 4.0 suportam apenas a versão Metastore 3.0 e posteriormente, enquanto os clusters HDInsight 3.6 não suportam a versão 3.0 e posterior da Metastore.

### <a name="resolution"></a>Resolução

Utilize apenas versões Metastore que a sua versão de cluster HDInsight suporta. Se não especificar uma metaloja personalizada, o HDInsight cria internamente uma metaloja e, em seguida, elimina-a após a eliminação do cluster.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Código de erro: FailedToConnectWithClusterErrorCode 

### <a name="error"></a>Erro

"Incapaz de ligar ao ponto final de gestão de clusters para realizar operação de escala. Verifique se as regras de segurança da rede não estão a bloquear o acesso externo ao cluster e que a UI do gestor de cluster (Ambari) pode ser acedida com sucesso."

### <a name="cause"></a>Causa

Uma regra de firewall no seu grupo de segurança de rede (NSG) está bloqueando a comunicação de cluster com serviços críticos de saúde e gestão azure.

### <a name="resolution"></a>Resolução

Se planeia utilizar grupos de segurança de rede para controlar o tráfego da rede, tome as seguintes ações antes de instalar o HDInsight:

- Identifique a região azure que pretende utilizar para o HDInsight.
- Identifique os endereços IP exigidos pelo HDInsight. Para obter mais informações, veja [Endereços IP de gestão do HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
  - Crie ou modifique os grupos de segurança da rede para a subnet a que planeia instalar o HDInsight.
  - Para os grupos de segurança da rede, permita o tráfego de entrada no porto 443 a partir dos endereços IP. Esta configuração garante que os serviços de gestão HDInsight podem chegar ao cluster de fora da rede virtual.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Código de erro: StoragePermissionsBlockedForMsi  

### <a name="error"></a>Erro

"A Identidade Gerida não tem permissões na conta de armazenamento. Verifique se a função 'Storage Blob Data Owner' está atribuída à Identidade Gerida para a conta de armazenamento. Armazenamento: /subscrições/ \<ID de subscrição\> /recursosGroups/\< Nome de grupo de recursos\> /fornecedores/Microsoft.Storage/storageAccounts/ \<Storage Account Name\>, Identidade Gerida: /subscrições/ \<Id de assinatura\> /recursosGroups/ / /\< Nome de grupo de recursos\> /fornecedores/Microsoft.ManagedIdentity/userAssignedIdentities/ \<User Managed Identity Name\>"

### <a name="cause"></a>Causa

Não forneceu as permissões necessárias para gerir a identidade. A identidade gerida atribuída ao utilizador não tem a função de Contribuinte de Armazenamento Blob na conta de armazenamento do Lago De dados Azure Gen2.

### <a name="resolution"></a>Resolução

1. Abra o portal do Azure.
1. Vá para a sua conta de armazenamento.
1. Procure sob controlo de **acesso (IAM)** .
1. Certifique-se de que o utilizador tem a função de Colaborador de Dados blob de armazenamento ou a função de Proprietário de Dados blob de armazenamento que lhes é atribuída.

Para mais informações, consulte [Configurar permissões para a identidade gerida na conta Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Código de erro: InvalidNetworkSecurityGroupSecurityRules  

### <a name="error"></a>Erro

"As regras de segurança do Grupo de Segurança da Rede /subscrições/\<SubscriçãoID\>/recursosGroups/<Nome do Grupo de Recursos\> padrão/fornecedores/Microsoft.Network/networkSecurityGroups/\<Network Security Group Name\> configurado com subnet /subscrições/\<SubscriçãoID\>/resourceGroups/\<Resource Group\> RG-westeurope-vnet-tomtom-default/providers/Microsoft.Network/virtualNetworks/\<Virtual Nome da rede\>/subnets/\<nome da sub-rede\> não permite a conectividade de entrada e/ou saída necessária. Para mais informações, visite [Plan a plana uma rede virtual para O Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment), ou suporte de contato."

### <a name="cause"></a>Causa

Se os grupos de segurança da rede ou as rotas definidas pelo utilizador (UDRs) controlarem o tráfego de entrada para o seu cluster HDInsight, certifique-se de que o seu cluster pode comunicar com serviços críticos de saúde e gestão azure.

### <a name="resolution"></a>Resolução

Se planeia utilizar grupos de segurança de rede para controlar o tráfego da rede, tome as seguintes ações antes de instalar o HDInsight:

- Identifique a região azure que pretende utilizar para o HDInsight e crie uma lista segura dos endereços IP para a sua região. Para mais informações, consulte serviços de [saúde e gestão: Regiões específicas.](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions)
- Identifique os endereços IP que o HDInsight necessita. Para mais informações, consulte os endereços IP de [gestão hDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
- Crie ou modifique os grupos de segurança da rede para a subnet a que planeia instalar o HDInsight. Para os grupos de segurança da rede, permita o tráfego de entrada no porto 443 a partir dos endereços IP. Esta configuração garante que os serviços de gestão HDInsight podem chegar ao cluster de fora da rede virtual.
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Código de erro: A configuração do cluster não instalou componentes num ou mais hospedeiros

###  <a name="error"></a>Erro

"A configuração do cluster não instalou componentes num ou mais hospedeiros. Por favor, tente novamente o seu pedido.

### <a name="cause"></a>Causa 

Tipicamente, este erro é gerado quando há um problema transitório ou uma paragem azure.

### <a name="resolution"></a>Resolução

Verifique a página de estado do [Azure](https://status.azure.com) para obter quaisquer interrupções do Azure que possam afetar a implementação do cluster. Se não houver interrupções, retente a implantação do cluster.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre erros de resolução de problemas na criação de clusters, consulte falhas de criação de [cluster saturadas com o Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails).
