---
title: Personalize o CoreDNS para o serviço Azure Kubernetes (AKS)
description: Saiba como personalizar o CoreDNS para adicionar subdomínios ou estender os pontos finais dNS personalizados usando o Serviço Azure Kubernetes (AKS)
services: container-service
author: palma21
ms.topic: article
ms.date: 03/15/2019
ms.author: jpalma
ms.openlocfilehash: 5b13931bc6a13d988c21f728b996c51270769e0c
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368686"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Personalizar o CoreDNS com o Azure Kubernetes Service

O Azure Kubernetes Service (AKS) utiliza o projeto [CoreDNS][coredns] para gestão e resolução de DNS de cluster com todos os clusters *1.12.x* e mais altos. Anteriormente, o projeto Kube-DNS foi usado. Este projeto kube-dns está agora depreciado. Para obter mais informações sobre a personalização do CoreDNS e kubernetes, consulte a [documentação oficial][corednsk8s]a montante.

Como a AKS é um serviço gerido, não é possível modificar a configuração principal para CoreDNS (um *CoreFile).* Em vez disso, utiliza um *ConfigMap* Kubernetes para anular as definições predefinidos. Para ver o padrão AKS CoreDNS ConfigMaps, utilize o `kubectl get configmaps --namespace=kube-system coredns -o yaml` comando.

Este artigo mostra-lhe como usar ConfigMaps para opções básicas de personalização do CoreDNS em AKS. Esta abordagem difere da configuração do CoreDNS noutros contextos, tais como a utilização do CoreFile. Verifique a versão do CoreDNS que está a executar, uma vez que os valores de configuração podem mudar entre versões.

> [!NOTE]
> `kube-dns` ofereceu diferentes opções de [personalização][kubednsblog] através de um mapa config de Kubernetes. CoreDNS **não** é compatível com kube-dns. Quaisquer personalizações que tenha usado anteriormente devem ser atualizadas para utilização com o CoreDNS.

## <a name="before-you-begin"></a>Before you begin

Este artigo pressupõe que você tem um cluster AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Ao criar uma configuração como os exemplos abaixo, os seus nomes na secção *de dados* devem terminar em *.servidor* ou *.override*. Esta convenção de nomeação é definida no configmap AKS CoreDNS padrão que pode ver usando o `kubectl get configmaps --namespace=kube-system coredns -o yaml` comando.

## <a name="what-is-supportedunsupported"></a>O que é suportado/não suportado

Todos os plugins CoreDNS incorporados são suportados. Não são suportados plugins adicionais/terceiros.

## <a name="rewrite-dns"></a>Reescrever DNS

Um dos cenários que tem é fazer reescritas de nome DNS on-the-fly. No exemplo seguinte, `<domain to be written>` substitua-o pelo seu próprio nome de domínio totalmente qualificado. Crie um ficheiro com o nome `corednsms.yaml` e cole a seguinte configuração de exemplo:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        kubernetes cluster.local in-addr.arpa ip6.arpa {
          pods insecure
          upstream
          fallthrough in-addr.arpa ip6.arpa
        }
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10, but that server must be able to resolve the rewritten domain name
    }
```

> [!IMPORTANT]
> Se redirecionar para um servidor DNS, como o IP de serviço CoreDNS, esse servidor DNS deve ser capaz de resolver o nome de domínio reescrito.

Crie o ConfigMap utilizando o [comando configmap de aplicação de kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f corednsms.yaml
```

Para verificar se as personalizações foram aplicadas, use o [kubectl obter configmaps][kubectl-get] e especifique o seu ConfigMap personalizado para *coredns:*

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Agora força o CoreDNS a recarregar o ConfigMap. O comando [de eliminação de kubectl][kubectl delete] não é destrutivo e não causa tempo de mente. As `kube-dns` cápsulas são eliminadas e o Programador Kubernetes recria-as. Estas novas cápsulas contêm a alteração do valor TTL.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> O comando acima está correto. Enquanto estamos a `coredns` mudar, a implantação está sob o nome **kube-dns.**

## <a name="custom-forward-server"></a>Servidor avançado personalizado

Se precisar de especificar um servidor avançado para o tráfego da sua rede, pode criar um ConfigMap para personalizar o DNS. No exemplo seguinte, atualize o `forward` nome e o endereço com os valores para o seu próprio ambiente. Crie um ficheiro com o nome `corednsms.yaml` e cole a seguinte configuração de exemplo:

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

Tal como nos exemplos anteriores, crie o ConfigMap utilizando o [comando configmap de aplicação de kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML. Em seguida, force o CoreDNS a recarregar o ConfigMap utilizando o [pod de eliminação de kubectl][kubectl delete] para o Programador Kubernetes para recriá-los:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Use domínios personalizados

Pode querer configurar domínios personalizados que só podem ser resolvidos internamente. Por exemplo, pode querer resolver o domínio personalizado *puglife.local,* que não é um domínio de nível superior válido. Sem um domínio personalizado ConfigMap, o cluster AKS não consegue resolver o endereço.

No exemplo seguinte, atualize o domínio personalizado e o endereço IP para direcionar o tráfego para com os valores para o seu próprio ambiente. Crie um ficheiro com o nome `corednsms.yaml` e cole a seguinte configuração de exemplo:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: | # you may select any name here, but it must end with the .server file extension
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

Tal como nos exemplos anteriores, crie o ConfigMap utilizando o [comando configmap de aplicação de kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML. Em seguida, force o CoreDNS a recarregar o ConfigMap utilizando o [pod de eliminação de kubectl][kubectl delete] para o Programador Kubernetes para recriá-los:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>Domínios de canhotos

O CoreDNS também pode ser usado para configurar domínios de canhotos. No exemplo seguinte, atualize os domínios personalizados e endereços IP com os valores para o seu próprio ambiente. Crie um ficheiro com o nome `corednsms.yaml` e cole a seguinte configuração de exemplo:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
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

Tal como nos exemplos anteriores, crie o ConfigMap utilizando o [comando configmap de aplicação de kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML. Em seguida, force o CoreDNS a recarregar o ConfigMap utilizando o [pod de eliminação de kubectl][kubectl delete] para o Programador Kubernetes para recriá-los:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Anfitriões plugin

Como todos os plugins incorporados são suportados, isto significa que o plugin CoreDNS [Hosts][coredns hosts] também está disponível para personalizar:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: | # you may select any name here, but it must end with the .override file extension
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>Ativar o registo de consultas DNS 

Para ativar o registo de consultas DNS, aplique a seguinte configuração no seu ConfigMap personalizado para coredns:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: | # you may select any name here, but it must end with the .override file extension
        log
```

## <a name="next-steps"></a>Passos seguintes

Este artigo mostrou alguns cenários de exemplo para a personalização do CoreDNS. Para obter informações sobre o projeto CoreDNS, consulte [a página do projeto CoreDNS a montante.][coredns]

Para saber mais sobre conceitos de rede core, consulte [conceitos de Rede para aplicações em AKS.][concepts-network]

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
