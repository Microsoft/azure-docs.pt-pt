---
title: Proteger pods com políticas de rede no serviço de kubernetes do Azure (AKS)
description: Saiba como proteger o tráfego que flui para dentro e fora do pods usando as políticas de rede kubernetes no serviço kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: c9bf2c2c459999813c7fc30f95be653168d270ad
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "67613957"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Proteger o tráfego entre pods usando as políticas de rede no serviço de kubernetes do Azure (AKS)

Quando você executa aplicativos modernos baseados em microserviços no kubernetes, muitas vezes você deseja controlar quais componentes podem se comunicar entre si. O princípio de privilégios mínimos deve ser aplicado a como o tráfego pode fluir entre os pods em um cluster AKS (serviço de kubernetes do Azure). Digamos que você provavelmente desejará bloquear o tráfego diretamente para aplicativos de back-end. O recurso de *política de rede* no kubernetes permite que você defina regras para tráfego de entrada e saída entre pods em um cluster.

Este artigo mostra como instalar o mecanismo de política de rede e criar políticas de rede kubernetes para controlar o fluxo de tráfego entre pods em AKS. A política de rede deve ser usada somente para nós baseados em Linux e pods em AKS.

## <a name="before-you-begin"></a>Antes de começar

Você precisa do CLI do Azure versão 2.0.61 ou posterior instalado e configurado. Execute `az --version` para localizar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure][install-azure-cli].

