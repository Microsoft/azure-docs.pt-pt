---
title: Personalize as rotas definidas pelo utilizador (UDR) no Serviço Azure Kubernetes (AKS)
description: Saiba como definir uma rota de saída personalizada no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 06/05/2020
ms.openlocfilehash: d62f40fb835bfe6993ad31ddd20cfdea1d9135c2
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310874"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>Personalize a saída do cluster com uma rota definida pelo utilizador

A Egress de um cluster AKS pode ser personalizada para se adaptar a cenários específicos. Por predefinição, a AKS irá providenciar um Balanceador de Carga Standard SKU para ser configurado e utilizado para a saída. No entanto, a configuração padrão pode não satisfazer os requisitos de todos os cenários se os IPs públicos forem proibidos ou se forem necessários lúpulos adicionais para a saída.

Este artigo percorre a forma de personalizar a rota de saída de um cluster para suportar cenários de rede personalizados, como aqueles que não permite iPs públicos e exige que o cluster se sente atrás de um aparelho virtual de rede (NVA).

## <a name="prerequisites"></a>Pré-requisitos
* Versão Azure CLI 2.0.81 ou maior
* Versão API de `2020-01-01` ou maior


## <a name="limitations"></a>Limitações
* O OutboundType só pode ser definido no cluster criar tempo e não pode ser atualizado posteriormente.
* A definição `outboundType` requer clusters AKS com um `vm-set-type` de `VirtualMachineScaleSets` . `load-balancer-sku` `Standard`
* Definir `outboundType` um valor requer uma rota definida pelo utilizador com conectividade de saída válida para o `UDR` cluster.
* Definir `outboundType` um valor de implica que o IP de `UDR` origem ingresso encaminhado para o balanceador de carga pode **não corresponder** ao endereço de destino de saída do cluster.

## <a name="overview-of-outbound-types-in-aks"></a>Visão geral dos tipos de saída em AKS

Um cluster AKS pode ser personalizado com um único `outboundType` balanceador de carga tipo ou encaminhamento definido pelo utilizador.

> [!IMPORTANT]
> O tipo de saída só afeta o tráfego de saída do seu cluster. Consulte [a instalação de controladores de entrada](ingress-basic.md) para obter mais informações.

> [!NOTE]
> Pode utilizar a sua própria [tabela de rotas][byo-route-table] com rede UDR e kubenet.

### <a name="outbound-type-of-loadbalancer"></a>Tipo de saída de loadBalancer

Se `loadBalancer` estiver definido, o AKS completa automaticamente a seguinte configuração. O equilibrador de carga é utilizado para a saída através de um IP público atribuído pela AKS. Um tipo de saída `loadBalancer` suporta serviços de tipo Kubernetes, `loadBalancer` que esperam saída do equilibrador de carga criado pelo fornecedor de recursos AKS.

A seguinte configuração é feita por AKS.
   * Um endereço IP público é previsto para a saída de cluster.
   * O endereço IP público é atribuído ao recurso do balançador de carga.
   * As piscinas de backend para o balançador de carga são configuradas para os nós de agente no cluster.

Abaixo está uma topologia de rede implantada em clusters AKS por padrão, que usam um `outboundType` de `loadBalancer` .

