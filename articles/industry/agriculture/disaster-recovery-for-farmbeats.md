---
title: Recuperação de desastres para FarmBeats
description: Este artigo descreve como a recuperação de dados protege de perder os seus dados.
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-umha
ms.openlocfilehash: bdd59d078da1df021b7e23ec858ba7305f72cfa1
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164215"
---
# <a name="disaster-recovery-for-farmbeats"></a>Recuperação de desastres para FarmBeats

A recuperação de dados protege-o de perder os seus dados num evento como o colapso da região de Azure. Em tal evento, você pode começar a falhar e recuperar os dados armazenados na sua implementação FarmBeats.

A recuperação de dados não é uma característica predefinida no Azure FarmBeats. Pode configurar esta funcionalidade manualmente, configurando os recursos Azure necessários que são utilizados pela FarmBeats para armazenar dados numa região emparelhada Azure. Utilizar Ative – Abordagem passiva para permitir a recuperação.

As seguintes secções fornecem informações sobre como pode configurar a recuperação de dados em Azure FarmBeats:

- [Permitir redundância de dados](#enable-data-redundancy)
- [Restaurar o serviço a partir de backup online](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>Permitir redundância de dados

A FarmBeats armazena dados em três serviços de primeira festa da Azure, que são **o armazenamento Azure,** **Cosmos DB** e **Time Series Insights**. Utilize as seguintes medidas para permitir a redundância de dados destes serviços a uma região de Azure emparelhada:

1.  **Azure Storage** - Siga esta orientação para permitir a redundância de dados para cada conta de armazenamento na sua implementação FarmBeats.
2.  **Azure Cosmos DB** - Siga esta orientação para permitir a redundância de dados para a cosmos DB conta a sua implementação FarmBeats.
3.  **Azure Time Series Insights (TSI)** - A TSI não oferece atualmente redundância de dados. Para recuperar os dados da Time Series Insights, vá ao seu sensor/parceiro meteorológico e empurre os dados novamente para a implementação do FarmBeats.

## <a name="restore-service-from-online-backup"></a>Restaurar o serviço a partir de backup online

Pode iniciar falhas e recuperar dados armazenados para os quais, cada uma das lojas de dados acima mencionadas para a sua implantação FarmBeats. Uma vez recuperados os dados para o armazenamento da Azure e para a Cosmos DB, crie outra implantação farmBeats na região emparelhada Azure e, em seguida, configuure a nova implementação para usar dados de lojas de dados restauradas (ou seja, Azure Storage e Cosmos DB) utilizando os passos abaixo:

1. [Configurar o Cosmos DB](#configure-cosmos-db)
2. [Conta de Armazenamento Configure](#configure-storage-account)


### <a name="configure-cosmos-db"></a>Configurar o Cosmos DB

Copie a chave de acesso do Cosmos DB restaurado e atualize o novo Cofre de Chaves FarmBeats Datahub.


  ![Screenshot que realça onde obter a cópia da chave de acesso.](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> Copie o URL do Cosmos DB restaurado e atualize-o na nova Configuração de Serviço de Aplicações FarmBeats Datahub. Agora pode eliminar a conta DB da Cosmos na nova implementação do FarmBeats.

  ![Screenshot que mostra onde copiar o URL da Cosmos DB restaurada.](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>Conta de Armazenamento Configure

Copie a chave de acesso da conta de armazenamento restaurada e atualize-a no novo Cofre de Chaves FarmBeats Datahub.

![Screenshot que mostra onde copiar a chave de acesso da conta de armazenamento restaurada.](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> Certifique-se de atualizar o nome da conta de armazenamento no novo ficheiro farmbeats Batch VM config.

![Recuperação Após Desastre](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

Da mesma forma, se tiver ativado a recuperação de dados para a sua conta de armazenamento do Acelerador, siga o passo 2 para atualizar a chave e nome de acesso à Conta de Armazenamento do acelerador, no novo caso FarmBeats.
