---
title: Personalizar CoreDNS para o serviço Kubernetes do Azure (AKS)
description: Saiba como personalizar CoreDNS adicionar subdomínios ou expandir a pontos finais DNS personalizados com o Azure Kubernetes Service (AKS)
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 03/15/2019
ms.author: jnoller
ms.openlocfilehash: 9c67902f8a6c10552ea60fed145afc24f82b01a1
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65594287"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Personalizar CoreDNS com o serviço Kubernetes do Azure

Azure Kubernetes Service (AKS) utiliza a [CoreDNS] [ coredns] projeto para a gestão de DNS do cluster e de resolução com todos os *1.12.x* e clusters superior. Anteriormente, o projeto do kube dns foi utilizado. Este projeto de dns do kube agora foi preterido. Para obter mais informações sobre a personalização de CoreDNS e o Kubernetes, consulte a [documentação oficial da montante][corednsk8s].

Como o AKS é um serviço gerido, não é possível modificar a configuração principal para CoreDNS (um *CoreFile*). Em alternativa, utilize o Kubernetes *ConfigMap* para substituir as predefinições. Para ver a predefinição AKS CoreDNS ConfigMaps, utilize o `kubectl get configmaps coredns -o yaml` comando.

Este artigo mostra-lhe como utilizar ConfigMaps para opções de personalização básica de CoreDNS no AKS.

> [!NOTE]
> `kube-dns` oferecidos diferentes [opções de personalização] [ kubednsblog] por meio de um mapa de configuração do Kubernetes. É CoreDNS **não** retrocompatível com o dns do kube. As personalizações que utilizou anteriormente devem ser atualizadas para utilização com CoreDNS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem um cluster do AKS existente. Se precisar de um cluster do AKS, consulte o guia de introdução do AKS [utilizando a CLI do Azure] [aks-início rápido-cli] ou [através do portal do Azure] [aks-início rápido-portal].

## <a name="what-is-supportedunsupported"></a>O que é suportado/não suportado

Incorporada todos os CoreDNS plug-ins são suportadas. Não existem plug-ins de add práticos/de terceiros são suportados.

## <a name="rewrite-dns"></a>Reescrever DNS

Um cenário que tem é realizar reescritas de nome DNS no momento. No exemplo seguinte, substitua `<domain to be written>` com seu próprio nome de domínio completamente qualificado. Crie um ficheiro denominado `corednsms.json` e cole a seguinte configuração de exemplo:

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

Crie a ConfigMap com o [kubectl aplicar configmap] [ kubectl-apply] de comando e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f corednsms.json
```

Para verificar as personalizações foram aplicadas, utilize o [kubectl obter configmaps] [ kubectl-get] e especifique seus *coredns personalizado* ConfigMap:

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Força agora CoreDNS para recarregar o ConfigMap. O [kubectl eliminar o pod] [ kubectl delete] comando não destrutivo e não causar período de indisponibilidade. O `kube-dns` pods são eliminados e, em seguida, recria o agendador do Kubernetes-los. Estes novos pods contenham a alteração no valor TTL.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> O comando acima está correto. Enquanto estamos a alterar `coredns`, a implementação está sob o **kube dns** nome.

## <a name="custom-proxy-server"></a>Servidor de proxy personalizado

Se tiver de especificar um servidor proxy para o tráfego de rede, pode criar um ConfigMap para personalizar o DNS. No exemplo seguinte, atualize o `proxy` nome e endereço com os valores para o seu próprio ambiente. Crie um ficheiro denominado `corednsms.json` e cole a seguinte configuração de exemplo:

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

Como nos exemplos anteriores, criar o ConfigMap utilizando o [kubectl aplicar configmap] [ kubectl-apply] de comando e especifique o nome do seu manifesto YAML. Em seguida, forçar CoreDNS para recarregar a ConfigMap utilizando o [kubectl eliminar o pod] [ kubectl delete] para o agendador de Kubernetes recriá-las:

```console
kubectl apply -f corednsms.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Utilizar domínios personalizados

Pode querer configurar domínios personalizados que só podem ser resolvidos internamente. Por exemplo, pode querer resolver o domínio personalizado *puglife.local*, que não é um domínio de nível superior válido. Sem um domínio personalizado ConfigMap, o cluster do AKS não é possível resolver o endereço.

No exemplo a seguir, atualize o domínio personalizado e o endereço IP para direcionar o tráfego para com os valores para o seu próprio ambiente. Crie um ficheiro denominado `corednsms.json` e cole a seguinte configuração de exemplo:

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

Como nos exemplos anteriores, criar o ConfigMap utilizando o [kubectl aplicar configmap] [ kubectl-apply] de comando e especifique o nome do seu manifesto YAML. Em seguida, forçar CoreDNS para recarregar a ConfigMap utilizando o [kubectl eliminar o pod] [ kubectl delete] para o agendador de Kubernetes recriá-las:

```console
kubectl apply -f corednsms.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="stub-domains"></a>Domínios de stub

CoreDNS também pode ser utilizado para configurar domínios de stub. No exemplo seguinte, atualize os endereços IP e de domínios personalizados com os valores para o seu próprio ambiente. Crie um ficheiro denominado `corednsms.json` e cole a seguinte configuração de exemplo:

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

Como nos exemplos anteriores, criar o ConfigMap utilizando o [kubectl aplicar configmap] [ kubectl-apply] de comando e especifique o nome do seu manifesto YAML. Em seguida, forçar CoreDNS para recarregar a ConfigMap utilizando o [kubectl eliminar o pod] [ kubectl delete] para o agendador de Kubernetes recriá-las:

```console
kubectl apply -f corednsms.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Plug-in de anfitriões

Como todos os plug-ins de incorporados são suportadas isso significa que o CoreDNS [anfitriões] [ coredns hosts] Plug-in está disponível para personalizar também:

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

Este artigo mostrou alguns exemplos de cenários de personalização de CoreDNS. Para obter informações sobre o projeto de CoreDNS, consulte [a página de projeto a montante CoreDNS][coredns].

Para saber mais sobre conceitos de rede de principais, consulte [rede conceitos para aplicações no AKS][concepts-network].

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - external -->
[concepts-network]: concepts-network.md
