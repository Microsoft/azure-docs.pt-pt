---
title: OpenShift no tarefas de pós-implementação do Azure | Documentos da Microsoft
description: Tarefas adicionais para depois de um cluster do OpenShift foi implementada.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/02/2019
ms.author: haroldw
ms.openlocfilehash: b99490102dd4f2c08a11be066b2ddc5fd6b7ea7b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57535840"
---
# <a name="post-deployment-tasks"></a>Tarefas de pós-implementação

Depois de implementar um cluster do OpenShift, pode configurar itens adicionais. Este artigo abrange:

- Como configurar o início de sessão único com o Azure Active Directory (Azure AD)
- Como configurar registos de Azure Monitor para monitorizar o OpenShift
- Como configurar métricas e registo
- Como instalar o Open Service Broker for Azure (OSBA)

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Configurar início de sessão único com o Azure Active Directory

Para utilizar o Azure Active Directory para autenticação, primeiro terá de criar um registo de aplicação do Azure AD. Este processo envolve dois passos: criar o registo de aplicações e a configuração de permissões.

### <a name="create-an-app-registration"></a>Criar um registo de aplicações

Estes passos utilizam a CLI do Azure para criar o registo de aplicações e a GUI (portal) para definir as permissões. Para criar o registo de aplicações, terá das seguintes cinco partes de informações:

