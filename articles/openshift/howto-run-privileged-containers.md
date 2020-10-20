---
title: Executar contentores privilegiados num aglomerado Azure Red Hat OpenShift Microsoft Docs
description: Executar recipientes privilegiados para monitorizar a segurança e a conformidade.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 12/05/2019
keywords: aro, openshift, aquasec, twistlock, chapéu vermelho
ms.openlocfilehash: 914b29410a0f30e5c3d3a893c2e278ecbb83b648
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92218872"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Executar contentores privilegiados num cluster do Azure Red Hat OpenShift

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 será retirado a 30 de junho de 2022. O apoio à criação de novos clusters Azure Red Hat OpenShift 3.11 continua até 30 de novembro de 2020. Após a reforma, os restantes clusters Azure Red Hat OpenShift 3.11 serão encerrados para evitar vulnerabilidades de segurança.
> 
> Siga este guia para [criar um cluster Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Se tiver questões específicas, [contacte-nos.](mailto:arofeedback@microsoft.com)

Não pode executar recipientes privilegiados arbitrários em aglomerados Azure Red Hat OpenShift.
Duas soluções de monitorização e conformidade de segurança são permitidas em clusters ARO.
Este documento descreve as diferenças da documentação genérica de implantação OpenShift dos fornecedores de produtos de segurança.


Leia estas instruções antes de seguir as instruções do vendedor.
Os títulos de secção em etapas específicas do produto abaixo referem-se diretamente aos títulos de secção na documentação do fornecedor.

## <a name="before-you-begin"></a>Antes de começar

A documentação da maioria dos produtos de segurança pressupõe que você tem privilégios de administração de cluster.
Os administradores de clientes não têm todos os privilégios no Azure Red Hat OpenShift. As permissões necessárias para modificar os recursos em todo o cluster são limitadas.

Em primeiro lugar, certifique-se de que o utilizador está ligado ao cluster como administrador de clientes, executando `oc get scc` . Todos os utilizadores que sejam membros do grupo de administração de clientes têm permissões para visualizar as Restrições de Contexto de Segurança (SCCs) no cluster.

Em seguida, certifique-se de que a `oc` versão binária é `3.11.154` .
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Passos específicos do produto para a Segurança Aqua
As instruções de base que vão ser modificadas podem ser encontradas na documentação de [implantação](https://docs.aquasec.com/docs/openshift-red-hat)da Aqua Security . Os passos aqui serão executados em conjunto com a documentação de implantação do Aqua.

O primeiro passo é anotar os SCCs necessários que serão atualizados. Estas anotações impedem que o Sync Pod do cluster reverta quaisquer alterações a estes SSCs.

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>Passo 1: Preparar pré-requisitos
Lembre-se de iniciar sessão no cluster como um administrador de clientes ARO em vez do papel de administrador de cluster.

Crie o projeto e a conta de serviço.
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

Em vez de atribuir o papel de leitor de cluster, atribua o papel de cluster-administrador ao cliente com o seguinte comando.
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

Continue seguindo as instruções restantes no passo 1.  Estas instruções descrevem a criação do segredo para o registo Aqua.

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>Passo 2: Implementar o Servidor Aqua, Base de Dados e Gateway
Siga os passos fornecidos na documentação Aqua para a instalação da aqua-consola.yaml.

Modifique o fornecido `aqua-console.yaml` .  Retire os dois objetos de cima rotulados `kind: ClusterRole` e `kind: ClusterRoleBinding` .  Estes recursos não serão criados, uma vez que o administrador do cliente não tem permissão neste momento para modificar `ClusterRole` e `ClusterRoleBinding` objetos.

A segunda modificação será na `kind: Route` parte do `aqua-console.yaml` . . Substitua o seguinte yaml para o `kind: Route` objeto no `aqua-console.yaml` ficheiro.
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: aqua-web
  name: aqua-web
  namespace: aqua-security
spec:
  port:
    targetPort: aqua-web
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: aqua-web
    weight: 100
  wildcardPolicy: None
```

Siga as instruções restantes.

### <a name="step-3-login-to-the-aqua-server"></a>Passo 3: Iniciar sessão no Servidor Aqua
Esta secção não é modificada de forma alguma.  Siga a documentação do Aqua.

Utilize o seguinte comando para obter o endereço Aqua Console.
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>Passo 4: Implementar os executores aqua
Definir os seguintes campos ao implantar executores:

| Campo          | Valor         |
| -------------- | ------------- |
| Orchestrator   | OpenShift     |
| ServiceAccount | conta aqua  |
| Project        | aqua-segurança |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Passos específicos do produto para Prisma Cloud / Twistlock

As instruções base que vamos modificar podem ser encontradas na documentação de implantação da [Nuvem prisma](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)

Comece por instalar a `twistcli` ferramenta como descrito nas secções "Instalar a Nuvem Prisma" e "Descarregar o software Prisma Cloud".

Criar um novo projeto OpenShift
```
oc new-project twistlock
```

Salte a secção opcional "Empurre as imagens da Nuvem Prisma para um registo privado". Não vai funcionar no Azure Red Hat OpenShift. Use o registo on-line em vez disso.

Pode seguir a documentação oficial enquanto aplica as correções descritas abaixo.
Comece com a secção "Instalar consola".

### <a name="install-console"></a>Instalar consola

Durante `oc create -f twistlock_console.yaml` o Passo 2, obterá um Erro ao criar o espaço de nomes.
Pode ignorá-lo com segurança, o espaço de nome foi criado anteriormente com o `oc new-project` comando.

Utilização `azure-disk` para tipo de armazenamento.

### <a name="create-an-external-route-to-console"></a>Criar uma rota externa para consola

Pode seguir a documentação ou as instruções abaixo se preferir o comando oc.
Copie a seguinte definição de Rota para um ficheiro chamado twistlock_route.yaml no seu computador
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    name: console
  name: twistlock-console
  namespace: twistlock
spec:
  port:
    targetPort: mgmt-http
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: twistlock-console
    weight: 100
  wildcardPolicy: None
```
em seguida, executar:
```
oc create -f twistlock_route.yaml
```

Pode obter o URL atribuído à consola Twistlock com este comando: `oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>Consola configurada

Siga a documentação twistlock.

### <a name="install-defender"></a>Instalar Defender

Durante `oc create -f defender.yaml` o Passo 2, você terá erros ao criar a role role cluster e a ligação de papel de cluster.
Pode ignorá-los.

Os defensores serão implantados apenas em nós computacional. Não é preciso limitá-los com um seletor de nó.
