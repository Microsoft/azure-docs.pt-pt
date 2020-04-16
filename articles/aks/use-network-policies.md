---
title: Tráfego de casulos seguros com política de rede
titleSuffix: Azure Kubernetes Service
description: Saiba como proteger o tráfego que flui dentro e fora das cápsulas utilizando as políticas de rede Kubernetes no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: a2794f53407be3ce3d7e69caa8039c13217a0356
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392620"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Tráfego seguro entre cápsulas utilizando políticas de rede no Serviço Azure Kubernetes (AKS)

Quando executa aplicações modernas baseadas em microserviços em Kubernetes, muitas vezes quer controlar quais os componentes que podem comunicar uns com os outros. O princípio do menor privilégio deve ser aplicado à forma como o tráfego pode fluir entre cápsulas num cluster do Serviço Azure Kubernetes (AKS). Digamos que provavelmente quer bloquear o tráfego diretamente para aplicações de back-end. A funcionalidade *Política de Rede* em Kubernetes permite definir regras para ingresse e esgres tráfego entre cápsulas num cluster.

Este artigo mostra-lhe como instalar o motor de política de rede e criar políticas de rede Kubernetes para controlar o fluxo de tráfego entre cápsulas em AKS. A política de rede só deve ser utilizada para nódos e pods baseados em Linux no AKS.

## <a name="before-you-begin"></a>Antes de começar

