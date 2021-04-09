---
title: Criptografe as reclamações de volume persistentes com uma chave gerida pelo cliente (CMK) no Azure Red Hat OpenShift (ARO)
description: Traga a sua própria chave (BYOK) / Chave gerida pelo cliente (CMK) para implementar instruções para Azure Red Hat OpenShift
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: encriptação, byok, aro, cmk, openshift, chapéu vermelho
ms.openlocfilehash: cf028456cc8971678373d36214885c3f79df8e82
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047070"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Criptografe as reclamações de volume persistentes com uma chave gerida pelo cliente (CMK) no Azure Red Hat OpenShift (ARO) (pré-visualização)

O Azure Storage utiliza encriptação do lado do servidor (SSE) para [encriptar](../storage/common/storage-service-encryption.md) automaticamente os seus dados quando estes são persistidos na nuvem. Por predefinição, os dados são encriptados com as teclas geridas pela plataforma da Microsoft. Para controlo adicional sobre as chaves de encriptação, pode fornecer as suas próprias chaves geridas pelo cliente para encriptar dados nos seus clusters Azure Red Hat OpenShift.

> [!NOTE]
> Nesta fase, o suporte existe apenas para encriptar volumes persistentes da ARO com chaves geridas pelo cliente. Esta funcionalidade não se encontra disponível para discos do sistema operativo master ou de nó de trabalhador.

> [!IMPORTANT]
> As funcionalidades de pré-visualização ARO estão disponíveis numa base de autosserviço, opt-in. As pré-visualizações são fornecidas "conforme" e "conforme disponível", e estão excluídas dos contratos de nível de serviço e garantia limitada. As pré-visualizações da ARO são parcialmente cobertas pelo apoio ao cliente numa base de melhor esforço. Como tal, estas características não se destinam ao uso da produção.

## <a name="before-you-begin"></a>Antes de começar
Este artigo assume que:

* Tem um cluster ARO pré-existente na versão OpenShift 4.4 (ou maior).

* Tem a ferramenta de linha de comando **oc** OpenShift, base64 (parte dos coreutils) e o **Azure** CLI instalados.

* Você está iniciado no seu cluster ARO usando **oc** como um utilizador global de cluster-administração (kubeadmin).

* Você está iniciando sessão no Azure CLI usando **az** com uma conta autorizada a conceder acesso "Contribuinte" na mesma subscrição que o cluster ARO.

## <a name="limitations"></a>Limitações

* A disponibilidade para encriptação de chaves gerida pelo cliente é específica da região. Para ver o estado de uma região específica de Azure, consulte as [regiões de Azure.][supported-regions]
* Se desejar utilizar Discos Ultra, tem primeiro de os ativar na sua subscrição antes de começar.

## <a name="declare-cluster--encryption-variables"></a>Declare variáveis de encriptação & cluster
Deve configurar as variáveis abaixo para o que for apropriado para o cluster ARO no qual deseja ativar as chaves de encriptação geridas pelo cliente:
```
aroCluster="mycluster"             # The name of the ARO cluster that you wish to enable CMK on. This may be obtained from **az aro list -o table**
buildRG="mycluster-rg"             # The name of the resource group used when you initially built the ARO cluster. This may be obtained from **az aro list -o table**
desName="aro-des"                  # Your Azure Disk Encryption Set name. This must be unique in your subscription.
vaultName="aro-keyvault-1"         # Your Azure Key Vault name. This must be unique in your subscription.
vaultKeyName="myCustomAROKey"      # The name of the key to be used within your Azure Key Vault. This is the name of the key, not the actual value of the key that you will rotate.
```

## <a name="obtain-your-subscription-id"></a>Obtenha o seu ID de subscrição
O seu ID de subscrição Azure é utilizado várias vezes na configuração de CMK. Obtê-lo e armazená-lo como variável:
```azurecli-interactive
# Obtain your Azure Subscription ID and store it in a variable
subId="$(az account list -o tsv | grep True | awk '{print $3}')"
```

## <a name="create-an-azure-key-vault-instance"></a>Criar uma instância Azure Key Vault
Uma instância do Cofre da Chave Azure deve ser usada para guardar as suas chaves. Crie um novo Cofre-chave com proteção de purga ativada. Em seguida, crie uma nova chave dentro do cofre para armazenar a sua própria chave personalizada:

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table

# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Criar um conjunto de encriptação de disco Azure

O Conjunto de Encriptação do Disco Azure é utilizado como ponto de referência para discos em ARO. Está ligado ao Cofre de Chaves Azure que criamos no passo anterior e irá retirar as chaves geridas pelo cliente a partir desse local.

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $buildRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>Conceder ao conjunto de encriptação do disco acesso ao cofre de chaves
Utilize o conjunto de encriptação do disco que criamos nos passos anteriores e conceda ao conjunto de encriptação do disco acesso ao Cofre da Chave Azure:

