---
title: Configurar a implementação contínua
description: Saiba como permitir o serviço de aplicações ci/CD do GitHub, BitBucket, Azure Repos ou outros repos. Selecione o gasoduto de construção que se adequa às suas necessidades.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/03/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 5af8294518759181326e7736ef755f0a83581014
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564969"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Implementação contínua no Serviço de Aplicações do Azure

[O Azure App Service](overview.md) permite a implementação contínua a partir de repositórios [GitHub,](https://help.github.com/articles/create-a-repo) [BitBucket](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html)e [Azure Repos,](/azure/devops/repos/git/creatingrepo) puxando as últimas atualizações.

> [!NOTE]
> A página **do Centro de Desenvolvimento (Clássico)** no portal Azure, que é a antiga experiência de implantação, será depretada em março de 2021. Esta alteração não afetará quaisquer definições de implementação existentes na sua aplicação, podendo continuar a gerir a implementação de aplicações na página **do Centro de Implementação.**

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-deployment-source"></a>Fonte de implantação de configure

1. No [portal Azure,](https://portal.azure.com)navegue para a página de gestão da sua aplicação App Service.

1. A partir do menu esquerdo, clique em **Definições do Centro de**  >  **Implementação**. 

1. Em **Fonte**, selecione uma das opções de CI/CD.

    ![Mostra como escolher fonte de implantação no Centro de Implementação do Serviço de Aplicações Azure](media/app-service-continuous-deployment/choose-source.png)

Escolha o separador que corresponde à sua seleção para os passos.

# <a name="github"></a>[GitHub](#tab/github)

4. [GitHub Actions](#how-the-github-actions-build-provider-works) é o fornecedor de construção padrão. Para alterá-lo, clique em **Change provider**  >  **App Service Build Service** (Kudu) > **OK**.

    > [!NOTE]
    > Para utilizar a Azure Pipelines como fornecedor de construção para a sua aplicação De Serviço de Aplicações, não o configuure no Serviço de Aplicações. Em vez disso, configurar CI/CD diretamente dos Gasodutos Azure. A opção **Azure Pipelines apenas** aponta-o na direção certa.

1. Se estiver a ser implantado a partir do GitHub pela primeira vez, clique em **Autorizar** e siga as indicações de autorização. Se quiser implementar a partir do repositório de um utilizador diferente, clique em **Alterar Conta**.

1. Assim que autorizar a sua conta Azure com o GitHub, selecione a **Organização,** **Repositório** e **Branch** para configurar CI/CD para.

1. Quando o GitHub Actions for o fornecedor de construção escolhido, pode selecionar o ficheiro de fluxo de trabalho que pretende com a pilha de tempo de **execução** e as reduções de **versão.** O Azure compromete este ficheiro de fluxo de trabalho no seu repositório gitHub selecionado para lidar com tarefas de construção e implementação. Para ver o ficheiro antes de guardar as alterações, clique no **ficheiro Pré-visualização**.

    > [!NOTE]
    > O Serviço de Aplicações deteta a configuração da pilha de [idiomas](configure-common.md#configure-language-stack-settings) da sua aplicação e seleciona o modelo de fluxo de trabalho mais adequado. Se escolher um modelo diferente, poderá implementar uma aplicação que não funciona corretamente. Para mais informações, consulte [como funciona o fornecedor de construção de ações GitHub.](#how-the-github-actions-build-provider-works)

1. Clique em **Guardar**.
   
    Novos compromissos no repositório selecionado e ramo agora implementam-se continuamente na sua aplicação de Serviço de Aplicações. Pode rastrear os compromissos e implementações no **separador Registos.**

# <a name="bitbucket"></a>[BitBucket](#tab/bitbucket)

A integração BitBucket utiliza os Serviços de Construção de Serviços de Aplicação (Kudu) para a automação de construção.

4. Se estiver a implementar a partir do BitBucket pela primeira vez, clique em **Autorizar** e siga as indicações de autorização. Se quiser implementar a partir do repositório de um utilizador diferente, clique em **Alterar Conta**.

1. Para o Bitbucket, selecione a Bitbucket **Team,** **Repository** e **Branch** que pretende implementar continuamente.

1. Clique em **Guardar**.
   
    Novos compromissos no repositório selecionado e ramo agora implementam-se continuamente na sua aplicação de Serviço de Aplicações. Pode rastrear os compromissos e implementações no **separador Registos.**
   
# <a name="local-git"></a>[Git Local](#tab/local)

Consulte [a implementação do Git local para o Serviço de Aplicações Azure.](deploy-local-git.md)

# <a name="azure-repos"></a>[Repositórios do Azure](#tab/repos)

> [!NOTE]
> Azure Repos como fonte de implementação é suporte para aplicações windows.
>

4. O App Service Build Service (Kudu) é o fornecedor de construção padrão.

    > [!NOTE]
    > Para utilizar a Azure Pipelines como fornecedor de construção para a sua aplicação De Serviço de Aplicações, não o configuure no Serviço de Aplicações. Em vez disso, configurar CI/CD diretamente dos Gasodutos Azure. A opção **Azure Pipelines apenas** aponta-o na direção certa.

1. Selecione a **Azure DevOps Organization**, **Project,** **Repository** e **Branch** que pretende implementar continuamente. 

    Se a sua organização DevOps não estiver listada, ainda não está ligada à sua subscrição do Azure. Para obter mais informações, consulte [Criar uma ligação de serviço Azure](/azure/devops/pipelines/library/connect-to-azure).

-----

## <a name="disable-continuous-deployment"></a>Desativar a implementação contínua

1. No [portal Azure,](https://portal.azure.com)navegue para a página de gestão da sua aplicação App Service.

1. A partir do menu esquerdo, clique em **'Definições do Centro de**  >  **Implementação' Desconecte** as  >  **definições**. 

    ![Mostra como desligar a sincronização da pasta cloud com a sua aplicação De Serviço de Aplicações no portal Azure.](media/app-service-continuous-deployment/disable.png)

1. Por predefinição, o ficheiro de fluxo de trabalho gitHub Actions está preservado no seu repositório, mas continuará a desencadear a implementação da sua app. Para eliminá-lo do seu repositório, **selecione Eliminar o ficheiro de fluxo de trabalho**.

1. Clique em **OK**.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="how-the-github-actions-build-provider-works"></a>Como funciona o gitHub Actions build provider

O fornecedor de construção de ações GitHub é uma opção para [CI/CD do GitHub,](#configure-deployment-source)e faz o seguinte para configurar CI/CD:

- Deposita um ficheiro de fluxo de trabalho gitHub Actions no seu repositório GitHub para lidar com a construção e implementação de tarefas no Serviço de Aplicações.
- Adiciona o perfil de publicação da sua aplicação como um segredo do GitHub. O ficheiro workflow utiliza este segredo para autenticar com o Serviço de Aplicações.
- Captura informações a partir dos [registos](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) de execução do fluxo de trabalho e exibe-as no **separador Registos** no Centro de **Implantação** da sua aplicação.

Pode personalizar o fornecedor de build as GitHub Actions das seguintes formas:

- Personalize o ficheiro de fluxo de trabalho depois de gerado no seu repositório GitHub. Para obter mais informações, consulte [a sintaxe do fluxo de trabalho para as ações do GitHub](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Apenas certifique-se de que o fluxo de trabalho é implantado no Serviço de Aplicações com a ação [de implantação de azure/webapps.](https://github.com/Azure/webapps-deploy)
- Se o ramo selecionado estiver protegido, ainda pode pré-visualizar o ficheiro de fluxo de trabalho sem guardar a configuração e, em seguida, adicioná-lo manualmente no seu repositório. Este método não lhe dá a integração de registos com o portal Azure.
- Em vez de um perfil de publicação, implemente usando um [diretor de serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) no Azure Ative Directory.

#### <a name="authenticate-with-a-service-principal"></a>Autenticar com um diretor de serviço

1. Gerencie um diretor de serviço com o comando [ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) no [Azure CLI](/cli/azure/). No seguinte exemplo, *\<subscription-id>* *\<group-name>* substitua, e pelos *\<app-name>* seus próprios valores:

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > Para a segurança, conceder o mínimo de acesso necessário ao diretor de serviço. O âmbito no exemplo anterior está limitado à aplicação específica do Serviço de Aplicações e não a todo o grupo de recursos.
    
1. Guarde toda a saída JSON para o próximo passo, incluindo o nível `{}` superior.

1. No [GitHub,](https://github.com/)navegue no seu repositório, selecione **Definições > Segredos > Adicione um novo segredo**.

1. Cole toda a saída JSON do comando Azure CLI para o campo de valor do segredo. Dê ao segredo um nome `AZURE_CREDENTIALS` como.

1. No ficheiro de fluxo de trabalho gerado pelo **Centro de Implantação,** reveja o `azure/webapps-deploy` passo com código como o seguinte exemplo (que é modificado a partir de um ficheiro de fluxo de trabalho Node.js):

    ```yaml
    - name: Sign in to Azure 
    # Use the GitHub secret you added
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Deploy to Azure Web App
    # Remove publish-profile
    - uses: azure/webapps-deploy@v2
      with:
        app-name: '<app-name>'
        slot-name: 'production'
        package: .
    - name: Sign out of Azure
      run: |
        az logout
    ```
    
## <a name="deploy-from-other-repositories"></a>Implantação de outros repositórios

Para aplicações windows, pode configurar manualmente a implementação contínua a partir de um repositório de Nuvem Git ou Mercurial que o portal não suporta diretamente, como [o GitLab.](https://gitlab.com/) Fá-lo escolhendo o Git Externo no dropdown **Source.** Para obter mais informações, consulte [Configurar a implementação contínua utilizando passos manuais.](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)

## <a name="more-resources"></a>Mais recursos

* [Implementar de Azure Pipelines para Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)
* [Investigar questões comuns com implantação contínua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Utilizar o Azure PowerShell](/powershell/azure/)
* [Kudu do projeto](https://github.com/projectkudu/kudu/wiki)
