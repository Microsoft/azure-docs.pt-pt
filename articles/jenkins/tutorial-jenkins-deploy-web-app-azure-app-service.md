---
title: 'Tutorial: Desdobre do GitHub para o Azure App Service com Jenkins'
description: Configurar jenkins para integração contínua (CI) do GitHub e implementação contínua (CD) para O Serviço de Aplicações Azure para aplicações web Java
ms.topic: tutorial
ms.date: 10/23/2019
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 9fcf178b71ac1f07bfb58cd2502701ae5392b472
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74158399"
---
# <a name="tutorial-deploy-from-github-to-azure-app-service-with-jenkins-continuous-integration-and-deployment"></a>Tutorial: Implemente do GitHub para o Azure App Service com integração e implantação contínuas de Jenkins

Este tutorial implementa uma aplicação web Java de GitHub para [Azure App Service no Linux,](/azure/app-service/containers/app-service-linux-intro) através da criação de integração contínua (CI) e implantação contínua (CD) em Jenkins. Ao atualizar a aplicação empurrando-se para o GitHub, a Jenkins constrói e reedita automaticamente a sua aplicação para o Azure App Service. A aplicação de amostras neste tutorial foi desenvolvida utilizando a estrutura da Bota de [primavera.](https://projects.spring.io/spring-boot/) 

![Visão geral de implementação do Serviço de Aplicações GitHub para Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/azure-continuous-integration-deployment-overview.png)

Neste tutorial, completará estas tarefas:

> [!div class="checklist"]
> * Instale plug-ins Jenkins para que possa construir a partir do GitHub, implementar para o Serviço de Aplicações Azure e outras tarefas relacionadas.
> * Fork the sample GitHub repo para que você tenha uma cópia de trabalho.
> * Ligue jenkins ao GitHub.
> * Crie um diretor de serviço Azure para que o Jenkins possa aceder ao Azure sem usar as suas credenciais.
> * Adicione o seu diretor de serviço ao Jenkins.
> * Crie o oleoduto Jenkins que constrói e implementa a aplicação de amostras sempre que atualizar a aplicação no GitHub.
> * Crie ficheiros de construção e implementação para o seu oleoduto Jenkins.
> * Aponte o seu oleoduto Jenkins para o guião de construção e implantação.
> * Implemente a sua aplicação de amostra seletiva para o Azure executando uma construção manual.
> * Empurre uma atualização de aplicativo no GitHub, o que despoleta jenkins para construir e recolocar para o Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa destes itens:

* Um servidor [Jenkins](https://jenkins.io/) com o Java Development Kit (JDK) e ferramentas Maven instalados num VM Azure Linux

  Se não tiver um servidor Jenkins, complete estes passos agora no portal Azure: Crie o [servidor Jenkins num VM Azure Linux](/azure/jenkins/install-jenkins-solution-template)

* Uma conta [GitHub](https://github.com) para que possa obter uma cópia de trabalho (garfo) para a aplicação web java da amostra. 

* [Azure CLI](/cli/azure/install-azure-cli), que pode executar a partir da sua linha de comando local ou [Azure Cloud Shell](/azure/cloud-shell/overview)

## <a name="install-jenkins-plug-ins"></a>Instalar os plug-ins do Jenkins

1. Inscreva-se na sua consola web Jenkins neste local:

   `https://<Jenkins-server-name>.<Azure-region>.cloudapp.azure.com`

1. Na página principal do Jenkins, selecione **Manage Jenkins** > **Manage Plugins**.

   ![Gerir os plug-ins do Jenkins](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-plugins.png)

1. No separador **Disponível,** selecione estes plug-ins:

   - [Serviço de Aplicações do Azure](https://plugins.jenkins.io/azure-app-service)
   - [Fonte de filial GitHub](https://plugins.jenkins.io/github-branch-source)
   - [Plug-in do injetor de ambiente](https://plugins.jenkins.io/envinject) Jenkins
   - [Credenciais do Azure](https://plugins.jenkins.io/azure-credentials)

   Se estes plug-ins não aparecerem, certifique-se de que ainda não estão instalados verificando o separador **Instalado.**

1. Para instalar os plug-ins selecionados, selecione **Descarregue agora e instale depois de reiniciar**.

1. Depois de terminar, no menu Jenkins, selecione **Manage Jenkins** para que volte à página de gestão jenkins para passos futuros.

## <a name="fork-sample-github-repo"></a>Amostra de garfo GitHub repo

1. [Inscreva-se no repo gitHub para a aplicação de amostra](https://github.com/spring-guides/gs-spring-boot)sino de primavera . 

1. No canto superior direito em GitHub, selecione **Fork**.

   ![Repo de amostra de garfo do GitHub](media/tutorial-jenkins-deploy-web-app-azure-app-service/fork-github-repo.png)

1. Siga as instruções para selecionar a sua conta GitHub e terminar a marcação.

Em seguida, prepara o Jenkins com as tuas credenciais gitHub.

## <a name="connect-jenkins-to-github"></a>Ligue Jenkins ao GitHub

Para que o Jenkins monitore o GitHub e responda quando os novos compromissos forem empurrados para a sua aplicação web no seu garfo GitHub, ative os [webhooks gitHub](https://developer.github.com/webhooks/) em Jenkins.

> [!NOTE]
> 
> Estes passos criam credenciais de acesso pessoal para jenkins trabalhar com o GitHub usando o seu nome de utilizador GitHub e senha. 
> No entanto, se a sua conta GitHub usar a autenticação de dois fatores, crie o seu símbolo no GitHub e crie o Jenkins para usar esse símbolo. 
> Para mais informações, consulte a documentação [de plug-in jenkins GitHub.](https://wiki.jenkins.io/display/JENKINS/GitHub+Plugin)

1. A partir da página **Manage Jenkins,** selecione **Configure System**. 

   ![Sistema de configuração em Jenkins](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-configure-system.png)

1. Na secção **GitHub,** forneça detalhes para o seu servidor GitHub. A partir da lista **add GitHub Server,** selecione **GitHub Server**. 

   ![Adicione o servidor GitHub em Jenkins](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-GitHub-server.png)

1. Se a propriedade **Manage hooks** não estiver selecionada, selecione esta propriedade. Selecione **Advanced** para que possa especificar outras definições. 

   ![Especificar definições avançadas de Jenkins para o Servidor GitHub](media/tutorial-jenkins-deploy-web-app-azure-app-service/advanced-GitHub-settings.png)

1. A partir da lista de ações adicionais do **GitHub,** selecione **Converter login e senha para token**.

   ![Converter o login e a palavra-passe em ficha para o GitHub](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-additional-actions.png)

1. Selecione **Entre sessão e senha** para que possa introduzir o seu nome de utilizador GitHub e palavra-passe. Quando terminar, selecione **Criar credenciais de token,** o que cria um token de [acesso pessoal GitHub (PAT)](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   

   ![Criar PAT do GitHub a partir de início de sessão e de palavra-passe](media/tutorial-jenkins-deploy-web-app-azure-app-service/create-github-token-credentials.png)

1. Na secção **GitHub Server,** a partir da lista **de Credenciais,** selecione o seu novo token. Verifique se a autenticação está a funcionar escolhendo a **ligação test**.

   ![Verifique a ligação ao servidor GitHub com o novo PAT](media/tutorial-jenkins-deploy-web-app-azure-app-service/check-github-connection.png)

Em seguida, crie o diretor de serviço Azure que jenkins usa para autenticar e aceder aos recursos Azure.

## <a name="create-service-principal"></a>Criar um principal de serviço

Numa secção posterior, cria-se um pipeline da Jenkins que constrói a sua aplicação a partir do GitHub e implementa a sua aplicação para o Azure App Service. Para que o Jenkins aceda ao Azure sem introduzir as suas credenciais, crie um diretor de [serviço](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) no Azure Ative Directory para o Jenkins. Um diretor de serviço é uma identidade separada que jenkins pode usar para autenticar o acesso aos recursos Azure. Para criar este diretor de serviço, [**`az ad sp create-for-rbac`**](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)execute o comando Azure CLI, quer a partir da sua linha de comando local quer da Azure Cloud Shell, por exemplo: 

```azurecli-interactive
az ad sp create-for-rbac --name "yourAzureServicePrincipalName" --password yourSecurePassword
```

Certifique-se de que utiliza aspas em torno do nome principal do serviço. Além disso, crie uma palavra-passe forte com base nas regras e restrições de senha do [Diretório Ativo Azure.](/azure/active-directory/active-directory-passwords-policy) Se não fornecer uma senha, o Azure CLI cria uma palavra-passe para si. 

Aqui está a saída gerada pelo **`create-for-rbac`** comando: 

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
> Se já tem um diretor de serviço, pode reutilizar essa identidade.
> Ao fornecer os principais valores `appId`de `password`serviço `tenant` para a autenticação, utilize os valores de propriedade e , use. 
> Ao procurar um diretor de serviço `displayName` existente, utilize o valor da propriedade.

## <a name="add-service-principal-to-jenkins"></a>Adicione o principal de serviço a Jenkins

1. Na página principal do Jenkins, selecione**Sistema** **de Credenciais** > . 

1. Na página **Sistema,** sob **domínio,** selecione **credenciais Globais (sem restrições)**.

1. A partir do menu esquerdo, selecione **Adicionar Credenciais**.

1. Na lista **Kind,** selecione **Azure Service Principal**.

1. Forneça as informações para o seu diretor de serviço e subscrição Azure nas propriedades descritas pela tabela neste passo:

   ![Adicione credenciais principais de serviço Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-service-principal-credentials.png)

   | Propriedade | Valor | Descrição | 
   |----------|-------|-------------| 
   | **ID de subscrição** | <*yourAzureSubscription-ID*> | O valor GUID para a sua subscrição Azure <p>**Dica**: Se não conhecer o seu ID de subscrição Azure, execute este comando Azure `id` CLI a partir da linha de comando ou da Cloud Shell e, em seguida, use o valor GUID: <p>`az account list` | 
   | **ID do cliente** | <*yourAzureServicePrincipal-ID*> | O `appId` valor GUID anteriormente gerado para o seu diretor de serviço Azure | 
   | **Segredo de Cliente** | <*sua Password Segura*> | O `password` valor ou "segredo" que forneceu para o seu diretor de serviço Azure | 
   | **ID do inquilino** | <*yourAzureActiveDirectoryTenant-ID*> | O `tenant` valor GUID para o seu inquilino de Diretório Ativo Azure | 
   | **ID** | <*yourAzureServicePrincipalName*> | O `displayName` valor para o seu diretor de serviço Azure | 

1. Para confirmar que o seu diretor de serviço funciona, selecione **Verificar o Diretor de Serviço**. Quando tiver terminado, selecione **OK**.

Em seguida, crie o oleoduto Jenkins que constrói e implementa a sua aplicação.

## <a name="create-jenkins-pipeline"></a>Criar o pipeline do Jenkins

Em Jenkins, crie o trabalho de oleoduto para construir e implementar a sua app.

1. Volte para a sua página inicial do Jenkins e selecione **New Item**. 

   ![Criar um pipeline do Jenkins](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-new-item.png)

1. Forneça um nome para o seu trabalho de pipeline, por exemplo, "My-Java-Web-App", e selecione **Pipeline**. Na parte inferior, selecione **OK**.  

   ![Nomeie o trabalho do oleoduto Jenkins](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-pipeline.png)

1. Instale o Jenkins com o seu diretor de serviço para que o Jenkins possa ser implantado no Azure sem usar as suas próprias credenciais.

   1. No separador **Geral,** **selecione Preparar um ambiente para a execução**. 

   1. Na caixa **de Conteúdo de Propriedades** que aparece, adicione estas variáveis ambientais e os seus valores. 

      ```ini
      AZURE_CRED_ID=yourAzureServicePrincipalName
      RES_GROUP=yourWebAppAzureResourceGroupName
      WEB_APP=yourWebAppName
      ```

      ![Prepare um ambiente para a corrida e detete as variáveis ambientais](media/tutorial-jenkins-deploy-web-app-azure-app-service/prepare-environment-for-jenkins-run.png)

1. Quando tiver terminado, selecione **Guardar**.

Em seguida, crie scripts de construção e implantação para Jenkins.

## <a name="create-build-and-deployment-files"></a>Criar ficheiros de construção e implementação

Agora crie os ficheiros que o Jenkins usa para construir e implementar a sua aplicação.

1. `src/main/resources/` Na pasta do garfo GitHub, crie `web.config`este ficheiro de configuração `$(JAR_FILE_NAME)` `gs-spring-boot-0.1.0.jar`da aplicação denominado , que contém este XML mas substitua por:

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

1. Na pasta raiz do seu garfo GitHub, crie este script de construção e implementação chamado `Jenkinsfile`, que contém este texto[(fonte no GitHub aqui):](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/doc/resources/jenkins/Jenkinsfile-webapp-se)

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

1. Comprometa `web.config` ambos `Jenkinsfile` e ficheiros no garfo GitHub e empurre as suas alterações.

## <a name="point-pipeline-at-script"></a>Oleoduto de ponto no script

Agora especifique o guião de construção e implementação que quer que o Jenkins use.

1. Em Jenkins, selecione o seu trabalho de oleoduto previamente criado. 

   ![Selecione o trabalho do pipeline Jenkins para a sua aplicação web](media/tutorial-jenkins-deploy-web-app-azure-app-service/select-pipeline-job.png)

1. No menu esquerdo, **selecione Configure**.

1. No **separador Pipeline,** a partir da lista **Definição,** selecione **script pipeline a partir de SCM**.

   1. Na caixa **SCM** que aparece, selecione **Git** como o seu controlo de origem. 

   1. Na secção **Repositórios,** para **URL repositório,** introduza o URL do seu garfo GitHub, por exemplo: 

      `https://github.com/<your-GitHub-username>/gs-spring-boot`

   1. Para **credenciais,** selecione o seu token de acesso pessoal GitHub previamente criado.

   1. Na caixa **Script Path,** adicione o caminho para o seu script "Jenkinsfile".

   Quando terminar, a sua definição de pipeline parece este exemplo: 

   ![Aponte o seu oleoduto Jenkins para o script](media/tutorial-jenkins-deploy-web-app-azure-app-service/set-up-jenkins-github.png)

1. Quando tiver terminado, selecione **Guardar**.

Em seguida, construa e implemente a sua app para o Azure App Service. 

## <a name="build-and-deploy-to-azure"></a>Construir e implantar para Azure

1. Com o Azure CLI, quer a partir da linha de comando quer da Azure Cloud Shell, crie uma [aplicação web Azure App Service no Linux](/azure/app-service/containers/app-service-linux-intro) onde o Jenkins implementa a sua aplicação web depois de terminar uma construção. Certifique-se de que a sua aplicação web tem um nome único.

   ```azurecli-interactive
   az group create --name yourWebAppAzureResourceGroupName --location yourAzureRegion
   az appservice plan create --name yourLinuxAppServicePlanName --resource-group yourWebAppAzureResourceGroupName --is-linux
   az webapp create --name yourWebAppName --resource-group yourWebAppAzureResourceGroupName --plan yourLinuxAppServicePlanName --runtime "java|1.8|Tomcat|8.5"
   ```

   Para obter mais informações sobre estes comandos Azure CLI, consulte estas páginas:

   * [**`az group create`**](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)

   * [**`az appservice plan create`**](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)

   * [**`az webapp create`**](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)

1. Em Jenkins, selecione o seu trabalho de pipeline e selecione **Build Now**.

   Após os acabamentos da construção, jenkins implementa a sua app, que agora está ao vivo no Azure no URL de publicação, por exemplo: 

   `http://<your-Java-web-app>.azurewebsites.net`

   ![Ver a aplicação implementada no Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-unedited.png)

## <a name="push-changes-and-redeploy"></a>Emitir alterações e reimplementar

1. No seu navegador web, vá a esta localização no garfo GitHub da sua aplicação web:

   `complete/src/main/java/Hello/Application.java`
   
1. A partir do canto superior direito do GitHub, **selecione Editar este ficheiro**.

1. Faça esta alteração `commandLineRunner()` ao método e comprometa a mudança `master` para o ramo do repo. Este compromisso `master` no ramo começa uma construção em Jenkins. 
   
   ```java
   System.out.println("Let's inspect the beans provided by Spring Boot on Azure");
   ```

1. Depois de terminar a construção, e Jenkins reimplanta-se para o Azure, refresque a sua aplicação, que agora mostra a sua atualização.

   ![Ver a aplicação implementada no Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-edited.png)

## <a name="troubleshooting-the-jenkins-plug-in"></a>Problemas de resolução do plug-in jenkins

Se encontrar algum bug com os plug-ins Jenkins, apresente um problema no [Jenkins JIRA](https://issues.jenkins-ci.org/) para o componente específico.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Use Azure VMs as build agents](/azure/jenkins/jenkins-azure-vm-agents) (Utilizar VMs do Azure como agentes de compilação)
