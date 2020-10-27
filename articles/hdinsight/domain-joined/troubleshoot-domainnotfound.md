---
title: Criação de cluster falha com erro DomainNotFound em Azure HDInsight
description: Etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 1b8d437b14e98e2b10df782e5587d06fa4ed3653
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540811"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>Cenário: Criação de cluster falha com erro DeTeNotFound em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

A criação de cluster HDI Secure (Pacote de Segurança Empresarial) falha com `DomainNotFound` mensagem de erro.

## <a name="cause"></a>Causa

Definições de DNS incorretas.

## <a name="resolution"></a>Resolução

Quando os clusters unidos ao domínio são implantados, o HDI cria um nome de utilizador interno e palavra-passe em AAD DS (para cada cluster) e une todos os nós de cluster a este domínio. A união de domínio é realizada usando ferramentas de Samba. Assegurar que os seguintes pré-requisitos sejam cumpridos:

* O nome de domínio deve ser resolvido através do DNS.
* O endereço IP dos controladores de domínio deve ser definido nas definições de DNS para a rede virtual onde o cluster está a ser implantado.
* Se a rede virtual é espremiada com a rede virtual de AAD DS, então tem de ser feita manualmente.
* Se estiver a utilizar os reencaminhadores DNS, o nome de domínio deve resolver-se corretamente dentro da rede virtual.
* As políticas de segurança (NSGs) não devem bloquear a junção do domínio.

### <a name="additional-debugging-steps"></a>Etapas adicionais de depuragem

* Implementar um VM do windows na mesma sub-rede, o domínio junta-se à máquina utilizando um nome de utilizador e uma palavra-passe (isto pode ser feito através do UI do painel de controlo), ou

* Implementar um VM ubuntu na mesma sub-rede e domínio junte-se à máquina
  * SSH na máquina
  * sudo su
  * Execute o script com nome de utilizador e senha
  * O script irá ping, criará os ficheiros de configuração necessários e, em seguida, o domínio. Se for bem sucedido, as definições de DNS são boas.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).