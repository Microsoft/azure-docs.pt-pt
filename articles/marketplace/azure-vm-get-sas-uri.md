---
title: Gere um SAS URI para uma imagem VM - Azure Marketplace
description: Gere uma assinatura de acesso partilhado (SAS) URI para um disco rígido virtual (VHD) no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 976d1dd4044649c9ef5307431c744ff3c2068bca
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103233570"
---
# <a name="how-to-generate-a-sas-uri-for-a-vm-image"></a>Como gerar um SAS URI para uma imagem VM

> [!NOTE]
> Não precisa de um SAS URI para publicar o seu VM. Pode simplesmente partilhar uma imagem no Parter Center. Consulte a [Criar uma máquina virtual utilizando uma base aprovada](https://docs.microsoft.com/azure/marketplace/azure-vm-create-using-approved-base) ou Criar uma máquina virtual utilizando as suas próprias instruções de [imagem.](https://docs.microsoft.com/azure/marketplace/azure-vm-create-using-own-image)

Gerar URIs SAS para os seus VHDs tem estes requisitos:

- Só apoiam VHDs não geridos.
- São necessárias apenas permissões de Lista e Leitura. Não forneça acesso a Escrever ou Eliminar.
- A duração do acesso (data de validade) deve ser mínima de três semanas a partir da criação do SAS URI.
- Para proteger contra alterações de tempo UTC, desajei a data de início para um dia antes da data atual. Por exemplo, se a data atual for 16 de junho de 2020, selecione 6/15/2020.

## <a name="extract-vhd-from-a-vm"></a>Extrato vhd de um VM

> [!NOTE]
> Pode saltar este passo se já tiver um vhd carregado numa Conta de Armazenamento.

Para extrair o vhd do seu VM, precisa tirar uma foto do seu disco VM e extrair vhd da foto.

Comece por tirar uma foto do disco VM:

1. Inicie sessão no Portal do Azure.
2. A partir da parte superior esquerda, selecione Criar um recurso e, em seguida, procurar e selecionar Snapshot.
3. Na lâmina Snapshot, selecione Criar.
4. Insira um nome para a foto.
5. Selecione um grupo de recursos existente ou insira o nome para um novo.
6. Para o disco 'Fonte', selecione o disco gerido para o instantâneo.
7. Selecione o tipo de Conta a utilizar para armazenar o instantâneo. Utilize o HDD standard a menos que precise de ser armazenado num SSD de alto desempenho.
8. Selecione Criar.

### <a name="extract-the-vhd"></a>Extrair o VHD

Use o seguinte script para exportar o instantâneo para um VHD na sua conta de armazenamento.

```azurecli
#Provide the subscription Id where the snapshot is created
$subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
$resourceGroupName=myResourceGroupName

#Provide the snapshot name
$snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1
$sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. 
$storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
$storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
$storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
$destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

### <a name="script-explanation"></a>Explicação do script
Este script utiliza comandos seguintes para gerar o SAS URI para uma snapshot e copia o VHD subjacente a uma conta de armazenamento usando o SAS URI. Cada comando na tabela liga à documentação específica do comando.


|Comando  |Notas  |
|---------|---------|
| az disk grant-access    |     Gera o SAS só de leitura utilizado para copiar o ficheiro VHD subjacente para uma conta de armazenamento ou transferi-lo para o local    |
|  az storage blob copy start   |    Copia uma bolha assíncronea de uma conta de armazenamento para outra. Utilize o show de bolhas de armazenamento az para verificar o estado da nova bolha.     |
|

## <a name="generate-the-sas-address"></a>Gerar o endereço SAS

Existem duas ferramentas comuns usadas para criar um endereço SAS (URL):

1. **Azure Storage Explorer** – Disponível no portal Azure.
2. **Azure CLI** – Recomendado para sistemas operativos não Windows e ambientes de integração automatizado ou contínuo.

### <a name="using-tool-1-azure-storage-explorer"></a>Ferramenta 1: Explorador de armazenamento Azure

1. Aceda à sua **Conta de Armazenamento.**
1. **Explorador de armazenamento aberto.**

    :::image type="content" source="media/create-vm/storge-account-explorer.png" alt-text="Janela da conta de armazenamento.":::

3. No **Recipiente,** clique com o botão direito no ficheiro VHD e selecione **Obter Assinatura de Acesso à Partilha**.
4. Na caixa de diálogo **assinatura de acesso partilhado,** complete os seguintes campos:

    1. Hora de início – Data de início da permissão para acesso vHD. Forneça uma data que seja um dia antes da data atual.
    2. Prazo de validade – Data de validade da autorização para acesso vHD. Forneça uma data pelo menos três semanas para além da data atual.
    3. Permissões – Selecione as permissões de Leitura e Lista.
    4. Nível de recipiente – Verifique a caixa de verificação URI de assinatura de assinatura de acesso partilhado ao nível do recipiente Gerar.

    ![Caixa de diálogo de assinatura de acesso partilhado.](media/vm/create-sas-uri-storage-explorer.png)

5. Para criar o SAS URI associado para este VHD, selecione **Create**.
6. Copie o URI e guarde-o num ficheiro de texto num local seguro. Este SAS URI gerado destina-se ao acesso ao nível do contentor. Para torná-lo específico, edite o ficheiro de texto para adicionar o nome VHD.
7. Insira o seu nome VHD após a corda vhds no SAS URI (inclua um corte para a frente). O SAS URI final deve ser assim:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

8. Repita estes passos para cada VHD nos planos que publicará.

### <a name="using-tool-2-azure-cli"></a>Ferramenta 2: Azure CLI

1. Descarregue e instale [o Microsoft Azure CL](/cli/azure/install-azure-cli)I. As versões estão disponíveis para Windows, macOS e vários distros do Linux.
2. Crie um ficheiro PowerShell (extensão de ficheiro.ps1), copie no seguinte código e guarde-o localmente.

    ```azurecli-interactive
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net’ --name <container-name> --permissions rl --start ‘<start-date>’ --expiry ‘<expiry-date>’
    ```

3. Edite o ficheiro para utilizar os seguintes valores de parâmetro. Fornecer datas no formato de datatác, como 2020-04-01T00:00:00Z.

    - nome da conta - O nome da sua conta de armazenamento Azure.
    - chave de conta - A sua chave de conta de armazenamento Azure.
    - data de início - Data de início da permissão para acesso vHD. Forneça uma data um dia antes da data atual.
    - data de validade – Data de validade da autorização para o acesso vHD. Forneça uma data pelo menos três semanas após a data atual.

    Aqui está um exemplo de valores parâmetros adequados (no momento desta escrita):

    ```azurecli-interactive
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net’ --name <container-name> -- permissions rl --start ‘2020-04-01T00:00:00Z’ --expiry ‘2021-04-01T00:00:00Z’
    ```

1. Guarde as alterações.
2. Utilizando um dos seguintes métodos, execute este script com privilégios administrativos para criar uma cadeia de conexão SAS para acesso ao nível do contentor:

    - Executar o guião a partir da consola. No Windows, clique com o botão direito no script e selecione **Executar como administrador**.
    - Execute o script a partir de um editor de scripts PowerShell, como [o Windows PowerShell ISE](/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Este ecrã mostra a criação de uma cadeia de conexão SAS dentro deste editor:

    [![criação de uma cadeia de conexão SAS dentro do editor PowerShell](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. Copie a cadeia de ligação SAS e guarde-a num ficheiro de texto num local seguro. Edite este string para adicionar as informações de localização VHD para criar o SAS URI final.
7. No portal Azure, vá ao armazenamento de bolhas que inclui o VHD associado ao novo URI.
8. Copiar o URL do ponto final do serviço blob:

    ![Cópia do URL do ponto final do serviço blob.](media/vm/create-sas-uri-blob-endpoint.png)

9. Editar o ficheiro de texto com a cadeia de ligação SAS a partir do passo 6. Crie o SAS URI completo utilizando este formato:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>Verifique o SAS URI

Consulte o SAS URI antes de publicá-lo no Partner Center para evitar quaisquer problemas relacionados com a submissão pós-envio do pedido da SAS URI. Este processo é opcional, mas recomendado.

- O URI inclui o seu nome de ficheiro de imagem VHD, incluindo a extensão do nome de ficheiro `.vhd` .
- `Sp=rl` aparece perto do meio do seu URI. Esta sequência mostra que o acesso à Leitura e à Lista é especificado.
- Quando `sr=c` aparece, isto significa que o acesso ao nível do contentor é especificado.
- Copie e cole o URI num browser para testar o blob (pode cancelar a operação antes que o download esteja concluído).

## <a name="next-steps"></a>Passos seguintes

- Se tiver problemas, consulte [as mensagens de falha da VM SAS](azure-vm-sas-failure-messages.md).
- [Inscreva-se no Centro de Parceiros](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)
- [Crie uma oferta de máquina virtual no Azure Marketplace](azure-vm-create.md)
