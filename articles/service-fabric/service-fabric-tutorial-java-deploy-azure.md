---
title: Implementar uma aplicação Java para um cluster de tecido de serviço em Azure
description: Neste tutorial, saiba como implementar uma aplicação Java Service Fabric num cluster do Azure Service Fabric.
ms.topic: tutorial
ms.date: 02/26/2018
ms.custom: mvc, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 89c49ae530b7a4716bc6e8bf0ea6ccb011847eb8
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92738902"
---
# <a name="tutorial-deploy-a-java-application-to-a-service-fabric-cluster-in-azure"></a>Tutorial: Implementar uma aplicação Java num cluster do Service Fabric no Azure

Este tutorial é a terceira parte de uma série e mostra-lhe como implementar uma aplicação do Service Fabric num cluster no Azure.

Na terceira parte da série, ficará a saber como:

> [!div class="checklist"]
> * Criar um cluster do Linux seguro no Azure
> * Implementar uma aplicação no cluster

Nesta série de tutoriais, ficará a saber como:

> [!div class="checklist"]
> * [Criar uma aplicação Java Service Fabric Reliable Services](service-fabric-tutorial-create-java-app.md)
> * [Implementar e depurar a aplicação num cluster local](service-fabric-tutorial-debug-log-local-cluster.md)
> * Implementar a aplicação num cluster do Azure
> * [Configurar a monitorização e os diagnósticos da aplicação](service-fabric-tutorial-java-elk.md)
> * [Configurar CI/CD](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Instalar a CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest)
* Instalar o SDK do Service Fabric para [Mac](service-fabric-get-started-mac.md) ou [Linux](service-fabric-get-started-linux.md)
* [Instalar o Python 3](https://wiki.python.org/moin/BeginnersGuide/Download)

## <a name="create-a-service-fabric-cluster-in-azure"></a>Criar um cluster do Service Fabric no Azure

Os passos seguintes criam os recursos necessários para implementar a sua aplicação num cluster do Service Fabric. Além disso, são configurados os recursos necessários para monitorizar o estado de funcionamento da sua solução através da pilha ELK (Elasticsearch Logstash, Kibana). Especificamente, os [Hubs de Eventos](https://azure.microsoft.com/services/event-hubs/) são utilizados como sink para registos do Service Fabric. É configurado para enviar registos do cluster do Service Fabric para a sua instância do Logstash.

1. Abrir um terminal e transferir o pacote seguinte que contém os scripts auxiliares necessários e os modelos para criar os recursos no Azure

    ```bash
    git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
    ```

2. Inicie sessão na sua conta do Azure

    ```azurecli
    az login
    ```

3. Definir a subscrição do Azure que quer utilizar para criar os recursos

    ```azurecli
    az account set --subscription [SUBSCRIPTION-ID]
    ```

4. Na pasta *service-fabric-java-quickstart/AzureCluster* , execute o comando seguinte para criar um certificado de cluster no Key Vault. Este certificado é utilizado para proteger o cluster do Service Fabric. Indique a região (tem de ser a mesma do cluster do Service Fabric), o nome do grupo de recursos do cofre de chaves, o nome do cofre de chaves, a palavra-passe do certificado e o nome DNS do cluster.

    ```bash
    ./new-service-fabric-cluster-certificate.sh [REGION] [KEY-VAULT-RESOURCE-GROUP] [KEY-VAULT-NAME] [CERTIFICATE-PASSWORD] [CLUSTER-DNS-NAME-FOR-CERTIFICATE]

    Example: ./new-service-fabric-cluster-certificate.sh 'westus' 'testkeyvaultrg' 'testkeyvault' '<password>' 'testservicefabric.westus.cloudapp.azure.com'
    ```

    O comando anterior devolve as informações seguintes, que deve anotar para utilização posterior.

    ```output
    Source Vault Resource Id: /subscriptions/<subscription_id>/resourceGroups/testkeyvaultrg/providers/Microsoft.KeyVault/vaults/<name>
    Certificate URL: https://<name>.vault.azure.net/secrets/<cluster-dns-name-for-certificate>/<guid>
    Certificate Thumbprint: <THUMBPRINT>
    ```

5. Criar um grupo de recursos para a conta de armazenamento que armazena os registos

    ```azurecli
    az group create --location [REGION] --name [RESOURCE-GROUP-NAME]

    Example: az group create --location westus --name teststorageaccountrg
    ```

6. Criar uma conta de armazenamento que será utilizada para armazenar os registos que serão produzidos

    ```azurecli
    az storage account create -g [RESOURCE-GROUP-NAME] -l [REGION] --name [STORAGE-ACCOUNT-NAME] --kind Storage

    Example: az storage account create -g teststorageaccountrg -l westus --name teststorageaccount --kind Storage
    ```

7. Aceda ao [portal do Azure](https://portal.azure.com) e navegue para o separador **Assinatura de Acesso Partilhado** da conta de armazenamento. Gere o token SAS da seguinte forma.

    ![Gerar SAS para Armazenamento](./media/service-fabric-tutorial-java-deploy-azure/storagesas.png)

8. Copie o URL de SAS da conta e defina-o separadamente para utilização quando criar o cluster do Service Fabric. Assemelha-se ao seguinte URL:

    ```output
    ?sv=2017-04-17&ss=bfqt&srt=sco&sp=rwdlacup&se=2018-01-31T03:24:04Z&st=2018-01-30T19:24:04Z&spr=https,http&sig=IrkO1bVQCHcaKaTiJ5gilLSC5Wxtghu%2FJAeeY5HR%2BPU%3D
    ```

9. Crie um grupo de recursos com os recursos do Hub de Eventos. Os Hubs de Eventos são utilizados para enviar mensagens do Service Fabric para o servidor que executa os recursos ELK.

    ```azurecli
    az group create --location [REGION] --name [RESOURCE-GROUP-NAME]

    Example: az group create --location westus --name testeventhubsrg
    ```

10. Crie um recurso dos Hubs de Eventos com o seguinte comando. Siga as indicações para introduzir detalhes para namespaceName, eventHubName, consumerGroupName, sendAuthorizationRule e receiveAuthorizationRule.

    ```azurecli
    az group deployment create -g [RESOURCE-GROUP-NAME] --template-file eventhubsdeploy.json

    Example:
    az group deployment create -g testeventhubsrg --template-file eventhubsdeploy.json
    Please provide string value for 'namespaceName' (? for help): testeventhubnamespace
    Please provide string value for 'eventHubName' (? for help): testeventhub
    Please provide string value for 'consumerGroupName' (? for help): testeventhubconsumergroup
    Please provide string value for 'sendAuthorizationRuleName' (? for help): sender
    Please provide string value for 'receiveAuthorizationRuleName' (? for help): receiver
    ```

    Copie o conteúdo do campo **saída** na saída JSON do comando anterior. As informações do remetente são utilizadas quando é criado o cluster do Service Fabric. O nome e a chave do recetor devem ser guardados para utilização no próximo tutorial quando o serviço Logstash está configurado para receber mensagens do Hub de Eventos. O blob seguinte é uma saída JSON de exemplo:

    ```json
    "outputs": {
        "receiver Key": {
            "type": "String",
            "value": "[KEY]"
        },
        "receiver Name": {
            "type": "String",
            "value": "receiver"
        },
        "sender Key": {
            "type": "String",
            "value": "[KEY]"
        },
        "sender Name": {
            "type": "String",
            "value": "sender"
        }
    }
    ```

11. Execute o script *eventhubssastoken.py* para gerar o URL de SAS para o recurso EventHubs que criou. Este URL de SAS é utilizado pelo cluster do Service Fabric para enviar registos aos Hubs de Eventos. Como resultado, é utilizada a política do **remetente** para gerar o URL. O script devolve o URL de SAS para o recurso dos Hubs de Eventos utilizado no passo seguinte:

    ```python
    python3 eventhubssastoken.py 'testeventhubs' 'testeventhubs' 'sender' '[PRIMARY-KEY]'
    ```

    Copie o valor do campo **sr** no JSON devolvido. O valor do campo **sr** é o token SAS para EventHubs. O URL seguinte é um exemplo do campo **sr** :

    ```output
    https%3A%2F%testeventhub.servicebus.windows.net%testeventhub&sig=7AlFYnbvEm%2Bat8ALi54JqHU4i6imoFxkjKHS0zI8z8I%3D&se=1517354876&skn=sender
    ```

    O url SAS para os EventHubs segue a estrutura: `https://<namespacename>.servicebus.windows.net/<eventhubsname>?sr=<sastoken>` . Por exemplo, `https://testeventhubnamespace.servicebus.windows.net/testeventhub?sr=https%3A%2F%testeventhub.servicebus.windows.net%testeventhub&sig=7AlFYnbvEm%2Bat8ALi54JqHU4i6imoFxkjKHS0zI8z8I%3D&se=1517354876&skn=sender`

12. Abra a *sfdeploy.parameters.jsno* ficheiro e substitua os seguintes conteúdos dos passos anteriores. [SAS-URL-STORAGE-ACCOUNT] foi indicado no passo 8. [SAS-URL-EVENT-HUBS] foi indicado no passo 11.

    ```json
    "applicationDiagnosticsStorageAccountName": {
        "value": "teststorageaccount"
    },
    "applicationDiagnosticsStorageAccountSasToken": {
        "value": "[SAS-URL-STORAGE-ACCOUNT]"
    },
    "loggingEventHubSAS": {
        "value": "[SAS-URL-EVENT-HUBS]"
    }
    ```

13. É aberta **sfdeploy.parameters.json** . Altere os parâmetros seguintes e, em seguida, guarde o ficheiro.
    - **clusterName** . Utilize apenas letras minúsculas e números.
    - **adminUserName** (para um valor diferente de zero)
    - **adminPassword** (para um valor diferente de zero)

14. Execute o seguinte comando para criar o cluster do Service Fabric

    ```azurecli
    az sf cluster create --location 'westus' --resource-group 'testlinux' --template-file sfdeploy.json --parameter-file sfdeploy.parameters.json --secret-identifier <certificate_url_from_step4>
    ```

## <a name="deploy-your-application-to-the-cluster"></a>Implemente a aplicação no cluster

1. Antes de implementar a aplicação, tem de adicionar o fragmento seguinte ao ficheiro *Voting/VotingApplication/ApplicationManifest.xml* . O campo **X509FindValue** é o thumbprint devolvido no Passo 4 da secção **Criar um cluster do Service Fabric no Azure** . Este fragmento está aninhado no campo **ApplicationManifest** (o campo raiz).

    ```xml
    <Certificates>
          <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[CERTIFICATE-THUMBPRINT]" />
    </Certificates>
    ```

2. Para implementar a aplicação neste cluster, tem de utilizar o SFCTL para estabelecer uma ligação ao cluster. O SFCTL necessita de um ficheiro PEM com chave pública e chave privada para se ligar ao cluster. Execute o seguinte comando para produzir um ficheiro PEM com chave pública e chave privada. 

    ```bash
    openssl pkcs12 -in <clustername>.<region>.cloudapp.azure.com.pfx -out sfctlconnection.pem -nodes -passin pass:<password>
    ```

3. Execute o seguinte comando para ligar ao cluster.

    ```bash
    sfctl cluster select --endpoint https://<clustername>.<region>.cloudapp.azure.com:19080 --pem sfctlconnection.pem --no-verify
    ```

4. Para implementar a aplicação, navegue para a pasta *Voting/Scripts* e execute o script **install.sh** .

    ```bash
    ./install.sh
    ```

5. Para aceder ao Service Fabric Explorer, abra o seu browser favorito e escreva `https://testlinuxcluster.westus.cloudapp.azure.com:19080`. Escolha o certificado no arquivo de certificados que quer utilizar para ligar a este ponto final. Se estiver a utilizar uma máquina Linux, os certificados gerados pelo script *new-service-fabric-cluster-certificate.sh* têm de ser importados para o Chrome para ver o Service Fabric Explorer. Se estiver a utilizar um Mac, tem de instalar o ficheiro PFX na Keychain. Repare se a aplicação foi instalada no cluster.

    ![SFX Java Azure](./media/service-fabric-tutorial-java-deploy-azure/sfxjavaonazure.png)

6. Para aceder à sua aplicação, escreva `https://testlinuxcluster.westus.cloudapp.azure.com:8080`

    ![Voting App Java Azure](./media/service-fabric-tutorial-java-deploy-azure/votingappjavaazure.png)

7. Para desinstalar a aplicação do cluster, execute o script *uninstall.sh* na pasta **Scripts**

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um cluster do Linux seguro no Azure
> * Criar os recursos necessários para monitorizar com o ELK

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Configurar Monitorização e Diagnóstico](service-fabric-tutorial-java-elk.md)
