---
title: Recuperação de desastre regional para Azure Databricks
description: Este artigo descreve uma abordagem para fazer a recuperação de desastres no Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/13/2019
ms.openlocfilehash: 800b51c8f900d2ea99900ea147b33010452348f5
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639876"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Recuperação de desastre regional para clusters de Azure Databricks

Este artigo descreve uma arquitetura de recuperação de desastre útil para clusters Azure Databricks e as etapas para realizar esse design.

## <a name="azure-databricks-architecture"></a>Arquitetura de Azure Databricks

Em um alto nível, quando você cria um espaço de trabalho de Azure Databricks da portal do Azure, um [dispositivo gerenciado](../azure-resource-manager/managed-applications/overview.md) é implantado como um recurso do Azure em sua assinatura, na região do Azure escolhida (por exemplo, oeste dos EUA). Esse dispositivo é implantado em uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md) com um [grupo de segurança de rede](../virtual-network/manage-network-security-group.md) e uma conta de armazenamento do Azure, disponível em sua assinatura. A rede virtual fornece segurança de nível de perímetro para o espaço de trabalho do databricks e é protegida por meio do grupo de segurança de rede. No espaço de trabalho, você pode criar clusters do databricks fornecendo o tipo de VM de operador e de driver e a versão de tempo de execução do databricks. Os dados persistentes estão disponíveis em sua conta de armazenamento, que pode ser o armazenamento de BLOBs do Azure ou Azure Data Lake Storage. Depois que o cluster é criado, você pode executar trabalhos por meio de blocos de anotações, APIs REST, pontos de extremidade ODBC/JDBC anexando-os a um cluster específico.

O plano de controle do databricks gerencia e monitora o ambiente de espaço de trabalho do databricks. Qualquer operação de gerenciamento, como criar cluster, será iniciada no plano de controle. Todos os metadados, como os trabalhos agendados, são armazenados em um banco de dados do Azure com replicação geográfica para tolerância a falhas.

![Arquitetura do databricks](media/howto-regional-disaster-recovery/databricks-architecture.png)

Uma das vantagens dessa arquitetura é que os usuários podem se conectar Azure Databricks a qualquer recurso de armazenamento em sua conta. Um dos principais benefícios é que tanto a computação (Azure Databricks) quanto o armazenamento podem ser dimensionados independentemente um do outro.

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>Como criar uma topologia de recuperação de desastre regional

Como você observa na descrição da arquitetura anterior, há uma série de componentes usados para um pipeline de Big data com o Azure Databricks: armazenamento do Azure, banco de dados do Azure e outras fontes. Azure Databricks é a *computação* do pipeline de Big Data. Ele é *efêmero* por natureza, o que significa que, embora seus dados ainda estejam disponíveis no armazenamento do Azure, a *computação* (Azure Databricks cluster) pode ser encerrada para que você não precise pagar pela computação quando não precisar dela. As fontes de *computação* (Azure Databricks) e de armazenamento devem estar na mesma região para que os trabalhos não tenham alta latência.  

Para criar sua própria topologia de recuperação de desastre regional, siga estes requisitos:

   1. Provisione vários espaços de trabalho Azure Databricks em regiões separadas do Azure. Por exemplo, crie o espaço de trabalho primário Azure Databricks no leste dos EUA 2. Crie o espaço de trabalho de recuperação de desastres secundário Azure Databricks em uma região separada, como oeste dos EUA.

   2. Use o [armazenamento com redundância geográfica](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage). Os dados associados a Azure Databricks são armazenados por padrão no armazenamento do Azure. Os resultados de trabalhos do databricks também são armazenados no armazenamento de BLOBs do Azure, para que os dados processados sejam duráveis e permaneçam altamente disponíveis depois que o cluster for encerrado. Como o cluster de armazenamento e databricks estão colocalizados, você deve usar o armazenamento com redundância geográfica para que os dados possam ser acessados na região secundária se a região primária não estiver mais acessível.

   3. Depois que a região secundária é criada, você deve migrar os usuários, pastas de usuário, blocos de anotações, configuração de cluster, configuração de trabalhos, bibliotecas, armazenamento, scripts de inicialização e reconfigurar o controle de acesso. Detalhes adicionais são descritos na seção a seguir.

## <a name="detailed-migration-steps"></a>Passos de migração detalhados

