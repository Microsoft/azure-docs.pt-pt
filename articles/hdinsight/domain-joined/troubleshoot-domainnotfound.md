---
title: Criação de cluster falha com erro do DomainNotFound no Azure HDInsight
description: Passos de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: adcdafbc07fa0a8cc6970ab227b52aee798b084f
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776238"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>Cenário: Criação de cluster falha com erro do DomainNotFound no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

A criação de cluster HDI Secure (Enterprise Security Package) falha com `DomainNotFound` mensagem de erro.

## <a name="cause"></a>Causa

Definições dNS incorretas.

## <a name="resolution"></a>Resolução

Quando os clusters de domínio são implantados, o HDI cria um nome de utilizador interno e uma palavra-passe em AAD DS (para cada cluster) e une todos os nós de cluster a este domínio. A adesão ao domínio é realizada usando ferramentas de Samba. Verifique se os seguintes pré-requisitos foram atendidos:

* O nome de domínio deve ser resolvido através do DNS.
* O endereço IP dos controladores de domínio deve ser definido nas definições de DNS para a rede virtual onde o cluster está a ser implantado.
* Se a rede virtual for espartada com a rede virtual de DS AAD, então tem de ser feita manualmente.
* Se estiver a utilizar os avançados DNS, o nome de domínio deve ser corrigido corretamente dentro da rede virtual.
* As políticas de segurança (NSGs) não devem bloquear a adesão ao domínio.

### <a name="additional-debugging-steps"></a>Passos adicionais de depuração

* Implementar um VM do windows na mesma sub-rede, o domínio junte a máquina usando um nome de utilizador e uma palavra-passe (isto pode ser feito através do painel de controlo UI), ou

* Implante um Ubuntu VM na mesma subnet e o domínio junte-se à máquina
  * SSH na máquina
  * sudo su
  * Executar o script com nome de utilizador e senha
  * O script irá ping, criará os ficheiros de configuração necessários e, em seguida, domínio. Se for bem sucedido, as definições de DNS são boas.

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
