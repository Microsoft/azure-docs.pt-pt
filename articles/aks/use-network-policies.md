---
title: Proteger os pods com as políticas de rede no Azure Kubernetes Service (AKS)
description: Saiba como proteger o tráfego que entram e saem pods através de políticas de rede de Kubernetes no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 02/12/2019
ms.author: iainfou
ms.openlocfilehash: d7d23300936cd512466e5c4b18f1f0922c81ceff
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57408195"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Proteger o tráfego entre pods através de políticas de rede no Azure Kubernetes Service (AKS)

Ao executar aplicações modernas e baseadas em microsserviços no Kubernetes, muitas vezes deseja controlar quais componentes podem comunicar entre si. O princípio de privilégio mínimo deve ser aplicado a forma como o tráfego pode fluir entre pods num cluster do AKS. Por exemplo, provavelmente desejará bloquear o tráfego diretamente para aplicações de back-end. No Kubernetes, o *política de rede* funcionalidade permite-lhe definir regras para o tráfego de entrada e saída entre pods num cluster.

Calico, um sistema de rede do código-fonte aberto e solução de segurança de rede fundada por Tigera, oferece um mecanismo de políticas de rede que pode implementar as regras de política de rede do Kubernetes. Este artigo mostra-lhe como instalar o motor de política de rede Calico e criar políticas de rede do Kubernetes para controlar o fluxo de tráfego entre pods no AKS.

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações ser-lhe-ão disponibilizadas na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="before-you-begin"></a>Antes de começar

Precisa da versão 2.0.56 da CLI do Azure ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar a CLI do Azure][install-azure-cli].

Para criar um AKS com a política de rede, ative primeiro um sinalizador de funcionalidade na sua subscrição. Para registar o *EnableNetworkPolicy* sinalizador de funcionalidade, utilize o [Registre-se de funcionalidade de az] [ az-feature-register] comando conforme mostrado no exemplo a seguir:

```azurecli-interactive
az feature register --name EnableNetworkPolicy --namespace Microsoft.ContainerService
```

Demora alguns minutos para que o estado a mostrar *registado*. Pode verificar o estado de registo com o [lista de funcionalidades de az] [ az-feature-list] comando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableNetworkPolicy')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registo do *containerservice* fornecedor de recursos com o [Registre-se fornecedor de az] [ az-provider-register] comando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-network-policy"></a>Descrição geral da política de rede

Por predefinição, todos os pods num cluster do AKS podem enviar e receber tráfego sem limitações. Para melhorar a segurança, pode definir regras que controlam o fluxo de tráfego. Por exemplo, aplicações de back-end, muitas vezes, só são expostas aos serviços de front-end necessário ou componentes da base de dados só estão acessíveis para os escalões de aplicação que se ligar aos mesmos.

As políticas de rede são recursos do Kubernetes que permitem-lhe controlar o fluxo de tráfego entre os pods. Pode optar por permitir ou negar o tráfego com base em etiquetas de definições de atribuídas como, espaço de nomes ou porta de tráfego. Políticas de rede são definidas como um YAML manifestos e pode ser incluído como parte de um manifesto mais amplo que também cria uma implementação ou o serviço.

Para ver as políticas de rede em ação, vamos criar e, em seguida, expanda numa diretiva que define o fluxo de tráfego da seguinte forma:

* Negar todo o tráfego para pod.
* Permitir o tráfego com base nas etiquetas de pod.
* Permitir o tráfego com base no espaço de nomes.

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Criar um cluster do AKS e ativar a política de rede

Política de rede pode ser ativada apenas quando o cluster ser criado. Não é possível ativar a política de rede num cluster do AKS existente. 

Para utilizar a política de rede com um cluster do AKS, tem de utilizar o [Plug-in do Azure CNI] [ azure-cni] e definir sua própria rede virtual e sub-redes. Para obter mais informações sobre como planear os intervalos de sub-rede obrigatório, consulte [configurar redes avançada][use-advanced-networking].

O script de exemplo seguinte:

* Cria uma rede e sub-rede virtual.
* Cria um Azure Active Directory (AD) principal de serviço para utilização com o cluster do AKS.
* Atribui *contribuinte* permissões para o AKS do cluster principal de serviço na rede virtual.
* Cria um cluster do AKS na rede virtual definida e ativa a política de rede.

Fornecer seu próprio segura *SP_PASSWORD*. Se assim o desejar, substitua a *RESOURCE_GROUP_NAME* e *CLUSTER_NAME* variáveis:

```azurecli-interactive
SP_PASSWORD=mySecurePassword
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP_ID=$(az ad sp create-for-rbac --password $SP_PASSWORD --skip-assignment --query [appId] -o tsv)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --kubernetes-version 1.12.4 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy calico
```

A criação do cluster demora alguns minutos. Quando terminar, configure `kubectl` para ligar ao seu cluster de Kubernetes com o [az aks get-credentials] [ az-aks-get-credentials] comando. Este comando transfere credenciais e configura a CLI do Kubernetes para utilizá-los:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Negar todo o tráfego de entrada para um pod

Antes de definir regras para permitir tráfego de rede específico, primeiro crie uma política de rede para negar todo o tráfego. Esta política dá-lhe um ponto de partida para começar, a lista de permissões, apenas o tráfego pretendido. Pode ver claramente também que o tráfego é ignorado quando é aplicada a política de rede.

Para nosso ambiente de aplicativo de exemplo e as regras de tráfego, primeiro vamos criar um namespace chamado *desenvolvimento* para executar nosso pods de exemplo:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Agora, crie um pod de back-end de exemplo que executa o NGINX. Este pod de back-end pode ser usado para simular um aplicativo de baseada na web de back-end de exemplo. Criar este pod no *desenvolvimento* espaço de nomes e abrir a porta *80* para servir o tráfego da web. Etiquetar o pod com *app = webapp, função = back-end* , de modo que podemos pode segmentá-la com uma política de rede na secção seguinte:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Para testar o que pode entrar com êxito a página de web NGINX predefinida, crie outro pod e anexar uma sessão de terminal:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Uma vez na linha de comandos de shell, utilize `wget` para confirmar que pode acessar a página de web NGINX predefinida:

```console
wget -qO- http://backend
```

A saída de exemplo seguinte mostra que a página de web NGINX predefinida devolvido:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saída de sessão de terminal anexada. O pod de teste é eliminado automaticamente:

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Criar e aplicar uma diretiva de rede

Agora que confirmou, que pode acessar a página de web NGINX básico no pod de back-end de exemplo, crie uma política de rede para negar todo o tráfego. Crie um ficheiro denominado `backend-policy.yaml` e cole o manifesto YAML seguinte. Esse manifesto utiliza um *podSelector* para anexar a política a pods que tenham o *app:webapp, função: back-end* etiqueta, tal como seu pod do NGINX de exemplo. Não foram definidas regras sob *entrada*, por isso, todo o tráfego de entrada para o pod é negado:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

Aplicar a política de rede com o [aplicam-se de kubectl] [ kubectl-apply] de comando e especifique o nome do seu manifesto YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testar a política de rede

Vamos ver se pode aceder novamente a página Web NGINX num pod do back-end. Crie outro teste pod e anexar uma sessão de terminal:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Uma vez na linha de comandos de shell, utilize `wget` para ver se pode acessar a página de web NGINX predefinida. Desta vez, definir um valor de tempo limite *2* segundos. A política de rede bloqueia agora todo o tráfego de entrada, pelo que não é possível carregar a página, conforme mostrado no exemplo a seguir:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Saída de sessão de terminal anexada. O pod de teste é eliminado automaticamente:

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Permitir tráfego de entrada com base numa etiqueta de pod

Na secção anterior, um pod do NGINX de back-end foi agendado e foi criada uma política de rede para negar todo o tráfego. Agora vamos criar um pod de front-end e atualizar a política de rede para permitir o tráfego de pods de front-end.

Atualizar a política de rede para permitir o tráfego de pods com as etiquetas *app:webapp, função: front-end* e em qualquer namespace. Editar anterior *policy.yaml de back-end* de ficheiros e adicionar um *matchLabels* regras de entrada para que seu manifesto é semelhante ao seguinte exemplo:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> Esta política de rede utiliza uma *namespaceSelector* e uma *podSelector* elemento para a regra de entrada. A sintaxe YAML é importante para as regras de entrada ser aditiva ou não. Neste exemplo, ambos os elementos têm de corresponder para a regra de entrada a ser aplicado. Kubernetes versões anteriores ao *1.12* não pode interpretar corretamente esses elementos e restringir o tráfego de rede conforme o esperado. Para obter mais informações, consulte [comportamento de e para seletores][policy-rules].

Aplicar a política de rede atualizado através da [kubectl aplicam-se] [ kubectl-apply] de comando e especifique o nome do seu manifesto YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

