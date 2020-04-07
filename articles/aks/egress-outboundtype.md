---
title: Personalizar rotas definidas pelo utilizador (UDR) no Serviço Azure Kubernetes (AKS)
description: Saiba como definir uma rota de saída personalizada no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 3780680c485aebf1ffc654d31c577821a9b96fff
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80676510"
---
# <a name="customize-cluster-egress-with-a-user-defined-route-preview"></a>Personalizar a saída do cluster com uma rota definida pelo utilizador (Pré-visualização)

A saída de um cluster AKS pode ser personalizada para se adaptar a cenários específicos. Por padrão, a AKS fornecerá um Balancer de Carga SKU Padrão para ser configurado e utilizado para saídas. No entanto, a configuração padrão pode não satisfazer os requisitos de todos os cenários se os IPs públicos forem proibidos ou forem necessários lúpulos adicionais para a saída.

Este artigo passa por personalizar a rota de saída de um cluster para suportar cenários de rede personalizados, como aqueles que não permitem iPs públicos e exige que o cluster se sita atrás de um aparelho virtual de rede (NVA).

> [!IMPORTANT]
> As funcionalidades de pré-visualização AKS são self-service e são oferecidas numa base de opt-in. As pré-visualizações são fornecidas *como estão* *disponíveis* e estão excluídas do acordo de nível de serviço (SLA) e da garantia limitada. As pré-visualizações do AKS são parcialmente cobertas pelo apoio ao cliente numa base de *melhor esforço.* Portanto, as características não são destinadas ao uso da produção. Para mais informações, consulte os seguintes artigos de apoio:
>
> * [Políticas de apoio aks](support-policies.md)
> * [FaQ de suporte azure](faq.md)

## <a name="prerequisites"></a>Pré-requisitos
* Versão Azure CLI 2.0.81 ou superior
* Versão de extensão de pré-visualização Azure CLI 0.4.28 ou superior
* Versão API `2020-01-01` de ou maior

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>Instale a mais recente extensão de pré-visualização Azure CLI AKS
Para definir o tipo de saída de um cluster, precisa da versão de extensão de pré-visualização Azure CLI AKS 0.4.18 ou posterior. Instale a extensão de pré-visualização Azure CLI AKS utilizando o comando de adição de extensão az e, em seguida, verifique se há quaisquer atualizações disponíveis utilizando o seguinte comando de atualização de extensão az:

