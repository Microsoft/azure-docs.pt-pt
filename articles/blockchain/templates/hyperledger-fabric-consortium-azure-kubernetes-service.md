---
title: Consórcio Hyperledger Fabric no Serviço Azure Kubernetes (AKS)
description: Como implantar e configurar a rede de consórcio hyperledger Fabric no Serviço Azure Kubernetes
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 2312c002e5c2e0b813f8acbdc3e3bff597f204d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79476445"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Consórcio Hyperledger Fabric no Serviço Azure Kubernetes (AKS)

Pode utilizar o modelo Hyperledger Fabric (HLF) no modelo Azure Kubernetes Service (AKS) para implantar e configurar uma rede de consórcio hyperledger Fabric no Azure.

Depois de ler este artigo, irá:

- Obtenha conhecimentos de trabalho da Hyperledger Fabric e dos vários componentes que formam os blocos de construção da rede blockchain Hyperledger Fabric.
- Aprenda a implementar e configurar um consórcio Hyperledger Fabric no Serviço Azure Kubernetes para os seus cenários de produção.

## <a name="hyperledger-fabric-consortium-architecture"></a>Arquitetura do Consórcio Hyperledger Fabric

Para construir a rede Hyperledger Fabric no Azure, é necessário implementar o Serviço de Encomendas e a organização com nós pares. Os diferentes componentes fundamentais que são criados como parte da implementação do modelo são:

- **Nódota :** Um nó responsável pela encomenda de transações no livro de contabilidade. Juntamente com outros nós, os nós encomendados formam o serviço de encomenda da rede Hyperledger Fabric.

- **Pares**de nós : Um nó que acolhe principalmente livros e contratos inteligentes, estes elementos fundamentais da rede.

- **Fabric CA**: Fabric CA é a Autoridade de Certificados (CA) para o Tecido Hyperledger. O Fabric CA permite-lhe inicializar e iniciar o processo de servidor que acolhe a autoridade do certificado. Permite-lhe gerir identidades e certificados. Cada cluster AKS implantado como parte do modelo terá uma cápsula De Tecido CA por padrão.

- **CouchDB ou LevelDB**: A base de dados do Estado mundial para os nós pares pode ser armazenada em LevelDB ou CouchDB. LevelDB é a base de dados estatal padrão incorporada no nó de pares e armazena dados de código em cadeia como simples pares de valor-chave e suporta apenas consultas de chave, chave e chave composta. CouchDB é uma base de dados estatal alternativa opcional que suporta consultas ricas quando os valores de dados de código em cadeia são modelados como JSON.

O modelo de implantação gira vários recursos Azure na sua subscrição. Os diferentes recursos Azure implantados são:

- **Cluster AKS**: Cluster Azure Kubernetes que é configurado de acordo com os parâmetros de entrada fornecidos pelo cliente. O cluster AKS tem várias cápsulas configuradas para executar os componentes da rede Hyperledger Fabric. As diferentes cápsulas criadas são:

  - **Ferramentas de tecido**: A ferramenta de tecido é responsável pela configuração dos componentes do Tecido Hyperledger.
  - **Cápsulas de ordenamento/pares**: Os nós da rede HLF.
  - **Proxy**: Um casulo de procuração NGNIX através do qual as aplicações do cliente podem interagir com o cluster AKS.
  - **Ca de tecido**: A cápsula que executa o Tecido CA.
- **PostgreSQL**: É implementada uma instância de PostgreSQL para manter as identidades do Tecido CA.

- **Cofre chave Azure**: Uma instância chave do cofre é implementada para salvar as credenciais de Ca de tecido e os certificados de raiz fornecidos pelo cliente, que é usado em caso de remissão de modelo, isto é para lidar com a mecânica do modelo.
- **Disco gerido pelo Azure**: O disco Azure Managed destina-se a uma loja persistente para a base de dados do estado do estado do estado do estado do estado do livro e do nó de pares.
- **IP público**: Um ponto final público do cluster AKS implantado para interfaceção com o cluster.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Configuração da rede Hyperledger Fabric Blockchain

