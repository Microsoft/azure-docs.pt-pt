---
title: Quickstart - Criar e gerir recursos em Serviços de Comunicação Azure
titleSuffix: An Azure Communication Services quickstart
description: Neste arranque rápido, aprenderá a criar e gerir o seu primeiro recurso Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-azp-net
ms.openlocfilehash: 3ba3d8d4b345c9db2e41c647147021eb9616288e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948299"
---
# <a name="quickstart-create-and-manage-communication-services-resources"></a>Quickstart: Criar e gerir recursos dos Serviços de Comunicação

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Inicie-se com os Serviços de Comunicação Azure, disponibilizando o seu primeiro recurso de Serviços de Comunicação. Os recursos dos serviços de comunicação podem ser adcedidos através do portal Azure ou com a biblioteca de clientes de gestão .NET. A biblioteca de clientes de gestão permite-lhe criar, configurar, atualizar e eliminar os seus recursos e interfaces com [o Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview), o serviço de implementação e gestão da Azure. Todas as funcionalidades disponíveis nas bibliotecas dos clientes estão disponíveis no portal Azure. 

> [!WARNING]
> Note que a disponibilidade dos Serviços de Comunicação está restrita à geografia dos EUA durante a pré-visualização pública. Note também que os recursos de comunicação não podem ser transferidos para uma subscrição diferente durante a pré-visualização pública.

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/create-resource-azp.md)]
::: zone-end

::: zone pivot="platform-net"
[!INCLUDE [.NET](./includes/create-resource-net.md)]
::: zone-end

## <a name="access-your-connection-strings-and-service-endpoints"></a>Aceda às cordas de ligação e pontos finais de serviço

As cadeias de ligação permitem que as bibliotecas de clientes dos Serviços de Comunicação se conectem e autentem ao Azure. Pode aceder às cadeias de ligação dos serviços de comunicação e pontos finais de serviço a partir do portal Azure ou programáticamente com APIs do Gestor de Recursos Azure. 

Depois de navegar para o seu recurso de Serviços de Comunicação, selecione **Chaves** do menu de navegação e copie os valores de **ligação** ou **ponto final** para utilização pelas bibliotecas de clientes dos Serviços de Comunicação. Note que tem acesso a chaves primárias e secundárias. Isto pode ser útil em cenários em que gostaria de fornecer acesso temporário aos seus recursos de Serviços de Comunicação a terceiros ou ambiente de encenação.

:::image type="content" source="./media/key.png" alt-text="Screenshot da página chave dos serviços de comunicação.":::

## <a name="store-your-connection-string"></a>Guarde a sua cadeia de ligação

As bibliotecas de clientes dos Serviços de Comunicação utilizam cadeias de ligação para autorizar pedidos feitos aos Serviços de Comunicação. Tem várias opções para armazenar a sua cadeia de ligação:

* Uma aplicação em execução no ambiente de trabalho ou num dispositivo pode armazenar a cadeia de ligação num **ficheiroapp.config** ou **web.config.** Adicione o fio de ligação à secção **AppSettings nestes** ficheiros.
* Uma aplicação em execução num Serviço de Aplicações Azure pode armazenar a cadeia de ligação nas definições de [aplicação do Serviço de Aplicações da App.](https://docs.microsoft.com/azure/app-service/configure-common) Adicione o fio de ligação à secção de cordas de **ligação** do separador Definições de Aplicação dentro do portal.
* Pode armazenar a sua cadeia de ligação no [Cofre da Chave Azure](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault).
* Se estiver a executar a sua aplicação localmente, pode querer armazenar a sua cadeia de ligação numa variável ambiental.

### <a name="store-your-connection-string-in-an-environment-variable"></a>Guarde a sua cadeia de conexão numa variável ambiental

Para configurar uma variável ambiental, abra uma janela da consola e selecione o seu sistema operativo a partir dos separadores abaixo. `<yourconnectionstring>`Substitua-a pela sua verdadeira cadeia de ligação.

#### <a name="windows"></a>[Windows](#tab/windows)

Abra uma janela da consola e introduza o seguinte comando:

```console
setx COMMUNICATION_SERVICES_CONNECTION_STRING "<yourconnectionstring>"
```

Depois de adicionar a variável de ambiente, poderá ter de reiniciar todos os programas em execução que irão precisar de ler a variável de ambiente, incluindo a janela da consola. Por exemplo, se estiver a utilizar o Visual Studio como seu editor, reinicie o Visual Studio antes de executar o exemplo.

#### <a name="macos"></a>[macOS](#tab/unix)

Edite o seu **.zshrc**e adicione a variável ambiental:

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

Depois de adicionar a variável de ambiente, execute `source ~/.zshrc` a partir da janela da consola para que as alterações entrem em vigor. Se criou a variável ambiental com o seu IDE aberto, poderá ter de fechar e reabrir o editor, IDE ou concha para aceder à variável.

#### <a name="linux"></a>[Linux](#tab/linux)

Edite o seu **.bash_profile**, e adicione a variável ambiental:

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

Depois de adicionar a variável de ambiente, execute `source ~/.bash_profile` a partir da janela da consola para que as alterações entrem em vigor. Se criou a variável ambiental com o seu IDE aberto, poderá ter de fechar e reabrir o editor, IDE ou concha para aceder à variável.

---

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma assinatura de Serviços de Comunicação, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

Se tiver algum número de telefone atribuído ao seu recurso após a eliminação de recursos, os números de telefone serão libertados automaticamente do seu recurso ao mesmo tempo. 

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido aprendeu a:

> [!div class="checklist"]
> * Criar um recurso de Serviços de Comunicação
> * Configurar geografia de recursos e etiquetas
> * Aceda às chaves desse recurso
> * Eliminar o recurso

> [!div class="nextstepaction"]
> [Crie os seus primeiros tokens de acesso ao utilizador](access-tokens.md)
