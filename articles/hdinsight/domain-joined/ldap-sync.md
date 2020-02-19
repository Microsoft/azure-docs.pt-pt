---
title: Sincronização LDAP em Ranger e Apache Ambari no Azure HDInsight
description: Dirija-se à sincronização LDAP em Ranger e Ambari e forneça orientações gerais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463222"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Sincronização LDAP em Ranger e Apache Ambari no Azure HDInsight

Os clusters hDInsight Enterprise Security Package (ESP) utilizam o Ranger como autorização. Apache Ambari e Ranger sincronizam utilizadores e grupos de forma independente e trabalham de forma um pouco diferente. Este artigo destina-se a abordar a sincronização lDAP em Ranger e Ambari.

## <a name="general-guidelines"></a>Orientações gerais

* Sempre implante agrupamentos com grupos.
* Em vez de mudar filtros de grupo em Ambari e Ranger, tente gerir tudo isto em Azure AD e use grupos aninhados para trazer os utilizadores necessários.
* Uma vez sincronizado um utilizador, não é removido mesmo que o utilizador não faça parte dos grupos.
* Se precisar de alterar diretamente os filtros LDAP, utilize primeiro o UI, uma vez que contém algumas validações.

## <a name="users-are-synced-separately"></a>Os utilizadores são sincronizados separadamente

Ambari e Ranger não partilham a base de dados do utilizador porque servem dois propósitos diferentes. Se um utilizador precisa de utilizar o Ambari UI, então o utilizador precisa de ser sincronizado com ambari. Se o utilizador não estiver sincronizado com Ambari, a Ambari UI/API rejeitará-a, mas outras partes do sistema funcionarão (estas são guardadas por Ranger ou Gestor de Recursos e não por Ambari). Se quiser incluir o utilizador numa política ranger, então sincronize o utilizador com o Ranger.

Quando um cluster seguro é implantado, os membros do grupo são sincronizados transitivamente (todos os subgrupos e seus membros) tanto ambari como ranger. 

## <a name="ambari-user-sync-and-configuration"></a>Sincronização e configuração do utilizador ambari

A partir dos nós da cabeça, um trabalho de sarjeta, `/opt/startup_scripts/start_ambari_ldap_sync.py`, é executado de hora a hora para agendar a sincronização do utilizador. O trabalho de compadrio chama os Ambari de descanso APIs para executar a sincronização. O script apresenta uma lista de utilizadores e grupos para sincronizar (uma vez que os utilizadores podem não pertencer aos grupos especificados, ambos são especificados individualmente). Ambari sincroniza o nome sAMAccount como nome de utilizador e todos os membros do grupo, transitivamente.

Os registos devem estar em `/var/log/ambari-server/ambari-server.log`. Para mais informações, consulte configurar o nível de registo de [Ambari](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html).

Nos clusters data lake, o gancho de criação de utilizadores post é usado para criar as pastas caseiras para os utilizadores sincronizados e são definidos como os proprietários das pastas domésticas. Se o utilizador não estiver sincronizado corretamente com ambari, então o utilizador poderá enfrentar falhas no acesso à encenação e outras pastas temporárias.

### <a name="update-groups-to-be-synced-to-ambari"></a>Atualizar grupos a sincronizar com Ambari

Se não consegue gerir membros de grupos em Azure AD, tem duas opções:

* Execute uma sincronização de uma vez, tal como descrito de forma mais completa na [Synchronize LDAP Users and Groups](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html). Sempre que a associação de grupo mudar, terá de fazer esta sincronização novamente.

* Escreva um trabalho de amigos, ligue para a [API Ambari periodicamente](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634) com os novos grupos.

## <a name="ranger-user-sync-and-configuration"></a>Sincronização e configuração do Utilizador Ranger

Ranger tem um motor de sincronização incorporado que funciona a cada hora para sincronizar os utilizadores. Não partilha a base de dados dos utilizadores com a Ambari. O HDInsight confunde o filtro de pesquisa para sincronizar o utilizador administrativo, o utilizador do cão de guarda e os membros do grupo especificados durante a criação do cluster. Os membros do grupo serão sincronizados transitivamente:

* Desativar a sincronização incremental.
* Ativar o mapa de sincronização do grupo user.
* Especifique o filtro de pesquisa para incluir os membros do grupo transitório.
* Sync sAMAccountName para utilizadores e atributo de nome para grupos.

### <a name="group-or-incremental-sync"></a>Sincronização de grupo ou incremental

O Ranger suporta uma opção de sincronização de grupo, mas funciona como uma intersecção com o filtro do utilizador. Não uma união entre membros do grupo e filtro de utilizador. Um caso de utilização típico para filtro de sincronização de grupo em Ranger é - filtro de grupo: (dn=clusteradmingroup), filtro de utilizador: (cidade=seattle).

A sincronização incremental funciona apenas para os utilizadores que já estão sincronizados (a primeira vez). Incremental não sincronizar ás novos utilizadores adicionados aos grupos após a sincronização inicial.

### <a name="update-ranger-sync-filter"></a>Atualizar filtro de sincronização Ranger

O filtro LDAP pode ser encontrado no Ambari UI, sob a secção de configuração de sincronização de utilizador ranger. O filtro existente estará na forma `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))`. Certifique-se de que adiciona predicado no final e testa o filtro utilizando `net ads` comando de pesquisa ou ldp.exe ou algo semelhante.

## <a name="ranger-user-sync-logs"></a>Registos de sincronização do utilizador ranger

A sincronização do utilizador ranger pode acontecer a partir de qualquer um dos cabeçados. Os registos estão em `/var/log/ranger/usersync/usersync.log`. Para aumentar a verbosidade dos troncos, faça os seguintes passos:

1. Faça login em Ambari.
1. Vá para a secção de configuração ranger.
1. Aceda à secção Advanced **usersync-log4j.**
1. Mude a `log4j.rootLogger` para `DEBUG` nível (depois de mudar deve parecer `log4j.rootLogger = DEBUG,logFile,FilterLog`).
1. Guarde a configuração e reinicie o ranger.

## <a name="next-steps"></a>Passos seguintes

* [Problemas de autenticação no Azure HDInsight](./domain-joined-authentication-issues.md)
* [Synchronize utilizadores de AD Azure para um cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
