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
ms.openlocfilehash: e812fa47d35889a9cf8c671a4df6034812272a6a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670634"
---
# <a name="troubleshoot-library-installation-errors"></a>Erros de instalação da biblioteca de resolução de problemas 
Para disponibilizar códigos de terceiros ou construído localmente para as suas aplicações, pode instalar uma biblioteca numa das suas piscinas Apache Spark sem servidor. Os pacotes listados no ficheiro requirements.txt são descarregados do PyPi no momento da startup de pool. Este ficheiro de requisitos é usado sempre que uma instância Spark é criada a partir dessa piscina Spark. Uma vez instalada uma biblioteca para uma piscina Spark, está disponível para todas as sessões usando a mesma piscina. 

Em alguns casos, poderá descobrir que a biblioteca que está a tentar instalar não está a aparecer na sua piscina Apache Spark. Este caso ocorre frequentemente quando há um erro nas bibliotecas requirements.txt ou especificadas fornecidas. Quando houver um erro no processo de instalação da biblioteca, a piscina Apache Spark voltará às bibliotecas especificadas no tempo de funcionamento da base de Synapse.

O objetivo deste documento é fornecer questões comuns e ajudá-lo a depurar erros de instalação da biblioteca.

## <a name="force-update-your-apache-spark-pool"></a>Force atualizar a sua piscina Apache Spark
Quando atualizar as bibliotecas da sua piscina Apache Spark, estas alterações serão recolhidas assim que a piscina tiver sido reiniciada. Se tiver empregos ativos, estes empregos continuarão a funcionar na versão original da piscina de faíscas.

Pode forçar as alterações a aplicar selecionando a opção para **forçar novas definições**. Esta definição terminará todas as sessões atuais para a piscina Spark selecionada. Uma vez terminadas as sessões, terá de esperar pelo recomeço da piscina. 

![Adicionar bibliotecas Python](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Adicionar bibliotecas Python")

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

## <a name="check-the-requirements-file"></a>Verifique o ficheiro de requisitos
Um ficheiro ***requirements.txt*** (saída do comando de congelamento de pip) pode ser usado para atualizar o ambiente virtual. Este ficheiro segue o formato descrito na documentação de referência de congelamento de [pip.](https://pip.pypa.io/en/stable/reference/pip_freeze/)

É importante observar as seguintes restrições:
   -  O nome do pacote PyPI deve ser listado juntamente com uma versão exata. 
   -  O conteúdo do ficheiro de requisitos não deve incluir linhas ou caracteres em branco extra. 
   -  O [Synapse Runtime](apache-spark-version-support.md) inclui um conjunto de bibliotecas que são pré-instaladas em todas as piscinas Apache Spark sem servidor. Os pacotes que vêm pré-instalados no tempo de funcionação da base não podem ser desclassificados. Os pacotes só podem ser adicionados ou atualizados.
   -  Alterar a versão PySpark, Python, Scala/Java, .NET ou Spark não é suportado.

O seguinte corte mostra o formato necessário para o ficheiro de requisitos.

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

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

## <a name="next-steps"></a>Passos seguintes
- Ver as bibliotecas padrão: [Suporte à versão Apache Spark](apache-spark-version-support.md)