---
title: Erros de instalação da biblioteca de resolução de problemas
description: Este tutorial fornece uma visão geral sobre como resolver erros de instalação da biblioteca.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 57e9d0c584600a8fac90499d72cfac1620052603
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694925"
---
# <a name="troubleshoot-library-installation-errors"></a>Erros de instalação da biblioteca de resolução de problemas 
Para disponibilizar códigos de terceiros ou construído localmente para as suas aplicações, pode instalar uma biblioteca numa das suas piscinas Apache Spark sem servidor. Os pacotes listados no ficheiro requirements.txt são descarregados do PyPi no momento da startup de pool. Este ficheiro de requisitos é usado sempre que uma instância Spark é criada a partir dessa piscina Spark. Uma vez instalada uma biblioteca para uma piscina Spark, está disponível para todas as sessões usando a mesma piscina. 

Em alguns casos, você pode descobrir que uma biblioteca não está aparecendo na sua piscina Apache Spark. Este caso ocorre frequentemente quando há um erro nas bibliotecas requirements.txt ou especificadas. Quando ocorrer um erro no processo de instalação da biblioteca, a piscina Apache Spark voltará às bibliotecas especificadas no tempo de funcionamento da base da Sinapse.

O objetivo deste documento é fornecer questões comuns e ajudá-lo a depurar erros de instalação da biblioteca.

## <a name="force-update-your-apache-spark-pool"></a>Force atualizar a sua piscina Apache Spark
Quando atualizar as bibliotecas da sua piscina Apache Spark, estas alterações serão recolhidas assim que a piscina tiver sido reiniciada. Se tiver empregos ativos, estes empregos continuarão a funcionar na versão original da piscina de faíscas.

Pode forçar as alterações a aplicar selecionando a opção para **forçar novas definições**. Esta definição terminará todas as sessões atuais para a piscina Spark selecionada. Uma vez terminadas as sessões, terá de esperar que a piscina recomece. 

