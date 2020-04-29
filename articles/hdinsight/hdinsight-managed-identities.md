---
title: Identidades geridas no Azure HDInsight
description: Fornece uma visão geral da implementação de identidades geridas no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 1081865a2e138af38ba171197719f08dedf6ffdb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81408945"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Identidades geridas no Azure HDInsight

Uma identidade gerida é uma identidade registada no Azure Ative Directory (Azure AD) cujas credenciais são geridas pelo Azure. Com identidades geridas, não precisa de registar os diretores de serviço sintetizar em Azure AD. Ou manter credenciais como certificados.

As identidades geridas são usadas no Azure HDInsight para aceder aos serviços de domínio azure AD ou ficheiros de acesso em Azure Data Lake Storage Gen2, quando necessário.

Existem dois tipos de identidades geridas: atribuídas ao utilizador e atribuídas ao sistema. O Azure HDInsight suporta apenas identidades geridas atribuídas ao utilizador. O HDInsight não suporta identidades geridas atribuídas pelo sistema. Uma identidade gerida atribuída ao utilizador é criada como um recurso Azure autónomo, que pode então atribuir a uma ou mais instâncias de serviço Azure. Em contraste, uma identidade gerida atribuída pelo sistema é criada em Azure AD e, em seguida, ativada diretamente em uma determinada instância de serviço Azure automaticamente. A vida daquela identidade gerida atribuída pelo sistema está então ligada à vida da instância de serviço em que está ativada.

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight geriu implementação de identidade

No Azure HDInsight, as identidades geridas são aprovisionadas em cada nó do cluster. Estes componentes de identidade, no entanto, são apenas utilizáveis pelo serviço HDInsight. Atualmente não existe um método suportado para gerar fichas de acesso usando as identidades geridas instaladas nos nós do cluster HDInsight. Para alguns serviços Azure, as identidades geridas são implementadas com um ponto final que pode usar para adquirir fichas de acesso. Use os símbolos para interagir com outros serviços Azure por conta própria.

## <a name="create-a-managed-identity"></a>Criar uma identidade gerida

As identidades geridas podem ser criadas com qualquer um dos seguintes métodos:

* [Portal do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [CLI do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

Os passos restantes para configurar a identidade gerida dependem do cenário onde será utilizado.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Cenários de identidade geridos no Azure HDInsight

As identidades geridas são usadas no Azure HDInsight em vários cenários. Consulte os documentos relacionados para obter instruções detalhadas de configuração e configuração:

* [Armazenamento do Azure Data Lake Ger2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Pacote de Segurança Enterprise](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Encriptação de discos chave gerida pelo cliente](disk-encryption.md)

## <a name="faq"></a>FAQ

### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>O que acontece se eu apagar a identidade gerida após a criação do cluster?

O seu aglomerado terá problemas quando a identidade gerida for necessária. Não há atualmente forma de atualizar ou alterar uma identidade gerida após a criação do cluster. Portanto, a nossa recomendação é garantir que a identidade gerida não seja eliminada durante o tempo de execução do cluster. Ou pode recriar o cluster e atribuir uma nova identidade gerida.

## <a name="next-steps"></a>Passos seguintes

* [O que são identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)
