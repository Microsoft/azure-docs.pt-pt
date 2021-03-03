---
ms.openlocfilehash: 9da0cbc3777359994fac3778dcc126c844754861
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657643"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>Adicione identidade gerida à sua solução de Serviços de Comunicação

### <a name="install-the-client-library-packages"></a>Instale os pacotes da biblioteca do cliente

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-client-library-packages"></a>Use os pacotes da biblioteca do cliente

Adicione o seguinte `import` ao seu código para utilizar a Identidade Azure.

```python
from azure.identity import DefaultAzureCredential
```

Os exemplos abaixo estão a utilizar o [DefaultAzureCredential](/python/api/azure.identity.defaultazurecredential). Esta credencial é adequada para ambientes de produção e desenvolvimento.

Para registar a aplicação no ambiente de desenvolvimento e configurar variáveis ambientais, consulte [Autorizar o acesso com identidade gerida](../managed-identity-from-cli.md)

### <a name="create-an-identity-and-issue-a-token"></a>Criar uma identidade e emitir um símbolo

O seguinte exemplo de código mostra como criar um objeto de cliente de serviço com identidade gerida e, em seguida, usar o cliente para emitir um símbolo para um novo utilizador:

```python
import azure.communication.identity 

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])
     
     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>Envie um SMS com identidade gerida a Azure

O exemplo de código que se segue mostra como criar um objeto de cliente de serviço com identidade gerida Azure e, em seguida, usar o cliente para enviar uma mensagem SMS:

```python
from azure.communication.sms import (
    PhoneNumberIdentifier,
    SendSmsOptions,
    SmsClient
)

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     credential = DefaultAzureCredential()
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_phone_number=PhoneNumberIdentitifier(from_phone_number),
          to_phone_numbers=[PhoneNumberIdentifier(to_phone_number)],
          message=message_content,
          send_sms_options=SendSmsOptions(enable_delivery_report=True))  # optional property
     )
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre a autenticação](../../concepts/authentication.md)

Também pode querer:

- [Saiba mais sobre o controlo de acesso baseado em funções da Azure](../../../../articles/role-based-access-control/index.yml)
- [Saiba mais sobre a biblioteca de identidades Azure para Python](/net/api/overview/azure/identity-readme)
- [Criação de fichas de acesso ao utilizador](../../quickstarts/access-tokens.md)
- [Enviar uma mensagem SMS](../../quickstarts/telephony-sms/send.md)
- [Saiba mais sobre SMS](../../concepts/telephony-sms/concepts.md)