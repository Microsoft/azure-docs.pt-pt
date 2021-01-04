---
title: Implementar tutorial nativo C++ para HoloLens
description: Quickstart que mostra como executar o tutorial nativo C++ em HoloLens
author: florianborn71
ms.author: flborn
ms.date: 06/08/2020
ms.topic: quickstart
ms.openlocfilehash: d35d6e75b45c2ea263c2e986c5fc6f414cad16e4
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724974"
---
# <a name="quickstart-deploy-native-c-sample-to-hololens"></a>Quickstart: Implementar a amostra nativa C++ para hololens

Este quickstart abrange como implementar e executar a aplicação tutorial nativa C++ num HoloLens 2.

Neste início rápido, vai aprender a:

> [!div class="checklist"]
>
>* Construa a aplicação tutorial para HoloLens.
>* Altere as credenciais de ARR no código fonte.
>* Desdobre e execute a amostra no dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para ter acesso ao serviço de renderização remota Azure, primeiro precisa [de criar uma conta.](../../../how-tos/create-an-account.md)

Deve ser instalado o seguinte software:

* Windows SDK 10.0.18362.0 [(download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* A mais recente versão do Visual Studio 2019 [(download)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Ferramentas de Estúdio Visual para Realidade Mista.](/windows/mixed-reality/install-the-tools) Especificamente, as seguintes instalações *de carga de trabalho* são obrigatórias:
  * **Desenvolvimento de desktop com C++**
  * **Desenvolvimento da Plataforma Universal windows (UWP)**
* GIT [(download)](https://git-scm.com/downloads)

## <a name="clone-the-arr-samples-repository"></a>Clone o repositório de amostras de ARR

Como primeiro passo, clonamos o repositório git, que abriga as amostras globais de renderização remota do Azure. Abra uma solicitação de comando (escreva `cmd` no menu inicial do Windows) e mude para um diretório onde pretende armazenar o projeto de amostra ARR.

Execute os seguintes comandos:

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

O último comando cria uma subdiretória no diretório ARR contendo os vários projetos de amostragem para a renderização remota Azure.

O tutorial C++ HoloLens pode ser encontrado no subdiretório *NativeCpp/HoloLens*.

## <a name="build-the-project"></a>Compilar o projeto

Abra o ficheiro de solução *HolographicApp.sln* localizado na subdirectory *NativeCpp/HoloLens* com o Visual Studio 2019.

Mude a configuração de construção para *Debug* (ou *Soltar)* e *ARM64*. Certifique-se também de que o modo depurar está definido para *dispositivo* em oposição à *Máquina Remota*:

![Estúdio Visual config](media/vs-config-native-cpp-tutorial.png)

Uma vez que as credenciais de conta são codificadas no código fonte do tutorial, altere-as para credenciais válidas. Para isso, abra o ficheiro `HolographicAppMain.cpp` dentro do Visual Studio e altere a parte onde o frontend é criado dentro do construtor de `HolographicAppMain` classe:

```cpp
// 2. Create front end
{
    // Users need to fill out the following with their account data and model
    RR::AzureFrontendAccountInfo init;
    init.AccountId = "00000000-0000-0000-0000-000000000000";
    init.AccountKey = "<account key>";
    init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to the region that the rendering session should be created in>
    init.AccountAuthenticationDomain = "westus2.mixedreality.azure.com"; // <change to the region the account was created in>
    m_modelURI = "builtin://Engine";
    m_sessionOverride = ""; // If there is a valid session ID to re-use, put it here. Otherwise a new one is created
    m_frontEnd = RR::ApiHandle(RR::AzureFrontend(init));
}
```

Especificamente, alterar os seguintes valores:
* `init.AccountId`, `init.AccountKey` e para usar os `init.AccountAuthenticationDomain` dados da sua conta. Consulte o parágrafo sobre como [obter informações sobre contas](../../../how-tos/create-an-account.md#retrieve-the-account-information).
* Especificar onde criar a sessão de renderização remota modificando a parte da cadeia da região `init.AccountDomain` para outras regiões que `westus2` não, por exemplo `"westeurope.mixedreality.azure.com"` .
* Além disso, `m_sessionOverride` pode ser alterado para um ID de sessão existente. As sessões podem ser criadas fora desta amostra, por exemplo, utilizando [o script PowerShell](../../../samples/powershell-example-scripts.md#script-renderingsessionps1) ou utilizando diretamente a [Sessão REST API.](../../../how-tos/session-rest-api.md#create-a-session)
Recomenda-se a criação de uma sessão fora da amostra quando a amostra deve ser executada várias vezes. Se nenhuma sessão for transmitida, a amostra criará uma nova sessão em cada startup, que poderá demorar vários minutos.

Agora a aplicação pode ser compilada.

## <a name="launch-the-application"></a>Iniciar a aplicação

1. Ligue o HoloLens com um cabo USB ao seu computador.
1. Ligue os HoloLens e espere até que o menu inicial apareça.
1. Inicie o Depurador no Visual Studio (F5). A aplicação é implementada automaticamente no dispositivo.

A aplicação da amostra deve ser lançada e deve aparecer um painel de texto que o informe sobre o estado atual da aplicação. O estado na hora do arranque é iniciar uma nova sessão ou ligar-se a uma sessão existente. Após o carregamento do modelo ter terminado, o modelo do motor incorporado aparece na sua posição de cabeça. Em termos de oclusão, o modelo do motor interage corretamente com o cubo de fiação que é renderizado localmente.

 Se quiser lançar a amostra uma segunda vez mais tarde, também pode encontrá-la a partir do menu inicial hololens, mas note que pode ter um ID de sessão expirado compilado nele.

## <a name="next-steps"></a>Passos seguintes

Este quickstart baseia-se no resultado de um tutorial que explica como integrar todas as peças relacionadas com a renderização remota numa *App Holográfica* de stock. Para saber quais são os passos necessários, siga este tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Integrar renderização remota numa app holográfica HoloLens](../../../tutorials/native-cpp/hololens/integrate-remote-rendering-into-holographic-app.md)