Precisa da versão Azure CLI 2.0.61 ou posteriormente instalada e configurada. Corra `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI][install-azure-cli].

> [!TIP]
> Se utilizou a funcionalidade de política de rede durante a pré-visualização, recomendamos que [crie um novo cluster](#create-an-aks-cluster-and-enable-network-policy).
> 
> Se desejar continuar a utilizar os clusters de teste existentes que utilizaram a política de rede durante a pré-visualização, atualize o seu cluster para uma nova versão Kubernetes para a mais recente versão gaG e, em seguida, implemente o seguinte manifesto YAML para corrigir o servidor de métricas de colisão e o dashboard Kubernetes. Esta correção só é necessária para clusters que utilizaram o motor de política da rede Calico.
>
> Como uma boa prática de segurança, [reveja o conteúdo deste manifesto YAML][calico-aks-cleanup] para entender o que é implantado no cluster AKS.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Visão geral da política de rede

Todas as cápsulas de um cluster AKS podem enviar e receber tráfego sem limitações, por defeito. Para melhorar a segurança, pode definir regras que controlam o fluxo de tráfego. As aplicações de back-end são frequentemente expostas apenas aos serviços frontais necessários, por exemplo. Ou, os componentes da base de dados só são acessíveis aos níveis de aplicação que lhes ligam.

A Política de Rede é uma especificação da Kubernetes que define as políticas de acesso à comunicação entre Pods. Utilizando políticas de rede, define um conjunto ordenado de regras para enviar e receber tráfego e aplicá-las a uma coleção de cápsulas que correspondam a um ou mais selecionadores de etiquetas.

Estas regras de política de rede são definidas como manifestos YAML. As políticas de rede podem ser incluídas como parte de um manifesto mais amplo que também cria uma implantação ou serviço.

### <a name="network-policy-options-in-aks"></a>Opções de política de rede em AKS

O Azure fornece duas formas de implementar a política de rede. Escolhe uma opção de política de rede quando cria um cluster AKS. A opção política não pode ser alterada após a criação do cluster:

* A própria implementação do Azure, chamada *Políticas de Rede Azure.*
* *Calico Network Policies*, uma rede de código aberto e solução de segurança de rede fundada pela [Tigera.][tigera]

Ambas as implementações utilizam *IPTables* Linux para impor as políticas especificadas. As políticas são traduzidas em conjuntos de pares IP permitidos e proibidos. Estes pares são então programados como regras de filtro IPTable.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Diferenças entre as políticas de Azure e Calico e as suas capacidades

| Capacidade                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Plataformas suportadas                      | Linux                      | Linux                       |
| Opções de networking suportadas             | Azure CNI                  | Azure CNI e kubenet       |
| Conformidade com a especificação kubernetes | Todos os tipos de políticas suportados |  Todos os tipos de políticas suportados |
| Características adicionais                      | Nenhuma                       | Modelo de política alargado composto por Global Network Policy, Global Network set e Host Endpoint. Para obter mais `calicoctl` informações sobre a utilização do CLI para gerir estas funcionalidades estendidas, consulte a [referência do utilizador calicoctl][calicoctl]. |
| Suporte                                  | Apoiado pela equipa de apoio e engenharia do Azure | Apoio comunitário de Calico. Para obter mais informações sobre apoio pago adicional, consulte as opções de suporte do [Project Calico.][calico-support] |
| Registo                                  | As regras adicionadas /eliminadas nos IPTables são registadas em cada anfitrião em */var/log/azure-npm.log.log* | Para mais informações, consulte os registos de [componentes da Calico][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Criar um cluster AKS e ativar a política de rede

Para ver as políticas de rede em ação, vamos criar e depois expandir uma política que define o fluxo de tráfego:

* Negue todo o tráfego para a cápsula.
* Permitir o tráfego com base nas etiquetas da cápsula.
* Permitir tráfego com base no espaço de nome.

Primeiro, vamos criar um cluster AKS que apoie a política de rede. 

> [!IMPORTANT]
>
> A função de política de rede só pode ser ativada quando o cluster é criado. Não se pode ativar a política de rede num cluster AKS existente.

Para utilizar a Política de Rede Azure, deve utilizar o [plug-in Azure CNI][azure-cni] e definir a sua própria rede virtual e subredes. Para obter informações mais detalhadas sobre como planear as gamas de sub-redes necessárias, consulte a [configuração da rede avançada][use-advanced-networking]. A Política de Rede Calico pode ser utilizada com este mesmo plug-in Azure CNI ou com o plug-in Kubenet CNI.

O seguinte exemplo script:

* Cria uma rede e sub-rede virtual.
* Cria um diretor de serviço Azure Ative Directory (Azure AD) para utilização com o cluster AKS.
* Atribui permissões *ao Colaborador* para o principal do serviço de cluster AKS na rede virtual.
* Cria um cluster AKS na rede virtual definida e permite a política de rede.
    * A opção de política de rede *azure* é utilizada. Para utilizar o Calico como opção `--network-policy calico` de política de rede, utilize o parâmetro. Nota: Calico pode ser `--network-plugin azure` `--network-plugin kubenet`utilizado com qualquer um ou .

Note que em vez de utilizar um diretor de serviço, pode utilizar uma identidade gerida para obter permissões. Para mais informações, consulte [Use identidades geridas](use-managed-identity.md).

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

A criação do cluster demora alguns minutos. Quando o cluster estiver `kubectl` pronto, configure para se ligar ao seu cluster Kubernetes utilizando o comando de [obter credenciais az aks.][az-aks-get-credentials] Este comando descarrega credenciais e confunde o ClI Kubernetes para usá-las:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Negue todo o tráfego de entrada para uma cápsula

Antes de definir regras para permitir tráfego específico de rede, primeiro crie uma política de rede para negar todo o tráfego. Esta política dá-lhe um ponto de partida para começar a branquear apenas o tráfego desejado. Também é evidente que o tráfego é reduzido quando a política de rede é aplicada.

Para o ambiente de aplicação da amostra e regras de tráfego, vamos primeiro criar um espaço de nome chamado *desenvolvimento* para executar as cápsulas de exemplo:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Crie um exemplo de casulo de back-end que executa o NGINX. Esta cápsula de back-end pode ser usada para simular uma aplicação baseada na web de volta. Crie este casulo no espaço de nome de *desenvolvimento* e abra a porta *80* para servir o tráfego web. Rotule o casulo com *app=webapp,role=backend* para que possamos direcioná-lo com uma política de rede na secção seguinte:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Crie outra cápsula e fixe uma sessão terminal para testar que pode chegar com sucesso à página web padrão ngINX:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Na solicitação da `wget` concha, utilize para confirmar que pode aceder à página web padrão ngINX:

```console
wget -qO- http://backend
```

A seguinte saída de amostra mostra que a página web padrão ngINX devolveu:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saia da sessão terminal anexa. A cápsula de teste é automaticamente eliminada.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Criar e aplicar uma política de rede

Agora que confirmou que pode usar a página básica do NGINX na cápsula de back-end da amostra, crie uma política de rede para negar todo o tráfego. Crie um `backend-policy.yaml` ficheiro nomeado e cola o seguinte manifesto YAML. Este manifesto utiliza um *podSelector* para anexar a política a cápsulas que tenham a *etiqueta app:webapp,role:backend,* como a sua amostra NGINX pod. Não são definidas regras ao abrigo da *entrada,* pelo que todo o tráfego de entrada para a cápsula é negado:

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

Vá [https://shell.azure.com](https://shell.azure.com) abrir a Azure Cloud Shell no seu navegador.

Aplique a política de rede utilizando o comando de aplicação do [kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testar a política de rede

Vamos ver se pode usar a página web NGINX na cápsula de back-end novamente. Crie outra cápsula de ensaio e fixe uma sessão terminal:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Na solicitação da `wget` concha, utilize para ver se consegue aceder à página web padrão ngINX. Desta vez, detete um valor de tempo para *2* segundos. A política de rede bloqueia agora todo o tráfego de entrada, por isso a página não pode ser carregada, como mostra o seguinte exemplo:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Saia da sessão terminal anexa. A cápsula de teste é automaticamente eliminada.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Permitir tráfego de entrada com base numa etiqueta de pod

Na secção anterior, estava programado um casulo NGINX de back-end, e foi criada uma política de rede para negar todo o tráfego. Vamos criar um casulo frontal e atualizar a política de rede para permitir o tráfego a partir de cápsulas frontais.

Atualize a política de rede para permitir o tráfego de pods com a *app de etiquetas:webapp,role:frontend* e em qualquer espaço de nome. Editar o ficheiro *backend-policy.yaml* anterior e adicionar regras de entrada *matchLabels* para que o seu manifesto se pareça com o seguinte exemplo:

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
> Esta política de rede utiliza um *espaço de nome Seletor* e um elemento *podSelector* para a regra de entrada. A sintaxe YAML é importante para que as regras de ingresso sejam aditivas. Neste exemplo, ambos os elementos devem coincidir com a aplicação da regra de ingresso. As versões Kubernetes antes do *1.12* podem não interpretar corretamente estes elementos e restringir o tráfego de rede como espera. Para mais informações sobre este comportamento, consulte [Comportamento de e para selecionadores.][policy-rules]

Aplique a política de rede atualizada utilizando o comando de aplicação do [kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f backend-policy.yaml
```

