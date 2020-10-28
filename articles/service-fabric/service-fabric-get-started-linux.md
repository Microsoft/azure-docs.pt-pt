---
title: Crie o seu ambiente de desenvolvimento no Linux
description: Instale o runtime e o SDK e crie um cluster de desenvolvimento local no Linux. Depois de concluir esta configuração, estará pronto para criar aplicações.
ms.topic: conceptual
ms.date: 10/16/2020
ms.custom: devx-track-js
ms.openlocfilehash: 39ed4a394a54112c03145c0d481e5459ecbf6d8b
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675280"
---
# <a name="prepare-your-development-environment-on-linux"></a>Preparar o ambiente de desenvolvimento no Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [Mac OS X](service-fabric-get-started-mac.md)

Para implementar e executar [aplicações do Azure Service Fabric](service-fabric-application-model.md) no seu computador de desenvolvimento Linux, instale o runtime e o SDK comum. Também pode instalar SDKs opcionais para programação em Java e .NET Core. 

Os passos deste artigo pressupõem que instale de forma nativa no Linux ou utilize a imagem do [contentor OneBox do tecido de serviço,](https://hub.docker.com/_/microsoft-service-fabric-onebox)ou seja, `mcr.microsoft.com/service-fabric/onebox:u18` .

Pode gerir as entidades de Tecido de Serviço hospedadas na nuvem ou no local com a interface de linha de comando do Azure Service Fabric (CLI). Para obter mais informações sobre como instalar a CLI, veja [Configurar a CLI do Service Fabric](./service-fabric-cli.md).


## <a name="prerequisites"></a>Pré-requisitos

Estas versões do sistema operativo são suportadas para desenvolvimento.

* Ubuntu 16.04 ( `Xenial Xerus` 18.04 `Bionic Beaver` )

    Certifique-se de que o pacote `apt-transport-https` está instalado.
         
    ```bash
    sudo apt-get install apt-transport-https
    ```
* Red Hat Enterprise Linux 7.4 (suporte de pré-visualização do Service Fabric)


## <a name="installation-methods"></a>Métodos de instalação

<!-- markdownlint-disable MD025 -->
<!-- markdownlint-disable MD024 -->

# <a name="ubuntu"></a>[Ubuntu](#tab/sdksetupubuntu)

## <a name="update-your-apt-sources"></a>Atualizar as origens do APT
Para instalar o SDK e o pacote de runtime associado através da ferramenta de linha de comandos apt-get, primeiro tem de atualizar as origens do Advanced Packaging Tool (APT).

## <a name="script-installation"></a>Instalação com script

Por conveniência, é fornecido um script para instalar o tempo de execução do Tecido de Serviço e o SDK comum do Tecido de Serviço, juntamente com o [ **SFCTL** CLI](service-fabric-cli.md). A execução do script parte do princípio de que concorda com as licenças de todo o software que está a ser instalado. Em alternativa, poderá executar os passos [de instalação manual](#manual-installation) na secção seguinte, que apresentarão licenças associadas, bem como os componentes instalados.

Após a execução com êxito do script, pode avançar para [Configurar um cluster local](#set-up-a-local-cluster).

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

## <a name="manual-installation"></a>Instalação manual
Para instalar manualmente o runtime do Service Fabric e o SDK comum, siga o resto deste guia.

1. Abra um terminal.

2. Adicione o `dotnet` repo à sua lista de fontes correspondente à sua distribuição.

    ```bash
    wget -q https://packages.microsoft.com/config/ubuntu/$(lsb_release -rs)/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    ```

3. Adicione a nova chave MS Open Tech Gnu Privacy Guard (GnuPG ou GPG) ao seu porta-chaves APT.

    ```bash
    sudo curl -fsSL https://packages.microsoft.com/keys/msopentech.asc | sudo apt-key add -
    ```

4. Adicione a chave de GPG oficial do Docker ao porta-chaves do APT.

    ```bash
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

5. Configure o repositório do Docker.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

6. Adicione a chave Azul JDK ao seu porta-chaves APT e configua o seu repositório.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9
    sudo apt-add-repository "deb http://repos.azul.com/azure-only/zulu/apt stable main"
    ```

7. Atualize as listas de pacotes com base nos repositórios adicionados recentemente.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-a-local-cluster"></a>Instalar e configurar o SDK do Service Fabric para um cluster local

Depois de atualizar as origens, pode instalar o SDK. Instale o pacote do SDK do Service Fabric, confirme a instalação e aceite o contrato de licença.

### <a name="ubuntu"></a>Ubuntu

```bash
sudo apt-get install servicefabricsdkcommon
```

> [!TIP]
>   Os comandos seguintes automatizam a aceitação da licença para pacotes do Service Fabric:
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-ga select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-ga select true" | sudo debconf-set-selections
>   ```

# <a name="red-hat-enterprise-linux-74"></a>[Red Hat Enterprise Linux 7.4](#tab/sdksetuprhel74)

## <a name="update-your-yum-repositories"></a>Atualize os seus repositórios Yum
Para instalar o pacote de tempo de execução SDK e associado através da ferramenta linha de comando yum, tem primeiro de atualizar as fontes do seu pacote.

## <a name="manual-installation-rhel"></a>Instalação manual (RHEL)
Para instalar manualmente o runtime do Service Fabric e o SDK comum, siga o resto deste guia.

1. Abra um terminal.
2. Transfira e instale Pacotes Adicionais para Enterprise Linux(EPEL).

    ```bash
    wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    sudo yum install epel-release-latest-7.noarch.rpm
    ```
3. Adicione o repositório de pacotes de EfficiOS RHEL7 ao seu sistema.

    ```bash
    sudo wget -P /etc/yum.repos.d/ https://packages.efficios.com/repo.files/EfficiOS-RHEL7-x86-64.repo
    ```

4. Importe a chave de assinatura do pacote EfficiOS para o porta-chaves de GPG local.

    ```bash
    sudo rpmkeys --import https://packages.efficios.com/rhel/repo.key
    ```

5. Adicione o repositório RHEL Microsoft ao seu sistema.

    ```bash
    curl https://packages.microsoft.com/config/rhel/7.4/prod.repo > ./microsoft-prod.repo
    sudo cp ./microsoft-prod.repo /etc/yum.repos.d/
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-a-local-cluster-rhel"></a>Instale e instale o Service Fabric SDK para um cluster local (RHEL)

Depois de atualizar as origens, pode instalar o SDK. Instale o pacote do SDK do Service Fabric, confirme a instalação e aceite o contrato de licença.

```bash
sudo yum install servicefabricsdkcommon
```

---

## <a name="included-packages"></a>Pacotes incluídos
O runtime do Service Fabric, que vem incluído com a instalação do SDK, inclui os pacotes na tabela seguinte. 

 | | DotNetCore | Java | Python | NodeJS | 
--- | --- | --- | --- |---
**Ubuntu** | 2.0.7 | AzulJDK 1.8 | Implícito de npm | mais recente |
**RHEL** | - | OpenJDK 1.8 | Implícito de npm | mais recente |

## <a name="set-up-a-local-cluster"></a>Configurar um cluster local
1. Inicie um cluster local de tecido de serviço para o desenvolvimento.

# <a name="container-based-local-cluster"></a>[Aglomerado local à base de contentores](#tab/localclusteroneboxcontainer)

Inicie um conjunto de tecido de [serviço](https://hub.docker.com/r/microsoft/service-fabric-onebox/)baseado em contentores.

1. Instale o Moby para poder implantar contentores Docker.
    ```bash
    sudo apt-get install moby-engine moby-cli -y
    ```
2. Atualize a configuração do daemon do Docker no seu anfitrião com as seguintes definições e reinicie o daemon Domon Do docker. Detalhes: [Ativar o suporte do IPv6](https://docs.docker.com/config/daemon/ipv6/)

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```

3. Inicie o agrupamento.<br/>
    <b>Ubuntu 18.04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16.04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > Por predefinição, isto irá extrair a imagem com a versão mais recente do Service Fabric. Para revisões específicas, visite a página do [Hub do Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/).

# <a name="local-cluster"></a>[Aglomerado local](#tab/localcluster)

Depois de instalar o SDK utilizando passos acima, inicie um cluster local.

1. Execute o script de configuração do cluster.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

---

2. Abra um navegador web e vá ao **Service Fabric Explorer** `http://localhost:19080/Explorer` (). Quando o cluster for iniciado, verá o dashboard do Service Fabric Explorer. Pode demorar alguns minutos até o cluster estar totalmente configurado. Se o browser falhar em abrir o URL ou se o Service Fabric Explorer não indicar que o sistema está preparado, aguarde alguns minutos e tente novamente.

    ![Service Fabric Explorer no Linux][sfx-linux]

    Agora, pode implementar pacotes de aplicações do Service Fabric pré-configurados ou novos com base em contentores convidados ou em executáveis convidados. Para criar novos serviços com os SDKs Java ou .NET Core, siga os passos de configuração opcionais apresentados nas secções seguintes.


> [!NOTE]
> O Linux não suporta clusters autónomos.


> [!TIP]
> Se tiver um disco SSD disponível, recomendamos a passagem de um caminho de pasta SSD com `--clusterdataroot`, com devclustersetup.sh para um desempenho superior.

## <a name="set-up-the-service-fabric-cli"></a>Configurar a CLI do Service Fabric

A [CLI do Service Fabric](service-fabric-cli.md) inclui comandos para interagir com entidades do Service Fabric, incluindo clusters e aplicações. Para instalar a CLI, siga as instruções em [CLI do Service Fabric](service-fabric-cli.md).


## <a name="set-up-yeoman-generators-for-containers-and-guest-executables"></a>Instalar geradores do Yeoman para contentores e executáveis convidados
O Service Fabric fornece ferramentas estruturais que o ajudam a criar aplicações do Service Fabric a partir de um terminal com os geradores de modelos Yeoman. Siga estes passos para configurar os geradores de modelos Yeoman do Service Fabric:

1. Instale Node.js e npm no seu computador.

    ```bash
    sudo add-apt-repository "deb https://deb.nodesource.com/node_8.x $(lsb_release -s -c) main"
    sudo apt-get update
    sudo apt-get install nodejs
    ```
2. Instale o gerador de modelos [Yeoman](https://yeoman.io/) no seu computador a partir do npm.

    ```bash
    sudo npm install -g yo
    ```
3. Instale o gerador de contentor Yeo do Service Fabric e o gerador executável convidado a partir do npm.

    ```bash
    sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
    sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
    ```

Depois de instalar os geradores, crie serviços de contentor ou executáveis convidados ao executar `yo azuresfguest` ou `yo azuresfcontainer`, respetivamente.

## <a name="set-up-net-core-31-development"></a>Configurar o desenvolvimento .NET Core 3.1

Instale o [.NET Core 3.1 SDK para ubuntu](https://www.microsoft.com/net/core#linuxubuntu) começar a [criar aplicações C# Service Fabric](service-fabric-create-your-first-linux-application-with-csharp.md). Os pacotes para aplicações de tecido de serviço .NET Core fabric são hospedados em NuGet.org.

## <a name="set-up-java-development"></a>Configurar o desenvolvimento em Java

Para construir serviços de Tecido de Serviço utilizando a Java, instale Gradle para executar tarefas de construção. Executar o comando abaixo para instalar Gradle. As bibliotecas Java do Service Fabric são extraídas do Maven.


* Ubuntu

    ```bash
    curl -s https://get.sdkman.io | bash
    sdk install gradle 5.1
    ```

* Red Hat Enterprise Linux 7.4 (suporte de pré-visualização do Service Fabric)

  ```bash
  sudo yum install java-1.8.0-openjdk-devel
  curl -s https://get.sdkman.io | bash
  sdk install gradle
  ```

Também terá de instalar o gerador Yeo do Service Fabric para executáveis Java. Certifique-se de que tem o [Yeoman instalado](#set-up-yeoman-generators-for-containers-and-guest-executables) e, em seguida, execute o seguinte comando:

  ```bash
  npm install -g generator-azuresfjava
  ```
 
## <a name="install-the-eclipse-plug-in-optional"></a>Instalar o plug-in Eclipse (opcional)

Pode instalar o plug-in do Eclipse para o Service Fabric a partir do IDE do Eclipse para Programadores de Java ou Programadores de Java EE. Pode utilizar o Eclipse para criar aplicações executáveis de convidado do Service Fabric e aplicações de contentor, além de aplicações Java do Service Fabric.

> [!IMPORTANT]
> O plug-in do Service Fabric requer o Eclipse ou uma versão posterior. Consulte as instruções que se seguem a esta nota para saber como verificar a sua versão do Eclipse. Se tiver uma versão anterior do Eclipse instalada, pode transferir as versões mais recentes do [site do Eclipse](https://www.eclipse.org). Recomendamos que não instale por cima de (substituir) uma versão existente do Eclipse. Remova-o antes de executar o instalador ou instale a versão mais recente num diretório diferente.
> 
> No Ubuntu, é recomendável instalar diretamente a partir do site do Eclipse, em vez de utilizar um instalador de pacote (`apt` ou `apt-get`). Se o fizer, assegura que obtém a versão mais recente do Eclipse. Pode instalar o Eclipse IDE para Programadores de Java ou para Programadores de Java EE.

1. No Eclipse, certifique-se de que tem instalado o Eclipse Neon ou posterior e a versão 2.2.1 do Buildship ou posterior. Verifique as versões dos componentes instalados selecionando Detalhes de **Instalação de Ajuda** Sobre  >  **Eclipse**  >  **.** Pode atualizar o Buildship com as instruções apresentadas em [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship: Plug-ins do Eclipse para Gradle).

2. Para instalar o plug-in do Tecido de Serviço, selecione **Ajuda a** instalar  >  **novos softwares** .

3. No **Trabalho com** caixa, **insira https: \/ /dl.microsoft.com/eclipse** .

4. Selecione **Adicionar** .

    ![Página Software Disponível][sf-eclipse-plugin]

5. Selecione o plug-in **ServiceFabric** e selecione **Seguinte** .

6. Execute os passos de instalação. Em seguida, aceite o contrato de licença do utilizador final.

Se já tiver o plug-in do Service Fabric para o Eclipse instalado, confirme que tem a versão mais recente. Verifique selecionando **Ajuda**  >  **Sobre Detalhes de**  >  **Instalação do Eclipse** . Em seguida, procure o Tecido de Serviço na lista de plug-ins instalados. Selecione **Update** se uma versão mais recente estiver disponível.

Para obter mais informações, veja [Plug-in do Service Fabric para desenvolvimento de aplicações Java de Eclipse](service-fabric-get-started-eclipse.md).

## <a name="update-the-sdk-and-runtime"></a>Atualizar o SDK e o runtime

Para atualizar para a versão mais recente do SDK e do runtime, execute os seguintes comandos.

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
Para atualizar os binários SDK do Java a partir do Maven, tem de atualizar os detalhes da versão do binário correspondente no ficheiro ``build.gradle``, para que apontem para a versão mais recente. Para saber exatamente onde é necessário atualizar a versão, consulte qualquer ficheiro ``build.gradle`` nos [exemplos de introdução ao Service Fabric](https://github.com/Azure-Samples/service-fabric-java-getting-started).

> [!NOTE]
> Atualizar os pacotes pode fazer com que a execução do cluster de desenvolvimento local pare. Reinicie o cluster local após uma atualização ao seguir as instruções neste artigo.

## <a name="remove-the-sdk"></a>Remover o SDK
Para remover os SDKs do Service Fabric, execute os seguintes comandos.

* Ubuntu

    ```bash
    sudo apt-get remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    sudo apt-get install -f
    ```


* Red Hat Enterprise Linux 7.4 (suporte de pré-visualização do Service Fabric)

    ```bash
    sudo yum remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    ```

## <a name="next-steps"></a>Passos seguintes

* [Criar e implementar a sua primeira aplicação Java do Service Fabric no Linux com o Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Criar e implementar a sua primeira aplicação Java do Service Fabric no Linux com o Plug-in do Service Fabric para Eclipse](service-fabric-get-started-eclipse.md)
* [Create your first CSharp application on Linux (Criar a sua primeira aplicação CSharp no Linux)](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Prepare your development environment on OSX (Preparar o ambiente de desenvolvimento no OSX)](service-fabric-get-started-mac.md)
* [Prepare a Linux development environment on Windows](service-fabric-local-linux-cluster-windows.md) (Preparar um ambiente de desenvolvimento do Linux no Windows)
* [Gerir as aplicações com a CLI do Service Fabric](service-fabric-application-lifecycle-sfctl.md)
* [Diferenças do Service Fabric no Windows e no Linux](service-fabric-linux-windows-differences.md)
* [Introdução à CLI do Service Fabric](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
