---
title: O consórcio de malha de hiperrazãos no serviço de kubernetes do Azure (AKS)
description: Como implantar e configurar a rede do consórcio do Fabric do Microsoft Azure no serviço kubernetes
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 5aed420295fd17cf4e7b26c86e8b84c4687e6545
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029916"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>O consórcio de malha de hiperrazãos no serviço de kubernetes do Azure (AKS)

Você pode usar a HLF (malha do Microsoft) no modelo AKS (serviço kubernetes do Azure) para implantar e configurar uma rede do consórcio de malha de multirazão no Azure.

Depois de ler este artigo, irá:

- Obtenha o conhecimento prático da malha de hiperrazãor e os vários componentes que formam os blocos de construção da rede blockchain de malha do
- Saiba como implantar e configurar um consórcio de malha de multirazão no serviço kubernetes do Azure para seus cenários de produção.

## <a name="hyperledger-fabric-consortium-architecture"></a>Arquitetura do consórcio de malha de multirazãor

Para criar uma rede de malha de multirazão no Azure, você precisa implantar o serviço de pedidos e a organização com nós pares. Os diferentes componentes fundamentais que são criados como parte da implantação do modelo são:

- **Nódota :** Um nó responsável pela encomenda de transações no livro de contabilidade. Junto com outros nós, os nós ordenados formam o serviço de pedidos da rede de malha de terceiros.

- **Pares**de nós : Um nó que acolhe principalmente livros e contratos inteligentes, estes elementos fundamentais da rede.

- **Fabric CA**: Fabric CA é a Autoridade de Certificados (CA) para o Tecido Hyperledger. A AC de malha permite que você inicialize e inicie o processo do servidor que hospeda a autoridade de certificação. Ele permite que você gerencie identidades e certificados. Cada cluster AKS implantado como parte do modelo terá um pod de AC de malha por padrão.

- **CouchDB ou LevelDB**: A base de dados do Estado mundial para os nós pares pode ser armazenada em LevelDB ou CouchDB. LevelDB é o banco de dados de estado padrão incorporado no nó de par e armazena o chaincode data como pares chave-valor simples e oferece suporte somente a consultas de chave, intervalo de chaves e chave composta. CouchDB é um banco de dados de estado alternativo opcional que dá suporte a consultas avançadas quando os valores de dados chaincode são modelados como JSON.

O modelo na implantação gira vários recursos do Azure em sua assinatura. Os diferentes recursos do Azure implantados são:

- **Cluster AKS**: Cluster Azure Kubernetes que é configurado de acordo com os parâmetros de entrada fornecidos pelo cliente. O cluster AKS tem vários pods configurados para executar os componentes de rede da malha do limiar. Os pods diferentes criados são:

  - **Ferramentas de tecido**: A ferramenta de tecido é responsável pela configuração dos componentes do Tecido Hyperledger.
  - **Cápsulas de ordenamento/pares**: Os nós da rede HLF.
  - **Proxy**: Um casulo de procuração NGNIX através do qual as aplicações do cliente podem interagir com o cluster AKS.
  - **Ca de tecido**: A cápsula que executa o Tecido CA.
- **PostgreSQL**: É implementada uma instância de PostgreSQL para manter as identidades do Tecido CA.

- **Cofre chave Azure**: Uma instância chave do cofre é implementada para salvar as credenciais de Ca de tecido e os certificados de raiz fornecidos pelo cliente, que é usado em caso de remissão de modelo, isto é para lidar com a mecânica do modelo.
- **Disco gerido pelo Azure**: O disco Azure Managed destina-se a uma loja persistente para a base de dados do estado do estado do estado do estado do estado do livro e do nó de pares.
- **IP público**: Um ponto final público do cluster AKS implantado para interfaceção com o cluster.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Configuração de rede de Blockchain de malha de multirazão

