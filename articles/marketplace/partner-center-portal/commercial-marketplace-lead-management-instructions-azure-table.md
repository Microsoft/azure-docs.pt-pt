---
title: Gestão de chumbo com armazenamento de mesa Azure - mercado comercial da Microsoft
description: Saiba como utilizar o armazenamento da Tabela Azure para configurar os leads para o Microsoft AppSource e para o Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 08/25/2020
ms.openlocfilehash: 3c8b9444344e5bae414145e2b3367c265114a423
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100586848"
---
# <a name="use-azure-table-storage-to-manage-commercial-marketplace-leads"></a>Use o armazenamento da Mesa Azure para gerir os leads de marketplace comercial

Se o seu sistema de gestão de relacionamento com o cliente (CRM) não for suportado explicitamente no Partner Center para receber os leads microsoft AppSource e Azure Marketplace, pode utilizar o armazenamento da Tabela Azure para lidar com estes leads. Em seguida, pode optar por exportar os dados e importá-lo para o seu sistema crm. Este artigo explica como criar uma conta de armazenamento Azure e uma tabela sob essa conta. Além disso, pode criar um novo fluxo utilizando o Power Automamate para enviar uma notificação por e-mail quando a sua oferta recebe uma pista.

## <a name="configure-an-azure-storage-account"></a>Configure uma conta de armazenamento Azure

