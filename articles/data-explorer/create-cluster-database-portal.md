---
title: 'Início Rápido: criar um cluster e uma base de dados do Azure Data Explorer'
description: Neste início rápido, aprenderá a criar um cluster e uma base de dados do Azure Data Explorer, e dados para ingerir (carregar).
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 07/22/2019
ms.openlocfilehash: 10030cf69009c59fa607ad68c3b7ab19c7a22860
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326718"
---
# <a name="quickstart-create-an-azure-data-explorer-cluster-and-database"></a>Início Rápido: criar um cluster e uma base de dados do Azure Data Explorer

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Modelo ARM](create-cluster-database-resource-manager.md)


O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. Para usar o Azure Data Explorer, primeiro crie um cluster e crie um ou mais bancos de dados nesse cluster. Em seguida, você pode ingerir (carregar) dados em um banco de dado para poder executar consultas nele. Neste início rápido, irá criar um cluster e uma base de dados.

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Iniciar sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-cluster"></a>Criar um cluster

Crie um cluster de Data Explorer do Azure com um conjunto definido de recursos de computação e armazenamento em um grupo de recursos do Azure.

1. Selecione o botão (+) **Criar um recurso**, no canto superior esquerdo do portal.

1. Procure o *Azure Data Explorer*.

   ![Procurar recursos](media/create-cluster-database-portal/search-resources.png)

1. No **Azure Data Explorer**, na parte inferior do ecrã, selecione **Criar**.

1. Preencha os detalhes do cluster básico com as informações a seguir.

   ![Criar formulário de cluster](media/create-cluster-database-portal/create-cluster-form2.png)

    **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Subscrição | A sua subscrição | Selecione a subscrição do Azure que quer utilizar para o cluster.|
    | Grupo de recursos | Seu grupo de recursos | Use um grupo de recursos existente ou crie um novo grupo de recursos. |
    | Nome do cluster | Um nome exclusivo de cluster | Escolha um nome exclusivo que identifique o seu cluster. O nome de domínio *[região].kusto.windows.net* é anexado ao nome de cluster que indicar. O nome pode conter apenas letras minúsculas e números. Ele deve conter de 4 a 22 caracteres.
    | Região | *Oeste dos EUA* ou *oeste dos EUA 2* | Selecione *oeste dos EUA* ou *oeste dos EUA 2* (se estiver usando zonas de disponibilidade) para este guia de início rápido. Para um sistema de produção, selecione a região que melhor se adequa às suas necessidades.
    | Zonas de disponibilidade | *1*, *2*e/ou *3* | Coloque as instâncias de cluster em várias zonas de disponibilidade na mesma região (opcional). [Zonas de disponibilidade do Azure](/azure/availability-zones/az-overview) são locais físicos exclusivos na mesma região do Azure. Eles protegem um cluster Data Explorer do Azure e dados de uma falha parcial da região. Os nós de cluster são criados, por padrão, na mesma data center. Ao selecionar várias zonas de disponibilidade, você pode eliminar um único ponto de falha e garantir a alta disponibilidade. A implantação em zonas de disponibilidade só é possível durante a criação do cluster e não pode ser modificada em uma data posterior.
    | Especificações do dispositivo | *D13_v2* | Selecione a especificação de preço mais baixa para este início rápido. Para um sistema de produção, selecione a especificação que melhor se adequa às suas necessidades.
    | | | |

1. Selecione **examinar + criar** para examinar os detalhes do cluster e **criar** para provisionar o cluster. O provisionamento normalmente leva cerca de 10 minutos.

1. Quando a implantação for concluída, selecione **ir para o recurso**.

    ![Ir para recurso](media/create-cluster-database-portal/notification-resource.png)

## <a name="create-a-database"></a>Criar uma base de dados

Está agora pronto para o segundo passo do processo: a criação da base de dados.

1. No separador **Descrição Geral**, selecione **Criar base de dados**.

    ![Etapa 2: criar um banco de dados](media/create-cluster-database-portal/database-creation.png)

1. Preencha o formulário com as seguintes informações.

    ![Criar formulário de base de dados](media/create-cluster-database-portal/create-database.png)

    **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Nome da base de dados | *TestDatabase* | O nome da base de dados tem de ser exclusivo dentro do cluster.
    | Período de retenção | *3650* | O período de tempo (em dias) para o qual é garantido que os dados sejam mantidos disponíveis para consulta. O intervalo de tempo é medido desde o momento em que os dados são ingeridos.
    | Período de colocação em cache | *31* | O período de tempo (em dias) para o qual manter os dados consultados frequentemente disponíveis no armazenamento SSD ou RAM, em vez de no armazenamento de longo prazo.
    | | | |

1. Selecione **criar** para criar o banco de dados. Normalmente, a criação demora menos de um minuto. Quando o processo estiver concluído, volta ao separador **Descrição Geral** do cluster.

## <a name="run-basic-commands-in-the-database"></a>Executar comandos básicos na base de dados

Agora que tem o cluster e a base de dados, pode executar consultas e comandos. Ainda não tem quaisquer dados na base de dados, mas pode ver como funcionam as ferramentas.

1. No cluster, selecione **Consulta**. Cole o comando `.show databases` na janela de consulta e, em seguida, selecione **executar**.

    ![Mostrar comando de bases de dados](media/create-cluster-database-portal/show-databases.png)

    O conjunto de resultados mostra **TestDatabase**, a única base de dados no cluster.

1. Cole o `.show tables` de comando na janela de consulta e selecione **executar**.

    Este comando devolve um conjunto de resultados vazio porque ainda não tem quaisquer tabelas. Vai adicionar uma tabela no próximo artigo desta série.

## <a name="stop-and-restart-the-cluster"></a>Parar e reiniciar o cluster

Pode parar e reiniciar um cluster consoante as necessidades empresariais.

1. Para parar o cluster, na parte superior do separador **Descrição Geral**, selecione **Parar**.

    Quando o cluster está parado, os dados não estão disponíveis para consultas e não pode ingerir dados novos.

1. Para reiniciar o cluster, na parte superior do separador **Descrição Geral**, selecione **Iniciar**.

    Quando o cluster é reiniciado, demora cerca de 10 minutos para que ele se torne disponível (como quando ele foi provisionado originalmente). Demora mais tempo a carregar os dados para a cache frequente.  

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja seguir outros guias de início rápido e tutoriais, mantenha os recursos que você criou. Caso contrário, limpe o grupo de recursos para evitar incorrer em custos.

1. No portal do Azure, selecione **grupos de recursos** na extrema esquerda e, em seguida, selecione o grupo de recursos que contém o cluster data Explorer.  

1. Selecione **excluir grupo de recursos** para excluir o grupo de recursos inteiro. Se estiver usando um grupo de recursos existente, você poderá optar por excluir apenas o cluster Data Explorer.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Início Rápido: Ingerir dados do Hub de Eventos para o Azure Data Explorer](ingest-data-event-hub.md)


