---
title: Criar controlador de dados utilizando O Azure Data CLI (azdata)
description: Crie um controlador de dados Azure Arc num cluster típico de Kubernetes multi-nódoas que já criou utilizando o Azure Data CLI (azdata).
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: facb7db73bf7a709b9ed07e460d8653d79f1ed2f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687608"
---
# <a name="create-azure-arc-data-controller-using-the-azure-data-cli-azdata"></a>Criar controlador de dados Azure Arc utilizando o [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Reveja o tópico [Criar o controlador de dados Azure Arc](create-data-controller.md) para obter informações gerais.

Para criar o Controlador de Dados Azure Arc utilizando o [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] sistema de dados, será necessário instalar o [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] controlador de dados.

   [Instale o [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](install-client-tools.md)

Independentemente da plataforma-alvo que escolher, terá de definir as seguintes variáveis ambientais antes da criação para o utilizador do administrador do controlador de dados. Pode fornecer estas credenciais a outras pessoas que necessitem de ter acesso ao controlador de dados, conforme necessário.

**AZDATA_USERNAME** - Nome de utilizador à sua escolha para o utilizador do administrador do controlador de dados. Exemplo: `arcadmin`

**AZDATA_PASSWORD** - Uma palavra-passe à sua escolha para o utilizador do administrador do controlador de dados. A palavra-passe deve ter pelo menos oito caracteres de comprimento e conter caracteres de três dos quatro conjuntos seguintes: letras maiúsculas, letras minúsculas, números e símbolos.

### <a name="linux-or-macos"></a>Linux ou macOS

```console
export AZDATA_USERNAME="<your username of choice>"
export AZDATA_PASSWORD="<your password of choice>"
```

### <a name="windows-powershell"></a>Windows PowerShell

```console
$ENV:AZDATA_USERNAME="<your username of choice>"
$ENV:AZDATA_PASSWORD="<your password of choice>"
```

Terá de se ligar e autenticar a um cluster Kubernetes e ter um contexto Kubernetes existente selecionado antes de iniciar a criação do controlador de dados Azure Arc. A forma como se liga a um cluster ou serviço kubernetes varia. Consulte a documentação para a distribuição ou serviço Kubernetes que está a utilizar sobre como ligar-se ao servidor API da Kubernetes.

Pode verificar se tem uma ligação Kubernetes atual e confirmar o seu contexto atual com os seguintes comandos.

```console
kubectl get namespace
kubectl config current-context
```

### <a name="connectivity-modes"></a>Modos de conectividade

Conforme descrito nos [modos e requisitos de conectividade,](./connectivity.md)o controlador de dados Azure Arc pode ser implantado com o modo de conectividade ou com `direct` o modo de `indirect` conectividade. Com `direct` o modo de conectividade, os dados de utilização são enviados automaticamente e continuamente para o Azure. Nestes artigos, os exemplos especificam o `direct` modo de conectividade da seguinte forma:

   ```console
   --connectivity-mode direct
   ```

   Para criar o controlador com `indirect` o modo de conectividade, atualize os scripts no exemplo especificado abaixo:

   ```console
   --connectivity-mode indirect
   ```

#### <a name="create-service-principal"></a>Criar um principal de serviço

Se estiver a implementar o controlador de dados Azure Arc com `direct` o modo de conectividade, são necessárias credenciais principais de serviço para a conectividade Azure. O diretor de serviço é utilizado para carregar dados de utilização e métricas. 

Siga estes comandos para criar o seu principal de serviço de upload de métricas:

> [!NOTE]
> A criação de um principal de serviço requer [certas permissões em Azure.](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)

Para criar um principal de serviço, atualize o seguinte exemplo. `<ServicePrincipalName>`Substitua-o pelo nome do seu chefe de serviço e execute o comando:

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName>
``` 

Se criou o principal de serviço mais cedo, e apenas precisa de obter as credenciais atuais, executar o seguinte comando para redefinir a credencial.

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

Por exemplo, para criar um principal de serviço chamado `azure-arc-metrics` , executar o seguinte comando

```console
az ad sp create-for-rbac --name azure-arc-metrics
```

Exemplo de saída:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Guarde o `appId` `password` , e `tenant` valores em uma variável ambiente para uso posterior. 

#### <a name="save-environment-variables-in-windows"></a>Guardar variáveis ambientais no Windows

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

#### <a name="save-environment-variables-in-linux-or-macos"></a>Guardar variáveis ambientais em Linux ou macOS

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

#### <a name="save-environment-variables-in-powershell"></a>Guardar variáveis ambientais em PowerShell

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
$Env:SPN_AUTHORITY="https://login.microsoftonline.com"
```

Depois de ter criado o principal de serviço, atribua o principal de serviço à função adequada. 

### <a name="assign-roles-to-the-service-principal"></a>Atribuir funções ao diretor de serviço

Executar este comando para atribuir o principal de serviço à `Monitoring Metrics Publisher` função na subscrição onde estão localizados os recursos da sua caixa de dados:

#### <a name="run-the-command-on-windows"></a>Executar o comando no Windows

> [!NOTE]
> Tem de utilizar cotações duplas para nomes de papéis quando correr a partir de um ambiente Windows.

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role "Contributor" --scope subscriptions/<Subscription ID>
```

#### <a name="run-the-command-on-linux-or-macos"></a>Executar o comando em Linux ou macOS

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

#### <a name="run-the-command-in-powershell"></a>Executar o comando em PowerShell

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

Com o principal de serviço atribuído à função adequada, e o conjunto de variáveis ambientais, pode proceder à criação do controlador de dados 

## <a name="create-the-azure-arc-data-controller"></a>Criar o controlador de dados Azure Arc

> [!NOTE]
> Pode utilizar um valor diferente para o `--namespace` parâmetro do azdata arc dc criar comando nos exemplos abaixo, mas certifique-se de usar esse nome de espaço para o `--namespace parameter` em todos os outros comandos abaixo.

- [Criar no Serviço Azure Kubernetes (AKS)](#create-on-azure-kubernetes-service-aks)
- [Criar no motor AKS no Azure Stack Hub](#create-on-aks-engine-on-azure-stack-hub)
- [Criar em AKS em Azure Stack HCI](#create-on-aks-on-azure-stack-hci)
- [Criar em Azure Red Hat OpenShift (ARO)](#create-on-azure-red-hat-openshift-aro)
- [Criar na Plataforma de Recipientes OpenShift de Chapéu Vermelho (OCP)](#create-on-red-hat-openshift-container-platform-ocp)
- [Criar em fonte aberta, a montante Kubernetes (kubeadm)](#create-on-open-source-upstream-kubernetes-kubeadm)
- [Criar no Serviço AWS Elastic Kubernetes (EKS)](#create-on-aws-elastic-kubernetes-service-eks)
- [Criar no Google Cloud Kubernetes Engine Service (GKE)](#create-on-google-cloud-kubernetes-engine-service-gke)

### <a name="create-on-azure-kubernetes-service-aks"></a>Criar no Serviço Azure Kubernetes (AKS)

Por predefinição, o perfil de implementação AKS utiliza a `managed-premium` classe de armazenamento. A `managed-premium` classe de armazenamento só funcionará se tiver VMs que foram implantados usando imagens VM que têm discos premium.

Se vai utilizar `managed-premium` como classe de armazenamento, então pode executar o seguinte comando para criar o controlador de dados. Substitua os espaços reservados no comando com o nome do grupo de recursos, iD de subscrição e localização Azure.

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Se não tiver a certeza de que classe de armazenamento utilizar, deve utilizar a `default` classe de armazenamento que é suportada independentemente do tipo VM que está a utilizar. Só não vai proporcionar o desempenho mais rápido.

Se quiser usar a `default` aula de armazenamento, pode executar este comando:

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Depois de executar o comando, continue a [monitorizar o estado de criação](#monitoring-the-creation-status).

### <a name="create-on-aks-engine-on-azure-stack-hub"></a>Criar no motor AKS no Azure Stack Hub

Por predefinição, o perfil de implementação utiliza a `managed-premium` classe de armazenamento. A `managed-premium` classe de armazenamento só funcionará se tiver VMs operários que foram implantados usando imagens VM que têm discos premium no Azure Stack Hub.

Pode executar o seguinte comando para criar o controlador de dados utilizando a classe de armazenamento premium gerido:

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Se não tiver a certeza de que classe de armazenamento utilizar, deve utilizar a `default` classe de armazenamento que é suportada independentemente do tipo VM que está a utilizar. No Azure Stack Hub, os discos premium e os discos standard são apoiados pela mesma infraestrutura de armazenamento. Portanto, espera-se que proporcionem o mesmo desempenho geral, mas com diferentes limites de IOPS.

Se quiser usar a `default` aula de armazenamento, pode executar este comando.

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Depois de executar o comando, continue a [monitorizar o estado de criação](#monitoring-the-creation-status).

### <a name="create-on-aks-on-azure-stack-hci"></a>Criar em AKS em Azure Stack HCI

Por predefinição, o perfil de implantação utiliza uma classe de armazenamento nomeada `default` e o tipo de serviço `LoadBalancer` .

Pode executar o seguinte comando para criar o controlador de dados utilizando a `default` classe de armazenamento e o tipo de serviço `LoadBalancer` .

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Depois de executar o comando, continue a [monitorizar o estado de criação](#monitoring-the-creation-status).


### <a name="create-on-azure-red-hat-openshift-aro"></a>Criar em Azure Red Hat OpenShift (ARO)

O Azure Red Hat OpenShift requer uma restrição de contexto de segurança.

#### <a name="apply-the-security-context"></a>Aplicar o contexto de segurança

Antes de criar o controlador de dados no Azure Red Hat OpenShift, terá de aplicar restrições específicas de contexto de segurança (SCC). Para o lançamento de pré-visualização, estes relaxam os constrangimentos de segurança. As futuras versões fornecerão SCC atualizado.

[!INCLUDE [apply-security-context-constraint](includes/apply-security-context-constraint.md)]

#### <a name="create-custom-deployment-profile"></a>Criar perfil de implementação personalizado

Utilize o perfil `azure-arc-azure-openshift` para Azure RedHat Open Shift.

```console
azdata arc dc config init --source azure-arc-azure-openshift --path ./custom
```

#### <a name="create-data-controller"></a>Criar controlador de dados

Pode executar o seguinte comando para criar o controlador de dados:

> [!NOTE]
> Use o mesmo espaço de nome aqui e nos `oc adm policy add-scc-to-user` comandos acima. O exemplo `arc` é.

```console
azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example
#azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Depois de executar o comando, continue a [monitorizar o estado de criação](#monitoring-the-creation-status).

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>Criar na Plataforma de Recipientes OpenShift de Chapéu Vermelho (OCP)

> [!NOTE]
> Se estiver a utilizar a Plataforma de Recipientes OpenShift do Chapéu Vermelho no Azure, é aconselhável utilizar a versão mais recente disponível.

Antes de criar o controlador de dados no Red Hat OCP, terá de aplicar restrições específicas de contexto de segurança. 

#### <a name="apply-the-security-context-constraint"></a>Aplicar a restrição de contexto de segurança

[!INCLUDE [apply-security-context-constraint](includes/apply-security-context-constraint.md)]

#### <a name="determine-storage-class"></a>Determinar a classe de armazenamento

Também terá de determinar qual a classe de armazenamento a utilizar executando o seguinte comando.

```console
kubectl get storageclass
```

#### <a name="create-custom-deployment-profile"></a>Criar perfil de implementação personalizado

Crie um novo ficheiro de perfil de implementação personalizado com base no `azure-arc-openshift` perfil de implementação executando o seguinte comando. Este comando cria um diretório `custom` no seu diretório de trabalho atual e um ficheiro de perfil de implantação personalizado `control.json` nesse diretório.

Utilize o perfil `azure-arc-openshift` para plataforma de recipientes openshift.

```console
azdata arc dc config init --source azure-arc-openshift --path ./custom
```

#### <a name="set-storage-class"></a>Definir classe de armazenamento 

Agora, desacorda a classe de armazenamento desejada substituindo `<storageclassname>` no comando abaixo o nome da classe de armazenamento que pretende utilizar que foi determinada executando o `kubectl get storageclass` comando acima.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

#### <a name="set-loadbalancer-optional"></a>Definir LoadBalancer (opcional)

Por predefinição, o `azure-arc-openshift` perfil de implementação utiliza `NodePort` como tipo de serviço. Se estiver a utilizar um cluster OpenShift que esteja integrado com um equilibrador de carga, pode alterar a configuração para utilizar o `LoadBalancer` tipo de serviço utilizando o seguinte comando:

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

#### <a name="verify-security-policies"></a>Verificar políticas de segurança

Ao utilizar o OpenShift, pode querer executar com as políticas de segurança predefinidos no OpenShift ou pretender bloquear o ambiente mais do que o normal. Pode optar opcionalmente por desativar algumas funcionalidades para minimizar as permissões necessárias no tempo de implementação e no tempo de execução, executando os seguintes comandos.

Este comando desativa as coleções métricas sobre cápsulas. Não poderá ver métricas sobre cápsulas nos tabliers grafana se desativar esta funcionalidade. A predefinição é verdadeiro.

```console
azdata arc dc config replace -p ./custom/control.json --json-values spec.security.allowPodMetricsCollection=false
```

Este comando desativa as coleções métricas sobre nós. Não poderá ver métricas sobre nós nos tabliers Grafana se desativar esta funcionalidade. A predefinição é verdadeiro.

```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowNodeMetricsCollection=false
```

Este comando desativa a capacidade de tirar lixeiras de memória para efeitos de resolução de problemas.
```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowDumps=false
```

#### <a name="create-data-controller"></a>Criar controlador de dados

Agora está pronto para criar o controlador de dados utilizando o seguinte comando.

> [!NOTE]
>   Use o mesmo espaço de nome aqui e nos `oc adm policy add-scc-to-user` comandos acima. O exemplo `arc` é.

> [!NOTE]
>   O `--path` parâmetro deve indicar que o _diretório_ que contém o control.jsem ficheiro não para o control.jsno próprio ficheiro.


```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Depois de executar o comando, continue a [monitorizar o estado de criação](#monitoring-the-creation-status).

### <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>Criar em fonte aberta, a montante Kubernetes (kubeadm)

Por predefinição, o perfil de implantação de Kubeadm utiliza uma classe de armazenamento chamada `local-storage` e tipo de serviço `NodePort` . Se isto for aceitável, pode saltar as instruções abaixo dessa definição da classe de armazenamento e do tipo de serviço pretendidos e executar imediatamente o `azdata arc dc create` comando abaixo.

Se pretender personalizar o seu perfil de implementação para especificar uma classe de armazenamento e/ou tipo de serviço específico, comece por criar um novo ficheiro de perfil de implementação personalizado baseado no perfil de implementação de Kubeadm executando o seguinte comando. Este comando criará um diretório `custom` no seu diretório de trabalho atual e um ficheiro de perfil de implantação personalizado `control.json` nesse diretório.

```console
azdata arc dc config init --source azure-arc-kubeadm --path ./custom
```

Pode procurar as aulas de armazenamento disponíveis executando o seguinte comando.

```console
kubectl get storageclass
```

Agora, desacorda a classe de armazenamento desejada substituindo `<storageclassname>` no comando abaixo o nome da classe de armazenamento que pretende utilizar que foi determinada executando o `kubectl get storageclass` comando acima.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

Por predefinição, o perfil de implantação de Kubeadm utiliza `NodePort` como tipo de serviço. Se estiver a utilizar um cluster Kubernetes que esteja integrado com um equilibrador de carga, pode alterar a configuração utilizando o seguinte comando.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

Agora está pronto para criar o controlador de dados utilizando o seguinte comando.

```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Depois de executar o comando, continue a [monitorizar o estado de criação](#monitoring-the-creation-status).

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>Criar no Serviço AWS Elastic Kubernetes (EKS)

Por predefinição, a classe de armazenamento EKS é `gp2` e o tipo de serviço é `LoadBalancer` .

Executar o seguinte comando para criar o controlador de dados utilizando o perfil de implementação EKS fornecido.

```console
azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Depois de executar o comando, continue a [monitorizar o estado de criação](#monitoring-the-creation-status).

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>Criar no Google Cloud Kubernetes Engine Service (GKE)

Por predefinição, a classe de armazenamento GKE é `standard` e o tipo de serviço é `LoadBalancer` .

Executar o seguinte comando para criar o controlador de dados utilizando o perfil de implementação GKE fornecido.

```console
azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Depois de executar o comando, continue a [monitorizar o estado de criação](#monitoring-the-creation-status).

## <a name="monitoring-the-creation-status"></a>Monitorização do estado de criação

A criação do controlador levará alguns minutos a ser concluída. Pode monitorizar o progresso noutra janela do terminal com os seguintes comandos:

> [!NOTE]
>  Os comandos de exemplo abaixo assumem que criou um controlador de dados e espaço de nome Kubernetes com o nome `arc` . Se usou um nome diferente de espaço de nome/controlador de dados, pode substituir `arc` pelo seu nome.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Também pode verificar o estado de criação de qualquer cápsula em particular executando um comando como abaixo. Isto é especialmente útil para resolver problemas.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Problemas de criação de resolução de problemas

Se encontrar algum problema com a criação, consulte o [guia de resolução de problemas.](troubleshoot-guide.md)