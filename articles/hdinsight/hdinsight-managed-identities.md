---
title: Identidades geridas no Azure HDInsight
description: Fornece uma descrição geral da implementação de identidades geridas no Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: hrasheed
ms.openlocfilehash: 9386bcb8e455bff5ceed1fccdf55874caf7b6507
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58175807"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Identidades geridas no Azure HDInsight

Uma identidade gerida é uma identidade registrada no Azure Active Directory (Azure AD) cujas credenciais são geridos pelo Azure. Com identidades geridas, não precisa de registar os principais de serviço no Azure AD ou manter as credenciais, tais como certificados.

Identidades geridas podem ser utilizadas no Azure HDInsight para permitir que os clusters aceder a serviços de domínio do Azure AD, aceder ao Azure Key Vault ou aceder a ficheiros na geração 2 de armazenamento do Azure Data Lake.

Existem dois tipos de identidades geridas: atribuído ao utilizador e sistema atribuídos. O Azure HDInsight utiliza identidades geridas atribuído ao utilizador. Uma identidade gerida atribuído ao utilizador é criada como um recurso do Azure que, em seguida, pode atribuir a uma ou mais instâncias de serviço do Azure de autónoma. Por outro lado, uma identidade gerida atribuído o sistema é criada no Azure AD e, em seguida, ativada diretamente numa instância de serviço do Azure específico automaticamente. O ciclo de vida dessa identidade gerida atribuído de sistema, em seguida, é vinculado ao ciclo de vida da instância de serviço que está ativada no.

## <a name="hdinsight-managed-identity-implementation"></a>Implementação de identidade gerida do HDInsight

No Azure HDInsight, identidades geridas são provisionadas em cada nó do cluster. No entanto, esses componentes de identidade, apenas serão utilizáveis pelo serviço do HDInsight. Atualmente, não existe nenhum método com suporte para gerar tokens de acesso através de identidades geridas instaladas em nós de cluster do HDInsight. Para alguns serviços do Azure, as identidades geridas são implementadas com um ponto final que pode utilizar para adquirir os tokens de acesso para interagir com outros serviços do Azure por conta própria.

## <a name="create-a-managed-identity"></a>Criar uma identidade gerida

Identidades geridas podem ser criadas com qualquer um dos seguintes métodos:

* [Portal do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [CLI do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

Os restantes passos para configurar a identidade gerida dependem do cenário em que será utilizado.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Cenários de identidade gerida no Azure HDInsight

Identidades geridas são utilizadas no Azure HDInsight em vários cenários. Veja os documentos relacionados para o programa de configuração detalhado e as instruções de configuração:

* [Geração 2 Lake armazenamento de dados do Azure](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-managed-identity)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka Bring Your Own Key (BYOK)](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="next-steps"></a>Passos Seguintes

* [O que são identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)