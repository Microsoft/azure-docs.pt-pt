---
title: Identidades geridas em Azure HDInsight
description: Fornece uma visão geral da implementação de identidades geridas em Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: f2b7f6e8421a735db131bc05605936e8cb2d87eb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944132"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Identidades geridas em Azure HDInsight

Uma identidade gerida é uma identidade registada no Azure Ative Directory (Azure AD) cujas credenciais são geridas pela Azure. Com identidades geridas, não precisa de registar os principais serviços em Azure AD. Ou manter credenciais como certificados.

As identidades geridas são usadas no Azure HDInsight para aceder aos serviços de domínio Azure AD ou aceder a ficheiros em Azure Data Lake Storage Gen2 quando necessário.

Existem dois tipos de identidades geridas: atribuídas pelo utilizador e atribuídas pelo sistema. O Azure HDInsight suporta apenas identidades geridas atribuídas pelo utilizador. O HDInsight não suporta identidades geridas atribuídas pelo sistema. Uma identidade gerida atribuída ao utilizador é criada como um recurso autónomo Azure, que pode atribuir a uma ou mais instâncias de serviço Azure. Em contrapartida, uma identidade gerida atribuída pelo sistema é criada em Azure AD e, em seguida, ativada diretamente numa determinada instância de serviço Azure automaticamente. A vida daquela identidade gerida atribuída pelo sistema está então ligada à vida da instância de serviço em que está ativada.

## <a name="hdinsight-managed-identity-implementation"></a>Implementação de identidade gerida pela HDInsight

No Azure HDInsight, as identidades geridas só são utilizáveis pelo serviço HDInsight para componentes internos. Atualmente não existe um método suportado para gerar tokens de acesso usando as identidades geridas instaladas nos nós do cluster HDInsight para aceder a serviços externos. Para alguns serviços Azure, como VMs compute, identidades geridas são implementadas com um ponto final que você pode usar para adquirir fichas de acesso. Este ponto final não está atualmente disponível nos nós HDInsight.

Se precisar de arrancar as suas aplicações para evitar colocar segredos/palavras-passe nos trabalhos de análise (por exemplo, empregos SCALA), pode distribuir os seus próprios certificados pelos nós do cluster usando ações de script e, em seguida, usar esse certificado para adquirir um token de acesso (por exemplo, aceder ao Azure KeyVault).

## <a name="create-a-managed-identity"></a>Criar uma identidade gerida

As identidades geridas podem ser criadas com qualquer um dos seguintes métodos:

* [Portal do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [CLI do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

Os passos restantes para configurar a identidade gerida dependem do cenário onde será utilizado.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Cenários de identidade geridos em Azure HDInsight

Identidades geridas são usadas em Azure HDInsight em vários cenários. Consulte os documentos relacionados para obter instruções detalhadas de configuração e configuração:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2-portal.md#create-a-user-assigned-managed-identity) (Armazenamento do Azure Data Lake Gen2)
* [Pacote de Segurança Enterprise](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Encriptação de discos chave gerida pelo cliente](disk-encryption.md)

O HDInsight renovará automaticamente os certificados para as identidades geridas que utiliza para estes cenários. No entanto, existe uma limitação quando várias identidades geridas diferentes são usadas para clusters de longa duração, a renovação do certificado pode não funcionar como esperado para todas as identidades geridas. Devido a esta limitação, se estiver a planear utilizar clusters de longa duração (por exemplo, mais de 60 dias), recomendamos que utilize a mesma identidade gerida para todos os cenários acima referidos. 

Se já criou um cluster de longa duração com múltiplas identidades geridas diferentes e está a depará-lo com uma destas questões:
 * Nos clusters ESP, os serviços de cluster começam a falhar ou a escalar e outras operações começam a falhar com erros de autenticação.
 * Nos clusters ESP, ao alterar o certificado LDAPS LDAPS, o certificado LDAPS não é automaticamente atualizado e, portanto, os sync e scale ups LDAP começam a falhar.
 * O acesso da MSI à ADLS Gen2 começa a falhar.
 * As chaves de encriptação não podem ser rodadas no cenário CMK.

então deve atribuir as funções e permissões necessárias para os cenários acima referidos a todas as identidades geridas utilizadas no cluster. Por exemplo, se utilizou diferentes identidades geridas para clusters ADLS Gen2 e ESP, então ambos devem ter as funções "Storage blob data Owner" e "HDInsight Domain Services Contributor" que lhes são atribuídas para evitar correr para estas questões.

## <a name="faq"></a>FAQ

### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>O que acontece se eu apagar a identidade gerida após a criação do cluster?

O seu cluster vai encontrar problemas quando a identidade gerida for necessária. Não existe atualmente nenhuma forma de atualizar ou alterar uma identidade gerida após a criação do cluster. Portanto, a nossa recomendação é garantir que a identidade gerida não seja eliminada durante o tempo de funcionação do cluster. Ou pode recriar o cluster e atribuir uma nova identidade gerida.

## <a name="next-steps"></a>Próximos passos

* [O que são identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)
