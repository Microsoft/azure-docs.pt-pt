---
title: Azure HDInsight Criar um cluster - dicionário de erro
description: Saiba como resolver erros que ocorrem ao criar clusters Azure HDInsight
author: karkrish
ms.author: v-todmc
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 08/24/2020
ms.openlocfilehash: cae8647d970020a22d59dc49b058d43fe28dd00c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88816461"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight: Erros de criação de cluster

Este artigo descreve resoluções a erros que poderá encontrar ao criar clusters.

> [!NOTE]
> Os três primeiros erros descritos neste artigo são erros de validação. Podem ocorrer quando um produto Azure HDInsight utiliza a classe **CsmDocument_2_0.**

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Código de erro: O 'CsmDocument_2_0' de implementação falhou na validação

**Erro**: "O local de ação do script não pode ser acedido uri: \<SCRIPT ACTION URL\> "

### <a name="error-message-1"></a>Mensagem de erro 1

"O servidor remoto devolveu um erro: (404) Não Encontrado."

#### <a name="cause"></a>Causa

O serviço HDInsight não pode aceder ao URL de ação do script que forneceu como parte do pedido do Create Cluster. O serviço recebe a mensagem de erro anterior quando tenta aceder à ação do script.

#### <a name="resolution"></a>Resolução

- Para um URL HTTP ou HTTPS, verifique o URL tentando ir até ele a partir de uma janela do navegador incógnita.
- Para um URL WASB, certifique-se de que o script existe na conta de armazenamento que você dá no pedido. Certifique-se também de que a chave de armazenamento desta conta de armazenamento está correta.
- Para um URL ADLS, certifique-se de que o script existe na conta de armazenamento.

---

### <a name="error-message-2"></a>Mensagem de erro 2

"O dado script URI \<SCRIPT_URI\> está em ADLS, mas este cluster não tem diretor de armazenamento de lago de dados"

#### <a name="cause"></a>Causa

O serviço HDInsight não pode aceder ao URL de ação do script que forneceu como parte do pedido do Create Cluster. O serviço recebe a mensagem de erro anterior quando tenta aceder à ação do script.

#### <a name="resolution"></a>Resolução

Adicione a correspondente conta Azure Data Lake Storage Gen 1 ao cluster. Adicione também o principal de serviço que acede à conta de Armazenamento de Data Lake Gen 1 ao cluster.

---

### <a name="error-message-3"></a>Mensagem de erro 3

"Tamanho \<CUSTOMER_SPECIFIED_VM_SIZE\> VM' fornecido no pedido é inválido ou não apoiado para o papel \<ROLE\> '. Valores válidos são: \<VALID_VM_SIZE_FOR_ROLE\> ."

#### <a name="cause"></a>Causa

O tamanho da máquina virtual que especificou não é permitido para o papel. Este erro pode ocorrer porque o valor do tamanho VM não funciona como esperado ou não é adequado para o papel do computador.

#### <a name="resolution"></a>Resolução

A mensagem de erro lista os valores válidos para o tamanho VM. Selecione um destes valores e relemisça o pedido de Criar Cluster.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Código de erro: InvalidVirtualNetworkId  

### <a name="error"></a>Erro

"A VirtualNetworkId não é válida. VirtualNetworkId \<USER_VIRTUALNETWORKID\> ' '*'

### <a name="cause"></a>Causa

O valor **VirtualNetworkId** que especificou durante a criação do cluster não está no formato correto.

### <a name="resolution"></a>Resolução

Certifique-se de que os valores **virtualNetworkId** e sub-redes estão no formato correto. Para obter o valor **VirtualNetworkId:**

1. Aceda ao portal do Azure.
1. Selecione a sua rede virtual.
1. Selecione o item do menu **Propriedades.** O valor da propriedade **ResourceID** é o valor **VirtualNetworkId.**

Aqui está um exemplo de um ID de rede virtual:

"/subscrições/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>Código de erro: PersonalizaçãoFailedErrorCode

### <a name="error"></a>Erro

"A implementação do cluster falhou devido a um erro na ação personalizada do script. Ações falhadas: \<SCRIPT_NAME\> , por favor, vá à UI Ambari para depurar ainda mais o fracasso."

### <a name="cause"></a>Causa

O script personalizado que forneceu durante o pedido do Agrupamento de Criação é executado após o cluster ser implementado com sucesso. Este código de erro indica que surgiu um erro durante a execução do script personalizado denominado \<SCRIPT_NAME\> .

