---
title: Armazenamento mount Azure Blob utilizando o protocolo NFS 3.0 (pré-visualização) | Microsoft Docs
description: Saiba como montar um recipiente no armazenamento Blob a partir de uma Máquina Virtual Azure (VM) ou de um cliente que funciona no local utilizando o protocolo NFS 3.0.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 2a37d206955e3372b9ecf97be8d27142bd417192
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490459"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol-preview"></a>Armazenamento do Monte Blob utilizando o protocolo Sistema de Ficheiros de Rede (NFS) 3.0 (pré-visualização)

Pode montar um recipiente no armazenamento Blob a partir de uma Máquina Virtual (VM) baseada em Linux ou um sistema Linux que funciona no local utilizando o protocolo NFS 3.0. Este artigo fornece orientação passo a passo. Para saber mais sobre o suporte ao protocolo NFS 3.0 no armazenamento blob, consulte [o suporte ao sistema de ficheiros de rede (NFS) 3.0 no armazenamento do Blob Azure Blob (pré-visualização)](network-file-system-protocol-support.md).

## <a name="step-1-register-the-nfs-30-protocol-feature-with-your-subscription"></a>Passo 1: Registe a função de protocolo NFS 3.0 com a sua assinatura

1. Abra uma janela de comando PowerShell. 

2. Inicie sessão na sua subscrição do Azure com o comando `Connect-AzAccount` e siga as instruções no ecrã.

   ```powershell
   Connect-AzAccount
   ```

3. Se a sua identidade estiver associada a mais de uma subscrição, então desa estale a sua subscrição ativa.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```
   
   Substitua o `<subscription-id>` valor do espaço reservado pelo ID da sua subscrição.

4. Registe a `AllowNFSV3` função utilizando o seguinte comando.

   ```powershell
   Register-AzProviderFeature -FeatureName AllowNFSV3 -ProviderNamespace Microsoft.Storage 
   ```

5. Registe o fornecedor de recursos utilizando o seguinte comando.
    
   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage   
   ```

## <a name="step-2-verify-that-the-feature-is-registered"></a>Passo 2: Verifique se a funcionalidade está registada 

A aprovação do registo pode demorar até uma hora. Para verificar se o registo está completo, utilize os seguintes comandos.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNFSV3
```

## <a name="step-3-create-an-azure-virtual-network-vnet"></a>Passo 3: Criar uma rede virtual Azure (VNet)

A sua conta de armazenamento deve estar contida num VNet. Um VNet permite que os clientes se conectem de forma segura à sua conta de armazenamento. Para saber mais sobre o VNet e como criar um, consulte a [documentação da Rede Virtual.](../../virtual-network/index.yml)

> [!NOTE]
> Os clientes do mesmo VNet podem montar contentores na sua conta. Também pode montar um contentor a partir de um cliente que funciona numa rede no local, mas terá primeiro de ligar a sua rede no local ao seu VNet. Consulte [as ligações de rede suportadas](network-file-system-protocol-support.md#supported-network-connections).

## <a name="step-4-configure-network-security"></a>Passo 4:Configure a segurança da rede

A única forma de proteger os dados na sua conta é utilizando um VNet e outras definições de segurança de rede. Qualquer outra ferramenta utilizada para proteger dados, incluindo a autorização da chave de conta, a segurança do Azure Ative Directory (AD) e as listas de controlo de acesso (ACLs) ainda não são suportadas em contas que tenham o suporte do protocolo NFS 3.0 ativado nos mesmos. 

Para proteger os dados na sua conta, consulte estas recomendações: [Recomendações de segurança da rede para armazenamento blob](security-recommendations.md#networking).

## <a name="step-5-create-and-configure-a-storage-account"></a>Passo 5: Criar e configurar uma conta de armazenamento

Para montar um recipiente utilizando o NFS 3.0, tem de criar uma conta de armazenamento **depois de** registar a funcionalidade com a sua subscrição. Não é possível ativar as contas que existiam antes de registar a funcionalidade. 

No lançamento de pré-visualização desta funcionalidade, o protocolo NFS 3.0 é suportado nas contas [BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md) e [V2 de uso geral.](../common/storage-account-overview.md#general-purpose-v2-accounts)

Ao configurar a conta, escolha estes valores:

|Definição | Desempenho premium | Desempenho padrão  
|----|---|---|
|Localização|Todas as regiões disponíveis |Uma das seguintes regiões: Austrália Leste, Coreia Central, Leste dos EUA e Centro Sul dos EUA   
|Desempenho|Premium| Standard
|Tipo de conta|BlockBlobStorage| V2 de uso geral
|Replicação|Armazenamento localmente redundante (LRS)| Armazenamento localmente redundante (LRS)
|Método de conectividade|Ponto final público (redes selecionadas) ou ponto final privado |Ponto final público (redes selecionadas) ou ponto final privado
|Transferência segura necessária|Desativado|Desativado
|Espaço de nomes hierárquico|Ativado|Ativado
|NFS V3|Ativado |Ativado 

Pode aceitar os valores predefinidos para todas as outras definições. 

## <a name="step-6-create-a-container"></a>Passo 6: Criar um contentor

Crie um recipiente na sua conta de armazenamento utilizando qualquer uma destas ferramentas ou SDKs:

|Ferramentas|SDKs|
|---|---|
|[Portal do Azure](https://portal.azure.com)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AzCopy](../common/storage-use-azcopy-v10.md#transfer-data)|[Java](data-lake-storage-directory-file-acl-java.md)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[CLI do Azure](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST](/rest/api/storageservices/create-container)|

## <a name="step-7-mount-the-container"></a>Passo 7: Montar o recipiente

Crie um diretório no seu sistema Linux e, em seguida, monte um recipiente na conta de armazenamento.

1. Num sistema Linux, crie um diretório.

   ```
   mkdir -p /mnt/test
   ```

2. Monte um recipiente utilizando o seguinte comando.

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - Substitua o `<storage-account-name>` espaço reservado que aparece neste comando pelo nome da sua conta de armazenamento.  

   - Substitua o `<container-name>` espaço reservado pelo nome do seu recipiente.

---

## <a name="resolve-common-issues"></a>Resolver questões comuns

|Emissão / erro | Resolução|
|---|---|
|`Access denied by server while mounting`|Confirme que o cliente está a ser executado numa sub-rede suportada. Consulte as localizações da [rede suportada.](network-file-system-protocol-support.md#supported-network-connections)|
|`No such file or directory`| Confirme que o contentor que está a montar foi criado depois de verificar que a funcionalidade estava registada. Ver [Passo 2: Verifique se a função está registada](#step-2-verify-that-the-feature-is-registered). Além disso, certifique-se de digitar o comando de montagem e os seus parâmetros diretamente para o terminal. Se copiar e colar qualquer parte deste comando no terminal a partir de outra aplicação, os carateres ocultos nas informações coladas poderão ser a causa deste erro.|

## <a name="see-also"></a>Ver também

[Suporte ao protocolo do Sistema de Ficheiros de Rede (NFS) 3.0 no armazenamento do Azure Blob (pré-visualização)](network-file-system-protocol-support.md)