Para começar, você precisa de uma assinatura do Azure que possa dar suporte à implantação de várias máquinas virtuais e contas de armazenamento padrão. Se não tiver uma subscrição Azure, pode [criar uma conta Azure gratuita.](https://azure.microsoft.com/free/)

Configure a rede Blockchain de malha do multirazão usando as seguintes etapas:

- [Implementar a organização de ordenamento/pares](#deploy-the-ordererpeer-organization)
- [Construir o consórcio](#build-the-consortium)
- [Executar operações nativas de HLF](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>Implantar a organização do solicitante/par

Para começar com a implantação de componentes de rede HLF, navegue para o [portal Azure](https://portal.azure.com). Selecione **Criar um recurso > Blockchain** > procure **hyperledger Fabric no Serviço Azure Kubernetes**.

1. Selecione **criar** para iniciar a implementação do modelo. O Tecido Create Hyperledger nos ecrãs **de serviço Azure Kubernetes.**

    ![Malha de multirazão no modelo do serviço kubernetes do Azure](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. Introduza os detalhes do projeto na página **Basics.**

    ![Malha de multirazão no modelo do serviço kubernetes do Azure](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Introduza os seguintes detalhes:
    - **Subscrição**: Escolha o nome de subscrição onde pretende implementar os componentes da rede HLF.
    - **Grupo de recursos**: Ou criar um novo grupo de recursos ou escolher um grupo de recursos vazios existente, o grupo de recursos irá deter todos os recursos implantados como parte do modelo.
    - **Região**: Escolha a região azure onde pretende implantar o cluster Azure Kubernetes para os componentes HLF. O modelo está disponível em todas as regiões em que AKS está disponível, certifique-se de escolher uma região em que sua assinatura não esteja atingindo o limite de cota da máquina virtual (VM).
    - **Prefixo de recurso**: Prefixo para nomeação de recursos que são implantados. O prefixo do recurso deve ter menos de seis caracteres e a combinação de caracteres deve incluir números e letras.
4. Selecione o separador **Definições** de Tecido para definir os componentes da rede HLF que serão implantados.

    ![Malha de multirazão no modelo do serviço kubernetes do Azure](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Introduza os seguintes detalhes:
    - **Nome**da organização : O nome da organização Fabric, que é necessário para várias operações de avião de dados.
    - **Componente de rede de tecidos:** Escolha os nós de Serviço de Encomenda ou Peer com base no componente da rede Blockchain que pretende configurar.
    - **Número de nós** - Seguem-se os dois tipos de nós:
        - Serviço de pedidos – selecione o número de nós para fornecer a tolerância a falhas para a rede. Somente 3, 5 e 7 são a contagem de nós do solicitante com suporte.
        - Nós de par-você pode escolher 1-10 nós com base em seu requisito.
    - **Base de dados do estado mundial**do nó peer : Escolha entre LevelDB e CoucbDB. Esse campo é exibido quando o usuário escolhe o nó par na lista suspensa componente de rede de malha.
    - **Nome de utilizador do tecido**: Introduza o nome de utilizador utilizado para a autenticação Fabric CA.
    - **Palavra-passe do Tecido CA**: Introduza a palavra-passe para autenticação De Tecido CA.
    - **Confirmar palavra-passe**: Confirme a palavra-passe do Tecido CA.
    - **Certificados**: Se pretender utilizar os seus próprios certificados de raiz para inicializar o Tecido CA, escolha o certificado raiz de upload para a opção Fabric CA, caso contrário, por padrão, o Fabric CA cria certificados auto-assinados.
    - **Certificado raiz**: Carregar o certificado de raiz (chave pública) com o qual o Tecido CA precisa de ser inicializado. Há suporte para certificados de formato. PEM, os certificados devem ser válidos no fuso horário UTC.
    - **Chave privada do Certificado raiz**: Faça o upload da chave privada do certificado raiz. Se você tiver um certificado. PEM, que tem a chave pública e privada combinada, carregue-o aqui também.


6. Selecione **separador Definições** de cluster AKS para definir a configuração do cluster Azure Kubernetes que é a infraestrutura subjacente à qual serão criados os componentes da rede Fabric.

    ![Malha de multirazão no modelo do serviço kubernetes do Azure](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Introduza os seguintes detalhes:
    - Nome do **cluster kubernetes**: O nome do cluster AKS que é criado. Este campo é preenchido previamente com base no prefixo de recurso fornecido, você pode alterar se necessário.
    - **Versão Kubernetes**: A versão dos Kubernetes que será implantada no cluster. Com base na região selecionada no separador **Basics,** as versões suportadas disponíveis podem ser alteradas.
    - **Prefixo DNS**: Prefixo de nome de domínio (DNS) para o cluster AKS. Você usará o DNS para se conectar à API do kubernetes ao gerenciar contêineres depois de criar o cluster.
    - **Tamanho**do nó : O tamanho do nó Kubernetes, pode escolher entre a lista da Unidade de Armazenamento de Stock VM (SKUs) disponível no Azure. Para obter um desempenho ideal, recomendamos o Standard DS3 v2.
    - **Contagem do nó**: A contagem do número de nós kubernetes a ser implantado no cluster. É recomendável manter essa contagem de nós pelo menos igual ou mais do que o número de nós HLF especificados nas configurações de malha.
    - **ID**do cliente principal de serviço : Introduza o ID do cliente de um principal de serviço existente ou crie um novo, que é necessário para a autenticação AKS. Veja, passos para [criar o diretor de serviço.](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal)
    - **Segredo**principal do cliente de serviço : Insira o segredo do cliente do principal serviço fornecido no id principal do cliente do serviço.
    - **Confirmar**o segredo do cliente : Confirme o segredo do cliente fornecido no segredo principal do cliente do serviço.
    - **Ativar a monitorização do recipiente:** Escolha ativar a monitorização AKS, que permite que os registos AKS empurrem para o espaço de trabalho de Log Analytics especificado.
    - **Log Analytics workspace**: Log analytics workspace será preenchido com o espaço de trabalho padrão que é criado se a monitorização estiver ativada.

8. Depois de fornecer todos os detalhes acima, selecione **Rever e crie** o separador. A revisão e criação desencadeia a validação dos valores que forneceu.
9. Uma vez que a validação passe, pode selecionar **criar**.
A implantação geralmente leva 10-12 minutos, pode variar dependendo do tamanho e do número de nós AKS especificados.
10. Após a implantação bem-sucedida, você será notificado por meio das notificações do Azure no canto superior direito.
11. Selecione **Ir ao grupo de recursos** para verificar todos os recursos criados como parte da implementação do modelo. Todos os nomes de recursos começarão com o prefixo fornecido na definição **Basics.**

## <a name="build-the-consortium"></a>Criar o consórcio

Para criar o blockchain Consortium após implantar o serviço de pedidos e os nós de mesmo nível, você precisa executar as etapas a seguir em sequência. **Construa** o script da Sua Rede (byn.sh), o que o ajuda a configurar o consórcio, a criar canal e a instalar código de corrente.

> [!NOTE]
> Criar seu script de rede (byn) fornecido é estritamente usado para cenários de demonstração/DevTest. Para a configuração de nível de produção, é recomendável usar as APIs nativas do HLF.

Todos os comandos para executar o script byn podem ser executados por meio da CLI (interface de linha de comando) do Azure bash. Você pode fazer logon na versão da Web do shell do Azure por meio de ![Malha de multirazão no modelo do serviço kubernetes do Azure](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) no canto superior direito do portal do Azure. No prompt de comando, digite bash e insira para mudar para a CLI do bash.

Consulte a [concha azure](https://docs.microsoft.com/azure/cloud-shell/overview) para mais informações.

![Malha de multirazão no modelo do serviço kubernetes do Azure](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Baixe o arquivo byn.sh e Fabric-admin. YAML.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/fabric-admin.yaml -o fabric-admin.yaml
```
**Definir abaixo variáveis ambientais na concha De bash Azure CLI:**

Definir informações de canal e informações da organização do solicitante

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=$(az aks show --resource-group $ORDERER_AKS_RESOURCE_GROUP --name $ORDERER_AKS_NAME --subscription $ORDERER_AKS_SUBSCRIPTION -o json | jq .addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName | tr -d '"')
ORDERER_END_POINT="orderer1.$ORDERER_DNS_ZONE:443"
CHANNEL_NAME=<channelName>
```
Definir informações da organização par

```bash
PEER_AKS_RESOURCE_GROUP=<peerAKSClusterResourceGroup>
PEER_AKS_NAME=<peerAKSClusterName>
PEER_AKS_SUBSCRIPTION=<peerAKSClusterSubscriptionID>
#Peer organization name is case-sensitive. Specify exactly the same name, which was provided while creating the Peer AKS Cluster.
PEER_ORG_NAME=<peerOrganizationName>
```

Crie um compartilhamento de arquivos do Azure para compartilhar vários certificados públicos entre organizações de mesmo nível e do solicitante.

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

Vá para a organização do orderr AKS cluster e o comando Issue para criar um novo canal

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**Comandos de Gestão de Consórcios**

Execute os comandos abaixo na ordem determinada para adicionar uma organização de mesmo nível em um canal e consórcio.

1. Vá para par organização AKS cluster e carregue seu serviço de membro fornecer (MSP) em um armazenamento de arquivos do Azure.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. Vá para a organização do orderr AKS cluster e adicione a organização par no canal e consórcio.

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. Volte para a organização par e o comando Issue para ingressar nós pares no canal.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

Da mesma forma, para adicionar mais organizações pares no canal, atualize as variáveis de ambiente AKS de mesmo nível de acordo com a organização par necessária e execute as etapas de 1 a 3.

**Comandos de gestão de códigos de corrente**

Execute o comando abaixo para executar a operação relacionada ao chaincode. Esses comandos executam todas as operações em um chaincode de demonstração. Este chaincode de demonstração tem duas variáveis "a" e "b". Na instanciação do chaincode, "a" é inicializado com 1000 e "b" é inicializado com 2000. Em cada invocação do chaincode, 10 unidades são transferidas de "a" para "b". A operação de consulta em chaincode mostra o estado mundial da variável "a".

Execute os comandos a seguir executados no cluster AKS da organização de mesmo nível.

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

## <a name="run-native-hlf-operations"></a>Executar operações HLF nativas

Para ajudar os clientes a começar a executar comandos nativos do multirazão na rede HLF no AKS. O aplicativo de exemplo é fornecido que usa o SDK do Fabric NodeJS para executar as operações de HLF. Os comandos são fornecidos para criar uma nova identidade de usuário e instalar seu próprio chaincode.

### <a name="before-you-begin"></a>Antes de começar

Siga os comandos abaixo para a configuração inicial do aplicativo:

- Baixar arquivos do aplicativo
- Gerar perfil de conexão e perfil de administrador
- Importar identidade de usuário administrador

Depois de concluir a configuração inicial, você pode usar o SDK para obter as operações abaixo:

- Geração de identidade do usuário
- Operações de Chaincode

Os comandos mencionados acima podem ser executados de Azure Cloud Shell.

### <a name="download-application-files"></a>Baixar arquivos do aplicativo

A primeira configuração para executar o aplicativo é baixar todos os arquivos de aplicativo em uma pasta.

**Criar pasta de aplicativos e introduzir na pasta:**

```bash
mkdir app
cd app
```
Execute o comando abaixo para baixar todos os arquivos e pacotes necessários:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/application/setup.sh | bash
```
Esse comando leva tempo para carregar todos os pacotes. Após a execução bem sucedida do comando, pode ver uma pasta `node_modules` no diretório atual. Todos os pacotes necessários são carregados na pasta `node_modules`.

### <a name="generate-connection-profile-and-admin-profile"></a>Gerar perfil de conexão e perfil de administrador

Crie `profile` diretório dentro da pasta `app`

```bash
cd app
mkdir ./profile
```
Definir essas variáveis de ambiente no Azure cloud Shell

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

Execute o comando abaixo para gerar o perfil de conexão e o perfil de administrador da organização

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

Criará perfis de ligação e `profile` administração da organização na pasta de perfil com nome `<orgname>-ccp.json` e `<orgname>-admin.json` respectivamente.

Da mesma forma, gere o perfil de conexão e o perfil de administrador para cada pedido e organização de mesmo nível.


### <a name="import-admin-user-identity"></a>Importar identidade de usuário administrador

A última etapa é importar a identidade do usuário administrador da organização na carteira.

```bash
npm run importAdmin -- -o <orgName>

```
O comando acima executa importAdmin. js para importar a identidade do usuário administrador para a carteira. O guião lê a identidade do administrador a partir do perfil de administração `<orgname>-admin.json` e importa-a na carteira para executar operações de HLF.

Os scripts usam a carteira do sistema de arquivos para armazenar as identidades. Ele cria uma carteira de acordo com o caminho especificado no campo ". carteira" no perfil de conexão. Por padrão, o campo ".wallet" é inicializado com `<orgname>`, o que significa que uma pasta chamada `<orgname>` é criada no atual diretório para armazenar as identidades. Se você quiser criar uma carteira em outro caminho, modifique o campo ". carteira" no perfil de conexão antes de executar registrar usuário administrador e qualquer outra operação HLF.

Da mesma forma, importe a identidade do usuário administrador para cada organização.

Consulte a ajuda do comando para obter mais detalhes sobre os argumentos passados no comando.

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>Geração de identidade do usuário

Execute os comandos abaixo na ordem fornecida para gerar novas identidades de usuário para a organização HLF.

> [!NOTE]
> Antes de começar com as etapas de geração de identidade do usuário, verifique se a configuração inicial do aplicativo está concluída.

Definir as variáveis de ambiente abaixo no Azure cloud Shell

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

Registrar e registrar novo usuário

Para registrar e registrar o novo usuário, execute o comando abaixo que executa o registerUser. js. Ele salva a identidade do usuário gerada na carteira.

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> A identidade do usuário administrador é usada para emitir o comando Register para o novo usuário. Portanto, é obrigatório ter a identidade do usuário administrador na carteira antes de executar esse comando. Caso contrário, esse comando falhará.

Consulte a ajuda do comando para obter mais detalhes sobre os argumentos passados no comando

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>Operações de Chaincode


> [!NOTE]
> Antes de começar com qualquer operação chaincode, verifique se a configuração inicial do aplicativo está concluída.

Defina as variáveis de ambiente específicas do chaincode no Azure cloud Shell:

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

As operações de chaincode abaixo podem ser executadas:

- Instalar o chaincode
- Criar instância de chaincode
- Invocar chaincode
- Chaincode de consulta

### <a name="install-chaincode"></a>Instalar o chaincode

Execute o comando abaixo para instalar o chaincode na organização par.

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
Vai instalar código de corrente em todos os nós pares da organização definidos na variável ambiente `ORGNAME`. Se houver duas ou mais organizações pares em seu canal e você quiser instalar o chaincode em todos eles, execute os comandos separadamente para cada organização par.

Siga as etapas:

- Coloque `ORGNAME` para `<peerOrg1Name>` e emitir `installCC` comando.
- Coloque `ORGNAME` para `<peerOrg2Name>` e emitir `installCC` comando.

  Execute-o para cada organização de mesmo nível.

Consulte a ajuda do comando para obter mais detalhes sobre os argumentos passados no comando.

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>Criar instância de chaincode

Execute o comando abaixo para criar uma instância de chaincode no par.

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
Passe o nome da função de instantânea e a vírvia separada lista de argumentos em `<instantiateFunc>` e `<instantiateFuncArgs>` respectivamente. Por exemplo, no código de [corrente fabrcar,](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go)para instantaneamente o código de corrente definido `<instantiateFunc>` para `"Init"` e `<instantiateFuncArgs>` a cadeia vazia `""`.

> [!NOTE]
> Execute o comando de uma vez em qualquer organização de mesmo nível no canal.
> Depois que a transação for enviada com êxito para o solicitante, o solicitante distribuirá essa transação para todas as organizações pares no canal. Portanto, o chaincode é instanciado em todos os nós de mesmo nível em todas as organizações pares no canal.

Consulte a ajuda do comando para obter mais detalhes sobre os argumentos passados no comando

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>Invocar chaincode

Execute o comando abaixo para invocar a função chaincode:

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
Passe o nome da função invocada e a vírvia separada lista de argumentos em `<invokeFunction>` e `<invokeFuncArgs>` respectivamente. Continuando com o exemplo de código de corrente fabcar, para invocar o conjunto de funções initLedger `<invokeFunction>` para `"initLedger"` e `<invokeFuncArgs>` para `""`.

> [!NOTE]
> Execute o comando de uma vez em qualquer organização de mesmo nível no canal.
> Depois que a transação for enviada com êxito para o solicitante, o solicitante distribuirá essa transação para todas as organizações pares no canal. Portanto, o estado mundial é atualizado em todos os nós pares de todas as organizações pares no canal.

Consulte a ajuda do comando para obter mais detalhes sobre os argumentos passados no comando

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>Chaincode de consulta

Execute o comando abaixo para consultar o chaincode:

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

Passe o nome da função de consulta e a vírvia separada lista de argumentos em `<queryFunction>` e `<queryFuncArgs>` respectivamente. Mais uma vez, tomando `fabcar` código de corrente como referência, para consultar todos os carros do estado mundial definido`<queryFunction>` para `"queryAllCars"` e `<queryArgs>` para `""`.

Consulte a ajuda do comando para obter mais detalhes sobre os argumentos passados no comando

```bash
npm run queryCC -- -h

```
