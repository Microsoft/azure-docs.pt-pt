---
title: 'Tutorial: Criar uma aplicação Java no Azure Service Fabric'
description: Neste tutorial, vai aprender a criar uma aplicação Java de serviço fiável com um front-end, a criar um back-end com estado de serviços fiáveis e a implementar a aplicação num cluster.
ms.topic: tutorial
ms.date: 09/01/2018
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java, devx-track-js
ms.openlocfilehash: d1f66177b1939d612a5ee80c99109c397b56cb37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91532198"
---
# <a name="tutorial-create-an-application-with-a-java-api-front-end-service-and-a-stateful-back-end-service-on-azure-service-fabric"></a>Tutorial: Crie uma aplicação com um serviço frontal java API e um serviço de back-end imponente no Azure Service Fabric

Este tutorial é a primeira parte de uma série. Quando terminar, tem uma aplicação de voto com uma extremidade frontal da Web Java que guarda os resultados da votação num serviço de back-end imponente no Azure Service Fabric. Esta série de tutoriais requer que tenha um computador de programador Mac OSX ou Linux. Se não quiser criar manualmente o pedido de voto, pode [descarregar o código fonte para a aplicação concluída](https://github.com/Azure-Samples/service-fabric-java-quickstart) e avançar para a Caminhada através da [aplicação da amostra de voto.](service-fabric-tutorial-create-java-app.md#walk-through-the-voting-sample-application) Além disso, considere seguir o [Quickstart para os serviços fiáveis da Java.](service-fabric-quickstart-java-reliable-services.md).

![Exemplo de votação do Service Fabric](./media/service-fabric-tutorial-create-java-app/service-fabric-java-voting-app-sample.png)

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * Criar uma aplicação Java Service Fabric Reliable Services
> * [Implementar e depurar a aplicação num cluster local](service-fabric-tutorial-debug-log-local-cluster.md)
> * [Implementar a aplicação num cluster do Azure](service-fabric-tutorial-java-deploy-azure.md)
> * [Configurar a monitorização e os diagnósticos da aplicação](service-fabric-tutorial-java-elk.md)
> * [Configurar CI/CD](service-fabric-tutorial-java-jenkins.md)


Na primeira parte da série, saiba como:

> [!div class="checklist"]
> * Criar um serviço Java fiável e com estado
> * Criar um serviço de aplicações Web em Java sem estado
> * Utilizar a comunicação remota do serviço para comunicar com o serviço com estado
> * Implementar a aplicação num cluster do Service Fabric local

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Configure o ambiente de desenvolvimento para [Mac](service-fabric-get-started-mac.md) ou [Linux](service-fabric-get-started-linux.md). Siga as instruções para instalar o plug-in do Eclipse, Gradle, o SDK do Service Fabric e a CLI do Service Fabric (sfctl).

## <a name="create-the-front-end-java-stateless-service"></a>Criar o serviço front-end em Java sem estado

Em primeiro lugar, crie o front-end para a Web da aplicação de Voto. Uma UI web alimentada por AngularJS envia pedidos para o serviço java apátrida, que executa um servidor HTTP leve. Este serviço processa cada pedido e envia uma chamada de procedimento remoto para o serviço estatal para armazenar os votos. 

1. Abra o Eclipse.

2. Criar um projeto com **file**  >  **new**  >    >  **service fabric** fabric  >  **project.**

    ![Novo projeto de tecido de serviço em Eclipse](./media/service-fabric-tutorial-create-java-app/service-fabric-project-wizard.png)

3. No diálogo **ServiceFabric Project Wizard,** nomeie o Projeto **De Votação** e selecione **Seguinte**.

    ![Escolher o serviço sem estado em Java na caixa de diálogo do novo serviço](./media/service-fabric-tutorial-create-java-app/name-service-fabric-project-wizard.png) 

4. Na página **Add Service,** selecione **Stateless Service**, e nomeie o seu serviço **VotingWeb**. Selecione **Finish** para criar o projeto.

    ![Crie um serviço apátrida para o seu projeto Service Fabric]( ./media/service-fabric-tutorial-create-java-app/add-service-fabric-votingweb-service.png)

    O Eclipse cria uma aplicação e um projeto de serviço e apresenta-os no Package Explorer (Explorador de Pacotes).

    ![Package Explorer do Eclipse após a criação da aplicação]( ./media/service-fabric-tutorial-create-java-app/eclipse-package-explorer.png)

A tabela mostra uma descrição breve de cada item no Package Explorer da captura de ecrã anterior. 

| **Item do Explorador de Pacotes** | **Descrição** |
| --- | --- |
| PublishProfiles | Contém os ficheiros JSON que descrevem os detalhes do perfil de clusters locais e do Azure Service Fabric. O plug-in utiliza o conteúdo destes ficheiros durnte a implementação da aplicação. |
| Scripts | Contém scripts de programa auxiliar que podem ser utilizados na linha de comandos para gerir rapidamente a sua aplicação com um cluster. |
| VotingApplication | Contém a aplicação do Service Fabric que é enviada por push para o cluster do Service Fabric. |
| VotingWeb | Contém os ficheiros de origem do serviço sem estado do front-end, juntamente com o ficheiro de compilação do gradle relacionados. |
| build.gradle | Ficheiro do Gradle utilizado para gerir o projeto. |
| settings.gradle | Contém os nomes de projetos do Gradle nesta pasta. |

### <a name="add-html-and-javascript-to-the-votingweb-service"></a>Adicionar HTML e Javascript ao serviço VotingWeb

Para adicionar uma UI que pode ser prestada pelo serviço apátrida, adicione um ficheiro HTML. Este ficheiro HTML é, então, processado pelo servidor HTTP simples que está incorporado no serviço de Java sem estado.

1. Expanda o diretório *VotingApplication* para ver o diretório *VotingApplication/VotingWebPkg/Code*.

2. Clique com o botão direito no *diretório* de código e selecione **Nova**  >  **Pasta**.

3. Nomeie a pasta *wwwroot* e **selecione Acabamento**.

    ![Criar pasta wwwroot no Eclipse](./media/service-fabric-tutorial-create-java-app/create-wwwroot-folder.png)

4. Adicione um ficheiro ao **wwwroot** chamado **index.html** e cole os seguintes conteúdos neste ficheiro.

```html
<!DOCTYPE html>
<html>
<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/0.13.4/ui-bootstrap-tpls.min.js"></script>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
<body>

<script>
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.controller("VotingAppController", ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {
    $scope.votes = [];
    
    $scope.refresh = function () {
        $http.get('getStatelessList')
            .then(function successCallback(response) {
        $scope.votes = Object.assign(
            {},
            ...Object.keys(response.data) .
            map(key => ({[decodeURI(key)]: response.data[key]}))
        )
        },
        function errorCallback(response) {
            alert(response);
        });
    };

    $scope.remove = function (item) {
       $http.get("removeItem", {params: { item: encodeURI(item) }})
            .then(function successCallback(response) {
                $scope.refresh();
            },
            function errorCallback(response) {
                alert(response);
            });
    };

    $scope.add = function (item) {
        if (!item) {return;}
        $http.get("addItem", {params: { item: encodeURI(item) }})
            .then(function successCallback(response) {
                $scope.refresh();
            },
            function errorCallback(response) {
                alert(response);
            });
    };
}]);
</script>

<div ng-app="VotingApp" ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-offset-2">
                <form style="width:50% ! important;" class="center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item" />
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr />

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="(key, value)  in votes">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(key)">
                            <span class="pull-left">
                                {{key}}
                            </span>
                            <span class="badge pull-right">
                                {{value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

</body>
</html>
```

### <a name="update-the-votingwebjava-file"></a>Atualizar o ficheiro VotingWeb.java

No sub-projeto **VotingWeb**, abra o ficheiro *VotingWeb/src/statelessservice/VotingWeb.java*. O serviço **VotingWeb** é o gateway para o serviço sem estado e é responsável por configurar o serviço de escuta de comunicação para a API de front-end.

Substitua o método **de criaçãoServires de Verificação existentes** no ficheiro com o seguinte e guarde as suas alterações.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {

    EndpointResourceDescription endpoint = this.getServiceContext().getCodePackageActivationContext().getEndpoint(webEndpointName);
    int port = endpoint.getPort();

    List<ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
    listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationListener(context, port)));
    return listeners;
}
```

### <a name="add-the-httpcommunicationlistenerjava-file"></a>Adicionar o ficheiro HTTPCommunicationListener.java

O serviço de escuta de comunicação HTTP atua como um controlador que configura o servidor HTTP e expõe as APIs que definem as ações de voto. Clique com o botão direito no pacote *de serviços apátridas* na pasta *VotingWeb/src/apátrida* e, em seguida, selecione **Novo**  >  **Ficheiro**.  Nomeie o ficheiro *HttpCommunicationListener.java* e **selecione Acabamento**.

Substitua o conteúdo do ficheiro pelo seguinte e, em seguida, guarde as alterações.  Mais adiante, em Atualizar o ficheiro HttpCommunicationListener.java, este ficheiro é modificado para compor, ler e escrever dados de voto do serviço back-end.  Por agora, o serviço de escuta devolve simplesmente o HTML estático da aplicação de Voto.

```java
// ------------------------------------------------------------
//  Copyright (c) Microsoft Corporation.  All rights reserved.
//  Licensed under the MIT License (MIT). See License.txt in the repo root for license information.
// ------------------------------------------------------------

