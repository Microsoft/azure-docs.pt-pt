---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: c0ba8e76e069bf9dc1c96aecdc670699c32b3c96
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "90948160"
---
## <a name="object-model"></a>Modelo de objeto

Os Tokens de Acesso ao Utilizador permitem-lhe autorizar os utilizadores da sua aplicação a ligarem-se diretamente ao seu recurso Azure Communication Services. Gera estes tokens no seu servidor, passa-os de volta para a aplicação do seu cliente e, em seguida, utiliza-os para rubricar as bibliotecas de clientes dos Serviços de Comunicação. Para tal, deverá construir um ponto final de serviço de confiança que possa autenticar os seus utilizadores e emitir fichas de acesso ao utilizador. Este serviço deve manter um mapeamento persistente entre as identidades dos utilizadores da sua aplicação e os utilizadores dos Serviços de Comunicação Azure.

A classe CommunicationIdentityClient fornece todas as funcionalidades relacionadas com o utilizador e acesso. Você instantanea-lo com uma cadeia de ligação e, em seguida, usá-lo para gerir utilizadores, e emitir fichas de acesso.

> [!WARNING]
> Os tokens são dados sensíveis, porque concedem acesso aos recursos de um utilizador. Portanto, é fundamental proteger os tokens de serem comprometidos. Deverá criar um ponto final de serviço de confiança que apenas emite acesso a tokens a utilizadores autorizados da sua aplicação. Se cache fichas de acesso do utilizador a uma loja de suporte, é fortemente recomendado que utilize encriptação.

### <a name="access-token-scopes"></a>Acesso a âmbitos simbólicos

Os âmbitos permitem especificar a funcionalidade exata dos Serviços de Comunicações Azure que um token de acesso ao utilizador poderá autorizar. Os âmbitos são aplicados a fichas de acesso individuais ao utilizador. A Azure Communication Services suporta os seguintes âmbitos para os tokens de acesso ao utilizador:

| Nome   | Descrição                                                                         |
| ------ | ----------------------------------------------------------------------------------- |
| `Chat` | Concede a capacidade de participar de um chat                                         |
| `VoIP` | Concede a capacidade de fazer e receber chamadas VOIP utilizando a biblioteca do cliente chamador* |
| `Pstn` | Concede a capacidade de fazer chamadas PSTN utilizando a biblioteca do cliente chamador*           |

\* Recurso ainda não está suportado e estará disponível em breve

Se desejar remover a capacidade de acesso de um utilizador a alguma funcionalidade específica, deve primeiro [revogar quaisquer fichas de acesso existentes](#revoke-user-access-tokens) que possam incluir âmbitos não desejados antes de emitir um novo token com um conjunto de âmbitos mais limitado.
