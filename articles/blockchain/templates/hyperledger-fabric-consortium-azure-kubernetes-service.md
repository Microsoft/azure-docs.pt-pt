---
title: Consórcio Hyperledger Fabric no Serviço Azure Kubernetes (AKS)
description: Como implantar e configurar a rede de consórcio hyperledger Fabric no Serviço Azure Kubernetes
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: da4ec99f1b9d73ab67a2312094feaa1a89aee394
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980235"
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

- **Cofre chave Azure**: Uma instância chave do cofre é implementada para salvar as credenciais de Ca de tecido e os certificados de raiz fornecidos pelo cliente, que é usado em caso de remissão de modelo, para lidar com a mecânica do modelo.
- **Disco gerido pelo Azure**: O disco Azure Managed destina-se a uma loja persistente para a base de dados do estado do estado do estado do estado do estado do livro e do nó de pares.
- **IP público**: Um ponto final público do cluster AKS implantado para interfaceção com o cluster.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Configuração da rede Hyperledger Fabric Blockchain

Para começar, precisa de uma subscrição Azure que possa suportar a implementação de várias máquinas virtuais e contas de armazenamento padrão. Se não tiver uma subscrição Azure, pode [criar uma conta Azure gratuita.](https://azure.microsoft.com/free/)

Configurar a rede Hyperledger Fabric Blockchain utilizando os seguintes passos:

- [Implementar a organização de ordenamento/pares](#deploy-the-ordererpeer-organization)
- [Construir o consórcio](#build-the-consortium)

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
    - **Certificados**: Se pretender utilizar os seus próprios certificados de raiz para inicializar o Tecido CA, escolha o certificado raiz de upload para a opção Fabric CA, caso contrário, por padrão, a Fabric CA cria certificados auto-assinados.
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

Para construir o post do consórcio blockchain implantando o serviço de encomenda e os nós de pares, você precisa realizar os passos abaixo em sequência. O script Azure HLF (azhlf), que o ajuda a criar o consórcio, criando operações de canal e código de corrente.

> [!NOTE]
> Existe uma atualização no script, esta atualização é para fornecer mais funcionalidade com script Azure HLF. Se quiser se referir ao velho guião, [consulte aqui.](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md) Este script é compatível com Hyperledger Fabric na versão modelo de serviço Azure Kubernetes 2.0.0 ou superior. Para verificar a versão da implementação, siga os passos em [Troubleshoot](#troubleshoot).

> [!NOTE]
> O script Azure HLF (azhlf) fornecido é para ajudar apenas com cenários de demonstração/DevTest. Canal e consórcio criados por este script tem políticas básicas de HLF para simplificar o cenário de demonstração/DevTest. Para a configuração da produção, recomendamos atualizar as políticas de HLF de canal/consórcio de acordo com as necessidades de conformidade da sua organização utilizando as APIs nativas do HLF.


Todos os comandos para executar o script Azure HLF podem ser executados através da Linha de Comando Azure Bash. Interface (CLI). Pode iniciar sessão na versão web da shell Do Azure através  ![Tecido de hiper-ledger no modelo de serviço Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) opção no canto superior direito do portal Azure. No pedido de comando, digite a bata e introduza para mudar para bater CLI.

Consulte a [concha azure](https://docs.microsoft.com/azure/cloud-shell/overview) para mais informações.

![Tecido de hiper-ledger no modelo de serviço Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


A imagem seguinte mostra o processo passo a passo para construir um consórcio entre uma organização de ordenamento e uma organização de pares. Os comandos detalhados para executar estes passos são capturados nas seguintes secções.

![Tecido de hiper-ledger no modelo de serviço Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

Siga os comandos abaixo para a configuração inicial da aplicação do cliente: 

1.  [Descarregue os ficheiros de aplicação do cliente](#download-client-application-files)
2.  [Configurar variáveis de ambiente](#setup-environment-variables)
3.  [Perfil de conexão de organização de importação, utilizador administrativo e MSP](#import-organization-connection-profile-admin-user-identity-and-msp)

Após a conclusão da configuração inicial, pode utilizar a aplicação do cliente para realizar as operações abaixo:  

- [Comandos de gestão de canais](#channel-management-commands)
- [Comandos de gestão de consórcios](#consortium-management-commands)
- [Comandos de gestão de códigos de corrente](#chaincode-management-commands)

### <a name="download-client-application-files"></a>Descarregue os ficheiros de aplicação do cliente

A primeira configuração é descarregar os ficheiros de aplicação do cliente. Execute abaixo o comando para descarregar todos os ficheiros e pacotes necessários:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup

```
Estes comandos clonam o código de aplicação do cliente Azure HLF do repo público gitHub seguido de carregar todos os pacotes npm dependentes. Após a execução bem sucedida do comando, pode ver uma pasta node_modules no diretório atual. Todos os pacotes necessários são carregados na pasta node_modules.


### <a name="setup-environment-variables"></a>Configurar variáveis de ambiente

> [!NOTE]
> Todas as variáveis ambientais seguem a convenção de nomeação de recursos Azure.


**Definir abaixo variáveis ambientais para cliente de organização de ordenadores**


```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```
**Descontete as variáveis ambientais abaixo para o cliente da organização peer**

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

> [!NOTE]
> Com base no número de Peer Orgs no seu consórcio, pode ser obrigado a repetir os comandos peer e definir a variável ambiental em conformidade.

**Definir as variáveis ambientais abaixo para a criação de uma conta de armazenamento azul**

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Siga abaixo os passos para a criação de conta de armazenamento Azure. Se já tem uma conta de armazenamento azul criada, ignore estes passos

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Siga abaixo os passos para uma criação de partilha de ficheiros na conta de armazenamento azul. Se já tem uma parte de arquivo criada, ignore estes passos

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Siga abaixo os passos para gerar a cadeia de conexão de partilha de ficheiros Azure

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-organization-connection-profile-admin-user-identity-and-msp"></a>Perfil de conexão da organização de importação, identidade do utilizador administrativo e MSP

Emissão abaixo comandos para obter o perfil de conexão da organização, identidade de utilizador administrativo, e MSP do Cluster Azure Kubernetes e armazenar essas identidades na aplicação de cliente loja local ou seja, no diretório "azhlfTool/stores".

Para a organização do ordenador:

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

Para organização de pares:

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="channel-management-commands"></a>Comandos de gestão de canais

> [!NOTE]
> Antes de iniciar qualquer operação do canal, certifique-se de que a configuração inicial da aplicação do cliente está feita.  

Seguem-se os dois comandos de gestão de canais:

1. [Criar o comando do canal](#create-channel-command)
2. [Definir comando de pares de âncora](#setting-anchor-peers-command)


#### <a name="create-channel-command"></a>Criar o comando do canal

Do cliente da organização do ordenador, emita comando para criar um novo canal. Este comando criará um canal com apenas uma organização de ordenamento.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

#### <a name="setting-anchor-peers-command"></a>Definir comando de pares de âncora
Do cliente da organização peer, emissão abaixo do comando para definir par de âncora(s) para a organização de pares no canal especificado.

>[!NOTE]
> Antes de executar este comando, certifique-se de que a organização de pares é adicionada no canal usando comandos de gestão do Consórcio.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY
```

`<anchorPeersList>`é uma lista separada do espaço de substantivos pares a definir como um par âncora. Por exemplo,

  - Coloque `<anchorPeersList>` como "peer1" se quiser definir apenas o nó de pares1 como par de âncora.
  - Coloque `<anchorPeersList>` como "peer1" "peer3" se quiser definir o nó de peer1 e peer3 como par de âncora.

### <a name="consortium-management-commands"></a>Comandos de gestão de consórcios

>[!NOTE]
> Antes de iniciar qualquer operação do consórcio, certifique-se de que a configuração inicial da aplicação do cliente está feita.  

Execute abaixo comandos na ordem dada para adicionar uma organização de pares em um canal e consórcio
1.  Do cliente da organização peer, upload peer organization MSP no armazenamento azul

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  Do cliente da organização do ordenador, descarregue a organização de pares MSP do armazenamento azure e, em seguida, emita comando para adicionar organização de pares no canal/consórcio.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  Do cliente da organização do ordenador, o perfil de conexão do ordenador de upload no armazenamento azure para que a organização de pares possa ligar-se aos nós de ordenamento usando este perfil de ligação

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  Do cliente da organização peer, descarregue o perfil de conexão do ordenador do armazenamento azul e, em seguida, emita comando para adicionar nós pares no canal

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

Da mesma forma, para adicionar mais organizações de pares no canal, atualizar variáveis ambientais peer conforme a organização de pares necessária e executar os passos 1 a 4.


### <a name="chaincode-management-commands"></a>Comandos de gestão de códigos de corrente

>[!NOTE]
> Antes de iniciar qualquer operação de código de corrente, certifique-se de que a configuração inicial da aplicação do cliente está feita.  

**Definir as variáveis ambientais específicas do código de corrente abaixo**

```bash
# peer organization name where chaincode operation is to be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is place.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode is to be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

As operações de código de corrente abaixo podem ser efetuadas:  

- [Instalar código de corrente](#install-chaincode)  
- [Código de corrente instantâneo](#instantiate-chaincode)  
- [Invocar código de corrente](#invoke-chaincode)
- [Código de cadeia de consultas](#query-chaincode)


### <a name="install-chaincode"></a>Instalar código de corrente  

Execute abaixo o comando para instalar código de corrente na organização de pares.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
Irá instalar código de corrente em todos os nós pares da organização peer definida na variável ambiente ORGNAME. Se houver duas ou mais organizações paritárias no seu canal e quiser instalar código de corrente em todas elas, execute este comando separadamente para cada organização de pares.  

Siga os passos:  

1.  Definir `ORGNAME` `USER_IDENTITY` e como peerOrg1 `./azhlf chaincode install` e emitir comando.  
2.  Definir `ORGNAME` `USER_IDENTITY` e como peerOrg2 `./azhlf chaincode install` e emitir comando.  

### <a name="instantiate-chaincode"></a>Código de corrente instantâneo  

Desde a aplicação do cliente peer, execute abaixo do comando até o código de cadeia instantâneo no canal.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>  
```
Passe o nome da função de instantânea `<instantiateFunc>` e `<instantiateFuncArgs>` o espaço separado separou a lista de argumentos dentro e respectivamente. Por exemplo, em chaincode_example02.go chaincode, para `<instantiateFunc>` instantaneamente o código de corrente definido para `init`e `<instantiateFuncArgs>` para "a" "2000" "b" "1000".

> [!NOTE]
> Execute o comando por uma vez de qualquer organização de pares no canal. Uma vez que a transação é submetida com sucesso ao ordenante, o ordenante distribui esta transação a todas as organizações de pares do canal. Assim, o código de corrente é instantâneo em todos os nós de pares em todas as organizações paritárias do canal.  


### <a name="invoke-chaincode"></a>Invocar código de corrente  

A partir de cliente de organização de pares, execute o comando abaixo para invocar a função de código de corrente:  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

Passe invocar nome de função e `<invokeFunction>` lista `<invokeFuncArgs>` de argumentos separados dentro e respectivamente. Continuando com o exemplo de código de cadeia `<invokeFunction>`  `invoke` chaincode_example02.go, para executar operação invocada definida para e `<invokeFuncArgs>` para "a" "b" "10".  

>[!NOTE]
> Execute o comando por uma vez de qualquer organização de pares no canal. Uma vez que a transação é submetida com sucesso ao ordenante, o ordenante distribui esta transação a todas as organizações de pares do canal. Assim, o estado mundial é atualizado em todos os nós pares de todas as organizações paritárias do canal.  


### <a name="query-chaincode"></a>Código de cadeia de consultas  

Executar abaixo do comando para consultar código de corrente:  

```bash
./azhlf chaincode query -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs>  
```
Passe o nome da função de consulta `<queryFunction>`  `<queryFuncArgs>` e a lista de argumentos separados dentro e respectivamente. Mais uma vez, tomando chaincode_example02.go chaincode como referência, para `<queryFunction>`  `query` consulta `<queryArgs>` do valor de "a" no estado mundial definido para e para "a".  

## <a name="troubleshoot"></a>Resolução de problemas

**Para verificar a versão do modelo de execução**

Execute os comandos abaixo para encontrar a versão da implementação do seu modelo.

Definir abaixo variáveis ambientais de acordo com o grupo de recursos onde o modelo foi implantado.

```bash

SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
AKS_CLUSTER_SUBSCRIPTION=<AKSClusterSubscriptionID>
AKS_CLUSTER_RESOURCE_GROUP=<AKSClusterResourceGroup>
AKS_CLUSTER_NAME=<AKSClusterName>
```
Correr abaixo do comando para imprimir a versão do modelo
```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3

```
