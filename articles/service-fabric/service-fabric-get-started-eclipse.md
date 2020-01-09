---
title: Plug-in do Azure Service Fabric para Eclipse
description: Saiba mais sobre como começar a usar o Azure Service Fabric em Java usando o Eclipse e o plug-in Service Fabric fornecido.
author: rapatchi
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: rapatchi
ms.openlocfilehash: b779873488f1fff754d4105249b28f545738c11b
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645706"
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Plug-in do Service Fabric para desenvolvimento de aplicações Java de Eclipse
O Eclipse é um dos ambientes de desenvolvimento integrados (IDEs) mais utilizados para programadores Java. Neste artigo, descrevemos como configurar o seu ambiente de desenvolvimento do Eclipse para funcionar com o Azure Service Fabric. Saiba como instalar o plug-in do Service Fabric e criar uma aplicação do Service Fabric e implementá-la num cluster do Service Fabric local ou remoto no Eclipse. 

> [!NOTE]
> O plug-in do Eclipse não é atualmente suportado no Windows. 

> [!IMPORTANT]
> Verifique se o JDK 8 está instalado no sistema e selecionado no Eclipse.

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse"></a>Instalar ou atualizar o plug-in do Service Fabric no Eclipse
Pode instalar um plug-in do Service Fabric no Eclipse. O plug-in pode ajudar a simplificar o processo de criação e implementação de serviços Java.

