---
title: Tutorial - implementar a partir do GitHub para o serviço de aplicações do Azure com o Jenkins
description: Configurar o Jenkins para integração contínua (CI) partir do GitHub e a implementação contínua (CD) para o serviço de aplicações do Azure para aplicações web Java
services: jenkins
ms.service: jenkins
author: tomarchermsft
ms.author: tarcher
manager: jeconnoc
ms.topic: tutorial
ms.date: 11/15/2018
ms.openlocfilehash: d59a811b09d2c60627f7d506f1f5fd3513c914e8
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57408025"
---
# <a name="tutorial-deploy-from-github-to-azure-app-service-with-jenkins-continuous-integration-and-deployment"></a>Tutorial: Implementar a partir do GitHub App Service do Azure com a integração contínua Jenkins e implementação

Este tutorial implementa uma aplicação web de Java de exemplo do GitHub para [serviço de aplicações do Azure no Linux](/azure/app-service/containers/app-service-linux-intro) ao configurar a integração contínua (CI) e a implementação contínua (CD) no Jenkins. Quando atualizar a aplicação ao emitir consolidações para o GitHub, o Jenkins compila automaticamente e republishes a sua aplicação no App Service do Azure. A aplicação de exemplo neste tutorial, foi desenvolvida utilizando o [Spring Boot](http://projects.spring.io/spring-boot/) framework. 

![Descrição geral](media/tutorial-jenkins-deploy-web-app-azure-app-service/overview.png)

Neste tutorial, irá concluir estas tarefas:

> [!div class="checklist"]
> * Instalar plug-ins do Jenkins para que possa criar a partir do GitHub, implementar no serviço de aplicações do Azure e outros relacionados com tarefas.
> * Bifurcar o repositório do GitHub de exemplo para que tenha uma cópia de trabalho.
> * Ligar o Jenkins ao GitHub.
> * Crie um Azure principal de serviço para que Jenkins pode aceder ao Azure sem utilizar as suas credenciais.
> * Adicione o seu principal de serviço para o Jenkins.
> * Crie o pipeline de Jenkins, que cria e implementa a aplicação de exemplo sempre que atualizar a aplicação no GitHub.
> * Crie ficheiros de compilação e implementação para o pipeline do Jenkins.
> * Aponte o seu pipeline de Jenkins para o script de compilação e implementação.
> * Implemente a sua aplicação de exemplo para o Azure ao executar uma compilação manual.
> * Enviar por push uma atualização da aplicação no GitHub, que dispara o Jenkins para criar e Reimplementar no Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa destes itens:

* R [Jenkins](https://jenkins.io/) servidor com as ferramentas do Kit de desenvolvimento Java (JDK) e o Maven instalado numa VM do Linux do Azure

  Se não tiver um servidor do Jenkins, conclua estes passos agora no portal do Azure: [Criar servidor do Jenkins numa VM do Linux do Azure](/azure/jenkins/install-jenkins-solution-template)

* R [GitHub](https://github.com) conta para que possa obter uma cópia de trabalho (fork) para a aplicação de web de Java de exemplo. 

* [CLI do Azure](/cli/azure/install-azure-cli), que pode executar na sua linha de comando local ou [Azure Cloud Shell](/azure/cloud-shell/overview)

## <a name="install-jenkins-plug-ins"></a>Instalar os plug-ins do Jenkins

1. Entrar para a consola web do Jenkins nesta localização:

   `https://<Jenkins-server-name>.<Azure-region>.cloudapp.azure.com`

1. Na página principal do Jenkins, selecione **gerir Jenkins** > **gerir plug-ins**.

   ![Gerir plug-ins do Jenkins](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-plugins.png)

1. Sobre o **disponível** selecione esses plug-ins:

   - [Serviço de Aplicações do Azure](https://plugins.jenkins.io/azure-app-service)
   - [Origem do ramo do GitHub](https://plugins.jenkins.io/github-branch-source)
   - Jenkins [Plug-in Injetor de ambiente](https://plugins.jenkins.io/envinject)
   - [Credenciais do Azure](https://plugins.jenkins.io/azure-credentials)

   Se esses plug-ins não aparecer, certificar-se de que já não são instaladas, verificando a **instalada** separador.

1. Para instalar os plug-ins selecionados, escolha **agora a transferir e instalar após o reinício**.

1. Depois de terminar, no menu do Jenkins, selecione **gerir Jenkins** para que voltar para a página de gestão do Jenkins para futuras etapas.

## <a name="fork-sample-github-repo"></a>Bifurcar o repositório do GitHub de exemplo

1. [Inicie sessão no repositório do GitHub para a aplicação de exemplo de Spring Boot](https://github.com/spring-guides/gs-spring-boot). 

1. No canto superior-direito no GitHub, escolha **Fork**.

   ![Repositório de exemplo do fork do GitHub](media/tutorial-jenkins-deploy-web-app-azure-app-service/fork-github-repo.png)

1. Siga as instruções para selecionar a sua conta do GitHub e concluir a bifurcação.

Em seguida, configure o Jenkins com as suas credenciais do GitHub.

## <a name="connect-jenkins-to-github"></a>Ligar o Jenkins ao GitHub

Para que o Jenkins, GitHub de monitorizar e responder quando novas consolidações obterem enviadas para a sua aplicação web no seu fork do GitHub, ative [GitHub webhooks](https://developer.github.com/webhooks/) no Jenkins.

> [!NOTE]
> 
> Estes passos criam credenciais de token do Jenkins trabalhar com o GitHub com o seu nome de utilizador do GitHub e a palavra-passe de acesso pessoal. 
> No entanto, se a sua conta do GitHub utiliza a autenticação de dois fatores, criar o token no GitHub e configurar o Jenkins para utilizar esse token em vez disso. 
> Para obter mais informações, consulte a [Jenkins GitHub Plug-in](https://wiki.jenkins.io/display/JENKINS/GitHub+Plugin) documentação.

1. Partir do **gerir Jenkins** página, selecione **configurar o sistema**. 

   ![Configurar o sistema](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-configure-system.png)

1. Na **GitHub** secção, forneça detalhes para o seu servidor do GitHub. Do **Adicionar servidor do GitHub** lista, selecione **GitHub servidor**. 

   ![Adicionar servidor do GitHub](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-GitHub-server.png)

1. Se o **gerir ganchos** propriedade não estiver selecionada, selecione esta propriedade. Escolher **avançadas** , para que possa especificar outras definições. 

   ![Escolha "Avançadas" para mais definições](media/tutorial-jenkins-deploy-web-app-azure-app-service/advanced-GitHub-settings.png)

1. Partir do **gerir ações adicionais do GitHub** lista, selecione **converter o início de sessão e palavra-passe para o token**.

   ![Selecione "Gerir GitHub ações adicionais"](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-additional-actions.png)

1. Selecione **de início de sessão e palavra-passe** para que possa introduzir o nome de utilizador do GitHub e a palavra-passe. Quando tiver terminado, escolha **criar token credenciais**, que cria um [GitHub token de acesso pessoal (PAT)](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   

   ![Criar dar um TAPINHA GitHub a partir do início de sessão e palavra-passe](media/tutorial-jenkins-deploy-web-app-azure-app-service/create-github-token-credentials.png)

1. Na **servidor do GitHub** secção, do **credenciais** , selecione o token novo. Verificar que a autenticação está a funcionar ao escolher **Testar ligação**.

   ![Verifique a ligação ao servidor do GitHub com o novo PAT](media/tutorial-jenkins-deploy-web-app-azure-app-service/check-github-connection.png)

Em seguida, crie o Azure principal de serviço que utiliza o Jenkins para autenticar e aceder aos recursos do Azure.

## <a name="create-service-principal"></a>Criar um principal de serviço

Uma seção posterior, vai criar uma tarefa de pipeline do Jenkins que cria a aplicação a partir do GitHub e implementa a aplicação de serviço de aplicações do Azure. Para que o Jenkins sem introduzir as suas credenciais de acesso do Azure, crie uma [principal de serviço](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) no Azure Active Directory para o Jenkins. Um principal de serviço é uma identidade separada que pode utilizar o Jenkins para autenticar o acesso aos recursos do Azure. Para criar este principal de serviço, execute o comando da CLI do Azure [ **`az ad sp create-for-rbac`** ](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest), a partir do seu local de linha de comandos ou o Azure Cloud Shell, por exemplo: 

```azurecli-interactive
az ad sp create-for-rbac --name "yourAzureServicePrincipalName" --password yourSecurePassword
```

Certifique-se de que utilizar o nome principal de serviço entre aspas. Além disso, crie uma palavra-passe forte com base na [restrições e regras de palavra-passe do Azure Active Directory](/azure/active-directory/active-directory-passwords-policy). Se não fornecer uma palavra-passe, a CLI do Azure cria uma palavra-passe por si. 

Aqui está o resultado gerado pela **`create-for-rbac`** comando: 

```json
{
   "appId": "yourAzureServicePrincipal-ID", // A GUID such as AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA
   "displayName": "yourAzureServicePrincipalName", // A user-friendly name for your Azure service principal
   "name": "http://yourAzureServicePrincipalName",
   "password": "yourSecurePassword",
   "tenant": "yourAzureActiveDirectoryTenant-ID" // A GUID such as BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB
}
```

> [!TIP]
> 
> Se já tiver um principal de serviço, pode reutilizar essa identidade em vez disso.
> Ao fornecer valores de principal de serviço para autenticação, utilize o `appId`, `password`, e `tenant` valores de propriedade. 
> Ao pesquisar um principal de serviço, utilize o `displayName` valor da propriedade.

## <a name="add-service-principal-to-jenkins"></a>Adicionar principal de serviço para o Jenkins

1. Na página principal do Jenkins, selecione **credenciais** > **sistema**. 

1. Sobre o **System** página, em **domínio**, selecione **Global de credenciais (ilimitado)**.

1. No menu à esquerda, selecione **adicionar credenciais**.

1. Partir do **tipo** lista, selecione **Principal de serviço do Azure**.

1. Forneça as informações para a sua subscrição de principal e do Azure do serviço nas propriedades descritas pela tabela de neste passo:

   ![Adicionar credenciais de principal de serviço do Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-service-principal-credentials.png)

   | Propriedade | Value | Descrição | 
   |----------|-------|-------------| 
   | **ID de subscrição** | <*yourAzureSubscription-ID*> | O valor de GUID para a sua subscrição do Azure <p>**Sugestão**: Se não souber o ID de subscrição do Azure, execute este comando da CLI do Azure da linha de comandos ou no Cloud Shell e, em seguida, utilizar o `id` valor GUID: <p>`az account list` | 
   | **ID de cliente** | <*yourAzureServicePrincipal-ID*> | O `appId` valor GUID gerado anteriormente para o seu principal de serviço do Azure | 
   | **Segredo do cliente** | <*yourSecurePassword*> | O `password` valor ou o "segredo" fornecida para o seu principal de serviço do Azure | 
   | **ID do inquilino** | <*yourAzureActiveDirectoryTenant-ID*> | O `tenant` valor GUID para o seu inquilino do Azure Active Directory | 
   | **ID** | <*yourAzureServicePrincipalName*> | O `displayName` valor para o seu principal de serviço do Azure | 
   |||| 

1. Para confirmar que o seu principal de serviço funciona, escolha **Verifique se o Principal de serviço**. Quando tiver terminado, escolha **OK**.

Em seguida, crie o pipeline do Jenkins que cria e implementa a sua aplicação.

## <a name="create-jenkins-pipeline"></a>Criar o pipeline do Jenkins

No Jenkins, crie a tarefa de pipeline para criar e implementar a sua aplicação.

1. Voltar à sua home page do Jenkins e selecione **Novo Item**. 

   ![Selecione "Novo Item"](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-new-item.png)

1. Forneça um nome para a sua tarefa de pipeline, por exemplo, "Meu-Java-Web-App" e selecione **Pipeline**. Na parte inferior, escolha **OK**.  

   ![Selecione "Pipeline"](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-pipeline.png)

1. Configure o Jenkins com o seu principal de serviço para que o Jenkins pode implementar no Azure sem utilizar as suas próprias credenciais.

   1. Sobre o **gerais** separador, selecione **preparar um ambiente para a execução**. 

   1. Na **propriedades conteúdo** caixa apresentada, adicione estas variáveis de ambiente e os respetivos valores. 

      ```ini
      AZURE_CRED_ID=yourAzureServicePrincipalName
      RES_GROUP=yourWebAppAzureResourceGroupName
      WEB_APP=yourWebAppName
      ```

      ![Selecione "Preparar um ambiente para a execução" e definir variáveis de ambiente](media/tutorial-jenkins-deploy-web-app-azure-app-service/prepare-environment-for-run.png)

1. Quando tiver terminado, escolha **Save** (Guardar).

Em seguida, crie scripts de compilação e implementação para o Jenkins.

## <a name="create-build-and-deployment-files"></a>Criar ficheiros de compilação e implementação

Agora, crie os ficheiros que utiliza o Jenkins para criar e implementar a sua aplicação.

1. Do seu fork GitHub `src/main/resources/` pasta, criar este ficheiro de configuração de aplicação com o nome `web.config`, que contém esse XML, mas substitua `$(JAR_FILE_NAME)` com `gs-spring-boot-0.1.0.jar`:

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
      <system.webServer>
         <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
         </handlers>
         <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\${JAR_FILE_NAME}&quot;"></httpPlatform>
      </system.webServer>
   </configuration>
   ```

1. Na pasta de raiz do seu fork GitHub, crie este script de compilação e implementação com o nome `Jenkinsfile`, que contém este texto ([origem no GitHub aqui](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/doc/resources/jenkins/Jenkinsfile-webapp-se)):

   ```groovy
   node {
      stage('init') {
         checkout scm
      }
      stage('build') {
         sh '''
            mvn clean package
            cd target
            cp ../src/main/resources/web.config web.config
            cp todo-app-java-on-azure-1.0-SNAPSHOT.jar app.jar 
            zip todo.zip app.jar web.config
         '''
      }
      stage('deploy') {
         azureWebAppPublish azureCredentialsId: env.AZURE_CRED_ID,
         resourceGroup: env.RES_GROUP, appName: env.WEB_APP, filePath: "**/todo.zip"
      }
   }
   ```

1. Consolidar ambos `web.config` e `Jenkinsfile` ficheiros para o seu GitHub bifurcar e emita as alterações.

## <a name="point-pipeline-at-script"></a>Pipeline de ponto no script

Agora, especifique o script de compilação e implementação que pretende que o Jenkins para utilizar.

1. No Jenkins, selecione a tarefa de pipeline criado anteriormente. 

   ![Selecione a tarefa de pipeline para a sua aplicação web](media/tutorial-jenkins-deploy-web-app-azure-app-service/select-pipeline-job.png)

1. No menu da esquerda, selecione **configurar**.

1. Na **Pipeline** separador, da **definição** lista, selecione **Pipeline de script do SCM**.

   1. Na **SCM** caixa apresentada, selecione **Git** como seu controle de origem. 

   1. Na **repositórios** secção, para **URL do repositório**, introduza o URL do seu fork GitHub, por exemplo: 

      `https://github.com/<your-GitHub-username>/gs-spring-boot`

   1. Para **credenciais**, selecione o seu token de acesso pessoal do GitHub criado anteriormente.

   1. Na **caminho do Script** caixa, adicione o caminho para o script "Jenkinsfile".

   Quando tiver terminado, a definição de pipeline é semelhante a este exemplo: 

   ![Pipeline de ponto no script](media/tutorial-jenkins-deploy-web-app-azure-app-service/set-up-jenkins-github.png)

1. Quando tiver terminado, escolha **Save** (Guardar).

Em seguida, criar e implementar a aplicação no App Service do Azure. 

## <a name="build-and-deploy-to-azure"></a>Criar e implementar no Azure

1. Com a CLI do Azure, a partir da linha de comandos ou o Azure Cloud Shell, crie uma [aplicação de web do serviço de aplicações do Azure no Linux](/azure/app-service/containers/app-service-linux-intro) em que o Jenkins implementa a sua aplicação web depois de concluir uma compilação. Certifique-se de que a sua aplicação web tem um nome exclusivo.

   ```azurecli-interactive
   az group create --name yourWebAppAzureResourceGroupName --location yourAzureRegion
   az appservice plan create --name yourLinuxAppServicePlanName --resource-group yourWebAppAzureResourceGroupName --is-linux
   az webapp create --name yourWebAppName --resource-group yourWebAppAzureResourceGroupName --plan yourLinuxAppServicePlanName --runtime "java|1.8|Tomcat|8.5"
   ```

   Para obter mais informações sobre estes comandos da CLI do Azure, consulte estas páginas:

   * [**`az group create`**](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)

   * [**`az appservice plan create`**](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)

   * [**`az webapp create`**](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)

1. No Jenkins, selecione a tarefa de pipeline e selecione **compilar agora**.

   Após a conclusão da compilação, o Jenkins implementa a sua aplicação, que está agora em direto no Azure com o URL de publicação, por exemplo: 

   `http://<your-Java-web-app>.azurewebsites.net`

   ![Ver a aplicação implementada no Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-unedited.png)

## <a name="push-changes-and-redeploy"></a>Emitir alterações e reimplementar

1. No seu browser, aceda a esta localização no fork do GitHub da sua aplicação web:

   `complete/src/main/java/Hello/Application.java`
   
1. No canto superior direito no GitHub, escolha **editar este ficheiro**.

1. Efetuar esta alteração para o `commandLineRunner()` método e confirmar a alteração para o repositório `master` ramo. Esta consolidação no `master` ramo uma compilação é iniciada no Jenkins. 
   
   ```java
   System.out.println("Let's inspect the beans provided by Spring Boot on Azure");
   ```

1. Após a conclusão da compilação e reimplementa do Jenkins no Azure, atualize a aplicação, que mostra agora a sua atualização.

   ![Ver a aplicação implementada no Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-edited.png)

## <a name="troubleshooting-the-jenkins-plug-in"></a>O plug-in Jenkins de resolução de problemas

Se encontrar quaisquer erros com os plug-ins do Jenkins, comunique o assunto na [Jenkins JIRA](https://issues.jenkins-ci.org/) para o componente específico.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Use Azure VMs as build agents](/azure/jenkins/jenkins-azure-vm-agents) (Utilizar VMs do Azure como agentes de compilação)
