---
title: Personalizar o CoreDNS para o AKS (serviço kubernetes do Azure)
description: Saiba como personalizar o CoreDNS para adicionar subdomínios ou estender pontos de extremidade DNS personalizados usando o AKS (serviço kubernetes do Azure)
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 03/15/2019
ms.author: jenoller
ms.openlocfilehash: 40c170575e0b6e6264eed8a464ac73012cbe8627
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70276510"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Personalizar o CoreDNS com o serviço kubernetes do Azure

O AKS (serviço kubernetes do Azure) usa o projeto [CoreDNS][coredns] para a resolução e o gerenciamento de DNS do cluster com todos os clusters *1.12. x* e superior. Anteriormente, o projeto Kube-DNS foi usado. Este projeto Kube-DNS agora está preterido. Para obter mais informações sobre personalização e kubernetes do CoreDNS, consulte a [documentação do upstream oficial][corednsk8s].

Como o AKS é um serviço gerenciado, você não pode modificar a configuração principal para CoreDNS(um corefile). Em vez disso, você usa um kubernetes *ConfigMap* para substituir as configurações padrão. Para ver o padrão AKs CoreDNS ConfigMaps, use o `kubectl get configmaps --namespace=kube-system coredns -o yaml` comando.

Este artigo mostra como usar o ConfigMaps para as opções básicas de personalização do CoreDNS no AKS.

> [!NOTE]
> `kube-dns`ofereciam diferentes [Opções de personalização][kubednsblog] por meio de um mapa de configuração do kubernetes. CoreDNS **não** é compatível com versões anteriores com Kube-DNS. Todas as personalizações que você usou anteriormente devem ser atualizadas para uso com CoreDNS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster AKS existente. Se você precisar de um cluster AKS, consulte o guia de início rápido do AKS [usando o CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

## <a name="what-is-supportedunsupported"></a>O que tem suporte/sem suporte

Todos os plug-ins CoreDNS internos têm suporte. Nenhum complemento/plug-in de terceiros tem suporte. 

## <a name="rewrite-dns"></a>Reescrever o DNS

Um cenário que você tem é executar regravações de nome DNS imediata. No exemplo a seguir, substitua `<domain to be written>` pelo seu próprio nome de domínio totalmente qualificado. Crie um arquivo chamado `corednsms.yaml` e cole a seguinte configuração de exemplo:

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
        proxy .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

Crie o ConfigMap usando o comando [kubectl Apply ConfigMap][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f corednsms.yaml
```

Para verificar se as personalizações foram aplicadas, use [kubectl Get configmaps][kubectl-get] e especifique seu ConfigMap *coredns-Custom* :

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Agora Force CoreDNS a recarregar o ConfigMap. O comando de [Pod kubectl Delete][kubectl delete] não é destrutivo e não causa tempo de inatividade. Os `kube-dns` pods são excluídos e o Agendador kubernetes os recria. Esses novos pods contêm a alteração no valor TTL.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> O comando acima está correto. Enquanto estamos alterando `coredns`, a implantação está sob o nome **DNS Kube** .

## <a name="custom-proxy-server"></a>Servidor proxy personalizado

Se você precisar especificar um servidor proxy para o tráfego de rede, poderá criar um ConfigMap para personalizar o DNS. No exemplo a seguir, atualize o `proxy` nome e o endereço com os valores para seu próprio ambiente. Crie um arquivo chamado `corednsms.yaml` e cole a seguinte configuração de exemplo:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        proxy foo.com 1.1.1.1
    }
```

Como nos exemplos anteriores, crie o ConfigMap usando o comando [kubectl Apply ConfigMap][kubectl-apply] e especifique o nome do manifesto do YAML. Em seguida, Force CoreDNS a recarregar o ConfigMap usando o [kubectl Delete Pod][kubectl delete] para o Agendador kubernetes para recriá-los:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Usar domínios personalizados

Talvez você queira configurar domínios personalizados que só podem ser resolvidos internamente. Por exemplo, talvez você queira resolver o domínio personalizado *puglife. local*, que não é um domínio de nível superior válido. Sem um ConfigMap de domínio personalizado, o cluster AKS não pode resolver o endereço.

No exemplo a seguir, atualize o domínio personalizado e o endereço IP para direcionar o tráfego para com os valores para seu próprio ambiente. Crie um arquivo chamado `corednsms.yaml` e cole a seguinte configuração de exemplo:

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
        proxy . 192.11.0.1  # this is my test/dev DNS server
    }
```

Como nos exemplos anteriores, crie o ConfigMap usando o comando [kubectl Apply ConfigMap][kubectl-apply] e especifique o nome do manifesto do YAML. Em seguida, Force CoreDNS a recarregar o ConfigMap usando o [kubectl Delete Pod][kubectl delete] para o Agendador kubernetes para recriá-los:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="stub-domains"></a>Domínios de stub

CoreDNS também pode ser usado para configurar domínios stub. No exemplo a seguir, atualize os domínios personalizados e os endereços IP com os valores para seu próprio ambiente. Crie um arquivo chamado `corednsms.yaml` e cole a seguinte configuração de exemplo:

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
        proxy . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        proxy . 2.3.4.5
    }

```

Como nos exemplos anteriores, crie o ConfigMap usando o comando [kubectl Apply ConfigMap][kubectl-apply] e especifique o nome do manifesto do YAML. Em seguida, Force CoreDNS a recarregar o ConfigMap usando o [kubectl Delete Pod][kubectl delete] para o Agendador kubernetes para recriá-los:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Hospeda plug-in

Como todos os plug-ins internos têm suporte, isso significa que [][coredns hosts] o plug-in CoreDNS hosts também está disponível para personalização:

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

## <a name="next-steps"></a>Passos Seguintes

Este artigo mostrou alguns cenários de exemplo para a personalização do CoreDNS. Para obter informações sobre o projeto CoreDNS, consulte [a página projeto upstream do CoreDNS][coredns].

Para saber mais sobre os principais conceitos de rede, consulte [conceitos de rede para aplicativos no AKs][concepts-network].

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
