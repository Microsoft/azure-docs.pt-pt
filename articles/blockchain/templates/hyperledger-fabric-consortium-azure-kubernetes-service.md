---
title: Consórcio de tecido hiper-iniciante no Serviço Azure Kubernetes (AKS)
description: Como implementar e configurar a rede de consórcios Hyperledger Fabric no Serviço Azure Kubernetes
ms.date: 07/07/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 1e90eeccb015b4d5ef78b79297565ddde9cfa305
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87081287"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Consórcio de tecido hiper-iniciante no Serviço Azure Kubernetes (AKS)

Pode utilizar o tecido hiper-ledger (HLF) no modelo Azure Kubernetes Service (AKS) para implementar e configurar uma rede de consórcio de tecido hiper-ledger no Azure.

Depois de ler este artigo, irá:

- Obtenha conhecimentos de trabalho da Hyperledger Fabric e dos vários componentes que formam os blocos de construção da rede de blockchain Hyperledger Fabric.
- Saiba como implementar e configurar um consórcio de tecidos hiper-ledger no Serviço Azure Kubernetes para os seus cenários de produção.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Escolha uma solução Azure Blockchain

Antes de optar por usar um modelo de solução, compare o seu cenário com os casos de uso comum das opções disponíveis do Azure Blockchain.

Opção | Modelo de serviço | Caso de uso comum
-------|---------------|-----------------
Modelos de solução | IaaS | Os modelos de solução são modelos de Gestor de Recursos Azure que pode utilizar para providenciar uma topologia de rede blockchain totalmente configurada. Os modelos implementam e configuram os serviços de computação, networking e armazenamento do Microsoft Azure para um determinado tipo de rede blockchain. Os modelos de solução são fornecidos sem um acordo de nível de serviço. Utilize a [página de perguntas&Microsoft Q](/answers/topics/azure-blockchain-workbench.html) para suporte.
[Azure Blockchain Service](../service/overview.md) | PaaS | A antevisão do serviço Azure Blockchain simplifica a formação, gestão e governação das redes blockchain do consórcio. Utilize o Serviço Azure Blockchain para soluções que exijam paaS, gestão de consórcios ou privacidade de contratos e transações.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS e PaaS | A Azure Blockchain Workbench Preview é uma coleção de serviços e capacidades da Azure projetadas para ajudá-lo a criar e implementar aplicações blockchain para partilhar processos de negócio e dados com outras organizações. Utilize a bancada Azure Blockchain workbench para prototipar uma solução blockchain ou uma aplicação blockchain comprovativo de conceito. O Azure Blockchain Workbench é fornecido sem contrato de nível de serviço. Utilize a [página de perguntas&Microsoft Q](/answers/topics/azure-blockchain-workbench.html) para suporte.

## <a name="hyperledger-fabric-consortium-architecture"></a>Arquitetura do Consórcio de Tecidos Hiper-Ledger

Para construir a rede Hyperledger Fabric no Azure, é necessário implementar o Serviço de Encomendas e organização com nós de pares. Os diferentes componentes fundamentais que são criados como parte da implementação do modelo são:

- **Nós de encomenda:** Um nó responsável pela encomenda de transações no livro-razão. Juntamente com outros nós, os nós ordenados formam o serviço de encomenda da rede Hyperledger Fabric.

- **Nó de pares**: Um nó que acolhe principalmente livros e contratos inteligentes, estes elementos fundamentais da rede.

- **Fabric CA**: Fabric CA é a Autoridade de Certificados (CA) para o Tecido Hiper-Ledger. O Fabric CA permite-lhe inicializar e iniciar o processo do servidor que acolhe a autoridade do certificado. Permite-lhe gerir identidades e certificados. Cada cluster AKS implantado como parte do modelo terá uma cápsula Fabric CA por padrão.

- **CouchDB ou LevelDB**: A base de dados do estado mundial para os nós pares pode ser armazenada no LevelDB ou no CouchDB. LevelDB é a base de dados de estado padrão incorporada no nó de pares e armazena dados de código de corrente como simples pares de valor-chave e suporta apenas as principais consultas de chave, teclado e compósitos. CouchDB é uma base de dados alternativa opcional que suporta consultas ricas quando os valores de dados de código de corrente são modelados como JSON.

