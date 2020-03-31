---
title: Recuperação regional de desastres para os Tijolos de Dados Azure
description: Este artigo descreve uma abordagem para fazer a recuperação de desastres em Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/13/2019
ms.openlocfilehash: 2604d5b357feacce3493b4a4ded971144262611d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161941"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Recuperação regional de desastres para clusters de Databricks Azure

Este artigo descreve uma arquitetura de recuperação de desastres útil para os clusters Azure Databricks, e os passos para realizar esse design.

## <a name="azure-databricks-architecture"></a>Arquitetura Azure Databricks

A um nível elevado, quando se cria um espaço de trabalho Azure Databricks a partir do portal Azure, um [aparelho gerido](../azure-resource-manager/managed-applications/overview.md) é implantado como um recurso Azure na sua subscrição, na região de Azure escolhida (por exemplo, West US). Este aparelho é implantado numa [Rede Virtual Azure](../virtual-network/virtual-networks-overview.md) com um Grupo de Segurança de [Rede](../virtual-network/manage-network-security-group.md) e uma conta de Armazenamento Azure, disponível na sua subscrição. A rede virtual fornece segurança ao nível do perímetro para o espaço de trabalho databricks e está protegida através de um grupo de segurança de rede. Dentro do espaço de trabalho, pode criar clusters databricks fornecendo o tipo de VM do trabalhador e do condutor e versão de tempo de execução databricks. Os dados persistidos estão disponíveis na sua conta de armazenamento, que pode ser o Armazenamento de Blob Azure ou o Armazenamento do Lago Azure Data. Uma vez criado o cluster, pode executar trabalhos através de cadernos, APIs REST, pontos finais ODBC/JDBC, fixando-os a um cluster específico.

O plano de controlo databricks gere e monitoriza o ambiente espaço de trabalho databricks. Qualquer operação de gestão como criar cluster será iniciada a partir do plano de controlo. Todos os metadados, como empregos programados, são armazenados numa Base de Dados Azure com geo-replicação para tolerância a falhas.

![Arquitetura de tijolos de dados](media/howto-regional-disaster-recovery/databricks-architecture.png)

Uma das vantagens desta arquitetura é que os utilizadores podem ligar os Azure Databricks a qualquer recurso de armazenamento na sua conta. Um benefício fundamental é que tanto a computação (Azure Databricks) como o armazenamento podem ser dimensionados independentemente uns dos outros.

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>Como criar uma topologia regional de recuperação de desastres

Como nota na descrição da arquitetura anterior, existem vários componentes utilizados para um pipeline Big Data com tijolos de dados Azure: Armazenamento Azure, Base de Dados Azure e outras fontes de dados. Azure Databricks é a *computação* do oleoduto Big Data. É de natureza *efémera,* o que significa que, embora os seus dados ainda estejam disponíveis no Armazenamento Azure, o *cálculo* (cluster Azure Databricks) pode ser encerrado para que não tenha de pagar a computação quando não precisa. A *computação* (Azure Databricks) e as fontes de armazenamento devem estar na mesma região para que os empregos não experimentem alta latência.  

Para criar a sua própria topologia regional de recuperação de desastres, siga estes requisitos:

   1. Fornecer vários espaços de trabalho Azure Databricks em regiões separadas de Azure. Por exemplo, crie o espaço de trabalho primário azure Databricks no Leste dos EUA2. Crie o espaço de trabalho de recuperação secundária de desastres Azure Databricks numa região separada, como os EUA Ocidentais.

   2. Utilize [armazenamento geo-redundante.](../storage/common/storage-redundancy.md) Os dados associados aos Tijolos de Dados Azure são armazenados por padrão no Armazenamento Azure. Os resultados dos trabalhos dos Databricks também são armazenados no Armazenamento De Blob Azure, de modo que os dados processados são duráveis e permanecem altamente disponíveis após o encerramento do cluster. Como o cluster de Armazenamento e Databricks é co-localizado, você deve usar armazenamento geo-redundante para que os dados possam ser acedidos na região secundária se a região primária deixar de ser acessível.

   3. Uma vez criada a região secundária, é necessário migrar os utilizadores, pastas de utilizador, cadernos, configuração de clusters, configuração de empregos, bibliotecas, armazenamento, scripts init e reconfigurar o controlo de acesso. Detalhes adicionais são delineados na secção seguinte.

## <a name="detailed-migration-steps"></a>Passos de migração detalhados

1. **Configurar a interface de linha de comando databricks no seu computador**

   Este artigo mostra uma série de exemplos de código que utilizam a interface da linha de comando para a maioria dos passos automatizados, uma vez que é um invólucro fácil de usar sobre a API DO REST De dados azure.

   Antes de realizar quaisquer passos de migração, instale os databricks-cli no seu computador de secretária ou numa máquina virtual onde planeia fazer o trabalho. Para mais informações, consulte [Instalar databricks CLI](/azure/databricks/dev-tools/databricks-cli)

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > Espera-se que quaisquer scripts de python fornecidos neste artigo funcionem com Python 2.7+ < 3.x.

2. **Configure dois perfis.**

   Configure um para o espaço de trabalho primário, e outro para o espaço de trabalho secundário:

   ```bash
   databricks configure --profile primary
   databricks configure --profile secondary
   ```

   Os blocos de código neste artigo alterem entre perfis em cada passo subsequente utilizando o comando espaço de trabalho correspondente. Certifique-se de que os nomes dos perfis que cria são substituídos em cada bloco de código.

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   Pode ligar manualmente na linha de comando se necessário:

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **Utilizadores de Diretório Ativo Migrate Azure**

   Adicione manualmente os mesmos utilizadores do Azure Ative Directory ao espaço de trabalho secundário que existe no espaço de trabalho primário.

