---
title: Iniciar uma conta ativação pós-falha de armazenamento (pré-visualização) - armazenamento do Azure
description: Saiba como iniciar uma ativação pós-falha de conta no caso do ponto final primário para a sua conta de armazenamento fique indisponível. A ativação pós-falha atualiza a região secundária para se tornar a região primária para a sua conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/11/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f582ef8cca3c36bad40f14026aea1ad422b6106f
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56668569"
---
# <a name="initiate-a-storage-account-failover-preview"></a>Iniciar um failover de conta de armazenamento (pré-visualização)

Se o ponto final primário para a sua conta de armazenamento georredundante ficar indisponível por qualquer motivo, pode iniciar uma ativação pós-falha de conta (pré-visualização). O ponto final secundário para se tornar o ponto final primário para a sua conta de armazenamento de atualizações de uma ativação pós-falha de conta. Depois de concluída a ativação pós-falha, os clientes podem começar a escrever para a região primária nova. Ativação pós-falha forçada permite-lhe manter uma elevada disponibilidade para as suas aplicações.

Este artigo mostra como iniciar uma ativação pós-falha de conta para a conta de armazenamento com o portal do Azure, o PowerShell ou a CLI do Azure. Para saber mais sobre a ativação pós-falha de conta, veja [failover conta e de recuperação após desastre (pré-visualização) no armazenamento do Azure](storage-disaster-recovery-guidance.md).

> [!WARNING]
> Uma ativação pós-falha de conta normalmente resulta em alguma perda de dados. Para compreender as implicações de uma ativação pós-falha de conta e preparar para a perda de dados, reveja [compreender o processo de ativação pós-falha de conta](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

## <a name="prerequisites"></a>Pré-requisitos

Antes de realizar uma ativação pós-falha de conta na sua conta de armazenamento, certifique-se de que executou os seguintes passos:

- Registe-se na pré-visualização de ativação pós-falha de conta. Para obter informações sobre como registar, consulte [sobre a pré-visualização](storage-disaster-recovery-guidance.md#about-the-preview).
- Certifique-se de que a sua conta de armazenamento está configurada para utilizar o armazenamento georredundante (GRS) ou armazenamento georredundante com acesso de leitura (RA-GRS). Para obter mais informações sobre o armazenamento georredundante, consulte [armazenamento georredundante (GRS): A replicação do armazenamento do Azure entre regiões](storage-redundancy-grs.md). 

## <a name="important-implications-of-account-failover"></a>Importantes implicações de ativação pós-falha de conta

Quando iniciar uma ativação pós-falha de conta para a sua conta de armazenamento, os registos DNS para o ponto final secundário são atualizados para que o ponto final secundário torna-se o ponto final primário. Certifique-se de que compreenda o impacto potencial à sua conta de armazenamento antes de iniciar uma ativação pós-falha.

Para estimar o grau de perda de dados provavelmente antes de iniciar uma ativação pós-falha, verifique os **hora da última sincronização** propriedade usando o `Get-AzureRmStorageAccount` cmdlet do PowerShell e incluem o `-IncludeGeoReplicationStats` parâmetro. Em seguida, verifique o `GeoReplicationStats` propriedade para a sua conta. 

Após a ativação pós-falha, seu tipo de conta de armazenamento será convertido automaticamente para o armazenamento localmente redundante (LRS) na nova região primária. Pode reativar o armazenamento georredundante (GRS) ou armazenamento georredundante de acesso de leitura (RA-GRS) para a conta. Tenha em atenção que a conversão de LRS para GRS ou RA-GRS implica um custo adicional. Para obter mais informações, consulte [detalhes dos preços da largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/). 

Depois de ativar novamente o GRS para a sua conta de armazenamento, a Microsoft começa a replicar os dados na sua conta para a nova região secundária. O tempo de replicação é depende da quantidade de dados a ser replicadas.  

## <a name="azure-portal"></a>Portal do Azure

Para iniciar uma ativação pós-falha de conta do portal do Azure, siga estes passos:

1. Navegue até à sua conta de armazenamento.
2. Sob **configurações**, selecione **georreplicação**. A imagem seguinte mostra o estado de georreplicação e ativação pós-falha de uma conta de armazenamento.

    ![Captura de ecrã que mostra o estado de georreplicação e ativação pós-falha](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. Certifique-se de que a sua conta de armazenamento está configurada para armazenamento georredundante (GRS) ou armazenamento georredundante com acesso de leitura (RA-GRS). Se não for, em seguida, selecione **Configuration** sob **definições** para atualizar a sua conta para ser georredundante. 
4. O **hora da última sincronização** propriedade indica a distância secundário está por trás dos principais. **Sincronização de hora da última** fornece uma estimativa da extensão de perda de dados que irá ocorrer depois de concluída a ativação pós-falha.
5. Selecione **preparar para ativação pós-falha (pré-visualização)**. 
6. Reveja a caixa de diálogo de confirmação. Quando estiver pronto, introduza **Sim** para confirmar e inicie a ativação pós-falha.

    ![Captura de ecrã que mostra diálogo de confirmação para uma conta de ativação pós-falha](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershell"></a>PowerShell

Para utilizar o PowerShell para iniciar uma ativação pós-falha de conta, tem de instalar primeiro o 6.0.1 módulo de pré-visualização. Siga estes passos para instalar o módulo:

1. Desinstale quaisquer instalações anteriores do Azure PowerShell:

    - Remova quaisquer instalações anteriores do Azure PowerShell do Windows utilizando o **aplicações e funcionalidades** em **definições**.
    - Remover tudo **Azure*** módulos de `%Program Files%\WindowsPowerShell\Modules`.
    
1. Certifique-se de que tem a versão mais recente do PowerShellGet instalado. Abra uma janela do Windows PowerShell e execute o seguinte comando para instalar a versão mais recente:
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```
1. Feche e reabra a janela do PowerShell depois de instalar o PowerShellGet. 

1. Instale a versão mais recente do Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Instale um módulo de pré-visualização do armazenamento do Azure que suporte do Azure AD:
   
    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force 
    ```
1. Feche e reabra a janela do PowerShell.
 

Para iniciar uma ativação pós-falha de conta do PowerShell, execute o seguinte comando:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name> 
```

## <a name="azure-cli"></a>CLI do Azure

Utilizar a CLI do Azure para iniciar uma ativação pós-falha de conta, execute os seguintes comandos:

```cli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

## <a name="next-steps"></a>Passos Seguintes

- [Failover conta e de recuperação após desastre (pré-visualização) no armazenamento do Azure](storage-disaster-recovery-guidance.md)
- [Conceber aplicações de elevada disponibilidade com o RA-GRS](storage-designing-ha-apps-with-ragrs.md)
- [Tutorial: Criar uma aplicação de elevada disponibilidade com armazenamento de BLOBs](../blobs/storage-create-geo-redundant-storage.md) 
