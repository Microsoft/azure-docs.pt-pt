---
title: Instalar ou atualizar as extensões de enlace de funções do Azure manualmente
description: Saiba como instalar ou atualizar as extensões de enlace de funções do Azure para aplicações de função implementada.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: as funções do Azure, funções, atualizações de extensões, o NuGet, de enlace
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/26/2018
ms.author: glenga
ms.openlocfilehash: cda977ba59070c3ddaac05784277d6c0b5109f0f
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727116"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Instalar ou atualizar as extensões de enlace de funções do Azure no portal manualmente

O tempo de execução das funções do Azure versão 2.x usa as extensões de enlace para implementar o código para acionadores e enlaces. Extensões de ligação são fornecidas nos pacotes de NuGet. Para registar uma extensão, essencialmente instalar um pacote. Ao desenvolver funções, a maneira que instale as extensões de ligação depende do ambiente de desenvolvimento. Para obter mais informações, consulte [registar as extensões de vinculação](./functions-bindings-register.md) no artigo de enlaces e acionadores.

Por vezes, terá de instalar ou atualizar suas extensões de ligação no portal do Azure manualmente. Por exemplo, terá de atualizar um enlace registado numa versão mais recente. Também poderá ter de registar um enlace suportado que não pode ser instalado no **integrar** separador no portal.

## <a name="install-a-binding-extension"></a>Instalar uma extensão de ligação

Utilize os seguintes passos para instalar ou atualizar as extensões do portal de manualmente.

1. Na [portal do Azure](https://portal.azure.com), localize a aplicação de funções e selecioná-lo. Escolha o **descrição geral** separador e selecione **parar**.  A parar a aplicação de funções desbloqueia a ficheiros, de modo a que podem ser feitas alterações.

1. Escolha o **funcionalidades de plataforma** separador e, em **ferramentas de desenvolvimento** selecionar **ferramentas avançadas (Kudu)**. O ponto de extremidade do Kudu (`https://<APP_NAME>.scm.azurewebsites.net/`) é aberta numa nova janela.

1. Na janela do Kudu, selecione **consola de depuração** > **CMD**.  

1. Na janela de comando, navegue até `D:\home\site\wwwroot` e selecione o ícone Eliminar junto à `bin` a eliminar a pasta. Selecione **OK** para confirmar a eliminação.

1. Escolha o ícone de edição junto aos `extensions.csproj` arquivo, que define as extensões de enlace para a aplicação de funções. O arquivo de projeto é aberto no online editor.

1. Tornar as necessárias adições e atualizações de **PackageReference** itens no **ItemGroup**, em seguida, selecione **guardar**. A lista atual de versões de pacotes suportados pode ser encontrada no [os pacotes que preciso?](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) artigo do wiki. Todos os enlaces de armazenamento do Azure de três necessitam que o pacote de Microsoft.Azure.WebJobs.Extensions.Storage.

1. Partir do `wwwroot` pasta, execute o seguinte comando para reconstruir as assemblagens referenciadas no `bin` pasta.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. De volta a **descrição geral** separador no portal, escolha **iniciar** para reiniciar a aplicação de funções.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
