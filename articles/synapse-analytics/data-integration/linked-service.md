---
title: Provision e garantir um serviço ligado na Azure Synapse Analytics
description: Saiba como fornecer e garantir um serviço ligado com a Managed Vnet
services: synapse-analytics
author: acomet
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 435c3fd6b1e6444fa3a31c68b4d74c2553d2e634
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430555"
---
# <a name="securing-a-linked-service-with-private-links"></a>Garantir um serviço ligado com Links Privados 

Neste artigo, você aprenderá a garantir um serviço ligado em Synapse com um ponto final privado.

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição Azure**: Se não tiver uma subscrição Azure, crie uma [conta Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta De Armazenamento Azure:** Utiliza o Azure Data Lake Gen 2 como uma loja de dados *de origem.* Se não tiver uma conta de armazenamento, consulte [Criar uma conta de Armazenamento Azure](../../storage/blobs/data-lake-storage-quickstart-create-account.md) para obter passos para criar uma. Certifique-se de que a Conta de Armazenamento tem a filtração IP do Estúdio Synapse para aceder à sua conta e que só permite que **as redes Selecionadas** acedam à conta de Armazenamento. A definição sob a lâmina **Firewalls e redes virtuais** deve parecer a imagem abaixo.

![Conta de Armazenamento Segura](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Criar um serviço ligado com Links Privados

No Azure Synapse Analytics, um serviço ligado é onde define as suas informações de ligação a outros serviços. Nesta secção, você adicionará Azure Synapse Analytics e Azure Data Lake Gen 2 como serviços ligados.

1. Abra o Estúdio Azure Synapse e vá ao separador **Manage.**
1. Sob **ligações externas,** selecione **serviços Linked**.
1. Para adicionar um serviço ligado, clique em **New**.
1. Selecione o azulejo Azure Data Lake Storage Gen2 da lista e clique **em Continuar**.
1. Certifique-se de que ativa **a Autoria Interativa**. Pode levar cerca de 1 minuto para ser ativado. 
1. Introduza as suas credenciais de autenticação. A chave da conta, o diretor de serviço e a identidade gerida são atualmente tipos de autenticação suportados. Clique na ligação de teste para verificar se as suas credenciais estão corretas.
1. Selecione **a ligação de teste,** deve falhar porque a Conta de Armazenamento não permite o acesso à sua sem a criação e aprovação de um Ponto Final Privado. Na mensagem de erro, deve ver um link para criar um **ponto final privado** que pode seguir para passar à próxima parte. Se seguir espetada a ligação, ignore a próxima parte.
1. Quando terminar, selecione **Criar**.

## <a name="create-a-managed-private-endpoint"></a>Criar um ponto final privado gerido

No caso de não ter clicado na hiperligação ao testar a ligação acima, siga o seguinte caminho. Agora precisa criar um ponto final privado gerido que irá ligar ao serviço ligado acima.

1. Vá ao separador **Manage.**
1. Vá à secção **Redes Virtuais Geridas.**
1. Selecione **+ Novo** sob ponto final privado gerido.
1. Selecione o azulejo Azure Data Lake Storage Gen2 da lista e selecione **Continue**.
1. Introduza o nome da Conta de Armazenamento que criou acima.
1. Selecione **Criar**
1. Deve ver depois de esperar alguns segundos que o link privado criado precisa de aprovação.

## <a name="approval-of-a-private-link"></a>Aprovação de um link privado
1. Selecione o Ponto Final Privado que criou acima. Pode ver uma hiperligação que lhe permitirá aprovar o Ponto Final Privado ao nível da Conta de Armazenamento. *Uma alternativa é ir diretamente para a Conta de Armazenamento do portal Azure e entrar na lâmina de **ligações de ponto final Privado.***
1. Marque o ponto final privado que criou no Estúdio e selecione **Approve**.
1. Adicione uma descrição e clique **em sim**
1. Volte para o Estúdio Synapse sob a secção redes **virtuais geridas** do **Managed**Tab.
1. Deve levar cerca de 1 minuto para obter a aprovação refletida para o seu ponto final privado.

## <a name="check-the-connection-works"></a>Verifique as obras de ligação
1. Vá ao separador **Gerir** e selecione o serviço ligado que criou.
1. Certifique-se de que **a autoria interativa** está ativa.
1. Selecione **Testar ligação**. Deviaver que a ligação fosse bem sucedida.

Estabeleceu agora uma ligação segura e privada entre a Synapse e o seu serviço ligado!

## <a name="next-steps"></a>Passos seguintes

Para desenvolver uma maior compreensão do ponto final privado gerido na Synapse Analytics, consulte o Conceito em torno do artigo de ponto final privado gerido pela [Synapse.](data-integration-data-lake.md)

Para obter mais informações sobre a integração de dados para a Synapse Analytics, consulte os dados de [Ingesting num](data-integration-data-lake.md) artigo do Data Lake.