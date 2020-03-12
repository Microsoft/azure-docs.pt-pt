---
title: Execute as suas Funções Azure a partir de um pacote
description: Faça com que as Funções Azure executem as suas funções montando um ficheiro de pacote de implementação que contenha os ficheiros do projeto da aplicação de funções.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: 3ae287939f22469b03f0e10f184f067274464905
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087021"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Execute as suas Funções Azure a partir de um ficheiro de pacote

No Azure, pode executar as suas funções diretamente a partir de um ficheiro de pacote de implementação na sua aplicação de função. A outra opção é implementar os seus ficheiros no `d:\home\site\wwwroot` diretório da sua aplicação de funções.

Este artigo descreve os benefícios de executar as suas funções a partir de um pacote. Também mostra como ativar esta funcionalidade na sua aplicação de funções.

> [!IMPORTANT]
> Ao implementar as suas funções numa aplicação de função Linux num [plano Premium,](functions-scale.md#premium-plan)deve sempre executar a partir do ficheiro de pacote e publicar a [sua aplicação utilizando as Ferramentas Core funções do Azure](functions-run-local.md#project-file-deployment).

## <a name="benefits-of-running-from-a-package-file"></a>Benefícios de correr a partir de um ficheiro de pacote
  
Existem vários benefícios para correr a partir de um ficheiro de pacote:

+ Reduz o risco de problemas de bloqueio de cópias de ficheiros.
+ Pode ser implantado numa aplicação de produção (com reinício).
+ Pode ter a certeza dos ficheiros que estão a ser recorridos na sua aplicação.
+ Melhora o desempenho das implementações do Gestor de [Recursos Azure.](functions-infrastructure-as-code.md)
+ Pode reduzir os tempos de arranque a frio, particularmente para funções JavaScript com grandes árvores de embalagem npm.

Para mais informações, consulte [este anúncio.](https://github.com/Azure/app-service-announcements/issues/84)

## <a name="enabling-functions-to-run-from-a-package"></a>Permitindo que as funções funcionem a partir de um pacote

Para permitir que a sua aplicação de função seja executada a partir de um pacote, basta adicionar uma definição `WEBSITE_RUN_FROM_PACKAGE` às definições da sua aplicação de função. A definição `WEBSITE_RUN_FROM_PACKAGE` pode ter um dos seguintes valores:

| Valor  | Descrição  |
|---------|---------|
| **`1`**  | Recomendado para aplicações de funções em execução no Windows. Faça a partir de um ficheiro de pacote na pasta `d:\home\data\SitePackages` da sua aplicação de função. Se não [for implantada com a implementação](#integration-with-zip-deployment)do zip, esta opção requer que a pasta também tenha um ficheiro chamado `packagename.txt`. Este ficheiro contém apenas o nome do ficheiro de embalagem na pasta, sem qualquer espaço branco. |
|**`<URL>`**  | Localização de um ficheiro de pacote específico que pretende executar. Ao utilizar o armazenamento Blob, deve utilizar um recipiente privado com uma Assinatura de [Acesso Partilhado (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) para permitir que o tempo de funcionamento das funções tenha acesso à embalagem. Pode utilizar o [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) para fazer o upload de ficheiros de pacotes para a sua conta de armazenamento Blob. Quando especifica um URL, também deve [sincronizar os gatilhos](functions-deployment-technologies.md#trigger-syncing) depois de publicar um pacote atualizado. |

> [!CAUTION]
> Ao executar uma aplicação de função no Windows, a opção DEURL externa produz pior desempenho de arranque a frio. Ao implementar a sua aplicação de funções para o Windows, deverá definir `WEBSITE_RUN_FROM_PACKAGE` para `1` e publicar com a implementação do zip.

O seguinte mostra uma aplicação de função configurada para ser executada a partir de um ficheiro .zip hospedado no armazenamento Azure Blob:

![definição de aplicativo WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Atualmente, apenas os ficheiros de pacotes .zip são suportados.

## <a name="integration-with-zip-deployment"></a>Integração com implantação zip

[A implementação em zip][Zip deployment for Azure Functions] é uma funcionalidade do Azure App Service que permite implementar o seu projeto de aplicação de funções para o diretório `wwwroot`. O projeto é embalado como um ficheiro de implantação .zip. As mesmas APIs podem ser utilizadas para implantar o seu pacote na pasta `d:\home\data\SitePackages`. Com o valor de definição de `1`da aplicação `WEBSITE_RUN_FROM_PACKAGE`, as APIs de implantação zip copiam o seu pacote para a pasta `d:\home\data\SitePackages` em vez de extrair os ficheiros para `d:\home\site\wwwroot`. Também cria o ficheiro `packagename.txt`. Após um reinício, a embalagem é montada para `wwwroot` como um sistema de ficheiros apenas para leitura. Para obter mais informações sobre a implantação do zip, consulte a [implementação zip para funções Azure](deployment-zip-push.md).

## <a name="adding-the-website_run_from_package-setting"></a>Adicionando a definição de WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

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

- [Referências chave vault para serviço de aplicações](../app-service/app-service-key-vault-references.md)
- [Encriptação azure storage para dados em repouso](../storage/common/storage-service-encryption.md)

## <a name="troubleshooting"></a>Resolução de problemas

- Run From Package faz `wwwroot` apenas para leitura, para que receba um erro ao escrever ficheiros para este diretório.
- Os formatos de alcatrão e gzip não são suportados.
- Esta funcionalidade não se compõe com cache local.
- Para um melhor desempenho a frio, utilize a opção Zip local (`WEBSITE_RUN_FROM_PACKAGE`=1).
- Run From Package é incompatível com a opção de personalização de implementação (`SCM_DO_BUILD_DURING_DEPLOYMENT=true`), o passo de construção será ignorado durante a implementação.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Implementação contínua para Funções do Azure](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
