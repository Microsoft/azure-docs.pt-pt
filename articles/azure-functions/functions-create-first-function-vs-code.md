---
title: Criar a sua primeira função no Azure com o Visual Studio Code
description: Criar e publicar no Azure uma simples função ativada por HTTP com a extensão das Funções do Azure no Visual Studio Code.
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 0540c7b01d693975f34515c7d13f0477ac74d4a1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842261"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>Quickstart: Criar um projeto de funções Azure usando o Código de Estúdio Visual

Neste artigo, utiliza o Código do Estúdio Visual para criar uma função que responda aos pedidos do HTTP. Depois de testar o código localmente, implementa-o para o ambiente sem servidor estoirar das Funções Azure. Completar este quickstart incorre num pequeno custo de alguns cêntimos de USD ou menos na sua conta Azure. 

Há também uma [versão baseada em CLI](functions-create-first-azure-function-azure-cli.md) deste artigo.

## <a name="prerequisites"></a>Pré-requisitos

+ [Código de Estúdio Visual](https://code.visualstudio.com/) numa das [plataformas suportadas.](https://code.visualstudio.com/docs/supporting/requirements#_platforms) 
::: zone pivot="programming-language-csharp"
+ A [ C# extensão](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) para Visual Studio Code.
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js,](https://nodejs.org/)itldes ative LTS e manutenção LTS (8.11.1 e 10.14.1 recomendados).
::: zone-end
::: zone pivot="programming-language-python"
+ [Python 3.7](https://www.python.org/downloads/release/python-375/) ou [Python 3.6,](https://www.python.org/downloads/release/python-368/)que apoiado por Funções Azure. Python 3.8 ainda não foi apoiado. 

+ A [extensão Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) para Visual Studio Code.
::: zone-end
::: zone pivot="programming-language-powershell"
+ [Núcleo PowerShell](/powershell/scripting/install/installing-powershell-core-on-windows)

+ O [Núcleo .NET SDK 2.2+](https://www.microsoft.com/net/download)

+ A [extensão PowerShell para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell). 
::: zone-end

+ A [extensão de funções Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Código de Estúdio Visual. 

+ Uma [conta Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) com uma subscrição ativa. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="create-an-azure-functions-project"></a>Crie o seu projeto local 

Nesta secção, você usa o Código de Estúdio Visual para criar um projeto local de Funções Azure no seu idioma escolhido. Mais tarde neste artigo, publicará o seu código de função ao Azure. 

1. Escolha o ícone Azure na barra de Atividades, em seguida, na área **Azure: Funções,** selecione o **novo projeto Criar...** ícone.

    ![Escolha Criar um novo projeto](media/functions-create-first-function-vs-code/create-new-project.png)

1. Escolha um local de diretório para o espaço de trabalho do projeto e escolha **selecionar**.

    > [!NOTE]
    > Essas etapas foram projetadas para serem concluídas fora de um espaço de trabalho. Neste caso, não selecione uma pasta de projeto que faz parte de uma área de trabalho.

1. Forneça as seguintes informações nas instruções:

    ::: zone pivot="programming-language-csharp"

    | Mensagem | Valor | 
    | ------ | ----- | 
    | Selecione um idioma para o seu projeto de função | C# |
    | selecionar uma versão | Funções Azure v2 | 
    | Selecione um modelo para a primeira função do seu projeto | Acionador HTTP | 
    | Fornecer um nome de função | HttpExample | 
    | Fornecer um espaço de nome | As minhas.funções | 
    | Nível de autorização | Anónimo | 
    | Selecione como pretende abrir o seu projeto | Adicione ao espaço de trabalho |

    ::: zone-end

    ::: zone pivot="programming-language-javascript"

    | Mensagem | Valor | 
    | ------ | ----- | 
    | Selecione um idioma para o seu projeto de função | Javascript | 
    | selecionar uma versão | Funções Azure v2 | 
    | Selecione um modelo para a primeira função do seu projeto | Acionador HTTP | 
    | Fornecer um nome de função | HttpExample | 
    | Nível de autorização | Anónimo | 
    | Selecione como pretende abrir o seu projeto | Adicione ao espaço de trabalho |

    ::: zone-end

    ::: zone pivot="programming-language-typescript"

    | Mensagem | Valor | 
    | ------ | ----- | 
    | Selecione um idioma para o seu projeto de função | TypeScript | 
    | selecionar uma versão | Funções Azure v2 | 
    | Selecione um modelo para a primeira função do seu projeto | Acionador HTTP | 
    | Fornecer um nome de função | HttpExample | 
    | Nível de autorização | Anónimo | 
    | Selecione como pretende abrir o seu projeto | Adicione ao espaço de trabalho |

    ::: zone-end

    ::: zone pivot="programming-language-powershell"

    | Mensagem | Valor | 
    | ------ | ----- | 
    | Selecione um idioma para o seu projeto de função | PowerShell | 
    | selecionar uma versão | Funções Azure v2 | 
    | Selecione um modelo para a primeira função do seu projeto | Acionador HTTP | 
    | Fornecer um nome de função | HttpExample | 
    | Nível de autorização | Anónimo | 
    | Selecione como pretende abrir o seu projeto | Adicione ao espaço de trabalho |

    ::: zone-end

    ::: zone pivot="programming-language-python"

    | Mensagem | Valor | 
    | ------ | ----- | 
    | Selecione um idioma para o seu projeto de função | Python | 
    | selecionar uma versão | Funções Azure v2 | 
    | Selecione um pseudónimo Python para criar um ambiente virtual | Pseudónimo python | 
    | Selecione um modelo para a primeira função do seu projeto | Acionador HTTP | 
    | Fornecer um nome de função | HttpExample | 
    | Nível de autorização | Anónimo | 
    | Selecione como pretende abrir o seu projeto | Adicione ao espaço de trabalho | 

    ::: zone-end

1. Visual Studio Code faz o seguinte:

    + Cria um projeto de Funções Azure num novo espaço de trabalho, que contém os ficheiros de configuração [host.json](functions-host-json.md) e [local.settings.json.](functions-run-local.md#local-settings-file) 

    ::: zone pivot="programming-language-csharp"

    + Cria um [ficheiro de biblioteca de classe HttpExample.cs](functions-dotnet-class-library.md#functions-class-library-project) que implementa a função.

    ::: zone-end
        
    ::: zone pivot="programming-language-javascript"
    
    + Cria um ficheiro pacote.json na pasta raiz.

    + Cria uma pasta HttpExample que contém o ficheiro de [definição function.json](functions-reference-node.md#folder-structure) e o [ficheiro index.js,](functions-reference-node.md#exporting-a-function)um ficheiro Node.js que contém o código de função.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-typescript"
    
    + Cria um ficheiro package.json e um ficheiro tsconfig.json na pasta raiz.

    + Cria uma pasta HttpExample que contém o ficheiro de [definição function.json](functions-reference-node.md#folder-structure) e o [ficheiro index.ts,](functions-reference-node.md#typescript)um ficheiro TypeScript que contém o código de função.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-powershell"
    
    + Cria uma pasta HttpExample que contém o ficheiro de [definição function.json](functions-reference-python.md#programming-model) e o ficheiro run.ps1, que contém o código de função.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-python"
    
    + Cria um ficheiro de requisitos de nível de projeto.txt que lista os pacotes exigidos pelas Funções.
    
    + Cria uma pasta HttpExample que contém o ficheiro de [definição function.json](functions-reference-python.md#programming-model) e o \_\_inite\_ficheiro \_.py, que contém o código de função.
    
    ::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Depois de verificar que a função funciona corretamente no seu computador local, é hora de usar o Código do Estúdio Visual para publicar o projeto diretamente para o Azure. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Executar a função em Azure

1. Copie o URL do acionador de HTTP no painel **Resultados**. Mais uma vez, adicione a cadeia de consulta `name` como `?name=<yourname>` ao final deste URL e execute o pedido.

    O URL que chama a função acionada por HTTP deve estar no seguinte formato:

        http://<functionappname>.azurewebsites.net/api/httpexample?name=<yourname> 

1. Cole este novo URL do pedido HTTP na barra de endereço do browser. O exemplo seguinte mostra a resposta no navegador ao pedido REMOTO GET devolvido pela função: 

    ![Resposta da função no browser](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando continuar até ao próximo passo, Adicione uma fila de [armazenamento Azure ligando à sua função,](functions-add-output-binding-storage-queue-vs-code.md)terá de manter todos os seus recursos no lugar para basear no que já fez.

Caso contrário, pode utilizar os seguintes passos para eliminar a aplicação de funções e os seus recursos conexos para evitar incorrer em custos adicionais.

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

Para saber mais sobre os custos das Funções, consulte [estimar os custos do plano de consumo.](functions-consumption-costs.md)

## <a name="next-steps"></a>Passos seguintes

Utilizou o Visual Studio Code para criar uma aplicação de funções com uma função simples acionada por HTTP. No artigo seguinte, expande essa função adicionando uma ligação de saída. Esta ligação escreve o fio do pedido HTTP para uma mensagem numa fila de armazenamento de fila Azure. 

> [!div class="nextstepaction"]
> [Adicione uma ligação de fila de armazenamento Azure à sua função](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
