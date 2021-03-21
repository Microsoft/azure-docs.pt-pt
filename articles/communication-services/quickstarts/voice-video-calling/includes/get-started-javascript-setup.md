---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 33212f14faa0e7c201c13474af981790c01284f3
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "104598826"
---
## <a name="setting-up"></a>Configuração

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Abra o seu terminal ou janela de comando crie um novo diretório para a sua aplicação e navegue até ele.

```console
mkdir calling-quickstart && cd calling-quickstart
```

Corra `npm init -y` para criar umapackage.js **no** ficheiro com definições predefinidos.

```console
npm init -y
```

### <a name="install-the-package"></a>Instale o pacote

Utilize o `npm install` comando para instalar a biblioteca de clientes Azure Communication Services Call para JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Recomenda-se as seguintes versões de webpack para este arranque rápido:

```console
"webpack": "^4.42.0",
"webpack-cli": "^3.3.11",
"webpack-dev-server": "^3.10.3"
```

A `--save` opção lista a biblioteca como uma dependência do seu **package.jsarquivado.**

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Este quickstart usa webpack para agregar os ativos da aplicação. Executar o seguinte comando para instalar os pacotes npm webpack, webpack-cli e webpack-dev-server e listá-los como dependências de desenvolvimento no seu **package.jsem**:

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Crie um **ficheiroindex.html** no diretório de raiz do seu projeto. Usaremos este ficheiro para configurar um layout básico que permitirá ao utilizador fazer uma chamada.