> [!IMPORTANT]
> O plug-in do Service Fabric requer o Eclipse ou uma versão posterior. Consulte as instruções que se seguem a esta nota para saber como verificar a sua versão do Eclipse. Se tiver uma versão anterior do Eclipse instalada, pode transferir as versões mais recentes do [site do Eclipse](https://www.eclipse.org). Não é recomendado que instale por cima de (substituir) uma versão existente do Eclipse. Pode removê-lo antes de executar o instalador ou instalar a versão mais recente num diretório diferente. 
> 
> No Ubuntu, é recomendável instalar diretamente a partir do site do Eclipse, em vez de utilizar um instalador de pacote (`apt` ou `apt-get`). Se o fizer, assegura que obtém a versão mais recente do Eclipse. 

Instale o Eclipse Neon ou posterior a partir do [site do Eclipse](https://www.eclipse.org).  Instale também a versão 2.2.1 ou posterior do Buildship (o plug-in do Service Fabric não é compatível com versões anteriores do Buildship):
-   Para verificar as versões dos componentes instalados, no Eclipse, aceda a **Ajuda** > **Acerca do Eclipse** > **Detalhes da Instalação**.
-   Para atualizar a criação, consulte [criação do eclipse: plug-ins do Eclipse para gradle][buildship-update].
-   Para procurar e instalar atualizações do Eclipse, aceda a **Ajuda** > **Procurar Atualizações**.

Para instalar o plug-in do Service Fabric, no Eclipse, aceda a **Ajuda** > **Instalar Software Novo**.
1. Na caixa **trabalhar com** , digite https:\//DL.Microsoft.com/eclipse.
2. Clique em **Adicionar**.

   ![Plug-in do Service Fabric para Eclipse][sf-eclipse-plugin-install]
3. Selecione o plug-in do Service Fabric e clique em **Seguinte**.
4. Conclua os passos de instalação e aceite os Termos de Licenciamento para Software Microsoft.
  
Se já tiver o plug-in do Service Fabric instalado, instale a versão mais recente. 
1. Para verificar se existem atualizações disponíveis, aceda a **Ajuda** > **Acerca do Eclipse** > **Detalhes da Instalação**. 
2. Na lista de plug-ins instalados, selecione Service Fabric e clique em **Atualizar**. As atualizações disponíveis são instaladas.
3. Depois de atualizar o plug-in do Service Fabric, atualize também o projeto do Gradle.  Clique com o botão direito do rato em **build.gradle** e, em seguida, selecione **Atualizar**.

> [!NOTE]
> Se a instalação ou atualização do plug-in do Service Fabric estiver lenta, tal poderá dever-se a uma definição do Eclipse. O Eclipse recolhe metadados sobre todas as alterações para atualizar os sites que estão registados na sua instância do Eclipse. Para acelerar o processo de procurar e instalar uma atualização do plug-in do Service Fabric, aceda a **Sites de Software Disponíveis**. Desmarque as caixas de seleção de todos os sites, exceto aquele que aponta para o local de plug-in Service Fabric (https:\//dl.microsoft.com/eclipse/azure/servicefabric).

> [!NOTE]
>Se o Eclipse não estiver a funcionar conforme esperado no Mac (ou se precisar que utilize o perfil de superutilizador), aceda à pasta **ECLIPSE_INSTALLATION_PATH** e navegue para a subpasta **Eclipse.app/Contents/MacOS**. Abra o Eclipse mediante a execução de `./eclipse`.


## <a name="create-a-service-fabric-application-in-eclipse"></a>Criar uma aplicação do Service Fabric no Eclipse

1.  No Eclipse, aceda a **File (Ficheiro)**  > **New (Novo)**  > **Other (Outro)** . Selecione **Service Fabric Project** (Projeto do Service Fabric) e clique em **Next** (Seguinte).

    ![Página 1 de Novo Projeto do Service Fabric][create-application/p1]

2.  Introduza um nome para o seu projeto e, em seguida, clique em **Next** (Seguinte).

    ![Página 2 de Novo Projeto do Service Fabric][create-application/p2]

3.  Na lista de modelos, selecione **Service Template** (Modelo de Serviço). Selecione o tipo de modelo de serviço (Actor (Ator), Stateless (Sem Estado), Container (Contentor) ou Binário Convidado (Guest Binary)) e clique em **Next** (Seguinte).

    ![Página 3 de Novo Projeto do Service Fabric][create-application/p3]

4.  Introduza o nome e os detalhes do serviço e clique em **Finish** (Concluir).

    ![Página 4 de Novo Projeto do Service Fabric][create-application/p4]

5. Quando cria o seu primeiro projeto do Service Fabric, na caixa de diálogo **Open Associated Perspective** (Abrir Perspetiva Associada), clique em **Yes** (Sim).

    ![Página 5 de Novo Projeto do Service Fabric][create-application/p5]

6.  O seu projeto novo tem o seguinte aspeto:

    ![Página 6 de Novo Projeto do Service Fabric][create-application/p6]

## <a name="build-a-service-fabric-application-in-eclipse"></a>Criar um aplicativo Service Fabric no Eclipse

1.  Clique com o botão direito na aplicação do Service Fabric nova e selecione **Service Fabric**.

    ![Menu de clique com o botão direito do rato do Service Fabric][publish/RightClick]

2. No menu de contexto, selecione uma das seguintes opções:
    -   Para criar a aplicação sem limpar, clique em **Build Application** (Criar Aplicação).
    -   Para criar a aplicação de forma limpa, clique em **Rebuild Application** (Recriar Aplicação).
    -   Para limpar a aplicação de artefactos criados, clique em **Clean Application** (Limpar Aplicação).
     
## <a name="deploy-a-service-fabric-application-to-the-local-cluster-with-eclipse"></a>Implantar um aplicativo Service Fabric no cluster local com o eclipse

Depois de criar seu aplicativo Service Fabric, siga estas etapas para implantá-lo no cluster local.

1. Se você não tiver iniciado o cluster local, siga as instruções em [configurar um cluster local](./service-fabric-get-started-linux.md#set-up-a-local-cluster) para iniciar o cluster local e verificar se ele está em execução.
2. Clique com o botão direito do mouse no aplicativo Service Fabric e selecione **Service Fabric**.

    ![Menu de clique com o botão direito do rato do Service Fabric][publish/RightClick]

3.  No menu de contexto, clique em **implantar aplicativo**.
4.  Você pode acompanhar o progresso da operação de implantação na janela do console.
5.  Para verificar se o aplicativo está em execução, abra Service Fabric Explorer em seu cluster local em uma janela do navegador [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Expanda o nó **aplicativos** e verifique se o aplicativo está em execução. 

Para saber como depurar seu aplicativo no Eclipse usando o cluster local, consulte [depurar um serviço Java no Eclipse](./service-fabric-debugging-your-application-java.md).

Você também pode implantar seu aplicativo no cluster local com o comando **publicar aplicativo** :

1. Clique com o botão direito do mouse no aplicativo Service Fabric e selecione **Service Fabric**.
2. No menu de contexto, clique em **publicar aplicativo...** .
3. Na janela **publicar aplicativo** , escolha **PublishProfiles/local. JSON** como o perfil de destino e clique em **publicar**.

    ![Caixa de Diálogo Publicar](./media/service-fabric-get-started-eclipse/localjson.png)

    Por padrão, o perfil de publicação local. JSON é configurado para publicar no cluster local. Para obter mais informações sobre os parâmetros Connection e Endpoint presentes em perfis de publicação, consulte a próxima seção.

## <a name="publish-your-service-fabric-application-to-azure-with-eclipse"></a>Publicar seu aplicativo Service Fabric no Azure com o eclipse

Para publicar seu aplicativo na nuvem, siga estas etapas:

1. Para publicar seu aplicativo em um cluster seguro na nuvem, você precisa de um certificado X. 509 a ser usado para se comunicar com o cluster. Em ambientes de teste e desenvolvimento, o certificado usado geralmente é o certificado do cluster. Em ambientes de produção, o certificado deve ser um certificado de cliente que seja diferente do certificado de cluster. Você precisa do certificado e da chave privada. O arquivo de certificado (e chave) deve ser formatado por PEM. Você pode criar um arquivo PEM que contém o certificado e a chave privada de um arquivo PFX com o comando openssl a seguir:

    ```bash
    openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
    ```

   Se o arquivo PFX não for protegido por senha, use `--passin pass:` para o último parâmetro.

2. Abra o arquivo **Cloud. JSON** no diretório **PublishProfiles** Você precisa configurar o ponto de extremidade do cluster e as credenciais de segurança adequadamente para o cluster.

   - O campo `ConnectionIPOrURL` contém o endereço IP ou a URL do cluster. Observe que o valor não contém o esquema de URL (`https://`).
   - Por padrão, o campo `ConnectionPort` deve ser `19080`, a menos que você tenha alterado explicitamente essa porta para o cluster.
   - O campo `ClientKey` deve apontar para um arquivo. PEM ou. Key formatado por PEM em seu computador local que contém a chave privada para seu certificado de cliente ou de cluster.
   - O campo `ClientCert` deve apontar para um arquivo. PEM ou. CRT formatado por PEM em seu computador local que contém os dados do certificado para seu cliente ou cluster. certificado. 

     ```bash
     {
         "ClusterConnectionParameters":
         {
            "ConnectionIPOrURL": "lnxxug0tlqm5.westus.cloudapp.azure.com",
            "ConnectionPort": "19080",
            "ClientKey": "[path_to_your_pem_file_on_local_machine]",
            "ClientCert": "[path_to_your_pem_file_on_local_machine]"
         }
     }
     ```

2. Clique com o botão direito do mouse no aplicativo Service Fabric e selecione **Service Fabric**.
3. No menu de contexto, clique em **publicar aplicativo...** .
3. Na janela **publicar aplicativo** , escolha **PublishProfiles/Cloud. JSON** como o perfil de destino e clique em **publicar**.

    ![Cloud da Caixa de diálogo Publicar](./media/service-fabric-get-started-eclipse/cloudjson.png)

4. Você pode seguir o progresso da operação de publicação na janela do console.
5. Para verificar se o aplicativo está em execução, abra Service Fabric Explorer no cluster do Azure em uma janela do navegador. Para o exemplo acima, isso seria: `https://lnxxug0tlqm5.westus.cloudapp.azure.com:19080/Explorer`. Expanda o nó **aplicativos** e verifique se o aplicativo está em execução. 


Em clusters Linux seguros, se seu aplicativo contiver serviços Reliable Services, você também precisará configurar um certificado que seus serviços possam usar para chamar Service Fabric APIs de tempo de execução. Para saber mais, confira [configurar um aplicativo Reliable Services para ser executado em clusters do Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).

Para obter uma visão rápida de como implantar um aplicativo Service Fabric Reliable Services escrito em Java em um cluster seguro do Linux, consulte [início rápido: implantar um aplicativo java Reliable Services](./service-fabric-quickstart-java-reliable-services.md).

## <a name="deploy-a-service-fabric-application-by-using-eclipse-run-configurations"></a>Implantar um aplicativo Service Fabric usando as configurações de execução do eclipse

Outra forma de implementar a aplicação do Service Fabric é utilizar configurações de execução do Eclipse.

1. No Eclipse, vá para **executar** > **executar configurações**.
2. Em **Gradle Project** (Projeto Gradle), selecione a configuração de execução **ServiceFabricDeployer**.
3. No painel direito, na guia **argumentos** , verifique se os parâmetros **IP**, **Port**, **clientCert**e **clientkey vazio** estão definidos adequadamente para sua implantação. Por padrão, os parâmetros são definidos para implantar no cluster local como na captura de tela a seguir. Para publicar seu aplicativo no Azure, você pode modificar os parâmetros para conter os detalhes do ponto de extremidade e as credenciais de segurança para o cluster do Azure. Para obter mais informações, consulte a seção anterior, [publicar seu aplicativo Service Fabric no Azure com o eclipse](#publish-your-service-fabric-application-to-azure-with-eclipse).

    ![Caixa de diálogo de configuração de execução local](./media/service-fabric-get-started-eclipse/run-config-local.png)

5. Certifique-se de que o **diretório de trabalho** aponta para o aplicativo que você deseja implantar. Para alterar a aplicação, clique no botão **Workspace** (Área de Trabalho) e selecione a aplicação que pretende.
6. Clique em **Apply** (Aplicar) e clique em **Run** (Executar).

A sua aplicação é criada e implementada ao fim de alguns momentos. Pode monitorizar o estado da implementação no Service Fabric Explorer.  

## <a name="add-a-service-fabric-service-to-your-service-fabric-application"></a>Adicionar um serviço do Service Fabric à sua aplicação do Service Fabric

Para adicionar um serviço do Service Fabric a uma aplicação do Service Fabric já existente, faça o seguinte:

1.  Clique com o botão direito do rato no projeto ao qual pretende adicionar um serviço e clique em **Service Fabric**.

    ![Página 1 de Adicionar Serviço do Service Fabric][add-service/p1]

2.  Clique em **Add Service Fabric Service** (Adicionar Serviço do Service Fabric) e conclua o conjunto de passos para adicionar um serviço ao projeto.
3.  Selecione o modelo de serviço que pretende adicionar ao seu projeto e clique em **Next** (Seguinte).

    ![Página 2 de Adicionar Serviço do Service Fabric][add-service/p2]

4.  Introduza o nome do serviço (e outros detalhes, conforme necessário) e clique no botão **Add Service** (Adicionar Serviço).  

    ![Página 3 de Adicionar Serviço do Service Fabric][add-service/p3]

5.  Quando o serviço for adicionado, a estrutura geral do seu projeto será semelhante ao projeto seguinte:

    ![Página 4 de Adicionar Serviço do Service Fabric][add-service/p4]

## <a name="edit-manifest-versions-of-your-service-fabric-java-application"></a>Editar versões do Manifesto da sua aplicação Java do Service Fabric

Para editar versões do manifesto, clique com o botão direito do rato no projeto, entre no **Service Fabric** e selecione **Editar Versões do Manifesto...**  do menu suspenso. No assistente, pode atualizar as versões do manifesto para o manifesto de aplicação, o manifesto do serviço e as versões dos pacotes **Código**, **Config** e **Dados**.

Se marcar a opção **Atualizar automaticamente as versões da aplicação e do serviço** e, em seguida, atualizar uma versão, as versões do manifesto serão atualizadas automaticamente. Por exemplo, primeiro selecione a caixa de verificação e atualize a versão do **Código** de 0.0.0 para 0.0.1 e clique em **Concluir**, em seguida, a versão do manifesto do serviço e a versão do manifesto de aplicação serão atualizadas automaticamente para 0.0.1.

## <a name="upgrade-your-service-fabric-java-application"></a>Atualizar a sua aplicação Java do Service Fabric

Num cenário de atualização, imaginemos que utilizou o plug-in do Service Fabric no Eclipse para criar o projeto **App1**. Para o implementar, utilizou o plug-in para criar uma aplicação com o nome **fabric:/App1Application**. O tipo de aplicativo é **App1ApplicationType**e a versão do aplicativo é 1,0. Agora, quer atualizar a aplicação sem interromper a disponibilidade.

Em primeiro lugar, faça eventuais alterações à aplicação e, em seguida, recrie o serviço modificado. Atualize o ficheiro de manifesto do serviço modificado (ServiceManifest.xml) com as versões atualizadas do serviço (e com Código, Configuração ou Dados, conforme relevante). Modifique também o manifesto da aplicação (ApplicationManifest.xml) com o número da versão atualizada da aplicação e o serviço modificado.  

Para atualizar a aplicação com o Eclipse, pode criar um perfil de configuração de execução duplicado. Em seguida, utilize-o para atualizar a sua aplicação, conforme necessário.

1.  Aceda a **Run (Executar)**  > **Run Configurations (Configurações de Execução)** . No painel do lado esquerdo, clique na seta pequena à esquerdo de **Gradle Project** (Projeto Gradle).
2.  Clique com o botão direito do rato em **ServiceFabricDeployer** e selecione **Duplicate** (Duplicado). Introduza um novo nome para esta configuração, como, por exemplo, **ServiceFabricUpgrader**.
3.  No painel direito, no separador **Arguments** (Argumentos), altere **-Pconfig='deploy'** para **-Pconfig='upgrade'** e clique em **Apply** (Aplicar).

Este processo cria e guarda um perfil de configuração de execução que pode utilizar em qualquer altura para atualizar a sua aplicação. Também obtém a versão de tipo de aplicação atualizada mais recente a partir do ficheiro de manifesto da aplicação.

A atualização da aplicação demora alguns minutos. Pode utilizar o Service Fabric Explorer para monitorizar a atualização da aplicação.

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Migrar aplicações Java antigas do Service Fabric para utilizar com o Maven
Recentemente, movemos as bibliotecas Java do Service Fabric do SDK Java do Service Fabric para o repositório Maven. Enquanto as aplicações novas que gerar através do Eclipse irão gerar projetos com as atualizações mais recentes (que conseguirão trabalhar com o Maven), pode atualizar as aplicações Java sem estado ou de Actor do Service Fabric, que estavam a utilizar anteriormente o SDK Java do Service Fabric, para utilizar as dependências Java do Service Fabric a partir do Maven. Siga os passos mencionados [aqui](service-fabric-migrate-old-javaapp-to-use-maven.md) para garantir que a aplicação mais antiga funciona com o Maven.

## <a name="next-steps"></a>Passos seguintes

- Para obter etapas rápidas sobre como criar um aplicativo de serviço confiável Java e implantá-lo localmente e no Azure, consulte [início rápido: implantar um aplicativo java Reliable Services](./service-fabric-quickstart-java-reliable-services.md).
- Para saber como depurar um aplicativo Java em seu cluster local, consulte [depurar um serviço Java no Eclipse](./service-fabric-debugging-your-application-java.md).
- Para saber como monitorar e diagnosticar Service Fabric aplicativos, consulte [monitorar e diagnosticar serviços em uma configuração de desenvolvimento de computador local](./service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-eclipse/service-fabric-eclipse-plugin.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