- Nome a apresentar: Nome de registo de aplicação (por exemplo, OCPAzureAD)
- Home page do: URL da consola OpenShift (por exemplo, https://masterdns343khhde.westus.cloudapp.azure.com/console)
- URI identificador: URL da consola OpenShift (por exemplo, https://masterdns343khhde.westus.cloudapp.azure.com/console)
- URL de resposta: Principal do URL público e o nome do registo de aplicação (por exemplo, https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD)
- Palavra-passe: Palavra-passe segura (utilize uma palavra-passe segura)

O exemplo seguinte cria um registo de aplicações ao utilizar as informações anteriores:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

Se o comando for bem-sucedida, obtém uma saída JSON semelhante a:

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

Tome nota da propriedade appId devolvida do comando de um passo posterior.

No portal do Azure:

1.  Selecione **do Azure Active Directory** > **registo da aplicação**.
2.  Procurar o registo de aplicação (por exemplo, OCPAzureAD).
3.  Nos resultados, clique o registo de aplicações.
4.  Sob **configurações**, selecione **permissões obrigatórias**.
5.  Sob **permissões obrigatórias**, selecione **Add**.

  ![Registo de aplicações](media/openshift-post-deployment/app-registration.png)

6.  Clique em passo 1: Selecione a API e, em seguida, clique em **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)**. Clique em **selecione** na parte inferior.

  ![API de selecione registo de aplicações](media/openshift-post-deployment/app-registration-select-api.png)

7.  No passo 2: Selecione as permissões, selecione **iniciar sessão e ler o perfil de utilizador** sob **permissões delegadas**e, em seguida, clique em **selecionar**.

  ![Acesso de registo de aplicações](media/openshift-post-deployment/app-registration-access.png)

8.  Selecione **Done** (Concluído).

### <a name="configure-openshift-for-azure-ad-authentication"></a>Configurar o OpenShift para autenticação do Azure AD

Para configurar o OpenShift para utilizar o Azure AD como fornecedor de autenticação, o ficheiro de /etc/origin/master/master-config.yaml tem de ser editado em todos os nós principais.

Encontre o ID do inquilino com o seguinte comando da CLI:

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

Certifique-se de que o texto se alinha corretamente em identityProviders. Encontre o ID do inquilino com o seguinte comando da CLI: ```az account show```

Reinicie os serviços de mestres de OpenShift em todos os nós principais:

**OpenShift Container Platform (OCP) com vários modelos de estrutura mestres**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**OpenShift Container Platform com uma única mestra**

```bash
sudo systemctl restart atomic-openshift-master
```

**OKD com vários modelos de estrutura mestres**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**OKD com uma única mestra**

```bash
sudo systemctl restart origin-master
```

Na consola do OpenShift, verá agora duas opções para a autenticação: htpasswd_auth e [registo da aplicação].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>Monitorizar o OpenShift com os registos do Azure Monitor

Existem três formas de adicionar o agente Log Analytics ao OpenShift.
- Instalar o agente do Log Analytics para Linux diretamente em cada nó do OpenShift
- Ativar a extensão de VM do Azure Monitor em cada nó do OpenShift
- Instalar o agente Log Analytics como um daemon-conjunto do OpenShift

As instruções completas estão localizadas aqui: https://docs.microsoft.com/azure/log-analytics/log-analytics-containers#configure-a-log-analytics-agent-for-red-hat-openshift.

## <a name="configure-metrics-and-logging"></a>Configurar métricas e registo

Com base no ramo, os modelos Azure Resource Manager para o OpenShift Container Platform e OKD podem fornecer parâmetros de entrada para ativar as métricas e registo como parte da instalação.

A oferta do Marketplace do OpenShift Container Platform também fornece uma opção para ativar o registo durante a instalação de cluster e de métricas.

Se as métricas / registo não foi ativado durante a instalação do cluster, pode facilmente ser ativados após o fato.

### <a name="ansible-inventory-pre-work"></a>Trabalho de pré-lançamento de inventário do Ansible

Verifique se o ficheiro de inventário do ansible (/ etc/ansible/anfitriões) tem as variáveis apropriadas para métricas / registo. O ficheiro de inventário pode ser encontrado em diferentes anfitriões com base no modelo utilizado.

Para o modelo de contentores do OpenShift e a oferta do Marketplace, o ficheiro de inventário está localizado no anfitrião de bastião. Para o modelo OKD, o ficheiro de inventário está localizado no anfitrião master-0 ou o anfitrião de bastião com base no ramo em utilização.

1. Edite o ficheiro de /etc/ansible/hosts e adicione as seguintes linhas depois da secção de fornecedor de identidade (# HTPasswdPasswordIdentityProvider ativar). Se estas linhas já estiverem presentes, não os adicione novamente.

   OpenShift / versões OKD 3.9 e anteriores

   ```yaml
   # Setup metrics
   openshift_hosted_metrics_deploy=false
   openshift_metrics_cassandra_storage_type=dynamic
   openshift_metrics_start_cluster=true
   openshift_metrics_hawkular_nodeselector={"type":"infra"}
   openshift_metrics_cassandra_nodeselector={"type":"infra"}
   openshift_metrics_heapster_nodeselector={"type":"infra"}
   openshift_hosted_metrics_public_url=https://metrics.$ROUTING/hawkular/metrics

   # Setup logging
   openshift_hosted_logging_deploy=false
   openshift_hosted_logging_storage_kind=dynamic
   openshift_logging_fluentd_nodeselector={"logging":"true"}
   openshift_logging_es_nodeselector={"type":"infra"}
   openshift_logging_kibana_nodeselector={"type":"infra"}
   openshift_logging_curator_nodeselector={"type":"infra"}
   openshift_master_logging_public_url=https://kibana.$ROUTING
   ```

   OpenShift / versões OKD 3.10 e posteriores

   ```yaml
   # Setup metrics
   openshift_metrics_install_metrics=false
   openshift_metrics_start_cluster=true
   openshift_metrics_hawkular_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_metrics_cassandra_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_metrics_heapster_nodeselector={"node-role.kubernetes.io/infra":"true"}

   # Setup logging
   openshift_logging_install_logging=false
   openshift_logging_fluentd_nodeselector={"logging":"true"}
   openshift_logging_es_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_logging_kibana_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_logging_curator_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_logging_master_public_url=https://kibana.$ROUTING
   ```

3. Substitua a cadeia utilizada para a opção de openshift_master_default_subdomain no mesmo ficheiro /etc/ansible/hosts $ROUTING.

### <a name="azure-cloud-provider-in-use"></a>Fornecedor de Cloud do Azure em utilização

SSH para o nó de bastion ou o primeiro nó mestre (com base no modelo e o ramo em utilização) utilizando as credenciais fornecidas durante a implementação. Emita o seguinte comando:

**OpenShift Container Platform 3.7 e anterior**

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

**OpenShift Container Platform 3.9 e posterior**

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

**OKD 3.7 e anterior**

```bash
ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

**OKD 3.9 e posterior**

```bash
ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook ~/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Fornecedor de Cloud do Azure não está em utilização

SSH para o nó de bastion ou o primeiro nó mestre (com base no modelo e o ramo em utilização) utilizando as credenciais fornecidas durante a implementação. Emita o seguinte comando:


**OpenShift Container Platform 3.7 e anterior**

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True
```

**OpenShift Container Platform 3.9 e posterior**

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

**OKD 3.7 e anterior**

```bash
ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True
```

**OKD 3.9 e posterior**

```bash
ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True
ansible-playbook ~/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Instalar o Open Service Broker for Azure (OSBA)

Abra o Service Broker para o Azure ou o OSBA, permite aprovisionar serviços Cloud do Azure diretamente a partir do OpenShift. OSBA numa implementação de API aberta de Mediador de serviço para o Azure. A API do Mediador de serviço aberta é uma especificação que define uma linguagem comum para fornecedores de aplicativos nativos da cloud podem utilizar para gerir serviços cloud, sem bloqueio-in de nuvem.

Para instalar o OSBA OpenShift, siga as instruções localizadas aqui: https://github.com/Azure/open-service-broker-azure#openshift-project-template. 

## <a name="next-steps"></a>Passos Seguintes

- [Introdução ao OpenShift Container Platform](https://docs.openshift.com/container-platform)
- [Guia de introdução OKD](https://docs.okd.io/latest)
