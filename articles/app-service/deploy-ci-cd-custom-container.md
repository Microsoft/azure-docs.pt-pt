---
title: CI/CD para recipientes personalizados
description: Confiúde a implementação contínua para um recipiente personalizado windows ou Linux no Azure App Service.
keywords: serviço de aplicativos azure, linux, docker, acr,oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 03/12/2021
ms.author: msangapu
ms.custom: seodec18
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 654b0f842a3165926242d1ef03f2dfe4e5bacfdc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643344"
---
# <a name="continuous-deployment-with-custom-containers-in-azure-app-service"></a>Implantação contínua com recipientes personalizados no Azure App Service

Neste tutorial, configura a implementação contínua para uma imagem personalizada de um recipiente a partir de repositórios geridos do [Registo de Contentores Azure](https://azure.microsoft.com/services/container-registry/) ou [do Docker Hub.](https://hub.docker.com)

## <a name="1-go-to-deployment-center"></a>1. Ir para o Centro de Implantação

No [portal Azure,](https://portal.azure.com)navegue para a página de gestão da sua aplicação App Service.

A partir do menu esquerdo, clique em **Definições do Centro de**  >  **Implementação**. 

::: zone pivot="container-linux"
## <a name="2-choose-deployment-source"></a>2. Escolha a fonte de implantação

**Escolha** a fonte de implantação depende do seu cenário:
- **O registo do contentor** configura o CI/CD entre o registo do contentor e o Serviço de Aplicações.
- A opção **GitHub Actions** é para si se mantiver o código fonte para a sua imagem de contentor no GitHub. Desencadeado por novos compromissos com o seu repositório GitHub, a ação de implementação pode ser executada `docker build` e `docker push` diretamente para o seu registo de contentores, atualizando depois a sua aplicação de Serviço de Aplicações para executar a nova imagem. Para obter mais informações, consulte [como o CI/CD funciona com as ações do GitHub](#how-cicd-works-with-github-actions).
- Para configurar o CI/CD com **os Gasodutos Azure,** consulte [implementar um recipiente de aplicações web Azure a partir de Gasodutos Azure](/azure/devops/pipelines/targets/webapp-on-container-linux).

> [!NOTE]
> Para uma aplicação Docker Compose, selecione **Registo de Contentores.**

Se escolher As Ações Do GitHub, **clique em** **'Autorizor'** e siga as indicações de autorização. Se já autorizou com o GitHub, pode implementar a partir do repositório de um utilizador diferente clicando na **Conta Change**.

Assim que autorizar a sua conta Azure com o GitHub, **selecione** a **Organização,** **Repositório** e **Branch** para implementar.
::: zone-end  

::: zone pivot="container-windows"
## <a name="2-configure-registry-settings"></a>2. Configurar as definições de registo
::: zone-end  
::: zone pivot="container-linux"
## <a name="3-configure-registry-settings"></a>3. Configurar as definições de registo

Para implementar uma aplicação multi-contentores (Docker Compose), **selecione** **Docker Compose** in **Container Type**.

Se não vir o **dropdown do tipo de recipiente,** volte para a **Fonte** e **selecione** **o Registo de Contentores**.
::: zone-end

Na **fonte de registo,** **selecione** onde está o seu registo de contentores. Se não for nem registo de contentores Azure nem Docker Hub, **selecione** **Registo Privado**.

::: zone pivot="container-linux"
> [!NOTE]
> Se a sua aplicação multi-contentor (Docker Compose) utilizar mais do que uma imagem privada, certifique-se de que as imagens privadas estão no mesmo registo privado e acessíveis com as mesmas credenciais de utilizador. Se a sua aplicação multi-contentores utilizar apenas imagens públicas, **selecione** **Docker Hub**, mesmo que algumas imagens não estejam no Docker Hub.
::: zone-end  

Siga os próximos passos selecionando o separador que corresponde à sua escolha.

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

O **dropdown do Registo** exibe os registos na mesma subscrição que a sua aplicação. **Selecione** o registo que deseja.

> [!NOTE]
> Para implantar a partir de um registo numa subscrição diferente, **selecione** **Registo Privado** na fonte do **Registo.**

::: zone pivot="container-windows"
**Selecione** a **Imagem** e **a Etiqueta** para implementar. Se desejar, **digite** o comando de arranque no **Ficheiro de Arranque**. 
::: zone-end
::: zone pivot="container-linux"
Siga o passo seguinte dependendo do **Tipo de Recipiente:**
- Para **Docker Compose,** **selecione** o registo para as suas imagens privadas. **Clique** **em Escolher o ficheiro** para fazer o upload do seu ficheiro Docker [Compose](https://docs.docker.com/compose/compose-file/)ou basta **colar** o conteúdo do seu ficheiro Docker Compose para **Config**.
- Para **um único recipiente,** **selecione** a **imagem** e a **etiqueta** para implantar. Se desejar, **digite** o comando de arranque no **Ficheiro de Arranque**. 
::: zone-end

O Serviço de Aplicações anexa a cadeia no **Ficheiro de Arranque** até ao fim do comando [ `docker run` (como o `[COMMAND] [ARG...]` segmento)](https://docs.docker.com/engine/reference/run/) ao iniciar o seu contentor.

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

::: zone pivot="container-windows"
No **Repository Access,** **selecione** se a imagem a implementar é pública ou privada.
::: zone-end
::: zone pivot="container-linux"
No **Repository Access,** **selecione** se a imagem a implementar é pública ou privada. Para uma aplicação Docker Compose com uma ou mais imagens privadas, **selecione** **Private**.
::: zone-end

Se selecionar uma imagem privada, **especifique** o **Login** (nome de utilizador) e a **Palavra-passe** da conta Docker.

::: zone pivot="container-windows"
**Forneça** a imagem e o nome da etiqueta em **Nome e Etiqueta de Imagem Completa,** separados por um `:` (por exemplo, `nginx:latest` ). Se desejar, **digite** o comando de arranque no **Ficheiro de Arranque**. 
::: zone-end
::: zone pivot="container-linux"
Siga o passo seguinte dependendo do **Tipo de Recipiente:**
- Para **Docker Compose,** **selecione** o registo para as suas imagens privadas. **Clique** **em Escolher o ficheiro** para fazer o upload do seu ficheiro Docker [Compose](https://docs.docker.com/compose/compose-file/)ou basta **colar** o conteúdo do seu ficheiro Docker Compose para **Config**.
- Para **um único recipiente,** **forneça** a imagem e o nome da etiqueta em Nome e Etiqueta de **Imagem Completo,** separados por um `:` (por exemplo, `nginx:latest` ). Se desejar, **digite** o comando de arranque no **Ficheiro de Arranque**. 
::: zone-end

O Serviço de Aplicações anexa a cadeia no **Ficheiro de Arranque** até ao fim do comando [ `docker run` (como o `[COMMAND] [ARG...]` segmento)](https://docs.docker.com/engine/reference/run/) ao iniciar o seu contentor.

# <a name="private-registry"></a>[Registo Privado](#tab/private)

No **URL do servidor,** **digite** o URL do servidor, começando com **https://**.

No **Início de Sessão** e **na Palavra-passe** **escreva** as suas credenciais de login para o seu registo privado.

::: zone pivot="container-windows"
**Forneça** a imagem e o nome da etiqueta em **Nome e Etiqueta de Imagem Completa,** separados por um `:` (por exemplo, `nginx:latest` ). Se desejar, **digite** o comando de arranque no **Ficheiro de Arranque**. 
::: zone-end
::: zone pivot="container-linux"
Siga o passo seguinte dependendo do **Tipo de Recipiente:**
- Para **Docker Compose,** **selecione** o registo para as suas imagens privadas. **Clique** **em Escolher o ficheiro** para fazer o upload do seu ficheiro Docker [Compose](https://docs.docker.com/compose/compose-file/)ou basta **colar** o conteúdo do seu ficheiro Docker Compose para **Config**.
- Para **um único recipiente,** **forneça** a imagem e o nome da etiqueta em Nome e Etiqueta de **Imagem Completo,** separados por um `:` (por exemplo, `nginx:latest` ). Se desejar, **digite** o comando de arranque no **Ficheiro de Arranque**. 
::: zone-end

O Serviço de Aplicações anexa a cadeia no **Ficheiro de Arranque** até ao fim do comando [ `docker run` (como o `[COMMAND] [ARG...]` segmento)](https://docs.docker.com/engine/reference/run/) ao iniciar o seu contentor.

-----

::: zone pivot="container-windows"
## <a name="3-enable-cicd"></a>3. Ativar o CI/CD
::: zone-end
::: zone pivot="container-linux"
## <a name="4-enable-cicd"></a>4. Ativar o CI/CD
::: zone-end

O Serviço de Aplicações suporta a integração CI/CD com o Registo de Contentores Azure e o Docker Hub. Para o ativar, **selecione** **On** in **Continuous deployment**.

::: zone pivot="container-linux"
> [!NOTE]
> Se selecionar **GitHub Actions** in **Source,** não obtém esta opção porque o CI/CD é tratado diretamente pelas Ações GitHub. Em vez disso, vê uma secção **de Configuração do Fluxo de Trabalho,** onde pode **clicar em** ficheiro **de pré-visualização** para inspecionar o ficheiro de fluxo de trabalho. O Azure compromete este ficheiro no seu repositório de origem GitHub selecionado para lidar com tarefas de construção e implementação. Para obter mais informações, consulte [como o CI/CD funciona com as ações do GitHub](#how-cicd-works-with-github-actions).
::: zone-end

Ao ativar esta opção, o App Service adiciona um webhook ao seu repositório no Registo de Contentores Azure ou no Docker Hub. As suas publicações de repositório para este webhook sempre que a sua imagem selecionada for atualizada com `docker push` . O webhook faz com que a sua aplicação de Serviço de Aplicações reinicie e corra `docker pull` para obter a imagem atualizada.

**Para outros registos privados,** pode colocar-se no webhook manualmente ou como um passo num pipeline CI/CD. No **URL Webhook,** **clique** no botão **Copiar** para obter o URL webhook.

::: zone pivot="container-linux"
> [!NOTE]
> O suporte para aplicações multi-contentores (Docker Compose) é limitado: 
> - Para o Registo de Contentores Azure, o Serviço de Aplicações cria um webhook no registo selecionado com o registo como âmbito. A `docker push` a qualquer repositório no registo (incluindo os não referenciados pelo seu ficheiro Docker Compose) desencadeia um reinício da aplicação. É possível que queira [modificar o webhook](../container-registry/container-registry-webhook.md) para um âmbito mais restrito.
> - Docker Hub não suporta webhooks ao nível do registo. Tem de **adicionar** os webhooks manualmente às imagens especificadas no seu ficheiro Docker Compose.
::: zone-end

::: zone pivot="container-windows"
## <a name="4-save-your-settings"></a>4. Guarde as suas definições
::: zone-end
::: zone pivot="container-linux"
## <a name="5-save-your-settings"></a>5. Guarde as suas definições
::: zone-end

**Clique** **em Guardar**.

::: zone pivot="container-linux"

## <a name="how-cicd-works-with-github-actions"></a>Como o CI/CD funciona com as ações do GitHub

Se escolher **as ações do GitHub** na **Fonte** (ver Escolha fonte [de implementação),](#2-choose-deployment-source)o Serviço de Aplicações configura o CI/CD das seguintes formas:

- Deposita um ficheiro de fluxo de trabalho gitHub Actions no seu repositório GitHub para lidar com a construção e implementação de tarefas no Serviço de Aplicações.
- Adiciona as credenciais para o seu registo privado como segredos do GitHub. O ficheiro de fluxo de trabalho gerado executa a ação [de login Azure/docker-para](https://github.com/Azure/docker-login) iniciar sessão com o seu registo privado e, em seguida, corre `docker push` para o implementar.
- Adiciona o perfil de publicação da sua aplicação como um segredo do GitHub. O ficheiro de fluxo de trabalho gerado utiliza este segredo para autenticar com o Serviço de Aplicações, executando a ação de implementação do [Azure/webapps](https://github.com/Azure/webapps-deploy) para configurar a imagem atualizada, que desencadeia um reinício de aplicações para puxar a imagem atualizada.
- Captura informações a partir dos [registos](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) de execução do fluxo de trabalho e exibe-as no **separador Registos** no Centro de **Implantação** da sua aplicação.

Pode personalizar o fornecedor de build as GitHub Actions das seguintes formas:

- Personalize o ficheiro de fluxo de trabalho depois de gerado no seu repositório GitHub. Para obter mais informações, consulte [a sintaxe do fluxo de trabalho para as ações do GitHub](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Apenas certifique-se de que o fluxo de trabalho termina com a ação de implementação do [Azure/webapps](https://github.com/Azure/webapps-deploy) para desencadear um reinício da aplicação.
- Se o ramo selecionado estiver protegido, ainda pode pré-visualizar o ficheiro de fluxo de trabalho sem guardar a configuração, em seguida, adicioná-lo e os segredos gitHub necessários no seu repositório manualmente. Este método não lhe dá a integração de registos com o portal Azure.
- Em vez de um perfil de publicação, implemente usando um [diretor de serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) no Azure Ative Directory.

#### <a name="authenticate-with-a-service-principal"></a>Autenticar com um diretor de serviço

Esta configuração opcional substitui a autenticação predefinida por perfis de publicação no ficheiro de fluxo de trabalho gerado.

**Gerencie** um diretor de serviço com o comando [ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) no [Azure CLI](/cli/azure/). No exemplo seguinte, *\<subscription-id>* *\<group-name>* substitua, e pelos *\<app-name>* seus próprios valores. **Guarde** toda a saída JSON para o próximo passo, incluindo o nível `{}` superior.

```azurecli-interactive
az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

> [!IMPORTANT]
> Para a segurança, conceder o mínimo de acesso necessário ao diretor de serviço. O âmbito no exemplo anterior está limitado à aplicação específica do Serviço de Aplicações e não a todo o grupo de recursos.

No [GitHub,](https://github.com/)navegue pelo seu **repositório** e, em seguida, **selecione** **Definições > Segredos > Adicione um novo segredo**. **Cole** toda a saída JSON do comando Azure CLI para o campo de valor do segredo. **Dê** ao segredo um nome `AZURE_CREDENTIALS` como.

No ficheiro de fluxo de trabalho gerado pelo **Centro de Implantação,** **reveja** o `azure/webapps-deploy` passo com código como o seguinte exemplo:

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
    images: '<registry-server>/${{ secrets.AzureAppService_ContainerUsername_... }}/<image>:${{ github.sha }}'
    - name: Sign out of Azure
    run: |
    az logout
```

::: zone-end

## <a name="automate-with-cli"></a>Automatize com CLI

Para configurar o registo do contentor e a imagem do Docker, **executar** [o conjunto de recipientes de config az webapp](/cli/azure/webapp/config/container#az-webapp-config-container-set).

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url 'https://<registry-name>.azurecr.io' --docker-registry-server-user '<username>' --docker-registry-server-password '<password>'
```

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

```azurecli-interactive
# Public image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name>

# Private image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

# <a name="private-registry"></a>[Registo Privado](#tab/private)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

-----

::: zone pivot="container-linux"
Para configurar uma aplicação multi-contentor (Docker Compose), **prepare** um ficheiro Docker Compose localmente e, em seguida, **executar** [um conjunto de recipiente de config webapp az](/cli/azure/webapp/config/container#az-webapp-config-container-set) com o `--multicontainer-config-file` parâmetro. Se o seu ficheiro Docker Compose contiver  `--docker-registry-server-*` imagens privadas, adicione parâmetros como mostrado no exemplo anterior.

```azurecli-interactive
az webapp config container set --resource-group <group-name> --name <app-name> --multicontainer-config-file <docker-compose-file>
```
::: zone-end

Para configurar o CI/CD do registo do contentor para a sua aplicação, **executar** [o recipiente de implantação az webapp configurar](/cli/azure/webapp/deployment/container#az-webapp-deployment-container-config) com o `--enable-cd` parâmetro. O comando sai do URL webhook, mas tem de criar o webhook no seu registo manualmente num passo separado. O exemplo a seguir permite o CI/CD na sua aplicação e, em seguida, utiliza o URL webhook na saída para criar o webhook no Registo do Contentor de Azure.

```azurecli-interactive
ci_cd_url=$(az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true --query CI_CD_URL --output tsv)

az acr webhook create --name <webhook-name> --registry <registry-name> --resource-group <group-name> --actions push --uri $ci_cd_url --scope '<image>:<tag>'
```

## <a name="more-resources"></a>Mais recursos

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Criar uma aplicação Web .NET Core no Serviço de Aplicações no Linux](quickstart-dotnetcore.md)
* [Quickstart: Executar um recipiente personalizado no Serviço de Aplicações](quickstart-custom-container.md)
* [FAQ do Serviço de Aplicações no Linux](faq-app-service-linux.md)
* [Configure recipientes personalizados](configure-custom-container.md)
* [Fluxos de trabalho de ações para implantar para Azure](https://github.com/Azure/actions-workflow-samples)
