---
title: Ativar suportes de espaço de nome múltiplos para controlador de entrada de gateway de aplicação
description: Este artigo fornece informações sobre como ativar o suporte de vários espaços de nome num cluster Kubernetes com um Controlador de Entrada de Gateway de Aplicação.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 83650e7cf46ec1dede5f25e32114d6469bab24be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79279926"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Ativar suporte múltiplo namespace em um cluster AKS com controlador de entrada de gateway aplicação

## <a name="motivation"></a>Motivação
Os espaços [de nome](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) kubernetes possibilitam a divisão de um cluster Kubernetes e atribuídos a subgrupos de uma equipa maior. Estas subequipas podem então implantar e gerir infraestruturas com controlos mais finos de recursos, segurança, configuração, etc. Kubernetes permite que um ou mais recursos de ingresso sejam definidos independentemente dentro de cada espaço de nome.

A partir da versão 0.7 [Azure Application Gateway Kubernetes IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC) pode ingerir eventos e observar vários espaços de nome. Caso o administrador da AKS decida utilizar o [App Gateway](https://azure.microsoft.com/services/application-gateway/) como entrada, todos os espaços de nome usarão a mesma instância de Gateway de Aplicação. Uma única instalação do Controlador Ingress monitorizará espaços de nome acessíveis e configurará o Gateway de Aplicação a que está associado.

A versão 0.7 da AGIC continuará `default` a observar exclusivamente o espaço de nome, a menos que este seja explicitamente alterado para um ou mais espaços de nome sinuosos na configuração Helm (ver secção abaixo).

## <a name="enable-multiple-namespace-support"></a>Ativar suporte de múltiplos espaços de nomes
Para permitir o suporte de vários espaços de nome:
1. modificar o ficheiro [helm-config.yaml](#sample-helm-config-file) de uma das seguintes formas:
   - eliminar `watchNamespace` a chave inteiramente de [helm-config.yaml](#sample-helm-config-file) - AGIC observará todos os espaços de nome
   - definido `watchNamespace` para uma corda vazia - AGIC observará todos os espaços de nome
   - adicionar vários espaços de nome separados por uma vírem (`watchNamespace: default,secondNamespace`) - AGIC observará estes espaços de nome exclusivamente
2. aplicar alterações no modelo de leme com:`helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

Uma vez implantado com a capacidade de observar vários espaços de nome, a AGIC:
  - lista de recursos ingressos de todos os espaços de nome acessíveis
  - filtro para recursos de ingresso anotado com`kubernetes.io/ingress.class: azure/application-gateway`
  - compor conjunto [aplicação gateway config](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744)
  - aplicar o config ao Gateway de Aplicação associado via [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

## <a name="conflicting-configurations"></a>Configurações contraditórias
Vários recursos de [ingresso](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) com nomes espaciais poderiam instruir a AGIC a criar configurações contraditórias para um único Gateway de Aplicação. (Duas ingressos que reclamam o mesmo domínio, por exemplo.)

No topo da hierarquia - **os ouvintes** (endereço IP, porto e anfitrião) e as regras de **encaminhamento** (ouvinte vinculativo, piscina de backend e definições HTTP) poderiam ser criadas e partilhadas por vários espaços de nome/entradas.

Por outro lado - caminhos, piscinas de backend, definições HTTP e certificados TLS poderiam ser criados apenas por um espaço de nome e duplicados serão removidos.

Por exemplo, considere os seguintes recursos `staging` `production` de `www.contoso.com`ingresso duplicados definidos espaços de nome e para:
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

Apesar dos dois recursos `www.contoso.com` de ingresso que exigem tráfego para serem encaminhados para os respetivos espaços de nome Kubernetes, apenas um backend pode servir o tráfego. A AGIC criaria uma configuração na base "first come, first served" para um dos recursos. Se dois recursos ingressos forem criados ao mesmo tempo, o anterior no alfabeto terá precedência. Pelo exemplo acima, só seremos capazes `production` de criar configurações para a entrada. O Gateway de aplicação será configurado com os seguintes recursos:

  - Ouvinte:`fl-www.contoso.com-80`
  - Regra de encaminhamento:`rr-www.contoso.com-80`
  - Piscina de backend:`pool-production-contoso-web-service-80-bp-80`
  - Definições HTTP:`bp-production-contoso-web-service-80-80-websocket-ingress`
  - Sonda de Saúde:`pb-production-contoso-web-service-80-websocket-ingress`

Note que, com exceção da regra do *ouvinte* e do *encaminhamento,* os recursos de Gateway de Aplicação criados incluem o nome do espaço de nome (`production`) para o qual foram criados.

Se os dois recursos de ingresso forem introduzidos no cluster AKS em diferentes pontos do tempo, é provável `namespace-B` que `namespace-A`a AGIC acabe num cenário em que reconfigure o Gateway de Aplicação e redirecione o tráfego de .

Por exemplo, `staging` se tiver adicionado primeiro, a AGIC configurará o Application Gateway para encaminhar o tráfego para a piscina de backend de preparação. Numa fase posterior, `production` a introdução da entrada, fará com que a AGIC `production` reprograme o Application Gateway, que começará a encaminhar o tráfego para a piscina de backend.

## <a name="restrict-access-to-namespaces"></a>Restringir o acesso aos espaços de nomes
Por padrão, a AGIC configurará o Gateway de Aplicação com base em Ingress anotado dentro de qualquer espaço de nome. Caso pretenda limitar este comportamento, tem as seguintes opções:
  - limitar os espaços de nome, definindo explicitamente `watchNamespace` os espaços de nome AGIC deve observar através da chave YAML em [helm-config.yaml](#sample-helm-config-file)
  - utilizar [Role/RoleBinding](https://docs.microsoft.com/azure/aks/azure-ad-rbac) para limitar a AGIC a espaços de nome específicos

## <a name="sample-helm-config-file"></a>Arquivo de config de leme de amostra
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