```azurecli-interactive
# First, find the disk encryption set's Azure Application ID value.
desIdentity="$(az disk-encryption-set show -n $desName -g $buildRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $buildRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>Obter outros IDs necessários para atribuições de funções
Precisamos permitir que o cluster ARO utilize o conjunto de encriptação do disco para encriptar as persistentes alegações de volume (PVCs) no cluster ARO. Para isso, criaremos uma nova Identidade de Serviço Gerido (MSI). Também definiremos outras permissões para o MSI ARO e para o Conjunto de Encriptação de Discos.

```azurecli-interactive
# First, get the Azure Application ID of the service principal used in the ARO cluster.
aroSPAppId="$(oc get secret azure-credentials -n kube-system -o jsonpath='{.data.azure_client_id}' | base64 --decode)"

# Next, get the object ID of the service principal used in the ARO cluster.
aroSPObjId="$(az ad sp show --id $aroSPAppId -o tsv --query [objectId])"

# Set the name of the ARO Managed Service Identity. 
msiName="$aroCluster-msi"

# Create the Managed Service Identity (MSI) required for disk encryption.
az identity create -g $buildRG -n $msiName -o jsonc

# Get the ARO Managed Service Identity Azure Application ID.
aroMSIAppId="$(az identity show -n $msiName -g $buildRG -o tsv --query [clientId])"

# Get the resource ID for the disk encryption set and the Key Vault resource group.
buildRGResourceId="$(az group show -n $buildRG -o tsv --query [id])"
```

### <a name="implement-other-role-assignments-required-for-cmk-encryption"></a>Implementar outras atribuições de funções necessárias para encriptação CMK
Aplicar as atribuições de função necessárias utilizando as variáveis obtidas no passo anterior:

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $buildRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $buildRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks"></a>Criar uma classe de armazenamento k8s para discos Premium & Ultra encriptados
Gerar aulas de armazenamento para serem utilizadas para cmk para discos de Premium_LRS e UltraSSD_LRS:

```azurecli-interactive
# Premium Disks
cat > managed-premium-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Premium_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF

# Ultra Disks
cat > managed-ultra-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-ultra-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: UltraSSD_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
```

Em seguida, execute esta implementação no seu cluster ARO para aplicar a configuração da classe de armazenamento:

```azurecli-interactive
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-cmk.yaml
oc apply -f managed-ultra-encrypted-cmk.yaml
```

## <a name="test-encryption-with-customer-managed-keys-optional"></a>Teste encriptação com chaves geridas pelo cliente (opcional)
Para verificar se o seu cluster está a usar uma chave gerida pelo cliente para encriptação de PVC, criaremos uma reivindicação de volume persistente usando a nova classe de armazenamento. O corte de código abaixo cria uma cápsula e monta uma reivindicação de volume persistente usando discos Premium.
```
# Create a pod which uses a persistent volume claim referencing the new storage class
cat > test-pvc.yaml<< EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypod-with-cmk-encryption-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-encrypted-cmk
  resources:
    requests:
      storage: 1Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: mypod-with-cmk-encryption
spec:
  containers:
  - name: mypod-with-cmk-encryption
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: mypod-with-cmk-encryption-pvc
EOF
```
### <a name="apply-the-test-pod-configuration-file-optional"></a>Aplicar o ficheiro de configuração do casulo de teste (opcional)
Execute os comandos abaixo para aplicar a configuração do Pod de teste e devolva o UID da nova reivindicação de volume persistente. O UID será utilizado para verificar se o disco está encriptado utilizando CMK.
```azurecli-interactive
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o jsonpath='{.items[0].metadata.uid}')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o jsonpath='{.spec.azureDisk.diskName}')"
```
> [!NOTE]
> Por vezes, pode haver um ligeiro atraso na aplicação de atribuições de funções no Diretório Azure Ative. Dependendo da velocidade que estas instruções são executadas, o comando para "Determinar o nome completo do disco Azure" pode não ter sucesso. Se isto ocorrer, reveja a saída do **oc descrever pvc mypod-with-cmk-encryption-pvc** para garantir que o disco foi a provisionado com sucesso. Se a propagação da atribuição de funções não tiver sido concluída, poderá ter de *eliminar* e *re-aplicar* o Pod & PVC YAML.

### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey-optional"></a>Verifique se o disco de PVC está configurado com "EncryptionAtRestWithCustomerKey" (Opcional)
O Pod deve criar uma alegação de volume persistente que refira a classe de armazenamento CMK. Executando o seguinte comando validará que o PVC foi implantado como esperado:
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $buildRG -o json --query [encryption]
```

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ../aks/operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions