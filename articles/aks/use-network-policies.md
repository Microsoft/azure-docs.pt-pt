---
title: Tráfego de pod seguro com política de rede
titleSuffix: Azure Kubernetes Service
description: Saiba como proteger o tráfego que flui dentro e fora das cápsulas utilizando as políticas de rede Kubernetes no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 7e494c6ac89289a9b271d16b871b8a22e1ca9e6a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83683203"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Tráfego seguro entre cápsulas utilizando políticas de rede no Serviço Azure Kubernetes (AKS)

Quando você corre aplicações modernas baseadas em microserviços em Kubernetes, você muitas vezes quer controlar quais componentes podem comunicar uns com os outros. O princípio do menor privilégio deve ser aplicado à forma como o tráfego pode fluir entre cápsulas num cluster do Serviço Azure Kubernetes (AKS). Digamos que provavelmente quer bloquear o tráfego diretamente para aplicações de back-end. A funcionalidade *De Política de Rede* em Kubernetes permite definir regras para o tráfego de entradas e saídas entre as cápsulas num cluster.

Este artigo mostra-lhe como instalar o motor de política de rede e criar políticas de rede Kubernetes para controlar o fluxo de tráfego entre cápsulas em AKS. A política de rede só deve ser utilizada para nós e cápsulas baseados em Linux em AKS.

## <a name="before-you-begin"></a>Before you begin

Precisa da versão 2.0.61 do Azure CLI ou posteriormente instalada e configurada. Corre  `az --version` para encontrar a versão. Se necessitar de instalar ou atualizar, consulte [instalar o Azure CLI][install-azure-cli].