O modelo na implementação gira vários recursos Azure na sua subscrição. Os diferentes recursos Azure utilizados são:

- **Cluster AKS**: Cluster Azure Kubernetes configurado de acordo com os parâmetros de entrada fornecidos pelo cliente. O cluster AKS tem várias cápsulas configuradas para executar os componentes da rede Hyperledger Fabric. As diferentes cápsulas criadas são:

  - **Ferramentas de**tecido : A ferramenta de tecido é responsável pela configuração dos componentes do Tecido Hiper-conjugador.
  - **Cápsulas de encomenda/pares**: Os nós da rede HLF.
  - **Proxy**: Um casulo de procuração NGNIX através do qual as aplicações do cliente podem interagir com o cluster AKS.
  - **Fabric CA**: A cápsula que executa o Fabric CA.
- **PostgreSQL**: É implantado um exemplo de PostgreSQL para manter as identidades do Fabric CA.

- **Cofre da chave Azure :** É implantado um caso de cofre chave para salvar as credenciais de Fabric CA e os certificados de raiz fornecidos pelo cliente, que é usado em caso de nova execução do modelo, para lidar com a mecânica do modelo.
- **Disco gerido Azure**: O disco Azure Managed é para loja persistente para base de dados do estado do estado do livro de contabilidade e do nó de pares.
- **IP público**: Um ponto final IP público do cluster AKS implantado para a inter-consulta com o cluster.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Configuração da rede de blockchain de tecido hiper-iniciante