package statelessservice;

import com.google.gson.Gson;
import com.sun.net.httpserver.HttpExchange;
import com.sun.net.httpserver.HttpHandler;
import com.sun.net.httpserver.Headers;

import java.io.File;
import java.io.OutputStream;
import java.io.FileInputStream;

import java.net.InetSocketAddress;
import java.net.URI;

import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.CompletableFuture;
import java.util.logging.Level;
import java.util.logging.Logger;

import microsoft.servicefabric.services.communication.runtime.CommunicationListener;
import microsoft.servicefabric.services.runtime.StatelessServiceContext;
import microsoft.servicefabric.services.client.ServicePartitionKey;
import microsoft.servicefabric.services.remoting.client.ServiceProxyBase;
import microsoft.servicefabric.services.communication.client.TargetReplicaSelector;
import system.fabric.CancellationToken;

public class HttpCommunicationListener implements CommunicationListener {

    private static final Logger logger = Logger.getLogger(HttpCommunicationListener.class.getName());

    private static final String HEADER_CONTENT_TYPE = "Content-Type";
    private static final int STATUS_OK = 200;
    private static final int STATUS_NOT_FOUND = 404;
    private static final int STATUS_ERROR = 500;
    private static final String RESPONSE_NOT_FOUND = "404 (Not Found) \n";
    private static final String MIME = "text/html";
    private static final String ENCODING = "UTF-8";

