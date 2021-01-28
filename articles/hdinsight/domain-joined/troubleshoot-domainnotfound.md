---
title: Criação de cluster falha com erro DomainNotFound em Azure HDInsight
description: Etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 54096e6d1741a7be9f50d4b4f7f0fcabbe880588
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943350"
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

## <a name="next-steps"></a>Próximos passos

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]