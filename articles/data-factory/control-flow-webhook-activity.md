---
title: Atividade webhook na Fábrica de Dados Azure
description: A atividade webhook não continua a executar o gasoduto até validar o conjunto de dados anexo com determinados critérios que o utilizador especifica.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 70c67a99274eaedc5592c7b90b1ef80a3a17acf8
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109997"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Atividade webhook na Fábrica de Dados Azure
Pode utilizar uma atividade de webhook para controlar a execução de gasodutos através do seu código personalizado. Utilizando a atividade do webhook, os clientes podem ligar para um ponto final e passar um URL de callback. A execução do gasoduto aguarda que a chamada seja invocada antes de prosseguir para a próxima atividade.

## <a name="syntax"></a>Sintaxe

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```


## <a name="type-properties"></a>Propriedades de tipo



Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
nome | Nome da atividade do gancho web | Cadeia | Sim |
tipo | Deve ser definido para **WebHook**. | Cadeia | Sim |
método | Método API de repouso para o ponto final alvo. | Cadeia. Tipos suportados: 'POST' | Sim |
url | Ponto final e caminho | Corda (ou expressão com resultadoTipo de corda). | Sim |
conector | Cabeçalhos que são enviados para o pedido. Por exemplo, para definir o idioma e escrever num pedido: "cabeçalhos" : {"Aceitar-Linguagem": "en-us", "Content-Type": "application/json" }. | Cadeia de caracteres (ou expressão com ResultType de cadeia de caracteres) | Sim, é necessário um cabeçalho do tipo conteúdo. "headers":{ "Content-Type":"application/json"} |
corpo | Representa a carga útil que é enviada para o ponto final. | JSON válido (ou expressão com resultadoType of JSON). Consulte o esquema da carga útil do pedido na secção esquema de [carga útil solicitação.](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0) | Sim |
autenticação | Método de autenticação utilizado para chamar o ponto final. Os tipos suportados são "Básicos" ou "Certificado de Cliente". Para mais informações, consulte a secção [autenticação.](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0) Se não for necessária a autenticação, exclua este imóvel. | Cadeia de caracteres (ou expressão com ResultType de cadeia de caracteres) | Não |
tempo limite | Quanto tempo a atividade &#39;vai esperar&#39; que a chamadaBackUri seja invocada. Quanto tempo a atividade vai esperar que o 'callBackUri' seja invocado. O valor predefinido é de 10mins ("00:10:00"). Formato é Timespan i.e. d.hh:mm:ss | Cadeia | Não |
Estado do relatório no callback | Permite ao utilizador o relatório o estado falhado da atividade do webhook que marcará a atividade como falhada | Booleano | Não |

## <a name="authentication"></a>Autenticação

Abaixo estão os tipos de autenticação suportados na atividade do webhook.

### <a name="none"></a>Nenhuma

Se não for necessária a autenticação, não inclua o imóvel de "autenticação".

### <a name="basic"></a>Básica

Especifique o nome do utilizador e a palavra-passe para usar com a autenticação básica.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certificado de cliente

Especifique o conteúdo codificado base64 de um ficheiro PFX e a palavra-passe.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Identidade Gerida

Especifique o uri de recurso para o qual o token de acesso será solicitado utilizando a identidade gerida para a fábrica de dados. Para ligar para a API de Gestão de Recursos Azure, use `https://management.azure.com/`. Para obter mais informações sobre como funcionam as identidades geridas, consulte as identidades geridas para a página geral dos [recursos do Azure.](/azure/active-directory/managed-identities-azure-resources/overview)

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Se a sua fábrica de dados estiver configurada com um repositório git, deve armazenar as suas credenciais no Cofre de Chaves Azure para utilizar a autenticação básica ou de certificado de cliente. A Azure Data Factory não armazena senhas em git.

## <a name="additional-notes"></a>Notas adicionais

A Azure Data Factory passará uma propriedade adicional "callBackUri" no corpo até ao ponto final do url, e espera que este uri seja invocado antes do valor limite especificado. Se o uri não for invocado, a atividade falhará com o estado 'TimedOut'.

A própria atividade do webhook falha quando a chamada para o ponto final personalizado falha. Qualquer mensagem de erro pode ser adicionada no corpo da chamada e usada numa atividade subsequente.

O corpo passado de volta para o callback URI deve ser válido JSON. Tem de definir o cabeçalho do Tipo conteúdo para `application/json`.

Quando utilizar a opção "Reportar no backback", deve adicionar o seguinte corte ao corpo ao efazer a chamada:

```
{
    "Output": {
        // output object will be used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity will be marked as failed
}
```



## <a name="next-steps"></a>Passos seguintes

Consulte outras atividades de fluxo de controlo suportadas pela Data Factory:

- [Atividade Se Condição](control-flow-if-condition-activity.md)
- [Atividade Executar Pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada Atividade](control-flow-for-each-activity.md)
- [Atividade Obter Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade Web](control-flow-web-activity.md)
- [Atividade Until](control-flow-until-activity.md)
