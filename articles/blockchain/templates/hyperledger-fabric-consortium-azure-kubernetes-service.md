---
title: Implementar consórcio de tecidos hiper-iniciantes no serviço Azure Kubernetes
description: Como implementar e configurar uma rede de consórcios Hyperledger Fabric no Serviço Azure Kubernetes
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3, devx-track-azurecli
ms.openlocfilehash: 03f19d1922c011c1b5304b66488e9fa8de703bf9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478322"
---
# <a name="deploy-hyperledger-fabric-consortium-on-azure-kubernetes-service"></a>Implementar consórcio de tecidos hiper-iniciantes no serviço Azure Kubernetes

Pode utilizar o tecido hiper-ledger no modelo Azure Kubernetes Service (AKS) para implementar e configurar uma rede de consórcios Hyperledger Fabric no Azure.

Depois de ler este artigo, irá:

- Tenha um conhecimento funcional da Hyperledger Fabric e dos componentes que formam os blocos de construção de uma rede de blockchain Hyperledger Fabric.
- Saiba como implementar e configurar uma rede de consórcios Hyperledger Fabric no Serviço Azure Kubernetes para os seus cenários de produção.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Escolha uma solução Azure Blockchain

Antes de optar por utilizar um modelo de solução, compare o seu cenário com os casos de utilização comum das opções disponíveis do Azure Blockchain:

Opção | Modelo de serviço | Caso de uso comum
-------|---------------|-----------------
Modelos de solução | IaaS | Os modelos de solução são modelos do Gestor de Recursos Azure que pode utilizar para providenciar uma topologia de rede blockchain totalmente configurada. Os modelos implementam e configuram os serviços de computação, networking e armazenamento do Microsoft Azure para um tipo de rede blockchain. Os modelos de solução são fornecidos sem um acordo de nível de serviço. Utilize a [página microsoft Q&A](/answers/topics/azure-blockchain-workbench.html) para suporte.
[Azure Blockchain Service](../service/overview.md) | PaaS | A antevisão do serviço Azure Blockchain simplifica a formação, gestão e governação das redes blockchain do consórcio. Utilize o Serviço Azure Blockchain para soluções que exijam PaaS, gestão de consórcios ou privacidade de contratos e transações.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS e PaaS | A Azure Blockchain Workbench Preview é uma coleção de serviços e capacidades da Azure que o ajudam a criar e implementar aplicações blockchain para partilhar processos de negócio e dados com outras organizações. Utilize a Azure Blockchain Workbench para prototipar uma solução blockchain ou uma prova de conceito para uma aplicação blockchain. A Azure Blockchain Workbench é fornecida sem um acordo de nível de serviço. Utilize a [página microsoft Q&A](/answers/topics/azure-blockchain-workbench.html) para suporte.

## <a name="deploy-the-orderer-and-peer-organization"></a>Implementar a organização de encomendas e pares

