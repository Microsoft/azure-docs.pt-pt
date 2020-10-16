---
title: Utilize certificados de LetsEncrypt.org com Gateway de aplicação
description: Este artigo fornece informações sobre como obter um certificado de LetsEncrypt.org e usá-lo no seu Gateway de Aplicação para clusters AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: df8722e8160538daa1535711092790dbb2405097
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84807037"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>Utilize certificados com LetsEncrypt.org no Gateway de Aplicações para clusters AKS

Esta secção configura o seu AKS para alavancar [LetsEncrypt.org](https://letsencrypt.org/) e obter automaticamente um certificado TLS/SSL para o seu domínio. O certificado será instalado no Application Gateway, que realizará a rescisão SSL/TLS para o seu cluster AKS. A configuração aqui descrita utiliza o [addon cert-manager](https://github.com/jetstack/cert-manager) Kubernetes, que automatiza a criação e gestão de certificados.

Siga os passos abaixo para instalar [o cert-manager](https://docs.cert-manager.io) no seu cluster AKS existente.

1. Gráfico de leme

    Execute o seguinte script para instalar o gráfico de `cert-manager` leme. Isto será:

    - criar um novo `cert-manager` espaço de nome na sua AKS
    - criar os seguintes CRDs: Certificado, Desafio, ClusterIssuer, Emitente, Ordem
    - instalar gráfico de gestor de certificados (a partir de [docs.cert-manager.io)](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)

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

    Criar um `ClusterIssuer` recurso. É exigido por `cert-manager` representar a autoridade de `Lets Encrypt` certificados onde os certificados assinados serão obtidos.

    Ao utilizar o recurso não-nomeado, o `ClusterIssuer` cert-manager emitirá certificados que podem ser consumidos a partir de vários espaços de nome. `Let’s Encrypt` utiliza o protocolo ACME para verificar se controla um determinado nome de domínio e para lhe emitir um certificado. Mais detalhes sobre a configuração de `ClusterIssuer` propriedades [aqui.](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html) `ClusterIssuer``cert-manager`instruir-se-á a emitir certificados utilizando o `Lets Encrypt` ambiente de preparação utilizado para testes (o certificado raiz não está presente nas lojas de confiança do navegador/cliente).

    O tipo de desafio padrão no YAML abaixo é `http01` . Outros desafios são documentados em [letsencrypt.org - Challenge Types](https://letsencrypt.org/docs/challenge-types/)

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

3. Implementar App

    Crie um recurso Ingress para expor a `guestbook` aplicação utilizando o Gateway de Aplicação com o Certificado De Criscrição Deses.

    Certifique-se de que o Gateway de Aplicação tem uma configuração IP frontal com um nome DNS (utilizando o domínio predefinido, `azure.com` ou fornecendo um `Azure DNS Zone` serviço, e atribuindo o seu próprio domínio personalizado).
    Note a `certmanager.k8s.io/cluster-issuer: letsencrypt-staging` anotação, que diz ao cert-manager para processar o recurso Ingress marcado.

    > [!IMPORTANT] 
    > Atualização `<PLACEHOLDERS.COM>` no YAML abaixo com o seu próprio domínio (ou o Gateway de aplicação um, por exemplo 'kh-aks-ingress.westeurope.cloudapp.azure.com')

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

    Após alguns segundos, pode aceder ao `guestbook` serviço através do url HTTPS do Gateway de Aplicação utilizando o certificado de **encenação** emitido `Lets Encrypt` automaticamente.
    O seu navegador poderá avisá-lo de uma autoridade de certificado inválida. O certificado de encenação é emitido por `CN=Fake LE Intermediate X1` . Esta é uma indicação de que o sistema funcionou como esperado e que está pronto para o seu certificado de produção.

4. Certificado de Produção

    Uma vez que o seu certificado de encenação é configurado com sucesso, pode mudar para um servidor ACME de produção:
    1. Substitua a anotação de encenação no seu recurso Ingress com: `certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Elimine a encenação existente `ClusterIssuer` que criou no passo anterior e crie uma nova substituindo o servidor ACME do ClusterIssuer YAML acima `https://acme-v02.api.letsencrypt.org/directory`

5. Expiração e Renovação do Certificado

    Antes que o `Lets Encrypt` certificado expire, `cert-manager` atualizará automaticamente o certificado na loja secreta kubernetes. Nessa altura, o Controlador de Entrada de Aplicação irá aplicar o segredo atualizado referenciado nos recursos ingress que está a usar para configurar o Gateway de Aplicação.
