---
title: Clusters Azure HDInsight com encriptação de disco perdem acesso ao Cofre chave
description: Passos de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: b1d941fbf86d453a56a5157ed988a32173c614fc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81461536"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>Cenário: Clusters Azure HDInsight com encriptação de disco perdem acesso ao Cofre chave

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

O alerta do Centro de `The HDInsight cluster is unable to access the key for BYOK encryption at rest`Saúde de Recursos (RHC), é mostrado para os clusters Bring Your Own Key (BYOK) onde os nós do cluster perderam o acesso aos clientes Key Vault (KV). Alertas semelhantes também podem ser vistos na Apache Ambari UI.

## <a name="cause"></a>Causa

O alerta garante que o KV está acessível a partir dos nós do cluster, garantindo assim a ligação de rede, a saúde KV e a política de acesso ao utilizador designado Identidade Gerida. Este alerta é apenas um aviso de paragem do corretor iminente nas reinicializações subsequentes do nó, o cluster continua a funcionar até que os nós reiniciem.

Navegue até à Apache Ambari UI para encontrar mais informações sobre o alerta do Estado do Cofre da Chave de **Encriptação**do Disco . Este alerta terá detalhes sobre o motivo da falha de verificação.

## <a name="resolution"></a>Resolução

### <a name="kvaad-outage"></a>Interrupção de KV/AAD

Veja a [disponibilidade do Cofre chave azure e a página](../../key-vault/general/disaster-recovery-guidance.md) de estado de Azure para mais detalheshttps://status.azure.com/

### <a name="kv-accidental-deletion"></a>Eliminação acidental de KV

* Restaurar a chave eliminada no KV para recuperar automaticamente. Para mais informações, consulte [Recuperar A Chave Apagada](https://docs.microsoft.com/rest/api/keyvault/recoverdeletedkey).
* Contacte a equipa kV para se recuperar de supressões acidentais.

### <a name="kv-access-policy-changed"></a>Política de acesso kV alterada

Restaurar as políticas de acesso ao utilizador designado Identidade Gerida que é atribuída ao cluster HDI para aceder ao KV.

### <a name="key-permitted-operations"></a>Operações-chave permitidas

Para cada tecla em KV, pode escolher o conjunto de operações permitidas. Certifique-se de que tem operações de embrulho e desembrulhar ativadas para a tecla BYOK

### <a name="expired-key"></a>Chave expirada

Se a caducidade tiver passado e a chave não estiver rodada, restaure a chave do HSM de reserva ou contacte a equipa KV para limpar a data de validade.

### <a name="kv-firewall-blocking-access"></a>Acesso de bloqueio de firewall KV

Fixe as definições de firewall KV para permitir que os nós do cluster BYOK acedam ao KV.

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>Regras do NSG sobre o bloqueio de rede virtual ao acesso

Verifique as regras da NSG associadas à rede virtual anexada ao cluster.

## <a name="mitigation-and-prevention-steps"></a>Medidas de mitigação e prevenção

### <a name="kv-accidental-deletion"></a>Eliminação acidental de KV

* Configurar o cofre de chaves com [o conjunto](../../azure-resource-manager/management/lock-resources.md)de bloqueio de recursos .
* As chaves de segurança do hardware.

### <a name="key-deletion"></a>Eliminação da chave

O cluster deve ser eliminado antes da eliminação da chave.

### <a name="kv-access-policy-changed"></a>Política de acesso kV alterada

Auditar e testar regularmente políticas de acesso.

### <a name="expired-key"></a>Chave expirada

* As chaves de apoio do seu HSM.
* Utilize uma tecla sem qualquer conjunto de validade.
* Se for necessário definir a expiração, rode as teclas antes da data de validade.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