```azure-cli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>Limitações
* Durante a `outboundType` pré-visualização, só pode ser definido no cluster criar tempo e não pode ser atualizado posteriormente.
* Durante a `outboundType` pré-visualização, os clusters AKS devem utilizar o Azure CNI. Kubenet é configurável, o uso requer associações manuais da tabela de rotas para a subnet AKS.
* A `outboundType` definição requer aglomerados `vm-set-type` `VirtualMachineScaleSets` AKS com um de e `load-balancer-sku` de `Standard`.
* A `outboundType` definição `UDR` para um valor requer uma rota definida pelo utilizador com conectividade de saída válida para o cluster.
* A `outboundType` definição `UDR` de um valor implica que a fonte de entrada IP direcionada para o equilibrador de carga não pode **corresponder** ao endereço de destino de saída do cluster.

## <a name="overview-of-outbound-types-in-aks"></a>Visão geral dos tipos de saída em AKS

Um cluster AKS pode ser `outboundType` personalizado com um único equilíbrior de carga ou encaminhamento definido pelo utilizador.

> [!IMPORTANT]
> O tipo de saída afeta apenas o tráfego de saída do seu cluster. Consulte a [configuração de controladores](ingress-basic.md) de ingresso para obter mais informações.

### <a name="outbound-type-of-loadbalancer"></a>Tipo de carga de saídaBalancer

Se `loadBalancer` estiver definido, o AKS completa automaticamente a seguinte configuração. O equilibrista de carga é utilizado para a saída através de um IP público atribuído à AKS. Um tipo de `loadBalancer` suporte de saída dos `loadBalancer`serviços kubernetes de tipo, que esperam saída do equilibrador de carga criado pelo fornecedor de recursos AKS.

A seguinte configuração é feita pela AKS.
   * Um endereço IP público está previsto para a saída do cluster.
   * O endereço IP público é atribuído ao recurso do equilibrador de carga.
   * As piscinas de backend para o equilibrador de carga são configuradas para nós de agente no cluster.

Abaixo está uma topologia de rede implantada em `outboundType` clusters AKS por padrão, que usam um de `loadBalancer`.

![outboundtype-lb](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Tipo de utilizador De saídaDefinedRouting

> [!NOTE]
> A utilização do tipo de saída é um cenário avançado de networking e requer uma configuração de rede adequada.

Se `userDefinedRouting` estiver definido, o AKS não configurará automaticamente os caminhos de saída. Espera-se que o **utilizador**seja feito o seguinte.

O cluster deve ser implantado numa rede virtual existente com uma sub-rede configurada. Deve existir uma rota válida definida pelo utilizador (UDR) na subnet com conectividade de saída.

O fornecedor de recursos AKS irá implantar um equilibrador de carga padrão (SLB). O equilibrista de carga não está configurado com quaisquer regras e [não incorre numa carga até](https://azure.microsoft.com/pricing/details/load-balancer/)que uma regra seja colocada . A AKS **não** fornecerá automaticamente um endereço IP público para o frontend SLB. O AKS **não** configurará automaticamente o conjunto de backend do equilibrador de carga.

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Implementar um cluster com tipo de saída de UDR e Firewall Azure

Para ilustrar a aplicação de um cluster com tipo de saída utilizando uma rota definida pelo utilizador, um cluster pode ser configurado numa rede virtual com uma Firewall Azure.

![Topologia bloqueada](media/egress-outboundtype/outboundtype-udr.png)

* Ingress é forçado a fluir através de filtros de firewall
   * Uma sub-rede isolada detém um equilibrante interno de carga para encaminhamento em nódos de agente
   * Os nódosos de agente estão isolados numa subnetdedicada
* Os pedidos de saída partem dos nódos os requisitos do agente para o IP interno da Firewall Azure utilizando uma rota definida pelo utilizador
   * Os pedidos dos nós do agente AKS seguem um UDR que foi colocado na sub-rede em que o cluster AKS foi implantado.
   * Azure Firewall egrs fora da rede virtual a partir de um ip frontend público
   * O acesso ao plano de controlo AKS está protegido por um NSG, que permitiu o endereço IP frontal da firewall
   * Acesso à internet pública ou outros serviços Azure flui de e para o endereço IP frontal firewall

### <a name="set-configuration-via-environment-variables"></a>Configuração de conjunto através de variáveis ambientais

Defina um conjunto de variáveis ambientais a utilizar nas criações de recursos.

```bash
PREFIX="contosofin"
RG="${PREFIX}-rg"
LOC="eastus"
NAME="${PREFIX}outboundudr"
AKS_NAME="${PREFIX}aks"
VNET_NAME="${PREFIX}vnet"
AKSSUBNET_NAME="${PREFIX}akssubnet"
SVCSUBNET_NAME="${PREFIX}svcsubnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}fw"
FWPUBLICIP_NAME="${PREFIX}fwpublicip"
FWIPCONFIG_NAME="${PREFIX}fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}fwrt"
FWROUTE_NAME="${PREFIX}fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}fwinternet"
DEVSUBNET_NAME="${PREFIX}dev"
```

Em seguida, definir iDs de subscrição.

```azure-cli
# Get ARM Access Token and Subscription ID - This will be used for AuthN later.

ACCESS_TOKEN=$(az account get-access-token -o tsv --query 'accessToken')

# NOTE: Update Subscription Name
# Set Default Azure Subscription to be Used via Subscription ID

az account set -s <SUBSCRIPTION_ID_GOES_HERE>

# NOTE: Update Subscription Name for setting SUBID

SUBID=$(az account show -s '<SUBSCRIPTION_NAME_GOES_HERE>' -o tsv --query 'id')
```

## <a name="create-a-virtual-network-with-multiple-subnets"></a>Criar uma rede virtual com várias sub-redes

Fornecer uma rede virtual com três subredes separadas, uma para o cluster, uma para a firewall e outra para entrada de serviço.

![Topologia de rede vazia](media/egress-outboundtype/empty-network.png)

Crie um grupo de recursos para deter todos os recursos.

```azure-cli
# Create Resource Group

