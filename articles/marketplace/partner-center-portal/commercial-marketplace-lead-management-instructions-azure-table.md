---
title: Gestão de chumbo com armazenamento de mesa Azure - mercado comercial da Microsoft
description: Saiba como usar o armazenamento da Tabela Azure para configurar os leads para o Microsoft AppSource e o Azure Marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 2ecca18e9de02bfe5f3bcb972d0b4034ab8012ac
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791022"
---
# <a name="use-azure-table-storage-to-manage-commercial-marketplace-leads"></a>Use o armazenamento da Mesa Azure para gerir os leads do mercado comercial

Se o seu sistema de gestão de relacionamento com o cliente (CRM) não for explicitamente suportado no Partner Center para receber os cabos Microsoft AppSource e Azure Marketplace, pode utilizar o armazenamento da Tabela Azure para lidar com estes cabos. Em seguida, pode optar por exportar os dados e importá-lo para o seu sistema de CRM. Este artigo explica como criar uma conta de armazenamento Azure e uma tabela nessa conta. Além disso, pode criar um novo fluxo utilizando power automate para enviar uma notificação de e-mail quando a sua oferta receber um chumbo.

## <a name="configure-an-azure-storage-account"></a>Configure uma conta de armazenamento Azure

1. Se não tiver uma conta Azure, pode [criar uma conta de teste gratuita.](https://azure.microsoft.com/pricing/free-trial/)
1. Depois da sua conta Azure estar ativa, inscreva-se no [portal Azure.](https://portal.azure.com)
1. No portal Azure, crie uma conta de armazenamento utilizando o seguinte procedimento:

    1. Selecione **+ Crie um recurso** na barra de menu seletiva esquerda. O **novo** painel parece estar à direita.
    1. Selecione **Armazenamento** no painel **Novo.** Uma lista **em destaque** aparece à direita.
    1. Selecione **conta de Armazenamento** para iniciar a criação de conta. Siga as instruções em [Criar uma conta](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)de armazenamento .

        ![Passos para criar uma conta de armazenamento Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        Para mais informações sobre contas de armazenamento, consulte [o tutorial Quickstart](https://docs.microsoft.com/azure/storage/). Para obter mais informações sobre os preços de armazenamento, consulte [os preços de armazenamento.](https://azure.microsoft.com/pricing/details/storage/)

1. Espere até que a sua conta de armazenamento seja aprovisionada. Este processo normalmente demora alguns minutos. 

## <a name="create-a-table-in-your-storage-account"></a>Crie uma tabela na sua conta de armazenamento

1. A partir da página **inicial** do portal Azure, selecione **Veja todos os seus recursos** para aceder à sua conta de armazenamento. Também pode selecionar **Todos os recursos** da barra de menu sobra do portal Azure.

    ![Aceda à sua conta de armazenamento Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

1. A partir do painel da sua conta de armazenamento, selecione **teclas de acesso** e copie o valor de cadeia de **ligação** para a chave. Poupe este valor porque é o valor de string de **conexão** de conta de armazenamento que você precisará fornecer no portal editorial para receber pistas para a sua oferta azure Marketplace. 

    Aqui está um exemplo de uma corda de ligação.

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.screens.net
    ```

    ![Chave de armazenamento azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

1. A partir do painel da sua conta de armazenamento, selecione **Tabelas,** e selecione **+ Tabela** para criar uma tabela. Introduza um nome para a sua mesa e selecione **OK**. Guarde este valor porque necessitará dele se quiser configurar um fluxo para receber notificações de e-mail quando os leads forem recebidos.

    ![Mesas azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    Pode utilizar o [Azure Storage Explorer](https://archive.codeplex.com/?p=azurestorageexplorer) ou qualquer outra ferramenta para ver os dados na sua tabela de armazenamento. Também pode exportar os dados na tabela Azure. 

## <a name="optional-use-power-automate-to-get-lead-notifications"></a>(Opcional) Use power automate para obter notificações de chumbo

Pode utilizar [o Power Automate](https://docs.microsoft.com/flow/) para automatizar notificações sempre que um fio for adicionado à sua tabela de armazenamento Azure. Se não tiver uma conta, pode [inscrever-se para uma conta gratuita.](https://flow.microsoft.com/)

### <a name="lead-notification-example"></a>Exemplo de notificação de chumbo

O exemplo cria um fluxo que envia automaticamente uma notificação por e-mail quando um novo chumbo é adicionado ao armazenamento da Tabela Azure. Este exemplo cria uma recorrência para enviar informações de chumbo a cada hora se o armazenamento da mesa for atualizado.

1. Inscreva-se na sua conta Power Automate.
1. Na barra esquerda, selecione **Os meus fluxos.**
1. Na barra superior, selecione **+ Novo**. 
1. Na lista de lançamentos, **selecione + Agendado -- a partir de branco**.

   ![Os meus fluxos + Programados -- em branco](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

1. Na **Construção de uma** janela de fluxo programada, para Repetir **cada**, selecione **1** para o intervalo e **Hora** para a frequência. Além disso, dê ao fluxo um nome, se quiser. Selecione **Criar**.

   >[!NOTE]
   >Embora este exemplo utilize um intervalo de uma hora, pode selecionar o intervalo e a frequência que é o melhor para as necessidades do seu negócio.

   ![Construir um fluxo programado](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

1. Selecione **+Novo passo**.
1. Na **janela Escolha uma** janela de ação, procure **passar o tempo.** Em seguida, em **Ações**, selecione **Passar o tempo**.

   ![Escolher uma ação](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

1. Na janela de **tempo Get passado,** coloque **intervalo** para **1**. A partir da lista de abandono da **unidade de tempo,** selecione **Hora**.

    >[!IMPORTANT]
    >Certifique-se de que o intervalo e a unidade de tempo que enviou no Passo 8 correspondem ao intervalo e frequência que configurapara recorrência no Passo 5.

    ![Set Get passado intervalo de tempo](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

   >[!TIP] 
   >Pode verificar o seu fluxo a qualquer momento para verificar se cada passo está configurado corretamente. Para verificar o seu fluxo, selecione **Flow checker** da barra de menu **Flow.**

   No próximo conjunto de passos, irá ligar-se à sua mesa e configurar a lógica de processamento para lidar com novos cabos.

1. Selecione **+Novo passo**. Em seguida, procure **por entidades Obter** Na Escolha uma janela de **ação.**
1. No âmbito **de Ações**, selecione Obter entidades (Armazenamento de **Mesa Azure)**.
1. Na janela de armazenamento de **mesa azul,** forneça informações para as seguintes caixas e selecione **Criar:**

    * **Nome de ligação**: Forneça um nome significativo para a ligação que está a estabelecer entre este fluxo e a tabela.
    * **Nome da conta**de armazenamento : Forneça o nome da conta de armazenamento para a sua tabela. Pode encontrar este nome na página de chaves de **acesso** da conta de armazenamento.
    * **Chave de Armazenamento Partilhada**: Forneça o valor-chave para a sua conta de loja para a sua tabela. Pode encontrar este valor na página de chaves de **acesso** da conta de armazenamento.

      ![Janela de armazenamento de mesa azul](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

   Depois de selecionar **Criar,** aparece a janela **de entidades Get.** Aqui, selecione **Mostrar opções avançadas,** e fornecer informações para as seguintes caixas:

   * **Tabela**: Selecione o nome da sua tabela (a partir de [Criar uma tabela](#create-a-table-in-your-storage-account)). A imagem que se segue mostra o pedido quando a tabela "marketplaceleads" é selecionada para este exemplo.

     ![Obtenha a janela das entidades](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

   * **Consulta do filtro**: Selecione esta caixa e colhe esta função na caixa:`Timestamp gt datetime'@{body('Get_past_time')}'`

     ![Obtenha entidades, filter queria caixa](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

1. Agora que completou a configuração da ligação à tabela Azure, selecione **Novo passo** para adicionar uma condição para digitalizar a tabela Azure para novas pistas.

1. Na **janela Escolha uma** janela de ação, selecione **Ações**. Em seguida, selecione **O Controlo de Condições**.

    ![Escolha uma janela de ação](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

1. Na janela **Condição,** **selecione Escolha um valor**. Em seguida, selecione **Expression** na janela pop-up.

1. Cola `length(body('Get_entities')?['value'])` na caixa **fx.** Selecione **OK** para adicionar esta função. 

1. Para terminar a configuração da condição:
    1. Selecione **é maior do que** a partir da lista de lançamentos.
    2. Insira **0** como valor.

        ![Janela de condição](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

   Nos próximos passos, você estabeleceu a ação a tomar com base no resultado da condição:

   * Se a condição resolver **se não,** não faça nada.
   * Se a condição se resolver **se sim,** desencadeie uma ação que ligue a sua conta office 365 para enviar um e-mail. 

1. Selecione **Adicionar uma ação** em se **sim**.

    ![Janela de condição, se sim, adicionar uma ação](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

1. Selecione **Enviar um e-mail (Office 365 Outlook)**.

    ![Janela de condição, se sim, enviar um e-mail](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!NOTE]
    >Para utilizar um fornecedor de e-mail diferente, procure e selecione Enviar uma notificação por **e-mail (Mail)** como ação. As instruções mostram-lhe como configurar utilizando o Office 365 Outlook, mas as instruções são semelhantes para um fornecedor de e-mail diferente.

1. Na janela Do Office 365 Outlook, forneça informações para as seguintes caixas:

    1. **Para**: Inserir um endereço de e-mail para todos os que receberão esta notificação.
    1. **Assunto**: Forneça um assunto para o e-mail. Um exemplo é **novas pistas!**
    1. **Corpo**: Adicione o texto que pretende incluir em cada e-mail (opcional) e, em seguida, colá-lo em `body('Get_entities')?['value']`.

    >[!NOTE]
    >Pode inserir pontos de dados estáticos ou dinâmicos adicionais para o corpo deste e-mail.

    ![Janela de condição, se sim, janela do Office 365 Outlook](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

1. Selecione **Guardar** para guardar o fluxo. Power Automate testa automaticamente o fluxo para erros. Se não houver erros, o seu fluxo começa a funcionar depois de ser guardado.

A imagem que se segue mostra um exemplo de como o fluxo final deve parecer.

![Um exemplo do fluxo final](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Gerir o seu fluxo

Gerir o seu fluxo depois de correr é fácil. Tens controlo total sobre o teu fluxo. Por exemplo, pode detê-lo, editá-lo, ver um histórico de corridas, e obter análises. A imagem que se segue mostra as opções disponíveis para gerir um fluxo.

 ![Gerir um fluxo](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

O fluxo continua a funcionar até que o pare utilizando a opção desligar a **curva.**

Se não receber notificações de e-mail de chumbo, significa que novas pistas não foram adicionadas à tabela Azure. Se houver alguma falha de fluxo, receberá um e-mail como este exemplo.

 ![Notificação de e-mail de falha de fluxo](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Configure a sua oferta para enviar pistas para a tabela Azure

Quando estiver pronto para configurar as informações de gestão de chumbo para a sua oferta no portal editorial, siga estes passos.

1. Vá à página de **configuração** da Oferta para a sua oferta.

1. Selecione **Ligar** sob a secção de gestão de **chumbo.**
     ![Gestão de oportunidades potenciais](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

1. Na janela pop-up de detalhes da **Ligação,** **selecione Azure Table** para o **destino Lead**. 
     ![Gestão de chumbo, detalhes de ligação](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

1. Colar na cadeia de ligação da conta de armazenamento Azure que criou seguindo passos anteriores na caixa de **cadeiade ligação** à conta de armazenamento.
     ![Gestão de chumbo, conta de armazenamento de detalhes de ligação](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

1. **E-mail de contacto**: Forneça e-mails para pessoas da sua empresa que devam receber notificações de e-mail quando um novo chumbo for recebido. Pode fornecer vários e-mails separando-os com pontos evíolos.

1. Selecione **OK**.

Para se certificar de que se ligou com sucesso a um destino de chumbo, selecione o botão **Validate.** Se for bem sucedido, terá um teste de chumbo no destino principal.

>[!NOTE]
>Tem de terminar de configurar o resto da oferta e publicá-la antes de poder receber pistas para a oferta.

Quando os cabos são gerados, a Microsoft envia pistas para a tabela Azure. Se configurar um fluxo, um e-mail também será enviado para o endereço de e-mail que configurado.