    private static final String ROOT = "wwwroot/";
    private static final String FILE_NAME = "index.html";
    private StatelessServiceContext context;
    private com.sun.net.httpserver.HttpServer server;
    private ServicePartitionKey partitionKey;
    private final int port;

    public HttpCommunicationListener(StatelessServiceContext context, int port) {
        this.partitionKey = new ServicePartitionKey(0);
        this.context = context;
        this.port = port;
    }

    // Called by openAsync when the class is instantiated
    public void start() {
        try {
            logger.log(Level.INFO, "Starting Server");
            server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(this.port), 0);
        } catch (Exception ex) {
            logger.log(Level.SEVERE, null, ex);
            throw new RuntimeException(ex);
        }

        // Responsible for rendering the HTML layout described in the previous step
        server.createContext("/", new HttpHandler() {
            @Override
            public void handle(HttpExchange t) {
                try {
                    File file = new File(ROOT + FILE_NAME).getCanonicalFile();

                    if (!file.isFile()) {
                      // Object does not exist or is not a file: reject with 404 error.
                      t.sendResponseHeaders(STATUS_NOT_FOUND, RESPONSE_NOT_FOUND.length());
                      OutputStream os = t.getResponseBody();
                      os.write(RESPONSE_NOT_FOUND.getBytes());
                      os.close();
                    } else {
                      Headers h = t.getResponseHeaders();
                      h.set(HEADER_CONTENT_TYPE, MIME);
                      t.sendResponseHeaders(STATUS_OK, 0);
    
                      OutputStream os = t.getResponseBody();
                      FileInputStream fs = new FileInputStream(file);
                      final byte[] buffer = new byte[0x10000];
                      int count = 0;
                      while ((count = fs.read(buffer)) >= 0) {
                        os.write(buffer,0,count);
                      }

                      fs.close();
                      os.close();
                    }
                } catch (Exception e) {
                    logger.log(Level.WARNING, null, e);
                }
            }
        });

