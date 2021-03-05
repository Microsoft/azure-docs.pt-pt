---
title: Criar um StorageClass dos Ficheiros do Azure no Azure Red Hat OpenShift 4
description: Saiba como criar uma Azure Files StorageClass no Azure Red Hat OpenShift
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 10/16/2020
author: grantomation
ms.author: b-grodel
keywords: aro, openshift, az aro, chapéu vermelho, cli, arquivo azul
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 039aa3cce6615e71960db810ae383d22d7bcd909
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212982"
---
# <a name="create-an-azure-files-storageclass-on-azure-red-hat-openshift-4"></a>Criar um StorageClass dos Ficheiros do Azure no Azure Red Hat OpenShift 4

Neste artigo, você vai criar uma StorageClass para Azure Red Hat OpenShift 4 que provisões dinamicamente ReadWriteMany (RWX) usando Ficheiros Azure. Irá aprender a:

> [!div class="checklist"]
> * Configurar os pré-requisitos e instalar as ferramentas necessárias
> * Crie um Azure Red Hat OpenShift 4 StorageClass com o provisioner de ficheiros Azure

Se optar por instalar e utilizar o CLI localmente, este tutorial requer que esteja a executar a versão Azure CLI 2.6.0 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

Implemente um cluster Azure Red Hat OpenShift 4 na sua subscrição, consulte [Criar um cluster Azure Red Hat OpenShift 4](tutorial-create-cluster.md)


### <a name="set-up-azure-storage-account"></a>Configurar a conta de armazenamento Azure

Este passo criará um grupo de recursos fora do grupo de recursos do cluster Azure Red Hat OpenShift (ARO). Este grupo de recursos conterá as ações Azure Files criadas pelo provisionador dinâmico da Azure Red Hat OpenShift.

```bash
AZURE_FILES_RESOURCE_GROUP=aro_azure_files
LOCATION=eastus

az group create -l $LOCATION -n $AZURE_FILES_RESOURCE_GROUP

AZURE_STORAGE_ACCOUNT_NAME=aroazurefilessa

az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_NAME \
    --resource-group $AZURE_FILES_RESOURCE_GROUP \
    --kind StorageV2 \
    --sku Standard_LRS
```

## <a name="set-permissions"></a>Definir permissões
### <a name="set-resource-group-permissions"></a>Definir permissões de grupo de recursos

O principal do serviço ARO requer a permissão de 'listKeys' no novo grupo de recursos de conta de armazenamento Azure. Atribua o papel de "Contribuinte" para o conseguir.

```bash
ARO_RESOURCE_GROUP=aro-rg
CLUSTER=cluster
ARO_SERVICE_PRINCIPAL_ID=$(az aro show -g $ARO_RESOURCE_GROUP -n $CLUSTER --query servicePrincipalProfile.clientId -o tsv)

az role assignment create --role Contributor --assignee $ARO_SERVICE_PRINCIPAL_ID -g $AZURE_FILES_RESOURCE_GROUP
```

### <a name="set-aro-cluster-permissions"></a>Definir permissões de cluster ARO

A conta de serviço de aglutinante de volume persistente OpenShift requer a capacidade de ler segredos. Crie e atribua um papel de cluster OpenShift para o conseguir.
```bash
ARO_API_SERVER=$(az aro list --query "[?contains(name,'$CLUSTER')].[apiserverProfile.url]" -o tsv)

oc login -u kubeadmin -p $(az aro list-credentials -g $ARO_RESOURCE_GROUP -n $CLUSTER --query=kubeadminPassword -o tsv) $APISERVER

oc create clusterrole azure-secret-reader \
    --verb=create,get \
    --resource=secrets

oc adm policy add-cluster-role-to-user azure-secret-reader system:serviceaccount:kube-system:persistent-volume-binder
```

## <a name="create-storageclass-with-azure-files-provisioner"></a>Criar StorageClass com provisioner de ficheiros Azure

Este passo criará uma Classe de Armazenamento com um provisionador de ficheiros Azure. Dentro do manifesto StorageClass, os detalhes da conta de armazenamento são necessários para que o cluster ARO saiba olhar para uma conta de armazenamento fora do grupo de recursos atual.

Durante o fornecimento de armazenamento, um segredo nomeado pelo nome secreto é criado para as credenciais de montagem. Num contexto multi-arrendamento, é fortemente recomendado definir o valor para o SecretNamespace explicitamente, caso contrário as credenciais de conta de armazenamento podem ser lidas por outros utilizadores.

```bash
cat << EOF >> azure-storageclass-azure-file.yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azure-file
provisioner: kubernetes.io/azure-file
parameters:
  location: $LOCATION
  secretNamespace: kube-system
  skuName: Standard_LRS
  storageAccount: $AZURE_STORAGE_ACCOUNT_NAME
  resourceGroup: $AZURE_FILES_RESOURCE_GROUP
reclaimPolicy: Delete
volumeBindingMode: Immediate
EOF

oc create -f azure-storageclass-azure-file.yaml
```

## <a name="change-the-default-storageclass-optional"></a>Alterar a Classe de Armazenamento predefinido (opcional)

O StorageClass predefinido na ARO é chamado de "premium gerido" e utiliza o provisionador de disco azul. Altere isto emitindo comandos de patch contra os manifestos StorageClass.

```bash
oc patch storageclass managed-premium -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'

oc patch storageclass azure-file -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

## <a name="verify-azure-file-storageclass-optional"></a>Verifique a Azure File StorageClass (opcional)

Crie uma nova aplicação e atribua-lhe armazenamento.

```bash
oc new-project azfiletest
oc new-app -template httpd-example

#Wait for the pod to become Ready
curl $(oc get route httpd-example -n azfiletest -o jsonpath={.spec.host})

oc set volume dc/httpd-example --add --name=v1 -t pvc --claim-size=1G -m /data

#Wait for the new deployment to rollout
export POD=$(oc get pods --field-selector=status.phase==Running -o jsonpath={.items[].metadata.name})
oc exec $POD -- bash -c "mkdir ./data"
oc exec $POD -- bash -c "echo 'azure file storage' >> /data/test.txt"

oc exec $POD -- bash -c "cat /data/test.txt"
azure file storage
```
O ficheiro test.txt também será visível através do Explorador de Armazenamento no portal Azure.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um armazenamento dinâmico e persistente utilizando os Ficheiros Microsoft Azure e o Azure Red Hat OpenShift 4. Aprendeu a:

> [!div class="checklist"]
> * Criar uma Conta de Armazenamento
> * Configure uma Classe de Armazenamento num cluster Azure Red Hat OpenShift 4 utilizando o provisioner Azure Files

Avance para o próximo artigo para saber sobre os recursos suportados Azure Red Hat OpenShift 4.

* [Política de suporte Azure Red Hat OpenShift](support-policies-v4.md)