1. **Configurar a interface de linha de comando do databricks em seu computador**

   Este artigo mostra vários exemplos de código que usam a interface de linha de comando para a maioria das etapas automatizadas, pois é um wrapper fácil para o usuário em Azure Databricks API REST.

   Antes de executar qualquer etapa de migração, instale o databricks-CLI no computador desktop ou em uma máquina virtual na qual você planeja fazer o trabalho. Para obter mais informações, consulte [instalar o databricks CLI](/azure/databricks/dev-tools/databricks-cli)

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > Todos os scripts Python fornecidos neste artigo devem funcionar com o Python 2.7 + < 3. x.

2. **Configure dois perfis.**

   Configure um para o espaço de trabalho primário e outro para o espaço de trabalho secundário:

   ```bash
   databricks configure --profile primary
   databricks configure --profile secondary
   ```

   Os blocos de código neste artigo alternam entre perfis em cada etapa subsequente usando o comando de espaço de trabalho correspondente. Certifique-se de que os nomes dos perfis criados sejam substituídos em cada bloco de código.

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   Você pode alternar manualmente na linha de comando, se necessário:

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **Migrar Azure Active Directory usuários**

   Adicione manualmente os mesmos Azure Active Directory usuários ao espaço de trabalho secundário que existem no espaço de trabalho primário.

4. **Migrar as pastas e os blocos de anotações do usuário**

   Use o código Python a seguir para migrar os ambientes de usuário em área restrita, que incluem a estrutura de pastas aninhada e os blocos de anotações por usuário.

   > [!NOTE]
   > As bibliotecas não são copiadas nesta etapa, pois a API subjacente não dá suporte a elas.

   Copie e salve o script Python a seguir em um arquivo e execute-o na linha de comando do databricks. Por exemplo, `python scriptname.py`.

   ```python
   from subprocess import call, check_output

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get a list of all users
   user_list_out = check_output(["databricks", "workspace", "ls", "/Users", "--profile", EXPORT_PROFILE])
   user_list = user_list_out.splitlines()

   # Export sandboxed environment (folders, notebooks) for each user and import into new workspace.
   # Libraries are not included with these APIs / commands.

   for user in user_list:
     print "Trying to migrate workspace for user " + user

     call("mkdir -p " + user, shell=True)
     export_exit_status = call("databricks workspace export_dir /Users/" + user + " ./" + user + " --profile " + EXPORT_PROFILE, shell=True)

     if export_exit_status==0:
       print "Export Success"
       import_exit_status = call("databricks workspace import_dir ./" + user + " /Users/" + user + " --profile " + IMPORT_PROFILE, shell=True)
       if import_exit_status==0:
         print "Import Success"
       else:
         print "Import Failure"
     else:
       print "Export Failure"

   print "All done"
   ```

