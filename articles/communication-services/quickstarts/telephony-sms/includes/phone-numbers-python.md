---
ms.openlocfilehash: 283d7d1c83dc4a68901c17c36b548e00e1044e34
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629400"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Python](https://www.python.org/downloads/) 2.7, 3.5, ou acima.
- Um recurso de serviços de comunicação implantado e uma cadeia de conexão. [Criar um recurso de Serviços de Comunicação.](../../create-communication-resource.md)

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

Abra o seu terminal ou janela de comando e crie um novo diretório para a sua aplicação e, em seguida, navegue para ela.

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

Utilize um editor de texto para criar um ficheiro chamado phone_numbers_sample.py no diretório de raiz do projeto e adicione o seguinte código. Vamos adicionar o código de arranque rápido restante nas seguintes secções.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

try:
   print('Azure Communication Services - Phone Numbers Quickstart')
   # Quickstart code goes here
except Exception as ex:
   print('Exception:')
   print(ex)
```

### <a name="install-the-package"></a>Instale o pacote

Enquanto ainda está no diretório de aplicações, instale a biblioteca de clientes da Azure Communication Services Administration para o pacote Python utilizando o `pip install` comando.

```console
pip install azure-communication-phone-numbers
```

## <a name="authenticate-the-phone-numbers-client"></a>Autenticar o Cliente números de telefone

O `PhoneNumbersClient` is enabled to use Azure Ative Directory Authentication. A utilização do `DefaultAzureCredential` objeto é a forma mais fácil de começar com o Azure Ative Directory e pode instalá-lo usando o `pip install` comando. 

```console
pip install azure-identity
```

A criação de um `DefaultAzureCredential` objeto requer que `AZURE_CLIENT_ID` tenha, e já definido como `AZURE_CLIENT_SECRET` `AZURE_TENANT_ID` variáveis ambientais com os seus valores correspondentes a partir da sua aplicação AD Azure registada.

Uma vez instalada a `azure-identity` biblioteca, podemos continuar a autenticar o cliente.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure Portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
except Exception as ex:
    print('Exception:')
    print(ex)
```

Em alternativa, é também posível utilizar o ponto final e a chave de acesso do recurso de comunicação para autenticar.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

# You can find your connection string from your resource in the Azure Portal
connection_string = 'https://<RESOURCE_NAME>.communication.azure.com/;accesskey=<YOUR_ACCESS_KEY>'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    phone_numbers_client = PhoneNumbersClient.from_connection_string(connection_string)
except Exception as ex:
    print('Exception:')
    print(ex)
```

## <a name="functions"></a>Funções

Uma vez `PhoneNumbersClient` autenticado, podemos começar a trabalhar nas diferentes funções que pode fazer.

### <a name="search-for-available-phone-numbers"></a>Pesquisa rumo a números de telefone disponíveis

Para comprar números de telefone, primeiro deve procurar por quaisquer números de telefone disponíveis. Para procurar números de telefone, forneça o código de área, o tipo de atribuição, [as capacidades do número de telefone,](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services) [o tipo de número de telefone](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)e a quantidade (a quantidade predefinida está definida para 1). Note que para o tipo de número de telefone gratuito, desde que o código de área seja opcional.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient, PhoneNumberCapabilityType, PhoneNumberAssignmentType, PhoneNumberType, PhoneNumberCapabilities
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure Portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = self.phone_number_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        polling = True
    )
    search_result = search_poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="purchase-phone-numbers"></a>Comprar Números de Telefone

O resultado da procura de números de telefone é um `PhoneNumberSearchResult` . Isto contém um `searchId` que pode ser passado para os números de compra API para adquirir os números na pesquisa. Note que ligar para os números de telefone de compra API resultará num encargo para a sua Conta Azure.

```python
import os
from azure.communication.phonenumbers import (
    PhoneNumbersClient, 
    PhoneNumberCapabilityType, 
    PhoneNumberAssignmentType, 
    PhoneNumberType, 
    PhoneNumberCapabilities
)
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure Portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = phone_numbers_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        area_code="833",
        polling = True
    )
    search_result = poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

    purchase_poller = phone_numbers_client.begin_purchase_phone_numbers(search_result.search_id, polling = True)
    purchase_poller.result()
    print("The status of the purchase operation was: " + purchase_poller.status())
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="get-purchased-phone-numbers"></a>Obter números de telefone comprados

Depois de um número de compra, pode recuperá-lo do cliente. 

```python
purchased_phone_number_information = phone_numbers_client.get_purchased_phone_number("+18001234567")
print('Phone number: ' + purchased_phone_number_information.phone_number)
print('Country code: ' + purchased_phone_number_information.country_code)
```

Também pode recuperar todos os números de telefone adquiridos.

```python
purchased_phone_numbers = phone_numbers_client.list_purchased_phone_numbers()
print('Purchased phone numbers:')
for purchased_phone_number in purchased_phone_numbers:
    print(purchased_phone_number.phone_number)
```

### <a name="update-phone-number-capabilities"></a>Atualizar capacidades de número de telefone

Pode atualizar as capacidades de um número de telefone previamente adquirido.
```python
update_poller = phone_numbers_client.begin_update_phone_number_capabilities(
    "+18001234567",
    PhoneNumberCapabilityType.OUTBOUND,
    PhoneNumberCapabilityType.OUTBOUND,
    polling = True
)
update_poller.result()
print('Status of the operation: ' + update_poller.status())
```

### <a name="release-phone-number"></a>Número de telefone de lançamento

Pode libertar um número de telefone comprado.

```python
release_poller = phone_numbers_client.begin_release_phone_number("+18001234567")
release_poller.result()
print('Status of the operation: ' + release_poller.status())
```

## <a name="run-the-code"></a>Executar o código

A partir de um pedido de consola, navegue até ao diretório que contém o ficheiro phone_numbers_sample.py e, em seguida, execute o seguinte comando python para executar a aplicação.

```console
./phone_numbers_sample.py
```
