---
title: Gerir a Azure Data Lake Analytics usando Python
description: Este artigo descreve como usar python para gerir contas data lake analytics, fontes de dados, utilizadores, & empregos.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: d4213a19-4d0f-49c9-871c-9cd6ed7cf731
ms.topic: how-to
ms.date: 06/08/2018
ms.custom: devx-track-python
ms.openlocfilehash: dd3511fefe39f51a521aa211454a49763224a6d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87876245"
---
# <a name="manage-azure-data-lake-analytics-using-python"></a>Gerir a Azure Data Lake Analytics usando Python
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Este artigo descreve como gerir as contas do Azure Data Lake Analytics, fontes de dados, utilizadores e empregos utilizando python.

## <a name="supported-python-versions"></a>Versões Python suportadas

* Use uma versão de 64 bits de Python.
* Você pode usar a distribuição padrão python encontrada em **[Python.org downloads](https://www.python.org/downloads/)**. 
* Muitos desenvolvedores acham conveniente usar a **[distribuição Daconda Python.](https://www.anaconda.com/download/)**  
* Este artigo foi escrito usando a versão Python 3.6 da distribuição padrão python

## <a name="install-azure-python-sdk"></a>Instalar o SDK de Python para o Azure

Instalar os seguintes módulos:

* O módulo **azure-mgmt-recurso** inclui outros módulos Azure para Ative Directory, etc.
* O módulo **azure-datalake-store** inclui as operações do sistema de ficheiros Azure Data Lake Store. 
* O módulo **azure-mgmt-datalake-store** inclui as operações de gestão da conta Azure Data Lake Store.
* O módulo **azure-mgmt-datalake-analytics** inclui as operações Azure Data Lake Analytics. 

Em primeiro lugar, certifique-se de que tem as últimas `pip` informações executando o seguinte comando:

```console
python -m pip install --upgrade pip
```

Este documento foi escrito utilizando `pip version 9.0.1` .

Utilize os `pip` seguintes comandos para instalar os módulos a partir da linha de comando:

```console
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>Criar um novo script Python

Cole o seguinte código no script:

```python
# Use this only for Azure AD service-to-service authentication
#from azure.common.credentials import ServicePrincipalCredentials

# Use this only for Azure AD end-user authentication
#from azure.common.credentials import UserPassCredentials

# Required for Azure Resource Manager
from azure.mgmt.resource.resources import ResourceManagementClient
from azure.mgmt.resource.resources.models import ResourceGroup

# Required for Azure Data Lake Store account management
from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
from azure.mgmt.datalake.store.models import DataLakeStoreAccount

# Required for Azure Data Lake Store filesystem management
from azure.datalake.store import core, lib, multithread

# Required for Azure Data Lake Analytics account management
from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInformation

# Required for Azure Data Lake Analytics job management
from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

# Required for Azure Data Lake Analytics catalog management
from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

# Use these as needed for your application
import logging
import getpass
import pprint
import uuid
import time
```

Execute este script para verificar se os módulos podem ser importados.

## <a name="authentication"></a>Autenticação

### <a name="interactive-user-authentication-with-a-pop-up"></a>Autenticação interativa do utilizador com um pop-up

Este método não é apoiado.

### <a name="interactive-user-authentication-with-a-device-code"></a>Autenticação interativa do utilizador com um código de dispositivo

```python
user = input(
    'Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>Autenticação não interativa com SPI e um segredo

```python
credentials = ServicePrincipalCredentials(
    client_id='FILL-IN-HERE', secret='FILL-IN-HERE', tenant='FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>Autenticação não interativa com API e certificado

Este método não é apoiado.

## <a name="common-script-variables"></a>Variáveis comuns de script

Estas variáveis são usadas nas amostras.

```python
subid = '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>'  # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adla = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>Criar os clientes

```python
resourceClient = ResourceManagementClient(credentials, subid)
adlaAcctClient = DataLakeAnalyticsAccountManagementClient(credentials, subid)
adlaJobClient = DataLakeAnalyticsJobManagementClient(
    credentials, 'azuredatalakeanalytics.net')
```

## <a name="create-an-azure-resource-group"></a>Criar um Grupo de Recursos do Azure

```python
armGroupResult = resourceClient.resource_groups.create_or_update(
    rg, ResourceGroup(location=location))
```

## <a name="create-data-lake-analytics-account"></a>Criar conta de Data Lake Analytics

Primeiro criar uma conta na loja.

```python
adlsAcctResult = adlsAcctClient.account.create(
    rg,
    adls,
    DataLakeStoreAccount(
        location=location)
    )
).wait()
```
Em seguida, crie uma conta ADLA que utilize essa loja.

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

## <a name="submit-a-job"></a>Submeter um emprego

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

## <a name="wait-for-a-job-to-end"></a>Esperar que um trabalho acabe.

```python
jobResult = adlaJobClient.job.get(adla, jobId)
while(jobResult.state != JobState.ended):
    print('Job is not yet done, waiting for 3 seconds. Current state: ' +
          jobResult.state.value)
    time.sleep(3)
    jobResult = adlaJobClient.job.get(adla, jobId)

print('Job finished with result: ' + jobResult.result.value)
```

## <a name="list-pipelines-and-recurrences"></a>Listar gasodutos e recorrências
Dependendo se os seus trabalhos têm metadados de pipeline ou recorrência anexados, pode listar os oleodutos e as recorrências.

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>Gerir políticas de cálculo

O objeto DataLakeAnalyticsAccountManagementClient fornece métodos para gerir as políticas de computação para uma conta Data Lake Analytics.

### <a name="list-compute-policies"></a>Listar políticas de cálculo

O código seguinte recupera uma lista de políticas de computação para uma conta Data Lake Analytics.

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' +
          p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>Criar uma nova política de computação

O código que se segue cria uma nova política de computação para uma conta Data Lake Analytics, definindo o máximo de AUs disponíveis para o utilizador especificado para 50, e a prioridade mínima de trabalho para 250.

```python
userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde"
newPolicyParams = ComputePolicyCreateOrUpdateParameters(
    userAadObjectId, "User", 50, 250)
adlaAccountClient.computePolicies.createOrUpdate(
    rg, adla, "GaryMcDaniel", newPolicyParams)
```

## <a name="next-steps"></a>Passos seguintes

- Para ver o mesmo tutorial, utilizando outras ferramentas, clique nos seletores de separador no topo da página.
- Para saber mais sobre U-SQL, consulte [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
- Para tarefas de gestão, consulte [Manage Azure Data Lake Analytics utilizando o portal Azure](data-lake-analytics-manage-use-portal.md).

