---
title: Utilize certificados LetsEncrypt.org com Gateway de Aplicação
description: Este artigo fornece informações sobre como obter um certificado de LetsEncrypt.org e usá-lo no seu Gateway de Aplicação para clusters AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 92e9747865f1a0910c8bae4001cc597ae9ea3da6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73957973"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>Utilize certificados com LetsEncrypt.org no Gateway de Aplicação para clusters AKS

Esta secção configura o seu AKS para alavancar [LetsEncrypt.org](https://letsencrypt.org/) e obter automaticamente um certificado TLS/SSL para o seu domínio. O certificado será instalado no Application Gateway, que executará a rescisão SSL/TLS para o seu cluster AKS. A configuração aqui descrita utiliza o add-on kubernetes [cert-manager,](https://github.com/jetstack/cert-manager) que automatiza a criação e gestão de certificados.

Siga os passos abaixo para instalar [cert-manager](https://docs.cert-manager.io) no seu cluster AKS existente.

1. Gráfico de leme

    Executar o seguinte script `cert-manager` para instalar o gráfico de leme. Isto irá:

    - criar um `cert-manager` novo espaço de nome no seu AKS
    - criar os seguintes CRDs: Certificado, Desafio, ClusterEmedr, Emitente, Ordem
    - instalar gráfico cert-manager (a partir de [docs.cert-manager.io)](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)

    ```bash
    #!/bin/bash

    # Install the CustomResourceDefinition resources separately
    kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

    # Create the namespace for cert-manager
    kubectl create namespace cert-manager

    # Label the cert-manager namespace to disable resource validation
    kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

    # Add the Jetstack Helm repository
    helm repo add jetstack https://charts.jetstack.io

    # Update your local Helm chart repository cache
    helm repo update

    # Install the cert-manager Helm chart
    helm install \
      --name cert-manager \
      --namespace cert-manager \
      --version v0.8.0 \
      jetstack/cert-manager
    ```

2. Recurso ClusterIssuer

    Criar `ClusterIssuer` um recurso. É exigido por `cert-manager` representar `Lets Encrypt` a autoridade do certificado onde os certificados assinados serão obtidos.

    Ao utilizar o recurso `ClusterIssuer` não-nomeado, o cert-manager emitirá certificados que podem ser consumidos a partir de vários espaços de nome. `Let’s Encrypt`utiliza o protocolo ACME para verificar se controla um determinado nome de domínio e para lhe emitir um certificado. Mais detalhes sobre `ClusterIssuer` a configuração de propriedades [aqui](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html). `ClusterIssuer`instruirá `cert-manager` a emissão `Lets Encrypt` de certificados utilizando o ambiente de paragem utilizado para os testes (o certificado de raiz não está presente nas lojas fidedignas de navegador/cliente).

    O tipo de desafio padrão no `http01`YAML abaixo é . Outros desafios estão documentados em [letsencrypt.org - Challenge Types](https://letsencrypt.org/docs/challenge-types/)

    > [!IMPORTANT] 
    > Atualização `<YOUR.EMAIL@ADDRESS>` no YAML abaixo

    ```bash
    #!/bin/bash
    kubectl apply -f - <<EOF
    apiVersion: certmanager.k8s.io/v1alpha1
    kind: ClusterIssuer
    metadata:
    name: letsencrypt-staging
    spec:
    acme:
        # You must replace this email address with your own.
        # Let's Encrypt will use this to contact you about expiring
        # certificates, and issues related to your account.
        email: <YOUR.EMAIL@ADDRESS>
        # ACME server URL for Let’s Encrypt’s staging environment.
        # The staging environment will not issue trusted certificates but is
        # used to ensure that the verification process is working properly
        # before moving to production
        server: https://acme-staging-v02.api.letsencrypt.org/directory
        privateKeySecretRef:
        # Secret resource used to store the account's private key.
        name: example-issuer-account-key
        # Enable the HTTP-01 challenge provider
        # you prove ownership of a domain by ensuring that a particular
        # file is present at the domain
        http01: {}
    EOF
    ```

3. Implementar app

    Criar um recurso Ingress `guestbook` para expor a aplicação utilizando o Gateway de Aplicação com o Certificado lets encrypt.

    Certifique-se de que o Gateway de Aplicação tem uma `azure.com` configuração IP `Azure DNS Zone` frontend pública com um nome DNS (utilizando o domínio predefinido, ou disponibilizando um serviço, e atribuindo o seu próprio domínio personalizado).
    Note a anotação `certmanager.k8s.io/cluster-issuer: letsencrypt-staging`, que diz ao cert-manager para processar o recurso Ingress marcado.

    > [!IMPORTANT] 
    > Atualização `<PLACEHOLDERS.COM>` no YAML abaixo com o seu próprio domínio (ou o Gateway aplicação, por exemplo' 'kh-aks-ingress.westeurope.cloudapp.azure.com')

    ```bash
    kubectl apply -f - <<EOF
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
    name: guestbook-letsencrypt-staging
    annotations:
        kubernetes.io/ingress.class: azure/application-gateway
        certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    spec:
    tls:
    - hosts:
        - <PLACEHOLDERS.COM>
        secretName: guestbook-secret-name
    rules:
    - host: <PLACEHOLDERS.COM>
        http:
        paths:
        - backend:
            serviceName: frontend
            servicePort: 80
    EOF
    ```

    Após alguns segundos, `guestbook` pode aceder ao serviço através do url 'Gateway HTTPS' da aplicação utilizando o certificado de **encenação** `Lets Encrypt` emitido automaticamente.
    O seu navegador pode avisá-lo de uma autoridade cert inválida. O certificado de encenação é emitido por `CN=Fake LE Intermediate X1`. Esta é uma indicação de que o sistema funcionou como esperado e que está pronto para o seu certificado de produção.

4. Certificado de Produção

    Assim que o seu certificado de preparação for configurado com sucesso, pode mudar para um servidor ACME de produção:
    1. Substitua a anotação de encenação no seu recurso Ingress por:`certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Elimine a `ClusterIssuer` encenação existente que criou no passo anterior e crie uma nova substituindo o servidor ACME do ClusterIssuer YAML acima com`https://acme-v02.api.letsencrypt.org/directory`

5. Expiração e Renovação do Certificado

    Antes `Lets Encrypt` de o `cert-manager` certificado expirar, atualizará automaticamente o certificado na loja secreta Kubernetes. Nessa altura, o Application Gateway Ingress Controller aplicará o segredo atualizado referenciado nos recursos de entrada que está a usar para configurar o Gateway de Aplicação.
