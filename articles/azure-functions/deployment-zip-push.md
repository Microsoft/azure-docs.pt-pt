---
title: Implantação de impulso zip para funções Azure
description: Utilize as instalações de implantação de ficheiros .zip do serviço de implantação Kudu para publicar as suas Funções Azure.
ms.topic: conceptual
ms.date: 08/12/2018
ms.openlocfilehash: 6bda0859ca4741fe74f572b204e40130c56c46fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769677"
---
# <a name="zip-deployment-for-azure-functions"></a>Zip deployment for Azure Functions (Implementação com ficheiro zip para as Funções do Azure)

Este artigo descreve como implementar ficheiros do projeto da sua aplicação de função para O Azure a partir de um ficheiro .zip (comprimido). Aprende-se a fazer uma implementação de impulsos, tanto utilizando o Azure CLI como utilizando as APIs REST. [As Ferramentas Core funcionais do Azure](functions-run-local.md) também utilizam estas APIs de implantação ao publicar um projeto local para o Azure.

A Azure Functions tem toda a gama de opções contínuas de implantação e integração que são fornecidas pelo Serviço de Aplicações Azure. Para mais informações, consulte a [implantação contínua para funções Azure](functions-continuous-deployment.md).

Para acelerar o desenvolvimento, poderá ser mais fácil implementar os ficheiros do projeto da sua aplicação de função diretamente a partir de um ficheiro .zip. A API de implementação .zip leva o conteúdo de `wwwroot` um ficheiro .zip e extrai o conteúdo para a pasta da sua aplicação de função. Esta implementação de ficheiros .zip utiliza o mesmo serviço Kudu que alimenta implementações contínuas baseadas na integração, incluindo:

+ Eliminação de ficheiros que foram deixados de destacamentos anteriores.
+ Personalização de implementação, incluindo scripts de implementação de execução.
+ Registos de implantação.
+ A função sincronização dispara numa aplicação de função de plano de [consumo.](functions-scale.md)

Para mais informações, consulte a referência de [implantação .zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="deployment-zip-file-requirements"></a>Requisitos de ficheiro saqueado .zip

O ficheiro .zip que utiliza para a implementação do impulso deve conter todos os ficheiros necessários para executar a sua função.

>[!IMPORTANT]
> Quando utiliza a implementação .zip, quaisquer ficheiros de uma implementação existente que não se encontre no ficheiro .zip são eliminados da aplicação de funções.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Uma aplicação de função inclui todos `wwwroot` os ficheiros e pastas no diretório. Uma implementação de ficheiros `wwwroot` .zip inclui o conteúdo do diretório, mas não o próprio diretório. Ao implementar um projeto de biblioteca de classe C#, deve incluir `bin` os ficheiros e dependências compilados da biblioteca numa subpasta no seu pacote .zip.

## <a name="download-your-function-app-files"></a>Descarregue os ficheiros da sua aplicação de funções

Quando está a desenvolver-se num computador local, é fácil criar um ficheiro .zip da pasta do projeto da aplicação de funções no seu computador de desenvolvimento.

No entanto, pode ter criado as suas funções utilizando o editor no portal Azure. Você pode baixar um projeto de aplicação de função existente de uma dessas formas:

+ **Do portal Azure:**

  1. Inscreva-se no [portal Azure](https://portal.azure.com)e depois vá à sua aplicação de funções.

  2. No separador **Overview,** selecione **descarregue o conteúdo**da aplicação . Selecione as suas opções de descarregamento e, em seguida, selecione **Download**.

      ![Descarregue o projeto de aplicação de funções](./media/deployment-zip-push/download-project.png)

     O ficheiro .zip descarregado encontra-se no formato correto a ser republicado na sua aplicação de funções utilizando a implementação de .zip push. O download do portal também pode adicionar os ficheiros necessários para abrir a sua aplicação de função diretamente no Estúdio Visual.

+ **Utilização de APIs de REPOUSO:**

    Utilize a seguinte implementação GET API `<function_app>` para descarregar os ficheiros do seu projeto: 

        https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/

    Incluindo certifique-se `/site/wwwroot/` de que o seu ficheiro zip inclui apenas os ficheiros do projeto da aplicação de função e não todo o site. Se ainda não assinou contrato com o Azure, será-lhe pedido que o faça.  

Também pode descarregar um ficheiro .zip a partir de um repositório GitHub. Ao descarregar um repositório GitHub como um ficheiro .zip, o GitHub adiciona um nível extra de pasta para o ramo. Este nível extra de pasta significa que não pode implementar o ficheiro .zip diretamente quando o descarregou do GitHub. Se estiver a utilizar um repositório GitHub para manter a sua aplicação de funções, deverá utilizar uma [integração contínua](functions-continuous-deployment.md) para implementar a sua aplicação.  

## <a name="deploy-by-using-azure-cli"></a><a name="cli"></a>Implementar com a CLI do Azure

Pode utilizar o Azure CLI para acionar uma implantação de impulsos. Empurre um ficheiro .zip para a sua aplicação de função utilizando o comando de origem de implantação de fonte de [função az.](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config-zip) Para utilizar este comando, deve utilizar a versão Azure CLI 2.0.21 ou posterior. Para ver que versão Azure CLI `az --version` está a utilizar, utilize o comando.

No comando seguinte, `<zip_file_path>` substitua o espaço reservado pelo caminho para a localização do seu ficheiro .zip. Além `<app_name>` disso, substitua-o pelo `<resource_group>` nome único da sua aplicação de funções e substitua-o pelo nome do seu grupo de recursos.

```azurecli-interactive
az functionapp deployment source config-zip -g <resource_group> -n \
<app_name> --src <zip_file_path>
```

Este comando implementa ficheiros de projeto sabotados do ficheiro .zip descarregado para a sua aplicação de função em Azure. Em seguida, reinicia a aplicação. Para ver a lista de implementações para esta aplicação de função, deve utilizar as APIs REST.

Quando estiver a utilizar o Azure CLI no seu computador local, `<zip_file_path>` é o caminho para o ficheiro .zip no seu computador. Também pode executar o Azure CLI em [Azure Cloud Shell.](../cloud-shell/overview.md) Quando utilizar a Cloud Shell, tem primeiro de carregar o ficheiro de implementação .zip para a conta Azure Files que está associada à sua Cloud Shell. Nesse caso, `<zip_file_path>` é o local de armazenamento que a sua conta Cloud Shell utiliza. Para mais informações, consulte [ficheiros Persist em Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Executar funções a partir do pacote de implantação

Também pode optar por executar as suas funções diretamente a partir do ficheiro do pacote de implementação. Este método ignora a etapa de implementação `wwwroot` de copiar ficheiros do pacote para o diretório da sua aplicação de função. Em vez disso, o ficheiro de embalagem é montado pelo `wwwroot` tempo de funcionamento das funções e o conteúdo do diretório torna-se apenas de leitura.  

A implementação em zip integra-se com esta funcionalidade, que pode ativar definindo a definição `WEBSITE_RUN_FROM_PACKAGE` da aplicação de funções para um valor de `1`. Para mais informações, consulte [Executar as suas funções a partir de um ficheiro de pacote](run-functions-from-deployment-package.md)de implementação .

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Implementação contínua para Funções do Azure](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
