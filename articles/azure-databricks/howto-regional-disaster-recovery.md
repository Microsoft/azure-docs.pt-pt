---
title: Recuperação regional de desastres para Azure Databricks
description: Este artigo descreve uma abordagem para fazer a recuperação de desastres em Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/13/2019
ms.openlocfilehash: 599dc950cf286520e3f099966786d836ad05ba94
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84789332"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Recuperação regional de desastres para aglomerados Azure Databricks

Este artigo descreve uma arquitetura de recuperação de desastres útil para clusters Azure Databricks, e os passos para realizar esse design.

## <a name="azure-databricks-architecture"></a>Arquitetura Azure Databricks

A um nível elevado, quando se cria um espaço de trabalho Azure Databricks a partir do portal Azure, um [aparelho gerido](../azure-resource-manager/managed-applications/overview.md) é implantado como um recurso Azure na sua subscrição, na região de Azure escolhida (por exemplo, Eua Ocidental). Este aparelho é implantado numa [Rede Virtual Azure](../virtual-network/virtual-networks-overview.md) com um [Grupo de Segurança de Rede](../virtual-network/manage-network-security-group.md) e uma conta de Armazenamento Azure, disponível na sua subscrição. A rede virtual fornece segurança ao nível do perímetro para o espaço de trabalho databricks e é protegida através do grupo de segurança da rede. Dentro do espaço de trabalho, pode criar clusters Databricks fornecendo o tipo VM do trabalhador e do condutor e a versão de tempo de execução databricks. Os dados persistidos estão disponíveis na sua conta de armazenamento, que pode ser Azure Blob Storage ou Azure Data Lake Storage. Uma vez criado o cluster, você pode executar empregos através de cadernos, APIs REST, pontos finais ODBC/JDBC, anexando-os a um cluster específico.

O plano de controlo Databricks gere e monitoriza o ambiente de espaço de trabalho databricks. Qualquer operação de gestão como a criação de cluster será iniciada a partir do plano de controlo. Todos os metadados, tais como trabalhos programados, são armazenados numa Base de Dados Azure com geo-replicação para tolerância a falhas.

![Arquitetura databricks](media/howto-regional-disaster-recovery/databricks-architecture.png)

Uma das vantagens desta arquitetura é que os utilizadores podem ligar a Azure Databricks a qualquer recurso de armazenamento na sua conta. Um benefício fundamental é que tanto o cálculo (Azure Databricks) como o armazenamento podem ser dimensionado independentemente uns dos outros.

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>Como criar uma topologia regional de recuperação de desastres

Como nota na descrição da arquitetura anterior, existem uma série de componentes usados para um pipeline big data com Azure Databricks: Azure Storage, Azure Database, e outras fontes de dados. Azure Databricks é o *cálculo* para o oleoduto Big Data. É de natureza *efémera,* o que significa que, embora os seus dados ainda estejam disponíveis no Azure Storage, o *cálculo* (cluster Azure Databricks) pode ser encerrado para que não tenha de pagar o cálculo quando não precisa. O *cálculo* (Azure Databricks) e as fontes de armazenamento devem estar na mesma região para que os empregos não experimentem uma elevada latência.  

Para criar a sua própria topologia regional de recuperação de desastres, siga estes requisitos:

   1. Fornecendo vários espaços de trabalho Azure Databricks em regiões separadas de Azure. Por exemplo, crie o espaço de trabalho primário Azure Databricks no Leste dos EUA2. Crie o espaço de trabalho secundário de recuperação de desastres Azure Databricks numa região separada, como os EUA Ocidentais.

   2. Utilize [armazenamento geo-redundante](../storage/common/storage-redundancy.md). Os dados associados Azure Databricks são armazenados por padrão no Azure Storage. Os resultados dos trabalhos da Databricks também são armazenados no Azure Blob Storage, de modo que os dados processados são duráveis e permanecem altamente disponíveis após o encerramento do cluster. Como o cluster de armazenamento e databricks está co-localizado, você deve usar armazenamento geo-redundante para que os dados possam ser acedidos na região secundária se a região primária não for mais acessível.

   3. Uma vez criada a região secundária, deve migrar os utilizadores, pastas de utilizador, cadernos, configuração de clusters, configuração de empregos, bibliotecas, armazenamento, scripts init e reconfigurar o controlo de acesso. Detalhes adicionais são descritos na secção seguinte.

