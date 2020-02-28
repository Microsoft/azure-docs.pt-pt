---
title: Construção contínua para aplicações Linux usando Jenkins
description: Construção e integração contínuas para a sua aplicação Service Fabric Linux usando Jenkins
keywords: jenkins, azure, devops, cicd, linux
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: 96e8c3721b5849b874878ea6a846271fb6760ffd
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77675240"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Use jenkins para construir e implementar as suas aplicações Linux

Este tutorial abrange várias formas possíveis de configurar o seu ambiente Jenkins, bem como diferentes formas de implementar a sua aplicação num cluster de Tecido de Serviço depois de ter sido construído. Siga estes passos gerais para configurar o Jenkins com sucesso, retirar alterações do GitHub, construir a sua aplicação e implementá-la para o seu cluster:

1. Certifique-se de que instala os [Pré-requisitos.](#prerequisites)
1. Em seguida, siga os passos numa destas secções para configurar Jenkins:
   * [Instale jenkins dentro de um cluster de Tecido de Serviço,](#set-up-jenkins-inside-a-service-fabric-cluster) 
   * [Configurar Jenkins fora de um cluster de Tecido de Serviço,](#set-up-jenkins-outside-a-service-fabric-cluster)ou
   * [Instale o plugin Service Fabric num ambiente Jenkins existente.](#install-service-fabric-plugin-in-an-existing-jenkins-environment)
1. Depois de configurar o Jenkins, siga os passos na [Create e configure um trabalho](#create-and-configure-a-jenkins-job) da Jenkins para configurar o GitHub para desencadear jenkins quando forem feitas alterações na sua aplicação e configurar o seu pipeline de trabalho Jenkins através do passo de construção para retirar as mudanças do GitHub e construir a sua aplicação. 
1. Por fim, configure o passo pós-construção do trabalho da Jenkins para implementar a sua aplicação no seu cluster Service Fabric. Existem duas formas de configurar o Jenkins para implementar a sua aplicação num cluster:    
   * Para ambientes de desenvolvimento e teste, utilize a [implementação do Configure utilizando o ponto final](#configure-deployment-using-cluster-management-endpoint)de gestão do cluster . Este é o método de implantação mais simples para criar.
   * Para ambientes de produção, utilize a [implementação do Configure utilizando credenciais Azure](#configure-deployment-using-azure-credentials). A Microsoft recomenda este método para ambientes de produção porque com credenciais Azure pode limitar o acesso que um trabalho da Jenkins tem aos seus recursos Azure. 

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que git está instalado localmente. Pode instalar a versão Git apropriada a partir da página de [downloads git](https://git-scm.com/downloads) com base no seu sistema operativo. Se é novo em Git, saiba mais sobre isso com a [documentação git.](https://git-scm.com/docs)
- Este artigo utiliza a amostra de fabricação de tecido de *serviço iniciando-se* no GitHub: [https://github.com/Azure-Samples/service-fabric-java-getting-started](https://github.com/Azure-Samples/service-fabric-java-getting-started) para a aplicação construir e implementar. Pode bifurcar este repositório para seguir, ou, com alguma modificação nas instruções, utilizar o seu próprio projeto GitHub.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Instale plugin de Tecido de Serviço num ambiente Jenkins existente

Se estiver a adicionar o plugin de Tecido de Serviço a um ambiente Jenkins existente, tem de fazer os seguintes passos:

- [Tecido de serviço CLI (sfctl)](../service-fabric/service-fabric-cli.md). Instale o CLI ao nível do sistema e não ao nível do utilizador, para que o Jenkins possa executar comandos CLI. 
- Para implementar aplicações Java, instale [gradle e Open JDK 8.0](../service-fabric/service-fabric-get-started-linux.md#set-up-java-development). 
- Para implementar as aplicações .NET Core 2.0, instale o [.NET Core 2.0 SDK](../service-fabric/service-fabric-get-started-linux.md#set-up-net-core-20-development). 

Depois de ter instalado os pré-requisitos necessários para o seu ambiente, pode procurar o Plugin de Tecido de Serviço Azure no mercado jenkins e instalá-lo.

Depois de instalar o plugin, salte para criar [e configurar um trabalho de Jenkins](#create-and-configure-a-jenkins-job).

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Configurar o Jenkins num cluster do Service Fabric

Pode configurar o Jenkins dentro ou fora de um cluster do Service Fabric. As seguintes secções mostram como instalá-lo dentro de um cluster enquanto utiliza uma conta de armazenamento Azure para salvar o estado da instância do contentor.

1. Certifique-se de que tem um cluster de Linux de Tecido de Serviço com o Docker instalado. Os clusters de tecido de serviço em funcionamento em Azure já têm Docker instalado. Se estiver a executar o cluster localmente (ambiente OneBox dev), verifique se o Docker está instalado na sua máquina com o comando `docker info`. Se não estiver instalado, instale-o utilizando os seguintes comandos:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Certifique-se de que a porta 8081 é especificada como um ponto final personalizado no cluster. Se estiver a utilizar um cluster local, certifique-se de que o porto 8081 está aberto na máquina hospedeira e que tem um endereço IP virado para o público.
   >

1. Clone a aplicação, utilizando os seguintes comandos:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. Persistir o estado do contentor Jenkins numa partilha de ficheiros:
   1. Crie uma conta de armazenamento Azure na **mesma região** que o seu cluster com um nome como `sfjenkinsstorage1`.
   1. Crie uma Partilha de **Ficheiros** na Conta de armazenamento com um nome como `sfjenkins`.
   1. Clique no **Connect** para obter a partilha de ficheiros e note os valores que exibe em **Connecting from Linux,** o valor deve ser semelhante ao abaixo:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Para montar ações cifs, é necessário instalar o pacote cifs-utils nos nós do cluster.      
   >

1. Atualize os valores do espaço reservado no roteiro `setupentrypoint.sh` com os detalhes de armazenamento azul a partir do passo 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Substitua `[REMOTE_FILE_SHARE_LOCATION]` pelo valor `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` a partir da saída da ligação no passo 2 acima.
   * Substitua `[FILE_SHARE_CONNECT_OPTIONS_STRING]` pelo valor `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` do passo 2 acima.

1. **Cluster seguro apenas:** 
   
   Para configurar a implementação de aplicações num cluster seguro da Jenkins, o certificado de cluster deve ser acessível dentro do contentor Jenkins. No ficheiro *ApplicationManifest.xml,* sob a etiqueta **ContainerHostPolicies** adicione esta referência de certificado e atualize o valor de impressão digital com o do certificado de cluster.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Além disso, adicione as seguintes linhas sob a etiqueta **ApplicationManifest** (raiz) no ficheiro *ApplicationManifest.xml* e atualize o valor de impressão digital com o do certificado de cluster.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

1. Ligue-se ao cluster e instale a aplicação do recipiente.

   **Cluster seguro**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   O comando anterior recebe o certificado em formato PEM. Se o seu certificado estiver em formato PFX, pode utilizar o seguinte comando para o converter. Se o seu ficheiro PFX não estiver protegido por palavra-passe, especifique o parâmetro **de passagem** como `-passin pass:`.
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **Cluster inseguro**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   Instala o contentor do Jenkins no cluster e pode ser monitorizado com o Service Fabric Explorer.

   > [!NOTE]
   > Pode levar alguns minutos para a imagem de Jenkins ser descarregada no cluster.
   >

1. A partir do browser, aceda a `http://PublicIPorFQDN:8081`. Fornece o caminho da palavra-passe de administrador inicial necessária para iniciar sessão. 
1. Olhe para o Explorador de Tecidode Serviço para determinar em que nó está o contentor Jenkins. Secure Shell (SSH) inscreva-se neste nó.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. Obtenha o ID de instância do contentor com `docker ps -a`.
1. Secure Shell (SSH) inscreva-se no recipiente e cola o caminho que foi mostrado no portal Jenkins. Por exemplo, se no portal mostrar o caminho `PATH_TO_INITIAL_ADMIN_PASSWORD`, executar os seguintes comandos:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. Na página Jenkins Getting Started, escolha os plugins Select para instalar a opção, selecione a caixa de verificação **Zero** e clique em instalar.
1. Crie um utilizador ou selecione para continuar como administrador.

Depois de configurar o Jenkins, avance para [criar e configurar um trabalho](#create-and-configure-a-jenkins-job)do Jenkins.  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Configurar o Jenkins fora de um cluster do Service Fabric

Pode configurar o Jenkins dentro ou fora de um cluster do Service Fabric. As secções seguintes mostram como configurá-lo fora de um cluster.

1. Certifique-se de que o Docker está instalado na sua máquina executando `docker info` no terminal. A saída indica se o serviço Docker está a funcionar.

1. Se o Docker não estiver instalado, execute os seguintes comandos:

    ```sh
    sudo apt-get install wget
    wget -qO- https://get.docker.io/ | sh
    ```

1. Extraia a imagem do contentor do Jenkins do Service Fabric: `docker pull rapatchi/jenkins:latest`. Esta imagem inclui o plug-in do Jenkins do Service Fabric pré-instalado.
1. Execute a imagem do contentor:`docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. Obtenha o ID da instância de imagem do contentor. Pode listar todos os contentores de Docker com o comando `docker ps –a`
1. Inscreva-se no portal Jenkins com os seguintes passos:

   1. Inscreva-se numa concha do Jenkins do seu anfitrião. Utilize os primeiros quatro dígitos do ID do recipiente. Por exemplo, se o ID do recipiente for `2d24a73b5964`, utilize `2d24`.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. A partir da concha jenkins, obtenha a senha de administração para a sua instância de recipiente:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. Para iniciar sessão no painel jenkins, abra o seguinte URL num navegador web: `http://<HOST-IP>:8080`. Use a palavra-passe do passo anterior para desbloquear o Jenkins.
   1. (Opcional.) Depois de iniciar sessão pela primeira vez, pode criar a sua própria conta de utilizador e usá-lo para os seguintes passos, ou pode continuar a utilizar a conta de administrador. Se criar um utilizador, tem de continuar com esse utilizador.
1. Instale o GitHub para trabalhar com o Jenkins utilizando os passos para [gerar uma nova chave SSH e adicioná-la ao agente SSH](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
   * Utilize as instruções fornecidas pelo GitHub para gerar a chave SSH e adicioná-la à conta GitHub que está a alojar o repositório.
   * Execute os comandos mencionados na ligação anterior na shell Docker do Jenkins (e não no seu anfitrião).
   * Para iniciar sessão na shell do Jenkins a partir do seu anfitrião, utilize o seguinte comando:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Certifique-se de que o cluster ou máquina onde a imagem do contentor Jenkins está hospedada tem um endereço IP virado para o público. Isto permite que a instância do Jenkins receba notificações do GitHub.

Depois de configurar o Jenkins, continue para a próxima secção, [crie e configure um trabalho](#create-and-configure-a-jenkins-job)de Jenkins.

## <a name="create-and-configure-a-jenkins-job"></a>Criar e configurar uma tarefa do Jenkins

Os passos nesta secção mostram-lhe como configurar um trabalho de Jenkins para responder às mudanças num repo GitHub, buscar as mudanças e construí-las. No final desta secção, é direcionado para os passos finais para configurar o trabalho para implementar a sua aplicação com base na implementação para um ambiente de desenvolvimento/teste ou para um ambiente de produção. 

1. No painel jenkins, clique **em New Item**.
1. Introduza um nome para o item (por exemplo, **MyJob**). Selecione **free-style project** (projeto de estilo livre) e clique em **OK**.
1. A página de configuração de trabalho abre. (Para chegar à configuração a partir do painel de instrumentos jenkins, clique no trabalho e, em seguida, clique **em Configurar**).

1. No separador **Geral,** verifique a caixa para o **projeto GitHub**e especifique o URL do projeto GitHub. Este URL aloja a aplicação Java do Service Fabric que pretende integrar no fluxo de integração contínua do Jenkins e implementação contínua (CI/CD) (por exemplo, `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

1. No separador **Source Code Management,** selecione **Git**. Especifique o URL do repositório que aloja a aplicação Java do Service Fabric que quer integrar no fluxo CI/CD do Jenkins (por exemplo, `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Também pode especificar qual ramo construir (por exemplo, `/master`).
1. Configure o seu repositório *GitHub* para falar com Jenkins:

   a. Na sua página de repositório GitHub, vá a **Definições** > **Integrações e Serviços.**

   b. Selecione **Add Service** (Adicionar Serviço), escreva **Jenkins** e selecione **Jenkins-GitHub plugin** (Plug-in Jenkins-GitHub).

   c. Introduza o URL do webhook do Jenkins (por predefinição, deve ser `http://<PublicIPorFQDN>:8081/github-webhook/`). Clique em **add/update service** (adicionar/atualizar serviço).

   d. É enviado um evento de teste para a instância do Jenkins. Deverá ver uma verificação a verde através do webhook do GitHub, e o seu projeto será compilado.

1. No separador **Build Triggers** em Jenkins, selecione qual a opção de construção que deseja. Para este exemplo, pretende desencadear uma construção sempre que um impulso ao repositório ocorra, por isso selecione o gatilho do **gancho GitHub para a sondagem GITScm**. (Anteriormente, esta opção era denominada **Build when a change is pushed to GitHub** [Compilar quando é enviada uma alteração para o GitHub]).
1. No separador **Build,** faça um dos seguintes dependendo se está a construir uma aplicação Java ou uma aplicação .NET Core:

   * **Para aplicações java:** A partir do **passo de construção Add** drop-down, selecione Invoke **Gradle Script**. Clique **em Avançado**. No menu avançado, especifique o roteiro para **a raiz construir** script para a sua aplicação. Este obtém o build.gradle a partir do caminho especificado e funciona em conformidade. Para a [aplicação ActorCounter,](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter)isto é: `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`.

     ![Ação de Compilação do Jenkins do Service Fabric](./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png)

   * **Para aplicações .NET Core:** A partir do **passo de construção Adicionar** descaído, selecione Executar **Shell**. Na caixa de comando que aparece, o diretório primeiro precisa de ser alterado para o caminho onde o ficheiro build.sh está localizado. Uma vez alterado o diretório, o guião build.sh pode ser executado e irá construir a aplicação.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     A imagem seguinte mostra um exemplo dos comandos que são usados para construir a amostra de [Counter Service](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) com um nome de trabalho Jenkins de CounterServiceApplication.

      ![Ação de Compilação do Jenkins do Service Fabric](./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png)

1. Para configurar o Jenkins para implementar a sua app num cluster de Tecido de Serviço nas ações pós-construção, precisa da localização do certificado desse cluster no seu contentor Jenkins. Escolha um dos seguintes dependendo se o seu contentor Jenkins está a funcionar dentro ou fora do seu cluster e note a localização do certificado de cluster:

   * **Para jenkins correndo dentro do seu aglomerado:** O caminho para o certificado pode ser encontrado ecoando o valor da *variável ambiente Certificates_JenkinsOnSF_Code_MyCert_PEM* a partir do interior do recipiente.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **Para jenkins correndo fora do seu aglomerado:** Siga estes passos para copiar o certificado de cluster para o seu recipiente:
     1. O seu certificado deve estar em formato PEM. Se não tiver um ficheiro PEM, pode criar um a partir do ficheiro PFX do certificado. Se o seu ficheiro PFX não estiver protegido por palavra-passe, execute o seguinte comando do seu anfitrião:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
        ``` 

        Se o ficheiro PFX estiver protegido por palavra-passe, inclua a palavra-passe no parâmetro `-passin`. Por exemplo:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
        ``` 

     1. Para obter a identificação do recipiente do seu contentor Jenkins, faça `docker ps` do seu anfitrião.
     1. Copie o ficheiro PEM para o seu recipiente com o seguinte comando Docker:
    
        ```sh
        docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
        ``` 

Está quase acabando! Mantém o emprego do Jenkins aberto. A única tarefa restante é configurar os passos pós-construção para implementar a sua aplicação no seu cluster De Serviço Tecido:

* Para implantar num ambiente de desenvolvimento ou teste, siga os passos na implementação da [Configuração utilizando o ponto final](#configure-deployment-using-cluster-management-endpoint)da gestão do cluster .
* Para implantar num ambiente de produção, siga os passos na implementação da [Configuração utilizando credenciais Azure](#configure-deployment-using-azure-credentials).

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Configurar a implementação utilizando o ponto final da gestão do cluster

Para ambientes de desenvolvimento e teste, pode utilizar o ponto final de gestão do cluster para implementar a sua aplicação. Configurar a ação pós-construção com o ponto final de gestão do cluster para implementar a sua aplicação requer a menor quantidade de configuração. Se estiver a implantar-se num ambiente de produção, salte para a configuração da [configuração da configuração](#configure-deployment-using-azure-credentials) de um serviço de Diretório Ativo Azure para utilizar durante a implementação.    

1. No trabalho de Jenkins, clique no separador **Post-build Actions.** 
1. No menu pendente **Post-Build Actions** (Ações de Pós-compilação), selecione **Deploy Service Fabric Project** (Implementar Projeto do Service Fabric). 
1. Sob a configuração do **cluster do tecido de serviço,** selecione o botão de rádio de ponta de gestão de tecido de **serviço.**
1. Para **o Anfitrião de Gestão,** introduza o ponto final de ligação para o seu cluster; por exemplo, `{your-cluster}.eastus.cloudapp.azure.com`.
1. Para **a Chave do Cliente** e o Cliente **Cert,** insira a localização do ficheiro PEM no seu recipiente Jenkins; por exemplo, `/var/jenkins_home/clustercert.pem`. (Copiou a localização do certificado no último passo da [Create e configuraum trabalho](#create-and-configure-a-jenkins-job)da Jenkins .)
1. Sob configuração de **aplicação,** configure o nome da **aplicação,** o tipo de **aplicação,** e os campos (relativos) para o manifesto de **aplicação.**

   ![Tecido de serviço Jenkins post-build Action configurar ponto final de gestão](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. Clique em **verificar configuração**. Na verificação bem sucedida, clique em **Guardar**. O teu oleoduto jenkins está agora totalmente configurado. Avance para [os próximos passos](#next-steps) para testar a sua implantação.

## <a name="configure-deployment-using-azure-credentials"></a>Configure a implementação usando credenciais Azure

Para ambientes de produção, configurar uma credencial Azure para implementar a sua aplicação é fortemente recomendado. Esta secção mostra-lhe como configurar um serviço de diretório Ativo Azure para utilizar para implementar a sua aplicação na ação pós-construção. Pode atribuir diretores de serviço a funções no seu diretório para limitar as permissões do trabalho do Jenkins. 

Para ambientes de desenvolvimento e teste, pode configurar as credenciais Azure ou o ponto final de gestão do cluster para implementar a sua aplicação. Para mais detalhes sobre como configurar um ponto final de gestão de cluster, consulte a [implementação do Configure utilizando o ponto final](#configure-deployment-using-cluster-management-endpoint)da gestão do cluster .   

1. Para criar um diretor de serviço Azure Ative Directory e atribuir permissões na sua subscrição Azure, siga os passos em Utilizar o portal para criar um diretor de [diretório ativo Azure e um diretor](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)de serviço. Preste atenção ao seguinte:

   * Ao seguir os passos no tópico, certifique-se de copiar e guardar os seguintes valores: ID de *aplicação,* *chave de aplicação,* *ID do diretório (ID*do inquilino) e *ID de subscrição*. Precisas deles para configurar as credenciais azure em Jenkins.
   * Se não tiver [as permissões necessárias](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) no seu diretório, terá de pedir a um administrador que lhe conceda as permissões ou crie o diretor de serviço para si, ou terá de configurar o ponto final de gestão do seu cluster nas **Ações Pós-Construção** para o seu trabalho em Jenkins.
   * Na secção [de aplicação Create a Azure Ative Directory,](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) pode introduzir qualquer URL bem formado para o **URL de início de sessão**.
   * Na [aplicação Atribuir a uma secção De Função,](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) pode atribuir à sua aplicação a função *Reader* no grupo de recursos para o seu cluster.

1. De volta ao trabalho do Jenkins, clique no separador **Post-Build Actions.**
1. No menu pendente **Post-Build Actions** (Ações de Pós-compilação), selecione **Deploy Service Fabric Project** (Implementar Projeto do Service Fabric). 
1. Sob a configuração do **cluster do tecido de serviço,** clique em selecionar o cluster de tecido de **serviço**. Clique **em Adicionar** ao lado das **credenciais Azure**. Clique em **Jenkins** para selecionar o Fornecedor de Credenciais Jenkins.
1. No Fornecedor de Credenciais Jenkins, selecione **Microsoft Azure Service Principal** a partir do drop-down **Kind.**
1. Utilize os valores que guardou ao configurar o seu diretor de serviço no passo 1 para definir os seguintes campos:

   * **ID do cliente**: *ID de aplicação*
   * **Segredo do Cliente**: *Chave de aplicação*
   * **ID do inquilino**: *ID do diretório*
   * **ID de subscrição**: *ID de subscrição*
1. Introduza um **ID** descritivo que utiliza para selecionar a credencial em Jenkins e uma breve **Descrição**. Em seguida, clique em **Verificar o Diretor de Serviço**. Se a verificação for bem sucedida, clique em **Adicionar**.

   ![Serviço Fabric Jenkins entra nas credenciais azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. De volta sob a **configuração**do cluster de tecido de serviço, certifique-se de que a sua nova credencial está selecionada para **credenciais Azure**. 
1. A partir da queda do **Grupo de Recursos,** selecione o grupo de recursos do cluster para o quais pretende implementar a aplicação.
1. A partir da queda do **Tecido de Serviço,** selecione o cluster para o que pretende implementar a aplicação.
1. Para **a Chave do Cliente** e o Cliente **Cert,** insira a localização do ficheiro PEM no seu recipiente Jenkins. Por exemplo, `/var/jenkins_home/clustercert.pem`. 
1. Sob configuração de **aplicação,** configure o nome da **aplicação,** o tipo de **aplicação,** e os campos (relativos) para o manifesto de **aplicação.**
    ![Service Fabric Jenkins Post-Build Action - Configure Azure credenciais](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. Clique em **verificar configuração**. Na verificação bem sucedida, clique em **Guardar**. O teu oleoduto jenkins está agora totalmente configurado. Continue a [seguir os próximos passos](#next-steps) para testar a sua implantação.

## <a name="troubleshooting-the-jenkins-plugin"></a>Resolver problemas nos plug-ins do Jenkins

Se se deparar com erros nos plug-ins do Jenkins, comunique os problemas com os componentes específicos no [Jenkins JIRA](https://issues.jenkins-ci.org/).

## <a name="ideas-to-try"></a>Ideias para experimentar

O GitHub e o Jenkins estão agora configurados. Considere fazer algumaalteração de amostra no projeto `reliable-services-actor-sample/Actors/ActorCounter` no seu garfo do repositório, https://github.com/Azure-Samples/service-fabric-java-getting-started. Empurre as alterações para o ramo `master` remoto (ou qualquer ramo com o que tenha configurado para trabalhar). Isto aciona a tarefa `MyJob` do Jenkins, que configurou. Ele rebusca as mudanças do GitHub, constrói-as e implementa a aplicação para o cluster que especificaste em ações pós-construção.  

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Jenkins no Azure](/azure/Jenkins/)