        /*
        [Replace this entire comment block in the 'Connect the services' section]
        */

        server.setExecutor(null);
        server.start();
    }

    //Helper method to parse raw HTTP requests
    private Map<String, String> queryToMap(String query){
        Map<String, String> result = new HashMap<String, String>();
        for (String param : query.split("&")) {
            String pair[] = param.split("=");
            if (pair.length>1) {
                result.put(pair[0], pair[1]);
            }else{
                result.put(pair[0], "");
            }
        }
        return result;
    }

    //Called closeAsync when the service is shut down
    private void stop() {
        if (null != server)
            server.stop(0);
    }

    //Called by the Service Fabric runtime when this service is created on a node
    @Override
    public CompletableFuture<String> openAsync(CancellationToken cancellationToken) {
        this.start();
                    logger.log(Level.INFO, "Opened Server");
        String publishUri = String.format("http://%s:%d/", this.context.getNodeContext().getIpAddressOrFQDN(), port);
        return CompletableFuture.completedFuture(publishUri);
    }

    //Called by the Service Fabric runtime when the service is shut down
    @Override
    public CompletableFuture<?> closeAsync(CancellationToken cancellationToken) {
        this.stop();
        return CompletableFuture.completedFuture(true);
    }

    //Called by the Service Fabric runtime to forcibly shut this listener down
    @Override
    public void abort() {
        this.stop();
    }
}
```

### <a name="configure-the-listening-port"></a>Configurar a porta de escuta

Quando o serviço front-end VotingWeb é criado, o Service Fabric seleciona uma porta na qual o serviço escuta.  O serviço VotingWeb atua como front-end desta aplicação e aceita tráfego externo, pelo que vamos vincular este serviço a uma porta fixa e conhecida. No Package Explorer, abra *VotingApplication/VotingWebPkg/ServiceManifest.xml*.  Encontre o recurso **Endpoint** na secção **Recursos** e altere o valor do **Porto** para 8080 (continuaremos a utilizar esta porta durante todo o tutorial). Para implementar e executar a aplicação localmente, a porta de escuta da aplicação tem de estar aberta e disponível no seu computador. Cole o seguinte fragmento de código no elemento **ServiceManifest** (imediatamente abaixo do elemento ```<DataPackage>```).

```xml
<Resources>
    <Endpoints>
        <!-- This endpoint is used by the communication listener to obtain the port on which to
            listen. Please note that if your service is partitioned, this port is shared with
            replicas of different partitions that are placed in your code. -->
        <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
    </Endpoints>
  </Resources>
