---
title: Plataforma de contentores OpenShift 3.11 em tarefas de pós-implantação do Azure
description: Tarefas adicionais para depois de um cluster OpenShift Container Platform 3.11 ter sido implementado.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 8d76588ae9124d34902659cc0149063400b6e766
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759531"
---
# <a name="post-deployment-tasks"></a>Tarefas de pós-implantação

Depois de implantar um cluster OpenShift, pode configurar itens adicionais. Este artigo abrange:

- Como configurar um único sign-on utilizando o Azure Ative Directory (Azure AD)
- Como configurar os registos do Monitor Azure para monitorizar o OpenShift
- Como configurar métricas e exploração madeireira
- Como instalar o Open Service Broker para o Azure (OSBA)

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Configure o único sign-on utilizando o Diretório Ativo Azure

Para utilizar o Diretório Ativo Azure para autenticação, primeiro é necessário criar uma inscrição na aplicação Azure AD. Este processo envolve duas etapas: criar o registo da aplicação e configurar permissões.

### <a name="create-an-app-registration"></a>Criar um registo de aplicativos

Estes passos utilizam o Azure CLI para criar o registo da aplicação, e o GUI (portal) para definir as permissões. Para criar o registo da aplicação, precisa das seguintes cinco informações:

- Nome do ecrã: Nome de registo de aplicativos (por exemplo, OCPAzureAD)
- Página inicial: URL de consola `https://masterdns343khhde.westus.cloudapp.azure.com/console`OpenShift (por exemplo, )
- Identificador URI: URL de consola `https://masterdns343khhde.westus.cloudapp.azure.com/console`OpenShift (por exemplo, )
- URL de resposta: Master public URL e `https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD`o nome de registo da aplicação (por exemplo, )
- Palavra-passe: Segura palavra-passe (use uma senha forte)

O exemplo seguinte cria um registo de aplicações utilizando as informações anteriores:

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

1. Selecione Registo de > **aplicações**de **diretório ativo Azure.**
2. Procure o registo da sua aplicação (por exemplo, OCPAzureAD).
3. Nos resultados, clique no registo da aplicação.
4. Em **Definições,** selecione **permissões necessárias**.
5. Sob **permissões requeridas,** selecione **Adicionar**.

   ![Registo de Aplicações](media/openshift-post-deployment/app-registration.png)

6. Clique no Passo 1: Selecione API e, em seguida, clique no **Diretório Ativo do Windows Azure (Microsoft.Azure.ActiveDirectory)**. Clique em **Selecionar** na parte inferior.

   ![Inscrição de aplicativos Selecione API](media/openshift-post-deployment/app-registration-select-api.png)

7. No passo 2: Selecione Permissões, selecione **Iniciar sessão e ler o perfil do utilizador** sob **permissões delegadas**e, em seguida, clique em **Selecionar**.

   ![Acesso ao Registo de Aplicações](media/openshift-post-deployment/app-registration-access.png)

8. Selecione **Done** (Concluído).

### <a name="configure-openshift-for-azure-ad-authentication"></a>Configure OpenShift para autenticação de AD Azure

Para configurar o OpenShift para utilizar o Azure AD como fornecedor de autenticação, o ficheiro /etc/origin/master/master-config.yaml deve ser editado em todos os nós principais.

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

Certifique-se de que o texto se alinha corretamente em identidadeProviders. Encontre o ID do inquilino utilizando o seguinte comando CLI:```az account show```

Reinicie os serviços principais OpenShift em todos os nós principais:

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

Na consola OpenShift, vê agora duas opções para autenticação: htpasswd_auth e [Registo de Aplicações].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>Monitor OpenShift com registos do Monitor Azure

Existem três formas de adicionar o agente Log Analytics ao OpenShift.
- Instale o agente Log Analytics para Linux diretamente em cada nó OpenShift
- Ativar extensão VM do Monitor Azure em cada nó OpenShift
- Instale o agente Log Analytics como um conjunto de daemon OpenShift

Leia as [instruções completas](https://docs.microsoft.com/azure/log-analytics/log-analytics-containers#configure-a-log-analytics-agent-for-red-hat-openshift) para mais detalhes.

## <a name="configure-metrics-and-logging"></a>Configure métricas e exploração madeireira

Com base no ramo, os modelos do Gestor de Recursos Azure para plataforma de contentores OpenShift e OKD podem fornecer parâmetros de entrada para permitir métricas e exploração como parte da instalação.

A oferta openShift container Platform Marketplace também oferece uma opção para ativar métricas e exploração madeireira durante a instalação do cluster.

Se as métricas/exploração não estiverem ativadas durante a instalação do cluster, podem facilmente ser ativadas após o facto.

### <a name="azure-cloud-provider-in-use"></a>Fornecedor de nuvem azure em uso

SSH para o nó do bastião ou primeiro nó principal (com base no modelo e no ramo em uso) utilizando as credenciais fornecidas durante a implantação. Emitir o seguinte comando:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Fornecedor de nuvem azure não está a ser utilizado

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Instalar corretor de serviço aberto para Azure (OSBA)

Open Service Broker para Azure, ou OSBA, permite-lhe fornecer Serviços Azure Cloud diretamente da OpenShift. OSBA numa implementação da API de Corretor de Serviços Abertos para o Azure. A API de Corretor de Serviçoaberto é uma especificação que define uma linguagem comum para fornecedores de nuvem que as aplicações nativas de nuvem podem usar para gerir serviços de nuvem sem bloqueio.

Para instalar a OSBA no OpenShift, https://github.com/Azure/open-service-broker-azure#openshift-project-templatesiga as instruções aqui localizadas: . 
> [!NOTE]
> Apenas complete os passos na secção Modelo de Projeto OpenShift e não toda a secção de Instalação.

## <a name="next-steps"></a>Passos seguintes

- [Começar com plataforma de contentores OpenShift](https://docs.openshift.com)
