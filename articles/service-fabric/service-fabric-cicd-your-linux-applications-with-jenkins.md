---
title: Compilação e integração para as suas aplicações do Azure Service Fabric do Linux com o Jenkins contínuas | Documentos da Microsoft
description: Compilação e integração para a sua aplicação do Service Fabric do Linux com o Jenkins contínuas
services: service-fabric
documentationcenter: java
author: sayantancs
manager: jpconnock
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/31/2018
ms.author: saysa
ms.openlocfilehash: 3b1e6f769d5c65065d95ac96c4ab4ed10702e5cf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58089901"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Utilizar o Jenkins para criar e implementar as suas aplicações do Linux
O Jenkins é uma ferramenta popular para integração e implementação contínuas de aplicações. Eis como pode utilizar o Jenkins para compilar e implementar a sua aplicação do Azure Service Fabric.

## <a name="topic-overview"></a>Descrição geral de tópico
Este artigo aborda várias maneiras possíveis de configuração de seu ambiente do Jenkins, bem como diferentes formas de implementar a sua aplicação para um cluster do Service Fabric depois que ele foi projetado. Siga estes passos gerais com êxito, configurar o Jenkins, tirar alterações do GitHub, crie seu aplicativo e implementá-la ao seu cluster:

1. Certifique-se que instale o [pré-requisitos](#prerequisites).
1. Em seguida, siga os passos nestas secções para configurar o Jenkins:
   * [Configurar o Jenkins num cluster do Service Fabric](#set-up-jenkins-inside-a-service-fabric-cluster), 
   * [Configurar o Jenkins fora de um cluster do Service Fabric](#set-up-jenkins-outside-a-service-fabric-cluster), ou
   * [Instalar o plug-in do Service Fabric num ambiente Jenkins existente](#install-service-fabric-plugin-in-an-existing-jenkins-environment).
1. Depois que tiver configurado a Jenkins, siga os passos em [criar e configurar uma tarefa do Jenkins](#create-and-configure-a-jenkins-job) para configurar o GitHub para disparar o Jenkins quando forem feitas alterações à sua aplicação e configurar o seu pipeline de tarefas do Jenkins através do passo de compilação para extrair o altera a partir do GitHub e crie seu aplicativo. 
1. Por fim, configure a etapa de pós-compilação de tarefa do Jenkins para implementar a sua aplicação ao seu cluster do Service Fabric. Existem duas formas de configurar o Jenkins para implementar a aplicação num cluster:    
   * Para ambientes de desenvolvimento e teste, utilize [configurar a implementação com o ponto final de gestão do cluster](#configure-deployment-using-cluster-management-endpoint). Este é o método de implementação mais simples de configurar.
   * Para ambientes de produção, utilize [configurar a implementação com as credenciais do Azure](#configure-deployment-using-azure-credentials). A Microsoft recomenda este método para ambientes de produção porque com credenciais do Azure pode limitar o acesso que tem uma tarefa do Jenkins para os recursos do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que o Git instalado localmente. Pode instalar a versão adequada do Git [página de transferências do Git](https://git-scm.com/downloads) com base no seu sistema operativo. Se estiver familiarizado com o Git, saiba mais sobre-lo a partir da [documentação do Git](https://git-scm.com/docs).
- Este artigo utiliza a *Fabric introdução ao exemplo de serviço* no GitHub: [ https://github.com/Azure-Samples/service-fabric-java-getting-started ](https://github.com/Azure-Samples/service-fabric-java-getting-started) para a aplicação criar e implementar. Pode copiar este repositório para acompanhar, ou, com algumas modificações para as instruções, utilize o seu próprio projeto do GitHub.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Instale o plug-in do Service Fabric num ambiente existente do Jenkins
Se estiver a adicionar o plug-in do Service Fabric para um ambiente existente do Jenkins, precisa do seguinte:

- O [CLI do Service Fabric](service-fabric-cli.md) (sfctl).

   > [!NOTE]
   > Certifique-se de que instalar a CLI no nível do sistema em vez de no nível do usuário, então, Jenkins pode executar comandos da CLI. 
   >

- Para implementar aplicações Java, instale as [Gradle e Open JDK 8.0](service-fabric-get-started-linux.md#set-up-java-development). 
- Para implementar aplicações de .NET Core 2.0, instale o [.NET Core 2.0 SDK](service-fabric-get-started-linux.md#set-up-net-core-20-development). 

Depois de instalar os pré-requisitos necessários para o seu ambiente, pode procurar o plug-in do Azure Service Fabric no marketplace do Jenkins e instalá-lo.

Depois de instalar o plug-in, avançar diretamente para [criar e configurar uma tarefa do Jenkins](#create-and-configure-a-jenkins-job).


## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Configurar o Jenkins num cluster do Service Fabric

Pode configurar o Jenkins dentro ou fora de um cluster do Service Fabric. As secções seguintes mostram como configurá-la num cluster ao utilizar uma conta de armazenamento do Azure para guardar o estado da instância do contentor.

### <a name="prerequisites"></a>Pré-requisitos
- Tem um cluster do Service Fabric do Linux com o Docker instalado. Clusters do Service Fabric em execução no Azure já tem o Docker instalado. Se estiver a executar o cluster localmente (ambiente de desenvolvimento do OneBox), verifique se o Docker está instalado no seu computador com o `docker info` comando. Se não estiver instalado, instale-o usando os seguintes comandos:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Certifique-se de que a porta 8081 está especificada como um ponto final personalizado no cluster. Se estiver a utilizar um cluster local, certifique-se de que a porta 8081 está aberta no computador anfitrião e que tem um endereço IP destinado ao público.
   >

### <a name="steps"></a>Passos
1. Clone a aplicação, utilizando os seguintes comandos:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. Manter o estado do contentor do Jenkins numa partilha de ficheiros:
   1. Criar uma conta de armazenamento do Azure no **mesma região** seu cluster com um nome, tais como `sfjenkinsstorage1`.
   1. Criar uma **partilha de ficheiros** sob o armazenamento de conta com um nome como `sfjenkins`.
   1. Clique em **Connect** para a partilha de ficheiros e tenha em atenção os valores ele exibe sob **ligar a partir do Linux**, o valor deve ter um aspeto semelhante à abaixo:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Para partilhas de cifs de montagem, tem de ter o pacote de utils do cifs instalado em nós de cluster.      
   >

1. Atualize os valores de marcador de posição no `setupentrypoint.sh` skript os detalhes de armazenamento do azure a partir do passo 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Substitua `[REMOTE_FILE_SHARE_LOCATION]` com o valor `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` a partir da saída do connect no passo 2 acima.
   * Substitua `[FILE_SHARE_CONNECT_OPTIONS_STRING]` com o valor `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` do passo 2 acima.

1. **Proteger o Cluster apenas:** 
   
   Para configurar a implantação de aplicativos num cluster seguro do Jenkins, o certificado de cluster tem de ser acessível dentro do contentor do Jenkins. No *Applicationmanifest* em ficheiro, o **ContainerHostPolicies** marca adicionar esta referência de certificado e atualize o valor do thumbprint com que o certificado de cluster.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Além disso, adicione as seguintes linhas sob o **ApplicationManifest** etiqueta (root) no *Applicationmanifest* de ficheiros e atualize o valor do thumbprint com que o certificado de cluster.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

1. Ligar ao cluster e instalar a aplicação de contentor.

   **Proteger o Cluster**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   O comando anterior usa o certificado no formato PEM. Se o seu certificado está no formato PFX, pode utilizar o seguinte comando para convertê-lo. Especifique se o ficheiro PFX não protegidos por senha, o **passin** parâmetro como `-passin pass:`.
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
   > Pode demorar alguns minutos para a imagem do Jenkins a serem baixados no cluster.
   >

1. A partir do browser, aceda a `http://PublicIPorFQDN:8081`. Fornece o caminho da palavra-passe de administrador inicial necessária para iniciar sessão. 
1. Ver o Service Fabric Explorer para determinar o nó que o contentor do Jenkins está a ser executado. Secure Shell (SSH) de início de sessão para este nó.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. Obtenha o ID de instância do contentor com `docker ps -a`.
1. Secure Shell (SSH) de início de sessão para o contentor e cole o caminho que lhe foi mostrado no portal do Jenkins. Por exemplo, se no portal for mostrado o caminho `PATH_TO_INITIAL_ADMIN_PASSWORD`, execute os seguintes comandos:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. Na página de introdução do Jenkins, selecione os plug-ins selecionados a opção de instalação, selecione o **None** caixa de verificação e clique em instalar.
1. Criar um utilizador ou optar por continuar como administrador.

Depois que tiver configurado a Jenkins, avançar diretamente para [criar e configurar uma tarefa do Jenkins](#create-and-configure-a-jenkins-job).  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Configurar o Jenkins fora de um cluster do Service Fabric

Pode configurar o Jenkins dentro ou fora de um cluster do Service Fabric. As secções seguintes mostram como configurá-lo fora de um cluster.

### <a name="prerequisites"></a>Pré-requisitos
- Certifique-se de que o Docker está instalado no seu computador. Os comandos seguintes podem ser utilizados para instalar o Docker a partir do terminal:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

  Quando executa `docker info` no terminal, a saída deve mostrar que o serviço Docker está em execução.

### <a name="steps"></a>Passos
1. Extraia a imagem do contentor do Jenkins do Service Fabric: `docker pull rapatchi/jenkins:latest`. Esta imagem inclui o plug-in do Jenkins do Service Fabric pré-instalado.
1. Execute a imagem do contentor:`docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. Obtenha o ID da instância de imagem do contentor. Pode listar todos os contentores de Docker com o comando `docker ps –a`
1. Inicie sessão no portal do Jenkins com os seguintes passos:

   1. Iniciar sessão uma shell do Jenkins a partir do seu anfitrião. Utilize os primeiros quatro dígitos do ID de contentor. Por exemplo, se o ID de contentor for `2d24a73b5964`, utilize `2d24`.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. A partir da shell do Jenkins, obtenha a palavra-passe de administrador para a instância do contentor:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. Para iniciar sessão no dashboard do Jenkins, abra a seguinte URL num navegador da web: `http://<HOST-IP>:8080`. Utilize a palavra-passe do passo anterior para desbloquear o Jenkins.
   1. (Opcional.) Depois de iniciar sessão pela primeira vez, pode criar sua própria conta de utilizador e utilizá-la para os seguintes passos, ou pode continuar a utilizar a conta de administrador. Se criar um utilizador, terá de continuar com esse utilizador.
1. Configurar o GitHub para funcionar com o Jenkins através dos passos em [gerar uma chave SSH nova e adicioná-la ao agente SSH](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
   * Utilize as instruções fornecidas pelo GitHub para gerar a chave SSH e adicioná-la à conta GitHub que está a alojar o repositório.
   * Execute os comandos mencionados na ligação anterior na shell Docker do Jenkins (e não no seu anfitrião).
   * Para iniciar sessão na shell do Jenkins a partir do seu anfitrião, utilize o seguinte comando:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Certifique-se de que o cluster ou a máquina em que a imagem de contentor do Jenkins está alojada tem um endereço IP destinado ao público. Isto permite que a instância do Jenkins receba notificações do GitHub.

Depois que tiver configurado a Jenkins, avance para a próxima seção [criar e configurar uma tarefa do Jenkins](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Criar e configurar uma tarefa do Jenkins

Os passos nesta secção mostram como configurar uma tarefa do Jenkins para responder a alterações num repositório do GitHub, obter as alterações e criá-los. No final desta seção, está direcionado para os últimos passos para configurar a tarefa de implementar a sua aplicação com base em se estiver a implementar para um ambiente de desenvolvimento/teste ou para um ambiente de produção. 

1. No dashboard do Jenkins, clique em **Novo Item**.
1. Introduza um nome para o item (por exemplo, **MyJob**). Selecione **free-style project** (projeto de estilo livre) e clique em **OK**.
1. É aberta a página de configuração da tarefa. (Para obter a configuração do dashboard do Jenkins, clique na tarefa e, em seguida, clique em **configurar**).

1. Sobre o **gerais** separador, marque a caixa **projeto GitHub**, e especifique o URL de projeto do GitHub. Este URL aloja a aplicação Java do Service Fabric que pretende integrar no fluxo de integração contínua do Jenkins e implementação contínua (CI/CD) (por exemplo, `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

1. Sobre o **gestão de código fonte** separador, selecione **Git**. Especifique o URL do repositório que aloja a aplicação Java do Service Fabric que quer integrar no fluxo CI/CD do Jenkins (por exemplo, `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Também pode especificar o ramo a compilar (por exemplo, `/master`).
1. Configurar o seu *GitHub* repositório para comunicar com o Jenkins:

   a. Na página de repositório do GitHub, aceda a **configurações** > **integrações e serviços**.

   b. Selecione **Add Service** (Adicionar Serviço), escreva **Jenkins** e selecione **Jenkins-GitHub plugin** (Plug-in Jenkins-GitHub).

   c. Introduza o URL do webhook do Jenkins (por predefinição, deve ser `http://<PublicIPorFQDN>:8081/github-webhook/`). Clique em **add/update service** (adicionar/atualizar serviço).

   d. É enviado um evento de teste para a instância do Jenkins. Deverá ver uma verificação a verde através do webhook do GitHub, e o seu projeto será compilado.

1. Sobre o **criar Acionadores** separador no Jenkins, selecione a opção que pretende de compilação. Neste exemplo, pretende acionar uma compilação sempre que um push para o repositório, por isso, selecione **acionador de hook do GitHub para consulta GITScm**. (Anteriormente, esta opção era denominada **Build when a change is pushed to GitHub** [Compilar quando é enviada uma alteração para o GitHub]).
1. Sobre o **criar** separador, efetue um dos seguintes, dependendo se está a criar uma aplicação de Java ou uma aplicação .NET Core:

   * **Para aplicações de Java:** Partir do **Adicionar passo de compilação** menu pendente, selecione **invocar Gradle Script**. Clique em **Advanced**. No menu avançado, especifique o caminho para **script de compilação de raiz** para a sua aplicação. Este obtém o build.gradle a partir do caminho especificado e funciona em conformidade. Para o [ActorCounter aplicativo](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter), isto é: `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`.

     ![Ação de Compilação do Jenkins do Service Fabric][build-step]

   * **Para aplicativos do .NET Core:** Partir do **Adicionar passo de compilação** menu pendente, selecione **executar Shell**. Na caixa de comando que aparece, o diretório em primeiro lugar tem de ser alterado para o caminho onde está localizado o ficheiro de build.sh. Assim que o diretório foi alterado, o script de build.sh pode ser executado e irá criar a aplicação.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     Captura de ecrã seguinte mostra um exemplo dos comandos que são utilizados para criar o [contador serviço](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) exemplo com um nome de tarefa do Jenkins de CounterServiceApplication.

      ![Ação de Compilação do Jenkins do Service Fabric][build-step-dotnet]

1. Para configurar o Jenkins para implementar a sua aplicação num cluster do Service Fabric nas ações de pós-compilação, terá a localização do certificado desse cluster no seu contentor do Jenkins. Escolha um dos seguintes, dependendo se o contentor do Jenkins está em execução dentro ou fora do cluster e tenha em atenção a localização do certificado de cluster:

   * **Do Jenkins em execução dentro do seu cluster:** O caminho para o certificado pode ser encontrado ao ecoar o valor do *Certificates_JenkinsOnSF_Code_MyCert_PEM* variável de ambiente de dentro do contentor.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **Do Jenkins em execução fora do seu cluster:** Siga estes passos para copiar o certificado de cluster para o seu contentor:
     1. O certificado tem de estar no formato PEM. Se não tiver um ficheiro PEM, pode criar um a partir do ficheiro PFX de certificado. Se o ficheiro PFX não protegidos por senha, execute o seguinte comando a partir do seu anfitrião:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
        ``` 

        Se o ficheiro PFX protegido de palavra-passe, incluir a palavra-passe no `-passin` parâmetro. Por exemplo:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
        ``` 

     1. Para obter o ID de contentor para o contentor do Jenkins, execute `docker ps` partir do seu anfitrião.
     1. Copie o ficheiro PEM para o contentor com o seguinte comando do Docker:
    
        ```sh
        docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
        ``` 

Está quase terminando! Mantenha aberto, a tarefa do Jenkins. É a única tarefa restante configurar os passos de pós-compilação para implementar a sua aplicação no seu cluster do Service Fabric:

* Para implementar um ambiente de desenvolvimento ou teste, siga os passos em [configurar a implementação com o ponto final de gestão do cluster](#configure-deployment-using-cluster-management-endpoint).
* Para implementar um ambiente de produção, siga os passos em [configurar a implementação com as credenciais do Azure](#configure-deployment-using-azure-credentials).

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Configurar a implementação com o ponto final de gestão do cluster
Para ambientes de desenvolvimento e teste, pode utilizar o ponto final de gestão de cluster para implementar a sua aplicação. Configurar a ação de pós-compilação com o ponto de final de gestão de cluster para implementar a sua aplicação requer o mínimo de configuração. Se estiver a implementar um ambiente de produção, avançar diretamente para [configurar a implementação com as credenciais do Azure](#configure-deployment-using-azure-credentials) para configurar um principal de serviço do Azure Active Directory para utilizar durante a implementação.    

1. A tarefa do Jenkins, clique nas **ações de pós-compilação** separador. 
1. No menu pendente **Post-Build Actions** (Ações de Pós-compilação), selecione **Deploy Service Fabric Project** (Implementar Projeto do Service Fabric). 
1. Sob **configuração do Cluster do Service Fabric**, selecione a **preencher o ponto final de gestão do Service Fabric** botão de opção.
1. Para **anfitrião de gestão**, introduza o ponto final de ligação para o seu cluster; por exemplo `{your-cluster}.eastus.cloudapp.azure.com`.
1. Para **chave de cliente** e **Client Cert**, introduza a localização do ficheiro PEM no seu contentor do Jenkins; por exemplo `/var/jenkins_home/clustercert.pem`. (Que copiou a localização do certificado, o último passo da [criar e configurar uma tarefa do Jenkins](#create-and-configure-a-jenkins-job).)
1. Sob **configuração da aplicação**, configure a **nome da aplicação**, **tipo de aplicação**e (relativo) **caminho para o manifesto de aplicativo** campos.

   ![Ação de pós-compilação do Jenkins de recursos de infraestrutura do serviço configurar o ponto final de gestão](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. Clique em **verificar a configuração**. Verificação bem sucedida, clique em **guardar**. O pipeline de tarefas do Jenkins está agora totalmente configurado. Avançar diretamente para [próximos passos](#next-steps) para testar a implementação.

## <a name="configure-deployment-using-azure-credentials"></a>Configurar a implementação com as credenciais do Azure
Para ambientes de produção, configurar uma credencial do Azure para implementar a aplicação é altamente recomendável. Esta secção mostra-lhe como configurar um principal de serviço do Azure Active Directory a utilizar para implementar a sua aplicação na ação de pós-compilação. Pode atribuir os principais de serviço para as funções no seu diretório para limitar as permissões da tarefa do Jenkins. 

Para ambientes de desenvolvimento e teste, pode configurar as credenciais do Azure ou o ponto de final de gestão de cluster para implementar a sua aplicação. Para obter detalhes sobre como configurar um ponto de final de gestão do cluster, consulte [configurar a implementação com o ponto final de gestão do cluster](#configure-deployment-using-cluster-management-endpoint).   

1. Para criar um Azure Active Directory, principal de serviço e o atribuir permissões na sua subscrição do Azure, siga os passos em [utilizar o portal para criar um Azure Active Directory principal de aplicações e serviço](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Preste atenção ao seguinte:

   * Ao seguir os passos no tópico, certifique-se de que copia e guarda os seguintes valores: *ID da aplicação*, *chave da aplicação*, *ID de diretório (ID de inquilino)*, e *ID de subscrição*. Precisa para configurar as credenciais do Azure no Jenkins.
   * Se não tiver o [permissões obrigatórias](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) em seu diretório, terá de pedir a um administrador para lhe conceder as permissões ou criar o principal de serviço para, ou terá de configurar o ponto final de gestão para o cluster no **ações de pós-compilação** para a sua tarefa no Jenkins.
   * Na [criar uma aplicação do Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) secção, pode introduzir qualquer URL bem formado para o **URL de início de sessão**.
   * Na [atribua a aplicação a uma função](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) secção, pode atribuir a aplicação a *leitor* função no grupo de recursos para o seu cluster.

1. Novamente na tarefa do Jenkins, clique nas **ações de pós-compilação** separador.
1. No menu pendente **Post-Build Actions** (Ações de Pós-compilação), selecione **Deploy Service Fabric Project** (Implementar Projeto do Service Fabric). 
1. Sob **configuração do Cluster do Service Fabric**, selecione a **selecione o Cluster do Service Fabric** botão de opção. Clique em **Add** junto a **credenciais do Azure**. Clique em **Jenkins** para selecionar o fornecedor de credenciais do Jenkins.
1. No provedor de credenciais do Jenkins, selecione **Principal de serviço do Microsoft Azure** partir do **tipo** pendente.
1. Utilize os valores que guardou ao definir a sua principal de serviço no passo 1 para definir os seguintes campos:

   * **ID de cliente**: *ID da aplicação*
   * **Segredo do cliente**: *Chave da aplicação*
   * **ID do inquilino**: *ID do diretório*
   * **ID de subscrição**: *ID de subscrição*
1. Introduza um **ID** que utilizou para selecionar a credencial no Jenkins e a breve **Descrição**. Em seguida, clique em **Verifique se o Principal de serviço**. Se a verificação for bem sucedida, clique em **adicionar**.

   ![Jenkins do Service Fabric introduza credenciais do Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. Volta ao abrigo **configuração do Cluster do Service Fabric**, certifique-se de que a sua nova credencial está selecionada para **credenciais do Azure**. 
1. Partir do **grupo de recursos** pendente, selecione o grupo de recursos do cluster que pretende implementar a aplicação.
1. Partir do **Service Fabric** pendente, selecione o cluster que pretende implementar a aplicação.
1. Para **chave de cliente** e **Client Cert**, introduza a localização do ficheiro PEM no seu contentor do Jenkins. Por exemplo, `/var/jenkins_home/clustercert.pem`. 
1. Sob **configuração da aplicação**, configure a **nome da aplicação**, **tipo de aplicação**e (relativo) **caminho para o manifesto de aplicativo** campos.
    ![Ação de pós-compilação do Jenkins de recursos de infraestrutura do serviço configurar credenciais do Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. Clique em **verificar a configuração**. Verificação bem sucedida, clique em **guardar**. O pipeline de tarefas do Jenkins está agora totalmente configurado. Continue para [próximos passos](#next-steps) para testar a implementação.

## <a name="troubleshooting-the-jenkins-plugin"></a>Resolver problemas nos plug-ins do Jenkins

Se se deparar com erros nos plug-ins do Jenkins, comunique os problemas com os componentes específicos no [Jenkins JIRA](https://issues.jenkins-ci.org/).

## <a name="next-steps"></a>Passos Seguintes
O GitHub e o Jenkins estão agora configurados. Considere fazer algumas alterações de exemplo da `reliable-services-actor-sample/Actors/ActorCounter` projeto no seu fork do repositório, https://github.com/Azure-Samples/service-fabric-java-getting-started. Enviar suas alterações para a ligação remota `master` branch (ou qualquer ramo que tenha configurado para trabalhar com). Isto aciona a tarefa `MyJob` do Jenkins, que configurou. Obtém as alterações a partir do GitHub, compila-as e implementa a aplicação no cluster que especificou nas ações de pós-compilação.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png

