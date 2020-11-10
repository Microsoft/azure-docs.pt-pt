---
title: Configure o registo de contentores incorporados para o Azure Red Hat OpenShift 4
description: Configure o registo de contentores incorporados para o Azure Red Hat OpenShift 4
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 917da58c7f5ac2294fc30cd385a4834fde3f5f0b
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414802"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Configure o registo de contentores incorporados para o Azure Red Hat OpenShift 4

Neste artigo, você irá configurar o registo de imagem de recipiente incorporado para um cluster Azure Red Hat OpenShift (ARO) 4. Vai aprender a:

> [!div class="checklist"]
> * Configurar o Azure AD
> * Configurar o OpenID Connect
> * Aceda ao registo de imagem do contentor incorporado

## <a name="prerequisites"></a>Pré-requisitos

* Crie um cluster seguindo os passos em [Criar um aglomerado Azure Red Hat OpenShift 4](/azure/openshift/tutorial-create-cluster). Certifique-se de criar o cluster com o `--pull-secret` argumento para `az aro create` .  Isto é necessário se quiser configurar o Azure AD para iniciar sação, conforme exigido por este artigo.
* Ligue-se ao cluster seguindo os passos em [Connect a um cluster Azure Red Hat OpenShift 4](/azure/openshift/tutorial-connect-cluster).
   * Certifique-se de seguir os passos em "Instalar o CLI OpenShift" porque usaremos o `oc` comando mais tarde neste artigo.
   * Tome nota do URL da consola de cluster, que parece `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` . Os valores para `<random>` e `<region>` serão usados mais tarde neste artigo.
   * Reparem nas `kubeadmin` credenciais. Serão usados mais tarde neste artigo.

## <a name="set-up-azure-active-directory"></a>Configurar o Diretório Ativo Azure

O Azure Ative Directory (Azure AD) implementa o OpenID Connect (OIDC). O OIDC permite-lhe utilizar o Azure AD para iniciar seduca no cluster ARO. Siga os passos abaixo para configurar a Azure AD.

1. Crie um inquilino AZure AD seguindo os passos em [Quickstart: Crie um inquilino.](/azure/active-directory/develop/quickstart-create-new-tenant) Sua conta Azure pode já ter um inquilino. Em caso afirmativo, pode saltar a criação de um se tiver privilégios suficientes no arrendatário para seguir os passos. Note a **identificação do** seu inquilino. Este valor vai ser utilizado mais à frente.
2. Crie pelo menos um utilizador Azure AD seguindo os passos em [Adicionar ou eliminar utilizadores usando o Azure Ative Directory](/azure/active-directory/fundamentals/add-users-azure-active-directory). Pode utilizar estas contas ou as suas para testar a aplicação. Tome nota dos endereços de e-mail e senhas destas contas para iniciar scontabilidade.
3. Crie um novo registo de aplicações no seu inquilino Azure AD seguindo os passos em [Quickstart: Registe uma aplicação com a plataforma de identidade Microsoft.](/azure/active-directory/develop/quickstart-register-app) 
   1. Recorde a nota nos pré-requisitos sobre os valores para `<random>` e `<region>` . Utilize estes valores para criar um URI de acordo com a seguinte fórmula:

      `https://oauth-openshift.apps.<random>.<region>.aroapp.io/oauth2callback/openid`
   1. Quando chegar ao passo que lida com o campo **URI de redirecionamento,** introduza o URI a partir do passo anterior.
   1. Selecione **Registar**.
   1. Na página **geral** da aplicação, note o valor mostrado para **o ID da Aplicação (cliente),** como mostrado aqui.
      :::image type="content" source="media/built-in-container-registry/azure-ad-app-overview-client-id.png" alt-text="Página geral da Aplicação AD Azure.":::

4. Criar um novo segredo de cliente. 
   1. No recém-criado registo de candidaturas, selecione **Certificados & segredos** no painel de navegação à esquerda.
   1. Selecione **Novo segredo do cliente**.
   1. Forneça **uma descrição** e selecione **Adicionar**.
   1. Poupe para além do valor secreto do **cliente** gerado. Este valor será usado mais tarde no artigo.

### <a name="add-openid-connect-identity-provider"></a>Adicionar fornecedor de identidade OpenID Connect

A ARO fornece um registo de contentores embutido.  Para aceder a ele, configurar um fornecedor de identidade (IDP) utilizando o Azure AD OIDC seguindo estes passos.

