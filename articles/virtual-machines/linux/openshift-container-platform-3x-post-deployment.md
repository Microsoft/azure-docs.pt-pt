---
title: Plataforma de contêiner OpenShift 3,11 em tarefas de pós-implantação do Azure | Microsoft Docs
description: Tarefas adicionais para depois que um cluster da plataforma de contêiner OpenShift 3,11 foi implantado.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: c1e04f048c081da4777045e5bee43991c95b4625
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392777"
---
# <a name="post-deployment-tasks"></a>Tarefas pós-implantação

Depois de implantar um cluster OpenShift, você pode configurar itens adicionais. Este artigo aborda:

- Como configurar o logon único usando o Azure Active Directory (Azure AD)
- Como configurar logs de Azure Monitor para monitorar o OpenShift
- Como configurar as métricas e o registro em log
- Como instalar o Open Service Broker for Azure (OSBA)

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Configurar o logon único usando o Azure Active Directory

Para usar Azure Active Directory para autenticação, primeiro você precisa criar um registro de aplicativo do Azure AD. Esse processo envolve duas etapas: criar o registro do aplicativo e configurar permissões.

### <a name="create-an-app-registration"></a>Criar um registro de aplicativo

Essas etapas usam o CLI do Azure para criar o registro do aplicativo e a GUI (Portal) para definir as permissões. Para criar o registro do aplicativo, você precisa das cinco informações a seguir:

- Nome de exibição: nome de registro do aplicativo (por exemplo, OCPAzureAD)
- Home Page: URL do console do OpenShift (por exemplo, https://masterdns343khhde.westus.cloudapp.azure.com/console)
- URI do identificador: URL do console do OpenShift (por exemplo, https://masterdns343khhde.westus.cloudapp.azure.com/console)
- URL de resposta: a URL pública mestre e o nome de registro do aplicativo (por exemplo, https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD)
- Senha: senha segura (use uma senha forte)

O exemplo a seguir cria um registro de aplicativo usando as informações anteriores:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

Se o comando for bem-sucedido, você obterá uma saída JSON semelhante a:

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

Anote a propriedade appId retornada do comando para uma etapa posterior.

No portal do Azure:

1. Selecione **Azure Active Directory** > **registro de aplicativo**.
2. Pesquise o registro do aplicativo (por exemplo, OCPAzureAD).
3. Nos resultados, clique no registro do aplicativo.
4. Em **configurações**, selecione **permissões necessárias**.
5. Em **permissões necessárias**, selecione **Adicionar**.

   ![Registro do aplicativo](media/openshift-post-deployment/app-registration.png)

6. Clique em etapa 1: selecionar API e, em seguida, clique em **Windows Azure Active Directory (Microsoft. Azure. ActiveDirectory)** . Clique em **selecionar** na parte inferior.

   ![API de seleção de registro do aplicativo](media/openshift-post-deployment/app-registration-select-api.png)

7. Na etapa 2: selecionar permissões, selecione **entrar e ler perfil do usuário** em **permissões delegadas**e clique em **selecionar**.

   ![Acesso ao registro do aplicativo](media/openshift-post-deployment/app-registration-access.png)

8. Selecione **Done** (Concluído).

### <a name="configure-openshift-for-azure-ad-authentication"></a>Configurar o OpenShift para autenticação do Azure AD

Para configurar o OpenShift para usar o Azure AD como um provedor de autenticação, o arquivo/etc/Origin/Master/Master-config.YAML deve ser editado em todos os nós mestres.

Localize a ID do locatário usando o seguinte comando da CLI:

```azurecli
az account show
```

No arquivo YAML, localize as seguintes linhas:

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

Verifique se o texto está alinhado corretamente em identityProviders. Localize a ID do locatário usando o seguinte comando da CLI: ```az account show```

Reinicie os serviços do OpenShift Master em todos os nós mestres:

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

No console do OpenShift, agora você verá duas opções de autenticação: htpasswd_auth e [registro do aplicativo].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>Monitorar OpenShift com logs de Azure Monitor

Há três maneiras de adicionar o agente de Log Analytics ao OpenShift.
- Instalar o agente de Log Analytics para Linux diretamente em cada nó do OpenShift
- Habilitar Azure Monitor extensão de VM em cada nó do OpenShift
- Instalar o agente de Log Analytics como um OpenShift daemon-Set

Leia as [instruções](https://docs.microsoft.com/azure/log-analytics/log-analytics-containers#configure-a-log-analytics-agent-for-red-hat-openshift) completas para obter mais detalhes.

## <a name="configure-metrics-and-logging"></a>Configurar métricas e registro em log

Com base na ramificação, os modelos de Azure Resource Manager para a plataforma de contêiner OpenShift e OKD podem fornecer parâmetros de entrada para habilitar as métricas e o registro em log como parte da instalação.

A oferta do Marketplace da plataforma de contêiner do OpenShift também fornece uma opção para habilitar as métricas e o registro em log durante a instalação do cluster.

Se as métricas/log não tiverem sido habilitadas durante a instalação do cluster, elas poderão ser facilmente habilitadas após o fato.

### <a name="azure-cloud-provider-in-use"></a>Provedor de nuvem do Azure em uso

SSH para o nó de bastiões ou primeiro nó mestre (com base no modelo e no Branch em uso) usando as credenciais fornecidas durante a implantação. Emita o seguinte comando:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>O provedor de nuvem do Azure não está em uso

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Instalar o Open Service Broker for Azure (OSBA)

Abra Service Broker para o Azure, ou OSBA, permite que você provisione os serviços de nuvem do Azure diretamente do OpenShift. OSBA em uma implementação de API aberta Service Broker para o Azure. A API aberta Service Broker é uma especificação que define uma linguagem comum para provedores de nuvem que os aplicativos nativos de nuvem podem usar para gerenciar serviços de nuvem sem bloqueio.

Para instalar o OSBA no OpenShift, siga as instruções localizadas aqui: https://github.com/Azure/open-service-broker-azure#openshift-project-template. 
> [!NOTE]
> Conclua apenas as etapas na seção modelo de projeto OpenShift e não na seção instalação inteira.

## <a name="next-steps"></a>Passos seguintes

- [Introdução à plataforma de contêiner do OpenShift](https://docs.openshift.com)