4. **Migrar as pastas e cadernos de utilizadores**

   Utilize o seguinte código python para migrar os ambientes de utilizador encaixotados de areia, que incluem a estrutura das pastas aninhadas e os cadernos por utilizador.

   > [!NOTE]
   > As bibliotecas não são copiadas neste passo, uma vez que a API subjacente não as suporta.

   Copie e guarde o seguinte guião python para um ficheiro e execute-o na linha de comando databricks. Por exemplo, `python scriptname.py`.

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

5. **Migrar as configurações do cluster**

   Uma vez migrados os cadernos, pode migrar opcionalmente as configurações do cluster para o novo espaço de trabalho. É quase um passo totalmente automatizado usando databricks-cli, a menos que você gostaria de fazer migração de config de cluster seletivo em vez de para todos.

   > [!NOTE]
   > Infelizmente não há nenhum ponto final de config de cluster, e este script tenta criar cada cluster imediatamente. Se não houver núcleos suficientes disponíveis na sua subscrição, a criação do cluster pode falhar. A falha pode ser ignorada, desde que a configuração seja transferida com sucesso.

   O seguinte script forneceu impressões de um mapeamento de antigos para novos IDs de cluster, que poderia ser usado para migração de emprego mais tarde (para trabalhos que estão configurados para usar clusters existentes).

   Copie e guarde o seguinte guião python para um ficheiro e execute-o na linha de comando databricks. Por exemplo, `python scriptname.py`.

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

6. **Migrar a configuração de empregos**

   Se migrar configurações de cluster no passo anterior, pode optar por migrar configurações de trabalho para o novo espaço de trabalho. Trata-se de um passo totalmente automatizado utilizando databricks-cli, a menos que queira fazer uma migração seletiva de config em vez de fazê-lo para todos os trabalhos.

   > [!NOTE]
   > A configuração para um trabalho programado também contém a informação de "agenda", pelo que, por padrão, começará a funcionar de acordo com o tempo configurado assim que for migrado. Assim, o seguinte bloco de código remove qualquer informação de horário durante a migração (para evitar duplicações em espaços de trabalho antigos e novos). Configure os horários para tais trabalhos uma vez que esteja pronto para o corte.

   A configuração do trabalho requer configurações para um novo ou um cluster existente. Se utilizar o cluster existente, o script/código abaixo tentará substituir o id do cluster antigo por um novo ID de cluster.

   Copie e guarde o seguinte guião python para um ficheiro. Substitua o `old_cluster_id` `new_cluster_id`valor para e, com a saída da migração do cluster feita em etapas anteriores. Execute-o na sua linha de comando databricks-cli, por exemplo, `python scriptname.py`.

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

7. **Bibliotecas de migração**

   Não há uma maneira simples de migrar bibliotecas de um espaço de trabalho para outro. Em vez disso, reinstale manualmente essas bibliotecas no novo espaço de trabalho. É possível automatizar usando a combinação de [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples) para carregar bibliotecas personalizadas para o espaço de trabalho e [bibliotecas CLI](https://github.com/databricks/databricks-cli#libraries-cli).

8. **Armazenamento de blob Migrate Azure e suportes de armazenamento de lago de dados Azure**

   Remonte manualmente todos os pontos de [armazenamento do Azure Blob](/azure/databricks/data/data-sources/azure/azure-storage) e [do Azure Data Lake (Gen 2)](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) utilizando uma solução baseada em cadernos. Os recursos de armazenamento teriam sido montados no espaço de trabalho primário, e isso tem de ser repetido no espaço de trabalho secundário. Não há API externo para cavalos.

9. **Migrar cluster init scripts**

   Quaisquer scripts de inicialização de cluster podem ser migrados de antigos para novos espaços de trabalho usando o [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples). Em primeiro lugar, copie os scripts necessários para `dbfs:/dat abricks/init/..` o seu ambiente de trabalho local ou máquina virtual. Em seguida, copie esses scripts para o novo espaço de trabalho no mesmo caminho.

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **Reconfigurar manualmente e reaplicar o controlo de acesso.**

    Se o seu espaço de trabalho primário existente estiver configurado para utilizar o nível Premium (SKU), é provável que também esteja a utilizar a [função](/azure/databricks/administration-guide/access-control/index)de Controlo de Acesso .

    Se utilizar a função de Controlo de Acesso, reaplique manualmente o controlo de acesso aos recursos (Cadernos, Clusters, Empregos, Tabelas).

## <a name="disaster-recovery-for-your-azure-ecosystem"></a>Recuperação de desastres para o seu ecossistema Azure

Se estiver a utilizar outros serviços Azure, certifique-se de implementar as melhores práticas de recuperação de desastres para esses serviços também. Por exemplo, se optar por utilizar uma metaloja externa da Hive, deve considerar a recuperação de desastres para [o Servidor Azure SQL,](../sql-database/sql-database-disaster-recovery.md) [Azure HDInsight](../hdinsight/hdinsight-high-availability-linux.md)e/ou Base de [Dados Azure para MySQL](../mysql/concepts-business-continuity.md). Para obter informações gerais sobre a recuperação de desastres, consulte a recuperação de [desastres para aplicações do Azure.](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications)

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte a [documentação dos Bricks do Azure.](index.yml)