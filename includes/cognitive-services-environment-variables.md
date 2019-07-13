---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 6d6451d50a00569eb1da8f5b0a0dc10d3c6b1115
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841526"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Configurar uma variável de ambiente para autenticação

Os aplicativos precisam autenticar o acesso aos serviços cognitivos utilizam. Para se autenticar, recomendamos que crie uma variável de ambiente para armazenar as chaves para os seus recursos do Azure. 

Depois de ter a chave, escreva-a uma nova variável de ambiente no computador local executando o aplicativo. Para definir a variável de ambiente, abra uma janela da consola e siga as instruções relevantes para o seu sistema operativo. Substitua `your-key` com uma das chaves para o seu recurso.

* Windows

    ```console
    setx COGNITIVE_SERVICE_KEY "your-key"
    ```

    Depois de adicionar a variável de ambiente, poderá ter de reiniciar todos os programas em execução que irão precisar de ler a variável de ambiente, incluindo a janela da consola. Por exemplo, se estiver a utilizar o Visual Studio como editor, reinicie o Visual Studio antes de executar o exemplo.

* Linux
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela da consola para que as alterações entrem em vigor.
    
* macOS
    
    Edite o seu .bash_profile e adicione a variável de ambiente:
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela da consola para que as alterações entrem em vigor.
