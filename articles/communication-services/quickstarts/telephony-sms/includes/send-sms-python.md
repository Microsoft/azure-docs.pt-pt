---
title: incluir ficheiro
description: incluir ficheiro
services: azure-communication-services
author: lakshmans
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: lakshmans
ms.openlocfilehash: aba9b71ec2fbfedecf08577c7bd2eae7a28a5588
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644305"
---
Inicie-se com os Serviços de Comunicação Azure utilizando os Serviços de Comunicação Python SMS SDK para enviar mensagens SMS.

Completar este quickstart incorre num pequeno custo de alguns usd ou menos na sua conta Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](#todo-sdk-repo) | [Package (PiPy)](#todo-nuget) | [Samples](#todo-samples)--> 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- [Python](https://www.python.org/downloads/) 2.7 ou 3.6+.
- Um recurso ativo dos Serviços de Comunicação e cadeia de ligação. [Criar um recurso de Serviços de Comunicação.](../../create-communication-resource.md)
- Um número de telefone por SMS habilitado. [Obter um número de telefone.](../get-phone-number.md)

### <a name="prerequisite-check"></a>Verificação pré-requisito

- Numa janela de terminal ou comando, verifique `python --version` se o Python está instalado.
- Para visualizar os números de telefone associados ao seu recurso serviços de comunicação, inscreva-se no [portal Azure,](https://portal.azure.com/)localize o seu recurso de Serviços de Comunicação e abra o separador **números** de telefone a partir do painel de navegação à esquerda.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

Abra o seu terminal ou janela de comando, crie um novo diretório para a sua aplicação e navegue até ele.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Use um editor de texto para criar um ficheiro chamado **send-sms.py** no diretório de raiz do projeto e adicione a estrutura para o programa, incluindo o manuseamento básico de exceções. Irá adicionar todo o código fonte para este arranque rápido a este ficheiro nas seguintes secções.

```python
import os
from azure.communication.sms import SmsClient

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-the-package"></a>Instale o pacote

Enquanto ainda está no diretório de aplicações, instale o pacote SMS SDK dos Serviços de Comunicação Azure para o pacote Python utilizando o `pip install` comando.

```console
pip install azure-communication-sms --pre
```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características do Azure Communication Services SMS SDK para Python.

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Esta classe é necessária para toda a funcionalidade SMS. Você instantaneamente com as suas informações de subscrição, e use-as para enviar mensagens SMS.                                                                                                                 |
| SmsSendResult               | Esta classe contém o resultado do serviço SMS.                                          |

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instantiizar um **SmsClient** com a sua cadeia de ligação. Saiba como gerir a [cadeia de ligação do seu recurso.](../../create-communication-resource.md#store-your-connection-string)

```python
# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(<connection_string>)
```
Para simplificar, estamos a usar cordas de ligação neste arranque rápido, mas em ambientes de produção recomendamos o uso [de identidades geridas](../../../quickstarts/managed-identity.md) porque são mais seguras e manejáveis à escala.


## <a name="send-a-11-sms-message"></a>Envie uma mensagem SMS 1:1

Para enviar uma mensagem SMS a um único destinatário, ligue para o ```send``` método do **SmsClient** com um único número de telefone do destinatário. Pode também passar em parâmetros opcionais para especificar se o relatório de entrega deve ser ativado e definir etiquetas personalizadas. Adicione este código ao fim do `try` bloco em **send-sms.py:**

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to="<to-phone-number>",
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

Deverá substituir `<from-phone-number>` por um número de telefone por SMS associado ao seu serviço de comunicação e `<to-phone-number>` pelo número de telefone a que deseja enviar uma mensagem. 

> [!WARNING]
> Note que os números de telefone devem ser fornecidos no formato padrão E.164. (por exemplo: +14255550123).

## <a name="send-a-1n-sms-message"></a>Envie uma mensagem SMS 1:N

Para enviar uma mensagem SMS para uma lista de destinatários, ligue para o ```send``` método do **SmsClient** com uma lista dos números de telefone do destinatário. Pode também passar em parâmetros opcionais para especificar se o relatório de entrega deve ser ativado e definir etiquetas personalizadas. Adicione este código ao fim do `try` bloco em **send-sms.py:**

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to=["<to-phone-number-1>", "<to-phone-number-2>"],
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

Deverá substituir `<from-phone-number>` por um número de telefone por SMS associado ao seu serviço de comunicação e `<to-phone-number-1>` `<to-phone-number-2>` por números de telefone para os quais deseja enviar uma mensagem. 

> [!WARNING]
> Note que os números de telefone devem ser fornecidos no formato padrão E.164. (por exemplo: +14255550123).

## <a name="optional-parameters"></a>Parâmetros Opcionais

O `enable_delivery_report` parâmetro é um parâmetro opcional que pode utilizar para configurar relatórios de entrega. Isto é útil para cenários em que pretende emitir eventos quando as mensagens SMS são entregues. Consulte o [Punho SMS Events](../handle-sms-events.md) quickstart para configurar relatórios de entrega para as suas mensagens SMS.

O `tag` parâmetro é um parâmetro opcional que pode utilizar para aplicar uma etiqueta no Relatório de Entrega.

## <a name="run-the-code"></a>Executar o código
Executar o pedido do seu diretório de candidaturas com o `python` comando.

```console
python send-sms.py
```

O roteiro completo da Python deve parecer algo como:

```python

import os
from azure.communication.sms import SmsClient

try:
    # Create the SmsClient object which will be used to send SMS messages
    sms_client = SmsClient.from_connection_string("<connection string>")
    # calling send() with sms values
    sms_responses = sms_client.send(
       from_="<from-phone-number>",
       to="<to-phone-number>",
       message="Hello World via SMS",
       enable_delivery_report=True, # optional property
       tag="custom-tag") # optional property

except Exception as ex:
    print('Exception:')
    print(ex)
```