```

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Adicionar um serviço de back-end com monitorização de estado à sua aplicação

Agora que a estrutura do serviço de API Web em Java está concluída, vamos avançar e concluir o serviço back-end com estado.

O Service Fabric permite-lhe armazenar de forma consistente e fiável os seus dados diretamente dentro do seu serviço através das Reliable Collections. As coleções fiáveis são um conjunto de classes de coleção de elevada disponibilidade e fiáveis. A utilização destas classes é familiar para qualquer pessoa que tenha utilizado coleções de Java.

1. No Package Explorer, clique à direita **Votando** dentro do projeto de aplicação e selecione **Service Fabric**  >  **Add Service Fabric Service**

2. No diálogo **'Add Service',** selecione **Stateful Service** e nomeie o Serviço **de Voto** de Voto e selecione **Add Service**.

    Uma vez criado o projeto de serviço, terá dois serviços na sua aplicação. À medida que continua a criar a sua aplicação, pode adicionar mais serviços com o mesmo método. Pode dar uma versão e atualizar cada serviço de forma independente.

3. O Eclipse cria um projeto de serviço e apresenta-o no Package Explorer.

    ![Explorador de Projeto Eclipse](./media/service-fabric-tutorial-create-java-app/service-fabric-package-explorer-java.png)

### <a name="add-the-votingdataservicejava-file"></a>Adicione o ficheiro VotingDataService.java

O ficheiro *VotingDataService.java* inclui os métodos que contêm a lógica para obter, adicionar e remover votos de coleções fiáveis. Adicione os seguintes métodos de classe **VotingDataService** ao ficheiro *VotingDataService/src/statefulservice/VotingDataService.java*.

```java
package statefulservice;

import java.util.HashMap;
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.atomic.AtomicInteger;

import microsoft.servicefabric.services.communication.runtime.ServiceReplicaListener;

import microsoft.servicefabric.services.runtime.StatefulService;

import microsoft.servicefabric.services.remoting.fabrictransport.runtime.FabricTransportServiceRemotingListener;

import microsoft.servicefabric.data.ReliableStateManager;
import microsoft.servicefabric.data.Transaction;
import microsoft.servicefabric.data.collections.ReliableHashMap;
import microsoft.servicefabric.data.utilities.AsyncEnumeration;
import microsoft.servicefabric.data.utilities.KeyValuePair;

import system.fabric.StatefulServiceContext;

import rpcmethods.VotingRPC;

class VotingDataService extends StatefulService implements VotingRPC {
    private static final String MAP_NAME = "votesMap";
    private ReliableStateManager stateManager;

    protected VotingDataService (StatefulServiceContext statefulServiceContext) {
        super (statefulServiceContext);
    }

    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        this.stateManager = this.getReliableStateManager();
        ArrayList<ServiceReplicaListener> listeners = new ArrayList<>();

