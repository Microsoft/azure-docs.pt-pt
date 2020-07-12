---
title: Instalação ou atualização manual extensões de ligação Azure Functions
description: Saiba como instalar ou atualizar extensões de ligação Azure Functions para aplicações de função implementadas.
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: f0705e62adc4acb26797b937a6dd8c684a598ebc
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86252628"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Instalação ou atualização manual extensões de ligação Azure Functions a partir do portal

Começando pela versão 2.x, o tempo de execução do Azure Functions utiliza extensões de ligação para implementar código para gatilhos e encadernações. As extensões de encadernação são fornecidas em pacotes NuGet. Para registar uma extensão, instale essencialmente um pacote. Ao desenvolver funções, a forma como instala extensões de ligação depende do ambiente de desenvolvimento. Para obter mais informações, consulte [registar extensões de ligação](./functions-bindings-register.md) no artigo de gatilhos e encadernações.

Por vezes é necessário instalar ou atualizar manualmente as extensões de encadernação no portal Azure. Por exemplo, poderá ter de atualizar uma ligação registada para uma versão mais recente. Pode também ser necessário registar uma ligação suportada que não pode ser instalada no **separador Integração** no portal.

## <a name="install-a-binding-extension"></a>Instale uma extensão de ligação

Utilize os seguintes passos para instalar ou atualizar manualmente extensões a partir do portal.

1. No [portal Azure,](https://portal.azure.com)localize a sua aplicação de função e selecione-a. Escolha o **separador Vista Geral** e selecione **Stop**.  Parar a aplicação de função desbloqueia ficheiros para que possam ser feitas alterações.

1. Escolha o separador **funcionalidades da Plataforma** e em **ferramentas de desenvolvimento** selecione **Ferramentas Avançadas (Kudu)**. O ponto final de Kudu `https://<APP_NAME>.scm.azurewebsites.net/` é aberto numa nova janela.

1. Na janela Kudu, selecione **Debug console**  >  **CMD**.  

1. Na janela de comando, navegue para `D:\home\site\wwwroot` e escolha o ícone de exclusão ao lado para eliminar a `bin` pasta. Selecione **OK** para confirmar a eliminação.

1. Escolha o ícone de edição ao lado do `extensions.csproj` ficheiro, que define as extensões de ligação para a aplicação de função. O ficheiro do projeto é aberto no editor online.

1. Faça as adições e atualizações necessárias dos itens **de PackageReference** no **ItemGroup**e, em seguida, selecione **Guardar**. A lista atual de versões de pacotes suportados pode ser encontrada nos [pacotes Que eu preciso?](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) As três ligações de armazenamento Azure requerem o pacote Microsoft.Azure.WebJobs.Extensions.Storage.

1. A partir da `wwwroot` pasta, executar o seguinte comando para reconstruir os conjuntos referenciados na `bin` pasta.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. De volta ao **separador Vista Geral** no portal, escolha **Iniciar** para reiniciar a aplicação de função.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções Azure desencadeia e encaderna](functions-triggers-bindings.md)
