---
title: Exponha um serviço AKS sobre HTTP ou HTTPS usando o Gateway de Aplicação
description: Este artigo fornece informações sobre como expor um serviço AKS através de HTTP ou HTTPS usando o Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c664141a8c89ccbdf37bd3f9a19cfa659982a47d
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73795577"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>Exponha um serviço AKS sobre HTTP ou HTTPS usando o Gateway de Aplicação 

Estes tutoriais ajudam a ilustrar o uso da [Kubernetes Ingress Resources](https://kubernetes.io/docs/concepts/services-networking/ingress/) para expor um serviço de exemplo Kubernetes através do Portal de [Aplicações Azure](https://azure.microsoft.com/services/application-gateway/) sobre HTTP ou HTTPS.

## <a name="prerequisites"></a>Pré-requisitos

- Gráfico `ingress-azure` de leme instalado.
  - [**Implementação**](ingress-controller-install-new.md)de Greenfield : Se estiver a começar do zero, consulte estas instruções de instalação, que delineia as medidas para implantar um cluster AKS com o Application Gateway e instale o controlador de entrada de gateway de aplicação no cluster AKS.
  - [**Implementação de Brownfield**](ingress-controller-install-existing.md): Se tiver um cluster AKS existente e Gateway de aplicação, consulte estas instruções para instalar o controlador de entrada de gateway de aplicação no cluster AKS.
- Se quiser utilizar https nesta aplicação, necessitará de um certificado x509 e da sua chave privada.

## <a name="deploy-guestbook-application"></a>Aplicação de implantação `guestbook`

A aplicação de guestbook é uma aplicação canónica kubernetes que compõe um frontend Web UI, um backend e uma base de dados Redis. Por predefinição, `guestbook` expõe a sua `frontend` aplicação através de um serviço com nome na porta `80`. Sem um Recurso Kubernetes Ingress, o serviço não é acessível de fora do cluster AKS. Utilizaremos a aplicação e configuraremos a Ingress Resources para aceder à aplicação através de HTTP e HTTPS.

Siga as instruções abaixo para implementar a aplicação do livro de hóspedes.

1. Baixar `guestbook-all-in-one.yaml` a partir [daqui](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)
1. Desloque-se `guestbook-all-in-one.yaml` para o seu cluster AKS executando

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

Agora, `guestbook` a aplicação foi implementada.

## <a name="expose-services-over-http"></a>Expor serviços sobre HTTP

Para expor a aplicação do guestbook, estaremos a utilizar o seguinte recurso ingress:

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

Esta entrada exporá `frontend` o `guestbook-all-in-one` serviço da implementação como um backend padrão do Gateway de Aplicação.

Guarde o recurso `ing-guestbook.yaml`de entrada acima como .

1. Implementar `ing-guestbook.yaml` executando:

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. Verifique o registo do controlador de entrada para obter o estado de implantação.

Agora `guestbook` o pedido deve estar disponível. Pode verificar isto visitando o endereço público do Gateway de Aplicação.

## <a name="expose-services-over-https"></a>Expor serviços sobre HTTPS

### <a name="without-specified-hostname"></a>Sem nome de anfitrião especificado

Sem especificar o nome de hóspedes, o serviço de guestbook estará disponível em todos os nomes de anfitriões que apontem para o gateway da aplicação.

1. Antes de implementar ingresso, você precisa criar um segredo kubernetes para hospedar o certificado e chave privada. Você pode criar um segredo kubernetes correndo

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. Defina a seguinte entrada. Na entrada, especifique o `secretName` nome do segredo na secção.

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
    > Substitua `<guestbook-secret-name>` no recurso Ingress acima com o nome do seu segredo. Guarde o recurso Ingress `ing-guestbook-tls.yaml`acima num nome de ficheiro .

1. Implementar ing-guestbook-tls.yaml executando

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. Verifique o registo do controlador de entrada para obter o estado de implantação.

Agora `guestbook` a aplicação estará disponível tanto em HTTP como em HTTPS.

### <a name="with-specified-hostname"></a>Com nome de anfitrião especificado

Também pode especificar o nome de anfitrião na entrada para configurações e serviços Multiplex TLS.
Especificando o nome de anfitrião, o serviço de guestbook só estará disponível no anfitrião especificado.

1. Defina a seguinte entrada.
    Na entrada, especifique o `secretName` nome do segredo na `hosts` secção e substitua o nome de anfitrião na secção em conformidade.

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

1. Implementar `ing-guestbook-tls-sni.yaml` por executar

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. Verifique o registo do controlador de entrada para obter o estado de implantação.

Agora `guestbook` a aplicação estará disponível tanto em HTTP`<guestbook.contoso.com>` como EM HTTPS apenas no anfitrião especificado (neste exemplo).

## <a name="integrate-with-other-services"></a>Integrar noutros serviços

A seguinte entrada permitirá adicionar caminhos adicionais a esta entrada e redirecionar esses caminhos para outros serviços:

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