        listeners.add(new ServiceReplicaListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));

        return listeners;
    }

    // Method that will be invoked via RPC from the front end to retrieve the complete set of votes in the map
    public CompletableFuture<HashMap<String,String>> getList() {
        HashMap<String, String> tempMap = new HashMap<String, String>();

        try {

            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();

            Transaction tx = stateManager.createTransaction();
            AsyncEnumeration<KeyValuePair<String, String>> kv = votesMap.keyValuesAsync(tx).get();
            while (kv.hasMoreElementsAsync().get()) {
                KeyValuePair<String, String> k = kv.nextElementAsync().get();
                tempMap.put(k.getKey(), k.getValue());
            }

            tx.close();


        } catch (Exception e) {
            e.printStackTrace();
        }

        return CompletableFuture.completedFuture(tempMap);
    }

    // Method that will be invoked via RPC from the front end to add an item to the votes list or to increase the
    // vote count for a particular item
    public CompletableFuture<Integer> addItem(String itemToAdd) {
        AtomicInteger status = new AtomicInteger(-1);

        try {

            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();

            Transaction tx = stateManager.createTransaction();
            votesMap.computeAsync(tx, itemToAdd, (k, v) -> {
                if (v == null) {
                    return "1";
                }
                else {
                    int numVotes = Integer.parseInt(v);
                    numVotes = numVotes + 1;
                    return Integer.toString(numVotes);
                }
            }).get();

            tx.commitAsync().get();
            tx.close();

            status.set(1);
        } catch (Exception e) {
            e.printStackTrace();
        }

        return CompletableFuture.completedFuture(new Integer(status.get()));
    }

    // Method that will be invoked via RPC from the front end to remove an item
    public CompletableFuture<Integer> removeItem(String itemToRemove) {
        AtomicInteger status = new AtomicInteger(-1);
        try {
            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();

            Transaction tx = stateManager.createTransaction();
            votesMap.removeAsync(tx, itemToRemove).get();
            tx.commitAsync().get();
            tx.close();

            status.set(1);
        } catch (Exception e) {
            e.printStackTrace();
        }

        return CompletableFuture.completedFuture(new Integer(status.get()));
    }

}
```

A estrutura do serviço front-end sem estado e do serviço back-end está agora criada.

## <a name="create-the-communication-interface-to-your-application"></a>Criar a interface de comunicação para a aplicação

 O próximo passo é ligar o serviço apátrida frontal e o serviço de backend. Ambos os serviços utilizam uma interface chamada VoteRPC que define as operações da aplicação De Voto. Esta interface é implementada por ambos os serviços front-end e back-end para ativar as chamadas de procedimento remoto (RPC) entre os dois serviços. Infelizmente, o Eclipse não suporta a adição de subprojetos Gradle, pelo que o pacote que contém esta interface tem de ser adicionado manualmente.

1. Clique com o botão direito no projeto **de votação** no Explorador de Pacotes e selecione **Nova**  >  **Pasta**. Dê à pasta o nome **VotingRPC/src/rpcmethods**.

    ![Criar pacote VotingRPC no Eclipse Package Explorer](./media/service-fabric-tutorial-create-java-app/create-voting-rpc-package-java.png)

3. Crie um ficheiro em *Voto/VotingRPC/src/rpcmethods* com o nome *VotingRPC.java* e cole o seguinte no interior do ficheiro **VotingRPC.java**. 

    ```java
    package rpcmethods;
    
    import java.util.ArrayList;
    import java.util.concurrent.CompletableFuture;
    import java.util.List;
    import java.util.HashMap;
    
    import microsoft.servicefabric.services.remoting.Service;
    
    public interface VotingRPC extends Service {
        CompletableFuture<HashMap<String, String>> getList();
    
        CompletableFuture<Integer> addItem(String itemToAdd);
    
        CompletableFuture<Integer> removeItem(String itemToRemove);
    }
    ```

4. Crie um ficheiro vazio chamado *build.gradle* no diretório *Voting/VotingRPC* e cole o seguinte no seu interior. Este ficheiro gradle é utilizado para criar o ficheiro jar que é importado pelos outros serviços. 

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/out"
        delete "${projectDir}/VotingRPC.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0')
    }
    
    jar {
        from configurations.compile.collect {
            (it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}

        manifest {
                attributes(
                'Main-Class': 'rpcmethods.VotingRPC')
            baseName "VotingRPC"
            destinationDir = file('./')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA'
    }

    defaultTasks 'clean', 'jar'
    ```

5. No ficheiro *Voto/settings.gradle*, adicione uma linha para incluir o projeto recentemente criado na compilação. 

    ```gradle
    include ':VotingRPC'
    ```

