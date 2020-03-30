---
title: Atividade webhook na Fábrica de Dados Azure
description: A atividade do webhook não continua a executar o pipeline até validar o conjunto de dados anexo com determinados critérios que o utilizador especifica.
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
ms.openlocfilehash: ced2279878ee2eb361ec7338647418658e411513
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213008"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Atividade webhook na Fábrica de Dados Azure

Uma atividade de webhook pode controlar a execução de gasodutos através do seu código personalizado. Com a atividade do webhook, o código dos clientes pode ligar para um ponto final e passar-lhe um URL de callback. A execução do gasoduto aguarda a invocação do regresso antes de passar para a próxima atividade.

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
**nome** | O nome da atividade do webhook. | Cadeia | Sim |
**tipo** | Deve ser definido para "WebHook". | Cadeia | Sim |
**método** | O método REST API para o ponto final do alvo. | Cadeia. O tipo suportado é "POST". | Sim |
**url** | O ponto final e o caminho do alvo. | Uma corda ou uma expressão com o **valor do tipo de resultado** de uma corda. | Sim |
**cabeçalhos** | Cabeçalhos que são enviados para o pedido. Aqui está um exemplo que define a linguagem `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`e escreve num pedido: . | Uma corda ou uma expressão com o **valor do tipo de resultado** de uma corda. | Sim. Um `Content-Type` cabeceamento `"headers":{ "Content-Type":"application/json"}` como é necessário. |
**corpo** | Representa a carga útil que é enviada para o ponto final. | JSON válido ou uma expressão com o **valor tipo de json** resultado. Consulte solicitar esquema de [carga útil](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#request-payload-schema) para o esquema da carga útil do pedido. | Sim |
**autenticação** | O método de autenticação usado para chamar o ponto final. Os tipos suportados são "Básico" e "Certificado de Cliente". Para obter mais informações, veja [Autenticação](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#authentication). Se a autenticação não for necessária, exclua esta propriedade. | Uma corda ou uma expressão com o **valor do tipo de resultado** de uma corda. | Não |
**tempo limite** | Quanto tempo a atividade aguarda a chamada especificada pela **callBackUri** para ser invocada. O valor predefinido é de 10 minutos ("00:10:00"). Os valores têm o formato TimeSpan *d*. *hh:**mm*:*ss*. | Cadeia | Não |
**Estado do relatório no callback** | Permite que um utilizador reporte o estado falhado de uma atividade de webhook. | Booleano | Não |

## <a name="authentication"></a>Autenticação

Uma atividade de webhook suporta os seguintes tipos de autenticação.

### <a name="none"></a>Nenhuma

Se a autenticação não for necessária, não inclua a propriedade de **autenticação.**

### <a name="basic"></a>Básico

Especifique o nome de utilizador e a palavra-passe para utilizar com a autenticação básica.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certificado de cliente

Especifique o conteúdo codificado base64 de um ficheiro PFX e uma palavra-passe.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Identidade gerida

Utilize a identidade gerida pela fábrica de dados para especificar o recurso URI para o qual o token de acesso é solicitado. Para ligar para a API `https://management.azure.com/`de Gestão de Recursos Azure, utilize. Para obter mais informações sobre como funcionam as identidades geridas, consulte as identidades geridas para a visão geral dos [recursos do Azure.](/azure/active-directory/managed-identities-azure-resources/overview)

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Se a sua fábrica de dados estiver configurada com um repositório Git, deve armazenar as suas credenciais no Cofre de Chaves Azure para utilizar a autenticação básica ou de certificado de cliente. A Azure Data Factory não armazena senhas em Git.

## <a name="additional-notes"></a>Notas adicionais

Data Factory passa a propriedade adicional **callBackUri** no corpo enviado para o ponto final url. Data Factory espera que este URI seja invocado antes do valor limite especificado. Se o URI não for invocado, a atividade falha com o estatuto "TimedOut".

A atividade do webhook falha quando a chamada para o ponto final personalizado falha. Qualquer mensagem de erro pode ser adicionada ao corpo de chamada e usada numa atividade posterior.

Para cada chamada rest API, o cliente horário se o ponto final não responder dentro de um minuto. Este comportamento é as melhores práticas padrão http. Para resolver este problema, implemente um padrão de 202. No caso em apreço, o ponto final devolve 202 (Aceito) e as sondagens dos clientes.

O intervalo de um minuto no pedido não tem nada a ver com o tempo limite de atividade. Este último é utilizado para esperar pelo backback especificado pela **callbackUri**.

O corpo passado de volta para o backback URI deve ser válido JSON. Coloque `Content-Type` o `application/json`cabeçalho para .

Quando utilizar o estado do Relatório na propriedade **de callback,** deve adicionar o seguinte código ao corpo quando efizer a chamada:

```json
{
    "Output": {
        // output object is used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity is marked as failed
}
```

## <a name="next-steps"></a>Passos seguintes

Consulte as seguintes atividades de fluxo de controlo suportadas pela Data Factory:

- [Atividade Se Condição](control-flow-if-condition-activity.md)
- [Atividade Executar Pipeline](control-flow-execute-pipeline-activity.md)
- [Para Cada Atividade](control-flow-for-each-activity.md)
- [Atividade Obter Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade Web](control-flow-web-activity.md)
- [Atividade Until](control-flow-until-activity.md)
