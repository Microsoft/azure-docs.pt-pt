---
title: Utilizar o GitHub Actions para implementar um site estático no Armazenamento do Azure
description: Website estático de armazenamento Azure hospedado com ações do GitHub
author: juliakm
ms.service: storage
ms.topic: how-to
ms.author: jukullam
ms.reviewer: dineshm
ms.date: 01/11/2021
ms.subservice: blobs
ms.custom: devx-track-javascript, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: d8727bd747ef6d035cabbccf2ad42b80937a06a8
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180205"
---
# <a name="set-up-a-github-actions-workflow-to-deploy-your-static-website-in-azure-storage"></a>Crie um fluxo de trabalho de GitHub Actions para implementar o seu website estático no Azure Storage

Inicie com [as ações do GitHub](https://docs.github.com/en/free-pro-team@latest/actions) usando um fluxo de trabalho para implantar um site estático numa conta de armazenamento Azure. Uma vez configurado um fluxo de trabalho gitHub Actions, poderá implementar automaticamente o seu site para Azure a partir do GitHub quando escoar alterações ao código do seu site.

> [!NOTE]
> Se estiver a utilizar [aplicações web estáticas Azure](../../static-web-apps/index.yml), então não precisa de configurar manualmente um fluxo de trabalho gitHub Actions.
> A azure Static Web Apps cria automaticamente um fluxo de trabalho de GitHub Actions para si. 

## <a name="prerequisites"></a>Pré-requisitos

Uma subscrição da Azure e uma conta GitHub. 

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Um repositório GitHub com o seu código de site estático. Se não tiver uma conta GitHub, [inscreva-se gratuitamente.](https://github.com/join)  
- Um site estático de trabalho hospedado no Azure Storage. Saiba como [hospedar um website estático no Azure Storage](storage-blob-static-website-how-to.md). Para seguir este exemplo, também deverá implementar [a Azure CDN](static-website-content-delivery-network.md).

> [!NOTE]
> É comum utilizar uma rede de entrega de conteúdos (CDN) para reduzir a latência aos seus utilizadores em todo o mundo e reduzir o número de transações na sua conta de armazenamento. A implementação de conteúdo estático num serviço de armazenamento baseado na nuvem pode reduzir a necessidade de uma instância computacional potencialmente cara. Para obter mais informações, consulte [o padrão de hospedagem de conteúdo estático.](/azure/architecture/patterns/static-content-hosting)

## <a name="generate-deployment-credentials"></a>Gerar credenciais de implantação

Pode criar um [principal de serviço](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) com o comando [ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) no [Azure CLI](/cli/azure/). Executar este comando com [Azure Cloud Shell](https://shell.azure.com/) no portal Azure ou selecionando o botão **Try it.**

Substitua o espaço reservado `myStaticSite` pelo nome do seu site alojado no Azure Storage. 

```azurecli-interactive
   az ad sp create-for-rbac --name {myStaticSite} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
```

No exemplo acima, substitua os espaços reservados pelo seu ID de subscrição e nome de grupo de recursos. A saída é um objeto JSON com as credenciais de atribuição de funções que fornecem acesso à sua conta de armazenamento semelhante abaixo. Copie este objeto JSON para mais tarde.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> É sempre uma boa prática conceder o mínimo acesso. O âmbito no exemplo anterior está limitado à aplicação específica do Serviço de Aplicações e não a todo o grupo de recursos.

## <a name="configure-the-github-secret"></a>Configure o segredo do GitHub

1. No [GitHub,](https://github.com/)navegue no seu repositório.

1. Selecione **Definições > Segredos > Novo segredo**.

1. Cole toda a saída JSON do comando Azure CLI para o campo de valor do segredo. Dê ao segredo um nome `AZURE_CREDENTIALS` como.

    Quando configurar o ficheiro de fluxo de trabalho mais tarde, utilize o segredo para a entrada `creds` da ação Azure Login. Por exemplo:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

## <a name="add-your-workflow"></a>Adicione o seu fluxo de trabalho

1. Vá às **ações** para o seu repositório GitHub. 

    :::image type="content" source="media/storage-blob-static-website/storage-blob-github-actions-header.png" alt-text="Item do menu de ações do GitHub":::

1. Selecione **Configurar o seu fluxo de trabalho por si mesmo**. 

1. Elimine tudo após a `on:` secção do seu ficheiro de fluxo de trabalho. Por exemplo, o seu fluxo de trabalho restante pode ser assim. 

    ```yaml
    name: CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]
    ```

1. Mude o nome do seu fluxo de trabalho `Blob storage website CI` e adicione as ações de check-out e login. Estas ações irão verificar o código do seu site e autenticar-se com o Azure usando o `AZURE_CREDENTIALS` segredo GitHub que criou anteriormente. 

    ```yaml
    name: Blob storage website CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Utilize a ação Azure CLI para carregar o seu código para o armazenamento de bolhas e para limpar o seu ponto final CDN. Para `az storage blob upload-batch` , substitua o espaço reservado pelo nome da sua conta de armazenamento. O guião será enviado para o `$web` contentor. Para `az cdn endpoint purge` , substitua os espaços reservados pelo nome do seu perfil CDN, nome de ponto final CDN e grupo de recursos.

    ```yaml
        - name: Upload to blob storage
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
    ``` 

1. Complete o seu fluxo de trabalho adicionando uma ação ao logout da Azure. Aqui está o fluxo de trabalho completo. O ficheiro aparecerá na `.github/workflows` pasta do seu repositório.

    ```yaml
    name: Blob storage website CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}

        - name: Upload to blob storage
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
      
      # Azure logout 
        - name: logout
          run: |
                az logout
    ```

## <a name="review-your-deployment"></a>Reveja a sua implementação

1. Vá às **ações** para o seu repositório GitHub. 

1. Abra o primeiro resultado para ver os registos detalhados do funcionaamento do seu fluxo de trabalho. 
 
    :::image type="content" source="../media/index/github-actions-run.png" alt-text="Log of GitHub actions run":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando o seu site estático e o repositório GitHub já não forem necessários, limpe os recursos que implementou eliminando o grupo de recursos e o seu repositório GitHub. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre Azure Static Web Apps](../../static-web-apps/index.yml)