6. No ficheiro *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java*, substitua o bloco de comentário pelo seguinte.  

    ```java
    server.createContext("/getStatelessList", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                t.sendResponseHeaders(STATUS_OK,0);
                OutputStream os = t.getResponseBody();
    
                HashMap<String,String> list = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").getList().get();
                String json = new Gson().toJson(list);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
        }
    });
    
    server.createContext("/removeItem", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                OutputStream os = t.getResponseBody();
                URI r = t.getRequestURI();
    
                Map<String, String> params = queryToMap(r.getQuery());
                String itemToRemove = params.get("item");

                Integer num = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").removeItem(itemToRemove).get();
    
                if (num != 1)
                {
                    t.sendResponseHeaders(STATUS_ERROR, 0);
                } else {
                    t.sendResponseHeaders(STATUS_OK,0);
                }
    
                String json = new Gson().toJson(num);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
    
        }
    });
    
    server.createContext("/addItem", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                URI r = t.getRequestURI();
                Map<String, String> params = queryToMap(r.getQuery());
                String itemToAdd = params.get("item");

                OutputStream os = t.getResponseBody();
                Integer num = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").addItem(itemToAdd).get();
                if (num != 1)
                {
                    t.sendResponseHeaders(STATUS_ERROR, 0);
                } else {
                    t.sendResponseHeaders(STATUS_OK,0);
                }
    
                String json = new Gson().toJson(num);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
        }
    });
    ```
7. Adicione a declaração de importação adequada à parte superior do ficheiro *Voto/VotingWeb/src/statelessservice/HttpCommunicationListener.java*. 

    ```java
    import rpcmethods.VotingRPC; 
    ```

Nesta fase, as funções das interfaces de front-end, back-end e RPC estão concluídas. A fase seguinte é configurar os scripts de Gradle corretamente antes de implementar um cluster do Service Fabric. 

## <a name="walk-through-the-voting-sample-application"></a>Percorrer a aplicação de votação de exemplo
A aplicação de votação é composta por dois serviços:
- Serviço front-end para a Web (VotingWeb) - um serviço front-end para a Web em Java, que serve a página Web e expõe as APIs para comunicar com o serviço back-end.
- Serviço back-end (VotingDataService) - um serviço Web em Java, que define os métodos que são invocados através de Chamadas de Procedimento Remoto (RPC) para manter os votos.

![Diagrama da amostra de voto](./media/service-fabric-tutorial-create-java-app/walkthrough-java-voting.png)

Quando efetua uma ação na aplicação (adicionar item, votar, remover item) ocorrem os seguintes eventos:
1. Um JavaScript envia o pedido adequado para a API Web no serviço front-end para a Web como um pedido HTTP.

2. O serviço front-end para a Web utiliza a funcionalidade incorporada de Comunicação Remota do Serviço do Service Fabric para localizar e reencaminhar o pedido para o serviço back-end. 

3. O serviço back-end define os métodos que atualizam o resultado num dicionário fiável. O conteúdo deste dicionário fiável é replicado para vários nós dentro do cluster e é mantido no disco. Todos os dados da aplicação são armazenados no cluster. 

## <a name="configure-gradle-scripts"></a>Configurar scripts de Gradle 

Nesta secção, vai configurar os scripts de Gradle para o projeto. 

