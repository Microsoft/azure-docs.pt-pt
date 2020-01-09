---
title: Compilação contínua para aplicativos do Linux usando Jenkins
description: Compilação e integração contínuas para seu aplicativo Service Fabric Linux usando o Jenkins
author: sayantancs
ms.topic: conceptual
ms.date: 07/31/2018
ms.openlocfilehash: 175338fef600f6e726fd02eee6b0f416181bd9dd
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610221"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Use o Jenkins para compilar e implantar seus aplicativos Linux
O Jenkins é uma ferramenta popular para integração e implementação contínuas de aplicações. Eis como pode utilizar o Jenkins para compilar e implementar a sua aplicação do Azure Service Fabric.

## <a name="topic-overview"></a>Visão geral do tópico
Este artigo aborda várias maneiras possíveis de configurar seu ambiente Jenkins, bem como maneiras diferentes de implantar seu aplicativo em um Cluster Service Fabric depois que ele tiver sido criado. Siga estas etapas gerais para configurar com êxito o Jenkins, efetuar pull de alterações do GitHub, compilar seu aplicativo e implantá-lo em seu cluster:

1. Certifique-se de instalar os [pré-requisitos](#prerequisites).
1. Em seguida, siga as etapas em uma dessas seções para configurar o Jenkins:
   * [Configurar o Jenkins dentro de um cluster Service Fabric](#set-up-jenkins-inside-a-service-fabric-cluster), 
   * [Configurar o Jenkins fora de um cluster Service Fabric](#set-up-jenkins-outside-a-service-fabric-cluster)ou
   * [Instale o plug-in Service Fabric em um ambiente Jenkins existente](#install-service-fabric-plugin-in-an-existing-jenkins-environment).
1. Depois de configurar o Jenkins, siga as etapas em [criar e configurar um trabalho do Jenkins](#create-and-configure-a-jenkins-job) para configurar o GitHub para disparar o Jenkins quando forem feitas alterações ao seu aplicativo e para configurar o pipeline de trabalho do Jenkins por meio da etapa de compilação para efetuar pull das alterações do GitHub e criar seu aplicativo. 
1. Por fim, configure a etapa pós-Build do trabalho Jenkins para implantar seu aplicativo no Cluster Service Fabric. Há duas maneiras de configurar o Jenkins para implantar seu aplicativo em um cluster:    
   * Para ambientes de desenvolvimento e teste, use [Configurar a implantação usando o ponto de extremidade de gerenciamento de cluster](#configure-deployment-using-cluster-management-endpoint). Esse é o método de implantação mais simples a ser configurado.
   * Para ambientes de produção, use [Configurar a implantação usando as credenciais do Azure](#configure-deployment-using-azure-credentials). A Microsoft recomenda esse método para ambientes de produção porque, com as credenciais do Azure, você pode limitar o acesso que um trabalho Jenkins tem aos recursos do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

- Verifique se o Git está instalado localmente. Você pode instalar a versão apropriada do git na [página de downloads do git](https://git-scm.com/downloads) com base no seu sistema operacional. Se você for novo no git, saiba mais sobre ele na [documentação do git](https://git-scm.com/docs).
- Este artigo usa a *amostra de Service Fabric introdução* no GitHub: [https://github.com/Azure-Samples/service-fabric-java-getting-started](https://github.com/Azure-Samples/service-fabric-java-getting-started) para o aplicativo compilar e implantar. Você pode bifurcar este repositório para acompanhar ou, com algumas modificações nas instruções, usar seu próprio projeto do GitHub.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Instalar Service Fabric plug-in em um ambiente Jenkins existente
Se estiver adicionando o plug-in Service Fabric a um ambiente Jenkins existente, você precisará do seguinte:

- A [CLI do Service Fabric](service-fabric-cli.md) (sfctl).

   > [!NOTE]
   > Certifique-se de instalar a CLI no nível do sistema, em vez de no nível do usuário, para que Jenkins possa executar comandos da CLI. 
   >

- Para implantar aplicativos Java, instale o [gradle e abra o JDK 8,0](service-fabric-get-started-linux.md#set-up-java-development). 
- Para implantar aplicativos .NET Core 2,0, instale o [SDK do .net core 2,0](service-fabric-get-started-linux.md#set-up-net-core-20-development). 

Depois de instalar os pré-requisitos necessários para o seu ambiente, você pode pesquisar o plug-in do Service Fabric do Azure no Marketplace do Jenkins e instalá-lo.

Depois de instalar o plug-in, pule para [criar e configurar um trabalho do Jenkins](#create-and-configure-a-jenkins-job).


## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Configurar o Jenkins num cluster do Service Fabric

Pode configurar o Jenkins dentro ou fora de um cluster do Service Fabric. As seções a seguir mostram como configurá-lo dentro de um cluster ao usar uma conta de armazenamento do Azure para salvar o estado da instância de contêiner.

### <a name="prerequisites"></a>Pré-requisitos
- Ter um Cluster Service Fabric Linux com o Docker instalado. Service Fabric clusters em execução no Azure já têm o Docker instalado. Se você estiver executando o cluster localmente (ambiente de desenvolvimento de OneBox), verifique se o Docker está instalado em seu computador com o comando `docker info`. Se não estiver instalado, instale-o usando os seguintes comandos:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Verifique se a porta 8081 está especificada como um ponto de extremidade personalizado no cluster. Se você estiver usando um cluster local, verifique se a porta 8081 está aberta no computador host e se ela tem um endereço IP voltado para o público.
   >

### <a name="steps"></a>Passos
1. Clone o aplicativo usando os seguintes comandos:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. Persistir o estado do contêiner Jenkins em um compartilhamento de arquivos:
   1. Crie uma conta de armazenamento do Azure na **mesma região** que o seu cluster com um nome, como `sfjenkinsstorage1`.
   1. Crie um **compartilhamento de arquivos** na conta de armazenamento com um nome como `sfjenkins`.
   1. Clique em **conectar** para o compartilhamento de arquivos e observe os valores que ele exibe em **conectando do Linux**, o valor deve ser semelhante ao seguinte:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Para montar compartilhamentos CIFS, você precisa ter o pacote CIFS-utils instalado nos nós do cluster.      
   >

1. Atualize os valores de espaço reservado no script `setupentrypoint.sh` com os detalhes do armazenamento do Azure da etapa 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Substitua `[REMOTE_FILE_SHARE_LOCATION]` pelo valor `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` da saída da conexão na etapa 2 acima.
   * Substitua `[FILE_SHARE_CONNECT_OPTIONS_STRING]` pelo valor `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` da etapa 2 acima.

1. **Somente cluster seguro:** 
   
   Para configurar a implantação de aplicativos em um cluster seguro do Jenkins, o certificado de cluster deve estar acessível no contêiner Jenkins. No arquivo *ApplicationManifest. xml* , na marca **ContainerHostPolicies** , adicione essa referência de certificado e atualize o valor de impressão digital com o do certificado de cluster.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Além disso, adicione as linhas a seguir na marca **ApplicationManifest** (raiz) no arquivo *ApplicationManifest. xml* e atualize o valor da impressão digital com o do certificado do cluster.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

1. Conecte-se ao cluster e instale o aplicativo de contêiner.

   **Cluster seguro**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   O comando anterior pega o certificado no formato PEM. Se o certificado estiver no formato PFX, você poderá usar o comando a seguir para convertê-lo. Se o arquivo PFX não estiver protegido por senha, especifique o parâmetro de **passagem** como `-passin pass:`.
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **Cluster não seguro**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   Instala o contentor do Jenkins no cluster e pode ser monitorizado com o Service Fabric Explorer.

   > [!NOTE]
   > Pode levar alguns minutos para que a imagem Jenkins seja baixada no cluster.
   >

1. A partir do browser, aceda a `http://PublicIPorFQDN:8081`. Fornece o caminho da palavra-passe de administrador inicial necessária para iniciar sessão. 
1. Examine a Service Fabric Explorer para determinar em qual nó o contêiner Jenkins está em execução. Secure Shell (SSH) entrar neste nó.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. Obtenha o ID de instância do contentor com `docker ps -a`.
1. Secure Shell (SSH) entre no contêiner e cole o caminho que você foi mostrado no portal do Jenkins. Por exemplo, se no portal ele mostrar o caminho `PATH_TO_INITIAL_ADMIN_PASSWORD`, execute os seguintes comandos:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. Na página Introdução do Jenkins, escolha a opção Selecionar plug-ins para instalar, marque a caixa de seleção **nenhum** e clique em instalar.
1. Crie um usuário ou selecione para continuar como administrador.

Depois de configurar o Jenkins, pule para [criar e configurar um trabalho do Jenkins](#create-and-configure-a-jenkins-job).  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Configurar o Jenkins fora de um cluster do Service Fabric

Pode configurar o Jenkins dentro ou fora de um cluster do Service Fabric. As secções seguintes mostram como configurá-lo fora de um cluster.

### <a name="prerequisites"></a>Pré-requisitos
- Verifique se o Docker está instalado em seu computador. Os comandos seguintes podem ser utilizados para instalar o Docker a partir do terminal:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

  Quando você executa `docker info` no terminal, a saída deve mostrar que o serviço do Docker está em execução.

### <a name="steps"></a>Passos
1. Extraia a imagem do contentor do Jenkins do Service Fabric: `docker pull rapatchi/jenkins:latest`. Esta imagem inclui o plug-in do Jenkins do Service Fabric pré-instalado.
1. Execute a imagem do contentor:`docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. Obtenha o ID da instância de imagem do contentor. Pode listar todos os contentores de Docker com o comando `docker ps –a`
1. Entre no portal do Jenkins com as seguintes etapas:

   1. Entre em um shell do Jenkins do seu host. Use os quatro primeiros dígitos da ID do contêiner. Por exemplo, se a ID do contêiner for `2d24a73b5964`, use `2d24`.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. No Shell Jenkins, obtenha a senha de administrador para sua instância de contêiner:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. Para entrar no painel do Jenkins, abra a seguinte URL em um navegador da Web: `http://<HOST-IP>:8080`. Use a senha da etapa anterior para desbloquear o Jenkins.
   1. (Opcional.) Depois de entrar pela primeira vez, você pode criar sua própria conta de usuário e usá-la para as etapas a seguir, ou você pode continuar a usar a conta de administrador. Se você criar um usuário, precisará continuar com esse usuário.
1. Configure o GitHub para trabalhar com o Jenkins usando as etapas em [gerando uma nova chave SSH e adicionando-a ao agente SSH](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
   * Utilize as instruções fornecidas pelo GitHub para gerar a chave SSH e adicioná-la à conta GitHub que está a alojar o repositório.
   * Execute os comandos mencionados na ligação anterior na shell Docker do Jenkins (e não no seu anfitrião).
   * Para iniciar sessão na shell do Jenkins a partir do seu anfitrião, utilize o seguinte comando:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Certifique-se de que o cluster ou computador em que a imagem de contêiner Jenkins está hospedada tenha um endereço IP voltado para o público. Isto permite que a instância do Jenkins receba notificações do GitHub.

Depois de configurar o Jenkins, continue na próxima seção, [crie e configure um trabalho do Jenkins](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Criar e configurar uma tarefa do Jenkins

As etapas nesta seção mostram como configurar um trabalho Jenkins para responder a alterações em um repositório GitHub, buscar as alterações e compilá-las. No final desta seção, você será direcionado para as etapas finais para configurar o trabalho para implantar seu aplicativo com base no fato de você estar implantando em um ambiente de desenvolvimento/teste ou em um ambiente de produção. 

1. No painel do Jenkins, clique em **novo item**.
1. Introduza um nome para o item (por exemplo, **MyJob**). Selecione **free-style project** (projeto de estilo livre) e clique em **OK**.
1. A página configuração de trabalho é aberta. (Para obter a configuração do painel do Jenkins, clique no trabalho e, em seguida, clique em **Configurar**).

1. Na guia **geral** , marque a caixa do **projeto do GitHub**e especifique a URL do projeto do github. Este URL aloja a aplicação Java do Service Fabric que pretende integrar no fluxo de integração contínua do Jenkins e implementação contínua (CI/CD) (por exemplo, `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

1. Na guia **Gerenciamento de código-fonte** , selecione **git**. Especifique o URL do repositório que aloja a aplicação Java do Service Fabric que quer integrar no fluxo CI/CD do Jenkins (por exemplo, `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Você também pode especificar qual ramificação compilar (por exemplo, `/master`).
1. Configure seu repositório *GitHub* para se comunicar com o Jenkins:

   a. Na página do repositório GitHub, vá para **configurações** > **integrações e serviços**.

   b. Selecione **Add Service** (Adicionar Serviço), escreva **Jenkins** e selecione **Jenkins-GitHub plugin** (Plug-in Jenkins-GitHub).

   c. Introduza o URL do webhook do Jenkins (por predefinição, deve ser `http://<PublicIPorFQDN>:8081/github-webhook/`). Clique em **add/update service** (adicionar/atualizar serviço).

   d. É enviado um evento de teste para a instância do Jenkins. Deverá ver uma verificação a verde através do webhook do GitHub, e o seu projeto será compilado.

1. Na guia **criar gatilhos** em Jenkins, selecione a opção de compilação desejada. Para este exemplo, você deseja disparar uma compilação sempre que um envio por push para o repositório ocorrer, portanto, selecione **gatilho de gancho do GitHub para sondagem de GITScm**. (Anteriormente, esta opção era denominada **Build when a change is pushed to GitHub** [Compilar quando é enviada uma alteração para o GitHub]).
1. Na guia **Compilar** , execute uma das ações a seguir, dependendo se você está criando um aplicativo Java ou um aplicativo .NET Core:

   * **Para aplicativos Java:** Na lista suspensa **Adicionar etapa de compilação** , selecione **invocar script gradle**. Clique em **Advanced** (Avançado). No menu avançado, especifique o caminho para o **script de Build raiz** para seu aplicativo. Este obtém o build.gradle a partir do caminho especificado e funciona em conformidade. Para o [aplicativo ActorCounter](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter), isso é: `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`.

     ![Ação de Compilação do Jenkins do Service Fabric][build-step]

   * **Para aplicativos .NET Core:** Na lista suspensa **Adicionar etapa de compilação** , selecione **executar Shell**. Na caixa de comando que aparece, o diretório primeiro precisa ser alterado para o caminho onde o arquivo build.sh está localizado. Depois que o diretório tiver sido alterado, o script build.sh poderá ser executado e criará o aplicativo.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     A captura de tela a seguir mostra um exemplo dos comandos que são usados para criar o exemplo de [serviço de contador](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) com um nome de trabalho Jenkins de CounterServiceApplication.

      ![Ação de Compilação do Jenkins do Service Fabric][build-step-dotnet]

1. Para configurar o Jenkins para implantar seu aplicativo em um Cluster Service Fabric nas ações de pós-compilação, você precisa do local do certificado do cluster em seu contêiner Jenkins. Escolha uma das opções a seguir dependendo se o contêiner Jenkins está em execução dentro ou fora do seu cluster e anote o local do certificado do cluster:

   * **Para Jenkins em execução dentro de seu cluster:** O caminho para o certificado pode ser encontrado ao ecoar o valor da variável de ambiente *Certificates_JenkinsOnSF_Code_MyCert_PEM* de dentro do contêiner.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **Para Jenkins em execução fora do cluster:** Siga estas etapas para copiar o certificado do cluster para o contêiner:
     1. Seu certificado deve estar no formato PEM. Se você não tiver um arquivo PEM, poderá criar um do arquivo PFX de certificado. Se o arquivo PFX não estiver protegido por senha, execute o seguinte comando em seu host:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
        ``` 

        Se o arquivo PFX for protegido por senha, inclua a senha no parâmetro `-passin`. Por exemplo:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
        ``` 

     1. Para obter a ID do contêiner para o contêiner Jenkins, execute `docker ps` do seu host.
     1. Copie o arquivo PEM para o contêiner com o seguinte comando do Docker:
    
        ```sh
        docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
        ``` 

Você está quase terminando! Mantenha o trabalho do Jenkins aberto. A única tarefa restante é configurar as etapas de pós-compilação para implantar seu aplicativo no Cluster Service Fabric:

* Para implantar em um ambiente de desenvolvimento ou teste, siga as etapas em [Configurar a implantação usando o ponto de extremidade de gerenciamento de cluster](#configure-deployment-using-cluster-management-endpoint).
* Para implantar em um ambiente de produção, siga as etapas em [Configurar a implantação usando as credenciais do Azure](#configure-deployment-using-azure-credentials).

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Configurar a implantação usando o ponto de extremidade de gerenciamento de cluster
Para ambientes de desenvolvimento e teste, você pode usar o ponto de extremidade de gerenciamento de cluster para implantar seu aplicativo. Configurar a ação de pós-compilação com o ponto de extremidade de gerenciamento de cluster para implantar seu aplicativo requer a quantidade mínima de configuração. Se você estiver implantando em um ambiente de produção, pule para [Configurar a implantação usando as credenciais do Azure](#configure-deployment-using-azure-credentials) para configurar uma entidade de serviço Azure Active Directory a ser usada durante a implantação.    

1. No trabalho do Jenkins, clique na guia **ações de pós-compilação** . 
1. No menu pendente **Post-Build Actions** (Ações de Pós-compilação), selecione **Deploy Service Fabric Project** (Implementar Projeto do Service Fabric). 
1. Em **Service Fabric configuração de cluster**, selecione o botão **de opção preencher o ponto de extremidade de gerenciamento de Service Fabric** .
1. Para o **host de gerenciamento**, insira o ponto de extremidade de conexão para o cluster; por exemplo `{your-cluster}.eastus.cloudapp.azure.com`.
1. Para **certificado de cliente**e **chave de cliente** , insira o local do arquivo PEM em seu contêiner Jenkins; por exemplo `/var/jenkins_home/clustercert.pem`. (Você copiou o local do certificado a última etapa de [criar e configurar um trabalho do Jenkins](#create-and-configure-a-jenkins-job).)
1. Em **configuração do aplicativo**, configure **o nome do aplicativo**, o tipo de **aplicativo**e o caminho (relativo) para os campos **de manifesto do aplicativo** .

   ![Ação de pós-compilação de Service Fabric Jenkins configurar ponto de extremidade de gerenciamento](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. Clique em **verificar configuração**. Após a verificação bem-sucedida, clique em **salvar**. O pipeline de trabalho do Jenkins agora está totalmente configurado. Pule para [as próximas etapas](#next-steps) para testar a implantação.

## <a name="configure-deployment-using-azure-credentials"></a>Configurar a implantação usando as credenciais do Azure
Para ambientes de produção, é altamente recomendável configurar uma credencial do Azure para implantar seu aplicativo. Esta seção mostra como configurar uma entidade de serviço Azure Active Directory a ser usada para implantar seu aplicativo na ação de pós-compilação. Você pode atribuir entidades de serviço a funções em seu diretório para limitar as permissões do trabalho Jenkins. 

Para ambientes de desenvolvimento e teste, você pode configurar as credenciais do Azure ou o ponto de extremidade de gerenciamento de cluster para implantar seu aplicativo. Para obter detalhes sobre como configurar um ponto de extremidade de gerenciamento de cluster, consulte [Configurar a implantação usando o ponto de extremidade de gerenciamento de cluster](#configure-deployment-using-cluster-management-endpoint).   

1. Para criar uma entidade de serviço Azure Active Directory e atribuir permissões de ti em sua assinatura do Azure, siga as etapas em [usar o portal para criar um aplicativo Azure Active Directory e uma entidade de serviço](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Preste atenção ao seguinte:

   * Ao seguir as etapas no tópico, certifique-se de copiar e salvar os seguintes valores: *ID do aplicativo*, *chave do aplicativo*, *ID do diretório (ID do locatário)* e ID da *assinatura*. Você precisa deles para configurar as credenciais do Azure no Jenkins.
   * Se você não tiver as [permissões necessárias](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) em seu diretório, será necessário solicitar que um administrador conceda a você as permissões ou crie a entidade de serviço para você, ou você precisará configurar o ponto de extremidade de gerenciamento para seu cluster nas **ações de pós-compilação** para seu trabalho no Jenkins.
   * Na seção [criar um aplicativo Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) , você pode inserir qualquer URL bem formada para a **URL de logon**.
   * Na seção [atribuir aplicativo a uma função](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) , você pode atribuir ao aplicativo a função *leitor* no grupo de recursos para o cluster.

1. De volta ao trabalho de Jenkins, clique na guia **ações de pós-compilação** .
1. No menu pendente **Post-Build Actions** (Ações de Pós-compilação), selecione **Deploy Service Fabric Project** (Implementar Projeto do Service Fabric). 
1. Em **Service Fabric configuração de cluster**, selecione o botão **de opção Selecionar o cluster de Service Fabric** . Clique em **Adicionar** ao lado de **credenciais do Azure**. Clique em **Jenkins** para selecionar o provedor de credenciais do Jenkins.
1. No provedor de credenciais do Jenkins, selecione **Microsoft Azure entidade de serviço** na lista suspensa **tipo** .
1. Use os valores que você salvou ao configurar sua entidade de serviço na etapa 1 para definir os seguintes campos:

   * **ID do cliente**: *ID do aplicativo*
   * **Segredo do cliente**: *chave do aplicativo*
   * **ID do locatário**: *ID do diretório*
   * **ID da assinatura**: *ID da assinatura*
1. Insira uma **ID** descritiva que você usa para selecionar a credencial em Jenkins e uma breve **Descrição**. Em seguida, clique em **verificar entidade de serviço**. Se a verificação for realizada com sucesso, clique em **Adicionar**.

   ![Service Fabric Jenkins inserir as credenciais do Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. De volta em **Service Fabric configuração de cluster**, certifique-se de que sua nova credencial esteja selecionada para **as credenciais do Azure**. 
1. Na lista suspensa **grupo de recursos** , selecione o grupo de recursos do cluster no qual você deseja implantar o aplicativo.
1. Na lista suspensa **Service Fabric** , selecione o cluster no qual você deseja implantar o aplicativo.
1. Para **certificado de cliente**e **chave de cliente** , insira o local do arquivo PEM em seu contêiner Jenkins. Por exemplo, `/var/jenkins_home/clustercert.pem`. 
1. Em **configuração do aplicativo**, configure **o nome do aplicativo**, o tipo de **aplicativo**e o caminho (relativo) para os campos **de manifesto do aplicativo** .
    ![ação de pós-compilação Jenkins Service Fabric configurar as credenciais do Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. Clique em **verificar configuração**. Após a verificação bem-sucedida, clique em **salvar**. O pipeline de trabalho do Jenkins agora está totalmente configurado. Continue nas [próximas etapas](#next-steps) para testar a implantação.

## <a name="troubleshooting-the-jenkins-plugin"></a>Resolver problemas nos plug-ins do Jenkins

Se se deparar com erros nos plug-ins do Jenkins, comunique os problemas com os componentes específicos no [Jenkins JIRA](https://issues.jenkins-ci.org/).

## <a name="next-steps"></a>Passos seguintes
O GitHub e o Jenkins estão agora configurados. Considere fazer algumas alterações de exemplo no projeto de `reliable-services-actor-sample/Actors/ActorCounter` na bifurcação do repositório, https://github.com/Azure-Samples/service-fabric-java-getting-started. Envie por push suas alterações para o Branch de `master` remoto (ou qualquer Branch que você tenha configurado para trabalhar). Isto aciona a tarefa `MyJob` do Jenkins, que configurou. Ele busca as alterações do GitHub, compila-as e implanta o aplicativo no cluster especificado nas ações de pós-compilação.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png

