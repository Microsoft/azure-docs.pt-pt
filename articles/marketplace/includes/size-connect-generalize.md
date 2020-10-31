---
title: incluir ficheiro
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: ecbafe0d3f39b1bd6f7c494695ea17e067f0c79e
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129283"
---
## <a name="generalize-the-image"></a>Generalizar a imagem

Todas as imagens no Mercado Azure devem ser reutilizáveis de forma genérica. Para isso, o sistema operativo VHD deve ser generalizado, uma operação que remove todos os identificadores e controladores de software específicos de instância de um VM.

### <a name="for-windows"></a>Para Windows

Os discos WINDOWS OS são generalizados com a ferramenta [sysprep.](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) Se atualizar ou reconfigurar o SO, terá de voltar a executar sysprep.

> [!WARNING]
> Depois de executar o sysprep, desligue o VM até ser implantado porque as atualizações podem ser executadas automaticamente. Esta paralisação evitará que atualizações subsequentes esveçam alterações específicas do sistema operativo ou dos serviços instalados. Para obter mais informações sobre a execução do sysprep, consulte [passos para generalizar um VHD](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>Para Linux

O processo seguinte generaliza um Linux VM e reimplanta-o como um VM separado. Para mais detalhes, consulte [Como criar uma imagem de uma máquina virtual ou VHD](../../virtual-machines/linux/capture-image.md). Pode parar quando chegar à secção "Criar um VM a partir da imagem capturada".

1. Retire o agente Azure Linux.
    1. Ligue-se ao seu VM Linux utilizando um cliente SSH.
    2. Na janela SSH, insira este comando: `sudo waagent –deprovision+user` .
    3. Tipo Y para continuar (pode adicionar o parâmetro de força ao comando anterior para evitar o passo de confirmação).
    4. Após o comando completo, **introduza saída** para fechar o cliente SSH.
2. Pare a máquina virtual.
    1. No portal Azure, selecione o seu grupo de recursos (RG) e desatribua o VM.
    2. O seu VM está agora generalizado e pode criar um novo VM utilizando este disco VM.

### <a name="take-a-snapshot-of-the-vm-disk"></a>Tire uma foto do disco VM

1. Inicie sessão no [portal do Azure](https://ms.portal.azure.com/).
2. A partir da parte superior esquerda, **selecione Criar um recurso** e, em seguida, procurar e selecionar **Snapshot** .
3. Na lâmina Snapshot,  **selecione Criar** .
4. Insira um **nome** para a foto.
5. Selecione um grupo de recursos existente ou insira o nome para um novo.
6. Para **o disco Source** , selecione o disco gerido para o instantâneo.
7. Selecione o **tipo de Conta** a utilizar para armazenar o instantâneo. Utilize **o HDD standard** a menos que precise de ser armazenado num SSD de alto desempenho.
8. Selecione **Criar** .

#### <a name="extract-the-vhd"></a>Extrair o VHD

Use o seguinte script para exportar o instantâneo para um VHD na sua conta de armazenamento.

```JSON
#Provide the subscription Id where the snapshot is created
subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
resourceGroupName=myResourceGroupName

#Provide the snapshot name
snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1
sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. 
storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $ snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

#### <a name="script-explanation"></a>Explicação do script

Este script utiliza comandos seguintes para gerar o SAS URI para uma snapshot e copia o VHD subjacente a uma conta de armazenamento usando o SAS URI. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
| --- | --- |
| az disk grant-access | Gera o SAS só de leitura utilizado para copiar o ficheiro VHD subjacente para uma conta de armazenamento ou transferi-lo para o local
| az storage blob copy start | Copia uma bolha assíncronea de uma conta de armazenamento para outra. Utilize `az storage blob show` para verificar o estado da nova bolha. |
|