5. **Migrar as configurações de cluster**

   Depois que os notebooks tiverem sido migrados, você poderá, opcionalmente, migrar as configurações de cluster para o novo espaço de trabalho. É quase uma etapa totalmente automatizada usando o databricks-CLI, a menos que você queira fazer a migração de configuração de cluster seletiva, em vez de todos.

   > [!NOTE]
   > Infelizmente, não há nenhum ponto de extremidade de configuração de cluster Create, e esse script tenta criar cada cluster imediatamente. Se não houver núcleos suficientes disponíveis em sua assinatura, a criação do cluster poderá falhar. A falha pode ser ignorada, desde que a configuração seja transferida com êxito.

   O script a seguir fornecido imprime um mapeamento de IDs de cluster antigo para o novo, que poderia ser usado para a migração de trabalho posteriormente (para trabalhos configurados para usar clusters existentes).

   Copie e salve o script Python a seguir em um arquivo e execute-o na linha de comando do databricks. Por exemplo, `python scriptname.py`.

   ```python
   from subprocess import call, check_output
   import json, os

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get all clusters info from old workspace
   clusters_out = check_output(["databricks", "clusters", "list", "--profile", EXPORT_PROFILE])
   clusters_info_list = clusters_out.splitlines()

   # Create a list of all cluster ids
   clusters_list = []
   ##for cluster_info in clusters_info_list: clusters_list.append(cluster_info.split(None, 1)[0])

   for cluster_info in clusters_info_list: 
      if cluster_info != '':
         clusters_list.append(cluster_info.split(None, 1)[0])

   # Optionally filter cluster ids out manually, so as to create only required ones in new workspace

   # Create a list of mandatory / optional create request elements
   cluster_req_elems = ["num_workers","autoscale","cluster_name","spark_version","spark_conf","node_type_id","driver_node_type_id","custom_tags","cluster_log_conf","spark_env_vars","autotermination_minutes","enable_elastic_disk"]

   print(str(len(clusters_list)) + " clusters found in the primary site" )

   print ("---------------------------------------------------------")
   # Try creating all / selected clusters in new workspace with same config as in old one.
   cluster_old_new_mappings = {}
   i = 0
   for cluster in clusters_list:
      i += 1
      print("Checking cluster " + str(i) + "/" + str(len(clusters_list)) + " : " + cluster)
      cluster_get_out = check_output(["databricks", "clusters", "get", "--cluster-id", cluster, "--profile", EXPORT_PROFILE])
      print ("Got cluster config from old workspace")

       # Remove extra content from the config, as we need to build create request with allowed elements only
      cluster_req_json = json.loads(cluster_get_out)
      cluster_json_keys = cluster_req_json.keys()

      #Don't migrate Job clusters
      if cluster_req_json['cluster_source'] == u'JOB' : 
         print ("Skipping this cluster as it is a Job cluster : " + cluster_req_json['cluster_id'] )
         print ("---------------------------------------------------------")
         continue

      for key in cluster_json_keys:
         if key not in cluster_req_elems:
            cluster_req_json.pop(key, None)

      # Create the cluster, and store the mapping from old to new cluster ids

      #Create a temp file to store the current cluster info as JSON
      strCurrentClusterFile = "tmp_cluster_info.json" 

      #delete the temp file if exists
      if os.path.exists(strCurrentClusterFile) : 
         os.remove(strCurrentClusterFile)

      fClusterJSONtmp = open(strCurrentClusterFile,"w+")
      fClusterJSONtmp.write(json.dumps(cluster_req_json))
      fClusterJSONtmp.close()

      #cluster_create_out = check_output(["databricks", "clusters", "create", "--json", json.dumps(cluster_req_json), "--profile", IMPORT_PROFILE])
      cluster_create_out = check_output(["databricks", "clusters", "create", "--json-file", strCurrentClusterFile , "--profile", IMPORT_PROFILE])
      cluster_create_out_json = json.loads(cluster_create_out)
      cluster_old_new_mappings[cluster] = cluster_create_out_json['cluster_id']

      print ("Cluster create request sent to secondary site workspace successfully")
      print ("---------------------------------------------------------")

      #delete the temp file if exists
      if os.path.exists(strCurrentClusterFile) : 
         os.remove(strCurrentClusterFile)

   print ("Cluster mappings: " + json.dumps(cluster_old_new_mappings))
   print ("All done")
   print ("P.S. : Please note that all the new clusters in your secondary site are being started now!")
   print ("       If you won't use those new clusters at the moment, please don't forget terminating your new clusters to avoid charges")
   ```

