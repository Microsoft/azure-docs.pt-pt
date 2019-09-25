---
title: Monitore seus dados usando painéis baseados em pastas de trabalho Azure Monitor no Azure Sentinel | Microsoft Docs
description: Use este tutorial para aprender a monitorar seus dados usando painéis baseados em pastas de trabalho no Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 3f5e01a0ceebac092084e339994161763a11c49b
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240426"
---
# <a name="tutorial-monitor-your-data"></a>Tutorial: Monitorize os pipelines



Depois de [conectar suas fontes](quickstart-onboard.md) de dados ao Azure Sentinel, você pode monitorar os dados usando a integração do Azure Sentinel com Azure monitor pastas de trabalho, que fornece versatilidade na criação de painéis personalizados. Embora as pastas de trabalho sejam exibidas de forma diferente no Azure Sentinel, pode ser útil ver como [criar relatórios interativos com pastas de trabalho do Azure monitor](../azure-monitor/app/usage-workbooks.md). O Azure Sentinel permite que você crie pastas de trabalho personalizadas em seus dados e também vem com modelos de pasta de trabalho internos para que você possa obter informações rapidamente em seus dados assim que você conectar uma fonte de dados.


Este tutorial ajuda você a monitorar seus dados no Azure Sentinel.
> [!div class="checklist"]
> * Usar pastas de trabalho internas
> * Criar novas pastas de trabalho

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter pelo menos as permissões de colaborador ou leitor de pasta de trabalho no grupo de recursos do espaço de trabalho do Azure Sentinel.

> [!NOTE]
> As pastas de trabalho que você pode ver no Azure Sentinel são salvas no grupo de recursos de espaço de trabalho do Azure Sentinel e são marcadas pelo espaço de trabalho no qual foram criadas.

## <a name="use-built-in-workbooks"></a>Usar pastas de trabalho internas

1. Vá para **pastas de trabalho** e selecione **modelos** para ver a lista completa de pastas de trabalho internas do Azure Sentinel. Para ver quais são relevantes para os tipos de dados que você conectou, o campo **tipos de dados necessários** em cada pasta de trabalho listará o tipo de dados ao lado de uma marca de seleção verde se você já transmitir dados relevantes para o Azure Sentinel.
  ![ir para pastas de trabalho](./media/tutorial-monitor-data/access-workbooks.png)
1. Clique em **Exibir pasta de trabalho** para ver o modelo preenchido com seus dados.
  
1. Para editar a pasta de trabalho, selecione **salvar**e, em seguida, selecione o local onde você deseja salvar o arquivo JSON para o modelo. 

   > [!NOTE]
   > Isso cria um recurso do Azure com base no modelo relevante e salva o próprio arquivo JSON de modelo e não os dados.


1. Selecione **Exibir pasta de trabalho**. Em seguida, clique no botão **Editar** na parte superior. Agora você pode editar a pasta de trabalho e personalizá-la de acordo com suas necessidades. Para obter mais informações sobre como personalizar a pasta de trabalho, consulte como [criar relatórios interativos com pastas de trabalho do Azure monitor](../azure-monitor/app/usage-workbooks.md).
![exibir pastas de trabalho](./media/tutorial-monitor-data/workbook-graph.png)
1. Depois de fazer as alterações, você pode salvar a pasta de trabalho. 

1. Você também pode clonar a pasta de trabalho: Selecione **Editar** e **salvar como**, salvando-o com outro nome, na mesma assinatura e grupo de recursos. Essas pastas de trabalho são exibidas na guia **minhas pastas de trabalho** .


## <a name="create-new-workbook"></a>Criar nova pasta de trabalho

1. Vá para **pastas de trabalho** e, em seguida, selecione **Adicionar pasta de trabalho** para criar uma nova pasta de trabalho do zero.
  ![ir para pastas de trabalho](./media/tutorial-monitor-data/create-workbook.png)

1. Para editar a pasta de trabalho, selecione **Editar**e, em seguida, adicione texto, consultas e parâmetros conforme necessário. Para obter mais informações sobre como personalizar a pasta de trabalho, consulte como [criar relatórios interativos com pastas de trabalho do Azure monitor](../azure-monitor/app/usage-workbooks.md). 

1. Ao criar uma consulta, definir a **fonte de dados** é definida **como logs**, o **tipo de recurso** é definido como **log Analytics** e, em seguida, escolher os espaços de trabalho relevantes. 

1. Depois de criar sua pasta de trabalho, salve a pasta de trabalho, salvando-a na assinatura e no grupo de recursos do seu espaço de trabalho do Azure Sentinel.

1. Se você quiser permitir que outras pessoas em sua organização usem a pasta de trabalho, em **salvar para** selecionar **relatórios compartilhados**. Se você quiser que essa pasta de trabalho esteja disponível apenas para você, selecione **meus relatórios**.

1. Para alternar entre pastas de trabalho em seu espaço de trabalho, ![você pode selecionar abrir](./media/tutorial-monitor-data/switch.png)as pastas de trabalho do comutador no painel superior de qualquer pasta de trabalho. Na janela que é aberta à direita, alterne entre pastas de trabalho.

   ![Alternar pastas de trabalho](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="how-to-delete-workbooks"></a>Como excluir pastas de trabalho

Você pode excluir pastas de trabalho que foram criadas a partir de um modelo do Azure Sentinel. 

Para excluir uma pasta de trabalho personalizada:
1.  Selecione **Exibir pasta de trabalho**.
2.  Selecione **abrir** na parte superior.
3.  Na tabela que lista as pastas de trabalho, na linha da pasta de trabalho que você deseja excluir, selecione os três pontos no final da linha... para abrir o menu e, em seguida, selecione **excluir**. Isso removerá a pasta de trabalho salva.

> [!NOTE]
> Isso remove o recurso, bem como as alterações feitas no modelo. O modelo original permanecerá disponível.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a exibir seus dados no Azure Sentinel.

Para saber como automatizar suas respostas a ameaças, consulte [configurar respostas de ameaças automatizadas no Azure Sentinel](tutorial-respond-threats-playbook.md).
