---
title: Mover regiões Azure - Portal Azure - Base de Dados Azure para MariaDB
description: Mova uma base de dados Azure para o servidor MariaDB de uma região de Azure para outra usando uma réplica de leitura e o portal Azure.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/29/2020
ms.openlocfilehash: adb82b3ff371bdcdecbd964fef3c1d0c971df71a
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664941"
---
# <a name="move-an-azure-database-for-mariadb-server-to-another-region-by-using-the-azure-portal"></a>Mover uma base de dados Azure para o servidor MariaDB para outra região utilizando o portal Azure

Existem vários cenários para mover uma Base de Dados Azure existente para o servidor MariaDB de uma região para outra. Por exemplo, é melhor mover um servidor de produção para outra região como parte do seu planeamento de recuperação de desastres.

Você pode usar uma base de dados Azure para [réplica de leitura de região mariadb](concepts-read-replicas.md#cross-region-replication) para completar a mudança para outra região. Para tal, primeiro crie uma réplica de leitura na região alvo. Em seguida, pare a replicação no servidor de réplicas de leitura para torná-lo um servidor autónomo que aceita tanto ler como escrever tráfego. 

> [!NOTE]
> Este artigo centra-se em mover o seu servidor para uma região diferente. Se pretender mover o seu servidor para um grupo de recursos ou subscrição diferente, consulte o artigo [de movimento.](../azure-resource-manager/management/move-resource-group-and-subscription.md) 

## <a name="prerequisites"></a>Pré-requisitos

- A funcionalidade de réplica de leitura só está disponível para a Base de Dados Azure para servidores MariaDB nos níveis de preços otimizados para fins gerais ou memória. Certifique-se de que o servidor de origem está num destes níveis de preços.

- Certifique-se de que a sua Base de Dados Azure para o servidor de origem MariaDB se encontra na região Azure de onde pretende mover-se.

## <a name="prepare-to-move"></a>Preparar para mover

Para criar um servidor de réplica de leitura transversal na região alvo utilizando o portal Azure, utilize os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Selecione a Base de Dados Azure existente para o servidor MariaDB que pretende utilizar como servidor de origem. Esta ação abre a página **de visão geral.**
1. Selecione **a replicação** do menu, em **DEFINIÇÕES**.
1. Selecione **Adicionar Réplica**.
1. Introduza um nome para o servidor de réplica.
1. Selecione a localização para o servidor de réplica. A localização padrão é a mesma que a do servidor de origem. Verifique se selecionou o local do alvo onde pretende que a réplica seja implantada.
1. Selecione **OK** para confirmar a criação da réplica. Durante a criação de réplicas, os dados são copiados do servidor de origem para a réplica. Criar tempo pode durar vários minutos ou mais, proporcionalmente ao tamanho do servidor de origem.

>[!NOTE]
> Quando cria uma réplica, não herda os pontos finais de serviço VNet do servidor de origem. Estas regras devem ser criadas de forma independente para a réplica.

## <a name="move"></a>Mover

> [!IMPORTANT]
> O servidor autónomo não pode ser transformado numa réplica novamente.
> Antes de parar a replicação numa réplica de leitura, certifique-se de que a réplica tem todos os dados necessários.

Parar a replicação no servidor de réplicas faz com que se torne um servidor autónomo. Para impedir a replicação da réplica do portal Azure, utilize os seguintes passos:

1. Uma vez criada a réplica, localize e selecione a sua Base de Dados Azure para o servidor de origem MariaDB. 
1. Selecione **a replicação** do menu, em **DEFINIÇÕES**.
1. Selecione o servidor de réplica.
1. **Selecione a replicação stop**.
1. Confirme que pretende parar a replicação clicando **OK**.

## <a name="clean-up-source-server"></a>Limpar servidor de fonte

Pode querer eliminar a base de dados Azure de origem para o servidor MariaDB. Para fazê-lo, siga os seguintes passos:

1. Uma vez criada a réplica, localize e selecione a sua Base de Dados Azure para o servidor de origem MariaDB.
1. Na janela **'Vista Geral',** selecione **Delete**.
1. Digite o nome do servidor de origem para confirmar que pretende eliminar.
1. Selecione **Eliminar**.

## <a name="next-steps"></a>Próximos passos

Neste tutorial, você mudou uma Base de Dados Azure para o servidor MariaDB de uma região para outra, usando o portal Azure e, em seguida, limpou os recursos de origem não necessários. 

- Saiba mais sobre [ler réplicas](concepts-read-replicas.md)
- Saiba mais sobre [a gestão de réplicas de leitura no portal Azure](howto-read-replicas-portal.md)
- Saiba mais sobre opções [de continuidade de negócios](concepts-business-continuity.md)