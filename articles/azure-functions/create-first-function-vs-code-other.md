---
title: Criar uma função em Go ou Rust usando Código de Estúdio Visual - Funções Azure
description: Aprenda a criar uma função Go como manipulador personalizado Azure Functions e, em seguida, publique o projeto local para hospedagem sem servidor em Funções Azure utilizando a extensão de Funções Azure no Código do Estúdio Visual.
ms.topic: quickstart
ms.date: 12/4/2020
ms.openlocfilehash: a3ef7bdd19badf7a7deaa8376440016e39e0d14b
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809584"
---
# <a name="quickstart-create-a-go-or-rust-function-in-azure-using-visual-studio-code"></a>Quickstart: Criar uma função Go ou Rust em Azure usando Código de Estúdio Visual

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Neste artigo, utiliza o Código do Estúdio Visual para criar uma função [de manipulador personalizado](functions-custom-handlers.md) que responde aos pedidos HTTP. Depois de testar o código localmente, implemente-o para o ambiente sem servidor das Funções Azure.

Os manipuladores personalizados podem ser utilizados para criar funções em qualquer idioma ou tempo de execução, executando um processo de servidor HTTP. Este artigo apoia tanto [Go](create-first-function-vs-code-other.md?tabs=go) como [Rust.](create-first-function-vs-code-other.md?tabs=rust)

Completar este quickstart incorre num pequeno custo de alguns usd ou menos na sua conta Azure.

## <a name="configure-your-environment"></a>Configurar o ambiente

Antes de começar, certifique-se de que tem os seguintes requisitos em vigor:

