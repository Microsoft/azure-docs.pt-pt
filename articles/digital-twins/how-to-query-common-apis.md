---
title: Padrões de consulta comuns do Azure digital gêmeos | Microsoft Docs
description: Aprenda padrões comuns de consulta das APIs de gerenciamento de gêmeos digitais do Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/27/2019
ms.openlocfilehash: ef9e7a41f1de05e4d21a9ad7b45ed2b99885b6b8
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044444"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>Como consultar as APIs do Azure digital gêmeos para tarefas comuns

Este artigo mostra padrões de consulta para ajudá-lo a executar cenários comuns para sua instância do gêmeos digital do Azure. Isso pressupõe que sua instância de gêmeos digital já está em execução. Você pode usar qualquer cliente REST, como o postmaster. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>Consultas de espaços e tipos

Esta seção mostra as consultas de exemplo para obter mais informações sobre os espaços provisionados. Faça solicitações HTTP GET autenticadas com as consultas de exemplo, substituindo os espaços reservados por valores da sua configuração. 

- Obter espaços que são nós raiz.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- Obtenha um espaço por nome e inclua dispositivos, sensores, valores computados e valores de sensor. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- Obtenha espaços e suas informações de dispositivo/sensor, cujo pai é a ID de espaço fornecida e que estão nos níveis de dois a cinco [em relação ao espaço determinado](how-to-navigate-apis.md#api-navigation). 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- Obtenha o espaço com a ID fornecida e inclua valores calculados e de sensor.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- Obter chaves de propriedade para um espaço específico.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- Obtenha espaços com a chave de propriedade chamada *AreaInSqMeters* e seu valor é 30. Você também pode fazer operações de cadeia de caracteres, por exemplo, obter espaços contendo a chave de propriedade com `name = X contains Y`.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- Obter todos os nomes com *temperatura* de nome e dependências associadas e ontologies.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>Consultas de funções e atribuições de função

Esta seção mostra algumas consultas para obter mais informações sobre funções e suas atribuições. 

- Obtenha todas as funções com suporte no Azure digital gêmeos.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Obtenha todas as atribuições de função em sua instância de gêmeos digital. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- Obter atribuições de função em um caminho específico.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>Consultas para dispositivos

Esta seção mostra alguns exemplos de como você pode usar as APIs de gerenciamento para obter informações específicas sobre seus dispositivos. Todas as chamadas à API precisam ser autenticadas para obter solicitações HTTP.

- Obter todos os dispositivos.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- Localizar todos os status do dispositivo.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- Obter um dispositivo específico.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- Obter todos os dispositivos anexados ao espaço raiz.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- Obtenha todos os dispositivos conectados a espaços nos níveis 2 a 4.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- Obter todos os dispositivos diretamente anexados a uma ID de espaço específica.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- Obter todos os dispositivos anexados a um espaço específico e seus descendentes.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- Obter todos os dispositivos anexados a descendentes de um espaço, excluindo esse espaço.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- Obter todos os dispositivos anexados a filhos diretos de um espaço.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- Obter todos os dispositivos conectados a um dos ancestrais de um espaço.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- Obtenha todos os dispositivos anexados a descendentes de um espaço que sejam de nível menor ou igual a 5.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- Obtenha todos os dispositivos conectados a espaços que estejam no mesmo nível que o espaço com a ID *YOUR_SPACE_ID*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- Obtenha a cadeia de conexão do dispositivo do Hub IoT para seu dispositivo.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- Obtenha o dispositivo com a ID de hardware fornecida, incluindo sensores anexados.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- Obter sensores para tipos de dados específicos, nesse caso, *movimento* e *temperatura*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>Consultas para correspondências e funções definidas pelo usuário 

- Obtenha todos os correspondentes provisionados e suas IDs.

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- Obtenha detalhes sobre um correspondente específico, incluindo os espaços e a função definida pelo usuário associados a ele.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- Avalie uma correspondência em relação a um sensor e habilite o registro em log para fins de depuração. O retorno dessa mensagem HTTP GET informa se o correspondente e o sensor pertencem ao tipo de dados. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- Obtenha a ID das funções definidas pelo usuário. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- Obter o conteúdo de uma função específica definida pelo usuário 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>Consultas para usuários

Esta seção mostra algumas consultas de API de exemplo para gerenciar usuários no Azure digital gêmeos. Faça uma solicitação HTTP GET substituindo os espaços reservados pelos valores da sua configuração. 

- Obter todos os usuários. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- Obter um usuário específico.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>Passos seguintes

Para saber como autenticar com sua API de gerenciamento, leia [Autenticando com APIs](./security-authenticating-apis.md).

Para saber mais sobre seus pontos de extremidade de API, leia [como usar o Swagger digital gêmeos](./how-to-use-swagger.md).
