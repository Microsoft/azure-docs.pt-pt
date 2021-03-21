---
title: Colocação de push zip para funções Azure
description: Utilize as instalações de implantação de ficheiros .zip do serviço de implantação Kudu para publicar as suas Funções Azure.
ms.topic: conceptual
ms.date: 08/12/2018
ms.openlocfilehash: e104661dcdf1f6c6fd6dd5eb1024748980e7931f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018453"
---
# <a name="zip-deployment-for-azure-functions"></a>Zip deployment for Azure Functions (Implementação com ficheiro zip para as Funções do Azure)

Este artigo descreve como implementar os ficheiros do projeto de aplicação de função para Azure a partir de um ficheiro de .zip (comprimido). Aprende-se a fazer uma implementação de impulsos, tanto utilizando o Azure CLI como utilizando as APIs REST. [A azure Functions Core Tools](functions-run-local.md) também utiliza estas APIs de implementação ao publicar um projeto local para a Azure.

A Azure Functions tem toda a gama de opções de implementação e integração contínuas que são fornecidas pelo Azure App Service. Para obter mais informações, consulte [a implementação contínua para funções Azure](functions-continuous-deployment.md).

Para acelerar o desenvolvimento, poderá ser mais fácil implementar os ficheiros do projeto de aplicações de função diretamente a partir de um ficheiro .zip. A API de implantação .zip leva o conteúdo de um ficheiro .zip e extrai o conteúdo para a `wwwroot` pasta da sua aplicação de função. Esta implementação de ficheiros .zip utiliza o mesmo serviço Kudu que alimenta implementações baseadas em integração contínua, incluindo:

+ Eliminação de ficheiros que foram deixados de implementações anteriores.
+ Personalização de implementação, incluindo scripts de implementação.
+ Registos de implantação.
+ A função de sincronização dispara numa aplicação de função [do plano de consumo.](functions-scale.md)

Para obter mais informações, consulte a [referência de .zip de implantação](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="deployment-zip-file-requirements"></a>Requisitos de ficheiros de .zip de implantação

O ficheiro .zip que utiliza para a implantação de push deve conter todos os ficheiros necessários para executar a sua função.

>[!IMPORTANT]
> Quando utiliza .zip implementação, quaisquer ficheiros de uma implementação existente que não sejam encontrados no ficheiro .zip são eliminados da sua aplicação de função.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Uma aplicação de função inclui todos os ficheiros e pastas no `wwwroot` diretório. Uma .zip implantação de ficheiros inclui o conteúdo do `wwwroot` diretório, mas não o próprio diretório. Ao implementar um projeto de biblioteca de classes C', deve incluir os ficheiros e dependências da biblioteca compiladas numa `bin` sub-dobradeira no seu pacote .zip.

## <a name="download-your-function-app-files"></a>Descarregue os ficheiros de aplicações da sua função

Quando está a desenvolver-se num computador local, é fácil criar um ficheiro .zip da pasta de projeto de aplicação de função no seu computador de desenvolvimento.

No entanto, pode ter criado as suas funções utilizando o editor no portal Azure. Você pode baixar um projeto de aplicativo de função existente de uma forma:

+ **Do portal Azure:**

  1. Inscreva-se no [portal Azure](https://portal.azure.com)e, em seguida, vá para a sua aplicação de função.

  2. No **separador 'Visão Geral',** selecione **Descarregue o conteúdo da aplicação**. Selecione as suas opções de descarregamento e, em seguida, selecione **Download**.

      ![Descarregue o projeto de aplicação de função](./media/deployment-zip-push/download-project.png)

     O ficheiro .zip descarregado encontra-se no formato correto para ser republicado na sua aplicação de função utilizando .zip implementação de push. O download do portal também pode adicionar os ficheiros necessários para abrir a sua aplicação de função diretamente no Visual Studio.

+ **Utilização de APIs de REPOUSO:**

    Utilize a seguinte implementação GET API para descarregar os ficheiros do seu `<function_app>` projeto: 

    ```http
    https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/
    ```

    Incluindo `/site/wwwroot/` certifique-se de que o seu ficheiro zip inclui apenas os ficheiros de projeto de aplicações de função e não todo o site. Se ainda não se inscreveu no Azure, ser-lhe-á pedido que o faça.  

Também pode descarregar um ficheiro .zip de um repositório GitHub. Quando descarrega um repositório GitHub como um ficheiro .zip, o GitHub adiciona um nível de pasta extra para o ramo. Este nível de pasta extra significa que não pode implementar o ficheiro .zip diretamente à medida que o descarregou do GitHub. Se estiver a utilizar um repositório GitHub para manter a sua aplicação de função, deve utilizar uma [integração contínua](functions-continuous-deployment.md) para implementar a sua aplicação.  

## <a name="deploy-by-using-azure-cli"></a><a name="cli"></a>Implementar com a CLI do Azure

Pode utilizar o Azure CLI para ativar uma colocação de impulso. Empurre implementar um ficheiro .zip para a sua aplicação de função utilizando o comando [config-zip de config-zip da fonte de implementação do az functionapp.](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config-zip) Para utilizar este comando, tem de utilizar a versão 2.0.21 ou posterior do Azure CLI. Para ver que versão Azure CLI está a usar, utilize o `az --version` comando.

No comando seguinte, substitua o `<zip_file_path>` espaço reservado pelo caminho para a localização do seu ficheiro .zip. Além disso, `<app_name>` substitua-o pelo nome único da sua aplicação de função e `<resource_group>` substitua-o pelo nome do seu grupo de recursos.

```azurecli-interactive
az functionapp deployment source config-zip -g <resource_group> -n \
<app_name> --src <zip_file_path>
```

Este comando implementa ficheiros de projeto do ficheiro .zip descarregado para a sua aplicação de função em Azure. Em seguida, reinicia a aplicação. Para visualizar a lista de implementações para esta aplicação de funções, tem de utilizar as APIs REST.

Quando estiver a utilizar o Azure CLI no seu computador local, `<zip_file_path>` é o caminho para o ficheiro .zip no seu computador. Também pode executar Azure CLI em [Azure Cloud Shell](../cloud-shell/overview.md). Quando utilizar o Cloud Shell, tem primeiro de enviar o ficheiro .zip de implementação para a conta Azure Files que está associada à sua Cloud Shell. Nesse caso, `<zip_file_path>` é o local de armazenamento que a sua conta Cloud Shell utiliza. Para obter mais informações, consulte [os ficheiros Persist em Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Executar funções a partir do pacote de implantação

Também pode optar por executar as suas funções diretamente a partir do ficheiro de pacote de implementação. Este método ignora o passo de implementação de ficheiros de cópia do pacote para o `wwwroot` diretório da sua aplicação de funções. Em vez disso, o ficheiro de pacote é montado pelo tempo de funcionamento de Funções e o conteúdo do `wwwroot` diretório torna-se apenas para leitura.  

A implementação zip integra-se com esta funcionalidade, que pode permitir definindo a definição da aplicação de função `WEBSITE_RUN_FROM_PACKAGE` para um valor de `1` . Para obter mais informações, consulte [Executar as suas funções a partir de um ficheiro de pacote de implantação](run-functions-from-deployment-package.md).

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Implementação contínua para Funções do Azure](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
