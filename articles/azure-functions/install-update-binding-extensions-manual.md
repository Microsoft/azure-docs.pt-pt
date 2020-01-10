---
title: Instalar ou atualizar manualmente Azure Functions extensões de associação
description: Saiba como instalar ou atualizar Azure Functions extensões de associação para aplicativos de funções implantados.
ms.topic: reference
ms.date: 09/26/2018
ms.openlocfilehash: e8716f691a5d19ddac7fece47c423e1f7787b9db
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768865"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Instalar ou atualizar manualmente Azure Functions extensões de associação do portal

A partir da versão 2. x, o tempo de execução do Azure Functions usa extensões de associação para implementar o código para gatilhos e associações. As extensões de associação são fornecidas em pacotes NuGet. Para registrar uma extensão, você essencialmente instala um pacote. Ao desenvolver funções, a maneira como você instala as extensões de associação depende do ambiente de desenvolvimento. Para obter mais informações, consulte [registrar extensões de associação](./functions-bindings-register.md) no artigo gatilhos e associações.

Às vezes, você precisa instalar ou atualizar manualmente suas extensões de associação no portal do Azure. Por exemplo, talvez seja necessário atualizar uma associação registrada para uma versão mais recente. Talvez você também precise registrar uma associação com suporte que não possa ser instalada na guia **integrar** no Portal.

## <a name="install-a-binding-extension"></a>Instalar uma extensão de associação

Use as etapas a seguir para instalar manualmente ou atualizar extensões do Portal.

1. Na [portal do Azure](https://portal.azure.com), localize seu aplicativo de funções e selecione-o. Escolha a guia **visão geral** e selecione **parar**.  Parar o aplicativo de funções desbloqueia arquivos para que as alterações possam ser feitas.

1. Escolha a guia **recursos da plataforma** e, em ferramentas de **desenvolvimento** , selecione **ferramentas avançadas (kudu)** . O ponto de extremidade kudu (`https://<APP_NAME>.scm.azurewebsites.net/`) é aberto em uma nova janela.

1. Na janela kudu, selecione **console de depuração** > **cmd**.  

1. Na janela comando, navegue até `D:\home\site\wwwroot` e escolha o ícone excluir ao lado de `bin` para excluir a pasta. Selecione **OK** para confirmar a exclusão.

1. Escolha o ícone Editar ao lado do arquivo `extensions.csproj`, que define as extensões de associação para o aplicativo de funções. O arquivo de projeto é aberto no editor online.

1. Faça as adições e atualizações necessárias dos itens **PackageReference** no **grupo**de item e selecione **salvar**. A lista atual de versões de pacotes com suporte pode ser encontrada no artigo [quais pacotes são necessários?](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) wiki. Todas as três associações de armazenamento do Azure exigem o pacote Microsoft. Azure. webjobs. Extensions. Storage.

1. Na pasta `wwwroot`, execute o comando a seguir para recriar os assemblies referenciados na pasta `bin`.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. De volta à guia **visão geral** no portal, escolha **Iniciar** para reiniciar o aplicativo de funções.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
