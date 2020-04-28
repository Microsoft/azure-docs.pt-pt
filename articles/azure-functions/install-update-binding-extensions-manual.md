---
title: Instalação manual ou atualização das extensões de ligação das funções do Azure
description: Saiba como instalar ou atualizar as extensões de ligação das Funções Azure para aplicações de função implementadas.
ms.topic: reference
ms.date: 09/26/2018
ms.openlocfilehash: e8716f691a5d19ddac7fece47c423e1f7787b9db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75768865"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Instalar ou atualizar manualmente as extensões de ligação das funções Azure do portal

A partir da versão 2.x, o tempo de funcionamento das Funções Azure utiliza extensões de ligação para implementar código para gatilhos e encadernações. As extensões de encadernação são fornecidas nos pacotes NuGet. Para registar uma extensão, instala essencialmente um pacote. Ao desenvolver funções, a forma como instala extensões de ligação depende do ambiente de desenvolvimento. Para mais informações, consulte O Registo de [extensões vinculativas](./functions-bindings-register.md) no artigo de gatilhos e encadernações.

Por vezes, é necessário instalar ou atualizar manualmente as suas extensões de ligação no portal Azure. Por exemplo, poderá ter de atualizar uma ligação registada para uma versão mais recente. Pode também ser necessário registar uma ligação suportada que não possa ser instalada no separador **Integração** no portal.

## <a name="install-a-binding-extension"></a>Instalar uma extensão de ligação

Utilize os seguintes passos para instalar ou atualizar manualmente as extensões do portal.

1. No [portal Azure,](https://portal.azure.com)localize a sua aplicação de funções e selecione-a. Escolha o separador **Overview** e selecione **Stop**.  Parar a aplicação de funções desbloqueia ficheiros para que as alterações possam ser feitas.

1. Escolha o separador **de funcionalidades da Plataforma** e, sob ferramentas de **Desenvolvimento,** selecione **Advanced Tools (Kudu)**. O ponto`https://<APP_NAME>.scm.azurewebsites.net/`final de Kudu é aberto numa nova janela.

1. Na janela Kudu, selecione > **CmD**da **consola Debug**.  

1. Na janela de comando, navegue `D:\home\site\wwwroot` e `bin` escolha o ícone de exclusão ao lado para eliminar a pasta. Selecione **OK** para confirmar a eliminação.

1. Escolha o ícone de `extensions.csproj` edição ao lado do ficheiro, que define as extensões de ligação para a aplicação de função. O ficheiro do projeto é aberto no editor online.

1. Faça as adições e atualizações necessárias dos itens **PackageReference** no **ItemGroup**e, em seguida, selecione **Guardar**. A lista atual de versões de pacotes suportadas pode ser encontrada [no](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) artigo da Wiki. Todas as três encadernações de armazenamento Azure requerem o pacote Microsoft.Azure.WebJobs.Extensions.Storage.

1. A `wwwroot` partir da pasta, executar o seguinte comando para `bin` reconstruir os conjuntos referenciados na pasta.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. De volta ao separador **Overview** no portal, escolha **Iniciar** a reinicialização da aplicação de funções.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções azure gatilhos e encadernações](functions-triggers-bindings.md)
