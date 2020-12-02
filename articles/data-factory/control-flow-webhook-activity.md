---
title: Atividade webhook na Azure Data Factory
description: A atividade webhook não continua a execução do pipeline até validar o conjunto de dados anexado com determinados critérios que o utilizador especifica.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 144006c3d0722bc3211f542b7059bba0bb0cbdbf
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499412"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Atividade webhook na Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Uma atividade webhook pode controlar a execução de oleodutos através do seu código personalizado. Com a atividade webhook, o código dos clientes pode chamar um ponto final e passar-lhe um URL de retorno de chamada. O gasoduto aguarda a invocação de retorno antes de passar para a próxima atividade.

## <a name="syntax"></a>Syntax

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

## <a name="type-properties"></a>Tipo de propriedades

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
**nome** | O nome da atividade webhook. | String | Sim |
**tipo** | Deve ser definido como "WebHook". | String | Sim |
**método** | O método REST API para o ponto final alvo. | Cadeia. O tipo suportado é "POST". | Sim |
**url** | O ponto final e o caminho do alvo. | Uma corda ou uma expressão com o **resultado Valor** de um fio. | Sim |
**cabeçalhos** | Cabeçalhos que são enviados para o pedido. Aqui está um exemplo que define o idioma e o tipo num pedido: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }` . | Uma corda ou uma expressão com o **resultado Valor** de um fio. | Sim. Um `Content-Type` cabeçalho como `"headers":{ "Content-Type":"application/json"}` é necessário. |
**corpo** | Representa a carga útil que é enviada para o ponto final. | JSON válido ou uma expressão com o **resultado Valor de** Tipo de JSON. Consulte [o esquema de carga útil do Pedido](./control-flow-web-activity.md#request-payload-schema) para o esquema da carga útil do pedido. | Sim |
**autenticação** | O método de autenticação usado para chamar o ponto final. Os tipos suportados são "Básico" e "ClientCertificate". Para obter mais informações, veja [Autenticação](./control-flow-web-activity.md#authentication). Se a autenticação não for necessária, exclua esta propriedade. | Uma corda ou uma expressão com o **resultado Valor** de um fio. | Não |
**tempo limite** | Quanto tempo a atividade aguarda que a chamada especificada pelo **callBackUri** seja invocada. O valor predefinido é de 10 minutos ("00:10:00"). Os valores têm o formato TimeSpan *d*. *hh*:*mm*:*ss*. | String | Não |
**Estado do relatório sobre o retorno** | Permite que um utilizador reporte o estado falhado de uma atividade webhook. | Booleano | Não |

## <a name="authentication"></a>Autenticação

Uma atividade webhook suporta os seguintes tipos de autenticação.

### <a name="none"></a>Nenhum

Se a autenticação não for necessária, não inclua o imóvel **de autenticação.**

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

Utilize a identidade gerida da fábrica de dados para especificar o recurso URI para o qual o token de acesso é solicitado. Para ligar para a API de Gestão de Recursos Azure, utilize `https://management.azure.com/` . Para obter mais informações sobre como funcionam as identidades geridas, consulte [as identidades geridas para a visão geral dos recursos Azure](../active-directory/managed-identities-azure-resources/overview.md).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Se a sua fábrica de dados estiver configurada com um repositório Git, deve armazenar as suas credenciais no Cofre da Chave Azure para utilizar a autenticação básica ou de certificado de cliente. A Azure Data Factory não armazena senhas em Git.

## <a name="additional-notes"></a>Notas adicionais

Data Factory passa a chamada adicional **de propriedadeBackUri** no corpo enviado para o ponto final URL. A Data Factory espera que este URI seja invocado antes do valor de tempo limite especificado. Se o URI não for invocado, a atividade falha com o estado "TimedOut".

A atividade webhook falha quando a chamada para o ponto final personalizado falha. Qualquer mensagem de erro pode ser adicionada ao corpo de retorno e usada numa atividade posterior.

Para cada chamada da API REST, o cliente termina se o ponto final não responder dentro de um minuto. Este comportamento é a melhor prática padrão HTTP. Para resolver este problema, implementar um padrão de 202. No caso em atualmente, o ponto final devolve 202 (Aceitos) e as sondagens dos clientes.

O tempo limite de um minuto no pedido não tem nada a ver com o tempo limite de atividade. Este último é utilizado para aguardar a chamada especificada por **callbackUri**.

O corpo passado de volta para o URI de retorno deve ser válido JSON. Desa estale o `Content-Type` cabeçalho para `application/json` .

Quando utilizar o estado do Relatório na propriedade **de retorno,** deve adicionar o seguinte código ao corpo quando fizer a chamada:

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
- [Executar a Atividade do Pipeline](control-flow-execute-pipeline-activity.md)
- [Para Cada Atividade](control-flow-for-each-activity.md)
- [Obter Atividade de Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade Web](control-flow-web-activity.md)
- [Até a Atividade](control-flow-until-activity.md)