---
title: Configurar o registo de contentor incorporado para o Azure Red Hat OpenShift 4
description: Configurar o registo de contentor incorporado para o Azure Red Hat OpenShift 4
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: dda050b8d824f0ff0ac1c84d2f008387de55aedf
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636387"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Configurar o registo de contentor incorporado para o Azure Red Hat OpenShift 4

O Azure Red Hat OpenShift fornece um registo integrado de imagem de contentor chamado [Registo de Contentores Desonciado (OCR)](https://docs.openshift.com/container-platform/4.6/registry/architecture-component-imageregistry.html) que adiciona a capacidade de fornecer automaticamente novos repositórios de imagem a pedido. Isto fornece aos utilizadores uma localização incorporada para as suas incorporações de aplicações para empurrar as imagens resultantes.

Neste artigo, você irá configurar o registo de imagem de recipiente incorporado para um cluster Azure Red Hat OpenShift (ARO) 4. Vai aprender a:

> [!div class="checklist"]
> * Configurar o Azure AD
> * Configurar o OpenID Connect
> * Aceda ao registo de imagem do contentor incorporado

## <a name="before-you-begin"></a>Antes de começar

Este artigo assumiu que tem um aglomerado ARO existente. Se precisar de um cluster ARO, consulte o tutorial da ARO, [Crie um cluster Azure Red Hat OpenShift 4](./tutorial-create-cluster.md). Certifique-se de criar o cluster com o `--pull-secret` argumento para `az aro create` .  Isto é necessário para configurar a autenticação do Azure Ative Directory e o registo de contentores incorporados.

Assim que tiver o seu cluster, ligue-se ao cluster seguindo os passos em [Connect a um cluster Azure Red Hat OpenShift 4](./tutorial-connect-cluster.md).
   * Certifique-se de seguir os passos em "Instalar o CLI OpenShift" porque usaremos o `oc` comando mais tarde neste artigo.
   * Tome nota do URL da consola de cluster, que parece `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` . Os valores para `<random>` e `<region>` serão usados mais tarde neste artigo.
   * Reparem nas `kubeadmin` credenciais. Também serão usados mais tarde neste artigo.

### <a name="configure-azure-active-directory-authentication"></a>Configurar a autenticação do Azure Active Directory 

O Azure Ative Directory (Azure AD) implementa o OpenID Connect (OIDC). O OIDC permite-lhe utilizar o Azure AD para iniciar seduca no cluster ARO. Siga os passos na [autenticação do Diretório Azure Ative](configure-azure-ad-cli.md) para configurar o seu cluster.

## <a name="access-the-built-in-container-image-registry"></a>Aceda ao registo de imagem do contentor incorporado

Agora que definiu os métodos de autenticação para o cluster ARO, vamos permitir o acesso ao registo incorporado.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>Defina o utilizador AD AZure para ser administrador

1. Inscreva-se na consola web OpenShift a partir do seu navegador utilizando as credenciais de um utilizador AZure AD. Vamos aproveitar a autenticação OpenShift OpenID contra o Azure Ative Directory para usar o OpenID para definir o administrador.

   1. Utilize uma função de janela de navegador InPrivate, Incógnita ou outra janela de navegador equivalente para iniciar sôm na consola. A janela ficará diferente depois de ter ativado o OIDC.
   
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="Sinal de ligação aberta e ativada na janela.":::
   1. Selecione **openid**

   > [!NOTE]
   > Tome nota do nome de utilizador e palavra-passe que utiliza para iniciar seducaqui. Este nome de utilizador e palavra-passe funcionarão como administrador para outras ações neste e noutros artigos.
2. Inscreva-se com o CLI OpenShift utilizando os seguintes passos.  Para discussão, este processo é conhecido como `oc login` .
   1. No topo direito da consola web, expanda o menu de contexto do utilizador inscrito e, em seguida, selecione **Copy Login Command**.
   2. Inscreva-se numa nova janela de separador com o mesmo utilizador, se necessário.
   3. Selecione **Display Token**.
   4. Copie o valor listado abaixo **Iniciar sessão com este token** para a área de transferência e execute-o numa concha, como mostrado aqui.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

3. Corra `oc whoami` na consola e note a saída como **\<aad-user>** .  Usaremos este valor mais tarde no artigo.
4. Assine fora da consola web OpenShift. Selecione o botão no superior direito da janela do navegador rotulado como o **\<aad-user>** e escolha Log **out**.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>Conceda ao utilizador Azure AD as funções necessárias para a interação do registo

1. Inscreva-se na consola web OpenShift a partir do seu navegador utilizando as `kubeadmin` credenciais.
1. Inscreva-se no CLI OpenShift com o símbolo para `kubeadmin` seguir os passos `oc login` acima, mas faça-os depois de iniciar sessão na consola web com `kubeadmin` .
1. Execute os seguintes comandos para permitir o acesso ao registo incorporado para o **utilizador aad**.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   
   # Output should look similar to the following.
   # Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge

   # Output should look similar to the following.
   # config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>

   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

#### <a name="obtain-the-container-registry-url"></a>Obtenha o URL de registo de contentores

Utilize o `oc get route` comando como mostrado ao lado para obter o URL de registo do contentor.

```bash
# Note: the value of "Container Registry URL" in the output is the fully qualified registry name.
HOST=$(oc get route default-route --template='{{ .spec.host }}')
echo "Container Registry URL: $HOST"
```

   > [!NOTE]
   > Note a saída da consola do URL do Registo de **Contentores**. Será usado como o nome de registo totalmente qualificado para este guia e os seguintes.

## <a name="next-steps"></a>Passos seguintes

Agora que montou o registo de imagem de contentor embutido, pode começar com a implementação de uma aplicação no OpenShift. Para aplicações Java, consulte [uma aplicação Java com Open Liberty/WebSphere Liberty num cluster Azure Red Hat OpenShift 4](howto-deploy-java-liberty-app.md).