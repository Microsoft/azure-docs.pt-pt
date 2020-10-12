---
title: Ativar vários suportes de espaço de nome para o Controlador de Entradas de Gateway de Aplicação
description: Este artigo fornece informações sobre como ativar o suporte a múltiplos espaços de nome num cluster Kubernetes com um Controlador de Entrada de Gateway de Aplicação.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 953430421bd30aaa1df352451b549994aeaa1a70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85556170"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Ativar vários suportes namespace num cluster AKS com controlador de entrada de gateway de aplicação

## <a name="motivation"></a>Motivação
Os espaços [de nomes](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) de Kubernetes possibilitam que um cluster Kubernetes seja dividido e alocado a subgrupos de uma equipa maior. Estas subemas podem então implantar e gerir infraestruturas com controlos mais finos de recursos, segurança, configuração, etc. Kubernetes permite que um ou mais recursos ingresss sejam definidos independentemente dentro de cada espaço de nome.

A partir da versão 0.7 [Azure Application Gateway Kubernetes IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC) pode ingerir eventos e observar vários espaços de nome. Se o administrador da AKS decidir utilizar o [App Gateway](https://azure.microsoft.com/services/application-gateway/) como uma entrada, todos os espaços de nome utilizarão a mesma instância do Gateway de Aplicação. Uma única instalação do Controlador de Ingress monitorizará espaços de nomes acessíveis e configurará o Gateway de Aplicação a que está associado.

A versão 0.7 da AGIC continuará a observar exclusivamente o espaço de `default` nomes, a menos que este seja explicitamente alterado para um ou mais espaços de nome diferentes na configuração Helm (ver secção abaixo).

## <a name="enable-multiple-namespace-support"></a>Ativar suporte de múltiplos espaços de nomes
Para ativar o suporte a vários nomes no espaço:
1. modificar o ficheiro [helm-config.yaml](#sample-helm-config-file) de uma das seguintes formas:
   - eliminar a `watchNamespace` chave inteiramente de [helm-config.yaml](#sample-helm-config-file) - AGIC observará todos os espaços de nome
   - definido `watchNamespace` para uma corda vazia - AGIC observará todos os espaços de nome
   - adicionar vários espaços de nome separados por uma vírgula `watchNamespace: default,secondNamespace` ( ) - AGIC observará estes espaços de nome exclusivamente
2. aplicar alterações do modelo de leme com: `helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

Uma vez implantado com a capacidade de observar vários espaços de nome, a AGIC:
  - listar recursos ingress de todos os espaços de nome acessíveis
  - filtro para recursos ingresss anotados com `kubernetes.io/ingress.class: azure/application-gateway`
  - compor combinado [Application Gateway config](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744)
  - aplicar o config ao Portal de Aplicação associado via [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

## <a name="conflicting-configurations"></a>Configurações conflituosas
Vários [recursos de entrada com nomes](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) poderiam instruir a AGIC a criar configurações conflituosas para um único Gateway de aplicação. (Duas ingresses que reivindicam o mesmo domínio, por exemplo.)

No topo da hierarquia - **os ouvintes** (endereço IP, porta e anfitrião) e **regras de encaminhamento (ouvinte** vinculativo, piscina de backend e configurações HTTP) poderiam ser criados e partilhados por vários espaços/ingresses de nomes.

Por outro lado - caminhos, piscinas de backend, definições HTTP e certificados TLS poderiam ser criados apenas por um espaço de nome e os duplicados serão removidos.

Por exemplo, considere os seguintes recursos duplicados de ingresss recursos definidos `staging` e `production` `www.contoso.com` para:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: staging
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

Apesar dos dois recursos ingressantes exigindo tráfego para `www.contoso.com` ser encaminhado para os respetivos espaços de nome Kubernetes, apenas um backend pode servir o tráfego. A AGIC criaria uma configuração em base "primeiro a chegar, primeiro a ser servido" para um dos recursos. Se dois recursos ingresses forem criados ao mesmo tempo, o anterior no alfabeto terá precedência. Pelo exemplo acima, só poderemos criar configurações para a `production` entrada. O Gateway de Aplicação será configurado com os seguintes recursos:

  - Ouvinte: `fl-www.contoso.com-80`
  - Regra de encaminhamento: `rr-www.contoso.com-80`
  - Piscina de backend: `pool-production-contoso-web-service-80-bp-80`
  - DEFINIÇÕES HTTP: `bp-production-contoso-web-service-80-80-websocket-ingress`
  - Sonda de Saúde: `pb-production-contoso-web-service-80-websocket-ingress`

Note que, com exceção da regra *de ouvinte* e *encaminhamento,* os recursos do Gateway de Aplicação criados incluem o nome do espaço de nomes ( ) para o `production` qual foram criados.

Se os dois recursos ingressados forem introduzidos no cluster AKS em diferentes pontos do tempo, é provável que a AGIC acabe num cenário em que reconfigure o Gateway de Aplicação e reencamque o tráfego de `namespace-B` `namespace-A` .

Por exemplo, se adicionar `staging` primeiro, a AGIC configurará o Application Gateway para encaminhar o tráfego para a piscina de backend de paragem. Numa fase posterior, introduzindo a `production` entrada, fará com que a AGIC reprograme o Gateway de Aplicação, que começará a encaminhar o tráfego para a `production` piscina de backend.

## <a name="restrict-access-to-namespaces"></a>Restringir o acesso aos espaços de nome
Por predefinição, a AGIC configurará o Gateway de Aplicação com base em Ingress anotado dentro de qualquer espaço de nome. Caso pretenda limitar este comportamento, tem as seguintes opções:
  - limitar os espaços de nome, definindo explicitamente os espaços de nomeS AGIC deve observar através da `watchNamespace` chave YAML em [helm-config.yaml](#sample-helm-config-file)
  - utilizar [Role/RoleBinding](https://docs.microsoft.com/azure/aks/azure-ad-rbac) para limitar a AGIC a espaços de nome específicos

## <a name="sample-helm-config-file"></a>Arquivo config de amostra helm

```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
```

