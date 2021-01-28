---
title: Sincronização LDAP em Ranger e Apache Ambari em Azure HDInsight
description: Dirija-se à sincronização LDAP em Ranger e Ambari e forneça orientações gerais.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: fb9035b4d816c1af84b15e6865335aa1bdf86f5b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933420"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Sincronização LDAP em Ranger e Apache Ambari em Azure HDInsight

Os clusters HDInsight Enterprise Security Package (ESP) utilizam o Ranger para autorização. Apache Ambari e Ranger sincronizam os utilizadores e grupos de forma independente e funcionam de forma um pouco diferente. Este artigo destina-se a abordar a sincronização LDAP em Ranger e Ambari.

## <a name="general-guidelines"></a>Orientações gerais

* Coloque sempre os aglomerados com um ou mais grupos.
* Se pretender utilizar mais grupos no cluster, verifique se faz sentido atualizar os membros do grupo no Azure Ative Directory (Azure AD).
* Se quiser alterar os grupos de agrupamento, pode alterar os filtros de sincronização utilizando Ambari.
* Todas as mudanças de membros do grupo no AD Azure são refletidas no cluster em sincronizações subsequentes. As alterações devem ser sincronizadas com os Serviços de Domínio AD Azure (Azure AD DS) primeiro e depois com os clusters.
* Os clusters HDInsight usam Samba/Winbind para projetar os membros do grupo nos nós do cluster.
* Os membros do grupo são sincronizados transitivamente (todos os subgrupos e seus membros) tanto a Ambari como ranger. 

## <a name="users-are-synced-separately"></a>Os utilizadores são sincronizados separadamente

 * Ambari e Ranger não partilham a base de dados dos utilizadores porque servem dois propósitos diferentes. 
   * Se um utilizador precisar de utilizar a UI Ambari, o utilizador precisa de ser sincronizado com a Ambari. 
   * Se o utilizador não estiver sincronizado com Ambari, a UI/API do Ambari rejeitará-o, mas outras partes do sistema funcionarão (estas são guardadas por Ranger ou Resource Manager, e não por Ambari).
   * Para incluir utilizadores ou grupos nas políticas ranger, os principais precisam ser explicitamente sincronizados no Ranger.

## <a name="ambari-user-sync-and-configuration"></a>Sincronização e configuração do utilizador Ambari

A partir dos nós da cabeça, um cron `/opt/startup_scripts/start_ambari_ldap_sync.py` job, é executado a cada hora para agendar a sincronização do utilizador. O cron job chama as APIs de descanso Ambari para realizar a sincronização. O script apresenta uma lista de utilizadores e grupos para sincronizar (uma vez que os utilizadores podem não pertencer aos grupos especificados, ambos são especificados individualmente). Ambari sincroniza o nome de número de utilizador e todos os membros do grupo, transitivamente.

Os registos devem estar dentro `/var/log/ambari-server/ambari-server.log` . Para mais informações, consulte [o nível de registo Configure Ambari.](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html)

Nos clusters data lake, o gancho de criação pós-utilizador é usado para criar as pastas caseiras para os utilizadores sincronizados e são definidos como os proprietários das pastas domésticas. Se o utilizador não estiver corretamente sincronizado com a Ambari, então o utilizador poderá enfrentar falhas na execução de trabalhos, uma vez que a pasta doméstica pode não estar corretamente configurada.

## <a name="ranger-user-sync-and-configuration"></a>Sincronização e configuração do utilizador ranger

O Ranger tem um motor de sincronização incorporado que funciona a cada hora para sincronizar os utilizadores. Não partilha a base de dados dos utilizadores com o Ambari. O HDInsight configura o filtro de pesquisa para sincronizar o utilizador administrativo, o utilizador do cão de guarda e os membros do grupo especificados durante a criação do cluster. Os membros do grupo serão sincronizados transitivamente:

1. Desative a sincronização incremental.
1. Ativar o mapa de sincronização do grupo utilizador.
1. Especifique o filtro de pesquisa para incluir os membros do grupo transitório.
1. Sincronizar o atributo sAMAccountName para os utilizadores e o atributo de nome para grupos.

### <a name="group-or-incremental-sync"></a>Sincronização de grupo ou incremental

O Ranger suporta uma opção de sincronização de grupo, mas funciona como uma intersecção com o filtro do utilizador, não como uma união entre membros do grupo e filtro de utilizador. Uma caixa de utilização típica para filtro de sincronização de grupo em Ranger é - filtro de grupo: (dn=clusteradmingroup), filtro do utilizador: (city=seattle).

A sincronização incremental funciona apenas para os utilizadores que já estão sincronizados (a primeira vez). Incremental não sincronizará quaisquer novos utilizadores adicionados aos grupos após a sincronização inicial.

### <a name="update-ranger-sync-filter"></a>Atualizar filtro de sincronização ranger

O filtro LDAP pode ser encontrado na UI Ambari, sob a secção de configuração de sincronização do utilizador ranger. O filtro existente estará no `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))` formulário. Certifique-se de que adiciona predicado no final e teste o filtro utilizando `net ads` o comando de busca ou ldp.exe ou algo semelhante.

## <a name="ranger-user-sync-logs"></a>Registos de sincronização de utilizadores do Ranger

A sincronização do utilizador ranger pode acontecer fora de qualquer um dos headnodes. Os registos estão dentro `/var/log/ranger/usersync/usersync.log` . Para aumentar a verbosidade dos troncos, faça os seguintes passos:

1. Faça login em Ambari.
1. Vá à secção de configuração ranger.
1. Aceda à secção Advanced **usersync-log4j.**
1. Mude o `log4j.rootLogger` `DEBUG` nível. (Depois de mudá-lo, deve `log4j.rootLogger = DEBUG,logFile,FilterLog` parecer).
1. Guarde a configuração e reinicie o Ranger.

## <a name="known-issues-with-ranger-user-sync"></a>Problemas conhecidos com sincronização de utilizador ranger
* Se o nome do grupo tiver caracteres unicódigos, a sincronização do Ranger não sincroniza esse objeto. Se um utilizador pertence a um grupo que tem caracteres internacionais, ranger sincroniza a filiação parcial do grupo
* O nome de utilizador (sAMAccountName) e o nome de grupo (nome) têm de ter 20 caracteres de comprimento ou menos. Se o nome do grupo for mais longo, então o utilizador será tratado como se não pertencesse ao grupo, ao calcular as permissões.

## <a name="next-steps"></a>Próximos passos

* [Problemas de autenticação no Azure HDInsight](./domain-joined-authentication-issues.md)
* [Sincronizar utilizadores do Azure AD para um cluster do HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
