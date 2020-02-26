---
title: Personalize o CoreDNS para o Serviço Azure Kubernetes (AKS)
description: Aprenda a personalizar o CoreDNS para adicionar subdomínios ou estender pontos finais dNS personalizados utilizando o Serviço Azure Kubernetes (AKS)
services: container-service
author: jnoller
ms.topic: article
ms.date: 03/15/2019
ms.author: jenoller
ms.openlocfilehash: 78132a53313f4a8ee5c10af340c8dab08c3e42c2
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595829"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Personalize coreDNS com serviço Azure Kubernetes

O Azure Kubernetes Service (AKS) utiliza o projeto [CoreDNS][coredns] para gestão e resolução de DNS de cluster com todos os *clusters 1.12.x* e superiores. Anteriormente, o projeto kube-dns foi usado. Este projeto kube-dns está agora depreciado. Para obter mais informações sobre a personalização do CoreDNS e kubernetes, consulte a [documentação oficial][corednsk8s]a montante .

Como o AKS é um serviço gerido, não é possível modificar a configuração principal para CoreDNS (um *CoreFile*). Em vez disso, utiliza um Kubernetes *ConfigMap* para anular as definições predefinidas. Para ver o comando padrão AKS CoreDNS ConfigMaps, utilize o comando `kubectl get configmaps --namespace=kube-system coredns -o yaml`.

Este artigo mostra-lhe como usar o ConfigMaps para opções básicas de personalização do CoreDNS em AKS. Esta abordagem difere da configuração do CoreDNS noutros contextos, tais como a utilização do CoreFile. Verifique a versão do CoreDNS que está a executar, uma vez que os valores de configuração podem mudar entre versões.

> [!NOTE]
> `kube-dns` ofereceram diferentes opções de [personalização][kubednsblog] através de um mapa de config Kubernetes. O CoreDNS **não** é compatível com kube-dns. Quaisquer personalizações que tenha usado anteriormente devem ser atualizadas para utilização com o CoreDNS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo assume que você tem um aglomerado AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

## <a name="what-is-supportedunsupported"></a>O que é suportado/não apoiado

Todos os plugins CoreDNS incorporados são suportados. Não são suportados plugins de adição/terceiros.

## <a name="rewrite-dns"></a>Reescrever DNS

Um dos cenários que tem é realizar reescritas de nome DNS on-the-fly. No exemplo seguinte, substitua `<domain to be written>` pelo seu próprio nome de domínio totalmente qualificado. Criar um ficheiro chamado `corednsms.yaml` e colar a seguinte configuração de exemplo:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

Crie o ConfigMap utilizando o [comando de cofigmap][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f corednsms.yaml
```

Para verificar se foram aplicadas as personalizações, utilize o [kubectl obter mapas configmaps][kubectl-get] e especificar o configMap personalizado para *coredns:*

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Agora force o CoreDNS a recarregar o ConfigMap. O comando de [cápsulas kubectl não][kubectl delete] é destrutivo e não causa tempo de descanso. As cápsulas `kube-dns` são eliminadas, e o Programador Kubernetes recria-os. Estas novas cápsulas contêm a alteração do valor TTL.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> O comando acima está correto. Enquanto estamos a mudar `coredns`, a implantação está sob o nome **kube-dns.**

## <a name="custom-forward-server"></a>Servidor avançado personalizado

Se precisar especificar um servidor avançado para o tráfego de rede, pode criar um ConfigMap para personalizar o DNS. No exemplo seguinte, atualize o nome `forward` e o endereço com os valores para o seu próprio ambiente. Criar um ficheiro chamado `corednsms.yaml` e colar a seguinte configuração de exemplo:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

Tal como nos exemplos anteriores, crie o ConfigMap utilizando o comando de aplicação de [cofigmap][kubectl-apply] e especifique o nome do seu manifesto YAML. Em seguida, force o CoreDNS a recarregar o ConfigMap utilizando o [pod de eliminação kubectl][kubectl delete] para o Agendador Kubernetes recriá-los:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Use domínios personalizados

Pode querer configurar domínios personalizados que só podem ser resolvidos internamente. Por exemplo, você pode querer resolver o domínio personalizado *puglife.local*, que não é um domínio de alto nível válido. Sem um domínio personalizado ConfigMap, o cluster AKS não pode resolver o endereço.

No exemplo seguinte, atualize o domínio personalizado e o endereço IP para direcionar o tráfego com os valores para o seu próprio ambiente. Criar um ficheiro chamado `corednsms.yaml` e colar a seguinte configuração de exemplo:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: |
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

Tal como nos exemplos anteriores, crie o ConfigMap utilizando o comando de aplicação de [cofigmap][kubectl-apply] e especifique o nome do seu manifesto YAML. Em seguida, force o CoreDNS a recarregar o ConfigMap utilizando o [pod de eliminação kubectl][kubectl delete] para o Agendador Kubernetes recriá-los:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>Domínios de stub

O CoreDNS também pode ser usado para configurar domínios de tubos. No exemplo seguinte, atualize os domínios personalizados e os endereços IP com os valores para o seu próprio ambiente. Criar um ficheiro chamado `corednsms.yaml` e colar a seguinte configuração de exemplo:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

Tal como nos exemplos anteriores, crie o ConfigMap utilizando o comando de aplicação de [cofigmap][kubectl-apply] e especifique o nome do seu manifesto YAML. Em seguida, force o CoreDNS a recarregar o ConfigMap utilizando o [pod de eliminação kubectl][kubectl delete] para o Agendador Kubernetes recriá-los:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Plugin anfitriões

Como todos os plugins incorporados são suportados, isto significa que o plugin CoreDNS [Hosts][coredns hosts] está disponível para personalizar também:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: |
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>Ativar a exploração de registo para depuração de consulta dNS 

Para permitir a consulta de dNS, aplique a seguinte configuração no configMap personalizado para coredns:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: |
        log
```

## <a name="next-steps"></a>Passos seguintes

Este artigo mostrou alguns cenários de exemplo para a personalização do CoreDNS. Para obter informações sobre o projeto CoreDNS, consulte [a página do projeto CoreDNS a montante][coredns].

Para saber mais sobre conceitos de rede core, consulte [conceitos de rede para aplicações no AKS][concepts-network].

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
