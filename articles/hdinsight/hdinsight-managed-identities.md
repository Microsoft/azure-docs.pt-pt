---
title: Identidades geridas em Azure HDInsight
description: Fornece uma visão geral da implementação de identidades geridas em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 1081865a2e138af38ba171197719f08dedf6ffdb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81408945"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Identidades geridas em Azure HDInsight

Uma identidade gerida é uma identidade registada no Azure Ative Directory (Azure AD) cujas credenciais são geridas pela Azure. Com identidades geridas, não precisa de registar os principais serviços em Azure AD. Ou manter credenciais como certificados.

As identidades geridas são usadas no Azure HDInsight para aceder aos serviços de domínio Azure AD ou aceder a ficheiros em Azure Data Lake Storage Gen2 quando necessário.

Existem dois tipos de identidades geridas: atribuídas pelo utilizador e atribuídas pelo sistema. O Azure HDInsight suporta apenas identidades geridas atribuídas pelo utilizador. O HDInsight não suporta identidades geridas atribuídas pelo sistema. Uma identidade gerida atribuída ao utilizador é criada como um recurso autónomo Azure, que pode atribuir a uma ou mais instâncias de serviço Azure. Em contrapartida, uma identidade gerida atribuída pelo sistema é criada em Azure AD e, em seguida, ativada diretamente numa determinada instância de serviço Azure automaticamente. A vida daquela identidade gerida atribuída pelo sistema está então ligada à vida da instância de serviço em que está ativada.

## <a name="hdinsight-managed-identity-implementation"></a>Implementação de identidade gerida pela HDInsight

Em Azure HDInsight, as identidades geridas são a provisionadas em cada nó do cluster. Estes componentes de identidade, no entanto, só são utilizáveis pelo serviço HDInsight. Atualmente não existe um método suportado para gerar tokens de acesso usando as identidades geridas instaladas nos nós do cluster HDInsight. Para alguns serviços Azure, as identidades geridas são implementadas com um ponto final que você pode usar para adquirir fichas de acesso. Use os tokens para interagir com outros serviços Azure por conta própria.

## <a name="create-a-managed-identity"></a>Criar uma identidade gerida

As identidades geridas podem ser criadas com qualquer um dos seguintes métodos:

* [Portal do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [CLI do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

Os passos restantes para configurar a identidade gerida dependem do cenário onde será utilizado.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Cenários de identidade geridos em Azure HDInsight

Identidades geridas são usadas em Azure HDInsight em vários cenários. Consulte os documentos relacionados para obter instruções detalhadas de configuração e configuração:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity) (Armazenamento do Azure Data Lake Gen2)
* [Pacote de Segurança Enterprise](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Encriptação de discos chave gerida pelo cliente](disk-encryption.md)

## <a name="faq"></a>FAQ

### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>O que acontece se eu apagar a identidade gerida após a criação do cluster?

O seu cluster vai encontrar problemas quando a identidade gerida for necessária. Não existe atualmente nenhuma forma de atualizar ou alterar uma identidade gerida após a criação do cluster. Portanto, a nossa recomendação é garantir que a identidade gerida não seja eliminada durante o tempo de funcionação do cluster. Ou pode recriar o cluster e atribuir uma nova identidade gerida.

## <a name="next-steps"></a>Passos seguintes

* [O que são identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)
