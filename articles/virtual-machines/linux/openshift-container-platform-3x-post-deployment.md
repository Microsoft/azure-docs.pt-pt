---
title: Plataforma de contentores OpenShift 3.11 em tarefas pós-implantação do Azure
description: Tarefas adicionais para depois de ter sido implantado um cluster da Plataforma de Contentores OpenShift 3.11.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.custom: devx-track-ansible
ms.openlocfilehash: 8e34f73f1c403e3a7d21c6c30844f8b9073b3113
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87373577"
---
# <a name="post-deployment-tasks"></a>Tarefas pós-implantação

Depois de implantar um cluster OpenShift, pode configurar itens adicionais. Este artigo abrange:

- Como configurar um único sign-on utilizando o Azure Ative Directory (Azure AD)
- Como configurar registos do Monitor Azure para monitorizar o OpenShift
- Como configurar métricas e madeireiros
- Como instalar o Open Service Broker para o Azure (OSBA)

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Configurar um único sign-on utilizando o Azure Ative Directory

Para utilizar o Azure Ative Directory para autenticação, primeiro precisa de criar um registo de aplicações AD Azure. Este processo envolve duas etapas: criar o registo da aplicação e configurar permissões.

### <a name="create-an-app-registration"></a>Criar um registo de aplicativos

Estes passos utilizam o CLI Azure para criar o registo da aplicação, e o GUI (portal) para definir as permissões. Para criar o registo da aplicação, precisa das seguintes cinco informações:

- Nome do ecrã: Nome de registo de aplicações (por exemplo, OCPAzureAD)
- Página inicial: URL de consola OpenShift (por exemplo, `https://masterdns343khhde.westus.cloudapp.azure.com/console` )
- Identifier URI: URL de consola openshift (por exemplo, `https://masterdns343khhde.westus.cloudapp.azure.com/console` )
- URL de resposta: URL público-mestre e o nome de registo da aplicação (por exemplo, `https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD` )
- Palavra-passe: Senha segura (use uma palavra-passe forte)

O exemplo a seguir cria um registo de aplicações utilizando as informações anteriores:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

Se o comando for bem sucedido, obtém-se uma saída JSON semelhante a:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD"
  ]
}
```

Tome nota da propriedade appId devolvida do comando para um passo posterior.

No portal do Azure:

1. Selecione **Azure Ative Directory**  >  **App Registration**.
2. Procure o seu registo de aplicações (por exemplo, OCPAzureAD).
3. Nos resultados, clique no registo da aplicação.
4. Em **Definições**, selecione **permissões necessárias**.
5. Sob **permissões necessárias**, selecione **Add**.

   ![Registo de Aplicações](media/openshift-post-deployment/app-registration.png)

6. Clique no Passo 1: Selecione API e, em seguida, clique no **Windows Azure Ative Directory (Microsoft.Azure.ActiveDirectory)**. Clique em **Selecionar** na parte inferior.

   ![Registo de aplicações Selecione API](media/openshift-post-deployment/app-registration-select-api.png)

7. No passo 2: Selecione Permissões, selecione **Iniciar sessão e leia o perfil do utilizador** em **Permissões Delegadas**e, em seguida, clique em **Selecionar**.

   ![Acesso ao Registo de Aplicações](media/openshift-post-deployment/app-registration-access.png)

8. Selecione **Concluído**.

### <a name="configure-openshift-for-azure-ad-authentication"></a>Configurar OpenShift para autenticação Azure AD

Para configurar o OpenShift para utilizar o Azure AD como fornecedor de autenticação, o ficheiro /etc/origem/master/master-config.yaml deve ser editado em todos os nós principais.

Encontre o ID do inquilino utilizando o seguinte comando CLI:

```azurecli
az account show
```

No ficheiro yaml, encontre as seguintes linhas:

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

Insira as seguintes linhas imediatamente após as linhas anteriores:

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

Certifique-se de que o texto está corretamente alinhado nos Protestantes de Identidade. Encontre o ID do inquilino utilizando o seguinte comando CLI: ```az account show```

Reinicie os serviços principais OpenShift em todos os nós principais:

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

Na consola OpenShift, vê agora duas opções de autenticação: htpasswd_auth e [Registo de Aplicações].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>MonitorAr OpenShift com registos do Monitor Azure

Existem três formas de adicionar o agente Log Analytics ao OpenShift.
- Instale o agente Log Analytics para o Linux diretamente em cada nó OpenShift
- Ativar extensão VM do monitor azul em cada nó OpenShift
- Instale o agente Log Analytics como um conjunto de daemon openshift

Leia as [instruções completas](../../azure-monitor/insights/containers.md#configure-a-log-analytics-agent-for-red-hat-openshift) para mais detalhes.

## <a name="configure-metrics-and-logging"></a>Configure métricas e madeireiros

Com base no ramo, os modelos Azure Resource Manager para Plataforma de Contentores OpenShift e OKD podem fornecer parâmetros de entrada para permitir métricas e registos como parte da instalação.

A oferta de Plataforma de Contentores OpenShift também oferece uma opção para permitir métricas e registos durante a instalação do cluster.

Se as métricas/registo não estiverem ativadas durante a instalação do cluster, podem ser facilmente ativadas após o facto.

### <a name="azure-cloud-provider-in-use"></a>Azure Cloud Provider em uso

SSH para o nó de bastião ou primeiro nó mestre (baseado no modelo e ramo em uso) usando as credenciais fornecidas durante a implementação. Emite o seguinte comando:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Azure Cloud Provider não está a ser utilizado

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Instale o Corretor de Serviço Aberto para O Azure (OSBA)

O Open Service Broker for Azure, ou OSBA, permite-lhe a prestação de Serviços Azure Cloud diretamente do OpenShift. OSBA numa implementação API de corretor de serviço aberto para a Azure. A API do Corretor de Serviços Abertos é uma especificação que define uma linguagem comum para fornecedores de nuvem que as aplicações nativas em nuvem podem usar para gerir serviços na nuvem sem bloqueio.

Para instalar a OSBA no OpenShift, siga as instruções aqui localizadas: https://github.com/Azure/open-service-broker-azure#openshift-project-template . 
> [!NOTE]
> Preencha apenas os passos na secção modelo do projeto OpenShift e não toda a secção de Instalação.

## <a name="next-steps"></a>Passos seguintes

- [Começando com plataforma de recipientes openshift](https://docs.openshift.com)
