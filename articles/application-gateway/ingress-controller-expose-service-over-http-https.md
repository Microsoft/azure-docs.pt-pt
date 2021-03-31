---
title: Expor um serviço AKS em HTTP ou HTTPS usando o Application Gateway
description: Este artigo fornece informações sobre como expor um serviço AKS através de HTTP ou HTTPS usando o Gateway de Aplicações.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3b816ddc0eccf8c406cfed37d6bfc594e27d3629
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85850358"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>Expor um serviço AKS em HTTP ou HTTPS usando o Application Gateway 

Estes tutoriais ajudam a ilustrar o uso da [Kubernetes Ingress Resources](https://kubernetes.io/docs/concepts/services-networking/ingress/) para expor um serviço de exemplo Kubernetes através do [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) sobre HTTP ou HTTPS.

## <a name="prerequisites"></a>Pré-requisitos

- Gráfico de `ingress-azure` leme instalado.
  - [**Implantação de Greenfield**](ingress-controller-install-new.md): Se estiver a começar do zero, consulte estas instruções de instalação, que delineiam passos para implantar um cluster AKS com o Gateway de aplicação e instalar o controlador de entrada de gateway de aplicação no cluster AKS.
  - [**Implementação brownfield**](ingress-controller-install-existing.md): Se tiver um cluster AKS existente e Gateway de aplicação, consulte estas instruções para instalar o controlador de entrada de gateway de aplicação no cluster AKS.
- Se pretender utilizar HTTPS nesta aplicação, necessitará de um certificado x509 e da sua chave privada.

## <a name="deploy-guestbook-application"></a>Implementação `guestbook` de aplicação

A aplicação guestbook é uma aplicação canónica de Kubernetes que compõe um frontend web UI, um backend e uma base de dados Redis. Por predefinição, `guestbook` expõe a sua aplicação através de um serviço com nome `frontend` na porta `80` . Sem recurso Kubernetes Ingress, o serviço não está acessível fora do cluster AKS. Utilizaremos a aplicação e configuraremos os Recursos Ingress para aceder à aplicação através de HTTP e HTTPS.

Siga as instruções abaixo para implementar a aplicação do livro de visitas.

1. Download `guestbook-all-in-one.yaml` a partir [daqui](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)
1. Implemente `guestbook-all-in-one.yaml` o seu cluster AKS executando

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

Agora, a `guestbook` aplicação foi implementada.

## <a name="expose-services-over-http"></a>Expor serviços em HTTP

Para expor a aplicação do livro de visitas, utilizaremos o seguinte recurso ingresss:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```

Esta entrada exporá o `frontend` serviço da `guestbook-all-in-one` implementação como um backend padrão do Gateway de aplicação.

Guarde o recurso de entrada acima como `ing-guestbook.yaml` .

1. Implementar `ing-guestbook.yaml` executando:

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. Verifique o registo do controlador de entrada para obter o estado de implantação.

Agora a `guestbook` inscrição deve estar disponível. Pode verificar isto visitando o endereço público do Gateway de Aplicações.

## <a name="expose-services-over-https"></a>Expor serviços em HTTPS

### <a name="without-specified-hostname"></a>Sem nome de anfitrião especificado

Sem especificar o nome de anfitrião, o serviço de guestbook estará disponível em todos os nomes de anfitriões que apontam para o gateway da aplicação.

1. Antes de implementar a entrada, precisa criar um segredo de kubernetes para hospedar o certificado e a chave privada. Você pode criar um segredo de kubernetes correndo

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. Defina a seguinte entrada. Na entrada, especifique o nome do segredo na `secretName` secção.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - secretName: <guestbook-secret-name>
      rules:
      - http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

    > [!NOTE] 
    > Substitua `<guestbook-secret-name>` no recurso Ingress acima pelo nome do seu segredo. Guarde o recurso ingress acima num nome de ficheiro `ing-guestbook-tls.yaml` .

1. Implementar ing-guestbook-tls.yaml executando

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. Verifique o registo do controlador de entrada para obter o estado de implantação.

Agora a `guestbook` aplicação estará disponível tanto em HTTP como EM HTTPS.

### <a name="with-specified-hostname"></a>Com nome de anfitrião especificado

Também pode especificar o nome de anfitrião na entrada para configurações e serviços de multiplex TLS.
Ao especificar o nome de anfitrião, o serviço de guestbook só estará disponível no anfitrião especificado.

1. Defina a seguinte entrada.
    Na entrada, especifique o nome do segredo na `secretName` secção e substitua o nome de anfitrião na secção em `hosts` conformidade.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - hosts:
          - <guestbook.contoso.com>
          secretName: <guestbook-secret-name>
      rules:
      - host: <guestbook.contoso.com>
        http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

1. Implementar `ing-guestbook-tls-sni.yaml` executando

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. Verifique o registo do controlador de entrada para obter o estado de implantação.

Agora a `guestbook` aplicação estará disponível em HTTP e HTTPS apenas no anfitrião especificado `<guestbook.contoso.com>` (neste exemplo).

## <a name="integrate-with-other-services"></a>Integrar noutros serviços

As seguintes entradas permitir-lhe-ão adicionar caminhos adicionais a esta entrada e redirecionar esses caminhos para outros serviços:

```yaml
apiVersion: extensions/v1beta1
  kind: Ingress
  metadata:
    name: guestbook
    annotations:
      kubernetes.io/ingress.class: azure/application-gateway
  spec:
    rules:
    - http:
        paths:
        - path: </other/*>
          backend:
            serviceName: <other-service>
            servicePort: 80
        - backend:
            serviceName: frontend
            servicePort: 80
```