# <a name="go"></a>[Ir](#tab/go)

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ [Visual Studio Code](https://code.visualstudio.com/) numa das [plataformas suportadas](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ A [extensão das Funções do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para o Visual Studio Code.

+ A versão 3.x [das Ferramentas Principais do Azure Functions.](./functions-run-local.md#v2) Utilize o `func --version` comando para verificar se está corretamente instalado.

+ [Vá,](https://golang.org/doc/install)versão mais recente recomendada. Utilize o `go version` comando para verificar a sua versão.

# <a name="rust"></a>[Rust](#tab/rust)

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ [Visual Studio Code](https://code.visualstudio.com/) numa das [plataformas suportadas](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ A [extensão das Funções do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para o Visual Studio Code.

+ A versão 3.x [das Ferramentas Principais do Azure Functions.](./functions-run-local.md#v2) Utilize o `func --version` comando para verificar se está corretamente instalado.

+ Cano de ferramentas de ferrugem utilizando [ferrugem](https://www.rust-lang.org/tools/install). Utilize o `rustc --version` comando para verificar a sua versão.

---

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Crie o seu projeto local

Nesta secção, você usa o Código do Estúdio Visual para criar um projeto local de manipuladores personalizados Azure Functions. Mais adiante neste artigo, irá publicar o seu código de função no Azure.

1. Selecione o ícone do Azure na barra de Atividade e, em seguida, na área **Azure: Funções**, selecione o ícone **Criar novo projeto...**.

    ![Selecione Criar um novo projeto](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Selecione uma localização do diretório para a área de trabalho do seu projeto e escolha **Selecionar**.

    > [!NOTE]
    > Estes passos foram concebidos para serem concluídos fora de um espaço de trabalho. Neste caso, não selecione uma pasta de projeto que faz parte de uma área de trabalho.

1. Forneça as seguintes informações nos pedidos:

    + **Selecione um idioma para o seu projeto de função**: Selecione `Custom`.

    + **Selecione um modelo para a primeira função do seu projeto**: Selecione `HTTP trigger`.

    + **Forneça um nome de função**: Escreva `HttpExample`.

    + **Nível de autorização**: `Anonymous` Escolha, que permite a qualquer pessoa ligar para o ponto final da sua função. Para saber mais sobre o nível de autorização, consulte [as teclas de Autorização](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Selecione como gostaria de abrir o seu projeto**: Selecione `Add to workspace`.

1. Utilizando esta informação, o Visual Studio Code gera um projeto Azure Functions com uma função de gatilho HTTP. Pode ver os ficheiros de projeto locais no Explorer. Para saber mais sobre ficheiros que são criados, consulte [os ficheiros do projeto Gerados.](functions-develop-vs-code.md#generated-project-files) 

## <a name="create-and-build-your-function"></a>Criar e construir a sua função

A *function.jsficheiro na* pasta *HttpExample* declara uma função de gatilho HTTP. Completa-se a função adicionando um manipulador e compilando-a num executável.

# <a name="go"></a>[Ir](#tab/go)

1. Prima <kbd>Ctrl + N</kbd> <kbd>(Cmd + N</kbd> no macOS) para criar um novo ficheiro. Guarde-o como *handler.go* na raiz da aplicação de função (na mesma pasta *quehost.js).*

1. Em *handler.go*, adicione o seguinte código e guarde o ficheiro. Este é o seu manipulador personalizado Go.

    ```go
    package main

    import (
        "fmt"
        "log"
        "net/http"
        "os"
    )

    func helloHandler(w http.ResponseWriter, r *http.Request) {
        message := "This HTTP triggered function executed successfully. Pass a name in the query string for a personalized response.\n"
        name := r.URL.Query().Get("name")
        if name != "" {
            message = fmt.Sprintf("Hello, %s. This HTTP triggered function executed successfully.\n", name)
        }
        fmt.Fprint(w, message)
    }

    func main() {
        listenAddr := ":8080"
        if val, ok := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT"); ok {
            listenAddr = ":" + val
        }
        http.HandleFunc("/api/HttpExample", helloHandler)
        log.Printf("About to listen on %s. Go to https://127.0.0.1%s/", listenAddr, listenAddr)
        log.Fatal(http.ListenAndServe(listenAddr, nil))
    }
    ```

1. Prima <kbd>Ctrl + Shift + '</kbd> ou selecione Novo *Terminal* a partir do menu *Terminal* para abrir um novo terminal integrado no Código VS.

1. Compile o seu manipulador personalizado utilizando o seguinte comando. Um ficheiro executável denominado `handler` `handler.exe` (no Windows) é a saída na pasta raiz da aplicação de função.

    ```bash
    go build handler.go
    ```

    ![VS Code - Build Go personal handler](./media/functions-create-first-function-vs-code/functions-vscode-go.png)

# <a name="rust"></a>[Rust](#tab/rust)

1. Prima <kbd>Ctrl + Shift + '</kbd> ou selecione Novo *Terminal* a partir do menu *Terminal* para abrir um novo terminal integrado no Código VS.

1. Na raiz da aplicação de função (a mesma pasta *quehost.js),* inicialize um projeto Rust chamado `handler` .

    ```bash
    cargo init --name handler
    ```

1. Em *Cargo.toml,* adicione as seguintes dependências necessárias para completar este arranque rápido. O exemplo utiliza a estrutura do servidor web [de dobra.](https://docs.rs/warp/)

    ```toml
    [dependencies]
    warp = "0.2"
    tokio = { version = "0.2", features = ["full"] }
    ```

1. Em *src/main.rs,* adicione o seguinte código e guarde o ficheiro. Este é o seu manipulador personalizado Rust.

    ```rust
    use std::collections::HashMap;
    use std::env;
    use std::net::Ipv4Addr;
    use warp::{http::Response, Filter};

    #[tokio::main]
    async fn main() {
        let example1 = warp::get()
            .and(warp::path("api"))
            .and(warp::path("HttpExample"))
            .and(warp::query::<HashMap<String, String>>())
            .map(|p: HashMap<String, String>| match p.get("name") {
                Some(name) => Response::builder().body(format!("Hello, {}. This HTTP triggered function executed successfully.", name)),
                None => Response::builder().body(String::from("This HTTP triggered function executed successfully. Pass a name in the query string for a personalized response.")),
            });

        let port_key = "FUNCTIONS_CUSTOMHANDLER_PORT";
        let port: u16 = match env::var(port_key) {
            Ok(val) => val.parse().expect("Custom Handler port is not a number!"),
            Err(_) => 3000,
        };

        warp::serve(example1).run((Ipv4Addr::UNSPECIFIED, port)).await
    }
    ```

1. Compilar um binário para o seu manipulador personalizado. Um ficheiro executável denominado `handler` `handler.exe` (no Windows) é a saída na pasta raiz da aplicação de função.

    ```bash
    cargo build --release
    cp target/release/handler .
    ```

    ![VS Code - Construa o manipulador personalizado Rust](./media/functions-create-first-function-vs-code/functions-vscode-rust.png)

---

## <a name="configure-your-function-app"></a>Configure a sua aplicação de função

O anfitrião da função precisa de ser configurado para executar o binário do seu manipulador habitual quando começar.

1. Abra *host.js.*

1. Na `customHandler.description` secção, desacione o valor de `defaultExecutablePath` `handler` (no Windows, desapedahá-lo para `handler.exe` ).

1. Na `customHandler` secção, adicione um imóvel nomeado `enableForwardingHttpRequest` e desaveri o seu valor para `true` . Para funções que consistem apenas num gatilho HTTP, esta definição simplifica a programação, permitindo-lhe trabalhar com um pedido HTTP típico em vez da [carga útil](functions-custom-handlers.md#request-payload)do pedido de pedido de encomenda personalizado .

1. Confirme que a `customHandler` secção parece este exemplo. Guarde o ficheiro.

    ```
    "customHandler": {
      "description": {
        "defaultExecutablePath": "handler",
        "workingDirectory": "",
        "arguments": []
      },
      "enableForwardingHttpRequest": true
    }
    ```

A aplicação de função está configurada para iniciar o seu manipulador personalizado executável.

## <a name="run-the-function-locally"></a>Executar a função localmente

Pode executar este projeto no seu computador de desenvolvimento local antes de publicar para a Azure.

1. No terminal integrado, inicie a aplicação de função utilizando ferramentas principais de funções Azure.

    ```bash
    func start
    ```

1. Com as Ferramentas Core em execução, navegue para o seguinte URL para executar um pedido GET, que inclui `?name=Functions` cadeia de consulta.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Uma resposta é devolvida, que se parece com o seguinte num browser:

    ![Browser - saída de exemplo local](./media/create-first-function-vs-code-other/functions-test-local-browser.png)

1. As informações sobre o pedido são mostradas no painel **terminal.**

    ![Início do anfitrião de tarefas - saída do terminal de código VS](../../includes/media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Prima <kbd>Ctrl + C</kbd> para parar as Ferramentas Core.

Depois de verificar que a função funciona corretamente no seu computador local, é hora de usar o Código do Estúdio Visual para publicar o projeto diretamente para o Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="compile-the-custom-handler-for-azure"></a>Compilar o manipulador personalizado para Azure

Nesta secção, publica o seu projeto para a Azure numa aplicação de função que executa o Linux. Na maioria dos casos, deve recompler o seu binário e ajustar a sua configuração para corresponder à plataforma-alvo antes de publicá-la ao Azure.

# <a name="go"></a>[Ir](#tab/go)

1. No terminal integrado, compile o manipulador para Linux/x64. Um binário nomeado `handler` é criado na raiz da aplicação de função.

    # <a name="macos"></a>[macOS](#tab/macos)

    ```bash
    GOOS=linux GOARCH=amd64 go build handler.go
    ```

    # <a name="linux"></a>[Linux](#tab/linux)

    ```bash
    GOOS=linux GOARCH=amd64 go build handler.go
    ```

    # <a name="windows"></a>[Windows](#tab/windows)
    ```cmd
    set GOOS=linux
    set GOARCH=amd64
    go build handler.go
    ```

    Mude o `defaultExecutablePath` *inhost.js* de `handler.exe` `handler` . Isto instrui a aplicação de função para executar o binário Linux.
    
    ---

# <a name="rust"></a>[Rust](#tab/rust)

1. Criar um ficheiro em *.cargo/config*. Adicione o seguinte conteúdo e guarde o ficheiro.

    ```
    [target.x86_64-unknown-linux-musl]
    linker = "rust-lld"
    ```

1. No terminal integrado, compile o manipulador para Linux/x64. Um binário chamado `handler` é criado. Copie-o para a raiz da aplicação de função.

    ```bash
    rustup target add x86_64-unknown-linux-musl
    cargo build --release --target=x86_64-unknown-linux-musl
    cp target/x86_64-unknown-linux-musl/release/handler .
    ```

1. Se estiver a utilizar o Windows, altere o `defaultExecutablePath` *inhost.js* `handler.exe` de `handler` . Isto instrui a aplicação de função para executar o binário Linux.

1. Adicione a seguinte linha ao ficheiro *.funcignore:*

    ```
    target
    ```

    Isto impede a publicação do conteúdo da *pasta-alvo.*

---

## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Nesta secção, cria uma aplicação de função e recursos relacionados na sua subscrição Azure e, em seguida, implementa o seu código. 

> [!IMPORTANT]
> A publicação de uma aplicação de funções existente substitui o conteúdo dessa aplicação no Azure. 


1. Escolha o ícone Azure na barra de Atividade, em seguida, na área **Azure: Funções,** escolha o **botão Implementar para funcionar...** botão.

    ![Publicar o projeto no Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Forneça as seguintes informações nos pedidos:

    + **Selecione pasta**: Escolha uma pasta do seu espaço de trabalho ou navegue para uma que contenha a sua aplicação de função. Não verá isto se já tiver uma aplicação de função válida aberta.

    + **Selecione subscrição**: Escolha a subscrição para usar. Não verá isto se tiver apenas uma assinatura.

    + **Selecione Aplicação de Funções no Azure**: Selecione `+ Create new Function App (advanced)`. 
    
        > [!IMPORTANT]
        > A `advanced` opção permite-lhe escolher o sistema operativo específico em que a sua aplicação de função funciona no Azure, que neste caso é o Linux.

        ![VS Code - Selecione avançado criar nova aplicação de função](./media/functions-create-first-function-vs-code/functions-vscode-create-azure-advanced.png)

    + **Introduza um nome globalmente único para a aplicação de função**: Escreva um nome que seja válido num caminho URL. O nome que digita é validado para se certificar de que é único em Funções Azure.

    + **Selecione uma pilha de tempo de execução**: Escolha `Custom Handler` .

    + **Selecione um SISTEMA**: Escolha `Linux` .

    + **Selecione um plano de hospedagem**: Escolha `Consumption` .

    + **Selecione um grupo de recursos**: Escolha `+ Create new resource group` . Insira um nome para o grupo de recursos. Este nome deve ser único dentro da sua assinatura Azure. Pode usar o nome sugerido na solicitação.

    + **Selecione uma conta de armazenamento**: Escolha `+ Create new storage account` . Este nome deve ser globalmente único dentro de Azure. Pode usar o nome sugerido na solicitação.

    + **Selecione um recurso Application Insights**: Escolha `+ Create Application Insights resource` . Este nome deve ser globalmente único dentro de Azure. Pode usar o nome sugerido na solicitação.

    + **Selecione uma localização para novos recursos**: Para um melhor desempenho, escolha uma [região](https://azure.microsoft.com/regions/) perto de si. A extensão mostra o estado dos recursos individuais à medida que estão a ser criados em Azure na área de notificação.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Notificação da criação de recursos Azure":::

1. Quando concluídos, os seguintes recursos Azure são criados na sua subscrição:

    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    Depois de criar a aplicação de funções, é apresentada uma notificação e o pacote de implementação é aplicado. 

4. Selecione **Ver Saída** nesta notificação para ver os resultados da criação e implementação, incluindo os recursos Azure que criou. Se perder a notificação, selecione o ícone da campainha no canto inferior direito para vê-lo novamente.

    ![Criar notificação completa](./media/functions-create-first-function-vs-code/function-create-notifications.png)

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre a Azure Functions manipuladores personalizados](functions-custom-handlers.md)
