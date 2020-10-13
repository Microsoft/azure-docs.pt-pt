---
title: Configuure Jenkins para uma aplicação Java em Tecido de Serviço em Azure
description: Neste tutorial, saiba como configurar a integração contínua com Jenkins para implementar uma aplicação Java Service Fabric.
ms.topic: tutorial
ms.date: 08/27/2018
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 4e29104f0586a8e18afb7a640903c57ce988b692
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91531977"
---
# <a name="tutorial-configure-a-jenkins-environment-to-enable-cicd-for-a-java-application-on-service-fabric"></a>Tutorial: Configurar um ambiente Jenkins para ativar o CI/CD para uma aplicação Java no Service Fabric

Este tutorial é a quinta parte de uma série. Mostra como utilizar o Jenkins para implementar atualizações na sua aplicação. Neste tutorial, o plugin de Tecido de Serviço Jenkins é usado em combinação com um repositório GitHub que hospeda a aplicação de Votação para implementar a aplicação num cluster.

Na quinta parte da série, ficará a saber como:
> [!div class="checklist"]
> * Implementar um contentor do Jenkins do Service Fabric no seu computador
> * Configurar o ambiente Jenkins para implementação no Service Fabric
> * Atualizar a sua aplicação

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * [Criar uma aplicação Java Service Fabric Reliable Services](service-fabric-tutorial-create-java-app.md)
> * [Implementar e depurar a aplicação num cluster local](service-fabric-tutorial-debug-log-local-cluster.md)
> * [Implementar a aplicação num cluster do Azure](service-fabric-tutorial-java-deploy-azure.md)
> * [Configurar a monitorização e os diagnósticos da aplicação](service-fabric-tutorial-java-elk.md)
> * Configurar CI/CD

## <a name="prerequisites"></a>Pré-requisitos