## <a name="detailed-migration-steps"></a>Passos de migração detalhados

1. **Configurar a interface de linha de comando Databricks no seu computador**

   Este artigo mostra uma série de exemplos de código que usam a interface de linha de comando para a maioria dos passos automatizados, uma vez que é um invólucro fácil de utilizador sobre Azure Databricks REST API.

   Antes de efetuar quaisquer etapas de migração, instale os databricks-cli no seu computador de secretária ou numa máquina virtual onde planeie fazer o trabalho. Para mais informações, consulte [instalar databricks CLI](/azure/databricks/dev-tools/databricks-cli)

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > Espera-se que quaisquer scripts python fornecidos neste artigo funcionem com Python 2.7+ < 3.x.

2. **Configure dois perfis.**

   Configure um para o espaço de trabalho primário, e outro para o espaço de trabalho secundário:

   ```bash
   databricks configure --profile primary --token
   databricks configure --profile secondary --token
   ```

   Os blocos de código deste artigo alternam entre perfis em cada passo subsequente utilizando o comando do espaço de trabalho correspondente. Certifique-se de que os nomes dos perfis que cria são substituídos em cada bloco de código.

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   Pode alternar manualmente na linha de comando, se necessário:

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **Migrar utilizadores do Diretório Ativo Azure**

   Adicione manualmente os mesmos utilizadores do Azure Ative Directory ao espaço de trabalho secundário que existe no espaço de trabalho primário.

4. **Migrar as pastas e cadernos do utilizador**

   Utilize o seguinte código python para migrar os ambientes de utilizador com caixa de areia, que incluem a estrutura de pasta aninhada e os cadernos por utilizador.

   > [!NOTE]
   > As bibliotecas não são copiadas neste passo, uma vez que a API subjacente não as apoia.

   Copie e guarde o seguinte script python para um ficheiro e execute-o na sua linha de comando Databricks. Por exemplo, `python scriptname.py`.

   ```python
   import sys
   import os
   import subprocess
   from subprocess import call, check_output
   
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   
   # Get a list of all users
   user_list_out = check_output(["databricks", "workspace", "ls", "/Users", "--profile", EXPORT_PROFILE])
   user_list = (user_list_out.decode(encoding="utf-8")).splitlines()
   
   print (user_list)
   
   # Export sandboxed environment(folders, notebooks) for each user and import into new workspace.
   #Libraries are not included with these APIs / commands.
   
   for user in user_list:
     #print("Trying to migrate workspace for user ".decode() + user)
     print (("Trying to migrate workspace for user ") + user)
   
     subprocess.call(str("mkdir -p ") + str(user), shell = True)
     export_exit_status = call("databricks workspace export_dir /Users/" + str(user) + " ./" + str(user) + " --profile " + EXPORT_PROFILE, shell = True)
   
     if export_exit_status==0:
       print ("Export Success")
       import_exit_status = call("databricks workspace import_dir ./" + str(user) + " /Users/" + str(user) + " --profile " + IMPORT_PROFILE, shell=True)
       if import_exit_status==0:
         print ("Import Success")
       else:
         print ("Import Failure")
     else:
       print ("Export Failure")
   print ("All done")
   ```