![fora-de-pista-lb](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Tipo de saída de userDefinedRouting

> [!NOTE]
> A utilização do tipo de saída é um cenário avançado de rede e requer uma configuração adequada da rede.

Se `userDefinedRouting` estiver definido, a AKS não configurará automaticamente caminhos de saída. Espera-se que o **utilizador o tenha feito a seguir.**

O cluster AKS deve ser implantado numa rede virtual existente com uma sub-rede configurada. Ao utilizar a arquitetura padrão do balanceador de carga (SLB), deve estabelecer uma saída explícita. Isto requer o envio de pedidos de saída para um aparelho como uma firewall, gateway, on-prem ou para permitir que a saída seja feita por um IP público atribuído ao balanceador de carga padrão ou a um dado nó.

O fornecedor de recursos AKS irá implantar um balanceador de carga padrão (SLB). O equilibrador de carga não está configurado com nenhuma regra e [não incorre numa carga até que uma regra seja colocada](https://azure.microsoft.com/pricing/details/load-balancer/). A AKS **não** irá automaticamente providenciar um endereço IP público para o frontend SLB. A AKS **não** configurará automaticamente o pool de backend do balançador de carga.

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Implementar um cluster com tipo de saída de UDR e Azure Firewall

Para ilustrar a aplicação de um cluster com tipo de saída utilizando uma rota definida pelo utilizador, um cluster pode ser configurado numa rede virtual espreitada com uma Firewall Azure.

![Topologia bloqueada](media/egress-outboundtype/outboundtype-udr.png)

* Ingress é forçado a fluir através de filtros de firewall
   * Uma sub-rede isolada detém um balançador de carga interno para encaminhamento em nós de agente
   * Os nós de agente estão isolados numa sub-rede dedicada
* Os pedidos de saída começam de nós de agente para o IP interno do Azure Firewall usando uma rota definida pelo utilizador
   * Os pedidos dos nós de agente da AKS seguem um UDR que foi colocado na sub-rede em que o cluster AKS foi implantado.
   * Azure Firewall sai da rede virtual a partir de um frontend IP público
   * O acesso ao plano de controlo AKS está protegido por um NSG, que permitiu o endereço IP frontal de firewall
   * O acesso à internet pública ou a outros serviços Azure flui de e para o endereço IP frontal de firewall

### <a name="set-configuration-via-environment-variables"></a>Definir configuração através de variáveis ambientais

Defina um conjunto de variáveis ambientais a serem usadas em criações de recursos.

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

# NOTE: Update Subscription Name
# Set Default Azure Subscription to be Used via Subscription ID

az account set -s <SUBSCRIPTION_ID_GOES_HERE>

# NOTE: Update Subscription Name for setting SUBID

SUBID=$(az account show -s '<SUBSCRIPTION_NAME_GOES_HERE>' -o tsv --query 'id')
```

## <a name="create-a-virtual-network-with-multiple-subnets"></a>Criar uma rede virtual com várias sub-redes

Fornecendo uma rede virtual com três sub-redes separadas, uma para o cluster, uma para a firewall e outra para entrada de serviço.

![Topologia de rede vazia](media/egress-outboundtype/empty-network.png)

Criar um grupo de recursos para deter todos os recursos.

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

## <a name="create-and-set-up-an-azure-firewall-with-a-udr"></a>Criar e configurar uma Firewall Azure com um UDR

As regras de entrada e saída do Azure Firewall devem ser configuradas. O principal objetivo da firewall é permitir que as organizações configuram a entrada de granular e saiam das regras de tráfego dentro e fora do Cluster AKS.

![Firewall e UDR](media/egress-outboundtype/firewall-udr.png)

Crie um recurso IP público SKU padrão que será usado como o endereço frontal Azure Firewall.

```azure-cli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Registe a pré-visualização da extensão cli para criar uma Firewall Azure.
```azure-cli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC
```

O endereço IP criado anteriormente pode agora ser atribuído ao frontend de firewall.
> [!NOTE]
> A configuração do endereço IP público para o Azure Firewall pode demorar alguns minutos.
> 
> Se os erros forem repetidamente recebidos no comando abaixo, elimine a firewall existente e o IP público existente e disposição do IP público e do Azure Firewall através do portal ao mesmo tempo.

```azure-cli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Quando o comando anterior tiver sido bem sucedido, guarde o endereço IP frontal de firewall para configuração mais tarde.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

> [!Note]
> Se utilizar acesso seguro ao servidor AKS API com [intervalos de endereço IP autorizados,](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges)tem de adicionar o IP público de firewall ao intervalo IP autorizado.

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Criar um UDR com um salto para Azure Firewall

> [!IMPORTANT]
> O tipo de saída de UDR requer uma rota para 0.0.0.0/0 e próximo destino de lúpulo de NVA (Aparelho Virtual de Rede) na tabela de rotas.

O Azure liga automaticamente o tráfego entre sub-redes Azure, redes virtuais e redes no local. Se quiser alterar qualquer um dos roteamentos padrão do Azure, fá-lo criando uma tabela de rotas.

Crie uma tabela de rotas vazia para ser associada a uma determinada sub-rede. A tabela de rotas definirá o próximo salto como o Azure Firewall criado acima. Cada sub-rede pode ter zero ou uma tabela de rotas associada a si.

```azure-cli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Consulte [a documentação da tabela de rotas de rede virtual](../virtual-network/virtual-networks-udr-overview.md#user-defined) sobre como pode sobrepor as rotas do sistema padrão da Azure ou adicionar rotas adicionais à tabela de rotas de uma sub-rede.

## <a name="adding-network-firewall-rules"></a>Adicionar regras de firewall de rede

> [!WARNING]
> Abaixo mostra um exemplo de adicionar uma regra de firewall. Todos os pontos finais de saída definidos nos [pontos finais de saídas necessários](egress.md) devem ser ativados pelas regras de firewall de aplicação para que os clusters AKS funcionem. Sem estes pontos finais ativados, o seu cluster não pode funcionar.

Abaixo está um exemplo de uma regra de rede e aplicação. Adicionamos uma regra de rede que permite qualquer protocolo, endereço de origem, endereço de destino e portos de destino. Também adicionamos uma regra de aplicação para **alguns** dos pontos finais exigidos pela AKS.

Num cenário de produção, só deve permitir o acesso aos pontos finais necessários para a sua aplicação e aos definidos em [saídas necessárias à AKS.](egress.md)

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

Consulte a [documentação do Azure Firewall](https://docs.microsoft.com/azure/firewall/overview) para saber mais sobre o serviço Azure Firewall.

## <a name="associate-the-route-table-to-aks"></a>Associe a tabela de rotas à AKS

Para associar o cluster à firewall, a sub-rede dedicada para a sub-rede do cluster deve fazer referência à tabela de rotas criada acima. A associação pode ser feita através da emissão de um comando à rede virtual que mantém o cluster e a firewall para atualizar a tabela de rotas da sub-rede do cluster.

```azure-cli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

## <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Implementar AKS com tipo de UDR de saída para a rede existente

Agora, um cluster AKS pode ser implantado na rede virtual existente. Para definir um tipo de saída de cluster para o encaminhamento definido pelo utilizador, deve ser fornecida uma sub-rede existente à AKS.

![aks-implementar](media/egress-outboundtype/outboundtype-udr.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Criar um principal de serviço com acesso à prestação dentro da rede virtual existente

Um diretor de serviço é usado pela AKS para criar recursos de cluster. O principal serviço passado no tempo de criação é usado para criar recursos AKS subjacentes, tais como VMs, Armazenamento e Balanceadores de Carga usados pela AKS. Se for concedidas poucas permissões, não poderá providenciar um cluster AKS.

```azure-cli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Agora substitua o `APPID` e `PASSWORD` abaixo pelo appid principal de serviço e senha principal de serviço autogerida pela saída de comando anterior. Vamos fazer referência ao ID de recursos VNET para conceder as permissões ao principal de serviço para que a AKS possa implantar recursos nele.

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

Finalmente, o cluster AKS pode ser implantado na sub-rede existente que dedicámos para o cluster. A sub-rede-alvo a implantar é definida com a variável ambiental, `$SUBNETID` . Não definimos a `$SUBNETID` variável nos passos anteriores. Para definir o valor do ID da sub-rede, pode utilizar o seguinte comando:

```azurecli
SUBNETID="/subscriptions/$SUBID/resourceGroups/$RG/providers/Microsoft.Network/virtualNetworks/$VNET_NAME/subnets/$AKSSUBNET_NAME"
```

Defina o tipo de saída para seguir o UDR que existe na sub-rede, permitindo que a AKS ignore a configuração e o provisionamento ip para o equilibrador de carga que agora pode ser estritamente interno.

A funcionalidade AKS para [gamas IP autorizadas pelo servidor API](api-server-authorized-ip-ranges.md) pode ser adicionada para limitar o acesso do servidor API apenas ao ponto final público da firewall. A funcionalidade de gamas IP autorizadas é denotada no diagrama como o NSG que deve ser passado para aceder ao plano de controlo. Ao permitir que a função de gama IP autorizada limite o acesso ao servidor API, as suas ferramentas de desenvolvedor devem utilizar uma caixa de salto a partir da rede virtual da firewall ou deve adicionar todos os pontos finais do desenvolvedor à gama IP autorizada.

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

### <a name="enable-developer-access-to-the-api-server"></a>Permitir o acesso do desenvolvedor ao servidor API

Devido aos intervalos de IP autorizados para o cluster, deve adicionar os endereços IP de ferramentas do desenvolvedor à lista de clusters AKS de gamas IP aprovadas para aceder ao servidor API. Outra opção é configurar uma caixa de salto com a ferramenta necessária dentro de uma sub-rede separada na rede virtual do Firewall.

Adicione outro endereço IP às gamas aprovadas com o seguinte comando

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKS_NAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Use o comando [az aks get-credentials][az-aks-get-credentials] para configurar `kubectl` para ligar ao seu cluster Kubernetes recém-criado. 

 ```azure-cli
 az aks get-credentials -g $RG -n $AKS_NAME
 ```

### <a name="set-up-the-internal-load-balancer"></a>Configurar o equilibrador de carga interno

A AKS lançou um equilibrador de carga com o cluster que pode ser configurado como um [equilibrador interno de carga](internal-lb.md).

Para criar um equilibrador de carga interno, crie um manifesto de serviço denominado internal-lb.yaml com o tipo de serviço LoadBalancer e a anotação interna de balançamento de carga-azul, como mostra o seguinte exemplo:

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

Coloque o balançador de carga interno utilizando o kubectl e especifique o nome do seu manifesto YAML:

```bash
kubectl apply -f internal-lb.yaml
```

## <a name="deploy-a-kubernetes-service"></a>Implementar um serviço Kubernetes

Uma vez que o tipo de saída do cluster é definido como UDR, associando os nós do agente como o pool de backend para o balançador de carga não é completado automaticamente por AKS no cluster criar tempo. No entanto, a associação backend pool é gerida pelo provedor de nuvem Kubernetes Azure quando o serviço Kubernetes é implantado.

Implemente a aplicação de aplicação de voto Azure copiando o yaml abaixo para um ficheiro chamado `example.yaml` .

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

## <a name="add-a-dnat-rule-to-azure-firewall"></a>Adicione uma regra de DNAT ao Azure Firewall

Para configurar a conectividade de entrada, uma regra de DNAT deve ser escrita para a Firewall Azure. Para testar a conectividade com o nosso cluster, é definida uma regra para o endereço IP público frontend firewall para encaminhar para o IP interno exposto pelo serviço interno.

O endereço de destino pode ser personalizado uma vez que é a porta da firewall a ser acedida. O endereço traduzido deve ser o endereço IP do balançador de carga interno. A porta traduzida deve ser a porta exposta para o seu serviço Kubernetes.

Terá de especificar o endereço IP interno atribuído ao balançador de carga criado pelo serviço Kubernetes. Recupere o endereço executando:

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
> Ao eliminar o serviço interno Kubernetes, se o balançador de carga interno deixar de ser utilizado por qualquer serviço, o fornecedor de nuvem Azure eliminará o balançador interno de carga. Na próxima implementação de serviço, será implantado um equilibrador de carga se nenhum for encontrado com a configuração solicitada.

Para limpar os recursos do Azure, elimine o grupo de recursos AKS.

```azure-cli
az group delete -g $RG
```

## <a name="validate-connectivity"></a>Validar conectividade

Navegue para o endereço IP frontend Azure Firewall num browser para validar a conectividade.

Você deve ver uma imagem da aplicação de voto Azure.

## <a name="next-steps"></a>Passos seguintes

Consulte [a visão geral da UDR em rede Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

Ver [como criar, alterar ou apagar uma tabela de rotas](https://docs.microsoft.com/azure/virtual-network/manage-route-table).

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet
