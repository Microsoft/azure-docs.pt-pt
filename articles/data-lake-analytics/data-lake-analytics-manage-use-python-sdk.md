---
title: Gerir o Azure Data Lake Analytics com Python
description: Este artigo descreve como utilizar Python para gerir contas do Data Lake Analytics, origens de dados, os utilizadores e tarefas.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: d4213a19-4d0f-49c9-871c-9cd6ed7cf731
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: 82007c780a0c9ff3bb2e1a50a4826499f9df9c9f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811724"
---
# <a name="manage-azure-data-lake-analytics-using-python"></a>Gerir o Azure Data Lake Analytics com Python
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Este artigo descreve como gerir contas do Azure Data Lake Analytics, origens de dados, os utilizadores e tarefas com o Python.

## <a name="supported-python-versions"></a>Versões suportadas do Python

* Utilize uma versão de 64 bits do Python.
* Pode usar o padrão de distribuição da Python encontrada na  **[Python.org downloads](https://www.python.org/downloads/)**. 
* Os desenvolvedores consideram útil para utilização a  **[distribuição Anaconda Python](https://www.anaconda.com/download/)**.  
* Este artigo foi escrito com o Python versão 3.6 da distribuição de Python padrão

## <a name="install-azure-python-sdk"></a>Instalar o SDK de Python para o Azure

Instale os seguintes módulos:

* O **azure-mgmt-resource** módulo inclui outros módulos do Azure para o Active Directory, etc.
* O **azure-datalake-store** módulo inclui as operações de sistema de ficheiros do Azure Data Lake Store. 
* O **azure-mgmt-datalake-store** módulo inclui as operações de gestão de conta do Azure Data Lake Store.
* O **azure-mgmt-datalake-analytics** módulo inclui as operações do Azure Data Lake Analytics. 

Em primeiro lugar, certifique-se de que tem a versão mais recente `pip` executando o seguinte comando:

```
python -m pip install --upgrade pip
```

Este documento foi escrito usando `pip version 9.0.1`.

Utilize o seguinte `pip` comandos para instalar os módulos de linha de comandos:

```
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>Criar um novo script de Python

Cole o seguinte código para o script:

```python
## Use this only for Azure AD service-to-service authentication
#from azure.common.credentials import ServicePrincipalCredentials

## Use this only for Azure AD end-user authentication
#from azure.common.credentials import UserPassCredentials

## Required for Azure Resource Manager
from azure.mgmt.resource.resources import ResourceManagementClient
from azure.mgmt.resource.resources.models import ResourceGroup

## Required for Azure Data Lake Store account management
from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
from azure.mgmt.datalake.store.models import DataLakeStoreAccount

## Required for Azure Data Lake Store filesystem management
from azure.datalake.store import core, lib, multithread

## Required for Azure Data Lake Analytics account management
from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInformation

## Required for Azure Data Lake Analytics job management
from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

## Required for Azure Data Lake Analytics catalog management
from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

## Use these as needed for your application
import logging, getpass, pprint, uuid, time
```

Execute este script para verificar que os módulos podem ser importados.

## <a name="authentication"></a>Autenticação

### <a name="interactive-user-authentication-with-a-pop-up"></a>Autenticação de utilizador interativa com um pop-up

Este método não é suportado.

### <a name="interactive-user-authentication-with-a-device-code"></a>Autenticação de utilizador interativa com um código de dispositivo

```python
user = input('Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>Autenticação noninteractive com SPI e um segredo

```python
credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>Autenticação noninteractive com a API e um certificado

Este método não é suportado.

## <a name="common-script-variables"></a>Variáveis de script comuns

Estas variáveis são utilizadas nos exemplos.

```python
subid= '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>' # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adla = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>Criar os clientes

```python
resourceClient = ResourceManagementClient(credentials, subid)
adlaAcctClient = DataLakeAnalyticsAccountManagementClient(credentials, subid)
adlaJobClient = DataLakeAnalyticsJobManagementClient( credentials, 'azuredatalakeanalytics.net')
```

## <a name="create-an-azure-resource-group"></a>Criar um Grupo de Recursos do Azure

```python
armGroupResult = resourceClient.resource_groups.create_or_update( rg, ResourceGroup( location=location ) )
```

## <a name="create-data-lake-analytics-account"></a>Criar conta de Data Lake Analytics

Em primeiro lugar, crie uma conta de armazenamento.

```python
adlsAcctResult = adlsAcctClient.account.create(
    rg,
    adls,
    DataLakeStoreAccount(
        location=location)
    )
).wait()
```
Em seguida, crie uma conta ADLA utiliza esse arquivo.

```python
adlaAcctResult = adlaAcctClient.account.create(
    rg,
    adla,
    DataLakeAnalyticsAccount(
        location=location,
        default_data_lake_store_account=adls,
        data_lake_store_accounts=[DataLakeStoreAccountInformation(name=adls)]
    )
).wait()
```

## <a name="submit-a-job"></a>Submeter uma tarefa

```python
script = """
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"""

jobId = str(uuid.uuid4())
jobResult = adlaJobClient.job.create(
    adla,
    jobId,
    JobInformation(
        name='Sample Job',
        type='USql',
        properties=USqlJobProperties(script=script)
    )
)
```

## <a name="wait-for-a-job-to-end"></a>Aguarde que uma tarefa terminar

```python
jobResult = adlaJobClient.job.get(adla, jobId)
while(jobResult.state != JobState.ended):
    print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
    time.sleep(3)
    jobResult = adlaJobClient.job.get(adla, jobId)

print ('Job finished with result: ' + jobResult.result.value)
```

## <a name="list-pipelines-and-recurrences"></a>Lista de pipelines e recorrências
Dependendo se suas tarefas têm de metadados de pipeline ou de periodicidade anexado, pode listar os pipelines e recorrências.

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>Gerir políticas de computação

O objeto de DataLakeAnalyticsAccountManagementClient fornece métodos para gerir as políticas de computação para uma conta do Data Lake Analytics.

### <a name="list-compute-policies"></a>Políticas de computação de lista

O código a seguir recupera uma lista de políticas de computação para uma conta do Data Lake Analytics.

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' + p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>Criar uma nova política de computação

O código seguinte cria uma nova política de computação para uma conta do Data Lake Analytics, definir o AUs máximo disponível para o utilizador especificado como 50 e a prioridade do trabalho mínimo para 250.

```python
userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde"
newPolicyParams = ComputePolicyCreateOrUpdateParameters(userAadObjectId, "User", 50, 250)
adlaAccountClient.computePolicies.createOrUpdate(rg, adla, "GaryMcDaniel", newPolicyParams)
```

## <a name="next-steps"></a>Passos Seguintes

- Para ver o mesmo tutorial, utilizando outras ferramentas, clique nos seletores de separador no topo da página.
- Para saber mais sobre U-SQL, consulte [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
- Para tarefas de gestão, veja [Manage Azure Data Lake Analytics using Azure portal (Gerir o Azure Data Lake Analytics com o Portal do Azure)](data-lake-analytics-manage-use-portal.md).

