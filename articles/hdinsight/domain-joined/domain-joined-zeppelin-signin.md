---
title: Não é possível entrar no Zeppelin no Azure HDInsight
description: Não é possível entrar no Zeppelin no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/12/2019
ms.openlocfilehash: 14f4cd7588405dbf351124b1e469fd96f9b38274
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977407"
---
# <a name="scenario-unable-to-sign-in-to-apache-zeppelin-in-azure-hdinsight"></a>Cenário: Não é possível entrar no Apache Zeppelin no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Não é possível entrar no Apache Zeppelin depois de alterar a senha do ADDS no Active Directory.

## <a name="cause"></a>Causa

O usuário mencionado no `activeDirectoryRealm.systemUsername` `shiro_ini` do arquivo alterou a senha do Active Directory.

## <a name="resolution"></a>Resolução

1. Verifique se a senha alterada é a causa raiz, incluindo `activeDirectoryRealm.systemPassword = <new password>` a configuração Zeppelin `shiro_ini` em Ambari. Remova a `activeDirectoryRealm.hadoopSecurityCredentialPath` configuração. Abaixo está o local `shiro ini`do.

    ![Shiro](./media/domain-joined-zeppelin-signin/shiro.png)

1. Se os usuários agora podem entrar no Zeppelin após a etapa 1, crie um `jceks` novo arquivo com a nova senha e substitua `activeDirectoryRealm.hadoopSecurityCredentialPath` o pelo novo arquivo.

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
