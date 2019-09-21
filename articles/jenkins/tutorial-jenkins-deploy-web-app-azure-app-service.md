---
title: 'Tutorial: Implantar do GitHub para Azure App serviço com o Jenkins'
description: Configurar o Jenkins para a CI (integração contínua) do GitHub e a implantação contínua (CD) para o serviço Azure App para aplicativos Web Java
services: jenkins
ms.service: jenkins
author: tomarchermsft
ms.author: tarcher
manager: jeconnoc
ms.topic: tutorial
ms.date: 11/15/2018
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: c4e4a984adc0ec6af99667ff36c009ca730acf48
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172837"
---
# <a name="tutorial-deploy-from-github-to-azure-app-service-with-jenkins-continuous-integration-and-deployment"></a>Tutorial: Implantar do GitHub para Azure App serviço com implantação e integração contínua Jenkins

Este tutorial implanta um aplicativo Web Java de exemplo do GitHub para o [serviço Azure app no Linux](/azure/app-service/containers/app-service-linux-intro) Configurando CI (integração contínua) e CD (implantação contínua) em Jenkins. Quando você atualiza o aplicativo enviando confirmações por push ao GitHub, o Jenkins compila e Republica automaticamente seu aplicativo para Azure App serviço. O aplicativo de exemplo neste tutorial foi desenvolvido usando a estrutura [Spring boot](https://projects.spring.io/spring-boot/) . 

![Visão geral da implantação do GitHub para Azure App Service](media/tutorial-jenkins-deploy-web-app-azure-app-service/azure-continuous-integration-deployment-overview.png)

Neste tutorial, você concluirá estas tarefas:

> [!div class="checklist"]
> * Instale plug-ins do Jenkins para que você possa criar a partir do GitHub, implantar no serviço Azure App e outras tarefas relacionadas.
> * Bifurcar o repositório GitHub de exemplo para que você tenha uma cópia de trabalho.
> * Conecte o Jenkins ao GitHub.
> * Crie uma entidade de serviço do Azure para que o Jenkins possa acessar o Azure sem usar suas credenciais.
> * Adicione sua entidade de serviço ao Jenkins.
> * Crie o pipeline Jenkins que cria e implanta o aplicativo de exemplo cada vez que você atualiza o aplicativo no GitHub.
> * Crie arquivos de compilação e implantação para o pipeline do Jenkins.
> * Aponte seu pipeline do Jenkins no script de compilação e implantação.
> * Implante seu aplicativo de exemplo no Azure executando uma compilação manual.
> * Envie por push uma atualização de aplicativo no GitHub, que dispara o Jenkins para compilar e reimplantar no Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará destes itens:

* Um servidor [Jenkins](https://jenkins.io/) com as ferramentas do Java Development Kit (JDK) e do Maven instaladas em uma VM Linux do Azure

  Se você não tiver um servidor Jenkins, conclua estas etapas agora no portal do Azure: [Criar um servidor Jenkins em uma VM Linux do Azure](/azure/jenkins/install-jenkins-solution-template)

* Uma conta do [GitHub](https://github.com) para que você possa obter uma cópia de trabalho (Fork) para o aplicativo Web Java de exemplo. 

* [CLI do Azure](/cli/azure/install-azure-cli), que você pode executar de sua linha de comando local ou [Azure cloud Shell](/azure/cloud-shell/overview)

## <a name="install-jenkins-plug-ins"></a>Instalar os plug-ins do Jenkins

1. Entre no console Web do Jenkins neste local:

   `https://<Jenkins-server-name>.<Azure-region>.cloudapp.azure.com`

1. Na página principal do Jenkins, selecione **gerenciar Jenkins** > **Gerenciar plug-ins**.

   ![Gerenciar plug-ins do Jenkins](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-plugins.png)

1. Na guia **disponível** , selecione estes plug-ins:

   - [Serviço de Aplicações do Azure](https://plugins.jenkins.io/azure-app-service)
   - [Origem da ramificação do GitHub](https://plugins.jenkins.io/github-branch-source)
   - [Plug-in injetador de ambiente](https://plugins.jenkins.io/envinject) Jenkins
   - [Credenciais do Azure](https://plugins.jenkins.io/azure-credentials)

   Se esses plug-ins não aparecerem, verifique se eles ainda não estão instalados marcando a guia **instalado** .

1. Para instalar os plug-ins selecionados, selecione **baixar agora e instalar após a reinicialização**.

1. Depois de terminar, no menu Jenkins, selecione **gerenciar Jenkins** para retornar à página de gerenciamento do Jenkins para futuras etapas.

## <a name="fork-sample-github-repo"></a>Repositório GitHub de exemplo de bifurcação

1. [Entre no repositório do GitHub para o aplicativo de exemplo do Spring boot](https://github.com/spring-guides/gs-spring-boot). 

1. No canto superior direito do GitHub, selecione **Fork**.

   ![Repositório de exemplo de bifurcação do GitHub](media/tutorial-jenkins-deploy-web-app-azure-app-service/fork-github-repo.png)

1. Siga os prompts para selecionar sua conta do GitHub e concluir a bifurcação.

Em seguida, configure o Jenkins com suas credenciais do GitHub.

## <a name="connect-jenkins-to-github"></a>Conectar o Jenkins ao GitHub

Para que o Jenkins monitore o GitHub e responda quando novas confirmações são enviadas por push para seu aplicativo Web em sua bifurcação do GitHub, habilite os WebHooks do [GitHub](https://developer.github.com/webhooks/) no Jenkins.

> [!NOTE]
> 
> Estas etapas criam credenciais de token de acesso pessoal para o Jenkins trabalhar com o GitHub usando seu nome de usuário e senha do GitHub. 
> No entanto, se sua conta do GitHub usar a autenticação de dois fatores, crie seu token no GitHub e configure o Jenkins para usar esse token em vez disso. 
> Para obter mais informações, consulte a documentação de [plug-in do GitHub do Jenkins](https://wiki.jenkins.io/display/JENKINS/GitHub+Plugin) .

1. Na página **gerenciar Jenkins** , selecione **Configurar sistema**. 

   ![Configurar o sistema no Jenkins](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-configure-system.png)

1. Na seção do **GitHub** , forneça detalhes para o servidor github. Na lista **Adicionar servidor GitHub** , selecione **servidor GitHub**. 

   ![Adicionar servidor GitHub no Jenkins](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-GitHub-server.png)

1. Se a propriedade **gerenciar ganchos** não estiver selecionada, selecione essa propriedade. Selecione **avançado** para que você possa especificar outras configurações. 

   ![Especificar configurações de Jenkins avançadas para o servidor GitHub](media/tutorial-jenkins-deploy-web-app-azure-app-service/advanced-GitHub-settings.png)

1. Na lista **Gerenciar ações adicionais do GitHub** , selecione **converter logon e senha em token**.

   ![Converter o logon e a senha para o token para o GitHub](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-additional-actions.png)

1. Selecione **de logon e senha** para que você possa inserir seu nome de usuário e senha do github. Quando terminar, selecione **criar credenciais de token**, que cria um [Pat (token de acesso pessoal) do GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   

   ![Criar PAT do GitHub de logon e senha](media/tutorial-jenkins-deploy-web-app-azure-app-service/create-github-token-credentials.png)

1. Na seção **servidor do GitHub** , na lista de **credenciais** , selecione o novo token. Verifique se a autenticação está funcionando escolhendo **testar conexão**.

   ![Verificar a conexão com o servidor GitHub com novo PAT](media/tutorial-jenkins-deploy-web-app-azure-app-service/check-github-connection.png)

Em seguida, crie a entidade de serviço do Azure que o Jenkins usa para autenticar e acessar os recursos do Azure.

## <a name="create-service-principal"></a>Criar um principal de serviço

Em uma seção posterior, você cria um trabalho de pipeline do Jenkins que cria seu aplicativo do GitHub e implanta seu aplicativo no serviço Azure App. Para que o Jenkins acesse o Azure sem inserir suas credenciais, crie uma [entidade de serviço](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) em Azure Active Directory para Jenkins. Uma entidade de serviço é uma identidade separada que o Jenkins pode usar para autenticar o acesso aos recursos do Azure. Para criar essa entidade de serviço, execute o comando [ **`az ad sp create-for-rbac`** ](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)CLI do Azure, seja na linha de comando ou Azure cloud Shell local, por exemplo: 

```azurecli-interactive
az ad sp create-for-rbac --name "yourAzureServicePrincipalName" --password yourSecurePassword
```

Certifique-se de usar aspas em volta do nome da entidade de serviço. Além disso, crie uma senha forte com base no [Azure Active Directory regras e restrições de senha](/azure/active-directory/active-directory-passwords-policy). Se você não fornecer uma senha, o CLI do Azure criará uma senha para você. 

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
> Se você já tiver uma entidade de serviço, poderá reutilizar essa identidade em vez disso.
> Ao fornecer valores de entidade de serviço para autenticação, `appId`use `password`os valores `tenant` de propriedade, e. 
> Ao procurar uma entidade de serviço existente, use o `displayName` valor da propriedade.

## <a name="add-service-principal-to-jenkins"></a>Adicionar entidade de serviço ao Jenkins

1. Na página principal do Jenkins, selecione **credenciais** > **sistema**. 

1. Na página **sistema** , em **domínio**, selecione **credenciais globais (Irrestrito)** .

1. No menu à esquerda, selecione **Adicionar credenciais**.

1. Na lista **tipo** , selecione **entidade de serviço do Azure**.

1. Forneça as informações para sua entidade de serviço e a assinatura do Azure nas propriedades descritas pela tabela nesta etapa:

   ![Adicionar credenciais da entidade de serviço do Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-service-principal-credentials.png)

   | Propriedade | Value | Descrição | 
   |----------|-------|-------------| 
   | **ID da assinatura** | <*yourAzureSubscription-ID*> | O valor de GUID para sua assinatura do Azure <p>**Dica**: Se você não souber sua ID de assinatura do Azure, execute este CLI do Azure comando na linha de comando ou em Cloud Shell e, em seguida `id` , use o valor de GUID: <p>`az account list` | 
   | **ID do cliente** | <*yourAzureServicePrincipal-ID*> | O `appId` valor de GUID gerado anteriormente para sua entidade de serviço do Azure | 
   | **Segredo do cliente** | <*yourSecurePassword*> | O `password` valor ou "segredo" que você forneceu para sua entidade de serviço do Azure | 
   | **ID do locatário** | <*yourAzureActiveDirectoryTenant-ID*> | O `tenant` valor de GUID para seu locatário Azure Active Directory | 
   | **ID** | <*yourAzureServicePrincipalName*> | O `displayName` valor para sua entidade de serviço do Azure | 

1. Para confirmar que a entidade de serviço funciona, selecione **verificar entidade de serviço**. Quando tiver terminado, selecione **OK**.

Em seguida, crie o pipeline Jenkins que cria e implanta seu aplicativo.

## <a name="create-jenkins-pipeline"></a>Criar o pipeline do Jenkins

No Jenkins, crie o trabalho de pipeline para compilar e implantar seu aplicativo.

1. Volte para o home page Jenkins e selecione **novo item**. 

   ![Criar um pipeline do Jenkins](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-new-item.png)

1. Forneça um nome para seu trabalho de pipeline, por exemplo, "My-Java-Web-App" e selecione **pipeline**. Na parte inferior, selecione **OK**.  

   ![Nomear o trabalho do pipeline do Jenkins](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-pipeline.png)

1. Configure o Jenkins com sua entidade de serviço para que o Jenkins possa implantar no Azure sem usar suas próprias credenciais.

   1. Na guia **geral** , selecione **preparar um ambiente para a execução**. 

   1. Na caixa de **conteúdo Propriedades** exibida, adicione essas variáveis de ambiente e seus valores. 

      ```ini
      AZURE_CRED_ID=yourAzureServicePrincipalName
      RES_GROUP=yourWebAppAzureResourceGroupName
      WEB_APP=yourWebAppName
      ```

      ![Preparar um ambiente para a execução e definir as variáveis de ambiente](media/tutorial-jenkins-deploy-web-app-azure-app-service/prepare-environment-for-jenkins-run.png)

1. Quando tiver terminado, selecione **Guardar**.

Em seguida, crie scripts de compilação e implantação para Jenkins.

## <a name="create-build-and-deployment-files"></a>Criar arquivos de compilação e implantação

Agora, crie os arquivos que o Jenkins usa para compilar e implantar seu aplicativo.

1. Na pasta de sua bifurcação do GitHub, crie esse arquivo `src/main/resources/` de `web.config`configuração de aplicativo chamado, que contém `$(JAR_FILE_NAME)` esse `gs-spring-boot-0.1.0.jar`XML, mas substitua por:

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

1. Na pasta raiz de sua bifurcação do GitHub, crie este script de compilação `Jenkinsfile`e implantação chamado, que contém este texto ([fonte no GitHub aqui](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/doc/resources/jenkins/Jenkinsfile-webapp-se)):

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

1. Confirme os `web.config` arquivos `Jenkinsfile` e para sua bifurcação do GitHub e envie por push suas alterações.

## <a name="point-pipeline-at-script"></a>Pipeline de ponto no script

Agora, especifique o script de compilação e implantação que você deseja que o Jenkins use.

1. Em Jenkins, selecione o trabalho de pipeline criado anteriormente. 

   ![Selecione o trabalho de pipeline do Jenkins para seu aplicativo Web](media/tutorial-jenkins-deploy-web-app-azure-app-service/select-pipeline-job.png)

1. No menu à esquerda, selecione **Configurar**.

1. Na guia **pipeline** , na lista **definição** , selecione **script de pipeline do SCM**.

   1. Na caixa **SCM** que aparece, selecione **git** como seu controle do código-fonte. 

   1. Na seção repositórios, para URL do **repositório**, insira a URL da bifurcação do GitHub, por exemplo: 

      `https://github.com/<your-GitHub-username>/gs-spring-boot`

   1. Para **credenciais**, selecione o token de acesso pessoal do GitHub criado anteriormente.

   1. Na caixa **caminho do script** , adicione o caminho ao script "Jenkinsfile".

   Quando terminar, sua definição de pipeline será parecida com este exemplo: 

   ![Aponte seu pipeline do Jenkins no script](media/tutorial-jenkins-deploy-web-app-azure-app-service/set-up-jenkins-github.png)

1. Quando tiver terminado, selecione **Guardar**.

Em seguida, compile e implante seu aplicativo no serviço Azure App. 

## <a name="build-and-deploy-to-azure"></a>Compilar e implantar no Azure

1. Com o CLI do Azure, na linha de comando ou Azure Cloud Shell, crie um [aplicativo Web de serviço de Azure app no Linux](/azure/app-service/containers/app-service-linux-intro) em que o Jenkins implanta seu aplicativo Web depois de concluir uma compilação. Verifique se seu aplicativo Web tem um nome exclusivo.

   ```azurecli-interactive
   az group create --name yourWebAppAzureResourceGroupName --location yourAzureRegion
   az appservice plan create --name yourLinuxAppServicePlanName --resource-group yourWebAppAzureResourceGroupName --is-linux
   az webapp create --name yourWebAppName --resource-group yourWebAppAzureResourceGroupName --plan yourLinuxAppServicePlanName --runtime "java|1.8|Tomcat|8.5"
   ```

   Para obter mais informações sobre esses CLI do Azure comandos, consulte estas páginas:

   * [**`az group create`**](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)

   * [**`az appservice plan create`**](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)

   * [**`az webapp create`**](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)

1. Em Jenkins, selecione seu trabalho de pipeline e selecione **Compilar agora**.

   Após a conclusão da compilação, o Jenkins implanta seu aplicativo, que agora está ativo no Azure na URL de publicação, por exemplo: 

   `http://<your-Java-web-app>.azurewebsites.net`

   ![Ver a aplicação implementada no Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-unedited.png)

## <a name="push-changes-and-redeploy"></a>Emitir alterações e reimplementar

1. No navegador da Web, vá para este local na bifurcação do GitHub do seu aplicativo Web:

   `complete/src/main/java/Hello/Application.java`
   
1. No canto superior direito do GitHub, selecione **Editar este arquivo**.

1. Faça essa alteração `commandLineRunner()` no método e confirme a alteração no Branch do `master` repositório. Essa confirmação na `master` ramificação inicia uma compilação em Jenkins. 
   
   ```java
   System.out.println("Let's inspect the beans provided by Spring Boot on Azure");
   ```

1. Após a conclusão da compilação, e Jenkins reimplanta no Azure, atualize seu aplicativo, que agora mostra sua atualização.

   ![Ver a aplicação implementada no Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-edited.png)

## <a name="troubleshooting-the-jenkins-plug-in"></a>Solucionando problemas do plug-in Jenkins

Se você encontrar bugs com os plug-ins Jenkins, execute um problema no [Jenkins JIRA](https://issues.jenkins-ci.org/) para o componente específico.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Use Azure VMs as build agents](/azure/jenkins/jenkins-azure-vm-agents) (Utilizar VMs do Azure como agentes de compilação)
