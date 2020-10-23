---
title: Criar e eliminar pontos finais privados num cluster Azure Stream Analytics
description: Aprenda a gerir pontos finais privados num cluster Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: b3ad0da1f910556c172c519faf953d4075647955
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92109056"
---
# <a name="create-and-delete-private-endpoints-in-an-azure-stream-analytics-cluster"></a>Criar e eliminar pontos finais privados num cluster Azure Stream Analytics

Pode ligar os trabalhos do Azure Stream Analytics em execução num cluster a recursos de entrada e saída que estão atrás de uma firewall ou de uma Rede Virtual (VNet) do Azure. Primeiro, crie um ponto final privado para um recurso, como o Hub de Eventos do Azure ou a Base de Dados SQL do Azure, no cluster do Stream Analytics. Em seguida, aprove a ligação do ponto final privado de entrada ou saída.

Uma vez aprovada a ligação, qualquer trabalho em execução no seu cluster Stream Analytics pode aceder ao recurso através do ponto final privado. Este artigo mostra como criar e eliminar pontos finais privados num cluster do Stream Analytics.

## <a name="create-private-endpoint-in-stream-analytics-cluster"></a>Criar ponto final privado no cluster Stream Analytics

Nesta secção, aprende-se a criar um ponto final privado num cluster Stream Analytics.

1. No portal Azure, localize e selecione o seu cluster Stream Analytics.

1. Em **Definições**, selecione **pontos finais privados**.

1. **Selecione Adicionar ponto final privado** e introduzir as seguintes informações para escolher o recurso que pretende aceder de forma segura através de um ponto final privado.

   |Definição|Valor|
   |---|---|
   |Nome|Insira qualquer nome para o seu ponto final privado. Se este nome for tomado, crie um nome único.|
   |Método de ligação|Selecione **Ligar a um recurso Azure no meu diretório**.<br><br>Pode escolher um dos seus recursos para se conectar de forma segura ao uso do ponto final privado, ou pode ligar-se ao recurso de outra pessoa usando um ID de recurso ou um pseudónimo que partilharam consigo.|
   |Subscrição|Selecione a sua subscrição.|
   |Tipo de recurso|Escolha o [tipo de recurso que mapeia para o seu recurso.](../private-link/private-endpoint-overview.md#private-link-resource)|
   |Recurso|Selecione o recurso que pretende ligar à utilização de ponto final privado.|
   |Subresource-alvo|O tipo de subreufonte para o recurso selecionado acima que o seu ponto final privado poderá aceder.|

   ![O ponto final privado cria experiência](./media/private-endpoints/create-private-endpoint.png)

1. Aprove a ligação a partir do recurso-alvo. Por exemplo, se criou um ponto final privado para uma instância da Base de Dados Azure SQL no passo anterior, deve ir a esta instância sql Database e ver uma ligação pendente que deve ser aprovada. Pode levar alguns minutos para o pedido de ligação aparecer.

    ![aprovar ponto final privado](./media/private-endpoints/approve-private-endpoint.png)

1. Pode voltar ao seu cluster Stream Analytics para ver a alteração do estado de **aprovação** pendente do cliente para **configuração de DNS pendente** para **configurar completa** dentro de alguns minutos.

## <a name="delete-a-private-endpoint-in-a-stream-analytics-cluster"></a>Elimine um ponto final privado num cluster Stream Analytics

1. No portal Azure, localize e selecione o seu cluster Stream Analytics.

1. Em **Definições**, selecione **pontos finais privados**.

1. Escolha o ponto final privado que pretende eliminar e selecione **Delete**.

   ![eliminar ponto final privado](./media/private-endpoints/delete-private-endpoint.png)

## <a name="next-steps"></a>Passos seguintes

Tem agora uma visão geral de como gerir pontos finais privados num cluster Azure Stream Analytics. Em seguida, você pode aprender a escalar seus clusters e executar empregos no seu cluster:

* [Escalar um cluster Azure Stream Analytics](scale-cluster.md)
* [Gerir trabalhos stream analytics em um cluster Stream Analytics](manage-jobs-cluster.md)
