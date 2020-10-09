---
title: Sincronização LDAP em Ranger e Apache Ambari em Azure HDInsight
description: Dirija-se à sincronização LDAP em Ranger e Ambari e forneça orientações gerais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77463222"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Sincronização LDAP em Ranger e Apache Ambari em Azure HDInsight

Os clusters HDInsight Enterprise Security Package (ESP) utilizam o Ranger para autorização. Apache Ambari e Ranger sincronizam os utilizadores e grupos de forma independente e funcionam de forma um pouco diferente. Este artigo destina-se a abordar a sincronização LDAP em Ranger e Ambari.

## <a name="general-guidelines"></a>Orientações gerais

* Coloque sempre os aglomerados com grupos.
* Em vez de mudar os filtros de grupo em Ambari e Ranger, tente gerir tudo isto em Azure AD e use grupos aninhados para trazer os utilizadores necessários.
* Uma vez sincronizado um utilizador, este não é removido mesmo que o utilizador não faça parte dos grupos.
* Se precisar de alterar diretamente os filtros LDAP, utilize primeiro o UI, uma vez que contém algumas validações.

## <a name="users-are-synced-separately"></a>Os utilizadores são sincronizados separadamente

Ambari e Ranger não partilham a base de dados dos utilizadores porque servem dois propósitos diferentes. Se um utilizador precisar de utilizar a UI Ambari, então o utilizador precisa de ser sincronizado com a Ambari. Se o utilizador não estiver sincronizado com a Ambari, a UI/API da Ambari rejeitará-o, mas outras partes do sistema funcionarão (estas são guardadas pela Ranger ou Resource Manager e não pela Ambari). Se quiser incluir o utilizador numa política ranger, então sincronize o utilizador com o Ranger.

Quando um cluster seguro é implantado, os membros do grupo são sincronizados transitivamente (todos os subgrupos e seus membros) para Ambari e Ranger. 

## <a name="ambari-user-sync-and-configuration"></a>Sincronização e configuração do utilizador Ambari

A partir dos nós da cabeça, um cron `/opt/startup_scripts/start_ambari_ldap_sync.py` job, é executado a cada hora para agendar a sincronização do utilizador. O cron job chama as APIs de descanso Ambari para realizar a sincronização. O script apresenta uma lista de utilizadores e grupos para sincronizar (uma vez que os utilizadores podem não pertencer aos grupos especificados, ambos são especificados individualmente). Ambari sincroniza o nome de número de utilizador e todos os membros do grupo, transitivamente.

Os registos devem estar dentro `/var/log/ambari-server/ambari-server.log` . Para mais informações, consulte [o nível de registo Configure Ambari.](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html)

Nos clusters data lake, o gancho de criação pós-utilizador é usado para criar as pastas caseiras para os utilizadores sincronizados e são definidos como os proprietários das pastas domésticas. Se o utilizador não estiver corretamente sincronizado com a Ambari, então o utilizador poderá enfrentar falhas no acesso à encenação e outras pastas temporárias.

### <a name="update-groups-to-be-synced-to-ambari"></a>Atualizar grupos a sincronizar com Ambari

Se não consegue gerir membros de grupos em Azure AD, tem duas opções:

* Execute uma sincronização única, conforme descrito mais plenamente em [Synchronize LDAP Users and Groups](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html). Sempre que a adesão ao grupo mudar, terás de fazer esta sincronização novamente.

* Escreva um cron job, ligue periodicamente para a [AMBAri API](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634) com os novos grupos.

## <a name="ranger-user-sync-and-configuration"></a>Sincronização e configuração do Utilizador Ranger

O Ranger tem um motor de sincronização incorporado que funciona a cada hora para sincronizar os utilizadores. Não partilha a base de dados dos utilizadores com o Ambari. O HDInsight configura o filtro de pesquisa para sincronizar o utilizador administrativo, o utilizador do cão de guarda e os membros do grupo especificados durante a criação do cluster. Os membros do grupo serão sincronizados transitivamente:

* Desative a sincronização incremental.
* Ativar o mapa de sincronização do grupo do utilizador.
* Especifique o filtro de pesquisa para incluir os membros do grupo transitório.
* Sync sAMAccountName para utilizadores e atributo de nome para grupos.

### <a name="group-or-incremental-sync"></a>Sincronização de grupo ou incremental

O Ranger suporta uma opção de sincronização de grupo, mas funciona como uma intersecção com o filtro do utilizador. Não é uma união entre membros do grupo e filtro de utilizador. Uma caixa de utilização típica para filtro de sincronização de grupo em Ranger é - filtro de grupo: (dn=clusteradmingroup), filtro do utilizador: (city=seattle).

A sincronização incremental funciona apenas para os utilizadores que já estão sincronizados (a primeira vez). Incremental não sincronizará quaisquer novos utilizadores adicionados aos grupos após a sincronização inicial.

### <a name="update-ranger-sync-filter"></a>Atualizar filtro de sincronização ranger

O filtro LDAP pode ser encontrado na UI Ambari, sob a secção de configuração de sincronização do utilizador ranger. O filtro existente estará no `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))` formulário. Certifique-se de que adiciona predicado no final e teste o filtro utilizando `net ads` o comando de busca ou ldp.exe ou algo semelhante.

## <a name="ranger-user-sync-logs"></a>Registos de sincronização de utilizadores do Ranger

A sincronização do utilizador ranger pode acontecer fora de qualquer um dos headnodes. Os registos estão dentro `/var/log/ranger/usersync/usersync.log` . Para aumentar a verbosidade dos troncos, faça os seguintes passos:

1. Faça login em Ambari.
1. Vá à secção de configuração ranger.
1. Aceda à secção Advanced **usersync-log4j.**
1. Mude o `log4j.rootLogger` `DEBUG` nível para nível (Depois de mudar deve `log4j.rootLogger = DEBUG,logFile,FilterLog` parecer).
1. Guarde a configuração e reinicie o ranger.

## <a name="next-steps"></a>Passos seguintes

* [Problemas de autenticação no Azure HDInsight](./domain-joined-authentication-issues.md)
* [Sincronizar utilizadores do Azure AD para um cluster do HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
