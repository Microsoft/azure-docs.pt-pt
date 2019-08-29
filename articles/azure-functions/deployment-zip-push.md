---
title: Implantação por push de zip para Azure Functions | Microsoft Docs
description: Use os recursos de implantação de arquivo. zip do serviço de implantação kudu para publicar seu Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/12/2018
ms.author: glenga
ms.openlocfilehash: c411ff6b3a7152adaaf29045f4c3b3a3deb22d09
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087597"
---
# <a name="zip-deployment-for-azure-functions"></a>Implantação de zip para Azure Functions

Este artigo descreve como implantar seus arquivos de projeto de aplicativo de funções no Azure por meio de um arquivo. zip (compactado). Você aprende como fazer uma implantação por push, usando CLI do Azure e usando as APIs REST. [Azure Functions Core Tools](functions-run-local.md) também usa essas APIs de implantação ao publicar um projeto local no Azure.

Azure Functions tem a gama completa de opções de implantação e integração contínuas fornecidas pelo serviço Azure App. Para obter mais informações, consulte [implantação contínua para Azure Functions](functions-continuous-deployment.md).

Para agilizar o desenvolvimento, você pode achar mais fácil implantar seus arquivos de projeto de aplicativo de funções diretamente de um arquivo. zip. A API de implantação. zip usa o conteúdo de um arquivo. zip e extrai o conteúdo para a `wwwroot` pasta do seu aplicativo de funções. Essa implantação de arquivo. zip usa o mesmo serviço kudu que capacita implantações baseadas em integração contínua, incluindo:

+ Exclusão de arquivos que foram deixados de implantações anteriores.
+ Personalização da implantação, incluindo a execução de scripts de implantação.
+ Logs de implantação.
+ Sincronizando gatilhos de função em um aplicativo de função de [plano de consumo](functions-scale.md) .

Para obter mais informações, consulte a [referência de implantação. zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="deployment-zip-file-requirements"></a>Requisitos de arquivo. zip de implantação

O arquivo. zip que você usa para a implantação por push deve conter todos os arquivos necessários para executar a função.

>[!IMPORTANT]
> Quando você usa a implantação. zip, todos os arquivos de uma implantação existente que não são encontrados no arquivo. zip são excluídos do seu aplicativo de funções.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Um aplicativo de funções inclui todos os arquivos e pastas no `wwwroot` diretório. Uma implantação de arquivo. zip inclui o conteúdo do `wwwroot` diretório, mas não o próprio diretório. Ao implantar um C# projeto de biblioteca de classes, você deve incluir os arquivos de biblioteca compilados e `bin` as dependências em uma subpasta no pacote. zip.

## <a name="download-your-function-app-files"></a>Baixar seus arquivos de aplicativo de funções

Quando você está desenvolvendo em um computador local, é fácil criar um arquivo. zip da pasta de projeto do aplicativo de funções em seu computador de desenvolvimento.

No entanto, você pode ter criado suas funções usando o editor no portal do Azure. Você pode baixar um projeto de aplicativo de funções existente de uma das seguintes maneiras:

+ **No portal do Azure:**

  1. Entre no [portal do Azure](https://portal.azure.com)e, em seguida, vá para seu aplicativo de funções.

  2. Na guia **visão geral** , selecione **baixar conteúdo do aplicativo**. Selecione as opções de download e, em seguida, selecione **baixar**.

      ![Baixar o projeto de aplicativo de funções](./media/deployment-zip-push/download-project.png)

     O arquivo. zip baixado está no formato correto para ser republicado em seu aplicativo de funções usando a implantação por push do. zip. O download do portal também pode adicionar os arquivos necessários para abrir seu aplicativo de funções diretamente no Visual Studio.

+ **Usando APIs REST:**

    Use a seguinte API Get de implantação para baixar os arquivos do `<function_app>` seu projeto: 

        https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/

    Incluir `/site/wwwroot/` garante que o arquivo zip inclua apenas os arquivos de projeto do aplicativo de funções e não todo o site. Se você ainda não tiver entrado no Azure, será solicitado a fazê-lo.  

Você também pode baixar um arquivo. zip de um repositório GitHub. Quando você baixa um repositório GitHub como um arquivo. zip, o GitHub adiciona um nível de pasta extra para a ramificação. Esse nível de pasta extra significa que você não pode implantar o arquivo. zip diretamente, pois o baixou do GitHub. Se você estiver usando um repositório GitHub para manter seu aplicativo de funções, deverá usar a [integração contínua](functions-continuous-deployment.md) para implantar seu aplicativo.  

## <a name="cli"></a>Implantar usando CLI do Azure

Você pode usar CLI do Azure para disparar uma implantação de envio por push. Envie por push um arquivo. zip para seu aplicativo de funções usando o comando [AZ functionapp Deployment Source config-zip](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config-zip) . Para usar esse comando, você deve usar CLI do Azure versão 2.0.21 ou posterior. Para ver o que CLI do Azure versão que você está usando, `az --version` use o comando.

No comando a seguir, substitua o `<zip_file_path>` espaço reservado pelo caminho para o local do arquivo. zip. Além disso, `<app_name>` substitua pelo nome exclusivo do seu aplicativo de funções. 

```azurecli-interactive
az functionapp deployment source config-zip  -g myResourceGroup -n \
<app_name> --src <zip_file_path>
```

Este comando implanta arquivos de projeto do arquivo. zip baixado em seu aplicativo de funções no Azure. Em seguida, ele reinicia o aplicativo. Para exibir a lista de implantações para este aplicativo de funções, você deve usar as APIs REST.

Quando você estiver usando CLI do Azure em seu computador local, `<zip_file_path>` será o caminho para o arquivo. zip em seu computador. Você também pode executar CLI do Azure em [Azure cloud Shell](../cloud-shell/overview.md). Ao usar Cloud Shell, você deve primeiro carregar o arquivo. zip de implantação na conta de arquivos do Azure associada ao seu Cloud Shell. Nesse caso, `<zip_file_path>` é o local de armazenamento que sua conta de Cloud Shell usa. Para obter mais informações, consulte [arquivos de persistência em Azure cloud Shell](../cloud-shell/persisting-shell-storage.md).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Executar funções do pacote de implantação

Você também pode optar por executar suas funções diretamente do arquivo do pacote de implantação. Esse método ignora a etapa de implantação de copiar arquivos do pacote para o `wwwroot` diretório do seu aplicativo de funções. Em vez disso, o arquivo de pacote é montado pelo tempo de execução do Functions `wwwroot` e o conteúdo do diretório torna-se somente leitura.  

A implantação de zip integra-se com esse recurso, que você pode habilitar definindo a configuração `WEBSITE_RUN_FROM_PACKAGE` do aplicativo de funções `1`com um valor de. Para obter mais informações, consulte [executar suas funções de um arquivo de pacote de implantação](run-functions-from-deployment-package.md).

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Implementação contínua para Funções do Azure](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