5. **Migrar as configurações do cluster**

   Uma vez migrados os cadernos, pode opcionalmente migrar as configurações do cluster para o novo espaço de trabalho. É quase um passo totalmente automatizado usando databricks-cli, a menos que você gostaria de fazer migração seletiva de config de cluster e não para todos.

   > [!NOTE]
   > Infelizmente não existe um ponto final de configuração de cluster config, e este script tenta criar cada cluster imediatamente. Se não houver núcleos suficientes disponíveis na sua subscrição, a criação do cluster pode falhar. A falha pode ser ignorada, desde que a configuração seja transferida com sucesso.

   O seguinte script forneceu impressões de um mapeamento de iDs antigos para novos clusters, que poderia ser usado para migração de emprego mais tarde (para trabalhos configurados para usar clusters existentes).

   Copie e guarde o seguinte script python para um ficheiro e execute-o na sua linha de comando Databricks. Por exemplo, `python scriptname.py`.

   ```python
   import sys
   import os
   import subprocess
   import json
   from subprocess import call, check_output
   
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   
   # Get all clusters info from old workspace
   clusters_out = check_output(["databricks", "clusters", "list",    "--profile", EXPORT_PROFILE])
   clusters_info_list = str(clusters_out.decode(encoding="utf-8")).   splitlines()
   print("Printting Cluster info List")
   print(clusters_info_list)
   
   # Create a list of all cluster ids
   clusters_list = []
   ##for cluster_info in clusters_info_list: clusters_list.append   (cluster_info.split(None, 1)[0])
   
   for cluster_info in clusters_info_list:
      if cluster_info != '':
         clusters_list.append(cluster_info.split(None, 1)[0])
   
   # Optionally filter cluster ids out manually, so as to create only required ones in new workspace

   # Create a list of mandatory / optional create request elements
   cluster_req_elems = ["num_workers","autoscale","cluster_name","spark_version","spark_conf","node_type_id","driver_node_type_id","custom_tags","cluster_log_conf","spark_env_vars","autotermination_minutes","enable_elastic_disk"]
   print("Printing Cluster element List")
   print (cluster_req_elems)
   print(str(len(clusters_list)) + " clusters found in the primary site" )
   
   print ("---------------------------------------------------------")
   # Try creating all / selected clusters in new workspace with same config as in old one.
   cluster_old_new_mappings = {}
   i = 0
   for cluster in clusters_list:
      i += 1
      print("Checking cluster " + str(i) + "/" + str(len(clusters_list)) + " : " +str(cluster))
      cluster_get_out_f = check_output(["databricks", "clusters", "get", "--cluster-id", str(cluster), "--profile", EXPORT_PROFILE])
      cluster_get_out=str(cluster_get_out_f.decode(encoding="utf-8"))
      print ("Got cluster config from old workspace")
      print (cluster_get_out)
       # Remove extra content from the config, as we need to build create request with allowed elements only
      cluster_req_json = json.loads(cluster_get_out)
      cluster_json_keys = cluster_req_json.keys()

      #Don't migrate Job clusters
      if cluster_req_json['cluster_source'] == u'JOB' :
         print ("Skipping this cluster as it is a Job cluster : " + cluster_req_json['cluster_id'] )
         print ("---------------------------------------------------------")
         continue

         #cluster_req_json.pop(key, None)
         for key in cluster_json_keys:
           if key not in cluster_req_elems:
            print (cluster_req_json)
            #cluster_del_item=cluster_json_keys .keys()
            cluster_req_json.popitem(key, None)
  
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

   Se migrar as configurações do cluster no passo anterior, pode optar por migrar as configurações de trabalho para o novo espaço de trabalho. É um passo totalmente automatizado usando databricks-cli, a menos que você gostaria de fazer o trabalho seletivo config migração em vez de fazê-lo para todos os trabalhos.

   > [!NOTE]
   > A configuração para um trabalho programado também contém a informação de "agendamento", por isso, por padrão, começará a funcionar de acordo com o tempo configurado assim que é migrado. Assim, o seguinte bloco de código remove qualquer informação de agenda durante a migração (para evitar duplicados corre através de espaços de trabalho antigos e novos). Configure os horários para tais empregos quando estiver pronto para o corte.

   A configuração do trabalho requer configurações para um cluster novo ou existente. Se utilizar o cluster existente, o script /código abaixo tentará substituir o ID do cluster antigo por um novo ID do cluster.

   Copie e guarde o seguinte script python para um ficheiro. Substitua o valor `old_cluster_id` `new_cluster_id` e, com a saída da migração do cluster feita na etapa anterior. Coloque-o na sua linha de comando databricks-cli, por exemplo, `python scriptname.py` .

   ```python
   import sys
   import os
   import subprocess
   import json
   from subprocess import call, check_output
   
   
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   
   # Please replace the old to new cluster id mappings from cluster migration output
   cluster_old_new_mappings = {"0227-120427-tryst214": "0229-032632-paper88"}
   
   # Get all jobs info from old workspace
   try:
     jobs_out = check_output(["databricks", "jobs", "list", "--profile", EXPORT_PROFILE])
     jobs_info_list = jobs_out.splitlines()
   except:
     print("No jobs to migrate")
     sys.exit(0)
   
   # Create a list of all job ids
   jobs_list = []
   for jobs_info in jobs_info_list:
     jobs_list.append(jobs_info.split(None, 1)[0])
   
   # Optionally filter job ids out manually, so as to create only required ones in new workspace
   
   # Create each job in the new workspace based on corresponding settings in the old workspace
   
   for job in jobs_list:
     print("Trying to migrate ") + job
   
     job_get_out = check_output(["databricks", "jobs", "get", "--job-id", job, "--profile", EXPORT_PROFILE])
     print("Got job config from old workspace")
   
     job_req_json = json.loads(job_get_out)  
     job_req_settings_json = job_req_json['settings']
   
     # Remove schedule information so job doesn't start before proper cutover
     job_req_settings_json.pop('schedule', None)
   
     # Replace old cluster id with new cluster id, if job configured to run against an existing cluster
     if 'existing_cluster_id' in job_req_settings_json:
       if job_req_settings_json['existing_cluster_id'] in cluster_old_new_mappings:
         job_req_settings_json['existing_cluster_id'] = cluster_old_new_mappings[job_req_settings_json['existing_cluster_id']]
       else:
         print("Mapping not available for old cluster id ") + job_req_settings_json['existing_cluster_id']
         continue
   
     call(["databricks", "jobs", "create", "--json", json.dumps(job_req_settings_json), "--profile", IMPORT_PROFILE])
     print("Sent job create request to new workspace successfully")
   
   print("All done")
   ```

7. **Bibliotecas migratórias**

   Não há uma maneira simples de migrar bibliotecas de um espaço de trabalho para outro. Em vez disso, reinstale essas bibliotecas no novo espaço de trabalho manualmente. É possível automatizar utilizando a combinação de [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples) para carregar bibliotecas personalizadas para o espaço de trabalho e [bibliotecas CLI.](https://github.com/databricks/databricks-cli#libraries-cli)

8. **Migrar o armazenamento de blob do Azure e os suportes de armazenamento do Lago de Dados Azure**

   Remonte manualmente todos os pontos [de armazenamento Azure Blob](/azure/databricks/data/data-sources/azure/azure-storage) e [Azure Data Lake Storage (Gen 2)](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) utilizando uma solução baseada em portátil. Os recursos de armazenamento teriam sido montados no espaço de trabalho primário, e isso tem que ser repetido no espaço de trabalho secundário. Não há API externa para os suportes.

9. **Roteiros de init cluster migram**

   Quaisquer scripts de inicialização de cluster podem ser migrados de antigos para novos espaços de trabalho utilizando o [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples). Em primeiro lugar, copie os scripts necessários `dbfs:/dat abricks/init/..` para o seu ambiente de trabalho local ou máquina virtual. Em seguida, copie esses scripts para o novo espaço de trabalho no mesmo caminho.

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **Reconfigure manualmente e reencafe o controlo de acesso.**

    Se o seu espaço de trabalho primário existente estiver configurado para utilizar o nível Premium (SKU), é provável que também esteja a utilizar a [função Controlo de Acesso](/azure/databricks/administration-guide/access-control/index).

    Se utilizar a função Controlo de Acesso, reaplie manualmente o controlo de acesso aos recursos (Cadernos, Clusters, Empregos, Tabelas).

## <a name="disaster-recovery-for-your-azure-ecosystem"></a>Recuperação de desastres para o seu ecossistema Azure

Se estiver a utilizar outros serviços da Azure, certifique-se de implementar também as melhores práticas de recuperação de desastres para esses serviços. Por exemplo, se optar por utilizar uma instância de metástaria de Colmeia externa, deve considerar a recuperação de desastres para [a Base de Dados Azure SQL](../azure-sql/database/disaster-recovery-guidance.md), [Azure HDInsight](../hdinsight/hdinsight-high-availability-linux.md)e/ou [Base de Dados Azure para o MySQL](../mysql/concepts-business-continuity.md). Para obter informações gerais sobre a recuperação de [desastres, consulte a recuperação de desastres para aplicações Azure](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte [a documentação da Azure Databricks](index.yml).
