---
title: Não é possível entrar no Zeppelin no Azure HDInsight
description: Não é possível entrar no Zeppelin no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/12/2019
ms.openlocfilehash: e9a81d458d1bab68bf94e9e9d0ebd87fac4580c8
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896143"
---
# <a name="scenario-unable-to-sign-in-to-apache-zeppelin-in-azure-hdinsight"></a>Cenário: não é possível entrar no Apache Zeppelin no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Não é possível entrar no Apache Zeppelin depois de alterar a senha do ADDS no Active Directory.

## <a name="cause"></a>Causa

O usuário mencionou na `activeDirectoryRealm.systemUsername` do arquivo de `shiro_ini` alterou a senha do Active Directory.

## <a name="resolution"></a>Resolução

1. Verifique se a senha alterada é a causa raiz, incluindo `activeDirectoryRealm.systemPassword = <new password>` na configuração de `shiro_ini` Zeppelin no Ambari. Remova a configuração `activeDirectoryRealm.hadoopSecurityCredentialPath`. Abaixo está o local de `shiro ini`.

    ![Shiro](./media/domain-joined-zeppelin-signin/shiro.png)

1. Se os usuários agora podem entrar no Zeppelin após a etapa 1, crie um novo arquivo de `jceks` com a nova senha e substitua o `activeDirectoryRealm.hadoopSecurityCredentialPath` pelo novo arquivo.

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
