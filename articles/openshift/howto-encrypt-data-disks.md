---
title: Criptografe as reclamações de volume persistentes com uma chave gerida pelo cliente (CMK) no Azure Red Hat OpenShift (ARO)
description: Traga a sua própria chave (BYOK) / Chave gerida pelo cliente (CMK) para implementar instruções para Azure Red Hat OpenShift
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: encriptação, byok, aro, openshift, chapéu vermelho
ms.openlocfilehash: 09e1f92a967c7b77d3bb8e27769f4cafd4fd4f53
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055008"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Criptografe as reclamações de volume persistentes com uma chave gerida pelo cliente (CMK) no Azure Red Hat OpenShift (ARO) (pré-visualização)

O Azure Storage encripta todos os dados numa conta de armazenamento em repouso. Por predefinição, os dados são encriptados com teclas geridas pela plataforma da Microsoft que incluem os discos de OS e de dados. Para obter mais controlo sobre as chaves de encriptação, pode fornecer chaves geridas pelo cliente para encriptar dados nos seus clusters Azure Red Hat OpenShift.

> [!NOTE]
> Nesta fase, o suporte existe apenas para encriptar volumes persistentes da ARO com chaves geridas pelo cliente. Esta funcionalidade não se encontra disponível para discos do sistema operativo.

> [!IMPORTANT]
> As funcionalidades de pré-visualização ARO estão disponíveis numa base de autosserviço, opt-in. As pré-visualizações são fornecidas "conforme" e "conforme disponível", e estão excluídas dos contratos de nível de serviço e garantia limitada. As pré-visualizações da ARO são parcialmente cobertas pelo apoio ao cliente numa base de melhor esforço. Como tal, estas características não se destinam ao uso da produção.

## <a name="before-you-begin"></a>Antes de começar
Este artigo assume que:

* Tem um cluster ARO pré-existente na versão OpenShift 4.4 (ou maior).

* Tem a ferramenta de linha de comando 'oc' OpenShift, base64 (parte dos utensílios de núcleo) e o 'az' Azure CLI instalados.

* Você está conectado ao seu cluster ARO usando *oc* como um utilizador global de cluster-administração (kubeadmin).

* Você está iniciando sessão no Azure CLI usando *az* com uma conta autorizada a conceder acesso "Contribuinte" na mesma subscrição que o cluster ARO.

## <a name="limitations"></a>Limitações

* A disponibilidade para encriptação de chaves gerida pelo cliente é específica da região. Para ver o estado de uma região específica de Azure, consulte as [regiões de Azure.][supported-regions]
* Se estiver a utilizar Discos Ultra, ative discos ultra na sua subscrição antes de começar.

## <a name="create-an-azure-key-vault-instance"></a>Criar uma instância Azure Key Vault
Uma instância do Cofre da Chave Azure deve ser usada para guardar as suas chaves. Crie um novo Cofre de Chaves com proteção de purga e eliminação suave ativado. Em seguida, crie uma nova chave dentro do cofre para armazenar a sua própria chave personalizada:

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table
# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Criar um conjunto de encriptação de disco Azure

O conjunto de encriptação do disco Azure é usado como ponto de referência para discos em ARO. Está ligado ao Cofre de Chaves Azure que criámos no passo anterior e vai retirar as chaves geridas pelo cliente a partir daquele local.

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $myRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>Conceder ao conjunto de encriptação do disco acesso ao Key Vault
Utilize o conjunto de encriptação do disco que criamos nos passos anteriores e conceda ao conjunto de encriptação do disco acesso ao Cofre da Chave Azure:

```azurecli-interactive
# First, find the disk encryption set's AppId value.
desIdentity="$(az disk-encryption-set show -n $desName -g $myRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $myRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the disk encryption set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>Obter outros IDs necessários para atribuições de funções
Precisamos permitir que o cluster ARO utilize o conjunto de encriptação do disco para encriptar as persistentes alegações de volume (PVCs) no cluster ARO. Para isso, criaremos uma nova Identidade de Serviço Gerido (MSI). Também definiremos outras permissões para o MSI ARO e para o conjunto de encriptação do disco.
```
# First, get the application ID of the service principal used in the ARO cluster.
aroSPAppId="$(oc get secret azure-credentials -n kube-system -o jsonpath='{.data.azure_client_id}' | base64 --decode)"