1. Substitua os conteúdos do ficheiro *Voto/build.gradle* pelo seguinte.

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    subprojects {
        apply plugin: 'java'
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

2. Substitua os conteúdos do ficheiro *Voting/VotingWeb/build.gradle* pelo seguinte.

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/../lib"
        delete "${projectDir}/out"
        delete "${projectDir}/../VotingApplication/VotingWebPkg/Code/lib"
        delete "${projectDir}/../VotingApplication/VotingWebPkg/Code/VotingWeb.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
        compile project(':VotingRPC')
    }
    
    task explodeDeps(type: Copy, dependsOn:configurations.compile) { task ->
        configurations.compile.filter {!it.toString().contains("native")}.each{
            from it
        }
    
        configurations.compile.filter {it.toString().contains("native")}.each{
            from zipTree(it)
        }
        into "../lib/"
        include "lib*.so", "*.jar"
    }
    
    task copyDeps<< {
        copy {
            from("../lib/")
            into("../VotingApplication/VotingWebPkg/Code/lib")
            include('lib*.so')
        }
    }
    
    compileJava.dependsOn(explodeDeps)
    
    jar {
        from configurations.compile.collect {(it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
    
        manifest {
            attributes(
                'Main-Class': 'statelessservice.VotingWebServiceHost')
            baseName "VotingWeb"
            destinationDir = file('../VotingApplication/VotingWebPkg/Code/')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA'
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

3. Substitua os conteúdos do ficheiro *Voting/VotingDataService/build.gradle*. 

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/../lib"
        delete "${projectDir}/out"
        delete "${projectDir}/../VotingApplication/VotingDataServicePkg/Code/lib"
        delete "${projectDir}/../VotingApplication/VotingDataServicePkg/Code/VotingDataService.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
        compile project(':VotingRPC')
    }
    
    task explodeDeps(type: Copy, dependsOn:configurations.compile) { task ->
        configurations.compile.filter {!it.toString().contains("native")}.each{
            from it
        }
    
        configurations.compile.filter {it.toString().contains("native")}.each{
            from zipTree(it)
        }
        into "../lib/"
        include "lib*.so", "*.jar"
    }
    
    compileJava.dependsOn(explodeDeps)
    
    task copyDeps<< {
        copy {
            from("../lib/")
            into("../VotingApplication/VotingDataServicePkg/Code/lib")
            include('lib*.so')
        }
    }
    
    jar {
        from configurations.compile.collect {
            (it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
    
        manifest {
            attributes('Main-Class': 'statefulservice.VotingDataServiceHost')
    
            baseName "VotingDataService"
            destinationDir = file('../VotingApplication/VotingDataServicePkg/Code/')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA'
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

## <a name="deploy-application-to-local-cluster"></a>Implementar aplicação no cluster local

Neste momento, a aplicação está pronta para ser implementada num cluster do Service Fabric local.

1. Clique com o botão direito no projeto **de votação** no Explorador de Pacotes e selecione a Aplicação de Construção **de Tecidos** de Serviço para construir a sua  >   aplicação.

2. Execute o seu cluster do Service Fabric local. Este passo depende do seu ambiente de desenvolvimento (Mac ou Linux).

    Se estiver a utilizar um Mac, execute o cluster local com o seguinte comando: substitua o comando transmitido no parâmetro **-v** pelo caminho para a sua área de trabalho.

    ```bash
    docker run -itd -p 19080:19080 -p 8080:8080 -p --name sfonebox mcr.microsoft.com/service-fabric/onebox:latest
    ```
    Consulte instruções mais detalhadas no [guia de configuração DOS X.](service-fabric-get-started-mac.md)

    Se estiver a executar num computador Linux, inicie o cluster local com o seguinte comando: 

    ```bash 
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Consulte instruções mais detalhadas no [guia de configuração do Linux.](service-fabric-get-started-linux.md)

4. No Package Explorer for Eclipse, clique à direita no projeto **de votação** e selecione a Aplicação de Publicação **de Tecidos de Serviço**  >   
5. Na janela **'Aplicação de publicação',** selecione **Local.js** a partir do dropdown e selecione **Publicar**.
6. Vá ao seu navegador web e aceda http: \/ /localhost:8080 para ver a sua aplicação de execução no cluster de Tecido de Serviço local. 

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar o serviço Java como um serviço fiável com estado
> * Criar o serviço Java como um serviço Web sem estado
> * Adicionar uma interface de Java para processar as Chamadas de Procedimento Remoto (RPC) entre os serviços
> * Configurar os scripts de Gradle
> * Criar e implementar a aplicação num cluster do Service Fabric local

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Depurar e registar aplicações no cluster local](service-fabric-tutorial-debug-log-local-cluster.md)
