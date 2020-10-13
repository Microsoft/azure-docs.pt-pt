---
title: Iniciar uma falha na conta de armazenamento
titleSuffix: Azure Storage
description: Saiba como iniciar uma falha de conta no caso de o principal ponto final da sua conta de armazenamento ficar indisponível. O failover atualiza a região secundária para se tornar a região primária para a sua conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/11/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e39548a923e76fc118dec4158398d02577ec20c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91610063"
---
# <a name="initiate-a-storage-account-failover"></a>Iniciar uma falha na conta de armazenamento

Se o ponto final principal da sua conta de armazenamento geo-redundante não se encontrar por qualquer motivo, pode iniciar uma falha na conta. Uma falha de conta atualiza o ponto final secundário para se tornar o principal ponto final da sua conta de armazenamento. Uma vez que o failover esteja concluído, os clientes podem começar a escrever para a nova região primária. O failover forçado permite-lhe manter uma elevada disponibilidade para as suas aplicações.

Este artigo mostra como iniciar uma falha de conta para a sua conta de armazenamento utilizando o portal Azure, PowerShell ou Azure CLI. Para saber mais sobre o failover da conta, consulte [a recuperação de desastres e a falha da conta de armazenamento.](storage-disaster-recovery-guidance.md)

> [!WARNING]
> Um falha na conta normalmente resulta em alguma perda de dados. Para compreender as implicações de uma falha de conta e preparar-se para a perda de dados, [reveja Compreender o processo de falha da conta](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder efetuar uma falha de conta na sua conta de armazenamento, certifique-se de que a sua conta de armazenamento está configurada para a geo-replicação. A sua conta de armazenamento pode utilizar qualquer uma das seguintes opções de redundância:

- Armazenamento geodusu redundante (GRS) ou armazenamento geo-redundante de acesso à leitura (RA-GRS)
- Armazenamento redundante de zonas geo-zona (GZRS) ou armazenamento de zonas de acesso à leitura redundante (RA-GZRS)

Para obter mais informações sobre a redundância do Azure Storage, consulte [a redundância do Azure Storage](storage-redundancy.md).

## <a name="initiate-the-failover"></a>Iniciar a falha

## <a name="portal"></a>[Portal](#tab/azure-portal)

Para iniciar uma falha de conta a partir do portal Azure, siga estes passos:

1. Navegue até à sua conta de armazenamento.
1. Em **Definições**, selecione **Geo-replicação**. A imagem a seguir mostra o estado de geo-replicação e falha de uma conta de armazenamento.

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-prepare.png" alt-text="Screenshot mostrando geo-replicação e estado de failover":::

1. Verifique se a sua conta de armazenamento está configurada para armazenamento geo-redundante (GRS) ou armazenamento geo-redundante de acesso à leitura (RA-GRS). Se não for, selecione **Configuração** em **Definições** para atualizar a sua conta para ser geo-redundante.
1. A **propriedade Last Sync Time** indica a distância do secundário a partir da primária. **O Último Tempo de Sincronização** fornece uma estimativa da extensão da perda de dados que irá experimentar após a conclusão da falha. Para obter mais informações sobre a verificação da propriedade **Last Sync Time,** consulte [a propriedade Last Sync Time para obter uma conta de armazenamento.](last-sync-time-get.md)
1. **Selecione Prepare-se para o failover**.
1. Reveja o diálogo de confirmação. Quando estiver pronto, insira **Sim** para confirmar e iniciar a falha.

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-confirm.png" alt-text="Screenshot mostrando geo-replicação e estado de failover":::

## <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A funcionalidade de falha da conta está geralmente disponível, mas ainda se baseia num módulo de pré-visualização para o PowerShell. Para utilizar o PowerShell para iniciar uma falha de conta, tem primeiro de instalar o módulo de pré-visualização Az.Storage [1.1.1.](https://www.powershellgallery.com/packages/Az.Storage/1.1.1-preview) Siga estes passos para instalar o módulo:

1. Desinstalar quaisquer instalações anteriores da Azure PowerShell:

    - Remova quaisquer instalações anteriores do Azure PowerShell do Windows utilizando as **funcionalidades & aplicações** de aplicações em **Definições**.
    - Remova todos os módulos **Azure** de `%Program Files%\WindowsPowerShell\Modules` .

1. Certifique-se de que tem a versão mais recente do PowerShellGet instalada. Abra uma janela Windows PowerShell e execute o seguinte comando para instalar a versão mais recente:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Feche e reabra a janela PowerShell depois de instalar o PowerShellGet.

1. Instale a versão mais recente do Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Instale um módulo de pré-visualização do Azure Storage que suporta o failover da conta:

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
    ```

Para iniciar uma falha de conta a partir da PowerShell, execute o seguinte comando:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name>
```

## <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para utilizar o CLI Azure para iniciar uma falha de conta, execute os seguintes comandos:

```azurecli-interactive
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="important-implications-of-account-failover"></a>Implicações importantes do failover da conta

Quando inicia uma falha de conta na sua conta de armazenamento, os registos DNS para o ponto final secundário são atualizados de modo a que o ponto final secundário se torne o principal ponto final. Certifique-se de que compreende o impacto potencial na sua conta de armazenamento antes de iniciar uma falha.

Para estimar a extensão da provável perda de dados antes de iniciar uma falha, verifique a propriedade **Last Sync Time.** Para obter mais informações sobre a verificação da propriedade **Last Sync Time,** consulte [a propriedade Last Sync Time para obter uma conta de armazenamento.](last-sync-time-get.md)

Após o failover, o seu tipo de conta de armazenamento é automaticamente convertido para armazenamento localmente redundante (LRS) na nova região primária. Pode ree capacitar o armazenamento geo-redundante (GRS) ou o armazenamento geo-redundante de acesso à leitura (RA-GRS) para a conta. Note que a conversão de LRS para GRS ou RA-GRS incorre num custo adicional. Para obter informações adicionais, consulte [detalhes de preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

Depois de reativar o GRS para a sua conta de armazenamento, a Microsoft começa a replicar os dados da sua conta para a nova região secundária. O tempo de replicação depende da quantidade de dados que estão a ser replicados.  

## <a name="next-steps"></a>Passos seguintes

- [Recuperação após desastre e ativação pós-falha de contas de armazenamento](storage-disaster-recovery-guidance.md)
- [Verifique a propriedade da Última Hora do Sincronização para obter uma conta de armazenamento](last-sync-time-get.md)
- [Use geo-redundância para projetar aplicações altamente disponíveis](geo-redundant-design.md)
- [Tutorial: Construa uma aplicação altamente disponível com armazenamento Blob](../blobs/storage-create-geo-redundant-storage.md)