1. Inscreva-se na consola web OpenShift a partir do seu navegador como `kubeadmin` .  Este é o mesmo procedimento utilizado na execução dos passos em [Tutorial: Conecte-se a um cluster Azure Red Hat OpenShift 4](/azure/openshift/tutorial-connect-cluster).
1. No painel de navegação **Administration** esquerdo, selecione  >  **Definições de Cluster de** Administração .
1. No meio da página, selecione **Configuração Global**.
1. Encontre **o OAuth** na coluna de Recursos de **Configuração** da tabela e selecione-a.
1. Em **Fornecedores de Identidade** , selecione **Adicionar** e escolha **OpenID Connect**.
1. Definir **Nome** como **openid**.  Este valor pode já estar preenchido.
1. Desateia o **ID do Cliente** e o Segredo do **Cliente** como os valores do passo anterior.
1. Siga este passo para encontrar o valor para **o URL do emitente.**
   1. **\<tenant-id>** Substitua-a guardada durante a secção Configurar o **Azure Ative Directory** no URL `https://login.microsoftonline.com/<tenant-id>/v2.0/.well-known/openid-configuration` .
   1. Abra o URL no mesmo navegador que tinha usado para interagir com o portal Azure.
   1. Copie o valor do **emitente** de propriedade no corpo JSON devolvido e cole-o na **URL do emitente** com rótulo textbox .  O valor **do emitente** será algo `https://login.microsoftonline.com/44p4o433-2q55-474q-on88-4on94469o74n/v2.0` parecido.
1. Mova-se para a parte inferior da página e **selecione Adicionar**.
   :::image type="content" source="media/built-in-container-registry/openid-connect-identity-provider.png" alt-text="Ligação OpenID em OpenShift.":::
1. Assine fora da consola web OpenShift selecionando o botão **kube:admin** no canto superior direito da janela do navegador e escolhendo **Log out**.

### <a name="access-the-built-in-container-image-registry"></a>Aceda ao registo de imagem do contentor incorporado

As seguintes instruções permitem o acesso ao registo incorporado.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>Defina o utilizador AD AZure para ser administrador

1. Inscreva-se na consola web OpenShift a partir do seu navegador utilizando as credenciais de um utilizador AZure AD.

   1. Utilize uma função de janela de navegador InPrivate, Incógnita ou outra janela de navegador equivalente para iniciar sôm na consola.
   1. A janela ficará diferente depois de ter ativado o OIDC.
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="Sinal de ligação aberta e ativada na janela.":::
   1. Selecione **openid**

   > [!NOTE]
   > Tome nota do nome de utilizador e palavra-passe que utiliza para iniciar seducaqui. Este nome de utilizador e palavra-passe funcionarão como administrador para outras ações neste e noutros artigos.
1. Inscreva-se com o CLI OpenShift utilizando os seguintes passos.  Para discussão, este processo é conhecido como `oc login` .
   1. No topo direito da consola web, expanda o menu de contexto do utilizador inscrito e, em seguida, selecione **Copy Login Command**.
   1. Inscreva-se numa nova janela de separador com o mesmo utilizador, se necessário.
   1. Selecione **Display Token**.
   1. Copie o valor listado abaixo **Iniciar sessão com este token** para a área de transferência e execute-o numa concha, como mostrado aqui.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

1. Corra `oc whoami` na consola e note a saída como **\<aad-user>** .  Usaremos este valor mais tarde no artigo.
1. Assine fora da consola web OpenShift. Selecione o botão no superior direito da janela do navegador rotulado como o **\<aad-user>** e escolha Log **out**.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>Conceda ao utilizador Azure AD as funções necessárias para a interação do registo

1. Inscreva-se na consola web OpenShift a partir do seu navegador utilizando as `kubeadmin` credenciais.
1. Inscreva-se no CLI OpenShift com o símbolo para `kubeadmin` seguir os passos `oc login` acima, mas faça-os depois de iniciar sessão na consola web com `kubeadmin` .
1. Execute os seguintes comandos para permitir o acesso ao registo incorporado para o **utilizador aad**.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   ```

   A saída deve ser semelhante à seguinte.

   ```bash
   Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge
   ```

   A saída deve ser semelhante à seguinte.

   ```bash
   config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>
   ```

   A saída deve ser semelhante à seguinte.

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   ```

   A saída deve ser semelhante à seguinte.

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
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

Utilize o registo de imagem do contentor incorporado implantando uma aplicação no OpenShift.  Para aplicações Java, siga o guia de como fazer, [implemente uma aplicação Java com Open Liberty/WebSphere Liberty num cluster Azure Red Hat OpenShift 4](howto-deploy-java-liberty-app.md).
