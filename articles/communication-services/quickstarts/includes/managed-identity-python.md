---
ms.openlocfilehash: c642b0e5f459b2412bca6588c8ae625142f0f59f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450219"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>Adicione identidade gerida à sua solução de Serviços de Comunicação

### <a name="install-the-sdk-packages"></a>Instalar os pacotes SDK

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-sdk-packages"></a>Utilize os pacotes SDK

Adicione o seguinte `import` ao seu código para utilizar a Identidade Azure.

```python
from azure.identity import DefaultAzureCredential
```

Os exemplos abaixo estão a utilizar o [DefaultAzureCredential](/python/api/azure-identity/azure.identity.defaultazurecredential). Esta credencial é adequada para ambientes de produção e desenvolvimento.

Para uma forma fácil de saltar para a autenticação de identidade gerida, consulte [Autorizar o acesso com identidade gerida](../managed-identity-from-cli.md)

Para uma análise mais aprofundada de como funciona o objeto DefaultAzureCredential e como pode usá-lo de formas que não são especificadas neste arranque rápido, consulte a [biblioteca de clientes da Identidade Azure para Python](https://docs.microsoft.com/python/api/overview/azure/identity-readme)

### <a name="create-an-identity-and-issue-a-token"></a>Criar uma identidade e emitir um símbolo

O seguinte exemplo de código mostra como criar um objeto de cliente de serviço com identidade gerida e, em seguida, usar o cliente para emitir um símbolo para um novo utilizador:

```python
from azure.communication.identity import CommunicationIdentityClient

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>Envie um SMS com identidade gerida a Azure
O exemplo de código que se segue mostra como criar um objeto de cliente de serviço com identidade gerida Azure e, em seguida, usar o cliente para enviar uma mensagem SMS:

```python
from azure.communication.sms import SmsClient

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     credential = DefaultAzureCredential()
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_=from_phone_number,
          to_=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
```

### <a name="list-all-your-purchased-phone-numbers"></a>Enuda todos os seus números de telefone adquiridos

O exemplo de código que se segue mostra como criar um objeto de cliente de serviço com identidade gerida Azure, em seguida, usar o cliente para ver todos os números de telefone adquiridos que o recurso tem:

```python
from azure.communication.phonenumbers import PhoneNumbersClient

def list_purchased_phone_numbers(resource_endpoint):
     credential = DefaultAzureCredential()
     phone_numbers_client = PhoneNumbersClient(resource_endpoint, credential)

     return phone_numbers_client.list_purchased_phone_numbers()
```