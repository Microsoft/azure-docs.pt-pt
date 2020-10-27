---
title: Aglomerados Azure HDInsight com encriptação de disco perdem acesso ao Cofre da Chave
description: Etapas de resolução de problemas e possíveis resoluções para problemas de acesso ao Cofre chave ao interagir com clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: bc8162f3a7ca8744a94aba039996275b5f13c727
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533415"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>Cenário: Clusters Azure HDInsight com encriptação de disco perdem acesso ao Cofre da Chave

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

O alerta do Centro de Saúde de Recursos (RHC), `The HDInsight cluster is unable to access the key for BYOK encryption at rest` é mostrado para os clusters Bring Your Own Key (BYOK) onde os nós de cluster perderam o acesso aos clientes Key Vault (KV). Alertas semelhantes também podem ser vistos na Apache Ambari UI.

## <a name="cause"></a>Causa

O alerta garante que o KV está acessível a partir dos nós do cluster, garantindo assim a ligação da rede, a saúde do KV e a política de acesso ao utilizador designado Identidade Gerida. Este alerta é apenas um aviso de encerramento de corretor iminente em reboots de nó posteriores, o cluster continua a funcionar até que os nós reiniciem.

Navegue até Apache Ambari UI para encontrar mais informações sobre o alerta a partir do estado do cofre da chave de **encriptação do disco** . Este alerta terá detalhes sobre o motivo da falha de verificação.

## <a name="resolution"></a>Resolução

### <a name="kvaad-outage"></a>Paragem KV/AAD

Veja a [disponibilidade e redundância do Azure Key Vault](../../key-vault/general/disaster-recovery-guidance.md) para mais detalhes https://status.azure.com/

### <a name="kv-accidental-deletion"></a>Eliminação acidental do KV

* Restaurar a chave apagada no KV para recuperar automaticamente. Para obter mais informações, consulte [a Chave Eliminada de Recuperar.](/rest/api/keyvault/recoverdeletedkey)
* Contacte a equipa KV para recuperar de supressões acidentais.

### <a name="kv-access-policy-changed"></a>A política de acesso ao KV foi alterada

Restaurar as políticas de acesso para o utilizador atribuído Identidade Gerida que é atribuída ao cluster HDI para aceder ao KV.

### <a name="key-permitted-operations"></a>Principais operações permitidas

Para cada tecla em KV, pode escolher o conjunto de operações permitidas. Certifique-se de que tem operações de embrulho e desembrulhação ativadas para a tecla BYOK

### <a name="expired-key"></a>Chave expirada

Se a expiração tiver passado e a tecla não estiver rodada, restaure a tecla do HSM de reserva ou contacte a equipa KV para limpar a data de validade.

### <a name="kv-firewall-blocking-access"></a>Firewall KV bloqueando o acesso

Fixe as definições de firewall KV para permitir que os nós de cluster BYOK acedam ao KV.

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>Regras NSG sobre o bloqueio de rede virtual ao acesso

Verifique as regras NSG associadas à rede virtual ligada ao cluster.

## <a name="mitigation-and-prevention-steps"></a>Medidas de mitigação e prevenção

### <a name="kv-accidental-deletion"></a>Eliminação acidental do KV

* Configure o cofre da chave com [o conjunto de bloqueio de recursos](../../azure-resource-manager/management/lock-resources.md).
* Volte a fazer as chaves do módulo de segurança de hardware.

### <a name="key-deletion"></a>Eliminação de chaves

O cluster deve ser eliminado antes da eliminação das chaves.

### <a name="kv-access-policy-changed"></a>A política de acesso ao KV foi alterada

Regularmente auditar e testar políticas de acesso.

### <a name="expired-key"></a>Chave expirada

* Volte a usar as chaves do seu HSM.
* Utilize uma chave sem qualquer conjunto de validade.
* Se tiver de ser definida a validade, rode as teclas antes da data de validade.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).