6. **Migrar a configuração de trabalhos**

   Se você migrou as configurações de cluster na etapa anterior, você pode optar por migrar as configurações de trabalho para o novo espaço de trabalho. É uma etapa totalmente automatizada usando o databricks-CLI, a menos que você queira fazer migração de configuração de trabalho seletiva em vez de fazer isso para todos os trabalhos.

   > [!NOTE]
   > A configuração de um trabalho agendado contém também as informações de "agendamento", portanto, por padrão, isso começará a funcionar de acordo com o tempo configurado assim que for migrado. Portanto, o bloco de código a seguir remove todas as informações de agendamento durante a migração (para evitar execuções duplicadas em espaços de trabalho novos e antigos). Configure os agendamentos para esses trabalhos quando você estiver pronto para a transferência.

   A configuração do trabalho requer configurações para um cluster novo ou existente. Se você estiver usando um cluster existente, o script/Code abaixo tentará substituir a ID de cluster antiga pela nova ID de cluster.

   Copie e salve o script Python a seguir em um arquivo. Substitua o valor para `old_cluster_id` e `new_cluster_id`, com a saída da migração de cluster feita na etapa anterior. Execute-o em sua linha de comando do databricks-CLI, por exemplo, `python scriptname.py`.

   ```python
   from subprocess import call, check_output
   import json

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Please replace the old to new cluster id mappings from cluster migration output
   cluster_old_new_mappings = {"old_cluster_id": "new_cluster_id"}

   # Get all jobs info from old workspace
   try:
     jobs_out = check_output(["databricks", "jobs", "list", "--profile", EXPORT_PROFILE])
     jobs_info_list = jobs_out.splitlines()
   except:
     print "No jobs to migrate"
     sys.exit(0)

   # Create a list of all job ids
   jobs_list = []
   for jobs_info in jobs_info_list:
     jobs_list.append(jobs_info.split(None, 1)[0])

   # Optionally filter job ids out manually, so as to create only required ones in new workspace

   # Create each job in the new workspace based on corresponding settings in the old workspace

   for job in jobs_list:
     print "Trying to migrate " + job

     job_get_out = check_output(["databricks", "jobs", "get", "--job-id", job, "--profile", EXPORT_PROFILE])
     print "Got job config from old workspace"

     job_req_json = json.loads(job_get_out)  
     job_req_settings_json = job_req_json['settings']

     # Remove schedule information so job doesn't start before proper cutover
     job_req_settings_json.pop('schedule', None)

     # Replace old cluster id with new cluster id, if job configured to run against an existing cluster
     if 'existing_cluster_id' in job_req_settings_json:
       if job_req_settings_json['existing_cluster_id'] in cluster_old_new_mappings:
         job_req_settings_json['existing_cluster_id'] = cluster_old_new_mappings[job_req_settings_json['existing_cluster_id']]
       else:
         print "Mapping not available for old cluster id " + job_req_settings_json['existing_cluster_id']
         continue

     call(["databricks", "jobs", "create", "--json", json.dumps(job_req_settings_json), "--profile", IMPORT_PROFILE])
     print "Sent job create request to new workspace successfully"

   print "All done"
   ```

7. **Migrar bibliotecas**

   Atualmente, não há uma maneira simples de migrar bibliotecas de um espaço de trabalho para outro. Em vez disso, reinstale as bibliotecas no novo espaço de trabalho manualmente. É possível automatizar o uso da combinação de [CLI DBFS](https://github.com/databricks/databricks-cli#dbfs-cli-examples) para carregar bibliotecas personalizadas para o espaço de trabalho e [bibliotecas CLI](https://github.com/databricks/databricks-cli#libraries-cli).

8. **Migrar o armazenamento de BLOBs do Azure e montagens de Azure Data Lake Storage**

   Remonte manualmente todos os pontos de montagem [do armazenamento de BLOBs do Azure e do](/azure/databricks/data/data-sources/azure/azure-storage) [Azure data Lake Storage (Gen 2)](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) usando uma solução baseada em bloco de anotações. Os recursos de armazenamento teriam sido montados no espaço de trabalho primário e precisam ser repetidos no espaço de trabalho secundário. Não há API externa para montagens.

9. **Migrar scripts de inicialização de cluster**

   Todos os scripts de inicialização do cluster podem ser migrados do espaço de trabalho antigo para o novo usando a [CLI do DBFS](https://github.com/databricks/databricks-cli#dbfs-cli-examples). Primeiro, copie os scripts necessários de `dbfs:/dat abricks/init/..` para sua área de trabalho local ou máquina virtual. Em seguida, copie esses scripts para o novo espaço de trabalho no mesmo caminho.

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **Reconfigure e reaplique manualmente o controle de acesso.**

    Se o espaço de trabalho primário existente estiver configurado para usar a SKU (camada Premium), é provável que você também esteja usando o [recurso de controle de acesso](/azure/databricks/administration-guide/access-control/index).

    Se você usar o recurso de controle de acesso, reaplique manualmente o controle de acesso aos recursos (blocos de anotações, clusters, trabalhos, tabelas).

## <a name="disaster-recovery-for-your-azure-ecosystem"></a>Recuperação de desastre para seu ecossistema do Azure

Se você estiver usando outros serviços do Azure, certifique-se de implementar práticas recomendadas de recuperação de desastre para esses serviços também. Por exemplo, se você optar por usar uma instância de metastore do Hive externa, deverá considerar a recuperação de desastre para o [azure SQL Server](../sql-database/sql-database-disaster-recovery.md), o [Azure HDInsight](../hdinsight/hdinsight-high-availability-linux.md)e/ou o [banco de dados do Azure para MySQL](../mysql/concepts-business-continuity.md). Para obter informações gerais sobre a recuperação de desastre, consulte [recuperação de desastre para aplicativos do Azure](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte a [documentação do Azure Databricks](index.yml).