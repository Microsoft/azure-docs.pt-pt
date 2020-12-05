---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: azure-communication-services
author: danieldoolabh
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 09/03/2020
ms.topic: include
ms.custom: include file
ms.author: dadoolab
ms.openlocfilehash: a24d9531b7b2d2d2f31eec275da7db7e48b9c74a
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96615778"
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
from azure.communication.sms import PhoneNumber
from azure.communication.sms import SendSmsOptions
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
| SmsClient | Esta classe é necessária para toda a funcionalidade SMS. Você instantaneamente com as suas informações de subscrição, e use-as para enviar mensagens SMS. |
| Envios Opções | Esta classe oferece opções para configurar relatórios de entrega. Se enable_delivery_report está definido para True, então um evento será emitido quando a entrega foi bem sucedida |

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instantiizar um **SmsClient** com a sua cadeia de ligação. O código abaixo recupera a cadeia de ligação para o recurso a partir de uma variável ambiental chamada `COMMUNICATION_SERVICES_CONNECTION_STRING` . Saiba como gerir a [cadeia de ligação dos recursos.](../../create-communication-resource.md#store-your-connection-string)

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.getenv('COMMUNICATION_SERVICES_CONNECTION_STRING')

# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(connection_string)
```

## <a name="send-an-sms-message"></a>Enviar uma mensagem SMS

Envie uma mensagem SMS chamando o método Enviar. Adicione este código ao fim do `try` bloco em **send-sms.py:**

```python

# calling send() with sms values
sms_response = sms_client.send(
        from_phone_number=PhoneNumber("<leased-phone-number>"),
        to_phone_numbers=[PhoneNumber("<to-phone-number>")],
        message="Hello World via SMS",
        send_sms_options=SendSmsOptions(enable_delivery_report=True)) # optional property

```

Deverá substituir `<leased-phone-number>` por um número de telefone por SMS associado ao seu serviço de comunicação e `<to-phone-number>` pelo número de telefone a que deseja enviar uma mensagem. 

O `send_sms_options` parâmetro é um parâmetro opcional que pode utilizar para configurar relatórios de entrega. Isto é útil para cenários em que pretende emitir eventos quando as mensagens SMS são entregues. Consulte o [Punho SMS Events](../handle-sms-events.md) quickstart para configurar relatórios de entrega para as suas mensagens SMS.

## <a name="run-the-code"></a>Executar o código

Executar o pedido do seu diretório de candidaturas com o `python` comando.

```console
python send-sms.py
```
