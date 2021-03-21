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
ms.openlocfilehash: e8424f6b5b7617b00de6dedbece3325f3c5513c8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103622198"
---
Inicie-se com os Serviços de Comunicação Azure utilizando a biblioteca de clientes PYTHON para enviar mensagens SMS.

Completar este quickstart incorre num pequeno custo de alguns usd ou menos na sua conta Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](#todo-sdk-repo) | [Package (PiPy)](#todo-nuget) | [Samples](#todo-samples)--> 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- [Python](https://www.python.org/downloads/) 2.7, 3.5, ou acima.
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

Enquanto ainda está no diretório de aplicações, instale a biblioteca de clientes Azure Communication Services PARA pacote Python utilizando o `pip install` comando.

```console
pip install azure-communication-sms --pre
```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características da biblioteca de clientes dos Serviços de Comunicação Azure para Python.

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Esta classe é necessária para toda a funcionalidade SMS. Você instantaneamente com as suas informações de subscrição, e use-as para enviar mensagens SMS.                                                                                                                 |
| SmsSendResult               | Esta classe contém o resultado do serviço SMS.                                          |

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instantiizar um **SmsClient** com a sua cadeia de ligação. O código abaixo recupera a cadeia de ligação para o recurso a partir de uma variável ambiental chamada `COMMUNICATION_SERVICES_CONNECTION_STRING` . Saiba como gerir a [cadeia de ligação dos recursos.](../../create-communication-resource.md#store-your-connection-string)

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.getenv('COMMUNICATION_SERVICES_CONNECTION_STRING')

# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(connection_string)
```

## <a name="send-a-11-sms-message"></a>Envie uma mensagem SMS 1:1

Para enviar uma mensagem SMS a um único destinatário, ligue para o ```send``` método do **SmsClient** com um único número de telefone do destinatário. Pode também passar em parâmetros opcionais para especificar se o relatório de entrega deve ser ativado e definir etiquetas personalizadas. Adicione este código ao fim do `try` bloco em **send-sms.py:**

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to="<to-phone-number>,
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

Deverá substituir `<from-phone-number>` por um número de telefone por SMS associado ao seu serviço de comunicação e `<to-phone-number>` pelo número de telefone a que deseja enviar uma mensagem. 

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

Deverá substituir `<from-phone-number>` por um número de telefone por SMS associado ao seu serviço de comunicação `<to-phone-number-1>` e pelos `<to-phone-number-2>` números de telefone a que deseja enviar uma mensagem. 

## <a name="optional-parameters"></a>Parâmetros Opcionais

O `enable_delivery_report` parâmetro é um parâmetro opcional que pode utilizar para configurar relatórios de entrega. Isto é útil para cenários em que pretende emitir eventos quando as mensagens SMS são entregues. Consulte o [Punho SMS Events](../handle-sms-events.md) quickstart para configurar relatórios de entrega para as suas mensagens SMS.

O `tag` parâmetro é um parâmetro opcional que pode usar para configurar a marcação personalizada.

## <a name="run-the-code"></a>Executar o código

Executar o pedido do seu diretório de candidaturas com o `python` comando.

```console
python send-sms.py
```