### <a name="resolution"></a>Resolução

Como o script é o seu script personalizado, recomendamos que resolva o problema e refaça o script se necessário. Para resolver problemas na falha do script, examine os registos na pasta /var/lib/ambari-agent/* . Ou abrir a página **Operações** na UI Ambari e, em seguida, selecionar a **operação run_customscriptaction** para ver os detalhes de erro.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Código de erro: InvalidDocumentErrorCode

### <a name="error"></a>Erro

"A \<META_STORE_TYPE\> versão do esquema de Metastore na base de \<METASTORE_MAJOR_VERSION\> dados é \<DATABASE_NAME\> incompatível com a versão do \<CLUSTER_VERSION\> cluster"

### <a name="cause"></a>Causa

A metástasia personalizada é incompatível com a versão de cluster HDInsight selecionada. Atualmente, os clusters HDInsight 4.0 suportam apenas a versão 3.0 da Metastore e posterior, enquanto os clusters HDInsight 3.6 não suportam a versão 3.0 da Metastore e posterior.

### <a name="resolution"></a>Resolução

Utilize apenas versões Metastore que a sua versão hdInsight cluster suporta. Se não especificar uma meta-loja personalizada, o HDInsight cria internamente uma meta-loja e, em seguida, elimina-a após a eliminação do cluster.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Código de erro: Falha noConnectWithClusterErrorCode 

### <a name="error"></a>Erro

"Incapaz de ligar ao ponto final de gestão do cluster para realizar uma operação de escala. Verifique se as regras de segurança da rede não estão a bloquear o acesso externo ao cluster e que o gestor de clusters (Ambari) UI pode ser acedido com sucesso."

### <a name="cause"></a>Causa

Uma regra de firewall no seu grupo de segurança de rede (NSG) está bloqueando a comunicação do cluster com serviços críticos de saúde e gestão de Azure.

### <a name="resolution"></a>Resolução

Se planeia utilizar grupos de segurança de rede para controlar o tráfego da rede, tome as seguintes ações antes de instalar o HDInsight:

- Identifique a região de Azure que pretende utilizar para o HDInsight.
- Identifique os endereços IP exigidos por HDInsight. Para obter mais informações, veja [Endereços IP de gestão do HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
  - Crie ou modifique os grupos de segurança da rede para a sub-rede em que planeia instalar o HDInsight.
  - Para os grupos de segurança da rede, permita o tráfego de entrada na porta 443 a partir dos endereços IP. Esta configuração garante que os serviços de gestão HDInsight podem chegar ao cluster de fora da rede virtual.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Código de erro: StoragePermissionsBlockedForMsi

### <a name="error"></a>Erro

"A Identidade Gerida não tem permissões na conta de armazenamento. Verifique se a função 'Proprietário de dados de armazenamento blob' está atribuída à Identidade Gerida para a conta de armazenamento. Armazenamento: /subscrições/ \<Subscription ID\> /recursosGroups/ \< Resource Group Name\> /fornecedores/Microsoft.Storage/storageAcounts/ \<Storage Account Name\> , Identidade Gerida: /subscrições/ \<Subscription ID\> /grupos de recursos/ \< Resource Group Name\> / /fornecedores/Microsoft.ManagedIdentity/userAssignedIdentities/ \<User Managed Identity Name\> "

### <a name="cause"></a>Causa

Não forneceu as permissões necessárias para gerir a identidade. A identidade gerida atribuída pelo utilizador não tem o papel de Contribuinte de Armazenamento blob na conta de armazenamento de Azure Data Lake Gen2.

### <a name="resolution"></a>Resolução

1. Abra o portal do Azure.
1. Vá para a sua conta de armazenamento.
1. Procure no **Controlo de Acesso (IAM)**.
1. Certifique-se de que o utilizador tem a função de Contribuinte de Dados blob de armazenamento ou a função de Proprietário de Dados blob de armazenamento que lhes foi atribuída.

Para obter mais informações, consulte [configurar permissões para a identidade gerida na conta da Data Lake Storage Gen2.](hdinsight-hadoop-use-data-lake-storage-gen2.md)

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Código de erro: InvalidNetworkSecurityGroupSecurityRules

### <a name="error"></a>Erro

"As regras de segurança no Grupo de Segurança da Rede /subscrições/ \<SubscriptionID\> /resourceGroups/<Nome do Grupo de Recursos \> predefinidos/fornecedores/provedores/Microsoft.Network/networkSecurityGroups/ \<Network Security Group Name\> configurados com sub-rede /subscrições/ \<SubscriptionID\> /resourceGroups/ \<Resource Group name\> RG-westeurope-vnet-tomtom-default/providers/Microsoft.Network/virtualNetworks/ \<Virtual Network Name\> /subnets/ \<Subnet Name\> não permite a conectividade de entrada e/ou saída requerida. Para mais informações, visite [o Plano de Uma rede virtual para Azure HDInsight,](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)ou suporte de contato."

### <a name="cause"></a>Causa

Se grupos de segurança de rede ou rotas definidas pelo utilizador (UDRs) controlarem o tráfego de entrada para o seu cluster HDInsight, certifique-se de que o seu cluster pode comunicar com serviços críticos de saúde e gestão da Azure.

### <a name="resolution"></a>Resolução

Se planeia utilizar grupos de segurança de rede para controlar o tráfego da rede, tome as seguintes ações antes de instalar o HDInsight:

- Identifique a região Azure que pretende utilizar para o HDInsight e crie uma lista segura dos endereços IP para a sua região. Para mais informações, consulte [serviços de saúde e gestão: Regiões específicas.](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions)
- Identifique os endereços IP que o HDInsight requer. Para obter mais informações, consulte [os endereços IP de gestão hdInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
- Crie ou modifique os grupos de segurança da rede para a sub-rede em que planeia instalar o HDInsight. Para os grupos de segurança da rede, permita o tráfego de entrada na porta 443 a partir dos endereços IP. Esta configuração garante que os serviços de gestão HDInsight podem chegar ao cluster de fora da rede virtual.

---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Código de erro: A configuração do cluster falhou na instalação de componentes num ou mais anfitriões

### <a name="error"></a>Erro

"A configuração do cluster não conseguiu instalar componentes num ou mais anfitriões. Por favor, re-tentar o seu pedido.

### <a name="cause"></a>Causa 

Normalmente, este erro é gerado quando há um problema transitório ou uma paragem do Azure.

### <a name="resolution"></a>Resolução

Verifique a página [de estado do Azure](https://status.azure.com) para obter quaisquer falhas de Azure que possam afetar a implementação do cluster. Se não houver interrupções, recandiduça a implantação do cluster.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Código de erro: Falha noConnectWithClusterErrorCode

### <a name="error"></a>Erro

Incapaz de ligar ao ponto final de gestão do cluster. Por favor, reda o novo tempo mais tarde.

### <a name="cause"></a>Causa

O Serviço HDInsight não pode ligar-se ao seu cluster quando tenta criar o cluster

### <a name="resolution"></a>Resolução

Se estiver a utilizar o grupo de segurança de rede VNet personalizado (NSGs) e as rotas definidas pelo utilizador (UDRs), certifique-se de que o seu cluster pode comunicar com os serviços de gestão hdInsight. Para obter informações adicionais consulte [endereços IP de gestão hdInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).

---

## <a name="error-code-deployments-failed-due-to-policy-violation-resource-resource-uri-was-disallowed-by-policy-policy-identifiers-policyassignmentnamepolicy-name-idprovidersmicrosoftmanagementmanagementgroupsmanagement-group-name-providersmicrosoftauthorizationpolicyassignmentspolicy-namepolicydefinition-policy-definition"></a>Código de erro: As implementações falharam devido à violação da política: 'Recurso' <Resource URI> foi proibido pela política. Identificadores de política: <Policy Name> "[{"policyAssignment":{"name":"""""""""id":"/providers/Microsoft.Management/managementGroups/providers/Microsoft.Authorization/policyAssignments/ <Management Group Name> <Policy Name> "}"policyDefinition": <Policy Definition>

### <a name="cause"></a>Causa

As políticas Azure baseadas em subscrições podem negar a criação de endereços IP públicos. A criação do cluster do HDInsight requer dois IPs públicos.

As seguintes políticas geralmente impactam a criação de clusters:

* Políticas que impedem a criação de endereços IP ou equilibradores de carga dentro da subscrição.
* Política que impede a criação de contas de armazenamento.
* Política que impede a eliminação de recursos de rede, tais como endereços IP ou equilibradores de carga.

### <a name="resolution"></a>Resolução

Elimine ou desative a atribuição da Política Azure baseada em subscrições enquanto cria o HdInsight Cluster.

---

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre erros de resolução de problemas na criação de [clusters, consulte falhas de criação de clusters de resolução de problemas com Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails).