Para começar, precisa de uma subscrição do Azure que possa suportar a implementação de várias máquinas virtuais e contas de armazenamento padrão. Se não tiver uma subscrição do Azure, pode [criar uma conta Azure gratuita.](https://azure.microsoft.com/free/)

Configurar a rede De Blockchain de Tecido Hiper-Iniciante utilizando os seguintes passos:

- [Implementar a organização de encomendas/pares](#deploy-the-ordererpeer-organization)
- [Construir o consórcio](#build-the-consortium)

## <a name="deploy-the-ordererpeer-organization"></a>Implementar a organização de encomendas/pares

Para começar com a implantação de componentes da rede HLF, navegue até ao [portal Azure](https://portal.azure.com). Selecione **Criar um recurso > blockchain** > procurar tecido **hiper-ledger no serviço Azure Kubernetes**.

1. Selecione **criar** para iniciar a implementação do modelo. O **tecido de hiper-ledger Create no Serviço Azure Kubernetes** exibe.

    ![Tecido hiper-iniciante no modelo de serviço Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. Insira os detalhes do projeto na página **Basics.**

    ![Tecido hiper-iniciante no modelo de serviço Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Introduza os seguintes detalhes:
    - **Subscrição**: Escolha o nome de subscrição onde pretende implantar os componentes da rede HLF.
    - **Grupo de recursos**: Ou cria um novo grupo de recursos ou escolhe um grupo de recursos vazios existente, o grupo de recursos irá manter todos os recursos implantados como parte do modelo.
    - **Região**: Escolha a região de Azure onde pretende implantar o cluster Azure Kubernetes para os componentes HLF. O modelo está disponível em todas as regiões onde a AKS está disponível Certifique-se de escolher uma região onde a sua subscrição não esteja a atingir o limite de quota da Máquina Virtual (VM).
    - **Prefixo de recursos**: Prefixo para a nomeação de recursos que são implantados. O prefixo de recursos deve ter menos de seis caracteres de comprimento e a combinação de caracteres deve incluir tanto números como letras.
4. Selecione **o separador Definições de Tecido** para definir os componentes da rede HLF que serão implantados.

    ![Tecido hiper-iniciante no modelo de serviço Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Introduza os seguintes detalhes:
    - **Nome da organização**: O nome da organização Fabric, que é necessária para várias operações de data plane. O nome da organização tem de ser único por implantação.
    - **Componente de rede de**tecido : Escolha os nós de serviço de encomenda ou peer com base no componente da rede Blockchain que pretende configurar.
    - Número de nós - **Seguem-se** os dois tipos de nós:
        - Serviço de encomenda - selecione o número de nós para a tolerância a falhas fornecidas à rede. Apenas 3,5 e 7 são a contagem de nóleiro suportado.
        - Nómada de pares - pode escolher 1-10 nós com base na sua exigência.
    - **Base de dados do estado do nó de**pares: Escolha entre LevelDB e CoucbDB. Este campo é apresentado quando o utilizador escolhe o nó de pares no drop-down do componente da rede De tecido.
    - **Nome de utilizador do**tecido : Introduza o nome de utilizador utilizado para a autenticação Fabric CA.
    - **Senha ca do tecido**: Introduza a palavra-passe para autenticação Fabric CA.
    - **Confirme a palavra-passe**: Confirme a palavra-passe Fabric CA.
    - **Certificados**: Se pretender utilizar os seus próprios certificados de raiz para rubricar o Fabric CA, então escolha o certificado de raiz de upload para a opção Fabric CA, caso contrário, por defeito Fabric CA cria certificados auto-assinados.
    - **Certificado raiz**: Carregar o certificado de raiz (chave pública) com o qual o Fabric CA precisa de ser inicializado. Os certificados de formato .pem são suportados, os certificados devem ser válidos no fuso horário UTC.
    - **Chave privada do Certificado de Raiz**: Carre faça o upload da chave privada do certificado raiz. Se tiver um certificado .pem, que tem chave pública e privada combinada, faça o upload aqui também.


6. Selecione o **separador de configurações do cluster AKS** para definir a configuração do cluster Azure Kubernetes que é a infraestrutura subjacente na qual os componentes da rede Fabric serão configurados.

    ![Tecido hiper-iniciante no modelo de serviço Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Introduza os seguintes detalhes:
    - **Kubernetes nome de cluster**: O nome do cluster AKS que é criado. Este campo é pré-voizado com base no prefixo de recursos fornecido, podendo ser alterado se necessário.
    - **Versão Kubernetes**: A versão dos Kubernetes que será implantada no cluster. Com base na região selecionada no separador **Básicos,** as versões suportadas disponíveis podem mudar.
    - **Prefixo do prefixo do dnso**: Sistema de Nome de Domínio (DNS) prefixo para cluster AKS. Utilizará o DNS para ligar à API de Kubernetes ao gerir os contentores depois de criar o cluster.
    - **Tamanho do nó**: O tamanho do nó Kubernetes, pode escolher entre a lista de unidade de manutenção de stock VM (SKUs) disponível no Azure. Para um melhor desempenho, recomendamos o Standard DS3 v2.
    - **Contagem de nó:** A contagem do número de nós Kubernetes a implantar no cluster. Recomendamos manter esta contagem de nós pelo menos igual ou superior ao número de nós HLF especificados nas definições de Tecido.
    - **ID do cliente principal**do serviço : Introduza a identificação do cliente de um principal de serviço existente ou crie um novo, que é necessário para a autenticação AKS. Veja, passos para [criar o principal de serviço.](/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal)
    - **Segredo do cliente principal**do serviço : Insira o segredo do cliente do principal serviço fornecido na identificação principal do cliente de serviço.
    - **Confirme o segredo**do cliente: Confirme o segredo do cliente fornecido no segredo do cliente principal do serviço.
    - **Ativar a monitorização do contentor**: Opte por ativar a monitorização AKS, que permite que os registos AKS empurrem para o espaço de trabalho do Log Analytics especificado.
    - **Log Analytics workspace**: Log analytics workspace será povoado com o espaço de trabalho padrão que é criado se a monitorização estiver ativada.

8. Depois de fornecer todos os detalhes acima, selecione **Rever e criar** separador. A revisão e criação despoleta a validação dos valores que forneceu.
9. Uma vez que a validação passe, pode selecionar **criar**.
A implementação geralmente demora 10-12 minutos, pode variar dependendo do tamanho e número de nós AKS especificados.
10. Após a implementação bem sucedida, é notificado através de notificações do Azure no canto superior direito.
11. Selecione **Vá ao grupo de recursos** para verificar todos os recursos criados como parte da implementação do modelo. Todos os nomes dos recursos começarão com o prefixo fornecido na definição **de Básicos.**

## <a name="build-the-consortium"></a>Construir o consórcio

Para construir o post do consórcio blockchain implantando o serviço de encomenda e os nós de pares, você precisa realizar os passos abaixo em sequência. Script Azure HLF (azhlf), que o ajuda na criação do consórcio, criando canais e operações de código de corrente.

> [!NOTE]
> Existe uma atualização no script, esta atualização é para fornecer mais funcionalidade com o script Azure HLF. Se quiser se referir ao antigo roteiro, [consulte aqui.](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md) Este script é compatível com o Tecido Hiperledger na versão 2.0.0 ou acima do modelo de serviço de Azure Kubernetes. Para verificar a versão da implementação, siga os passos na [Resolução de Problemas](#troubleshoot).

> [!NOTE]
> O script Azure HLF (azhlf) fornecido é para ajudar apenas com cenários demo/DevTest. O canal e o consórcio criados por este script têm políticas básicas de HLF para simplificar o cenário demo/DevTest. Para a configuração da produção, recomendamos atualizar as políticas HLF do canal/consórcio de acordo com as necessidades de conformidade da sua organização utilizando as APIs nativas do HLF.


Todos os comandos para executar o script Azure HLF podem ser executados através da Linha de Comando Azure Bash. Interface (CLI). Você pode iniciar sessão na versão web da Azure shell através  ![Tecido hiper-iniciante no modelo de serviço Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) opção no canto superior direito do portal Azure. No pedido de comando, escreva e entre para mudar para bater CLI.

Consulte [a concha Azure](../../cloud-shell/overview.md) para mais informações.

![Tecido hiper-iniciante no modelo de serviço Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


A imagem a seguir mostra o processo passo-a-passo para construir um consórcio entre uma organização de encomendas e uma organização de pares. Os comandos detalhados para executar estes passos são capturados nas seguintes secções.

![Tecido hiper-iniciante no modelo de serviço Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

Siga os comandos abaixo para a configuração inicial da aplicação do cliente: 

1.  [Baixar ficheiros de aplicações de clientes](#download-client-application-files)
2.  [Configurar variáveis de ambiente](#setup-environment-variables)
3.  [Perfil de conexão da organização de importação, utilizador administrativo e MSP](#import-organization-connection-profile-admin-user-identity-and-msp)

Após completar a configuração inicial, pode utilizar a aplicação do cliente para obter as operações abaixo:  

- [Comandos de gestão de canais](#channel-management-commands)
- [Comandos de gestão do consórcio](#consortium-management-commands)
- [Comandos de gestão de códigos de corrente](#chaincode-management-commands)

### <a name="download-client-application-files"></a>Baixar ficheiros de aplicações de clientes

A primeira configuração é para descarregar os ficheiros de aplicação do cliente. Execute abaixo o comando para descarregar todos os ficheiros e pacotes necessários:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup

```
Estes comandos clonarão o código de aplicação do cliente Azure HLF do repo público do GitHub, seguido de carregar todos os pacotes npm dependentes. Após a execução bem sucedida do comando, pode ver uma pasta node_modules no diretório atual. Todas as embalagens necessárias são carregadas na pasta node_modules.


### <a name="setup-environment-variables"></a>Configurar variáveis de ambiente

> [!NOTE]
> Todas as variáveis ambientais seguem a convenção de nomeação de recursos Azure.


**Definir abaixo variáveis ambientais para cliente de organização de encomendas**


```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```
**Definir as variáveis de ambiente abaixo para cliente de organização de pares**

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

> [!NOTE]
> Com base no número de Peer Orgs no seu consórcio, poderá ser-lhe exigido que repita os comandos Peer e desaprote o ambiente em conformidade.

**Definir as variáveis ambientais abaixo para a criação da conta de armazenamento Azure**

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Siga abaixo os passos para a criação de conta Azure Storage. Se já tem a conta Azure Storage criada, ignore estes passos

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Siga abaixo os passos para uma criação de ações de ficheiro na conta de Armazenamento Azure. Se já tem uma partilha de ficheiros criada, ignore estes passos

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Siga os passos abaixo para gerar a cadeia de ligação de partilha de ficheiros Azure

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-organization-connection-profile-admin-user-identity-and-msp"></a>Perfil de conexão da organização de importação, identidade do utilizador administrada e MSP

Emite abaixo os comandos para obter o perfil de conexão da organização, a identidade do utilizador administrativo e o MSP do Azure Kubernetes Cluster e armazenar estas identidades na loja local de aplicação do cliente, ou seja, no diretório "azhlfTool/stores".

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
> Antes de iniciar qualquer operação de canal, certifique-se de que a configuração inicial da aplicação do cliente está feita.  

Seguem-se os dois comandos de gestão de canais:

1. [Criar comando de canal](#create-channel-command)
2. [Definição do(s) comando de pares de âncora](#setting-anchor-peers-command)


#### <a name="create-channel-command"></a>Criar comando de canal

Do cliente da organização de encomendas, emita comando para criar um novo canal. Este comando criará um canal com apenas organização de ordenados nele.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

#### <a name="setting-anchor-peers-command"></a>Definição do(s) comando de pares de âncora
Do cliente da organização de pares, emita abaixo o comando para definir os pares de âncora para a organização de pares no canal especificado.

>[!NOTE]
> Antes de executar este comando, certifique-se de que a organização de pares é adicionada no canal usando comandos de gestão do Consórcio.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY
```

`<anchorPeersList>`é uma lista de pares separados de espaço para ser definido como um par de âncora. Por exemplo,

  - Desacordo `<anchorPeersList>` como "peer1" se quiser definir apenas o nó peer1 como par de âncora.
  - Definir `<anchorPeersList>` como "peer1" "peer3" se quiser definir o nó peer1 e peer3 como par de âncora.

### <a name="consortium-management-commands"></a>Comandos de gestão do consórcio

>[!NOTE]
> Antes de iniciar qualquer operação de consórcio, certifique-se de que a configuração inicial da aplicação do cliente está feita.  

Execute abaixo os comandos na ordem dada para adicionar uma organização de pares em um canal e consórcio
1.  Do cliente da organização de pares, carrema organização de pares MSP no Azure Storage

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  Do cliente da organização orderer, baixe a organização de pares MSP da Azure Storage e em seguida emita comando para adicionar organização de pares no canal/consórcio.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  Do cliente da organização do ordenador, faça upload do perfil de ligação do ordenador no Azure Storage para que a organização de pares possa ligar-se aos nosdes do ordenador usando este perfil de conexão

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  Do cliente da organização de pares, descarregue o perfil de conexão do ordenador a partir do Azure Storage e em seguida emita comando para adicionar nós de pares no canal

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

Da mesma forma, para adicionar mais organizações de pares no canal, atualizar as variáveis do ambiente entre pares de acordo com a organização de pares necessária e executar os passos 1 a 4.


### <a name="chaincode-management-commands"></a>Comandos de gestão de códigos de corrente

>[!NOTE]
> Antes de iniciar qualquer operação de código de corrente, certifique-se de que a configuração inicial da aplicação do cliente está feita.  

**Definir as variáveis de ambiente específicas de código de corrente abaixo**

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
- [Código de cadeia de consulta](#query-chaincode)


### <a name="install-chaincode"></a>Instalar código de corrente  

Execute abaixo o comando para instalar o código de corrente na organização de pares.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
Instalará o código de corrente em todos os nós pares da organização de pares definidos na variável ambiente ORGNAME. Se existirem duas ou mais organizações de pares no seu canal e pretender instalar o código de corrente em todas elas, execute este comando separadamente para cada organização de pares.  

Siga os passos:  

1.  Desaveie `ORGNAME` e de acordo com o `USER_IDENTITY` peerOrg1 e emita `./azhlf chaincode install` comando.  
2.  Desaveie `ORGNAME` e de acordo com o `USER_IDENTITY` peerOrg2 e emita `./azhlf chaincode install` comando.  

### <a name="instantiate-chaincode"></a>Código de corrente instantâneo  

A partir da aplicação do cliente por pares, execute abaixo do comando para instantanear o código de corrente no canal.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>  
```
Passe o nome da função instantânea e o espaço separados lista de argumentos dentro `<instantiateFunc>` e `<instantiateFuncArgs>` respectivamente. Por exemplo, em chaincode_example02.go chaincode, para instantaneaizar o código de corrente definido `<instantiateFunc>` para `init` e para `<instantiateFuncArgs>` "a" "2000" "b" "1000".

> [!NOTE]
> Execute o comando pela uma vez de qualquer organização de pares no canal. Uma vez que a transação é submetida com sucesso ao ordenante, o ordenante distribui esta transação a todas as organizações de pares do canal. Assim, o código de corrente é instantâneo em todos os nós de pares em todas as organizações de pares do canal.  


### <a name="invoke-chaincode"></a>Invocar código de corrente  

A partir do cliente da organização de pares, execute o comando abaixo para invocar a função de código de corrente:  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

Passe invocar o nome da função e o espaço separados lista de argumentos dentro  `<invokeFunction>`   e  `<invokeFuncArgs>`   respectivamente. Continuando com o exemplo de código de cadeia chaincode_example02.go, para executar a operação de invocação definida  `<invokeFunction>`   para e para  `invoke`    `<invokeFuncArgs>`   "a" "b" "10".  

>[!NOTE]
> Execute o comando pela uma vez de qualquer organização de pares no canal. Uma vez que a transação é submetida com sucesso ao ordenante, o ordenante distribui esta transação a todas as organizações de pares do canal. Assim, o estado mundial é atualizado em todos os nós de todos os pares de todas as organizações de pares no canal.  


### <a name="query-chaincode"></a>Código de cadeia de consulta  

Execute abaixo o comando para consultar o código de corrente:  

```bash
./azhlf chaincode query -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs>  
```
Passe nome da função de consulta e espaço separado lista de argumentos dentro  `<queryFunction>`   e  `<queryFuncArgs>`   respectivamente. Mais uma vez, tomando chaincode_example02.go chaincode como referência, ao valor de consulta de "a" no estado mundial definido  `<queryFunction>`   para e para  `query`  `<queryArgs>` "a".  

## <a name="troubleshoot"></a>Resolução de Problemas

**Para verificar a versão do modelo de execução**

Execute os comandos abaixo para encontrar a versão da sua implementação do modelo.

Coloque abaixo as variáveis ambientais de acordo com o grupo de recursos onde o modelo foi implementado.

```bash

SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
AKS_CLUSTER_SUBSCRIPTION=<AKSClusterSubscriptionID>
AKS_CLUSTER_RESOURCE_GROUP=<AKSClusterResourceGroup>
AKS_CLUSTER_NAME=<AKSClusterName>
```
Corra abaixo do comando para imprimir a versão do modelo
```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3

```

## <a name="support-and-feedback"></a>Suporte e comentários

Para notícias da Azure Blockchain, visite o [blog Azure Blockchain](https://azure.microsoft.com/blog/topics/blockchain/) para se manter atualizado sobre ofertas de serviços blockchain e informações da equipa de engenharia Azure Blockchain.

Para fornecer feedback do produto ou para solicitar novas funcionalidades, publicar ou votar por uma ideia através do [fórum de feedback Azure para blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Suporte da comunidade

Envolva-se com engenheiros da Microsoft e especialistas da comunidade Azure Blockchain.

- [Microsoft Q&Uma página de perguntas](/answers/topics/azure-blockchain-workbench.html). O suporte de engenharia para modelos blockchain está limitado a problemas de implantação.
- [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