> [!TIP]
> Se utilizar a função de política de rede durante a pré-visualização, recomendamos que [crie um novo cluster](#create-an-aks-cluster-and-enable-network-policy).
> 
> Se desejar continuar a utilizar os clusters de teste existentes que utilizaram a política de rede durante a pré-visualização, atualize o seu cluster para uma nova versão Kubernetes para a mais recente versão ga-mail e, em seguida, implemente o manifesto YAML seguinte para corrigir o servidor de métricas de colisão e o dashboard Kubernetes. Esta correção só é necessária para os clusters que utilizaram o motor de política da rede Calico.
>
> Como uma boa prática de segurança, [reveja o conteúdo deste manifesto YAML][calico-aks-cleanup] para entender o que é implantado no cluster AKS.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Visão geral da política de rede

Todas as cápsulas de um cluster AKS podem enviar e receber tráfego sem limitações, por padrão. Para melhorar a segurança, pode definir regras que controlam o fluxo de tráfego. As aplicações back-end são frequentemente expostas apenas a serviços frontais necessários, por exemplo. Ou, os componentes da base de dados só estão acessíveis aos níveis de aplicação que se ligam aos mesmos.

A Política de Rede é uma especificação de Kubernetes que define políticas de acesso para comunicação entre Pods. Utilizando as Políticas de Rede, define um conjunto de regras ordenadas para enviar e receber tráfego e aplicá-las a uma coleção de cápsulas que correspondem a um ou mais seletores de etiquetas.

Estas regras de política de rede são definidas como manifestos YAML. As políticas de rede podem ser incluídas como parte de um manifesto mais amplo que também cria uma implantação ou serviço.

### <a name="network-policy-options-in-aks"></a>Opções de política de rede em AKS

O Azure fornece duas formas de implementar a política de rede. Escolha uma opção de política de rede quando criar um cluster AKS. A opção política não pode ser alterada após a criação do cluster:

* A própria implementação do Azure, chamada *Políticas de Rede Azure.*
* *Calico Network Policies*, uma solução de segurança de rede e rede de código aberto fundada pela [Tigera.][tigera]

Ambas as implementações utilizam *IPTables* Linux para impor as políticas especificadas. As políticas são traduzidas em conjuntos de pares IP permitidos e não autorizados. Estes pares são então programados como regras de filtro IPTable.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Diferenças entre as políticas do Azure e do Calico e as suas capacidades

| Funcionalidade                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Plataformas suportadas                      | Linux                      | Linux                       |
| Opções de networking suportadas             | Azure CNI                  | Azure CNI e kubenet       |
| Conformidade com a especificação de Kubernetes | Todos os tipos de política suportados |  Todos os tipos de política suportados |
| Características adicionais                      | Nenhuma                       | Modelo de política alargado constituído por Global Network Policy, Global Network set e Host Endpoint. Para obter mais informações sobre a utilização do `calicoctl` CLI para gerir estas funcionalidades estendidas, consulte [a referência do utilizador calicoctl][calicoctl]. |
| Suporte                                  | Apoiado pela equipa de apoio e Engenharia da Azure | Apoio da comunidade calico. Para obter mais informações sobre apoio adicional pago, consulte [as opções de suporte do Projeto Calico.][calico-support] |
| Registo                                  | As regras adicionadas / eliminadas nos IPTables são registadas em todos os anfitriões em */var/log/azure-npm.log* | Para mais informações, consulte [os registos de componentes da Calico][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Crie um cluster AKS e permita a política de rede

Para ver as políticas de rede em ação, vamos criar e, em seguida, expandir sobre uma política que define o fluxo de tráfego:

* Negar todo o tráfego para a cápsula.
* Permitir o tráfego com base nas etiquetas das cápsulas.
* Permitir tráfego baseado no espaço de nomes.

Primeiro, vamos criar um cluster AKS que apoie a política de rede. 

> [!IMPORTANT]
>
> A função de política de rede só pode ser ativada quando o cluster é criado. Não é possível ativar a política de rede num cluster AKS existente.

Para utilizar a Política de Rede Azure, tem de utilizar o [plug-in Azure CNI][azure-cni] e definir a sua própria rede virtual e sub-redes. Para obter informações mais detalhadas sobre como planear as gamas de sub-redes necessárias, consulte [configurar a rede avançada][use-advanced-networking]. A Calico Network Policy pode ser utilizada com este mesmo plug-in Azure CNI ou com o plug-in CNI Kubenet.

O seguinte exemplo de script:

* Cria uma rede e sub-rede virtual.
* Cria um diretor de serviço Azure Ative (Azure AD) para utilização com o cluster AKS.
* Atribui permissões *de contribuinte* para o principal do serviço de cluster AKS na rede virtual.
* Cria um cluster AKS na rede virtual definida e permite a política de rede.
    * É utilizada a opção política _da Rede Azure._ Para utilizar o Calico como opção de política de rede, utilize o `--network-policy calico` parâmetro. Nota: O Calico pode ser utilizado com um `--network-plugin azure` ou `--network-plugin kubenet` .

Note que em vez de usar um principal de serviço, você pode usar uma identidade gerida para permissões. Para obter mais informações, consulte [utilização de identidades geridas.](use-managed-identity.md)

Forneça o seu próprio *SP_PASSWORD*seguro. Pode substituir as variáveis *RESOURCE_GROUP_NAME* e *CLUSTER_NAME:*

```azurecli-interactive
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
SP=$(az ad sp create-for-rbac --output json)
SP_ID=$(echo $SP | jq -r .appId)
SP_PASSWORD=$(echo $SP | jq -r .password)

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
# Enable network policy by using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy azure
```

A criação do cluster demora alguns minutos. Quando o cluster estiver pronto, configuure-se `kubectl` para ligar ao seu cluster Kubernetes utilizando o comando [az aks get-credentials.][az-aks-get-credentials] Este comando descarrega credenciais e configura o CLI de Kubernetes para usá-los:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Negar todo o tráfego de entrada para uma cápsula

Antes de definir regras para permitir o tráfego específico da rede, primeiro crie uma política de rede para negar todo o tráfego. Esta política dá-lhe um ponto de partida para começar a criar uma lista de permitis apenas para o tráfego desejado. Pode também ver claramente que o tráfego é abandonado quando a política da rede é aplicada.

Para o ambiente de aplicação da amostra e regras de tráfego, vamos primeiro criar um espaço de nome chamado *desenvolvimento* para executar as cápsulas de exemplo:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Crie um pod de back-end exemplo que executa o NGINX. Esta cápsula de back-end pode ser usada para simular uma aplicação baseada na web com base na amostra. Crie este casulo no espaço de *nomes de desenvolvimento* e abra a porta *80* para servir o tráfego web. Rotular o pod com *app=webapp,role=backend* para que possamos direcioná-lo com uma política de rede na secção seguinte:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Crie outra cápsula e anexe uma sessão de terminal para testar que pode alcançar com sucesso a página web padrão do NGINX:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Na operação de concha, utilize `wget` para confirmar que pode aceder à página web padrão do NGINX:

```console
wget -qO- http://backend
```

A seguinte saída da amostra mostra que a página web NGINX predefinida devolveu:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saia da sessão do terminal anexa. A cápsula de teste é automaticamente eliminada.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Criar e aplicar uma política de rede

Agora que confirmou que pode usar a página web básica do NGINX na cápsula de back-end da amostra, crie uma política de rede para negar todo o tráfego. Crie um ficheiro com o nome `backend-policy.yaml` e cole o seguinte manifesto YAML. Este manifesto utiliza um *podSelector* para anexar a política a cápsulas que têm a *app:webapp,role:backend* label, como a sua amostra NGINX pod. Não são *definidas regras sob entrada,* pelo que todo o tráfego de entrada para a cápsula é negado:

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

Vá [https://shell.azure.com](https://shell.azure.com) para abrir a Azure Cloud Shell no seu navegador.

Aplique a política de rede utilizando o comando [de aplicação de kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testar a política da rede

Vamos ver se consegues voltar a usar a página web do NGINX na cápsula de fundo. Crie outra cápsula de teste e anexe uma sessão terminal:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Na operação de concha, use `wget` para ver se consegue aceder à página web NGINX predefinido. Desta vez, desa um valor de tempo limite para *2* segundos. A política de rede bloqueia agora todo o tráfego de entrada, pelo que a página não pode ser carregada, como mostra o exemplo seguinte:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Saia da sessão do terminal anexa. A cápsula de teste é automaticamente eliminada.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Permitir o tráfego de entrada com base numa etiqueta de vagem

Na secção anterior, foi agendada uma cápsula NGINX de back-end, e foi criada uma política de rede para negar todo o tráfego. Vamos criar um pod frontal e atualizar a política de rede para permitir o tráfego a partir de cápsulas frontais.

Atualize a política de rede para permitir o tráfego de cápsulas com a app de *etiquetas:webapp,role:frontend* e em qualquer espaço de nome. Edite o ficheiro *backend-policy.yaml* anterior e adicione as regras de entrada de *matchLabels* para que o seu manifesto se pareça com o seguinte exemplo:

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
> Esta política de rede utiliza um *nomespaceSelector* e um elemento *podSelector* para a regra de entrada. A sintaxe YAML é importante para que as regras de entrada sejam aditivas. Neste exemplo, ambos os elementos devem corresponder à regra de entrada a aplicar. As versões Kubernetes antes do *1.12* podem não interpretar corretamente estes elementos e restringir o tráfego da rede como espera. Para mais informações sobre este comportamento, consulte [Comportamento de de e para os selecionadores][policy-rules].

Aplique a política de rede atualizada utilizando o comando [de aplicação de kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f backend-policy.yaml
```

Agende um casulo que está rotulado como *app=webapp,role=frontend* e anexar uma sessão terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Na operação de concha, use `wget` para ver se consegue aceder à página web padrão do NGINX:

```console
wget -qO- http://backend
```

Porque a regra de entrada permite o tráfego com pods que têm a app de *etiquetas: webapp,role: frontend*, o tráfego da cápsula frontal é permitido. A saída de exemplo a seguir mostra a página web NGINX padrão devolvida:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saia da sessão do terminal anexa. A cápsula é automaticamente eliminada.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Teste uma cápsula sem uma etiqueta correspondente

A política de rede permite o tráfego a partir de cápsulas de *aplicação rotuladas: webapp,role: frontend*, mas deve negar todo o tráfego. Vamos testar se outra cápsula sem essas etiquetas pode aceder à cápsula NGINX de fundo. Crie outra cápsula de teste e anexe uma sessão terminal:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Na operação de concha, use `wget` para ver se consegue aceder à página web NGINX predefinido. A política de rede bloqueia o tráfego de entrada, para que a página não possa ser carregada, como mostra o exemplo seguinte:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Saia da sessão do terminal anexa. A cápsula de teste é automaticamente eliminada.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Permitir o tráfego apenas a partir de um espaço de nome definido

Nos exemplos anteriores, criou uma política de rede que negou todo o tráfego e, em seguida, atualizou a política para permitir o tráfego de cápsulas com um rótulo específico. Outra necessidade comum é limitar o tráfego apenas dentro de um determinado espaço de nome. Se os exemplos anteriores eram para o tráfego num espaço de nome de *desenvolvimento,* criar uma política de rede que impeça o tráfego de outro espaço de nome, como *a produção,* de chegar às cápsulas.

Primeiro, crie um novo espaço de nome para simular um espaço de nome de produção:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Agende uma cápsula de teste no espaço de nome de *produção* que é rotulado como *app=webapp,role=frontend*. Anexar uma sessão terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Na operação de concha, utilize `wget` para confirmar que pode aceder à página web padrão do NGINX:

```console
wget -qO- http://backend.development
```

Uma vez que as etiquetas para a cápsula correspondem ao que é atualmente permitido na política da rede, o tráfego é permitido. A política da rede não olha para os espaços de nome, apenas para as etiquetas das cápsulas. A saída de exemplo a seguir mostra a página web NGINX padrão devolvida:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saia da sessão do terminal anexa. A cápsula de teste é automaticamente eliminada.

```console
exit
```

### <a name="update-the-network-policy"></a>Atualizar a política de rede

Vamos atualizar a secção de *nomes de ingresssspaceSelector* para permitir apenas o tráfego a partir do espaço de nome de *desenvolvimento.* Edite o ficheiro manifesto *backend policy.yaml* como mostrado no seguinte exemplo:

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

Em exemplos mais complexos, pode-se definir várias regras de entrada, como um *nomespaceSelector* e, em seguida, um *podSelector*.

Aplique a política de rede atualizada utilizando o comando [de aplicação de kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testar a política de rede atualizada

Agende outra cápsula no espaço de nome de *produção* e anexe uma sessão terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Na origem da concha, use `wget` para ver que a política da rede agora nega o tráfego:

```console
wget -qO- --timeout=2 http://backend.development
```

```output
wget: download timed out
```

Sair da cápsula de teste:

```console
exit
```

Com o tráfego negado do espaço de nome de *produção,* agende uma cápsula de teste de volta no espaço de nome de *desenvolvimento* e anexe uma sessão terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Na solicitação da concha, utilize `wget` para ver se a política da rede permite o tráfego:

```console
wget -qO- http://backend
```

O tráfego é permitido porque o casulo está programado no espaço de nomes que corresponde ao permitido na política de rede. A seguinte saída de amostra mostra a página web NGINX padrão devolvida:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saia da sessão do terminal anexa. A cápsula de teste é automaticamente eliminada.

```console
exit
```

## <a name="clean-up-resources"></a>Limpar recursos

Neste artigo, criámos dois espaços de nome e aplicámos uma política de rede. Para limpar estes recursos, utilize o [comando de eliminação de kubectl][kubectl-delete] e especifique os nomes dos recursos:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre os recursos de rede, consulte [conceitos de Rede para aplicações no Serviço Azure Kubernetes (AKS)][concepts-network].

Para saber mais sobre políticas, consulte as [políticas de rede de Kubernetes.][kubernetes-network-policies]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/maintenance/troubleshoot/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-azure-cni.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
