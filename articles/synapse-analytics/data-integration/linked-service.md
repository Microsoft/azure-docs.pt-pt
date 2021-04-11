---
title: Garantir um serviço ligado
description: Saiba como providenciar e garantir um serviço ligado com o Managed VNet
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: be7ee2f4b2b8472c5edeff63e143d99c958bfc5a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627222"
---
# <a name="secure-a-linked-service-with-private-links"></a>Garantir um serviço ligado com links privados

Neste artigo, você aprenderá a garantir um serviço ligado em Synapse com um ponto final privado.

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição Azure**: Se não tiver uma subscrição do Azure, crie uma [conta Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta Azure Storage**: Utiliza o Azure Data Lake Gen 2 como uma loja de dados *de origem.* Se não tiver uma conta de armazenamento, consulte [criar uma conta de Armazenamento Azure](../../storage/common/storage-account-create.md) para obter passos para criar uma. Certifique-se de que a Conta de Armazenamento tem a filtragem IP do Estúdio Synapse para aceder à sua aceder e que só permite que **as redes Selecionadas acedam** à conta de Armazenamento. A definição sob as **firewalls** da lâmina e as redes virtuais devem parecer a imagem abaixo.

![Conta de Armazenamento Segura](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Criar um serviço ligado com links privados

No Azure Synapse Analytics, um serviço ligado é onde define as suas informações de ligação a outros serviços. Nesta secção, você adicionará Azure Synapse Analytics e Azure Data Lake Gen 2 como serviços ligados.

1. Abra o Azure Synapse Studio e vá ao **separador Gerir.**
1. Em **ligações externas**, selecione **serviços Linked**.
1. Para adicionar um serviço ligado, selecione **New**.
1. Selecione o azulejo Azure Data Lake Storage Gen2 da lista e selecione **Continue**.
1. Certifique-se de que ativa **a Autoria Interativa.** Pode levar cerca de 1 minuto para ser ativado. 
1. Insira as suas credenciais de autenticação. A chave de conta, o principal do serviço e a identidade gerida são atualmente tipos de autenticação suportados. Selecione a ligação de teste para verificar se as suas credenciais estão corretas.
1. Selecione **a ligação de teste,** deve falhar porque a Conta de Armazenamento não permite o acesso à sua sem a criação e aprovação de um Ponto Final Privado. Na mensagem de erro, deverá ver um link para criar um **ponto final privado** que pode seguir para ir à próxima parte. Se seguires esse link, salta a próxima parte.
1. Quando terminar, selecione **Criar**.

## <a name="create-a-managed-private-endpoint"></a>Criar um ponto final privado gerido

Se não selecionou para a hiperligação ao testar a ligação acima, siga o seguinte caminho. Crie um ponto final privado gerido que irá ligar ao serviço ligado acima.

1. Vá ao **separador Gerir.**
1. Aceda à secção **Redes Virtuais Geridas.**
1. Selecione **+ Novo** em Ponto final privado gerido.
1. Selecione o azulejo Azure Data Lake Storage Gen2 da lista e selecione **Continue**.
1. Insira o nome da Conta de Armazenamento que criou acima.
1. Selecione **Criar**
1. Você deve ver depois de esperar alguns segundos que o link privado criado precisa de uma aprovação.

## <a name="private-link-approval"></a>Aprovação de ligação privada
1. Selecione o Ponto Final Privado que criou acima. Pode ver uma hiperligação que lhe permitirá aprovar o Ponto Final Privado ao nível da Conta de Armazenamento. *Uma alternativa é ir diretamente para a Conta de Armazenamento do portal Azure e entrar na lâmina **de ligações de ponto final privado.***
1. Marque o ponto final privado que criou no Estúdio e selecione **Aprovar**.
1. Adicione uma descrição e selecione **sim**
1. Volte para o Synapse Studio sob a secção **Redes Virtuais Geridas** do **separador Managed.**
1. Deve levar cerca de 1 minuto para que a aprovação seja refletida para o seu ponto final privado.

## <a name="check-the-connection-works"></a>Verifique os trabalhos de ligação
1. Vá ao **separador Gerir** e selecione o serviço ligado que criou.
1. Certifique-se de que **a autoria interativa** está ativa.
1. Selecione **Testar ligação**. Devia ver se a ligação tinha sucesso.

Estabeleceu agora uma ligação segura e privada entre a Synapse e o seu serviço ligado.

## <a name="next-steps"></a>Passos seguintes


Para desenvolver uma maior compreensão do ponto final privado gerido no Azure Synapse Analytics, consulte [os pontos finais privados geridos](../security/synapse-workspace-managed-private-endpoints.md).


Para obter mais informações sobre a integração de dados para a Azure Synapse Analytics, consulte os [dados de Ingeste num](data-integration-data-lake.md) artigo do Data Lake.