* Instalar o Git no computador local a partir da [página de transferências do Git](https://git-scm.com/downloads). Para obter mais informações sobre o Git, leia a [documentação do Git](https://git-scm.com/docs).
* Ter um conhecimento prático do [Jenkins](https://jenkins.io/).
* Criar uma conta do [GitHub](https://github.com/) e saber como utilizá-lo.
* Instalar o [Docker](https://www.docker.com/community-edition) no computador.

## <a name="pull-and-deploy-service-fabric-jenkins-container-image"></a>Extrair e implementar a imagem do contentor do Jenkins do Service Fabric

Pode configurar o Jenkins dentro ou fora de um cluster do Service Fabric. As instruções seguintes mostram como configurá-lo fora de um cluster com uma imagem fornecida do Docker. No entanto, também pode ser utilizado um ambiente de compilação Jenkins pré-configurado. A imagem do contentor seguinte é instalada com o plug-in do Service Fabric e está pronta para ser utilizada imediatamente com o Service Fabric.

1. Extraia a imagem do contentor do Jenkins do Service Fabric: ``docker pull rapatchi/jenkins:v10``. Esta imagem inclui o plug-in do Jenkins do Service Fabric pré-instalado.

1. Execute a imagem do contentor com a localização onde os certificados do Azure estão armazenados no computador local montado.

    ```bash
    docker run -itd -p 8080:8080 -v /service-fabric-java-quickstart/AzureCluster rapatchi/jenkins:v10
    ```

1. Obtenha o ID da instância de imagem do contentor. Pode listar todos os contentores de Docker com o comando ``docker ps –a``

1. Obtenha a palavra-passe da instância do Jenkins ao executar o seguinte comando:

    ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```

    Se o ID do contentor for 2d24a73b5964, utilize 2d24.
    * Esta palavra-passe é necessária para iniciar sessão no dashboard do Jenkins a partir do portal, que é ``http://<HOST-IP>:8080``
    * Depois de iniciar sessão pela primeira vez, pode criar a sua própria conta de utilizador ou utilizar a conta de administrador.

1. Utilize os passos mencionados em [Gerar uma chave SSH nova e adicioná-la ao agente SSH](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) para configurar o GitHub para funcionar com o Jenkins. Uma vez que os comandos são executados a partir do contentor do Docker, siga as instruções para o ambiente do Linux.
   * Utilize as instruções fornecidas pelo GitHub para gerar a chave SSH. Em seguida, adicione a chave SSH à conta do GitHub que aloja o repositório.
   * Execute os comandos mencionados na ligação anterior na shell Docker do Jenkins (e não no seu anfitrião).
   * Para iniciar sessão na shell do Jenkins a partir do seu anfitrião, utilize os comandos seguintes:

     ```sh
     docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
     ```

     Verifique se o cluster ou a máquina em que a imagem de contentor do Jenkins está alojada tem um IP destinado ao público. Ter um IP destinado ao público permite que a instância do Jenkins receba notificações do GitHub.

## <a name="create-and-configure-a-jenkins-job"></a>Criar e configurar uma tarefa do Jenkins

1. Em primeiro lugar, se não tiver um repositório que possa utilizar para acolher o projeto de votação no GitHub, crie um. O repositório é denominado **dev_test** no restante tutorial.

1. Crie um **novo item** no dashboard do Jenkins em ``http://<HOST-IP>:8080``.

1. Introduza um nome para o item (por exemplo, **MyJob**). Selecione **free-style project** (projeto de estilo livre) e clique em **OK**.

1. Vá para a página da tarefa e clique em **Configure** (Configurar).

   a. Na secção geral, selecione a caixa de verificação de **Projeto do GitHub** e especifique o URL do seu projeto do GitHub. Este URL aloja a aplicação Java do Service Fabric que pretende integrar no fluxo de integração contínua do Jenkins e implementação contínua (CI/CD) (por exemplo, ``https://github.com/testaccount/dev_test``).

   b. Na secção **Gestão de Código Fonte**, selecione **Git**. Especifique o URL repositório que acolhe a aplicação Service Fabric Java que pretende integrar com o fluxo Jenkins CI/CD (por exemplo, *`https://github.com/testaccount/dev_test.git`* ). Também pode especificar aqui o ramo a compilar, (por exemplo, **/master**).

1. Configure o seu *GitHub* (o que está a alojar o repositório) para que seja capaz de comunicar com o Jenkins. Utilize os passos seguintes:

   a. Aceda à sua página de repositório do GitHub. Ir para **Configurações**  >  **Integrações e Serviços**.

   b. Selecione **Add Service** (Adicionar Serviço), escreva **Jenkins** e selecione **Jenkins-GitHub plugin** (Plug-in Jenkins-GitHub).

   c. Introduza o URL do webhook do Jenkins (por predefinição, deve ser ``http://<PublicIPorFQDN>:8081/github-webhook/``). Clique em **add/update service** (adicionar/atualizar serviço).

   d. É enviado um evento de teste para a instância do Jenkins. Deverá ver uma verificação a verde através do webhook do GitHub e o seu projeto é compilado.

   ![Configuração do Jenkins do Service Fabric](./media/service-fabric-tutorial-java-jenkins/jenkinsconfiguration.png)

1. Na secção **Build Triggers** (Criar Acionadores), selecione a opção de compilação que pretende. Neste exemplo, pretende-se acionar uma compilação sempre que se dá um envio para o repositório. Para isso, selecionamos **GitHub hook trigger for GITScm polling** (Acionador de hook do GitHub para consulta GITScm).

1. Na **secção Build** (Compilar), na lista pendente **Add build step** (Adicionar passo de compilação), selecione a opção **Invoke Gradle Script** (Invocar Gradle Script). No widget fornecido, abra o menu avançado e especifique o caminho para o **Script de compilação de raiz** da sua aplicação. Este obtém o build.gradle a partir do caminho especificado e funciona em conformidade.

    ![Ação de Compilação do Jenkins do Service Fabric](./media/service-fabric-tutorial-java-jenkins/jenkinsbuildscreenshot.png)

1. No menu pendente **Post-Build Actions** (Ações de Pós-compilação), selecione **Deploy Service Fabric Project** (Implementar Projeto do Service Fabric). Aqui, tem de indicar os detalhes do cluster no qual a aplicação do Service Fabric compilada com o Jenkins seria implementada. O caminho para o certificado é onde o volume foi montado (/tmp/myCerts).

    Também tem de indicar os detalhes adicionais utilizados para implementar a aplicação. Veja a captura de ecrã seguinte para obter um exemplo dos detalhes da aplicação:

    ![Diálogo de ações pós-construção com opções adicionais de implementação de projetos de tecido de serviço](./media/service-fabric-tutorial-java-jenkins/sfjenkins.png)

    > [!NOTE]
    > Este cluster seria igual ao que aloja a aplicação de contentor do Jenkins, no caso de estar a utilizar o Service Fabric para implementar a imagem de contentor do Jenkins.
    >

1. Clique em **Guardar**.

## <a name="update-your-existing-application"></a>Atualizar a aplicação existente

1. Atualize o título do HTML no ficheiro *VotingApplication/VotingWebPkg/Code/wwwroot/index.html* com **Service Fabric Voting Sample V2**.

    ```html
    <div ng-app="VotingApp" ng-controller="VotingAppController" ng-init="refresh()">
        <div class="container-fluid">
            <div class="row">
                <div class="col-xs-8 col-xs-offset-2 text-center">
                    <h2>Service Fabric Voting Sample V2</h2>
                </div>
            </div>
        </div>
    </div>
    ```

1. Atualize as versões **ApplicationTypeVersion** e **ServiceManifestVersion** para **2.0.0** no ficheiro *Voting/VotingApplication/ApplicationManifest.xml*.

    ```xml
    <?xml version="1.0" encoding="utf-8" standalone="no"?>
    <ApplicationManifest xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingApplicationType" ApplicationTypeVersion="2.0.0">
      <Description>Voting Application</Description>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="2.0.0"/>
      </ServiceManifestImport>
      <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="VotingDataServicePkg" ServiceManifestVersion="1.0.0"/>
        </ServiceManifestImport>
        <DefaultServices>
          <Service Name="VotingWeb">
             <StatelessService InstanceCount="1" ServiceTypeName="VotingWebType">
                <SingletonPartition/>
             </StatelessService>
          </Service>
       <Service Name="VotingDataService">
                <StatefulService MinReplicaSetSize="3" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="3">
                    <UniformInt64Partition HighKey="9223372036854775807" LowKey="-9223372036854775808" PartitionCount="1"/>
                </StatefulService>
            </Service>
        </DefaultServices>
    </ApplicationManifest>
    ```

1. Atualize o campo **Version** nos campos **ServiceManifest** e **Version** na etiqueta **CodePackage** no ficheiro *Voting/VotingApplication/VotingWebPkg/ServiceManifest.xml* para **2.0.0**.

    ```xml
    <CodePackage Name="Code" Version="2.0.0">
    <EntryPoint>
        <ExeHost>
        <Program>entryPoint.sh</Program>
        </ExeHost>
    </EntryPoint>
    </CodePackage>
    ```

1. Para rubricar um trabalho da Jenkins que executa uma atualização de aplicações, empurre as suas novas alterações para o seu repositório GitHub.

1. No Service Fabric Explorer, clique no menu pendente **Aplicações**. Para ver o estado da atualização, clique no separador **Atualizações em Curso**.

    ![Atualização em curso](./media/service-fabric-tutorial-create-java-app/upgradejava.png)

1. Se tiver acesso **http:// \<Host-IP> :8080,**  a aplicação de voto com funcionalidade completa está agora em funcionamento.

    ![Aplicação de Voto Local](./media/service-fabric-tutorial-java-jenkins/votingv2.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Implementar um contentor do Jenkins do Service Fabric no seu computador
> * Configurar o ambiente Jenkins para implementação no Service Fabric
> * Atualizar a sua aplicação

* Veja outros [Exemplos de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)