Agendar agora um pod assinalada como como *app = webapp, função = front-end* e anexar uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Uma vez na linha de comandos de shell, utilize `wget` para ver se pode acessar a página de web NGINX predefinida:

```console
wget -qO- http://backend
```

Como a entrada de regra permite o tráfego com pods que têm as etiquetas *aplicação: aplicação Web, função: front-end*, o tráfego do pod de front-end é permitido. O resultado de exemplo seguinte mostra a página de web NGINX predefinida devolvido:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saída de sessão de terminal anexada. O pod é eliminado automaticamente:

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Testar uma vagem sem uma etiqueta correspondente

A política de rede permite tráfego de pods rotulado *aplicação: aplicação Web, função: front-end*, mas deve negar a todos os outros tráfegos. Vamos testar que pod outro sem essas etiquetas não é possível acessar o pod do NGINX de back-end. Crie outro teste pod e anexar uma sessão de terminal:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Uma vez na linha de comandos de shell, utilize `wget` para ver se pode acessar a página de web NGINX predefinida. A política de rede bloqueia o tráfego de entrada, pelo que não é possível carregar a página, conforme mostrado no exemplo a seguir:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Saída de sessão de terminal anexada. O pod de teste é eliminado automaticamente:

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Permitir o tráfego apenas a partir de dentro de um espaço de nomes definido

Nos exemplos anteriores, criou uma política de rede que negado todo o tráfego, em seguida, atualizar a política para permitir o tráfego de pods com uma etiqueta específica. É uma necessidade comum limitar o tráfego para apenas dentro do espaço de nomes especificado. Se os exemplos anteriores foram para o tráfego num *desenvolvimento* espaço de nomes, pode querer, em seguida, criar uma política de rede que impede o tráfego a partir de outro espaço de nomes, tal como *produção*, cheguem a pods.

Primeiro, crie um novo espaço de nomes para simular um espaço de nomes de produção:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Agendar um pod de teste nos *produção* espaço de nomes que é identificado como *app = webapp, função = front-end*. Anexe uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Uma vez na linha de comandos de shell, utilize `wget` para confirmar que pode acessar a página de web NGINX predefinida:

```console
wget -qO- http://backend.development
```

Como as etiquetas para o pod coincidir com o que é atualmente permitido na política de rede, o tráfego é permitido. Os espaços de nomes, apenas as etiquetas de pod não ver a política de rede. O resultado de exemplo seguinte mostra a página de web NGINX predefinida devolvido:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saída de sessão de terminal anexada. O pod de teste é eliminado automaticamente:

```console
exit
```

### <a name="update-the-network-policy"></a>Atualizar a política de rede

Agora vamos atualizar a regra de entrada *namespaceSelector* secção para permitir apenas tráfego de dentro do *desenvolvimento* espaço de nomes. Editar a *policy.yaml de back-end* arquivo de manifesto, conforme mostrado no exemplo a seguir:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

Nos exemplos mais complexos, pode definir várias regras de entrada, como a utilizar um *namespaceSelector* e, em seguida, uma *podSelector*.

Aplicar a política de rede atualizado através da [kubectl aplicam-se] [ kubectl-apply] de comando e especifique o nome do seu manifesto YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testar a política de rede atualizado

Agendar agora outra pod no *produção* espaço de nomes e anexar uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Uma vez na linha de comandos de shell, utilize `wget` para ver a política de rede a negar o tráfego:

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

Sair do pod de teste:

```console
exit
```

Com o tráfego de impedido do *produção* espaço de nomes, criar uma agenda de agora um pod de teste no *desenvolvimento* espaço de nomes e anexar uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Uma vez na linha de comandos de shell, utilize `wget` para ver a rede política permitir o tráfego:

```console
wget -qO- http://backend
```

Como o pod está agendado no espaço de nomes que corresponde ao que é permitido na política de rede, o tráfego é permitido. O resultado de exemplo seguinte mostra a página de web NGINX predefinida devolvido:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saída de sessão de terminal anexada. O pod de teste é eliminado automaticamente:

```console
exit
```

## <a name="clean-up-resources"></a>Limpar recursos

Neste artigo, criamos dois espaços de nomes e aplicada uma política de rede. Para limpar esses recursos, utilize o [eliminar kubectl] [ kubectl-delete] de comando e especifique os nomes de recursos da seguinte forma:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre os recursos de rede, consulte [conceitos para aplicações no Azure Kubernetes Service (AKS) de rede][concepts-network].

Para saber mais sobre como utilizar políticas, consulte [as políticas de rede do Kubernetes][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
