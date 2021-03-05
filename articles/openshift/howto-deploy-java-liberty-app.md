---
title: Implemente uma aplicação Java com Open Liberty/WebSphere Liberty num cluster Azure Red Hat OpenShift 4
description: Implemente uma aplicação Java com Open Liberty/WebSphere Liberty num cluster Azure Red Hat OpenShift 4.
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/30/2020
keywords: java, jacarta, javaee, microprofile, open-liberty, websphere-liberty, aro, openshift, red hat
ms.openlocfilehash: 08fd3ab112498a983b438d5ba1f1f100816cbf5d
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212999"
---
# <a name="deploy-a-java-application-with-open-libertywebsphere-liberty-on-an-azure-red-hat-openshift-4-cluster"></a>Implemente uma aplicação Java com Open Liberty/WebSphere Liberty num cluster Azure Red Hat OpenShift 4

Este guia demonstra como executar a sua aplicação Java, Java EE, [Jakarta EE,](https://jakarta.ee/)ou [MicroProfile](https://microprofile.io/) no tempo de funcionamento Open Liberty/WebSphere Liberty e, em seguida, implementar a aplicação contentorizada para um cluster Azure Red Hat OpenShift (ARO) 4 utilizando o Operador da Liberdade Aberta. Este artigo irá acompanhá-lo através da preparação de uma aplicação Liberty, construindo a aplicação Estiva e executando a aplicação contentorizada num cluster ARO 4.  Para mais detalhes sobre a Open Liberty, consulte [a página do projeto Open Liberty.](https://openliberty.io/) Para mais detalhes sobre a IBM WebSphere Liberty consulte [a página de produto WebSphere Liberty.](https://www.ibm.com/cloud/websphere-liberty)

[!INCLUDE [aro-support](includes/aro-support.md)]

## <a name="prerequisites"></a>Pré-requisitos

Complete os seguintes pré-requisitos para caminhar com sucesso através deste guia.

> [!NOTE]
> O Azure Red Hat OpenShift requer um mínimo de 40 núcleos para criar e executar um cluster OpenShift. A quota de recursos Azure por defeito para uma nova subscrição do Azure não satisfaz este requisito. Para solicitar um aumento do seu limite de recursos, consulte [quota standard: Aumentar os limites por série VM](../azure-portal/supportability/per-vm-quota-requests.md). Note que a subscrição de teste gratuito não é elegível para um aumento de quota, [upgrade para uma subscrição Pay-As-You-Go](../cost-management-billing/manage/upgrade-azure-subscription.md) antes de solicitar um aumento de quota.

1. Prepare uma máquina local com sistema operativo unix instalado (por exemplo, Ubuntu, macOS).
1. Instale uma implementação Java SE (por exemplo, [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
1. Instale [Maven](https://maven.apache.org/download.cgi) 3.5.0 ou superior.
1. Instale [o Docker](https://docs.docker.com/get-docker/) para o seu SISTEMA.
1. Instale [o Azure CLI](/cli/azure/install-azure-cli) 2.0.75 ou posterior.
1. Verifique e instale [`envsubst`](https://command-not-found.com/envsubst) se não estiver pré-instalado no seu sistema operativo.
1. Clone o código para esta amostra no seu sistema local. A amostra está no [GitHub.](https://github.com/Azure-Samples/open-liberty-on-aro)
1. Siga as instruções no [Criar um conjunto Azure Red Hat OpenShift 4](./tutorial-create-cluster.md).

   Embora o passo "Get a Red Hat pull secret" seja rotulado como opcional, **é necessário para este artigo**.  O segredo de pull permite que o seu cluster Azure Red Hat OpenShift encontre o Operador da Liberdade Aberta.

   Se planeia executar aplicações intensivas de memória no cluster, especifique o tamanho adequado da máquina virtual para os nós do trabalhador utilizando o `--worker-vm-size` parâmetro. Por exemplo, `Standard_E4s_v3` é o tamanho mínimo da máquina virtual para instalar o Operador de Elasticsearch num cluster. Para obter mais informações, consulte:

   * [Azure CLI para criar um cluster](/cli/azure/aro#az-aro-create)
   * [Tamanhos de máquina virtual suportados para memória otimizada](./support-policies-v4.md#memory-optimized)
   * [Pré-requisitos para instalar o Operador de Elasticsearch](https://docs.openshift.com/container-platform/4.3/logging/cluster-logging-deploying.html#cluster-logging-deploy-eo-cli_cluster-logging-deploying)

1. Ligue-se ao cluster seguindo os passos em [Connect a um cluster Azure Red Hat OpenShift 4](./tutorial-connect-cluster.md).
   * Certifique-se de seguir os passos em "Instalar o CLI OpenShift" porque usaremos o `oc` comando mais tarde neste artigo.
   * Escreva o URL da consola de cluster que parece `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` .
   * Tome nota das `kubeadmin` credenciais.

1. Verifique se pode iniciar sôm no CLI OpenShift com o símbolo para o utilizador `kubeadmin` .

### <a name="enable-the-built-in-container-registry-for-openshift"></a>Ativar o registo de contentores incorporados para o OpenShift

Os passos deste tutorial criam uma imagem docker que deve ser empurrada para um registo de contentores acessível a OpenShift. A opção mais simples é utilizar o registo incorporado fornecido pela OpenShift. Para ativar o registo de contentores incorporados, siga os passos no [registo de contentores incorporados Configure para O Azure Red Hat OpenShift 4](built-in-container-registry.md). Neste artigo são utilizados três artigos desses degraus.

* O nome de utilizador e a palavra-passe do utilizador Azure AD para iniciar sessão na consola web OpenShift.
* A saída de depois de `oc whoami` seguir os passos para iniciar a sessão no CLI OpenShift.  Este valor é chamado **de utilizador aad** para discussão.
* A URL do registo do contentor.

Observe estes itens enquanto completa os passos para ativar o registo de contentores incorporados.

### <a name="create-an-openshift-namespace-for-the-java-app"></a>Crie um espaço de nomes OpenShift para a aplicação Java

1. Inscreva-se na consola web OpenShift a partir do seu navegador utilizando as `kubeadmin` credenciais.
2. Navegar para **espaços**  >  **de nomes de administração**  >  **Criar espaço de nome.**
3. Preencha `open-liberty-demo` **o nome** e selecione **Criar,** como mostrado em seguida.

   ![criar espaço de nome](./media/howto-deploy-java-liberty-app/create-namespace.png)

### <a name="create-an-administrator-for-the-demo-project"></a>Criar um administrador para o projeto de demonstração

Além da gestão de imagem, o **utilizador da AAD** também receberá permissões administrativas para a gestão de recursos no projeto de demonstração do cluster ARO 4.  Inscreva-se no CLI OpenShift e conceda ao **utilizador aad** os privilégios necessários seguindo estes passos.

1. Inscreva-se na consola web OpenShift a partir do seu navegador utilizando as `kubeadmin` credenciais.
1. No topo direito da consola web, expanda o menu de contexto do utilizador inscrito e, em seguida, selecione **Copy Login Command**.
1. Inscreva-se numa nova janela de separador com o mesmo utilizador, se necessário.
1. Selecione **Display Token**.
1. Copie o valor listado abaixo **Iniciar sessão com este token** para a área de transferência e execute-o numa concha, como mostrado aqui.
1. Execute os seguintes comandos para conceder `admin` o papel ao utilizador **aad** no espaço de `open-liberty-demo` nomes .

   ```bash
   # Switch to project "open-liberty-demo"
   oc project open-liberty-demo
   Now using project "open-liberty-demo" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   # Note: replace "<aad-user>" with the one noted by executing the steps in
   # Configure built-in container registry for Azure Red Hat OpenShift 4
   oc adm policy add-role-to-user admin <aad-user>
   clusterrole.rbac.authorization.k8s.io/admin added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

### <a name="install-the-open-liberty-openshift-operator"></a>Instale o Operador Open Liberty OpenShift

Depois de criar e ligar ao cluster, instale o Operador da Liberdade Aberta.  A página principal de partida para o Operador da Liberdade Aberta está no [GitHub.](https://github.com/OpenLiberty/open-liberty-operator)

1. Inscreva-se na consola web OpenShift a partir do seu navegador utilizando as `kubeadmin` credenciais.
2. Navegue para **o Operador**  >  **OperadorHub** e procure o **Operador de Liberdade Aberta.**
3. Selecione **o Operador da Liberdade Aberta** a partir dos resultados da pesquisa.
4. Selecione **Instalar**.
5. Na subscrição popup **Criar Operador,** verifique **todos os espaços de nomes no cluster (predefinido)** para modo de **instalação,** **beta** para o Canal **de Atualização** e Estratégia **Automática** de **Aprovação**:

   ![criar subscrição de operador para operador de liberdade aberta](./media/howto-deploy-java-liberty-app/install-operator.png)
6. **Selecione Subscrever** e aguarde um minuto ou dois até que o Operador da Liberdade Aberta seja apresentado.
7. Observe o Operador da Liberdade Aberta com o estatuto de "Bem sucedido".  Se não o fizer, diagnostice e resolva o problema antes de continuar.
   :::image type="content" source="media/howto-deploy-java-liberty-app/open-liberty-operator-installed.png" alt-text="Operadores instalados que mostrem a Open Liberty estão instalados.":::

## <a name="prepare-the-liberty-application"></a>Preparar a aplicação Liberty

Usaremos uma aplicação Java EE 8 como exemplo neste guia. Open Liberty é um servidor compatível [com perfil completo Java EE 8,](https://javaee.github.io/javaee-spec/javadocs/) para que possa executar facilmente a aplicação.  Open Liberty também é [compatível com perfil completo jakarta EE 8.](https://jakarta.ee/specifications/platform/8/apidocs/)

### <a name="run-the-application-on-open-liberty"></a>Executar a aplicação no Open Liberty

Para executar a aplicação no Open Liberty, é necessário criar um ficheiro de configuração do servidor Open Liberty para que o [plugin Liberty Maven](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin) possa embalar a aplicação para implementação. O plugin Liberty Maven não é necessário para implementar a aplicação no OpenShift.  No entanto, vamos usá-lo neste exemplo com o modo de desenvolvimento (dev) da Open Liberty.  O modo de desenvolvimento permite-lhe executar facilmente a aplicação localmente. Complete os seguintes passos no seu computador local.

1. Copiar `2-simple/src/main/liberty/config/server.xml` para `1-start/src/main/liberty/config` , sobre-escrever o ficheiro de comprimento zero existente. Isto `server.xml` configura o servidor Open Liberty com funcionalidades Java EE.
1. Copiar `2-simple/pom.xml` para `1-start/pom.xml` .  Este passo adiciona o `liberty-maven-plugin` POM.
1. Mude o diretório para `1-start` o seu clone local.
1. Corra `mvn clean package` numa consola para gerar um pacote de guerra no `javaee-cafe.war` diretório `./target` .
1. Corra `mvn liberty:dev` para iniciar a Open Liberty no modo dev.
1. Espere até o servidor começar. A saída da consola deve terminar com a seguinte mensagem:

   ```Text
   [INFO] CWWKM2015I: Match number: 1 is [6/10/20 10:26:09:517 CST] 00000022 com.ibm.ws.kernel.feature.internal.FeatureManager            A CWWKF0011I: The defaultServer server is ready to run a smarter planet. The defaultServer server started in 6.447 seconds..
   [INFO] Press the Enter key to run tests on demand. To stop the server and quit dev mode, use Ctrl-C or type 'q' and press the Enter key.
   [INFO] Source compilation was successful.
   ```

1. Abra `http://localhost:9080/` no seu navegador para visitar a página inicial da aplicação. A aplicação será semelhante à seguinte imagem:

   ![JavaEE Café Web UI](./media/howto-deploy-java-liberty-app/javaee-cafe-web-ui.png)
1. Pressione **o Control-C** para parar a aplicação e o servidor Open Liberty.

O diretório `2-simple` do seu clone local mostra o projeto Maven com as alterações acima já aplicadas.

## <a name="prepare-the-application-image"></a>Preparar a imagem da aplicação

Para implementar e executar a sua aplicação Liberty num cluster ARO 4, containerize a sua aplicação como uma imagem docker usando [imagens de contentores Open Liberty](https://github.com/OpenLiberty/ci.docker) ou [imagens de contentores WebSphere Liberty](https://github.com/WASdev/ci.docker).

### <a name="build-application-image"></a>Construir imagem de aplicação

Complete os seguintes passos para construir a imagem da aplicação:

1. Mude o diretório para `2-simple` o seu clone local.
2. Corra `mvn clean package` para embalar o pedido.
3. Executar um dos seguintes comandos para construir a imagem da aplicação.
   * Construa com imagem base Open Liberty:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary Open Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull .
     ```

   * Construa com imagem base WebSphere Liberty:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary WebSphere Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull --file=Dockerfile-wlp .
     ```

### <a name="run-the-application-locally-with-docker"></a>Executar a aplicação localmente com Docker

Antes de colocar a aplicação contentorizada num cluster remoto, corra com o seu Docker local para verificar se funciona:

1. Corre `docker run -it --rm -p 9080:9080 javaee-cafe-simple:1.0.0` na tua consola.
2. Aguarde o arranque do servidor Liberty e a aplicação para implementar com sucesso.
3. Abra `http://localhost:9080/` no seu navegador para visitar a página inicial da aplicação.
4. Pressione **o Control-C** para parar a aplicação e o servidor Liberty.

### <a name="push-the-image-to-the-container-image-registry"></a>Empurre a imagem para o registo de imagem do contentor

Quando estiver satisfeito com o estado da aplicação, empurre-o para o registo de imagem do contentor incorporado seguindo as instruções abaixo.

#### <a name="log-in-to-the-openshift-cli-as-the-azure-ad-user"></a>Inicie sessão no CLI OpenShift como utilizador AZure AD

1. Inscreva-se na consola web OpenShift a partir do seu navegador utilizando as credenciais de um utilizador AZure AD.

   1. Utilize uma função de janela de navegador InPrivate, Incógnita ou outra janela de navegador equivalente para iniciar sôm na consola.
   1. Selecione **openid**

   > [!NOTE]
   > Tome nota do nome de utilizador e palavra-passe que utiliza para iniciar seducaqui. Este nome de utilizador e palavra-passe funcionarão como administrador para outras ações neste e noutros artigos.
1. Inscreva-se com o CLI OpenShift utilizando os seguintes passos.  Para discussão, este processo é conhecido como `oc login` .
   1. No topo direito da consola web, expanda o menu de contexto do utilizador inscrito e, em seguida, selecione **Copy Login Command**.
   1. Inscreva-se numa nova janela de separador com o mesmo utilizador, se necessário.
   1. Selecione **Display Token**.
   1. Copie o valor listado abaixo **Iniciar sessão com este token** para a área de transferência e execute-o numa concha, como mostrado aqui.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

#### <a name="push-the-container-image-to-the-container-registry-for-openshift"></a>Empurre a imagem do recipiente para o registo do contentor para o OpenShift

Execute estes comandos para empurrar a imagem para o registo do contentor para OpenShift.

```bash
# Note: replace "<Container_Registry_URL>" with the fully qualified name of the registry
Container_Registry_URL=<Container_Registry_URL>

# Create a new tag with registry info that refers to source image
docker tag javaee-cafe-simple:1.0.0 ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0

# Sign in to the built-in container image registry
docker login -u $(oc whoami) -p $(oc whoami -t) ${Container_Registry_URL}
```

A saída bem sucedida será semelhante à seguinte.

```bash
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```

Empurre a imagem para o registo de imagem do recipiente incorporado com o seguinte comando.

```bash

docker push ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0
```

## <a name="deploy-application-on-the-aro-4-cluster"></a>Implementar aplicação no cluster ARO 4

Agora pode implementar a aplicação Liberty da amostra para o cluster Azure Red Hat OpenShift 4 que criou anteriormente ao trabalhar através dos pré-requisitos.

### <a name="deploy-the-application-from-the-web-console"></a>Implementar a aplicação a partir da consola web

Como utilizamos o Operador da Liberdade Aberta para gerir aplicações liberty, precisamos de criar uma instância da sua Definição de *Recursos Personalizados*, do tipo "OpenLibertyApplication". O Operador cuidará então de todos os aspetos da gestão dos recursos OpenShift necessários para a implantação.

1. Inscreva-se na consola web OpenShift a partir do seu navegador utilizando as credenciais do utilizador Azure AD.
1. Expandir **o Lar**, Selecione **Projetos**  >  **open-liberty-demo**.
1. Navegue para  >  **operadores instalados.**
1. No meio da página, selecione **Open Liberty Operator**.
1. No meio da página, selecione **Open Liberty Application**.  A navegação de itens na interface do utilizador espelha a hierarquia real de contenção das tecnologias em uso.
   <!-- Diagram source https://github.com/Azure-Samples/open-liberty-on-aro/blob/master/diagrams/aro-java-containment.vsdx -->
   ![Contenção ARO Java](./media/howto-deploy-java-liberty-app/aro-java-containment.png)
1. Selecione **Criar OpenLibertyApplication**
1. Substitua o yaml gerado pelo seu, que está localizado em `<path-to-repo>/2-simple/openlibertyapplication.yaml` .
1. Selecione **Criar**. Será devolvido à lista de Aberturas de Aplicações.
1. Selecione **javaee-café-simple**.
1. No meio da página, selecione **Recursos.**
1. Na tabela, selecione o link para **javaee-café-simple** com o **Tipo** de **Rota.**
1. Na página que abre, selecione o link abaixo **Localização**.

Verá a página inicial da aplicação aberta no navegador.

### <a name="delete-the-application-from-the-web-console"></a>Eliminar a aplicação da consola web

Quando terminar a aplicação, siga estes passos para eliminar a aplicação do Open Shift.

1. No painel de navegação à esquerda, expanda a entrada para **os Operadores.**
1. Selecione **Operadores instalados**.
1. Selecione **o Operador de Liberdade Aberta**.
1. No meio da página selecione **Open Liberty Application**.
1. Selecione a elipse vertical (três pontos verticais) e, em seguida, **selecione Eliminar Aplicação OpenLiberty**.

### <a name="deploy-the-application-from-cli"></a>Implementar a aplicação do CLI

Em vez de utilizar a consola web GUI, pode implementar a aplicação a partir do CLI. Se ainda não o fez, faça o download e instale a `oc` ferramenta da linha de comando seguindo a documentação do Chapéu Vermelho [Começando com o CLI](https://docs.openshift.com/container-platform/4.2/cli_reference/openshift_cli/getting-started-cli.html).

1. Inscreva-se na consola web OpenShift a partir do seu navegador utilizando as credenciais do utilizador Azure AD.
2. Inscreva-se no CLI OpenShift com o símbolo para o utilizador Azure AD.
3. Mude o diretório para `2-simple` o seu clone local e execute os seguintes comandos para implementar a sua aplicação Liberty para o cluster ARO 4.  A saída do comando também é mostrada em linha.

   ```bash
   # Switch to namespace "open-liberty-demo" where resources of demo app will belong to
   oc project open-liberty-demo

   Now using (or already on) project "open-liberty-demo" on server "https://api.aqlm62xm.rnfghf.aroapp.io:6443".

   # Create OpenLibertyApplication "javaee-cafe-simple"
   oc create -f openlibertyapplication.yaml

   openlibertyapplication.openliberty.io/javaee-cafe-simple created

   # Check if OpenLibertyApplication instance is created
   oc get openlibertyapplication javaee-cafe-simple

   NAME                 IMAGE                      EXPOSED   RECONCILED   AGE
   javaee-cafe-simple   javaee-cafe-simple:1.0.0   true      True         36s

   # Check if deployment created by Operator is ready
   oc get deployment javaee-cafe-simple

   NAME                 READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-cafe-simple   1/1     1            0           102s
   ```

4. Verifique para ver `1/1` debaixo da coluna antes de `READY` continuar.  Caso contrário, investigue e resolva o problema antes de continuar.
5. Descubra o anfitrião do percurso para a aplicação com o `oc get route` comando, como mostrado aqui.

   ```bash
   # Get host of the route
   HOST=$(oc get route javaee-cafe-simple --template='{{ .spec.host }}')
   echo "Route Host: $HOST"

   Route Host: javaee-cafe-simple-open-liberty-demo.apps.aqlm62xm.rnfghf.aroapp.io
   ```

   Assim que a aplicação Liberty estiver em funcionamento, abra a saída do **Route Host** no seu navegador para visitar a página inicial da aplicação.

### <a name="delete-the-application-from-cli"></a>Eliminar a aplicação do CLI

Elimine a aplicação do CLI executando este comando.

```bash
oc delete -f openlibertyapplication.yaml
```

## <a name="clean-up-resources"></a>Limpar os recursos

Elimine o cluster ARO seguindo os passos em [Tutorial: Elimine um aglomerado de chapéu aberto Azure RedShift 4](./tutorial-delete-cluster.md)

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu a:
> [!div class="checklist"]
>
> * Preparar a aplicação Liberty
> * Construa a imagem da aplicação
> * Executar a aplicação contentorizada num cluster ARO 4 utilizando o GUI e o CLI

Pode aprender mais com as referências utilizadas neste guia:

* [Liberdade Aberta](https://openliberty.io/)
* [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [Operador de Liberdade Aberta](https://github.com/OpenLiberty/open-liberty-operator)
* [Configuração do servidor de liberdade aberta](https://openliberty.io/docs/ref/config/)
* [Liberty Maven Plugin](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Imagens de recipientes de liberdade abertas](https://github.com/OpenLiberty/ci.docker)
* [Imagens do recipiente da liberdade da WebSphere](https://github.com/WASdev/ci.docker)