> [!TIP]
> Se você usou o recurso de política de rede durante a visualização, recomendamos que você [crie um novo cluster](#create-an-aks-cluster-and-enable-network-policy).
> 
> Se você quiser continuar usando os clusters de teste existentes que usaram a política de rede durante a visualização, atualize o cluster para uma nova versão do kubernetes para o lançamento de GA mais recente e, em seguida, implante o seguinte manifesto do YAML para corrigir o servidor de métricas com falha e kubernetes painéis. Essa correção só é necessária para clusters que usavam o mecanismo de política de rede Calico.
>
> Como prática recomendada de segurança, [examine o conteúdo desse manifesto do YAML][calico-aks-cleanup] para entender o que é implantado no cluster AKs.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Visão geral da política de rede

Todos os pods em um cluster AKS podem enviar e receber tráfego sem limitações, por padrão. Para melhorar a segurança, você pode definir regras que controlam o fluxo de tráfego. Os aplicativos de back-end geralmente são expostos apenas aos serviços front-end necessários, por exemplo. Ou, os componentes de banco de dados só são acessíveis para as camadas de aplicativo que se conectam a eles.

A política de rede é uma especificação kubernetes que define políticas de acesso para comunicação entre pods. Usando as políticas de rede, você define um conjunto ordenado de regras para enviar e receber tráfego e aplicá-las a uma coleção de pods que correspondem a um ou mais seletores de rótulo.

Essas regras de política de rede são definidas como manifestos YAML. As políticas de rede podem ser incluídas como parte de um manifesto mais amplo que também cria uma implantação ou um serviço.

### <a name="network-policy-options-in-aks"></a>Opções de política de rede no AKS

O Azure fornece duas maneiras de implementar a diretiva de rede. Você escolhe uma opção de política de rede ao criar um cluster AKS. A opção de política não pode ser alterada após a criação do cluster:

* Implementação do Azure, chamada de *políticas de rede do Azure*.
* *Calico políticas de rede*, uma solução de segurança de rede e rede de software [][tigera]livre fundada por tigera.

Ambas as implementações usam *iptables* do Linux para impor as políticas especificadas. As políticas são convertidas em conjuntos de pares de IP permitidos e não permitidos. Esses pares são programados como regras de filtro IPTable.

A política de rede funciona apenas com a opção CNI do Azure (avançado). A implementação é diferente para as duas opções:

* *Políticas de rede do Azure* – o CNI do Azure configura uma ponte no host da VM para a rede entre nós. As regras de filtragem são aplicadas quando os pacotes passam pela ponte.
* *Calico políticas de rede* – o CNI do Azure configura rotas de kernel locais para o tráfego intra-node. As políticas são aplicadas na interface de rede do pod.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Diferenças entre as políticas do Azure e do Calico e seus recursos

| Funcionalidade                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Plataformas suportadas                      | Linux                      | Linux                       |
| Opções de rede com suporte             | Azure CNI                  | Azure CNI                   |
| Conformidade com a especificação kubernetes | Todos os tipos de política com suporte |  Todos os tipos de política com suporte |
| Funcionalidades adicionais                      | Nenhum                       | Modelo de política estendida que consiste em política de rede global, conjunto de rede global e ponto de extremidade do host. Para obter mais informações sobre como `calicoctl` usar a CLI para gerenciar esses recursos estendidos, consulte [calicoctl User Reference][calicoctl]. |
| Suporte                                  | Suporte da equipe de suporte e engenharia do Azure | Suporte da Comunidade Calico. Para obter mais informações sobre suporte pago adicional, consulte [Opções de suporte do Project Calico][calico-support]. |
| Registo                                  | As regras adicionadas/excluídas no IPTables são registradas em todos os hosts em */var/log/Azure-NPM.log* | Para obter mais informações, consulte [Calico Component logs][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Criar um cluster AKS e habilitar a política de rede

Para ver as políticas de rede em ação, vamos criar e, em seguida, expandir uma política que define o fluxo de tráfego:

* Negar todo o tráfego para o pod.
* Permitir o tráfego com base em rótulos de Pod.
* Permitir tráfego com base no namespace.

Primeiro, vamos criar um cluster AKS que dê suporte à política de rede. O recurso de política de rede só pode ser habilitado quando o cluster é criado. Não é possível habilitar a política de rede em um cluster AKS existente.

Para usar a política de rede com um cluster AKS, você deve usar o [plug-in do CNI do Azure][azure-cni] e definir sua própria rede virtual e sub-redes. Para obter informações mais detalhadas sobre como planejar os intervalos de sub-rede necessários, consulte [Configurar a rede avançada][use-advanced-networking].

O script de exemplo a seguir:

* Cria uma rede e sub-rede virtual.
* Cria uma entidade de serviço Azure Active Directory (AD do Azure) para uso com o cluster AKS.
* Atribui permissões de *colaborador* para a entidade de serviço de cluster AKs na rede virtual.
* Cria um cluster AKS na rede virtual definida e habilita a política de rede.
    * A opção de política de rede *do Azure* é usada. Para usar o Calico como a opção de política de rede, `--network-policy calico` use o parâmetro.

Forneça seu próprio *SP_PASSWORD*seguro. Você pode substituir as variáveis *RESOURCE_GROUP_NAME* e *CLUSTER_NAME* :

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

A criação do cluster demora alguns minutos. Quando o cluster estiver pronto, configure `kubectl` o para se conectar ao cluster do kubernetes usando o comando [AZ AKs Get-Credentials][az-aks-get-credentials] . Este comando baixa as credenciais e configura a CLI do kubernetes para usá-las:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Negar todo o tráfego de entrada a um pod

Antes de definir regras para permitir tráfego de rede específico, primeiro crie uma política de rede para negar todo o tráfego. Essa política oferece um ponto de partida para começar a listar somente o tráfego desejado. Você também pode ver claramente que o tráfego é Descartado quando a diretiva de rede é aplicada.

Para o ambiente de aplicativo de exemplo e as regras de tráfego, vamos primeiro criar um namespace chamado *Development* para executar o pods de exemplo:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Crie um pod de back-end de exemplo que execute NGINX. Esse pod de back-end pode ser usado para simular um aplicativo de back-end baseado na Web de exemplo. Crie esse pod no namespace de *desenvolvimento* e abra a porta *80* para fornecer o tráfego da Web. Rotule o Pod com *app = webapp, role = backend* para que possamos direcioná-lo com uma política de rede na próxima seção:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Crie outro pod e anexe uma sessão de terminal para testar que você pode acessar com êxito a página da Web NGINX padrão:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

No prompt do Shell, use `wget` para confirmar que você pode acessar a página da Web padrão do Nginx:

```console
wget -qO- http://backend
```

A seguinte saída de exemplo mostra que a página da Web NGINX padrão retornou:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Sair da sessão de terminal anexada. O pod de teste é excluído automaticamente.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Criar e aplicar uma política de rede

Agora que você confirmou que pode usar a página da Web NGINX básica no pod de back-end de exemplo, crie uma política de rede para negar todo o tráfego. Crie um arquivo chamado `backend-policy.yaml` e cole o seguinte manifesto YAML. Este manifesto usa um *podSelector* para anexar a política a pods que têm o rótulo *App: WebApp, role: backend* , como seu pod de Nginx de exemplo. Nenhuma regra é definida sob *entrada*, portanto, todo o tráfego de entrada para o pod é negado:

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

Aplique a política de rede usando o comando [kubectl Apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testar a política de rede


Vamos ver se você pode usar a página da Web NGINX no pod de back-end novamente. Criar outro pod de teste e anexar uma sessão de terminal:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

No prompt do Shell, use `wget` para ver se você pode acessar a página da Web padrão do nginx. Desta vez, defina um valor de tempo limite como *2* segundos. A política de rede agora bloqueia todo o tráfego de entrada, portanto, a página não pode ser carregada, conforme mostrado no exemplo a seguir:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Sair da sessão de terminal anexada. O pod de teste é excluído automaticamente.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Permitir tráfego de entrada com base em um rótulo de Pod

Na seção anterior, um pod de NGINX de back-end foi agendado e uma política de rede foi criada para negar todo o tráfego. Vamos criar um pod de front-end e atualizar a política de rede para permitir o tráfego de pods de front-end.

Atualize a política de rede para permitir o tráfego de pods com os rótulos *App: WebApp, role: frontend* e em qualquer namespace. Edite o arquivo *. YAML de política de back-end* anterior e adicione regras de entrada do *matchLabels* para que o manifesto seja semelhante ao exemplo a seguir:

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
> Essa política de rede usa um *namespaceSelector* e um elemento *podSelector* para a regra de entrada. A sintaxe YAML é importante para que as regras de entrada sejam aditivas. Neste exemplo, os dois elementos devem corresponder à regra de entrada a ser aplicada. As versões do kubernetes anteriores ao *1,12* podem não interpretar esses elementos corretamente e restringir o tráfego de rede conforme o esperado. Para obter mais informações sobre esse comportamento, consulte [comportamento de para e de seletores][policy-rules].

Aplique a política de rede atualizada usando o comando [kubectl Apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

Agende um pod que seja rotulado como *app = webapp, role = frontend* e anexe uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

No prompt do Shell, use `wget` para ver se você pode acessar a página da Web padrão do Nginx:

```console
wget -qO- http://backend
```

Como a regra de entrada permite o tráfego com pods que têm o *aplicativo rótulos: WebApp, role: frontend*, o tráfego do pod de front-end é permitido. A saída de exemplo a seguir mostra a página da Web NGINX padrão retornada:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Sair da sessão de terminal anexada. O pod é excluído automaticamente.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Testar um pod sem um rótulo correspondente

A política de rede permite o tráfego de pods rotulado *App: WebApp, role: frontend*, mas deve negar todos os outros tráfegos. Vamos testar para ver se outro Pod sem esses rótulos pode acessar o Pod NGINX de back-end. Criar outro pod de teste e anexar uma sessão de terminal:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

No prompt do Shell, use `wget` para ver se você pode acessar a página da Web padrão do nginx. A política de rede bloqueia o tráfego de entrada, portanto, a página não pode ser carregada, conforme mostrado no exemplo a seguir:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Sair da sessão de terminal anexada. O pod de teste é excluído automaticamente.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Permitir tráfego somente de dentro de um namespace definido

Nos exemplos anteriores, você criou uma política de rede que negou todo o tráfego e, em seguida, atualizou a política para permitir o tráfego de pods com um rótulo específico. Outra necessidade comum é limitar o tráfego somente dentro de um namespace específico. Se os exemplos anteriores eram para o tráfego em um namespace de *desenvolvimento* , crie uma política de rede que impeça o tráfego de outro namespace, como a *produção*, de alcançar o pods.

Primeiro, crie um novo namespace para simular um namespace de produção:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Agende um pod de teste no namespace de *produção* que é rotulado como *app = webapp, role = frontend*. Anexar uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

No prompt do Shell, use `wget` para confirmar que você pode acessar a página da Web padrão do Nginx:

```console
wget -qO- http://backend.development
```

Como os rótulos para o Pod correspondem ao que atualmente é permitido na política de rede, o tráfego é permitido. A política de rede não examina os namespaces, somente os rótulos de Pod. A saída de exemplo a seguir mostra a página da Web NGINX padrão retornada:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Sair da sessão de terminal anexada. O pod de teste é excluído automaticamente.

```console
exit
```

### <a name="update-the-network-policy"></a>Atualizar a política de rede

Vamos atualizar a seção *namespaceSelector* da regra de entrada para permitir somente o tráfego de dentro do namespace de *desenvolvimento* . Edite o arquivo de manifesto *back-end-Policy. YAML* , conforme mostrado no exemplo a seguir:

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

Em exemplos mais complexos, você pode definir várias regras de entrada, como um *namespaceSelector* e, em seguida, um *podSelector*.

Aplique a política de rede atualizada usando o comando [kubectl Apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testar a política de rede atualizada

Agende outro pod no namespace de *produção* e anexe uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

No prompt do Shell, use `wget` para ver que a política de rede agora nega o tráfego:

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

Saia do pod de teste:

```console
exit
```

Com o tráfego negado do namespace de *produção* , agende um pod de teste de volta no namespace de *desenvolvimento* e anexe uma sessão de terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

No prompt do Shell, use `wget` para ver que a política de rede permite o tráfego:

```console
wget -qO- http://backend
```

O tráfego é permitido porque o Pod está agendado no namespace que corresponde ao que é permitido na política de rede. A seguinte saída de exemplo mostra a página da Web NGINX padrão retornada:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Sair da sessão de terminal anexada. O pod de teste é excluído automaticamente.

```console
exit
```

## <a name="clean-up-resources"></a>Limpar recursos

Neste artigo, criamos dois namespaces e aplicamos uma diretiva de rede. Para limpar esses recursos, use o comando [kubectl Delete][kubectl-delete] e especifique os nomes dos recursos:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os recursos de rede, consulte [conceitos de rede para aplicativos no serviço de kubernetes do Azure (AKs)][concepts-network].

Para saber mais sobre políticas, consulte [kubernetes Network Policies][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.6/reference/calicoctl/
[calico-support]: https://www.projectcalico.org/support
[calico-logs]: https://docs.projectcalico.org/v3.6/maintenance/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
