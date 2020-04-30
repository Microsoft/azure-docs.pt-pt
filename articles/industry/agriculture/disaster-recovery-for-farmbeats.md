---
title: Recuperação de desastres para FarmBeats
description: Este artigo descreve como a recuperação de dados protege da perda dos seus dados.
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-umha
ms.openlocfilehash: 1665c535d4b1fb6190ee5736b688b402f8b4a541
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81683894"
---
# <a name="disaster-recovery-for-farmbeats"></a>Recuperação de desastres para FarmBeats

A recuperação de dados protege-o de perder os seus dados num evento como o colapso da região de Azure. Neste evento, pode começar a falhar e recuperar os dados armazenados na sua implementação farmBeats.

A recuperação de dados não é uma funcionalidade padrão no Azure FarmBeats. Pode configurar esta funcionalidade manualmente configurando os recursos Azure necessários que são utilizados pela FarmBeats para armazenar dados numa região emparelhada com Azure. Utilizar a abordagem ativa – passiva para permitir a recuperação.

As seguintes secções fornecem informações sobre como configurar a recuperação de dados em Azure FarmBeats:

- [Ativar a redundância de dados](#enable-data-redundancy)
- [Restaurar o serviço a partir de backup online](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>Ativar a redundância de dados

A FarmBeats armazena dados em três serviços de primeira festa azure, que são **o armazenamento Azure,** **Cosmos DB** e Time Series **Insights.** Utilize as seguintes medidas para permitir a redundância de dados destes serviços para uma região azure emparelhada:

1.  **Armazenamento Azure** - Siga esta orientação para permitir a redundância de dados para cada conta de armazenamento na sua implementação FarmBeats.
2.  **Azure Cosmos DB** - Siga esta orientação para permitir a redundância de dados para a conta Cosmos DB a sua implementação FarmBeats.
3.  **Azure Time Series Insights (TSI)** - A TSI atualmente não oferece redundância de dados. Para recuperar os dados da Time Series Insights, vá ao seu sensor/parceiro meteorológico e empurre os dados novamente para a implementação do FarmBeats.

## <a name="restore-service-from-online-backup"></a>Restaurar o serviço a partir de backup online

Pode iniciar falhas e recuperar dados armazenados para os quais, cada uma das lojas de dados acima mencionadas para a sua implementação farmBeats. Depois de recuperar os dados para o armazenamento Azure e cosmos DB, crie outra implementação farmBeats na região emparelhada do Azure e, em seguida, configure a nova implementação para usar dados de lojas de dados restauradas (ou seja, Armazenamento Azure e Cosmos DB) utilizando os passos abaixo:

1. [Configurar o Cosmos DB](#configure-cosmos-db)
2. [Configure conta de armazenamento](#configure-storage-account)


### <a name="configure-cosmos-db"></a>Configurar o Cosmos DB

Copie a chave de acesso do Cosmos DB restaurado e atualize o novo FarmBeats Datahub Key Vault.


  ![Recuperação Após Desastre](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> Copie o URL do Cosmos DB restaurado e atualize-o na nova Configuração do Serviço de Aplicações FarmBeats Datahub. Agora pode eliminar a conta Cosmos DB na nova implementação farmBeats.

  ![Recuperação Após Desastre](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>Configure conta de armazenamento

Copie a chave de acesso da conta de armazenamento restaurada e atualize-a no novo FarmBeats Datahub Key Vault.

![Recuperação Após Desastre](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> Certifique-se de atualizar o nome da Conta de Armazenamento no novo ficheiro config FarmBeats Batch VM.

![Recuperação Após Desastre](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

Da mesma forma, se tiver ativado a recuperação de dados para a sua conta de armazenamento accelerator, siga o passo 2 para atualizar a chave e o nome de acesso à Conta de Armazenamento do acelerador, na nova instância FarmBeats.