![Adicionar bibliotecas Python](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Adicionar bibliotecas Python")

## <a name="track-installation-progress"></a>Acompanhar o progresso da instalação
Um trabalho de Spark reservado é iniciado cada vez que uma piscina é atualizada com um novo conjunto de bibliotecas. Este trabalho de faísca ajuda a monitorizar o estado da instalação da biblioteca. Se a instalação falhar devido a conflitos de bibliotecas ou outros problemas, a piscina Spark reverterá para o seu estado anterior ou padrão. 

Além disso, os utilizadores também podem inspecionar os registos de instalação para identificar conflitos de dependência ou ver quais as bibliotecas instaladas durante a atualização da piscina.

Para ver estes registos:
1. Navegue para a lista de aplicações Spark no **separador Monitor.** 
2. Selecione o trabalho de aplicação spark do sistema que corresponde à sua atualização de piscina. Estes trabalhos de sistema funcionam sob o título *de SystemReservedJob-LibraryManagement.*
   ![Screenshot que destaca o trabalho reservado ao sistema na biblioteca.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Ver trabalho de biblioteca de sistema")
3. Mude para ver o **controlador** e os registos **de stdout.** 
4. Dentro dos resultados, você verá os registos relacionados com a instalação dos seus pacotes.
    ![Screenshot que destaca o sistema reservado resultados de trabalho na biblioteca.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Ver o progresso do trabalho na biblioteca do sistema")

## <a name="validate-your-permissions"></a>Valide as suas permissões
Para instalar e atualizar bibliotecas, tem de ter as permissões **do Storage Blob Data Contributor** ou do Proprietário de **Dados blob de armazenamento** na conta primária de armazenamento de dados do Lago de Dados Gen2 que está ligada ao espaço de trabalho Azure Synapse Analytics.

Para validar que tem estas permissões, pode executar o seguinte código:

```python
from pyspark.sql.types import StructType,StructField, StringType, IntegerType
data2 = [("James","Smith","Joe","4355","M",3000),
    ("Michael","Rose","Edward","40288","F",4000)
  ]

schema = StructType([ \
    StructField("firstname",StringType(),True), \
    StructField("middlename",StringType(),True), \
    StructField("lastname",StringType(),True), \
    StructField("id", StringType(), True), \
    StructField("gender", StringType(), True), \
    StructField("salary", IntegerType(), True) \
  ])
 
df = spark.createDataFrame(data=data2,schema=schema)

df.write.csv("abfss://<<ENTER NAME OF FILE SYSTEM>>@<<ENTER NAME OF PRIMARY STORAGE ACCOUNT>>.dfs.core.windows.net/validate_permissions.csv")

```
Se receber um erro, é provável que esteja a perder as permissões necessárias. Para obter as permissões necessárias, visite este documento: [Atribua permissões ao Contribuinte de Dados blob de armazenamento ou ao proprietário do depósito](../../storage/common/storage-auth-aad-rbac-portal.md#assign-an-azure-built-in-role)de dados do Armazenamento Blob .

Além disso, se estiver a executar um Pipeline, o MSI do Espaço de Trabalho deve também ter permissões de Storage Blob Data Owner ou De Armazenamento Blob Data Contributor. Para aprender a conceder a sua identidade de espaço de trabalho esta permissão, visite: [Conceder permissões ao espaço de trabalho identidade gerida.](../security/how-to-grant-workspace-managed-identity-permissions.md)

## <a name="check-the-environment-configuration-file"></a>Verifique o ficheiro de configuração do ambiente
Um ficheiro de configuração ambiental pode ser usado para atualizar o ambiente Conda. Estes formatos de ficheiros aceitáveis para a gestão da piscina Python estão listados [aqui.](./apache-spark-manage-python-packages.md)

É importante observar as seguintes restrições:
   -  O conteúdo do ficheiro de requisitos não deve incluir linhas ou caracteres em branco extra. 
   -  O [Synapse Runtime](apache-spark-version-support.md) inclui um conjunto de bibliotecas que são pré-instaladas em todas as piscinas Apache Spark sem servidor. As embalagens que vêm pré-instaladas no tempo de funcionação da base não podem ser removidas ou desinstaladas.
   -  Alterar a versão PySpark, Python, Scala/Java, .NET ou Spark não é suportado.
   -  As bibliotecas com âmbito de sessão python só aceitam ficheiros com uma extensão YML.

## <a name="validate-wheel-files"></a>Validar ficheiros de rodas
As piscinas Apache Spark sem servidor Synapse são baseadas na distribuição Linux. Ao descarregar e instalar ficheiros Wheel diretamente do PyPI, certifique-se de selecionar a versão que é construída no Linux e funciona na mesma versão Python que a piscina Spark.

>[!IMPORTANT]
>Os pacotes personalizados podem ser adicionados ou modificados entre as sessões. No entanto, terá de esperar pela piscina e pela sessão para reiniciar para ver o pacote atualizado.

## <a name="check-for-dependency-conflicts"></a>Verifique se há conflitos de dependência
 Em geral, a resolução de dependência python pode ser difícil de gerir. Para ajudar a depurar conflitos de dependência localmente, pode criar o seu próprio ambiente virtual baseado no Tempo de Execução da Sinapse e validar as suas alterações.

Para recriar o ambiente e validar as suas atualizações:
 1. [Descarregue](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml) o modelo para recriar localmente o tempo de execução da Sinapse. Pode haver pequenas diferenças entre o modelo e o ambiente sinapse real.
   
 2. Criar um ambiente virtual utilizando as [seguintes instruções](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html). Este ambiente permite criar uma instalação Python isolada com a lista especificada de bibliotecas. 
    
    ```
    conda myenv create -f environment.yml
    conda activate myenv
    ```
   
 3. Utilize ``pip install -r <provide your req.txt file>`` para atualizar o ambiente virtual com os seus pacotes especificados. Se a instalação resultar num erro, poderá haver um conflito entre o que está pré-instalado no tempo de funcionamento da base synapse e o especificado no ficheiro de requisitos fornecido. Estes conflitos de dependência devem ser resolvidos para obter as bibliotecas atualizadas na sua piscina Apache Spark sem servidor.

>[!IMPORTANT]
>Os problemas podem arrisear-se ao utilizar pip e conda juntos. Ao combinar pip e conda, é melhor seguir estas [melhores práticas recomendadas.](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html#using-pip-in-an-environment)

## <a name="next-steps"></a>Passos seguintes
- Ver as bibliotecas padrão: [Suporte à versão Apache Spark](apache-spark-version-support.md)