Agende um casulo que seja rotulado como *app=webapp,role=frontend* e fixe uma sessão terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Na solicitação da `wget` concha, utilize para ver se consegue aceder à página web padrão nginx:

```console
wget -qO- http://backend
```

Porque a regra de ingresso permite o tráfego com cápsulas que têm a app de *etiquetas: webapp,role: frontend*, o tráfego da cápsula frontal é permitido. A saída de exemplo seguinte mostra a página web padrão NGINX devolvida:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saia da sessão terminal anexa. A cápsula é automaticamente eliminada.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Teste uma cápsula sem etiqueta correspondente

A política de rede permite o tráfego de cápsulas rotuladas *de aplicação: webapp,role: frontend*, mas deve negar todo o tráfego. Vamos testar se outra cápsula sem essas etiquetas pode aceder à cápsula NGINX de back-end. Crie outra cápsula de ensaio e fixe uma sessão terminal:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Na solicitação da `wget` concha, utilize para ver se consegue aceder à página web padrão ngINX. A política de rede bloqueia o tráfego de entrada, por isso a página não pode ser carregada, como mostra o seguinte exemplo:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Saia da sessão terminal anexa. A cápsula de teste é automaticamente eliminada.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Permitir o tráfego apenas a partir de um espaço de nome definido

Nos exemplos anteriores, criou uma política de rede que negasse todo o tráfego e, em seguida, atualizou a política para permitir o tráfego de casulos com um rótulo específico. Outra necessidade comum é limitar o tráfego apenas dentro de um determinado espaço de nome. Se os exemplos anteriores fossem para o tráfego num espaço de nome de *desenvolvimento,* criar uma política de rede que impeça o tráfego de outro espaço de nome, como a *produção,* de chegar às cápsulas.

Primeiro, crie um novo espaço de nome para simular um espaço de nome de produção:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Agende uma cápsula de teste no espaço de nome de *produção* que seja rotulado como *app=webapp,role=frontend*. Fixe uma sessão terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Na solicitação da `wget` concha, utilize para confirmar que pode aceder à página web padrão ngINX:

```console
wget -qO- http://backend.development
```

Uma vez que as etiquetas para o casulo correspondem ao que é atualmente permitido na política de rede, o tráfego é permitido. A política de rede não olha para os espaços de nome, apenas para as etiquetas de pod. A saída de exemplo seguinte mostra a página web padrão NGINX devolvida:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saia da sessão terminal anexa. A cápsula de teste é automaticamente eliminada.

```console
exit
```

### <a name="update-the-network-policy"></a>Atualizar a política de rede

Vamos atualizar a secção de *nome sinuoso* de regras de ingresso para permitir apenas o tráfego dentro do espaço de nome de *desenvolvimento.* Editar o ficheiro manifesto *backend-policy.yaml,* como mostra o seguinte exemplo:

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

Em exemplos mais complexos, pode definir várias regras de ingresso, como um *seletor* de espaço de nome e, em seguida, um *podSelector*.

Aplique a política de rede atualizada utilizando o comando de aplicação do [kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testar a política de rede atualizada

Agende outra cápsula no espaço de nome de *produção* e prenda uma sessão terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

No pedido de `wget` concha, use para ver que a política da rede agora nega o tráfego:

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

Com o tráfego negado do espaço de nome de *produção,* agende uma cápsula de teste de volta no espaço de nome de *desenvolvimento* e prenda uma sessão terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Na solicitação do `wget` reservatório, utilize para ver se a política de rede permite o tráfego:

```console
wget -qO- http://backend
```

O tráfego é permitido porque a cápsula está programada no espaço de nome que corresponde ao permitido na política de rede. A seguinte saída de amostra mostra a página web padrão NGINX devolvida:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Saia da sessão terminal anexa. A cápsula de teste é automaticamente eliminada.

```console
exit
```

## <a name="clean-up-resources"></a>Limpar recursos

Neste artigo, criámos dois espaços de nome e aplicámos uma política de rede. Para limpar estes recursos, utilize o [kubectl eliminar][kubectl-delete] o comando e especificar os nomes de recursos:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre os recursos de rede, consulte conceitos de [rede para aplicações no Serviço Azure Kubernetes (AKS)][concepts-network].

Para saber mais sobre as políticas, consulte as políticas de [rede kubernetes.][kubernetes-network-policies]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.9/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/v3.9/maintenance/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
