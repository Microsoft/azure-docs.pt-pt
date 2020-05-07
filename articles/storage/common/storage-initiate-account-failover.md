---
title: Iniciar uma falha na conta de armazenamento
titleSuffix: Azure Storage
description: Saiba como iniciar uma falha de conta caso o ponto final primário da sua conta de armazenamento fique indisponível. O failover atualiza a região secundária para se tornar a região primária para a sua conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/05/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 8de5290c41e6213e61b7b5b09cffba1f4638df30
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857033"
---
# <a name="initiate-a-storage-account-failover"></a>Iniciar uma falha na conta de armazenamento

Se o principal ponto final da sua conta de armazenamento geo-redundante ficar indisponível por qualquer motivo, pode iniciar uma falha na conta. Uma falha de conta atualiza o ponto final secundário para se tornar o ponto final primário da sua conta de armazenamento. Uma vez concluída a falha, os clientes podem começar a escrever para a nova região primária. Falha forçada permite-lhe manter alta disponibilidade para as suas aplicações.

Este artigo mostra como iniciar uma falha na conta para a sua conta de armazenamento usando o portal Azure, PowerShell ou Azure CLI. Para saber mais sobre o fracasso da conta, consulte a falha da [recuperação de desastres e](storage-disaster-recovery-guidance.md)da conta de armazenamento.

> [!WARNING]
> Uma falha de conta normalmente resulta em alguma perda de dados. Para compreender as implicações de uma falha de conta e preparar-se para a perda de dados, reveja Compreender o processo de [falha da conta](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder executar uma falha na conta de armazenamento, certifique-se de que a sua conta de armazenamento está configurada para geo-replicação. A sua conta de armazenamento pode utilizar qualquer uma das seguintes opções de despedimento:

- Armazenamento geo-redundante (GRS) ou armazenamento geo-redundante de acesso à leitura (RA-GRS)
- Armazenamento geo-zona redundante (GZRS) ou armazenamento geozona-redundante de acesso à leitura (RA-GZRS)

Para obter mais informações sobre a redundância do Armazenamento Azure, consulte o [despedimento do Azure Storage.](storage-redundancy.md)

## <a name="initiate-the-failover"></a>Iniciar a falha

## <a name="portal"></a>[Portal](#tab/azure-portal)

Para iniciar uma falha de conta no portal Azure, siga estes passos:

1. Navegue até à sua conta de armazenamento.
2. Em **Definições,** **selecione Geo-replicação**. A imagem seguinte mostra a geo-replicação e o estado de failover de uma conta de armazenamento.

    ![Screenshot mostrando geo-replicação e estado de failover](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. Verifique se a sua conta de armazenamento está configurada para armazenamento geo-redundante (GRS) ou armazenamento geo-redundante de acesso de leitura (RA-GRS). Caso contrário, selecione **Configuração** em **Definições** para atualizar a sua conta para ser geo-redundante. 
4. A propriedade **Do Último Tempo sincronizado** indica a distância que o secundário está atrás das primárias. **O Último Tempo de Sincronização** fornece uma estimativa da extensão da perda de dados que irá experimentar após a conclusão da falha.
5. **Selecione Preparar para a falha**.
6. Reveja o diálogo de confirmação. Quando estiver pronto, insira **Sim** para confirmar e iniciar a falha.

    ![Screenshot mostrando diálogo de confirmação para uma falha de conta](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A funcionalidade de failover da conta está geralmente disponível, mas ainda conta com um módulo de pré-visualização para powerShell. Para utilizar o PowerShell para iniciar uma falha na conta, tem primeiro de instalar o módulo de [pré-visualização Az.Storage 1.1.1.](https://www.powershellgallery.com/packages/Az.Storage/1.1.1-preview) Siga estes passos para instalar o módulo:

1. Desinstale quaisquer instalações anteriores da Azure PowerShell:

    - Remova quaisquer instalações anteriores do Azure PowerShell do Windows utilizando as **aplicações & definição** de definições em **Definições**.
    - Retire todos os módulos **Azure** de `%Program Files%\WindowsPowerShell\Modules`.

1. Certifique-se de que tem a versão mais recente do PowerShellGet instalada. Abra uma janela do Windows PowerShell e execute o seguinte comando para instalar a versão mais recente:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Feche e reabra a janela PowerShell depois de instalar o PowerShellGet.

1. Instale a versão mais recente do Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Instale um módulo de pré-visualização de armazenamento Azure que suporta falhas na conta:

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
    ```

Para iniciar uma falha de conta a partir da PowerShell, execute o seguinte comando:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name>
```

## <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para utilizar o Azure CLI para iniciar uma falha de conta, execute os seguintes comandos:

```azurecli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="important-implications-of-account-failover"></a>Implicações importantes da falha da conta

Quando inicia uma falha na conta para a sua conta de armazenamento, os registos dNS para o ponto final secundário são atualizados de modo a que o ponto final secundário se torne o ponto final primário. Certifique-se de que compreende o impacto potencial na sua conta de armazenamento antes de iniciar uma falha.

Para estimar a extensão da provável perda de dados antes de `Get-AzStorageAccount` iniciar uma falha, verifique `-IncludeGeoReplicationStats` a propriedade Last **Sync Time** utilizando o cmdlet PowerShell e inclua o parâmetro. Em seguida, verifique a `GeoReplicationStats` propriedade para a sua conta.

Após o failover, o seu tipo de conta de armazenamento é automaticamente convertido para armazenamento localmente redundante (LRS) na nova região primária. Pode reativar o armazenamento geo-redundante (GRS) ou o armazenamento geo-redundante de acesso de leitura (RA-GRS) para a conta. Note que a conversão de LRS para GRS ou RA-GRS incorre num custo adicional. Para obter informações adicionais, consulte os detalhes de preços da [largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

Depois de reativar o GRS para a sua conta de armazenamento, a Microsoft começa a replicar os dados da sua conta para a nova região secundária. O tempo de replicação depende da quantidade de dados que estão a ser replicados.  

## <a name="next-steps"></a>Passos seguintes

- [Falha na conta de recuperação e armazenamento de desastres](storage-disaster-recovery-guidance.md)
- [Use geo-redundância para conceber aplicações altamente disponíveis](geo-redundant-design.md)
- [Tutorial: Construir uma aplicação altamente disponível com armazenamento Blob](../blobs/storage-create-geo-redundant-storage.md)