Para começar, precisa de uma subscrição Azure que possa suportar a implementação de várias máquinas virtuais e contas de armazenamento padrão. Se não tiver uma subscrição Azure, pode [criar uma conta Azure gratuita.](https://azure.microsoft.com/free/)

Configurar a rede Hyperledger Fabric Blockchain utilizando os seguintes passos:

- [Implementar a organização de ordenamento/pares](#deploy-the-ordererpeer-organization)
- [Construir o consórcio](#build-the-consortium)
- [Executar operações nativas de HLF](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>Implementar a organização de ordenamento/pares

Para começar com a implantação de componentes de rede HLF, navegue para o [portal Azure](https://portal.azure.com). Selecione **Criar um recurso > blockchain** > pesquisa de Tecido **Hiperledger no Serviço Azure Kubernetes**.

1. Selecione **criar** para iniciar a implementação do modelo. O Tecido Create Hyperledger nos ecrãs **de serviço Azure Kubernetes.**

    ![Tecido de hiper-ledger no modelo de serviço Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. Introduza os detalhes do projeto na página **Basics.**

    ![Tecido de hiper-ledger no modelo de serviço Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Introduza os seguintes detalhes:
    - **Subscrição**: Escolha o nome de subscrição onde pretende implementar os componentes da rede HLF.
    - **Grupo de recursos**: Ou criar um novo grupo de recursos ou escolher um grupo de recursos vazios existente, o grupo de recursos irá deter todos os recursos implantados como parte do modelo.
    - **Região**: Escolha a região azure onde pretende implantar o cluster Azure Kubernetes para os componentes HLF. O modelo está disponível em todas as regiões onde o AKS está disponível Certifique-se de escolher uma região onde a sua subscrição não esteja a atingir o limite de quota da Máquina Virtual (VM).
    - **Prefixo de recurso**: Prefixo para nomeação de recursos que são implantados. O prefixo de recursos deve ter menos de seis caracteres de comprimento e a combinação de caracteres deve incluir tanto os números como as letras.
4. Selecione o separador **Definições** de Tecido para definir os componentes da rede HLF que serão implantados.

    ![Tecido de hiper-ledger no modelo de serviço Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Introduza os seguintes detalhes:
    - **Nome**da organização : O nome da organização Fabric, que é necessário para várias operações de avião de dados. O nome da organização tem de ser único por implantação. 
    - **Componente de rede de tecidos:** Escolha os nós de Serviço de Encomenda ou Peer com base no componente da rede Blockchain que pretende configurar.
    - **Número de nós** - Seguem-se os dois tipos de nós:
        - Serviço de encomenda - selecione o número de nós para fornecer tolerância à falha à rede. Apenas 3,5 e 7 são a contagem de nós do ordenador suportado.
        - Pares - você pode escolher 1-10 nódosos com base na sua exigência.
    - **Base de dados do estado mundial**do nó peer : Escolha entre LevelDB e CoucbDB. Este campo é apresentado quando o utilizador escolhe o nó de pares no componente de rede Fabric drop-down.
    - **Nome de utilizador do tecido**: Introduza o nome de utilizador utilizado para a autenticação Fabric CA.
    - **Palavra-passe do Tecido CA**: Introduza a palavra-passe para autenticação De Tecido CA.
    - **Confirmar palavra-passe**: Confirme a palavra-passe do Tecido CA.
    - **Certificados**: Se pretender utilizar os seus próprios certificados de raiz para inicializar o Tecido CA, escolha o certificado raiz de upload para a opção Fabric CA, caso contrário, por padrão, o Fabric CA cria certificados auto-assinados.
    - **Certificado raiz**: Carregar o certificado de raiz (chave pública) com o qual o Tecido CA precisa de ser inicializado. Os certificados de formato .pem são suportados, os certificados devem ser válidos no fuso horário UTC.
    - **Chave privada do Certificado raiz**: Faça o upload da chave privada do certificado raiz. Se tiver um certificado .pem, que tenha chave pública e privada combinada, carregue-o também aqui.


6. Selecione **separador Definições** de cluster AKS para definir a configuração do cluster Azure Kubernetes que é a infraestrutura subjacente à qual serão criados os componentes da rede Fabric.

    ![Tecido de hiper-ledger no modelo de serviço Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Introduza os seguintes detalhes:
    - Nome do **cluster kubernetes**: O nome do cluster AKS que é criado. Este campo é pré-povoado com base no prefixo de recursos fornecido, pode alterar se necessário.
    - **Versão Kubernetes**: A versão dos Kubernetes que será implantada no cluster. Com base na região selecionada no separador **Basics,** as versões suportadas disponíveis podem ser alteradas.
    - **Prefixo DNS**: Prefixo de nome de domínio (DNS) para o cluster AKS. Utilizará o DNS para se ligar à API kubernetes ao gerir os contentores após a criação do cluster.
    - **Tamanho**do nó : O tamanho do nó Kubernetes, pode escolher entre a lista da Unidade de Armazenamento de Stock VM (SKUs) disponível no Azure. Para um desempenho ótimo, recomendamos o Standard DS3 v2.
    - **Contagem do nó**: A contagem do número de nós kubernetes a ser implantado no cluster. Recomendamos que esta contagem de nós seja igual ou superior ao número de nós HLF especificados nas definições do Tecido.
    - **ID**do cliente principal de serviço : Introduza o ID do cliente de um principal de serviço existente ou crie um novo, que é necessário para a autenticação AKS. Veja, passos para [criar o diretor de serviço.](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal)
    - **Segredo**principal do cliente de serviço : Insira o segredo do cliente do principal serviço fornecido no id principal do cliente do serviço.
    - **Confirmar**o segredo do cliente : Confirme o segredo do cliente fornecido no segredo principal do cliente do serviço.
    - **Ativar a monitorização do recipiente:** Escolha ativar a monitorização AKS, que permite que os registos AKS empurrem para o espaço de trabalho de Log Analytics especificado.
    - **Log Analytics workspace**: Log analytics workspace será preenchido com o espaço de trabalho padrão que é criado se a monitorização estiver ativada.

8. Depois de fornecer todos os detalhes acima, selecione **Rever e crie** o separador. A revisão e criação desencadeia a validação dos valores que forneceu.
9. Uma vez que a validação passe, pode selecionar **criar**.
A implementação geralmente demora 10-12 minutos, pode variar dependendo do tamanho e número de nós AKS especificados.
10. Após a implementação bem sucedida, é notificado através de notificações do Azure no canto superior direito.
11. Selecione **Ir ao grupo de recursos** para verificar todos os recursos criados como parte da implementação do modelo. Todos os nomes de recursos começarão com o prefixo fornecido na definição **Basics.**

## <a name="build-the-consortium"></a>Construir o consórcio

Para construir o post do consórcio blockchain implantando o serviço de encomenda e os nós de pares, você precisa realizar os passos abaixo em sequência. **Construa** o script da Sua Rede (byn.sh), o que o ajuda a configurar o consórcio, a criar canal e a instalar código de corrente.

> [!NOTE]
> O script Build Your Network (byn) fornecido é estritamente utilizado para cenários de demonstração/devtest. Para a configuração do grau de produção recomendamos a utilização das APIs hlf nativas.

Todos os comandos para executar o script byn podem ser executados através da Interface de Linha de Comando De Bash Azure (CLI). Pode iniciar sessão na versão web da shell Do Azure através ![Tecido de hiper-ledger no modelo de serviço Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) opção no canto superior direito do portal Azure. No pedido de comando, digite a bata e introduza para mudar para bater CLI.

Consulte a [concha azure](https://docs.microsoft.com/azure/cloud-shell/overview) para mais informações.

![Tecido de hiper-ledger no modelo de serviço Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Descarregue byn.sh e ficheiro fabric-admin.yaml.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/fabric-admin.yaml -o fabric-admin.yaml
```
**Definir abaixo variáveis ambientais na concha De bash Azure CLI:**

Definir informações de canal e informações da organização do ordenador

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=$(az aks show --resource-group $ORDERER_AKS_RESOURCE_GROUP --name $ORDERER_AKS_NAME --subscription $ORDERER_AKS_SUBSCRIPTION -o json | jq .addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName | tr -d '"')
ORDERER_END_POINT="orderer1.$ORDERER_DNS_ZONE:443"
CHANNEL_NAME=<channelName>
```
Definir informações sobre organização de pares

```bash
PEER_AKS_RESOURCE_GROUP=<peerAKSClusterResourceGroup>
PEER_AKS_NAME=<peerAKSClusterName>
PEER_AKS_SUBSCRIPTION=<peerAKSClusterSubscriptionID>
#Peer organization name is case-sensitive. Specify exactly the same name, which was provided while creating the Peer AKS Cluster.
PEER_ORG_NAME=<peerOrganizationName>
```

Crie uma partilha de Ficheiros Azure para partilhar vários certificados públicos entre organizações de pares e ordenadores.

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>

az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING="https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN"
```
**Comandos de Gestão de Canais**

Vá ao cluster AKS da organização de ordenamento e emita comando para criar um novo canal

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**Comandos de Gestão de Consórcios**

Execute abaixo os comandos na ordem dada para adicionar uma organização de pares num canal e consórcio.

1. Vá ao Cluster AKS da Organização Peer e carregue o seu Serviço de Assistência (MSP) num armazenamento de ficheiros Azure.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. Vá ao cluster AKS da Organização de Ordenamento e adicione a organização de pares no canal e no consórcio.

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. Volte para a organização de pares e emita ordens para se juntar a pares no canal.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

Da mesma forma, para adicionar mais organizações de pares no canal, atualizar as variáveis ambientais aks peer de acordo com a organização de pares necessária e executar os passos 1 a 3.

**Comandos de gestão de códigos de corrente**

Execute o comando abaixo para executar o funcionamento relacionado com o código de corrente. Estes comandos executam todas as operações num código de demo. Este código de demonstração tem duas variáveis "a" e "b". Na instantiação do código de corrente, "a" é inicializado com 1000 e "b" é inicializado com 2000. Em cada invocação do código de corrente, 10 unidades são transferidas de "a" para "b". A operação de consulta em código de corrente mostra o estado mundial da variável "A".

Execute os seguintes comandos executados no cluster AKS da organização de pares.

```bash
# switch to peer organization AKS cluster. Skip this command if already connected to the required Peer AKS Cluster
SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
```
**Comandos de operação de código de corrente**

```bash
PEER_NODE_NAME="peer<peer#>"
./byn.sh installDemoChaincode "$PEER_NODE_NAME"
./byn.sh instantiateDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh invokeDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh queryDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME"
```

## <a name="run-native-hlf-operations"></a>Executar operações nativas de HLF

Para ajudar os clientes a começar a executar comandos nativos hyperledger na rede HLF na AKS. A aplicação da amostra é fornecida que utiliza o tecido NodeJS SDK para realizar as operações hlf. Os comandos são fornecidos para Criar uma nova identidade de utilizador e instalar o seu próprio código de corrente.

### <a name="before-you-begin"></a>Antes de começar

Siga os comandos abaixo para a configuração inicial da aplicação:

- Descarregue ficheiros de aplicações
- Gerar perfil de ligação e perfil de administração
- Identidade de utilizador de administração de importação

Após completar a configuração inicial, pode utilizar o SDK para alcançar as operações abaixo:

- Geração de identidade do utilizador
- Operações de código de corrente

Os comandos acima mencionados podem ser executados a partir de Azure Cloud Shell.

### <a name="download-application-files"></a>Descarregue ficheiros de aplicações

A primeira configuração para executar aplicações é descarregar todos os ficheiros de aplicação numa pasta.

**Criar pasta de aplicativos e introduzir na pasta:**

```bash
mkdir app
cd app
```
Execute abaixo o comando para descarregar todos os ficheiros e pacotes necessários:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/application/setup.sh | bash
```
Este comando leva tempo para carregar todos os pacotes. Após a execução bem sucedida `node_modules` do comando, pode ver uma pasta no diretório atual. Todos os pacotes necessários `node_modules` estão carregados na pasta.

### <a name="generate-connection-profile-and-admin-profile"></a>Gerar perfil de ligação e perfil de administração

Criar `profile` diretório `app` dentro da pasta

```bash
cd app
mkdir ./profile
```
Desloque estas variáveis ambientais na casca de nuvem Azure

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

Execute abaixo o comando para gerar perfil de conexão e perfil de administração da organização

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

Criará perfil de ligação e administrador `profile` da organização `<orgname>-ccp.json` `<orgname>-admin.json` na pasta de perfil com nome e respectivamente.

Da mesma forma, gere o perfil de conexão e o perfil de administração para cada ordenador e organização de pares.


### <a name="import-admin-user-identity"></a>Identidade de utilizador de administração de importação

O último passo é importar a identidade de utilizador administrativo da organização na carteira.

```bash
npm run importAdmin -- -o <orgName>

```
O comando acima executa a importAdmin.js para importar a identidade do utilizador administrativo para a carteira. O guião lê a identidade `<orgname>-admin.json` do administrador a partir do perfil de administrador e importa-a na carteira para executar operações de HLF.

Os scripts usam a carteira do sistema de ficheiros para armazenar as identidades. Cria uma carteira de acordo com o caminho especificado no campo ".wallet" no perfil de ligação. Por padrão, o campo ".wallet" é inicializado com `<orgname>`, o que significa que uma pasta nomeada `<orgname>` é criada no atual diretório para armazenar as identidades. Se pretender criar carteira noutro caminho, modifique o campo ".wallet" no perfil de ligação antes de executar o utilizador de administração de inscrição e quaisquer outras operações hlf.

Da mesma forma, importe a identidade do utilizador administrativo para cada organização.

Consulte a ajuda do comando para mais detalhes sobre os argumentos passados no comando.

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>Geração de identidade do utilizador

Execute os comandos abaixo na ordem dada para gerar novas identidades de utilizador para a organização HLF.

> [!NOTE]
> Antes de começar com os passos de geração de identidade do utilizador, certifique-se de que a configuração inicial da aplicação está feita.

Definir abaixo variáveis ambientais na casca de nuvem azul

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

Registe-se e inscreva-se em novo utilizador

Para registar e inscrever um novo utilizador, execute o comando abaixo que executa o registoUser.js. Guarda a identidade de utilizador gerada na carteira.

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> A identidade do utilizador da Administração é usada para emitir o comando de registo para o novo utilizador. Assim, é obrigatório ter a identidade do utilizador administrativo na carteira antes de executar este comando. Caso contrário, este comando falhará.

Consulte a ajuda do comando para mais detalhes sobre os argumentos passados no comando

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>Operações de código de corrente


> [!NOTE]
> Antes de iniciar qualquer operação de código de corrente, certifique-se de que a configuração inicial da aplicação está feita.

Definir abaixo variáveis ambientais específicas de código de corrente na concha da Nuvem Azure:

```bash
# peer organization name where chaincode is to be installed
ORGNAME=<orgName>
USER_IDENTITY="admin.$ORGNAME"
CC_NAME=<chaincodeName>
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'
# Default value is 'golang'
CC_LANG=<chaincodeLanguage>
# CC_PATH contains the path where your chaincode is place. In case of go chaincode, this path is relative to 'GOPATH'.
# For example, if your chaincode is present at path '/opt/gopath/src/chaincode/chaincode.go'.
# Then, set GOPATH to '/opt/gopath' and CC_PATH to 'chaincode'
CC_PATH=<chaincodePath>
# 'GOPATH' environment variable. This needs to be set in case of go chaincode only.
export GOPATH=<goPath>
# Channel on which chaincode is to be instantiated/invoked/queried
CHANNEL=<channelName>

````

As operações de código de corrente abaixo podem ser efetuadas:

- Instalar código de corrente
- Código de corrente instantâneo
- Invocar código de corrente
- Código de cadeia de consultas

### <a name="install-chaincode"></a>Instalar código de corrente

Execute abaixo o comando para instalar código de corrente na organização de pares.

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
Vai instalar código de corrente em todos os nós `ORGNAME` pares da organização definida na variável ambiental. Se houver duas ou mais organizações paritárias no seu canal e quiser instalar código de corrente em todas elas, execute os comandos separadamente para cada organização de pares.

Siga os passos:

- Pronto `ORGNAME` `<peerOrg1Name>` para `installCC` emitir comando.
- Pronto `ORGNAME` `<peerOrg2Name>` para `installCC` emitir comando.

  Execute-o para cada organização de pares.

Consulte a ajuda do comando para mais detalhes sobre os argumentos passados no comando.

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>Código de corrente instantâneo

Execute abaixo o comando para codificar em cadeia instantânea no par.

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
Passe o nome da função de instantânea e `<instantiateFunc>` a `<instantiateFuncArgs>` vírvia separada lista de argumentos dentro e respectivamente. Por exemplo, em [cadeia fabrcar,](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go)para instantaneamente `<instantiateFuncArgs>` o código `""`de corrente definido `<instantiateFunc>` para `"Init"` e para esvaziar a corda .

> [!NOTE]
> Execute o comando por uma vez de qualquer organização de pares no canal.
> Uma vez que a transação é submetida com sucesso ao ordenante, o ordenante distribui esta transação a todas as organizações de pares do canal. Assim, o código de corrente é instantâneo em todos os nós de pares em todas as organizações paritárias do canal.

Consulte a ajuda do comando para mais detalhes sobre os argumentos passados no comando

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>Invocar código de corrente

Execute o comando abaixo para invocar a função de código de corrente:

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
Passe o nome da função invocação e `<invokeFunction>` `<invokeFuncArgs>` a vírvia separada lista de argumentos dentro e respectivamente. Continuando com o exemplo de código de cadeia `<invokeFunction>` fabcar, para invocar a função initLedger definida para `"initLedger"` e `<invokeFuncArgs>` para `""`.

> [!NOTE]
> Execute o comando por uma vez de qualquer organização de pares no canal.
> Uma vez que a transação é submetida com sucesso ao ordenante, o ordenante distribui esta transação a todas as organizações de pares do canal. Assim, o estado mundial é atualizado em todos os nós pares de todas as organizações paritárias do canal.

Consulte a ajuda do comando para mais detalhes sobre os argumentos passados no comando

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>Código de cadeia de consultas

Executar abaixo do comando para consultar código de corrente:

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

Passe o nome da função de consulta e `<queryFunction>` `<queryFuncArgs>` a vírvia separada lista de argumentos dentro e respectivamente. Mais uma `fabcar` vez, tomando o código em cadeia `<queryFunction>` como `"queryAllCars"` `<queryArgs>` referência, para consultar todos os carros do estado mundial definidos para e para `""`.

Consulte a ajuda do comando para mais detalhes sobre os argumentos passados no comando

```bash
npm run queryCC -- -h

```
