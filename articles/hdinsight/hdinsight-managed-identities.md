---
title: Identidades geridas no Azure HDInsight
description: Fornece uma visão geral da implementação de identidades geridas no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: 236f79c9060a0d6fdcb0f558373d02f32eba7abb
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905600"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Identidades geridas no Azure HDInsight

Uma identidade gerenciada é uma identidade registrada no Azure Active Directory (AD do Azure) cujas credenciais são gerenciadas pelo Azure. Com identidades geridas, não precisa de registar os principais de serviço sintetários em Azure AD, ou manter credenciais como certificados.

As identidades geridas são usadas no Azure HDInsight para aceder aos serviços de domínio azure AD ou ficheiros de acesso em Azure Data Lake Storage Gen2, quando necessário.

Existem dois tipos de identidades geridas: atribuídas ao utilizador e atribuídas ao sistema. O Azure HDInsight utiliza identidades geridas atribuídas ao utilizador. Uma identidade gerida atribuída ao utilizador é criada como um recurso Azure autónomo, que pode então atribuir a uma ou mais instâncias de serviço Azure. Em contraste, uma identidade gerida atribuída pelo sistema é criada em Azure AD e, em seguida, ativada diretamente em uma determinada instância de serviço Azure automaticamente. A vida daquela identidade gerida atribuída pelo sistema está então ligada à vida da instância de serviço em que está ativada.

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight geriu implementação de identidade

No Azure HDInsight, as identidades geridas são aprovisionadas em cada nó do cluster. Estes componentes de identidade, no entanto, são apenas utilizáveis pelo serviço HDInsight. Atualmente não existe um método suportado para que gere fichas de acesso utilizando as identidades geridas instaladas nos nós do cluster HDInsight. Para alguns serviços Azure, as identidades geridas são implementadas com um ponto final que você pode usar para adquirir fichas de acesso para interagir com outros serviços Azure por conta própria.

## <a name="create-a-managed-identity"></a>Criar uma identidade gerida

As identidades geridas podem ser criadas com qualquer um dos seguintes métodos:

* [Portal do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [O Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [CLI do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

Os passos restantes para configurar a identidade gerida dependem do cenário onde será utilizado.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Cenários de identidade geridos no Azure HDInsight

As identidades geridas são usadas no Azure HDInsight em vários cenários. Consulte os documentos relacionados para obter instruções detalhadas de configuração e configuração:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity) (Armazenamento do Azure Data Lake Gen2)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka Traga a sua própria chave (BYOK)](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="faq"></a>FAQ
### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>O que acontece se eu apagar a identidade gerida após a criação do cluster?
O seu aglomerado terá problemas quando a identidade gerida for necessária. Não existe atualmente forma de atualizar ou alterar a idenidade de gestão após a criação do cluster. Por conseguinte, a nossa recomendação é que a identidade gerida não seja eliminada durante o período de execução do cluster. Em alternativa, pode recriar o cluster e atribuir uma nova identidade gerida.

## <a name="next-steps"></a>Passos seguintes

* [O que são identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)