az group create --name $RG --location $LOC
```

Crie duas redes virtuais para acolher o cluster AKS e o Azure Firewall. Cada um terá a sua própria sub-rede. Vamos começar com a rede AKS.

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --address-prefixes 100.64.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 100.64.1.0/24

# Dedicated subnet for K8s services

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $SVCSUBNET_NAME \
    --address-prefix 100.64.2.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 100.64.3.0/24
```

## <a name="create-and-setup-an-azure-firewall-with-a-udr"></a>Crie e crie uma Firewall Azure com um UDR

As regras de entrada e saída da Firewall Azure devem ser configuradas. O principal objetivo da firewall é permitir que as organizações configuram a entrada granular e estivam as regras de tráfego para dentro e para fora do Cluster AKS.

![Firewall e UDR](media/egress-outboundtype/firewall-udr.png)

Crie um recurso IP público padrão SKU que será usado como o endereço frontal Azure Firewall.

```azure-cli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Registe a extensão cli de pré-visualização para criar uma Firewall Azure.
```azure-cli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC
```

O endereço IP criado anteriormente pode agora ser atribuído à frente da firewall.
> [!NOTE]
> A configuração do endereço IP público para o Firewall Azure pode demorar alguns minutos.
> 
> Se os erros forem repetidamente recebidos no comando abaixo, elimine a firewall existente e o IP público e forneça o IP público e o Firewall Azure através do portal ao mesmo tempo.

```azure-cli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Quando o comando anterior tiver sido bem sucedido, guarde o endereço IP frontal da firewall para posterior configuração.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Crie um UDR com um salto para azure firewall

O Azure encaminha automaticamente o tráfego entre as redes Azure, redes virtuais e no local. Se quiser alterar algum dos encaminhamentos padrão do Azure, fá-lo criando uma tabela de rotas.

Crie uma mesa de rota vazia para ser associada a uma determinada sub-rede. A tabela de rotas definirá o próximo salto como o Azure Firewall criado acima. Cada sub-rede pode ter zero ou uma tabela de rotas associada a si.

```azure-cli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Consulte [a documentação](../virtual-network/virtual-networks-udr-overview.md#user-defined) da tabela de rotas de rede virtual sobre como pode sobrepor as rotas padrão do sistema do Azure ou adicionar rotas adicionais à tabela de rotas de uma subnet.

## <a name="adding-network-firewall-rules"></a>Adicionar regras de firewall de rede

> [!WARNING]
> Abaixo mostra um exemplo de adição de uma regra de firewall. Todos os pontos finais de egress definidos nos [pontos finais de saída necessários](egress.md) devem ser ativados pelas regras de firewall de aplicação para os clusters AKS funcionarem. Sem estes pontos finais ativados, o seu cluster não pode funcionar.

Abaixo está um exemplo de uma regra de rede e aplicação. Adicionamos uma regra de rede que permite qualquer protocolo, endereço de origem, endereço de destino e portos de destino. Também adicionamos uma regra de candidatura para **alguns** dos pontos finais exigidos pela AKS.

Num cenário de produção, só deve permitir o acesso aos pontos finais necessários para a sua aplicação e os definidos na saída necessária para [a AKS](egress.md).

```
# Add Network FW Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'netrules' --protocols 'Any' --source-addresses '*' --destination-addresses '*' --destination-ports '*' --action allow --priority 100

# Add Application FW Rules
# IMPORTANT: Add AKS required egress endpoints

az network firewall application-rule create -g $RG -f $FWNAME \
    --collection-name 'AKS_Global_Required' \
    --action allow \
    --priority 100 \
    -n 'required' \
    --source-addresses '*' \
    --protocols 'http=80' 'https=443' \
    --target-fqdns \
        'aksrepos.azurecr.io' \
        '*blob.core.windows.net' \
        'mcr.microsoft.com' \
        '*cdn.mscr.io' \
        '*.data.mcr.microsoft.com' \
        'management.azure.com' \
        'login.microsoftonline.com' \
        'ntp.ubuntu.com' \
        'packages.microsoft.com' \
        'acs-mirror.azureedge.net'
