---
title: 'Quickstart: Criar um cluster azure data explorer & DB'
description: Neste início rápido, aprenderá a criar um cluster e uma base de dados do Azure Data Explorer, e dados para ingerir (carregar).
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 07/22/2019
ms.openlocfilehash: ed0c570449a0c21e9eace1273228539db7c208da
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80238645"
---
# <a name="quickstart-create-an-azure-data-explorer-cluster-and-database"></a>Início Rápido: criar um cluster e uma base de dados do Azure Data Explorer

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Pitão](create-cluster-database-python.md)
> * [Modelo de BRAÇO](create-cluster-database-resource-manager.md)


O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. Para utilizar o Azure Data Explorer, primeiro crie um cluster e crie uma ou mais bases de dados nesse cluster. Em seguida, ingira (carregue) os dados para uma base de dados, de modo a poder executar consultas neles. Neste início rápido, irá criar um cluster e uma base de dados.

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-cluster"></a>Criar um cluster

Crie um cluster Azure Data Explorer com um conjunto definido de recursos de computação e armazenamento num grupo de recursos Azure.

1. Selecione o **+ Criar um** botão de recurso no canto superior esquerdo do portal.

1. Procure o *Azure Data Explorer*.

   ![Procurar recursos](media/create-cluster-database-portal/search-resources.png)

1. No **Azure Data Explorer**, na parte inferior do ecrã, selecione **Criar**.

1. Preencha os detalhes básicos do cluster com as seguintes informações.

   ![Criar formulário de cluster](media/create-cluster-database-portal/create-cluster-form2.png)

    **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Subscrição | A sua subscrição | Selecione a subscrição do Azure que quer utilizar para o cluster.|
    | Grupo de recursos | O seu grupo de recursos | Utilize um grupo de recursos existente ou crie um novo grupo de recursos. |
    | Nome do cluster | Um nome exclusivo de cluster | Escolha um nome exclusivo que identifique o seu cluster. O nome de domínio *[região].kusto.windows.net* é anexado ao nome de cluster que indicar. O nome pode conter apenas letras minúsculas e números. Deve conter de 4 a 22 caracteres.
    | Região | *Oeste dos EUA* ou *Oeste DOS EUA 2* | Selecione *West US* ou *West US 2* (se utilizar zonas de disponibilidade) para este arranque rápido. Para um sistema de produção, selecione a região que melhor se adequa às suas necessidades.
    | Zonas de disponibilidade | *1,* *2,* e/ou *3* | Coloque as instâncias de cluster em várias zonas de disponibilidade na mesma região (opcional). [As Zonas de Disponibilidade Azure](/azure/availability-zones/az-overview) são localizações físicas únicas dentro da mesma região de Azure. Protegem um cluster do Azure Data Explorer e dados de falhas parciais da região. Os nós de cluster são criados, por padrão, no mesmo centro de dados. Ao selecionar várias zonas de disponibilidade, pode eliminar um único ponto de falha e garantir uma elevada disponibilidade. A implantação para zonas de disponibilidade só é possível durante a criação do cluster e não pode ser modificada mais tarde.
    | Especificações do dispositivo | *D13_v2* | Selecione a especificação de preço mais baixa para este início rápido. Para um sistema de produção, selecione a especificação que melhor se adequa às suas necessidades.
    | | | |

1. Selecione **Review + crie** para rever os detalhes do cluster e **crie** para fornecer o cluster. O provisionamento normalmente demora cerca de 10 minutos.

1. Quando a implementação estiver concluída, selecione **Ir para o recurso**.

    ![Ir para recurso](media/create-cluster-database-portal/notification-resource.png)

## <a name="create-a-database"></a>Criar uma base de dados

Está agora pronto para o segundo passo do processo: a criação da base de dados.

1. No separador **Descrição Geral**, selecione **Criar base de dados**.

    ![Passo 2: criar uma base de dados](media/create-cluster-database-portal/database-creation.png)

1. Preencha o formulário com as seguintes informações.

    ![Criar formulário de base de dados](media/create-cluster-database-portal/create-database.png)

    **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Nome da base de dados | *TestDatabase* | O nome da base de dados tem de ser exclusivo dentro do cluster.
    | Período de retenção | *3650* | O tempo (em dias) para o qual é garantido que os dados são mantidos disponíveis para consulta. O intervalo de tempo é medido desde o momento em que os dados são ingeridos.
    | Período de colocação em cache | *31* | O período de tempo (em dias) para o qual se mantêm frequentemente consultados dados disponíveis no armazenamento ou RAM do SSD, em vez de em armazenamento a mais longo prazo.
    | | | |

1. Selecione **Criar** para criar a base de dados. Normalmente, a criação demora menos de um minuto. Quando o processo estiver concluído, volta ao separador **Descrição Geral** do cluster.

## <a name="run-basic-commands-in-the-database"></a>Executar comandos básicos na base de dados

Agora que tem o cluster e a base de dados, pode executar consultas e comandos. Ainda não tem quaisquer dados na base de dados, mas pode ver como funcionam as ferramentas.

1. No cluster, selecione **Consulta**. Colhe o `.show databases` comando na janela de consulta e, em seguida, selecione **Executar**.

    ![Mostrar comando de bases de dados](media/create-cluster-database-portal/show-databases.png)

    O conjunto de resultados mostra **TestDatabase**, a única base de dados no cluster.

1. Colhe o `.show tables` comando na janela de consulta e selecione **Executar**.

    Este comando devolve um conjunto de resultados vazio porque ainda não tem quaisquer tabelas. Vai adicionar uma tabela no próximo artigo desta série.

## <a name="stop-and-restart-the-cluster"></a>Parar e reiniciar o cluster

Pode parar e reiniciar um cluster consoante as necessidades empresariais.

1. Para parar o cluster, na parte superior do separador **Descrição Geral**, selecione **Parar**.

    Quando o cluster está parado, os dados não estão disponíveis para consultas e não pode ingerir dados novos.

1. Para reiniciar o cluster, na parte superior do separador **Descrição Geral**, selecione **Iniciar**.

    Quando o cluster é reiniciado, leva cerca de 10 minutos para ficar disponível (como quando foi originalmente provisionado). Demora mais tempo a carregar os dados para a cache frequente.  

## <a name="clean-up-resources"></a>Limpar recursos

Se planeia seguir outros quickstarts e tutoriais, guarde os recursos que criou. Caso contrário, limpe o seu grupo de recursos, para evitar incorrer em custos.

1. No portal Azure, selecione **grupos de recursos** na extrema esquerda e, em seguida, selecione o grupo de recursos que contém o seu cluster Data Explorer.  

1. **Selecione Eliminar o grupo de recursos** para eliminar todo o grupo de recursos. Se utilizar um grupo de recursos existente, pode optar por eliminar apenas o cluster Data Explorer.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Início Rápido: Ingerir dados do Hub de Eventos para o Azure Data Explorer](ingest-data-event-hub.md)


