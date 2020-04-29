---
title: incluir ficheiro
description: incluir ficheiro
ms.topic: include
ms.date: 01/23/2020
ms.openlocfilehash: a7f4e0b47b3aaf9924f8c82eb6a9fa37decc0b2a
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76772363"
---
### <a name="create-an-environment-variable"></a>Criar uma variável ambiental

Utilizando a sua chave de tempo de execução e o ponto final do tempo de execução, crie variáveis ambientais para autenticação e acesso:

* `LUIS_RUNTIME_KEY`- A chave de recursos de tempo de execução para autenticar os seus pedidos.
* `LUIS_RUNTIME_ENDPOINT`- O ponto final do tempo de execução associado à sua chave.
* `LUIS_APP_ID`- O ID da aplicação `df67dcdb-c37d-46af-88e1-8b97951ca1c2`LUIS IoT pública é .
* `LUIS_APP_SLOT_NAME` - `production`ou`staging`

Se pretende utilizar este quickstart para aceder à sua própria app, tem de tomar medidas adicionais:
* Crie a app e obtenha o ID da aplicação
* Atribuir a chave de tempo de execução da app no portal LUIS
* Teste o URL com o navegador, que pode aceder à aplicação

Utilize as instruções para o seu sistema operativo.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_RUNTIME_KEY <replace-with-your-resource-key>
setx LUIS_RUNTIME_ENDPOINT <replace-with-your-resource-endpoint>
setx LUIS_APP_ID <replace-with-your-app-id>
setx LUIS_APP_SLOT_NAME <replace-with-production-or-staging>
```

Depois de adicionar as variáveis ambientais, reinicie a janela da consola.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

Depois de adicionar as variáveis ambientais, corra `source ~/.bashrc` da janela da consola para tornar as alterações eficazes.

#### <a name="macos"></a>[macOS](#tab/unix)

Edite `.bash_profile`a sua , e adicione a variável ambiental:

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

Depois de adicionar as variáveis ambientais, corra `source .bash_profile` da janela da consola para tornar as alterações eficazes.

***
