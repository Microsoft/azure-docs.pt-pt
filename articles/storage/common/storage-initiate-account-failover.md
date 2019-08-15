---
title: Iniciar um failover de conta de armazenamento (versão prévia) – armazenamento do Azure
description: Saiba como iniciar um failover de conta caso o ponto de extremidade primário da sua conta de armazenamento fique indisponível. O failover atualiza a região secundária para se tornar a região primária da sua conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/11/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: d94f6297f27eb3ea130b443ccf94052d391eb46d
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985330"
---
# <a name="initiate-a-storage-account-failover-preview"></a>Iniciar um failover de conta de armazenamento (versão prévia)

Se o ponto de extremidade primário para sua conta de armazenamento com redundância geográfica ficar indisponível por algum motivo, você poderá iniciar um failover de conta (versão prévia). Um failover de conta atualiza o ponto de extremidade secundário para se tornar o ponto de extremidade primário para sua conta de armazenamento. Quando o failover for concluído, os clientes poderão começar a gravar na nova região primária. O failover forçado permite que você mantenha a alta disponibilidade para seus aplicativos.

Este artigo mostra como iniciar um failover de conta para sua conta de armazenamento usando o portal do Azure, o PowerShell ou o CLI do Azure. Para saber mais sobre o failover de conta, consulte [recuperação de desastre e failover de conta (versão prévia) no armazenamento do Azure](storage-disaster-recovery-guidance.md).

> [!WARNING]
> Um failover de conta normalmente resulta em alguma perda de dados. Para entender as implicações de um failover de conta e para se preparar para a perda de dados, examine [entender o processo de failover de conta](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de executar um failover de conta em sua conta de armazenamento, verifique se você executou as seguintes etapas:

- Registre-se para a versão prévia de failover da conta. Para obter informações sobre como se registrar, consulte [sobre a versão prévia](storage-disaster-recovery-guidance.md#about-the-preview).
- Verifique se sua conta de armazenamento está configurada para usar o armazenamento com redundância geográfica (GRS) ou o armazenamento com redundância geográfica com acesso de leitura (RA-GRS). Para obter mais informações sobre o armazenamento com redundância geográfica [, consulte armazenamento com redundância geográfica (GRS): Replicação entre regiões para o armazenamento](storage-redundancy-grs.md)do Azure. 

## <a name="important-implications-of-account-failover"></a>Implicações importantes do failover de conta

Quando você inicia um failover de conta para sua conta de armazenamento, os registros DNS para o ponto de extremidade secundário são atualizados para que o ponto de extremidade secundário se torne o ponto de extremidade primário. Verifique se você entendeu o impacto potencial para sua conta de armazenamento antes de iniciar um failover.

Para estimar a extensão de perda de dados provável antes de iniciar um failover, verifique a propriedade **hora da última sincronização** usando o cmdlet do `Get-AzStorageAccount` PowerShell e inclua o `-IncludeGeoReplicationStats` parâmetro. Em seguida, `GeoReplicationStats` Verifique a propriedade da sua conta. 

Após o failover, o tipo de conta de armazenamento é convertido automaticamente em LRS (armazenamento com redundância local) na nova região primária. Você pode habilitar novamente o armazenamento com redundância geográfica (GRS) ou o armazenamento com redundância geográfica com acesso de leitura (RA-GRS) para a conta. Observe que a conversão de LRS para GRS ou RA-GRS incorre em um custo adicional. Para obter informações adicionais, consulte [detalhes de preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/). 

Depois que você reabilitar o GRS para sua conta de armazenamento, a Microsoft começará a replicar os dados em sua conta para a nova região secundária. O tempo de replicação depende da quantidade de dados sendo replicados.  

## <a name="azure-portal"></a>Portal do Azure

Para iniciar um failover de conta do portal do Azure, siga estas etapas:

1. Navegue até à sua conta de armazenamento.
2. Em **configurações**, selecione **replicação geográfica**. A imagem a seguir mostra a replicação geográfica e o status de failover de uma conta de armazenamento.

    ![Captura de tela mostrando o status de failover e replicação geográfica](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. Verifique se sua conta de armazenamento está configurada para armazenamento com redundância geográfica (GRS) ou armazenamento com redundância geográfica com acesso de leitura (RA-GRS). Se não for, selecione **configuração** em **configurações** para atualizar sua conta para ser com redundância geográfica. 
4. A propriedade **hora da última sincronização** indica até onde o secundário está atrás do primário. A **hora da última sincronização** fornece uma estimativa da extensão da perda de dados que você terá após a conclusão do failover.
5. Selecione **preparar para failover (versão prévia)** . 
6. Examine a caixa de diálogo de confirmação. Quando estiver pronto, insira **Sim** para confirmar e iniciar o failover.

    ![Captura de tela mostrando a caixa de diálogo de confirmação para um failover de conta](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershell"></a>PowerShell

Para usar o PowerShell para iniciar um failover de conta, você deve primeiro instalar o módulo 6.0.1 Preview. Siga estas etapas para instalar o módulo:

1. Desinstale todas as instalações anteriores do Azure PowerShell:

    - Remova todas as instalações anteriores do Azure PowerShell do Windows usando a configuração **aplicativos & recursos** em **configurações**.
    - Remova todos os módulos do `%Program Files%\WindowsPowerShell\Modules`Azure de.

1. Verifique se você tem a versão mais recente do PowerShellGet instalada. Abra uma janela do Windows PowerShell e execute o seguinte comando para instalar a versão mais recente:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Feche e reabra a janela do PowerShell depois de instalar o PowerShellGet. 

1. Instale a versão mais recente do Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Instalar um módulo de visualização do armazenamento do Azure que dá suporte ao failover de conta:

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

1. Feche e reabra a janela do PowerShell.
 
Para iniciar um failover de conta do PowerShell, execute o seguinte comando:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name> 
```

## <a name="azure-cli"></a>CLI do Azure

Para usar CLI do Azure para iniciar um failover de conta, execute os seguintes comandos:

```cli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

## <a name="next-steps"></a>Passos Seguintes

- [Recuperação de desastres e failover de conta (versão prévia) no armazenamento do Azure](storage-disaster-recovery-guidance.md)
- [Conceber aplicações de elevada disponibilidade com o RA-GRS](storage-designing-ha-apps-with-ragrs.md)
- [Tutorial: Crie um aplicativo altamente disponível com o armazenamento de BLOBs](../blobs/storage-create-geo-redundant-storage.md) 