Para começar, precisa de uma subscrição do Azure que possa suportar a implementação de várias máquinas virtuais e contas de armazenamento padrão. Se não tiver uma subscrição do Azure, pode [criar uma conta Azure gratuita.](https://azure.microsoft.com/free/)

Para começar com a implantação de componentes de rede Hyperledger Fabric, aceda ao [portal Azure](https://portal.azure.com).

1. Selecione **Criar um blockchain de recurso**  >  e, em seguida, procurar por **Tecido Hiper-ledger no Serviço Azure Kubernetes (pré-visualização)**.

2. Insira os detalhes do projeto no separador **Básicos.**

    ![Screenshot que mostra o separador Básicos.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Introduza os seguintes detalhes:
    - **Subscrição**: Escolha o nome de subscrição onde pretende implantar os componentes da rede Hyperledger Fabric.
    - **Grupo de recursos**: Ou cria um novo grupo de recursos ou escolhe um grupo de recursos vazios existente. O grupo de recursos irá deter todos os recursos implantados como parte do modelo.
    - **Região**: Escolha a região de Azure onde pretende implantar o cluster de Serviço Azure Kubernetes para os componentes do Tecido Hiperledger. O modelo está disponível em todas as regiões onde a AKS está disponível. Escolha uma região onde a sua subscrição não esteja a atingir o limite de quota da máquina virtual (VM).
    - **Prefixo de recursos**: Introduza um prefixo para a nomeação dos recursos que são utilizados. Deve ter menos de seis caracteres, e a combinação de caracteres deve incluir tanto números como letras.
4. Selecione o separador **definições de Tecido** para definir os componentes da rede Hyperledger Fabric que serão implantados.

    ![Screenshot que mostra o separador de definições de Tecido.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Introduza os seguintes detalhes:
    - **Nome da organização**: Introduza o nome da organização Hyperledger Fabric, que é necessária para várias operações de data plane. O nome da organização tem de ser único por implantação.
    - **Componente de rede de** tecido : Escolha o **serviço de encomenda** ou os **nós peer,** com base no componente da rede blockchain que pretende configurar.
    - Número de nós : **Seguem-se** os dois tipos de nós:
        - **Serviço de encomenda**: Nó responsáveis pela encomenda de transações no livro-razão. Selecione o número de nós para fornecer tolerância a falhas à rede. A contagem de nós de ordem apoiada é 3, 5 e 7.
        - **Nó de pares**: Nós que acolhem livros e contratos inteligentes. Pode escolher 1 a 10 nós com base na sua exigência.
    - **Base de dados do estado do nó de pares**: Bases de dados do Estado mundial para os nós dos pares. LevelDB é a base de dados de estado padrão incorporada no nó de pares. Armazena dados de código de corrente como simples pares chave/valor e suporta chaves, gama de chaves e consultas de chaves compostas apenas. CouchDB é uma base de dados alternativa opcional que suporta consultas ricas quando os valores de dados de código de corrente são modelados como JSON. Este campo é apresentado quando escolhe **nós Peer** na lista de drop-down de componentes da rede **De Tecido.**
    - **Nome de utilizador Fabric CA**: A autoridade do certificado de tecido permite-lhe inicializar e iniciar um processo de servidor que acolhe a autoridade do certificado. Permite-lhe gerir identidades e certificados. Cada cluster AKS implantado como parte do modelo terá uma cápsula Fabric CA por padrão. Introduza o nome de utilizador utilizado para a autenticação fabricac ca.
    - **Senha ca do tecido**: Introduza a palavra-passe para autenticação Fabric CA.
    - **Confirme a palavra-passe**: Confirme a palavra-passe Fabric CA.
    - **Certificados**: Se pretender utilizar os seus próprios certificados de raiz para rubricar o Fabric CA, escolha o certificado de raiz upload para a opção **Fabric CA.** Caso contrário, o Fabric CA cria certificados auto-assinados por padrão.
    - **Certificado raiz**: Carre faça o upload do certificado de raiz (chave pública) com o qual o Fabric CA precisa de ser inicializado. Os certificados de formato .pem são suportados. Os certificados devem ser válidos e num fuso horário UTC.
    - **Chave privada do Certificado de Raiz**: Carre faça o upload da chave privada do certificado raiz. Se tiver um certificado .pem, que tem uma chave combinada pública e privada, faça o upload aqui também.


6. Selecione o **separador definições do cluster AKS** para definir a configuração do cluster do serviço Azure Kubernetes. O cluster AKS tem várias cápsulas configuradas para executar os componentes da rede Hyperledger Fabric. Os recursos Azure implantados são:

    - **Ferramentas de** tecido : Ferramentas responsáveis pela configuração dos componentes do Tecido Hiper-conjugador.
    - **Cápsulas de encomenda/pares**: Os nós da rede Hyperledger Fabric.
    - **Proxy**: Um casulo de procuração NGNIX através do qual as aplicações do cliente podem comunicar com o cluster AKS.
    - **Fabric CA**: A cápsula que executa o Fabric CA.
    - **PostgreSQL**: Caso de base de dados que mantém as identidades da Fabric CA.
    - **Cofre chave**: Exemplo do serviço Azure Key Vault que está implantado para salvar as credenciais de Fabric CA e os certificados de raiz fornecidos pelo cliente. O cofre é utilizado em caso de nova operação de implantação do modelo, para manusear a mecânica do modelo.
    - **Disco gerido**: Exemplo do serviço Azure Managed Disks que fornece uma loja persistente para o livro de contabilidade e para a base de dados do estado mundial do nó de pares.
    - **IP público**: Ponto final do cluster AKS implantado para comunicação com o cluster.

    Introduza os seguintes detalhes: 

    ![Screenshot que mostra o separador de definições do cluster A K S.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

    - **Nome do cluster Kubernetes**: Mude o nome do cluster AKS, se necessário. Este campo é pré-voizado com base no prefixo de recursos fornecido.
    - **Versão Kubernetes**: Escolha a versão de Kubernetes que será implantada no cluster. Com base na região que selecionou no separador **Básicos,** as versões suportadas disponíveis podem mudar.
    - **Prefixo DNS**: Introduza um prefixo de nome do Sistema de Nome de Domínio (DNS) para o cluster AKS. Utilizará o DNS para ligar à API de Kubernetes ao gerir os contentores depois de criar o cluster.
    - **Tamanho do nó**: Para o tamanho do nó Kubernetes, pode escolher entre a lista de unidades de armazenamento VM (SKUs) disponíveis no Azure. Para um melhor desempenho, recomendamos o Standard DS3 v2.
    - **Contagem de nó:** Introduza o número de nós Kubernetes a serem implantados no cluster. Recomendamos manter esta contagem de nós igual ou superior ao número de nós de tecido hiper-ledger especificados no **separador definições de Tecido.**
    - **ID do cliente principal do serviço**: Introduza a identificação do cliente de um principal de serviço existente ou crie uma nova. É necessário um principal de serviço para a autenticação AKS. Consulte os [passos para criar um principal de serviço.](/powershell/azure/create-azure-service-principal-azureps#create-a-service-principal)
    - **Segredo do cliente principal** do serviço : Insira o segredo do cliente do principal serviço fornecido na identificação do cliente para o principal serviço.
    - **Confirme o segredo** do cliente: Confirme o segredo do cliente para o diretor de serviço.
    - **Ativar a monitorização do contentor**: Opte por ativar a monitorização AKS, que permite que os registos AKS empurrem para o espaço de trabalho especificado do Log Analytics.
    - **Log Analytics workspace**: O espaço de trabalho Log Analytics será preenchido com o espaço de trabalho predefinido que é criado se a monitorização estiver ativada.

8. Selecione o **separador 'Rever's e criar** o separador. Este passo desencadeia a validação dos valores que forneceu.
9. Após os passes de validação, **selecione Criar**.

    A colocação geralmente demora 10 a 12 minutos. O tempo pode variar dependendo do tamanho e número de nós AKS especificados.
10. Após uma implementação bem sucedida, é notificado através de notificações do Azure no canto superior direito.
11. Selecione **Vá ao grupo de recursos** para verificar todos os recursos criados como parte da implementação do modelo. Todos os nomes dos recursos começarão com o prefixo fornecido no **separador Básicos.**

## <a name="build-the-consortium"></a>Construir o consórcio

Para construir o consórcio blockchain depois de implantar o serviço de encomenda e os nós de pares, efetuar os seguintes passos em sequência. O script Azure Hyperledger Fabric (azhlf) ajuda-o a criar o consórcio, a criar o canal e a realizar operações de código de corrente.

> [!NOTE]
> O script Azure Hyperledger Fabric (azhlf) foi atualizado para fornecer mais funcionalidade. Se quiser se referir ao antigo script, consulte a [leitura no GitHub.](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md) Este script é compatível com o Tecido Hiperledger na versão 2.0.0 e posterior do modelo de serviço de Azure Kubernetes. Para verificar a versão da implementação, siga os passos na [Resolução de Problemas](#troubleshoot).

> [!NOTE]
> O script é fornecido para ajudar apenas com cenários de demonstração, desenvolvimento e teste. O canal e consórcio que este script cria tem políticas básicas de Hyperledger Fabric para simplificar demo, dev e cenários de teste. Para a configuração da produção, recomendamos atualizar as políticas de tecido hiperledger/canal/consórcio de acordo com as necessidades de conformidade da sua organização, utilizando as APIs de Tecido Hiperledger nativo.


Todos os comandos para executar o script Azure Hyperledger Fabric podem ser executados através da interface de linha de comando Azure Bash (CLI). Pode iniciar sôms na Azure Cloud Shell através da ![ opção Hyperledger Fabric on Azure Kubernetes Service Template ](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) no canto superior direito do portal Azure. Na solicitação de comando, `bash` escreva e selecione a tecla 'Entrar' para mudar para o CLI bash ou selecione **Bash** na barra de ferramentas Cloud Shell.

Consulte [a Azure Cloud Shell](../../cloud-shell/overview.md) para obter mais informações.

![Screenshot que mostra comandos em Azure Cloud Shell.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


A imagem a seguir mostra o processo passo-a-passo para construir um consórcio entre uma organização de encomendas e uma organização de pares. As seguintes secções mostram comandos detalhados para completar estes passos.

![Diagrama do processo para construir um consórcio.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

Depois de terminar a configuração inicial, utilize a aplicação do cliente para realizar as seguintes operações:

- Gestão de canais
- Gestão do consórcio
- Gestão de códigos de corrente

### <a name="download-client-application-files"></a>Baixar ficheiros de aplicações de clientes

A primeira configuração é para descarregar os ficheiros de aplicação do cliente. Executar os seguintes comandos para descarregar todos os ficheiros e pacotes necessários:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

Estes comandos clonarão o código de aplicação do cliente Azure Hyperledger Fabric a partir do repo do GitHub público, seguido de carregar todos os pacotes npm dependentes. Após a execução bem sucedida do comando, pode ver uma pasta node_modules no diretório atual. Todas as embalagens necessárias são carregadas na pasta node_modules.

### <a name="set-up-environment-variables"></a>Configurar as variáveis de ambiente

Todas as variáveis ambientais seguem a convenção de nomeação de recursos Azure.

#### <a name="set-environment-variables-for-the-orderer-organizations-client"></a>Definir variáveis ambientais para o cliente da organização do ordenador

```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

#### <a name="set-environment-variables-for-the-peer-organizations-client"></a>Definir variáveis ambientais para o cliente da organização de pares

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

Com base no número de organizações de pares no seu consórcio, poderá ser obrigado a repetir os comandos dos pares e definir a variável ambiental em conformidade.

#### <a name="set-environment-variables-for-an-azure-storage-account"></a>Definir variáveis ambientais para uma conta de armazenamento Azure

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Utilize os seguintes comandos para criar uma conta de armazenamento Azure. Se já tem conta de armazenamento Azure, ignore este passo.

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Utilize os seguintes comandos para criar uma partilha de ficheiros na conta de armazenamento Azure. Se já tem uma partilha de ficheiros, ignore este passo.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Utilize os seguintes comandos para gerar uma cadeia de ligação para uma partilha de ficheiros Azure.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-an-organization-connection-profile-admin-user-identity-and-msp"></a>Importar um perfil de conexão de organização, identidade de utilizador administrador, e MSP

Utilize os seguintes comandos para obter o perfil de conexão da organização, identidade do utilizador administrativo e Fornecedor de Serviços Geridos (MSP) do cluster de ServiçoS Azure Kubernetes e armazenar estas identidades na loja local da aplicação do cliente. Um exemplo de uma loja local é o *diretório azhlfTool/lojas.*

Para a organização do ordenador:

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

Para a organização de pares:

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="create-a-channel"></a>Criar um canal

A partir do cliente da organização do ordenador, use o seguinte comando para criar um canal que contenha apenas a organização do ordenador.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="add-a-peer-organization-for-consortium-management"></a>Adicione uma organização de pares para a gestão do consórcio

>[!NOTE]
> Antes de começar com qualquer operação de consórcio, certifique-se de que terminou a configuração inicial da aplicação do cliente.  

Executar os seguintes comandos na ordem dada para adicionar uma organização de pares em um canal e consórcio: 

1.  A partir do cliente da organização de pares, faça o upload do MSP da organização de pares no Azure Storage.

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  A partir do cliente da organização de encomendas, descarregue o MSP da organização de pares da Azure Storage. Em seguida, emita o comando para adicionar a organização de pares no canal e consórcio.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  A partir do cliente da organização do ordenador, faça o upload do perfil de ligação do ordenante no Azure Storage para que a organização de pares possa ligar-se aos nós do ordenador utilizando este perfil de ligação.

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  A partir do cliente da organização de pares, descarregue o perfil de conexão do ordenante a partir do Azure Storage. Em seguida, executar o comando para adicionar nós de pares no canal.

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

Da mesma forma, para adicionar mais organizações de pares no canal, atualizar as variáveis do ambiente entre pares de acordo com a organização de pares necessária e refazer os passos 1 a 4.

### <a name="set-anchor-peers"></a>Definir pares de âncora

A partir do cliente da organização de pares, executar o comando para definir os pares âncora para a organização de pares no canal especificado.

>[!NOTE]
> Antes de executar este comando, certifique-se de que a organização de pares é adicionada no canal utilizando comandos de gestão de consórcios.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>` é uma lista separada do espaço de nós pares a definir como um par de âncora. Por exemplo:

  - Desacorde `<anchorPeersList>` como se quisesse definir `"peer1"` apenas o nó peer1 como um par de âncora.
  - Desacordo `<anchorPeersList>` como se quisesse definir os nós `"peer1" "peer3"` peer1 e peer3 como pares de âncora.

## <a name="chaincode-management-commands"></a>Comandos de gestão de códigos de corrente

>[!NOTE]
> Antes de iniciar qualquer operação de código de corrente, certifique-se de que fez a configuração inicial da aplicação do cliente.  

### <a name="set-the-chaincode-specific-environment-variables"></a>Definir as variáveis ambientais específicas do código de cadeia

```bash
# Peer organization name where the chaincode operation will be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=â€œchaincode_example02â€
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=â€œ1â€ for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang', and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is placed. This is the absolute path to the chaincode project root directory.
# If you are using chaincode_example02 to validate then CC_PATH=â€œ/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/goâ€
CC_PATH=<chaincodePath>  
# Channel on which chaincode will be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>Instalar código de corrente  

Executar o seguinte comando para instalar o código de corrente na organização de pares.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
O comando instalará o código de corrente em todos os nós pares da organização de pares definidos na `ORGNAME` variável ambiental. Se duas ou mais organizações de pares estiverem no seu canal e pretender instalar o código de corrente em todas elas, execute este comando separadamente para cada organização de pares.  

Siga estes passos:  

1.  Definir `ORGNAME` e de acordo com e executar o `USER_IDENTITY` `peerOrg1` `./azhlf chaincode install` comando.  
2.  Definir `ORGNAME` e de acordo com e executar o `USER_IDENTITY` `peerOrg2` `./azhlf chaincode install` comando.  

### <a name="instantiate-chaincode"></a>Código de corrente instantâneo  

A partir da aplicação do cliente de pares, executar o seguinte comando para instantaneamente o código de corrente no canal.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>
```

Passe o nome da função instantânea e a lista de argumentos separados pelo espaço dentro `<instantiateFunc>` e `<instantiateFuncArgs>` respectivamente. Por exemplo, para instantaneaizar chaincode_example02.go chaincode, definido `<instantiateFunc>` para `init` e para `<instantiateFuncArgs>` `"a" "2000" "b" "1000"` .

Também pode passar o ficheiro JSON de configuração da coleção utilizando a `--collections-config` bandeira. Ou, definir os argumentos transitórios usando a `-t` bandeira enquanto instantaneamente o código de corrente utilizado para transações privadas.

Por exemplo:

```bash
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath>
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath> -t <transientArgs>
```

A `<collectionConfigJSONFilePath>` peça é o caminho para o ficheiro JSON que contém as coleções definidas para a instantânea de código de cadeia de dados privados. Pode encontrar o ficheiro JSON de configuração de uma recolha de amostras relativamente ao diretório *azhlfTool* no seguinte caminho: `./samples/chaincode/src/private_marbles/collections_config.json` .
Passe `<transientArgs>` como JSON válido em formato de corda. Escapar de qualquer personagem especial. Por exemplo: `'{\\\"asset\":{\\\"name\\\":\\\"asset1\\\",\\\"price\\\":99}}'`

> [!NOTE]
> Executar o comando uma vez de qualquer organização de pares no canal. Após a transação ser submetida com sucesso ao ordenante, o ordenante distribui esta transação a todas as organizações de pares do canal. O código de corrente é então instantâneo em todos os nós de pares em todas as organizações de pares do canal.  

### <a name="invoke-chaincode"></a>Invocar código de corrente  

A partir do cliente da organização de pares, executar o seguinte comando para invocar a função de código de corrente:  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

Passe o nome da função invocada e a lista de argumentos separados pelo espaço inâ€ e `<invokeFunction>` 0009. `<invokeFuncArgs>` Continuando com o exemplo de chaincode_example02.go chaincode, para realizar uma operação de invocação, definido por `<invokeFunction>` `invoke` 100€ `<invokeFuncArgs>` `"a" "b" "10"`  

>[!NOTE]
> Executar o comando uma vez de qualquer organização de pares no canal. Após a transação ser submetida com sucesso ao ordenante, o ordenante distribui esta transação a todas as organizações de pares do canal. O estado mundial é então atualizado em todos os nós de todos os pares de todas as organizações de pares no canal.  


### <a name="query-chaincode"></a>Código de cadeia de consulta  

Executar o seguinte comando para consultar o código de corrente:  

```bash
./azhlf chaincode query -o $ORGNAME -p <endorsingPeers> -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs> 
```
Os pares de apoio são pares onde o código de corrente é instalado e é solicitado para a execução de transações. Você deve definir `<endorsingPeers>` para conter nomes de nó de pares da organização de pares atual. Listar os pares de apoio para uma determinada combinação de código de corrente e de canal separada por espaços. Por exemplo: `-p "peer1" "peer3"`.

Se estiver a usar *o azhlfTool* para instalar o código de corrente, passe os nomes dos nóns pares como valor para o argumento do par de apoio. O código de corrente está instalado em todos os nós de pares para essa organização. 

Passe o nome da função de consulta e a lista de argumentos separados pelo espaço inâ€ e `<queryFunction>` `<queryFuncArgs>` 00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000. Mais uma vez tomando chaincode_example02 o código como referência ao valor de "a" no Estado mundial, definido e a `<queryFunction>` `query` `<queryArgs>` `"a"` .  

## <a name="troubleshoot"></a>Resolução de problemas

### <a name="find-deployed-version"></a>Encontrar versão implementada

Executar os seguintes comandos para encontrar a versão da sua implementação do modelo. Desloque as variáveis ambientais de acordo com o grupo de recursos onde o modelo foi implantado.

```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3
```

### <a name="patch-previous-version"></a>Verrou versão anterior

Se estiver a enfrentar problemas com o código de corrente em qualquer implementação da versão do modelo abaixo do v3.0.0, siga os passos abaixo para corrigir os seus nós de pares com uma correção.

Descarregue o script de implementação de pares.

```bash
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/scripts/patchPeerDeployment.sh -o patchPeerDeployment.sh; chmod 777 patchPeerDeployment.sh
```

Execute o script utilizando o seguinte comando substituindo os parâmetros para o seu par.

```bash
source patchPeerDeployment.sh <peerOrgSubscription> <peerOrgResourceGroup> <peerOrgAKSClusterName>
```

Espere que todos os seus nós parem para ser remendados. Pode sempre verificar o estado dos seus nós pares, em diferentes instâncias da concha utilizando o seguinte comando.

```bash
kubectl get pods -n hlf
```

## <a name="support-and-feedback"></a>Suporte e comentários

Para se manter atualizado sobre as ofertas e informações do serviço blockchain da equipa de engenharia Azure Blockchain, visite o [blog Azure Blockchain](https://azure.microsoft.com/blog/topics/blockchain/).

Para fornecer feedback do produto ou para solicitar novas funcionalidades, publicar ou votar por uma ideia através do [fórum de feedback Azure para blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Suporte da comunidade

Envolva-se com engenheiros da Microsoft e especialistas da comunidade Azure Blockchain:

- [Página do Microsoft Q&A](/answers/topics/azure-blockchain-workbench.html) 
   
  O suporte de engenharia para modelos blockchain está limitado a problemas de implantação.
- [Comunidade tecnológica da Microsoft](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
