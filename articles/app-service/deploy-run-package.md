---
title: Executar seu aplicativo de um pacote ZIP
description: Implante o pacote ZIP do aplicativo com atomicidade. Melhore a previsibilidade e a confiabilidade do comportamento do seu aplicativo durante o processo de implantação ZIP.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 5cc909d79b3f5ea2b4c6a3da12bc7250addbe00c
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945841"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Executar seu aplicativo no serviço Azure App diretamente de um pacote ZIP

No [Azure app Service](overview.md), você pode executar seus aplicativos diretamente de um arquivo de pacote zip de implantação. Este artigo mostra como habilitar essa funcionalidade em seu aplicativo.

Todos os outros métodos de implantação no serviço de aplicativo têm algo em comum: os arquivos são implantados no *D:\home\site\wwwroot* em seu aplicativo (ou */Home/site/wwwroot* para aplicativos Linux). Como o mesmo diretório é usado pelo seu aplicativo em tempo de execução, é possível que a implantação falhe devido a conflitos de bloqueio de arquivo, e para que o aplicativo se comporte de forma inesperada porque alguns dos arquivos ainda não foram atualizados.

Por outro lado, quando você executa diretamente de um pacote, os arquivos no pacote não são copiados para o diretório *wwwroot* . Em vez disso, o próprio pacote ZIP é montado diretamente como o diretório *wwwroot* somente leitura. Há vários benefícios em executar diretamente a partir de um pacote:

- Elimina conflitos de bloqueio de arquivo entre implantação e tempo de execução.
- Garante que somente aplicativos de implantação completa estejam em execução a qualquer momento.
- Pode ser implantado em um aplicativo de produção (com reinicialização).
- Melhora o desempenho de implantações de Azure Resource Manager.
- Pode reduzir os horários de início frio, especialmente para funções de JavaScript com árvores de pacote NPM de grande porte.

> [!NOTE]
> Atualmente, há suporte apenas para arquivos de pacote ZIP.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Habilitar execução do pacote

A configuração do aplicativo `WEBSITE_RUN_FROM_PACKAGE` permite a execução de um pacote. Para defini-lo, execute o comando a seguir com CLI do Azure.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"` permite executar seu aplicativo de um pacote local para seu aplicativo. Você também pode [executar a partir de um pacote remoto](#run-from-external-url-instead).

## <a name="run-the-package"></a>Executar o pacote

A maneira mais fácil de executar um pacote em seu serviço de aplicativo é com o comando CLI do Azure [AZ webapp Deployment Source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) . Por exemplo:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Como a configuração do aplicativo `WEBSITE_RUN_FROM_PACKAGE` está definida, esse comando não extrai o conteúdo do pacote para o diretório *D:\home\site\wwwroot* do seu aplicativo. Em vez disso, ele carrega o arquivo ZIP como está para *D:\home\data\SitePackages*e cria um *PackageName. txt* no mesmo diretório, que contém o nome do pacote zip a ser carregado no tempo de execução. Se você carregar o pacote ZIP de uma maneira diferente (como [FTP](deploy-ftp.md)), precisará criar o diretório *D:\home\data\SitePackages* e o arquivo *PackageName. txt* manualmente.

O comando também reinicia o aplicativo. Como `WEBSITE_RUN_FROM_PACKAGE` é definido, o serviço de aplicativo monta o pacote carregado como o diretório *wwwroot* somente leitura e executa o aplicativo diretamente desse diretório montado.

## <a name="run-from-external-url-instead"></a>Executar da URL externa em vez disso

Você também pode executar um pacote de uma URL externa, como o armazenamento de BLOBs do Azure. Você pode usar o [Gerenciador de armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) para carregar arquivos de pacote na sua conta de armazenamento de BLOBs. Você deve usar um contêiner de armazenamento privado com uma [SAS (assinatura de acesso compartilhado)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) para permitir que o tempo de execução do serviço de aplicativo acesse o pacote com segurança. 

Depois de carregar o arquivo no armazenamento de BLOBs e ter uma URL SAS para o arquivo, defina a configuração `WEBSITE_RUN_FROM_PACKAGE` aplicativo como a URL. O exemplo a seguir faz isso usando CLI do Azure:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Se você publicar um pacote atualizado com o mesmo nome para o armazenamento de BLOBs, será necessário reiniciar o aplicativo para que o pacote atualizado seja carregado no serviço de aplicativo.

## <a name="troubleshooting"></a>Resolução de problemas

- A execução direta de um pacote torna `wwwroot` somente leitura. Seu aplicativo receberá um erro se tentar gravar arquivos nesse diretório.
- Não há suporte para formatos TAR e GZIP.
- Este recurso não é compatível com o [cache local](overview-local-cache.md).
- Para obter um desempenho de inicialização a frio aprimorado, use a opção de zip local (`WEBSITE_RUN_FROM_PACKAGE`= 1).

## <a name="more-resources"></a>Mais recursos

- [Implantação contínua para o serviço Azure App](deploy-continuous-deployment.md)
- [Implantar código com um arquivo ZIP ou WAR](deploy-zip.md)
