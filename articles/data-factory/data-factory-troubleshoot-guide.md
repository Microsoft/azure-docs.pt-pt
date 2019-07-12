---
title: Resolução de problemas do Azure Data Factory | Documentos da Microsoft
description: Resolução de problemas do Azure Data Factory. Documento comuns para externas todas as atividades de controlo.
services: data-factory
author: abnarain
manager: craigg
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 6/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: d220730bb2e93e32d00e56ed98f4962ad89eda5a
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626313"
---
# <a name="troubleshooting-azure-data-factory"></a>Resolução de problemas do Azure Data Factory
Este artigo apresenta uma lista de perguntas de resolução de problemas comuns.

- [O Azure Databricks (bloco de notas, jars, python)](#azure-databricks)
- [Azure Data Lake Analytics (U-SQL)](#azure-data-lake-analytics-u-sql)
- [Funções do Azure](#azure-functions)
- [Personalizado (o Azure Batch)](#custom-azure-batch)
- [HDInsight (Spark, Hive, MapReduce, Pig, transmissão em fluxo Hadoop)](#hdinsight-spark-hive-mapreduce-pig-hadoop-streaming)
- [Atividade Web](#web-activity)



## <a name="azure-databricks"></a>Azure Databricks
| Código de Erro | Mensagem de Erro                                          | Descrição do Problema                             | Correção possível / ação recomendada                            |
| -------------- | ----------------------------------------------------- | --------------------------------------------------------------| :----------------------------------------------------------- |
| 3200           | Erro 403                                                    | Token de acesso do Databricks expirou.                         | Por predefinição, o token de acesso do Databricks é válido durante 90 dias.  Crie um novo token e atualizar o serviço ligado. |
| 3201           | Campo obrigatório em falta: settings.task.notebook_task.notebook_path | Má criação: Caminho do notebook não está especificado corretamente. | Especifique o caminho do notebook na atividade do Databricks. |
| 3201           | Cluster... não existe                                 | Erro de criação: Cluster do Databricks não existe ou foi eliminado. | Verifique se o cluster do Databricks existe. |
| 3201           | URI de ficheiro de python inválido:... Visite o guia de utilizador do Databricks para esquemas URI suportados. | Má de criação                                                | Especifique os caminhos absolutos para esquemas de endereçamento de área de trabalho, ou "dbfs:/folder/subfolder/foo.py" para ficheiros armazenados em DBFS. |
| 3201           | {0}   O LinkedService deve ter domínio e accessToken como propriedades necessárias | Má de criação                                                | Verifique [associado à definição de serviço](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | {0}   O LinkedService deve especificar o Id de cluster existente ou novas informações de criação de cluster | Má de criação                                                | Verifique [associado à definição de serviço](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | Tipo de nó Standard_D16S_v3 não é suportado. Tipos de nó suportados:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_ NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2 | Má de criação                                                | Consulte a mensagem de erro                                          |
| 3201           | Notebook_path inválido:... Atualmente são suportados apenas caminhos absolutos. Caminhos tem de começar por "/". | Má de criação                                                | Consulte a mensagem de erro                                          |
| 3202           | Já havia 1000 tarefas criadas no passado 3600 segundos, que excede o limite de taxa:   criações de tarefa de 1000 por 3600 segundos. | Databricks demasiados é executado numa hora.                         | Verifique todos os pipelines que utilizam esta área de trabalho do Databricks para a taxa de criação da tarefa.  Se pipelines iniciados demasiados Databricks execuções em forma agregada, migre alguns pipelines para uma nova área de trabalho. |
| 3202           | Não foi possível analisar o objeto de solicitação: Esperado 'key' e 'value' a definir para base_parameters de campo de mapa JSON, nos "chave:""..." em vez disso. | Erro de criação: Nenhum valor fornecido para o parâmetro         | Inspecione o json do pipeline e certifique-se de que todos os parâmetros no bloco de notas baseParameters tem um valor não vazio especificado. |
| 3202           | Utilizador: SimpleUserContext {userId =..., nome =user@company.com, orgId =...} não tem autorização para aceder ao cluster | Utilizador que gerou o token de acesso não tem permissão para aceder ao cluster do Databricks especificado no serviço ligado. | Certifique-se de que o utilizador tem as permissões necessárias na área de trabalho.   |
| 3203           | O cluster está no estado de Terminated, não está disponível para receber as tarefas. . Corrija o cluster ou tente novamente mais tarde. | Cluster foi terminado.    Para o cluster interativo, isso pode ser uma condição de corrida. | Melhor maneira de evitar isso é usar clusters de tarefa.             |
| 3204           | Falha na execução de tarefa. Pode existir qualquer número de mensagens de erro, de estado de cluster inesperado a mensagem de específicos da atividade.  Mais comum não é nenhuma mensagem de erro. | N/A                                                          | N/A                                                          |



## <a name="azure-data-lake-analytics-u-sql"></a>Azure Data Lake Analytics (U-SQL)

| Código de erro         | Mensagem de Erro                                                | Descrição do Problema                                          | Correção possível / ação recomendada                             |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2709                 | O token de acesso é do inquilino errado                    | Inquilino do AAD incorreto                                         | O Principal de serviço utilizada para aceder a ADLA pertence a outro inquilino do AAD. Crie o novo Principal de serviço no mesmo inquilino como conta ADLA. |
| 2711,   2705,   2704 | Não é permitido. Falha na verificação de ACL. O recurso não existe ou o utilizador não está autorizado a efetuar a operação pedida<br/><br/>Utilizador não é capaz de aceder para datalake store  <br/><br/>Utilizador não tem autorização para o data lake analytics | O Principal de serviço ou o certificado fornecido não tem acesso para o ficheiro no armazenamento | Certifique-se de que o Principal de serviço ou certificado fornecem para tarefas ADLA tem acesso à conta do ADLA e o armazenamento do ADLS predefinido para o mesmo da pasta de raiz. |
| 2711                 | Não é possível localizar a pasta ou ficheiro de "Do Azure Data Lake Store"       | O caminho para o ficheiro de u-SQL é o serviço ligado não tem acesso a credenciais ou de eram incorretas | Verifique se o caminho e as credenciais fornecidas no serviço ligado |
| 2707                 | Não é possível resolver a conta de AzureDataLakeAnalytics. Verifique "AccountName" e "DataLakeAnalyticsUri". | A conta ADLA no serviço ligado está errada                  | Verifique se que a conta correta é fornecida             |
| 2703                 | Id do erro: E_CQO_SYSTEM_INTERNAL_ERROR ou qualquer erro começa com "Id do erro:" | Erro for proveniente de ADLA                                    | Qualquer erro que parece que o exemplo significa que a tarefa foi submetido para ADLA e o script existe com falha. A investigação deve ser feita em ADLA. Se abrir o portal e navegue para a conta ADLA, procure a tarefa com o Id (não execução de pipeline) de execução da atividade ADF. A tarefa lá terão mais informações sobre o erro e irá ajudar a resolver problemas. Se a resolução não está clara, contacte a equipa de suporte do ADLA e indique o URL de tarefa, que inclui o nome da sua conta e o ID da tarefa. |
| 2709                 | Nós não pode aceitar o seu trabalho neste momento. O número máximo de tarefas em fila para a sua conta é 200. | Limitação de ADLA                                           | Reduza o número de trabalhos submetidos para ADLA alterando o ADF acionadores e as definições de simultaneidade em atividades ou aumentar os limites em ADLA |
| 2709                 | Esta tarefa foi rejeitada porque requer 24 UAS e esta conta tem uma política definida pelo administrador que impede que uma tarefa usando mais do que 5 UAS. | Limitação de ADLA                                           | Reduza o número de trabalhos submetidos para ADLA alterando o ADF acionadores e as definições de simultaneidade em atividades ou aumentar os limites em ADLA |



## <a name="azure-functions"></a>Funções do Azure

| Código de erro | Mensagem de Erro                           | Descrição                                                  | Correção possível / ação recomendada                           |
| ------------ | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3600         | Conteúdo da resposta não é um válido JObject | Isso significa que a função do Azure que foi chamada não devolveu um Payload de JSON na resposta. Actividade de função do Azure do ADF suporta apenas o conteúdo da resposta JSON. | Atualizar a função do Azure para devolver um Payload de JSON válido por exemplo, um C# função pode retornar (ActionResult) novo < OkObjectResult ("{`\"Id\":\"123\"`}"); |
| 3600         | Invalid HttpMethod: ‘..’.               | Isso significa que o método Http especificado no payload de atividade não é suportado pela atividade de função do Azure. | Os métodos Http suportados são:  <br/>COLOCAR, PUBLICAR, OBTER, ELIMINAR, OPTIONS, HEAD, RASTREIO |



## <a name="custom-azure-batch"></a>Personalizado (o Azure Batch)
| Código de erro | Mensagem de Erro                                                | Descrição                                                  | Correção possível / ação recomendada                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2500         | Pressione uma exceção inesperada e falha na execução.             | Não é possível iniciar o comando ou o programa devolveu um código de erro. | Verifique se existe o executável. Se o programa iniciado, verifique o stdout.txt e stderr. txt carregado para a conta de armazenamento. É uma boa prática para emitir os registos grandes em seu código para depuração. |
| 2501         | Não pode aceder à conta de batch de utilizador, verifique as definições de conta do batch. | Batch acesso à chave ou um conjunto de nome incorreto fornecido.            | Tem de verificar o nome do conjunto e a chave de acesso do Batch no serviço ligado. |
| 2502         | Pode não armazenamento com acesso de utilizador da conta, tenha definições de conta de armazenamento de verificação. | Incorreto conta nome ou o acesso de armazenamento fornecido.       | Tem de verificar a conta acesso e o nome de armazenamento no serviço ligado. |
| 2504         | Operação de devolveu um código de estado inválido 'BadRequest'     | Demasiados ficheiros em folderPath se a atividade personalizada.  (O tamanho total dos resourceFiles não pode ser mais do que 32 768 carateres.) | Remova ficheiros desnecessários, ou zip-los e adicionar um comando de descompactar para extrair, por exemplo: powershell.exe - nologo - noprofile-comando "& {Add-Type - um 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory ($zipFile, $folder); }" ;  $folder\yourProgram.exe |
| 2505         | Não é possível criar a assinatura de acesso partilhado, a menos que são utilizadas credenciais de chave da conta. | Atividades personalizadas suportam apenas contas de armazenamento que utilizar uma chave de acesso. | Consulte a descrição                                            |
| 2507         | O caminho da pasta não existe ou está vazio:...            | Não existem ficheiros na conta de armazenamento no caminho especificado.       | FolderPath tem de conter os executáveis que pretende executar. |
| 2508         | Lá está arquivos duplicados na pasta de recurso.               | Existem vários ficheiros, o mesmo nome em subpastas diferentes de folderPath. | Atividades personalizadas nivelamento de estrutura de pastas em folderPath.  Se a estrutura de pastas tem de ser preservados, os ficheiros zip e extraí-las no Azure Batch com um comando de descompactar, por exemplo: powershell.exe - nologo - noprofile-comando "& {Add-Type - um 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory ($zipFile, $folder); }" ;   $folder\yourProgram.exe |
| 2509         | Url do batch... é inválido, tem de estar no formato de Uri.         | URLs de batch devem ser semelhantes ao https:\//mybatchaccount.eastus.batch.azure.com | Consulte a descrição                                            |
| 2510         | Ocorreu um erro ao enviar o pedido.               | O URL de batch é inválido                                         | Verifique se o URL do batch.                                            |

## <a name="hdinsight-spark-hive-mapreduce-pig-hadoop-streaming"></a>HDInsight (Spark, Hive, MapReduce, Pig, transmissão em fluxo Hadoop)

| Código de erro | Mensagem de Erro                                                | Descrição                                                  | Correção possível / ação recomendada                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2300,   2310 | Falha na submissão de tarefas do Hadoop. Erro: Não foi possível resolver o nome remoto. <br/><br/>O cluster não foi encontrado. | URI do cluster fornecido é inválido                              | Certifique-se de que o cluster não foi eliminado e o URI fornecido está correto. É possível abrir o URI em qualquer browser e deverá ver a IU do Ambari. Se o cluster estiver numa vNet, em seguida, o URI deve ser o URI privado e tente abri-lo a partir de uma VM que faz parte da mesma vNet. Obter mais informações para [rede Virtual no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services). |
| 2300         | Falha na submissão de tarefas do Hadoop. Tarefa:..., Cluster:... /. Erro: Uma tarefa foi cancelada. | A submissão da tarefa excedeu o tempo limite.                         | Isto pode ser o problema de conectividade geral do HDInsight ou o problema de conectividade de rede. Confirme que a IU do Ambari HDInsight está disponível através de qualquer browser e as suas credenciais ainda são válidas. Certifique-se de fazer isso a partir da VM/máquina onde o runtime de integração autoalojado está instalado se utilizar o ir autoalojado. Em seguida, tente novamente a submeter a tarefa a partir do ADF. Se continuar a falhar, contacte a equipa do ADF para obter suporte. |
| 2300         | Não autorizado:   Nome de utilizador do Ambari ou a palavra-passe está incorreta  <br/><br/>Não autorizado:   Administrador de utilizador está bloqueada no Ambari   <br/><br/>403 - Proibido: Acesso negado | As credenciais fornecidas para o HDInsight estão incorretas ou estão expiradas | Corrija-os e volte a implementar o serviço ligado. Certificar-se de que as credenciais de trabalho no HDInsight pela primeira vez ao abrir o URI de cluster em qualquer browser e tentar iniciar sessão. Se não funcionam, pode repô-los a partir do Portal do Azure. |
| 2300,   2310 | 502 - o servidor web recebeu uma resposta inválida enquanto funcionava como um servidor de gateway ou proxy       <br/>Gateway incorrecto | Erro está vindo do HDInsight                               | Este erro é proveniente de cluster do HDInsight. Consultar [solucionador de problemas do HDInsight](https://hdinsight.github.io/ambari/ambari-ui-502-error.html) com erros comuns.    <br/>Para clusters do Spark-lo pode também dever devido a [isso](https://hdinsight.github.io/spark/spark-thriftserver-errors.html). <br/><br/>[Hiperligação adicional](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502) |
| 2300         | Falha na submissão de tarefas do Hadoop. Tarefa:..., Cluster:... Erro: {\"erro\":\"não é possível para responder ao pedido de tarefa de envio como templeton serviço está ocupado com demasiados pedidos de tarefa de envio. Aguarde algum tempo antes de repetir a operação. Consulte o templeton.parallellism.job.submit de configuração para configurar pedidos em simultâneo. \  <br/><br/>Falha na submissão de tarefas do Hadoop. Tarefa: 161da5d4-6fa8-4ef4-a240-6b6428c5ae2f, Cluster: https:\//abc-analytics-prod-hdi-hd-trax-prod01.azurehdinsight.net/.   Error: {\"error\":\"java.io.IOException:   org.apache.hadoop.yarn.exceptions.YarnException: Failed to submit   application_1561147195099_3730 to YARN :   org.apache.hadoop.security.AccessControlException: Fila root.joblauncher já tem 500 aplicativos, não é possível aceitar a submissão da aplicação: application_1561147195099_3730\ | Ao mesmo tempo, as tarefas demasiados estão a ser submetidas para HDInsight | Considere limitar o número de tarefas simultâneas que está a ser submetido para o HDI. Consulte a simultaneidade de atividade ADF se eles são enviados pela mesma atividade. Altere os acionadores, para que as execuções de pipeline em simultâneo são espalhadas ao longo do tempo. Consulte também a docs do HDInsight para ajustar o "templeton.parallellism.job.submit" como sugere o erro. |
| 2303,   2347 | Falha na tarefa de Hadoop com o código de saída "5". Consulte "wasbs://adfjobs@adftrialrun.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr" para obter mais detalhes.  <br/><br/>Falha na execução do Hive com o código de erro 'UserErrorHiveOdbcCommandExecutionFailure'.   Consulte "wasbs://adfjobs@eclsupplychainblobd.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out" para obter mais detalhes | A tarefa foi submetida para o HDInsight, e não conseguiu no HDInsight | A tarefa foi submetida para o HDInsight com êxito. Falha no cluster. Volte abrir o trabalho na IU do Ambari do HDInsight e abra os registos existem ou abra o ficheiro de armazenamento dos pontos de mensagem de erro horizontalmente. Os detalhes do erro será nesse ficheiro. |
| 2328         | Ocorreu um erro de servidor interno ao processar o pedido. Repita o pedido ou contacte o suporte | Acontece no HDInsight a pedido.                              | Este erro é proveniente de serviço do HDInsight quando o aprovisionamento do HDInsight falha. Contacte a equipa do HDInsight e disponibilizar-lhes o nome do cluster a pedido no. |
| 2310         | java.lang.NullPointerException                               | Ocorreu um erro ao submeter a tarefa para o cluster do Spark      | Essa exceção é proveniente de HDInsight e é ocultar o problema real.   . Contacte a equipa do HDInsight para obter suporte e fornecê-los com o nome do cluster e o intervalo de tempo de execução de atividade. |
|              | Todos os outros erros                                             |                                                              | Consulte a [solucionador de problemas do HDInsight](../hdinsight/hdinsight-troubleshoot-guide.md) e [FAQ do HDInsight](https://hdinsight.github.io/) |



## <a name="web-activity"></a>Atividade Web

| Código de erro | Mensagem de Erro                                                | Descrição                                                  | Correção possível / ação recomendada                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2108         | Invalid HttpMethod: ‘..’.                                    | Isso significa que o método Http especificado no payload de atividade não é suportado pela atividade Web. | Os métodos Http suportados são: <br/>COLOCAR, PUBLICAR, OBTER, ELIMINAR |
| 2108         | Erro de servidor inválido 500                                     | Erro interno no ponto final                               | Verifique a funcionalidade na URL (com o Fiddler/Postman): [Como utilizar o Fiddler para criar uma sessão HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 401 não autorizado                                             | Autenticação válido em falta no pedido                      | Fornecer o método de autenticação válido (pode ter expirado token).   <br/><br/>Verifique a funcionalidade na URL (com o Fiddler/Postman): [Como utilizar o Fiddler para criar uma sessão HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 403 Proibido                                                | Permissões necessárias em falta                                 | Verifique as permissões de utilizador no recurso acedido.   <br/><br/>Verifique a funcionalidade na URL (com o Fiddler/Postman): [Como utilizar o Fiddler para criar uma sessão HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Pedido incorreto 400                                              | Pedido de Http inválido                                         | Verifique o URL, verbo e corpo do pedido.   <br/><br/>Utilize o Fiddler/Postman para validar o pedido: [Como utilizar o Fiddler para criar uma sessão HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 404 não encontrado                                                | Recurso não foi encontrado                                       | Utilize o Fiddler/Postman para validar o pedido: [Como utilizar o Fiddler para criar uma sessão HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Serviço indisponível                                          | O serviço está indisponível                                       | Utilize o Fiddler/Postman para validar o pedido: [Como utilizar o Fiddler para criar uma sessão HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Tipo de suporte não suportado                                       | Tipo de conteúdo sem correspondência com o corpo de atividade Web           | Especifique o Content-Type correto que corresponde ao formato de payload utilização Fiddler/Postman para validar o pedido: [Como utilizar o Fiddler para criar uma sessão HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | O recurso que estiver à procura para tiver sido removido, o nome alterado, ou está temporariamente indisponível. | O recurso não está disponível                                | Utilize o Fiddler/Postman para verificar o ponto final: [Como utilizar o Fiddler para criar uma sessão HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Não é possível apresentar a página que procura porque está a ser utilizado um método inválido (verbo HTTP). | Método de atividade Web incorreto foi especificado no pedido   | Utilize o Fiddler/Postman para verificar o ponto final: [Como utilizar o Fiddler para criar uma sessão HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | invalid_payload                                              | O corpo da atividade Web está incorreto                       | Utilize o Fiddler/Postman para verificar o ponto final: [Como utilizar o Fiddler para criar uma sessão HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |

#### <a name="how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application"></a>Como utilizar o Fiddler para criar uma sessão HTTP do aplicativo web monitorizadas

1. Transferir e instalar [Fiddler](https://www.telerik.com/download/fiddler)

2. Se a sua aplicação web utiliza HTTPS:

   1. Abra o Fiddler

   2. Aceda a **ferramentas > Opções de Fiddler** e selecione como mostrado na captura de ecrã abaixo. 

      ![Opções do fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

3. Se a sua aplicação utiliza certificados SSL, também tem de adicionar o certificado do Fiddler para o seu dispositivo.

4. Para adicionar o certificado do Fiddler para o seu dispositivo, aceda a **ferramentas** > **Fiddler opções** > **HTTPS**  >   **Ações** > **exportar o certificado de raiz para área de trabalho** para obter o certificado de Fiddler.

5. Desative a captura para que o cache do navegador pode ser desmarcada para iniciar um novo rastreamento.

6. 1. Aceda a **arquivo** > **capturar tráfego** ou prima **F12**.
   2. Limpe o cache do seu browser, para que todos os itens em cache são removidos e tem de ser novamente transferidos.

7. Crie pedido: 

8. 1. Clique na guia Composer
   2. Definir o método Http e o URL
   3. Adicionar cabeçalhos e o corpo do pedido, se necessário
   4. Clique em executar

9. Comece a capturar tráfego novamente e concluir a transação problemática na sua página.

10. Depois de concluída, aceda a **arquivo** > **guardar** > **todas as sessões**.

Obter mais informações sobre o Fiddler [aqui](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)

## <a name="next-steps"></a>Passos Seguintes

Para mais ajuda para encontrar a solução para o seu problema, aqui estão alguns outros recursos, que pode experimentar.

*  [Blogues](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Pedidos de funcionalidades](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter](https://twitter.com/hashtag/DataFactory)