```

Consulte a [documentação da Firewall Azure](https://docs.microsoft.com/azure/firewall/overview) para saber mais sobre o serviço Azure Firewall.

## <a name="associate-the-route-table-to-aks"></a>Associar a tabela de rotas ao AKS

Para associar o cluster à firewall, a sub-rede dedicada à sub-rede do cluster deve fazer referência à tabela de rotas acima criada. A associação pode ser feita através da emissão de um comando para a rede virtual que mantém tanto o cluster como a firewall para atualizar a tabela de rotas da subnet do cluster.

```azure-cli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

## <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Implementar AKS com tipo de UDR de saída para a rede existente

Agora, um cluster AKS pode ser implantado na configuração da rede virtual existente. Para definir um tipo de saída de cluster para o encaminhamento definido pelo utilizador, uma sub-rede existente deve ser fornecida ao AKS.

![aks-deploy](media/egress-outboundtype/outboundtype-udr.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Criar um serviço principal com acesso à prestação dentro da rede virtual existente

Um diretor de serviço é usado pela AKS para criar recursos de cluster. O principal de serviço passado no tempo de criação é usado para criar recursos AKS subjacentes, tais como VMs, Armazenamento e Balanceadores de Carga usados pela AKS. Se for concedida suposições muito poucas, não poderá fornecer um Cluster AKS.

```azure-cli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Agora substitua o `APPID` e abaixo pela appid principal do `PASSWORD` serviço e a palavra-passe principal de serviço autogerada pela saída de comando anterior. Vamos fazer referência ao ID de recursos VNET para conceder as permissões ao diretor de serviço para que o AKS possa implantar recursos nele.

```azure-cli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role Contributor

# View Role Assignment
az role assignment list --assignee $APPID --all -o table
```

### <a name="deploy-aks"></a>Implementar AKS

Finalmente, o cluster AKS pode ser implantado na subnet existente que dedicámos para o cluster. A sub-rede-alvo a ser implantada `$SUBNETID`é definida com a variável ambiental, . Não definimos a `$SUBNETID` variável nos passos anteriores. Para definir o valor para o ID da sub-rede, pode utilizar o seguinte comando:

```azurecli
SUBNETID="/subscriptions/$SUBID/resourceGroups/$RG/providers/Microsoft.Network/virtualNetworks/$VNET_NAME/subnets/$AKSSUBNET_NAME"
```

Definiremos o tipo de saída para seguir o UDR que existe na subnet, permitindo que o AKS ignore a configuração e o fornecimento de IP para o equilibrante de carga que pode agora ser estritamente interno.

A funcionalidade AKS para [intervalos IP autorizados](api-server-authorized-ip-ranges.md) pelo servidor API pode ser adicionada para limitar o acesso do servidor API apenas ao ponto final público da firewall. A função de gamaip autorizada é denotada no diagrama como o NSG que deve ser passado para aceder ao plano de controlo. Ao permitir que a função de gama IP autorizada limite o acesso ao servidor API, as ferramentas de desenvolvimento devem utilizar uma caixa de salto a partir da rede virtual da firewall ou deve adicionar todos os pontos finais do desenvolvedor à gama IP autorizada.

> [!TIP]
> Podem ser adicionadas funcionalidades adicionais à implantação do cluster, tais como (Cluster Privado)[]. Ao utilizar gamas IP autorizadas, será necessária uma caixa de salto dentro da rede de cluster para aceder ao servidor API.

```azure-cli
az aks create -g $RG -n $AKS_NAME -l $LOC \
  --node-count 3 \
  --network-plugin azure --generate-ssh-keys \
  --service-cidr 192.168.0.0/16 \
  --dns-service-ip 192.168.0.10 \
  --docker-bridge-address 172.22.0.1/29 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --load-balancer-sku standard \
  --outbound-type userDefinedRouting \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
  ```

### <a name="enable-developer-access-to-the-api-server"></a>Ativar o acesso do programador ao servidor API

Devido à configuração de gamas IP autorizadas para o cluster, deve adicionar os seus endereços IP de ferramentas de desenvolvimento à lista de clusters AKS das gamas IP aprovadas para aceder ao servidor API. Outra opção é configurar uma caixa de salto com a ferramenta necessária dentro de uma sub-rede separada na rede virtual da Firewall.

Adicione outro endereço IP às gamas aprovadas com o seguinte comando

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKS_NAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Use o comando [az aks get-credentials][az-aks-get-credentials] para configurar `kubectl` para se conectar ao seu recém-criado cluster Kubernetes. 

 ```azure-cli
 az aks get-credentials -g $RG -n $AKS_NAME
 ```

### <a name="setup-the-internal-load-balancer"></a>Configurar o equilibrador de carga interna

A AKS implementou um equilibrador de carga com o cluster que pode ser configurado como um [equilibrante](internal-lb.md)interno de carga .

Para criar um equilibrador de carga interno, crie um manifesto de serviço denominado interno-lb.yaml com o tipo de serviço LoadBalancer e a anotação interna de equilíbrio-carga azul, como mostra o seguinte exemplo:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Coloque o equilibrador de carga interno utilizando o kubectl e especifique o nome do seu manifesto YAML:

```bash
kubectl apply -f internal-lb.yaml
```

## <a name="deploy-a-kubernetes-service"></a>Implementar um serviço Kubernetes

Uma vez que o tipo de saída do cluster é definido como UDR, associando os nós do agente como o pool de backend para o equilibrante de carga não é concluído automaticamente pelo AKS no cluster criar tempo. No entanto, a associação de piscinas backend é tratada pelo fornecedor de nuvem Kubernetes Azure quando o serviço Kubernetes é implantado.

Implemente a aplicação da aplicação de votação Azure `example.yaml`copiando o yaml abaixo para um ficheiro chamado .

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Implementar o serviço executando:

```bash
kubectl apply -f example.yaml
```

## <a name="add-a-dnat-rule-to-azure-firewall"></a>Adicione uma regra de ADN ao Firewall Azure

Para configurar a conectividade de entrada, uma regra de ADN deve ser escrita para o Firewall Azure. Para testar a conectividade com o nosso cluster, é definida uma regra para o endereço IP público frontal firewall para o caminho para o IP interno exposto pelo serviço interno.

O endereço de destino pode ser personalizado, uma vez que é a porta da firewall a ser acedida. O endereço traduzido deve ser o endereço IP do equilibrador de carga interna. A porta traduzida deve ser a porta exposta para o seu serviço Kubernetes.

Terá de especificar o endereço IP interno atribuído ao equilibrador de carga criado pelo serviço Kubernetes. Recupere o endereço executando:

```bash
kubectl get services
```

O endereço IP necessário será listado na coluna EXTERNAL-IP, semelhante ao seguinte.

```bash
NAME               TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
azure-vote-back    ClusterIP      192.168.92.209   <none>        6379/TCP       23m
azure-vote-front   LoadBalancer   192.168.19.183   100.64.2.5    80:32106/TCP   23m
kubernetes         ClusterIP      192.168.0.1      <none>        443/TCP        4d3h
```

```azure-cli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address <INSERT IP OF K8s SERVICE>
```

## <a name="clean-up-resources"></a>Limpar recursos

> [!NOTE]
> Ao eliminar o serviço interno Kubernetes, se o equilibrante interno de carga deixar de ser utilizado por qualquer serviço, o fornecedor de nuvem Azure eliminará o equilibrador interno de carga. Na próxima implementação do serviço, será implantado um equilibrante de carga se não for possível encontrar nenhum com a configuração solicitada.

Para limpar os recursos do Azure, elimine o grupo de recursos AKS.

```azure-cli
az group delete -g $RG
```

## <a name="validate-connectivity"></a>Validar a conectividade

Navegue para o endereço IP frontal do Azure Firewall num browser para validar a conectividade.

Deve ver uma imagem da aplicação de voto Azure.

## <a name="next-steps"></a>Passos seguintes

Consulte a visão geral do UDR em [rede Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

Veja [como criar, alterar ou apagar uma tabela](https://docs.microsoft.com/azure/virtual-network/manage-route-table)de rotas .

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
