---
title: Usar o agente de ID (versão prévia) para o gerenciamento de credenciais-Azure HDInsight
description: Saiba mais sobre o agente de ID do HDInsight para simplificar a autenticação para clusters de Apache Hadoop ingressados no domínio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: f14cbef2ab568962601b3a407fa979e8f982598d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483014"
---
# <a name="use-id-broker-preview-for-credential-management"></a>Usar o agente de ID (versão prévia) para o gerenciamento de credenciais

Este artigo descreve como configurar e usar o recurso de agente de ID no Azure HDInsight. Você pode usar esse recurso para entrar no Apache Ambari por meio da autenticação multifator do Azure e obter os tíquetes Kerberos necessários sem precisar de hashes de senha no Azure Active Directory Domain Services (AD DS do Azure).

## <a name="overview"></a>Visão geral

O agente de ID simplifica as configurações de autenticação complexas nos seguintes cenários:

* Sua organização depende da Federação para autenticar usuários para acessar recursos de nuvem. Anteriormente, para usar clusters do Enterprise Security Package do HDInsight (ESP), era necessário habilitar a sincronização de hash de senha do seu ambiente local para Azure Active Directory. Esse requisito pode ser difícil ou indesejável para algumas organizações.

* Você está criando soluções que usam tecnologias que dependem de mecanismos de autenticação diferentes. Por exemplo, Apache Hadoop e Apache Ranger dependem do Kerberos, enquanto Azure Data Lake Storage se baseia no OAuth.

O agente de ID fornece uma infraestrutura de autenticação unificada e remove o requisito de sincronização de hashes de senha para o Azure AD DS. O agente de ID consiste em componentes em execução em uma VM do Windows Server (nó do agente de ID), juntamente com nós de gateway de cluster. 

O diagrama a seguir mostra o fluxo de autenticação para todos os usuários, incluindo usuários federados, depois que o agente de ID está habilitado:

![Fluxo de autenticação com o agente de ID](./media/identity-broker/identity-broker-architecture.png)

O agente de ID permite que você entre em clusters ESP usando a autenticação multifator, sem fornecer nenhuma senha. Se você já tiver entrado em outros serviços do Azure, como o portal do Azure, você pode entrar em seu cluster HDInsight com uma experiência de logon único (SSO).

## <a name="enable-hdinsight-id-broker"></a>Habilitar agente de ID do HDInsight

Para criar um cluster ESP com o agente de ID habilitado, execute as seguintes etapas:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Siga as etapas de criação básicas para um cluster ESP. Para obter mais informações, consulte [criar um cluster HDInsight com ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Selecione **habilitar agente de ID do HDInsight**.

O recurso do agente de ID adicionará uma VM extra ao cluster. Essa VM é o nó do agente de ID e inclui componentes de servidor para dar suporte à autenticação. O nó do agente de ID é ingressado no domínio do Azure AD DS domínio.

![Opção para habilitar o agente de ID](./media/identity-broker/identity-broker-enable.png)

## <a name="tool-integration"></a>Integração de ferramentas

O [plug-in HDInsight IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) é atualizado para dar suporte ao OAuth. Você pode usar esse plug-in para se conectar ao cluster e enviar trabalhos.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Acesso SSH sem um hash de senha no Azure AD DS

Depois que o agente de ID estiver habilitado, você ainda precisará de um hash de senha armazenado no Azure AD DS para cenários SSH com contas de domínio. Para realizar o SSH em uma VM ingressada no domínio ou para executar o comando `kinit`, você precisa fornecer uma senha. 

A autenticação SSH requer que o hash esteja disponível no Azure AD DS. Se você quiser usar o SSH somente para cenários administrativos, poderá criar uma conta somente em nuvem e usá-la para SSH para o cluster. Outros usuários ainda podem usar as ferramentas Ambari ou HDInsight (como o plug-in IntelliJ) sem ter o hash de senha disponível no Azure AD DS.

## <a name="next-steps"></a>Passos seguintes

* [Configurar um cluster HDInsight com Enterprise Security Package usando Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Sincronizar Azure Active Directory usuários com um cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Monitorizar o desempenho do cluster](../hdinsight-key-scenarios-to-monitor.md)