# Next, get the object ID of the service principal used in the ARO cluster.
aroSPObjId="$(az ad sp show --id $aroSPAppId -o tsv --query [objectId])"

# Set the name of the ARO Managed Service Identity. 
msiName="$aroCluster-msi"

# Create the Managed Service Identity (MSI) required for disk encryption.
az identity create -g $myRG -n $msiName -o jsonc

# Get the ARO Managed Service Identity application ID.
aroMSIAppId="$(az identity show -n $msiName -g $myRG -o tsv --query [clientId])"

# Get the resource ID for the disk encryption set and the Key Vault resource group.
myRGResourceId="$(az group show -n $myRG -o tsv --query [id])"
```

### <a name="implement-other-role-assignments-required-for-byokcmk-encryption"></a>Implementar outras atribuições de funções necessárias para encriptação BYOK/CMK
Aplicar as atribuições de função necessárias utilizando as variáveis obtidas no passo anterior:

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $myRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $myRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks-optional"></a>Criar uma classe de armazenamento k8s para discos Premium & Ultra encriptados (opcional)
Gere aulas de armazenamento para os discos BYOK/CMK para Premium_LRS e UltraSSD_LRS:
```
# Premium Disks
cat > managed-premium-encrypted-byok.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-encrypted-byok
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Premium_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/subId/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/desName"
  resourceGroup: myRG
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF

# Ultra Disks
cat > managed-ultra-encrypted-byok.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-ultra-encrypted-byok
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: UltraSSD_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/subId/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/desName"
  resourceGroup: myRG
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
```
### <a name="set-up-your-storage-class-configuration"></a>Configurar a sua configuração de classe de armazenamento
Substitua as variáveis que são únicas do seu cluster ARO nos dois ficheiros de configuração da classe de armazenamento:
```
# Insert your current active subscription ID into the configuration
sed -i "s/subId/$subId/g" managed-premium-encrypted-byok.yaml
sed -i "s/subId/$subId/g" managed-ultra-encrypted-byok.yaml

# Replace the name of the Resource Group which contains the disk encryption set and Key Vault
sed -i "s/myRG/$myRG/g" managed-premium-encrypted-byok.yaml
sed -i "s/myRG/$myRG/g" managed-ultra-encrypted-byok.yaml

# Replace the name of the disk encryption set
sed -i "s/desName/$desName/g" managed-premium-encrypted-byok.yaml
sed -i "s/desName/$desName/g" managed-ultra-encrypted-byok.yaml
```
Em seguida, execute esta implementação no seu cluster ARO para aplicar a configuração da classe de armazenamento:
```
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-byok.yaml
oc apply -f managed-ultra-encrypted-byok.yaml
```
## <a name="test-encryption-with-customer-managed-keys"></a>Teste encriptação com chaves geridas pelo cliente
Para verificar se o seu cluster está a usar uma chave gerida pelo cliente para encriptação de PVC, criaremos uma reivindicação de volume persistente usando a classe de armazenamento apropriada. O corte de código abaixo cria uma cápsula e monta uma reivindicação de volume persistente usando discos standard
```
# Create a pod which uses a persistent volume claim referencing the new storage class
cat > test-pvc.yaml<< EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypod-with-byok-encryption-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-encrypted-byok
  resources:
    requests:
      storage: 1Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: mypod-with-byok-encryption
spec:
  containers:
  - name: mypod-with-byok-encryption
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
        claimName: mypod-with-byok-encryption-pvc
EOF
```
### <a name="apply-the-test-pod-configuration-file"></a>Aplicar o ficheiro de configuração do pod de teste
Execute os comandos abaixo para aplicar a configuração do Pod de teste e devolva o UID da nova reivindicação de volume persistente. O UID será utilizado para verificar se o disco está encriptado utilizando BYOK/CMK.
```
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o json | jq -r '.items[0].metadata.uid')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o json |jq -r '.spec.azureDisk.diskName')"
```
### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey"></a>Verifique se o disco de PVC está configurado com "EncryptionAtRestWithCustomerKey" 
O Pod deve criar uma alegação de volume persistente que refira a classe de armazenamento BYOK/CMK. Executando o seguinte comando validará que o PVC foi implantado como esperado:
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $myRG -o json --query [encryption]
```

## <a name="next-steps"></a>Passos seguintes

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions

