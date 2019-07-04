---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2018
ms.author: glenga
ms.openlocfilehash: f9b853f0e86fc298ab35421928492ba813d85827
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444591"
---
## <a name="create-an-azure-functions-project"></a>Criar o seu projeto de funções com uma função 

O modelo do projeto das Funções do Azure no Visual Studio Code cria um projeto que pode ser publicado numa aplicação de funções no Azure. As aplicações de funções permitem-lhe agrupar funções como unidades lógicas para a gestão, implementação e partilha de recursos.

1. No Visual Studio Code, prima F1 para abrir a paleta de comandos. Na paleta de comandos, procure e selecione `Azure Functions: Create new project...`.

1. Escolha uma localização do diretório para a área de trabalho do projeto e escolha **selecione**.

    > [!NOTE]
    > Estes passos foram projetados para conclusão fora de uma área de trabalho. Neste caso, não selecione uma pasta de projeto que faz parte de uma área de trabalho.

1. Siga as instruções, forneça as seguintes informações:

    | Mensagem | Value | Descrição |
    | ------ | ----- | ----------- |
    | Selecione um idioma para o seu projeto de aplicação de função | C# ou JavaScript | Este artigo oferece suporte a C# e JavaScript. Para o Python, veja [neste artigo de Python](https://code.visualstudio.com/docs/python/tutorial-azure-functions)e para o PowerShell, veja [este artigo do PowerShell](../articles/azure-functions/functions-create-first-function-powershell.md).  |
    | Selecione um modelo para a primeira função do seu projeto | Acionador HTTP | Crie uma função de acionada por HTTP na nova aplicação de funções. |
    | Forneça um nome de função | HttpTrigger | Prima Enter para utilizar o nome predefinido. |
    | Forneça um espaço de nomes | My.Functions | (C# apenas) C# bibliotecas de classes tem de ter um espaço de nomes.  |
    | Nível de autorização | Função | Requer uma [tecla de função](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) para chamar o ponto final de HTTP da função. |
    | Selecione como pretende abrir seu projeto | Adicionar área de trabalho | Cria a aplicação de funções na área de trabalho atual. |

O Visual Studio Code cria o projeto da aplicação de funções numa nova área de trabalho. Este projeto contém os ficheiros de configuração [host.json](../articles/azure-functions/functions-host-json.md) e [local.settings.json](../articles/azure-functions/functions-run-local.md#local-settings-file), bem como os ficheiros de projetos específico de idiomas. 

Também é criada uma nova função de acionada por HTTP na pasta HttpTrigger do projeto de aplicação de função.