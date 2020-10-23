---
title: Opções de segurança para Colmeia em Azure HDInsight
description: Opções de segurança para colmeias em clusters Standard e ESP.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/02/2020
ms.openlocfilehash: 14a41365640439ff99861bbb22cc04a40f35da5e
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222653"
---
# <a name="security-options-for-hive-in-azure-hdinsight"></a>Opções de segurança para Colmeia em Azure HDInsight

Este documento descreve as opções de segurança recomendadas para a Colmeia em HDInsight. Estas opções podem ser configuradas através de Ambari.

!['Opções de segurança para colmeia'](./media/hdinsight-security-options-for-hive/security-options-hive.png "Opções de segurança para colmeia")

## <a name="hiveserver2-authentication"></a>Autenticação HiveServer2

Para os agrupamentos padrão, a definição recomendada para a autenticação HiveServer2 é o padrão que não é nenhum. Para ativar a autenticação, recomendamos o upgrade para um cluster [ESP](https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview) (Enterprise Security Package). 

Para os clusters ESP, a autenticação [Kerberos](https://web.mit.edu/Kerberos/) é ativada por padrão. Os Módulos de Autenticação Pluggable (PAM) e os esquemas de autenticação personalizados não são suportados.

## <a name="hiveserver2-authorization"></a>Autorização HiveServer2

Para os clusters padrão, a definição padrão é Nenhuma. [SqlStdAuth (SQL Standards Based Authorization)](https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+based+hive+authorization) pode ser ativado. A autorização através do [Apache Ranger](https://ranger.apache.org/) não é suportada para aglomerados padrão. Recomendamos a atualização para um cluster ESP para autorização ranger. 

Para os clusters ESP, a autorização através do Ranger é ativada por defeito. 


## <a name="ssl-encryption-for-hiveserver2"></a>Encriptação SSL para HiveServer2

Permitir o Hiveserver2 SSL não é recomendado para aglomerados standard ou ESP. Em vez disso, o SSL está ativado no portal. [A encriptação em trânsito](https://docs.microsoft.com/azure/hdinsight/domain-joined/encryption-in-transit) pode ser ativada para encriptar comunicações entre os nós do cluster utilizando a [Internet Protocol Security (IPSec)](https://en.wikipedia.org/wiki/IPsec).


## <a name="next-steps"></a>Passos seguintes
* [Visão geral da Autenticação HiveServer2](https://cwiki.apache.org/confluence/display/Hive/Setting+up+HiveServer2#SettingUpHiveServer2-Authentication/SecurityConfiguration)
* [Visão geral da Autorização HiveServer2](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Authorization#:~:text=%20Overview%20of%20Authorization%20Modes%20%201%201,and%20Apache%20Sentry%20are%20apache%20projects...%20More%20)
* [Habilitar a autorização de colmeia baseada em padrões SQL](https://community.cloudera.com/t5/Community-Articles/Getting-started-with-SQLStdAuth/ta-p/244263)
* [Ranger Apache com Colmeia](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-run-hive#:~:text=Create%20Hive%20ODBC%20data%20source%20%20%20,Enter%20hiveuser1%40contoso158.onmicrosoft.c%20...%20%205%20more%20rows%20)
