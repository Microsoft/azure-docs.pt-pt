---
title: Execute a sua aplicação a partir de um pacote ZIP
description: Implemente o pacote ZIP da sua aplicação com atómico. Melhore a previsibilidade e fiabilidade do comportamento da sua aplicação durante o processo de implementação do ZIP.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 316ada7700a5cf45ee90f515336039702bab48c0
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920727"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Execute a sua aplicação no Serviço de Aplicações Azure diretamente de um pacote ZIP

No [Azure App Service,](overview.md)pode executar as suas aplicações diretamente a partir de um ficheiro de pacote ZIP de implementação. Este artigo mostra como ativar esta funcionalidade na sua aplicação.

Todos os outros métodos de implementação no App Service têm algo em comum: os seus ficheiros são implantados para *D:\home\site\wwwroot* na sua app (ou */home/site/wwwroot* para aplicações Linux). Uma vez que o mesmo diretório é utilizado pela sua app em tempo de execução, é possível que a implementação falhe devido a conflitos de bloqueio de ficheiros, e para que a app se compore de forma imprevisível porque alguns dos ficheiros ainda não estão atualizados.

Em contrapartida, quando se executa diretamente de um pacote, os ficheiros da embalagem não são copiados para o diretório *wwwroot.* Em vez disso, o pacote ZIP em si é montado diretamente como o diretório *wwwroot* apenas de leitura. Existem vários benefícios para correr diretamente a partir de um pacote:

- Elimina os conflitos de bloqueio de ficheiros entre a implantação e o tempo de execução.
- Garante que apenas as aplicações totalmente implementadas estão a funcionar a qualquer momento.
- Pode ser implantado numa aplicação de produção (com reinício).
- Melhora o desempenho das implementações do Gestor de Recursos Azure.
- Pode reduzir os tempos de arranque a frio, particularmente para funções JavaScript com grandes árvores de embalagem npm.

> [!NOTE]
> Atualmente, apenas os ficheiros de pacotezip são suportados.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Ativar o funcionamento do pacote

A definição de aplicações `WEBSITE_RUN_FROM_PACKAGE` permite funcionar a partir de um pacote. Para o definir, execute o seguinte comando com o Azure CLI.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"` permite executar a sua aplicação de um pacote local para a sua aplicação. Também pode correr a [partir de um pacote remoto.](#run-from-external-url-instead)

## <a name="run-the-package"></a>Executar o pacote

The easiest way to run a package in your App Service is with the Azure CLI [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) command. Por exemplo:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Uma vez definida a definição de `WEBSITE_RUN_FROM_PACKAGE` aplicações, este comando não extrai o conteúdo do pacote para o *d:\home\site\wwwroot* diretório da sua aplicação. Em vez disso, ele envia o ficheiro ZIP como é para *D:\home\data\SitePackages*, e cria um *nome de pacote.txt* no mesmo diretório, que contém o nome do pacote ZIP para carregar no tempo de execução. Se fizer o upload do seu pacote ZIP de uma forma diferente (como [FTP),](deploy-ftp.md)tem de criar manualmente o diretório *D:\home\data\SitePackages* e o ficheiro *packagename.txt* manualmente.

O comando também reinicia a aplicação. Como `WEBSITE_RUN_FROM_PACKAGE` está definido, o App Service monta o pacote carregado como o diretório *wwwroot* apenas de leitura e executa a app diretamente a partir desse diretório montado.

## <a name="run-from-external-url-instead"></a>Executar a partir de URL externo em vez

Também pode executar uma embalagem a partir de um URL externo, como o Armazenamento De Blob Azure. Pode utilizar o [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) para fazer o upload de ficheiros de pacotes para a sua conta de armazenamento Blob. Deve utilizar um recipiente de armazenamento privado com uma Assinatura de [Acesso Partilhado (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) para permitir que o tempo de execução do Serviço de Aplicações aceda de forma segura à embalagem. 

Assim que enviar o seu ficheiro para o armazenamento blob e tiver um URL SAS para o ficheiro, defina a definição de aplicação `WEBSITE_RUN_FROM_PACKAGE` para o URL. O exemplo seguinte fá-lo utilizando o Azure CLI:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Se publicar um pacote atualizado com o mesmo nome para o armazenamento Blob, precisa de reiniciar a sua aplicação para que o pacote atualizado seja carregado no Serviço de Aplicações.

### <a name="use-key-vault-references"></a>Utilizar referências de cofre de chaves

Para uma maior segurança, pode utilizar referências de cofre de chave em conjunto com o seu URL externo. Isto mantém o URL encriptado em repouso e permite alavancar o Key Vault para gestão e rotação secretas. Recomenda-se utilizar o armazenamento Azure Blob para que possa rodar facilmente a tecla SAS associada. O armazenamento do Azure Blob é encriptado em repouso, o que mantém os dados da sua aplicação seguros quando não é implementado no Serviço de Aplicações.

1. Crie um cofre de chaves Azure.

    ```azurecli
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus
    ```

1. Adicione o seu URL externo como um segredo no Cofre chave.

    ```azurecli
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<insert-your-URL>"
    ```

1. Crie a definição de aplicações `WEBSITE_RUN_FROM_PACKAGE` e defina o valor como uma referência de cofre chave ao URL externo.

    ```azurecli
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"
    ```

Consulte os seguintes artigos para obter mais informações.

- [Referências chave vault para serviço de aplicações](app-service-key-vault-references.md)
- [Encriptação azure storage para dados em repouso](../storage/common/storage-service-encryption.md)

## <a name="troubleshooting"></a>Resolução de problemas

- Correr diretamente de um pacote faz `wwwroot` apenas para leitura. A sua aplicação receberá um erro se tentar escrever ficheiros para este diretório.
- Os formatos TAR e GZIP não são suportados.
- Esta função não é compatível com [cache local](overview-local-cache.md).
- Para um melhor desempenho a frio, utilize a opção Zip local (`WEBSITE_RUN_FROM_PACKAGE`=1).

## <a name="more-resources"></a>Mais recursos

- [Implantação contínua para o Serviço de Aplicações Azure](deploy-continuous-deployment.md)
- [Implementar código com um ficheiro ZIP ou WAR](deploy-zip.md)
