---
title: Executar a sua aplicação a partir de um pacote ZIP
description: Implemente o pacote ZIP da sua aplicação com aatólidade. Melhore a previsibilidade e a fiabilidade do comportamento da sua aplicação durante o processo de implementação zip.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 3440653455626af4e3705d89349a66d6bf2fbfc0
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008134"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Execute a sua aplicação no Azure App Service diretamente a partir de um pacote ZIP

No [Azure App Service,](overview.md)pode executar as suas aplicações diretamente a partir de um ficheiro de pacote ZIP de implementação. Este artigo mostra como ativar esta funcionalidade na sua aplicação.

Todos os outros métodos de implementação no Serviço de Aplicações têm algo em comum: os seus ficheiros são implantados em *D:\home\site\wwwroot* na sua app (ou */home/site/wwwroot* para aplicações Linux). Uma vez que o mesmo diretório é utilizado pela sua app em tempo de execução, é possível que a implementação falhe devido a conflitos de bloqueio de ficheiros, e para que a app se comporte de forma imprevisível porque alguns dos ficheiros ainda não estão atualizados.

Em contrapartida, quando se corre diretamente a partir de um pacote, os ficheiros da embalagem não são copiados para o *diretório wwwroot.* Em vez disso, o pacote ZIP em si é montado diretamente como o diretório *de wwwroot* apenas de leitura. Existem vários benefícios para correr diretamente a partir de um pacote:

- Elimina conflitos de bloqueio de ficheiros entre a implementação e o tempo de funcionamento.
- Garante que apenas as aplicações totalmente implementadas estão a ser executadas a qualquer momento.
- Pode ser implantado numa aplicação de produção (com reinício).
- Melhora o desempenho das implementações do Azure Resource Manager.
- Pode reduzir os tempos de arranque a frio, particularmente para funções JavaScript com grandes árvores de pacote npm.

> [!NOTE]
> Atualmente, apenas os ficheiros de pacotes ZIP são suportados.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Permitir correr a partir de pacote

A `WEBSITE_RUN_FROM_PACKAGE` definição da aplicação permite correr a partir de um pacote. Para defini-lo, executar o seguinte comando com Azure CLI.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"` permite executar a sua aplicação de um pacote local para a sua aplicação. Também pode [correr a partir de um pacote remoto.](#run-from-external-url-instead)

## <a name="run-the-package"></a>Executar o pacote

A maneira mais fácil de executar um pacote no seu Serviço de Aplicações é com o comando [config-zip de implementação](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-zip) da fonte de implementação do Azure CLI az. Por exemplo:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Como a definição da `WEBSITE_RUN_FROM_PACKAGE` aplicação está definida, este comando não extrai o conteúdo do pacote para o *diretório D:\home\site\wwwroot* da sua aplicação. Em vez disso, carrega o ficheiro ZIP como é para *D:\home\data\SitePackages*, e cria uma *packagename.txt* no mesmo diretório, que contém o nome do pacote ZIP para carregar em tempo de execução. Se carregar o seu pacote ZIP de uma forma diferente (como [FTP),](deploy-ftp.md)tem de criar o diretório *D:\home\data\SitePackages* e o *ficheiropackagename.txt* manualmente.

O comando também reinicia a aplicação. Porque `WEBSITE_RUN_FROM_PACKAGE` está definido, o Serviço de Aplicações monta o pacote carregado como o diretório *de wwwroot* apenas de leitura e executa a aplicação diretamente a partir desse diretório montado.

## <a name="run-from-external-url-instead"></a>Executar a partir de URL externo em vez

Também pode executar um pacote a partir de um URL externo, como o Azure Blob Storage. Pode utilizar o [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) para enviar ficheiros de pacotes para a sua conta de armazenamento Blob. Deverá utilizar um recipiente de armazenamento privado com uma [Assinatura de Acesso Partilhado (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) para permitir que o serviço de aplicações aceda de forma segura ao pacote. 

Assim que fizer o upload do seu ficheiro para o armazenamento blob e tiver um URL SAS para o ficheiro, defina a definição da `WEBSITE_RUN_FROM_PACKAGE` aplicação para o URL. O exemplo que se segue fá-lo utilizando o Azure CLI:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Se publicar um pacote atualizado com o mesmo nome para o armazenamento Blob, tem de reiniciar a sua aplicação para que o pacote atualizado seja carregado no Serviço de Aplicações.

## <a name="troubleshooting"></a>Resolução de problemas

- Correr diretamente de um pacote faz `wwwroot` apenas leitura. A sua aplicação receberá um erro se tentar escrever ficheiros para este diretório.
- Os formatos TAR e GZIP não são suportados.
- Esta função não é compatível com [cache local.](overview-local-cache.md)
- Para melhorar o desempenho do arranque a frio, utilize a opção Zip local `WEBSITE_RUN_FROM_PACKAGE` (=1).

## <a name="more-resources"></a>Mais recursos

- [Implementação contínua para o Serviço de Aplicações Azure](deploy-continuous-deployment.md)
- [Implementar código com um ficheiro ZIP ou WAR](deploy-zip.md)