1. Se não tiver uma conta Azure, pode [criar uma conta de teste gratuita.](https://azure.microsoft.com/pricing/free-trial/)
1. Depois da sua conta Azure estar ativa, inscreva-se no [portal Azure](https://portal.azure.com).
1. No portal Azure, crie uma conta de armazenamento utilizando o seguinte procedimento:

    1. Selecione **+ Crie um recurso** na barra de menu esquerda. O **novo** painel aparece à direita.
    1. Selecione **Armazenamento** no **painel Novo.** Uma lista **em destaque** aparece à direita.
    1. Selecione **a conta de Armazenamento** para iniciar a criação de conta. Siga as instruções na [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md?tabs=azure-portal).

        :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png" alt-text="Passos para criar uma conta de armazenamento Azure.":::

        Para obter mais informações sobre contas de armazenamento, consulte [o tutorial quickstart.](../../storage/index.yml) Para obter mais informações sobre os preços de armazenamento, consulte [os preços de Armazenamento.](https://azure.microsoft.com/pricing/details/storage/)

1. Aguarde até que a sua conta de armazenamento seja a provisionada. Este processo normalmente demora alguns minutos.

## <a name="create-a-table-in-your-storage-account"></a>Crie uma tabela na sua conta de armazenamento

1. Na página **inicial** do portal Azure, selecione **Ver todos os seus recursos** para aceder à sua conta de armazenamento. Também pode selecionar **todos os recursos** da barra de menu esquerda do portal Azure.

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png" alt-text="Aceda à sua conta de armazenamento Azure.":::

1. A partir do painel de conta de armazenamento, selecione **as teclas de acesso** e copie o valor da cadeia De **ligação** para a tecla. Guarde este valor porque é o valor de cadeia de **ligação de conta** de armazenamento que terá de fornecer no portal de publicação para receber leads para a sua oferta Azure Marketplace.

    Aqui está um exemplo de uma ligação.

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.screens.net
    ```

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png" alt-text="Chave de armazenamento Azure.":::


1. A partir do painel de conta de armazenamento, selecione **Tabelas** e selecione **+ Tabela** para criar uma tabela. Insira um nome para a sua mesa e selecione **OK**. Guarde este valor porque vai precisar dele se quiser configurar um fluxo para receber notificações por e-mail quando os leads são recebidos.

    ![Mesas azul](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    Pode utilizar [o Azure Storage Explorer](https://www.storageexplorer.com) ou qualquer outra ferramenta para ver os dados na sua mesa de armazenamento. Também pode exportar os dados na tabela Azure.

## <a name="optional-use-power-automate-to-get-lead-notifications"></a>(Opcional) Use Power Automamate para obter notificações de chumbo

Pode utilizar [o Power Automamate](/flow/) para automatizar notificações sempre que for adicionado um chumbo à sua tabela de Armazenamento Azure. Se não tiver uma conta, pode [inscrever-se numa conta gratuita.](https://flow.microsoft.com/)

### <a name="lead-notification-example"></a>Exemplo de notificação de chumbo

O exemplo cria um fluxo que envia automaticamente uma notificação por e-mail quando um novo chumbo é adicionado ao armazenamento da Tabela Azure. Este exemplo configura uma recorrência para enviar informações de chumbo a cada hora se o armazenamento da mesa for atualizado.

1. Inscreva-se na sua conta Power Automamate.
1. Na barra esquerda, selecione **Os meus fluxos.**
1. Na barra superior, selecione **+ Novo**.
1. Na lista de suspensos, selecione **+ Programado -- a partir de branco**.

   ![Meus fluxos + programado -- de branco](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

1. Na **Janela de fluxo programada para Construir,** para **Repetir cada**, selecione **1** para o intervalo e **hora** para a frequência. Além disso, dê um nome ao fluxo, se quiser. Selecione **Criar**.

   >[!NOTE]
   >Embora este exemplo utilize um intervalo de uma hora, pode selecionar o intervalo e a frequência que é melhor para as necessidades do seu negócio.

   ![Construir um fluxo programado](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

1. Selecione **+Novo passo**.
1. Na janela **de ação Escolha,** procure tempo **de passagem.** Em seguida, em **Ações**, **selecione Obter tempo passado**.

   ![Escolher uma ação](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

1. Na janela **de tempo Get passado,** definir **intervalo** para **1**. A partir da lista de down-down **da unidade Time,** selecione **Hour**.

    >[!IMPORTANT]
    >Certifique-se de que o intervalo e a unidade de tempo que enviou no Passo 8 correspondem ao intervalo e frequência que configura para recorrência no passo 5.

    ![Definir Obter intervalo de tempo passado](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

   >[!TIP]
   >Pode verificar o seu fluxo a qualquer momento para verificar se cada passo está configurado corretamente. Para verificar o seu fluxo, selecione **Flow checker** da barra de menu **Flow.**

   No próximo conjunto de passos, irá ligar-se à sua mesa e definir a lógica de processamento para lidar com novas ligações.

1. Selecione **+Novo passo**. Em seguida, procure **por obter entidades** na janela **de ação Escolher.**
1. Em **Ações**, **selecione Obter entidades (Azure Table Storage)**.
1. Na janela de armazenamento da **mesa Azure,** forneça informações para as seguintes caixas e selecione **Criar**:

    * **Nome de ligação**: Forneça um nome significativo para a ligação que está a estabelecer entre este fluxo e a tabela.
    * **Nome da conta de armazenamento**: Forneça o nome da conta de armazenamento para a sua mesa. Pode encontrar este nome na página de chaves de **acesso** da conta de armazenamento.
    * **Chave de armazenamento compartilhada**: Forneça o valor chave para a sua conta de loja para a sua mesa. Pode encontrar este valor na página de **chaves de acesso** da conta de armazenamento.

      ![Janela de armazenamento de mesa Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

   Depois de selecionar **Criar,** aparece a janela **'Get entities'.** Aqui, selecione **Mostrar opções avançadas,** e forneça informações para as seguintes caixas:

   * **Tabela**: Selecione o nome da sua tabela (a partir de [criar uma tabela).](#create-a-table-in-your-storage-account) A imagem a seguir mostra a solicitação quando `marketplaceleads` a tabela é selecionada para este exemplo.

     ![Obter janela de entidades](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

   * **Consulta de filtro**: Selecione esta caixa e cole esta função na caixa: `Timestamp gt datetime'@{body('Get_past_time')}'`

     ![Obter entidades, caixa de consulta de filtro](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

1. Agora que completou a configuração da ligação à tabela Azure, selecione **Novo passo** para adicionar uma condição para digitalizar a tabela Azure para novas pistas.

1. Na janela **de ação Escolha,** selecione **Ações**. Em seguida, selecione **Controlo de Condições**.

    ![Escolha uma janela de ação](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

1. Na janela **'Estado',** **selecione Escolha um valor**. Em seguida, selecione **Expressão** na janela pop-up.

1. Cole `length(body('Get_entities')?['value'])` na caixa **de fx.** Selecione **OK** para adicionar esta função.

1. Para terminar a configuração da condição:
    1. O selete **é maior do que** na lista de espera.
    2. Insira **0** como o valor.

        ![Janela de condição](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

   Nos próximos passos, você configura a ação a tomar com base no resultado da condição:

   * Se a condição resolver **se não,** não faça nada.
   * Se a condição resolver **se sim,** desencadeie uma ação que ligue o seu trabalho ou conta escolar para enviar um e-mail. 

1. **Selecione Adicione uma ação** em Se **sim**.

    ![Janela de condição, se sim, adicione uma ação](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

1. Selecione **Enviar um e-mail (Office 365 Outlook)**.

    ![Janela de condição, se sim, envie um e-mail](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!NOTE]
    >Para utilizar um fornecedor de e-mail diferente, procure e selecione **Enviar por email** o artigo Enviar por email o artigo Enviar por email o artigo 200. As instruções mostram como configurar utilizando o Office 365 Outlook, mas as instruções são semelhantes para um fornecedor de e-mail diferente.

1. Na janela Do Office 365 Outlook, forneça informações sobre as seguintes caixas:

    1. **Para**: Insira um endereço de e-mail para todos os que receberão esta notificação.
    1. **Objeto**: Fornecer um assunto para o e-mail. Um exemplo é **Novas pistas!**
    1. **Corpo**: Adicione o texto que pretende incluir em cada e-mail (opcional) e, em seguida, cole `body('Get_entities')?['value']` em .

    >[!NOTE]
    >Pode inserir pontos de dados estáticos ou dinâmicos adicionais para o corpo deste e-mail.

    ![Janela de condição, se sim, janela do Office 365 Outlook](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

1. Selecione **Guardar** para guardar o fluxo. O Automatismo automático testa automaticamente o fluxo para erros. Se não houver erros, o seu fluxo começa a funcionar depois de guardado.

A imagem que se segue mostra um exemplo de como o fluxo final deve parecer.

![Um exemplo do fluxo final](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Gerencie o seu fluxo

Gerir o seu fluxo depois de estar a funcionar é fácil. Tens controlo total sobre o teu fluxo. Por exemplo, pode detê-lo, editá-lo, ver uma história de execução e obter análises. A imagem a seguir mostra as opções disponíveis para gerir um fluxo.

 ![Gerir um fluxo](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

O fluxo continua a funcionar até o parar utilizando a opção **Desativar** o fluxo de saída.

Se não receber notificações de e-mail de chumbo, significa que novas pistas não foram adicionadas à tabela Azure. Se houver falhas de fluxo, receberá um e-mail como este exemplo.

 ![Notificação de e-mail de falha de fluxo](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Configure a sua oferta de envio leva à mesa Azure

Quando estiver pronto para configurar as informações de gestão de chumbo para a sua oferta no portal de publicação, siga estes passos.

1. Aceda à página **de configuração da Oferta** para a sua oferta.

1. Na secção **de comandos do Cliente,** selecione **Connect**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-table/customer-leads.png" alt-text="Ligações ao cliente":::

1. Na janela pop-up **de detalhes da Ligação,** selecione **Azure Table** para o destino **Chumbo**. 
     ![Gestão de chumbo, detalhes de conexão](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

1. Cole na cadeia de ligação a partir da conta de armazenamento Azure que criou seguindo os passos anteriores na caixa **de cordas de ligação da conta de armazenamento.**
     ![Gestão de chumbo, Conta de armazenamento de detalhes de conexão](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

1. **E-mail de contato:** Forneça e-mails para pessoas da sua empresa que deverão receber notificações por e-mail quando um novo chumbo for recebido. Pode fornecer vários e-mails separando-os com pontos-e-vírguis.

1. Selecione **OK**.

Para se certificar de que está ligado com sucesso a um destino de chumbo, selecione o botão **Validate.** Se for bem sucedido, terá uma pista de teste no destino principal.

>[!NOTE]
>Tem de configurar o resto da oferta e publicá-la antes de poder receber pistas para a oferta.

Quando os cabos são gerados, a Microsoft envia leva à tabela Azure. Se configurar um fluxo, será também enviado um e-mail para o endereço de e-mail configurado.

## <a name="next-steps"></a>Passos seguintes

- [FaQ de gestão de chumbo e resolução de problemas](../lead-management-faq.md)
