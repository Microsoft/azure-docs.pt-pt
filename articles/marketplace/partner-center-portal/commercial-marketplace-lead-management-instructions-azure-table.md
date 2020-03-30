---
title: Mesa Azure [ Mesa Azul] Mercado Azure
description: Configure a gestão de chumbo para a Tabela Azure.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dsindona
ms.openlocfilehash: f511a60b533d6d1e0b1ae8847d0ee0fb6be3500c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288840"
---
# <a name="configure-lead-management-using-an-azure-table"></a>Configure a gestão de chumbo usando uma tabela Azure

Se o seu sistema de Gestão de Relacionamento com o Cliente (CRM) não for explicitamente suportado no Partner Center para receber os leads Azure Marketplace e AppSource, pode utilizar uma Tabela Azure para lidar com estes cabos. Em seguida, pode optar por exportar os dados e importá-lo para o seu sistema de CRM. As instruções deste artigo irão acompanhá-lo através do processo de criação de uma conta de Armazenamento Azure, e de uma Tabela Azure nessa conta. Além disso, pode criar um novo fluxo utilizando o Microsoft Flow para enviar uma notificação de e-mail quando a sua oferta receber um chumbo.

## <a name="configure-azure-table"></a>Mesa Azure configure

1. Se não tiver uma conta Azure, pode [criar uma conta de teste gratuita.](https://azure.microsoft.com/pricing/free-trial/)
2. Depois da sua conta Azure estar ativa, inscreva-se no [portal Azure.](https://portal.azure.com)
3. No portal Azure, crie uma conta de armazenamento utilizando o seguinte procedimento.  
    1. Selecione **+Criar um recurso** na barra de menu seletiva esquerda.  O **New** novo painel (lâmina) será exibido à direita.
    2. Selecione **Armazenamento** no painel **Novo.**  Uma lista **em destaque** é apresentada à direita.
    3. Selecione **Conta de Armazenamento** para iniciar a criação de conta.  Siga as instruções do artigo [Criar uma conta](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)de armazenamento .

        ![Passos para criar uma conta de armazenamento Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        Para mais informações sobre contas de armazenamento, selecione [tutorial Quickstart](https://docs.microsoft.com/azure/storage/).  Para obter mais informações sobre os preços de armazenamento, consulte [os preços de armazenamento.](https://azure.microsoft.com/pricing/details/storage/)

4. Aguarde até que a sua conta de armazenamento seja disponibilizada, um processo que normalmente demora alguns minutos.  Em seguida, aceda à sua conta de armazenamento a partir da página **inicial** do portal Azure, selecionando **Todos os seus recursos** ou selecionando Todos os **recursos** da barra de menude navegação esquerda do portal Azure.

    ![Aceda à sua conta de armazenamento Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

5. A partir do painel da sua conta de armazenamento, selecione **teclas de acesso** e copie o valor de cadeia de *ligação* para a chave. Guarde este valor, uma vez que este é o valor de string de *conexão* de conta de armazenamento que você precisará fornecer no portal de publicação para receber pistas para a sua oferta de mercado. 

    Um exemplo de uma picada de ligação é:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

    ![Chave de armazenamento azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

6. A partir do painel da sua conta de armazenamento, selecione **Tabelas** e **selecione +Tabela** para criar uma tabela. Introduza um nome para a sua mesa e selecione **OK**. Guarde este valor pois necessitará dele se quiser configurar um MS Flow para receber notificações de e-mail quando os leads forem recebidos.

    ![Mesas azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    Pode utilizar o explorador de [armazenamento Azure](https://archive.codeplex.com/?p=azurestorageexplorer) ou qualquer outra ferramenta para ver os dados na sua tabela de armazenamento. Também pode exportar os dados na Tabela Azure. 

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>(Opcional) Use o Microsoft Flow com uma tabela Azure  

Pode utilizar o [Microsoft Flow](https://docs.microsoft.com/flow/) para automatizar notificações sempre que um fio for adicionado à tabela Azure. Se não tiver uma conta, pode [inscrever-se para uma conta gratuita.](https://flow.microsoft.com/)

### <a name="lead-notification-example"></a>Exemplo de notificação de chumbo

Use este exemplo como um guia para criar um fluxo simples que automaticamente envia uma notificação por e-mail quando um novo fio é adicionado a uma tabela Azure. Este exemplo cria uma recorrência para enviar informações de chumbo a cada hora se o armazenamento da mesa for atualizado.

1. Inscreva-se na sua conta Microsoft Flow.
2. Na barra de navegação à esquerda, selecione **Os meus fluxos.**
3. Na barra de navegação superior, selecione **+ Novo**.  
4. Na lista de dropdown, **selecione + Agendado - em branco**

   ![Meus fluxos **+ Agendados - de blank**](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

5.  Na *Construção de uma* janela de fluxo programada sob *A Repetição de cada* "1" selecionado para intervalo e "hora" para a frequência. Além disso, dê ao fluxo um nome, se quiser. Selecione **Criar**.

    >[!Note]
    >Embora este exemplo utilize um intervalo de 1 hora, pode selecionar o intervalo e a frequência que é o melhor para as necessidades do seu negócio.

    ![Construir um fluxo programado.](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

6. Selecione **+Novo passo**.
7. Na *escolha de uma* janela de ação para "passar o tempo", e, em seguida, selecione **Passar o tempo** em Ações.

   ![Escolha uma ação.](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

8. Na janela do **tempo Get passado,** desloque o **intervalo** para 1. A partir da lista de abandono da **unidade de tempo,** selecione **Hora**.

    >[!Important]
    >Certifique-se de que esta unidade de intervalo e tempo corresponde ao intervalo e frequência configurado para recorrência no passo 5.

    ![Definir passar o intervalo de tempo](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

>[!TIP] 
>Pode verificar o seu fluxo a qualquer momento para verificar se cada passo está configurado corretamente. Para verificar o seu fluxo, selecione **Flow checker** da barra de menu Flow.

Nos próximos passos, irá ligar-se à sua tabela Azure e configurar a lógica de processamento para lidar com novos cabos.

9. Depois do passo de tempo do Get, selecione **+ Novo passo,** e depois procure "Obter entidades" na janela de ação Escolha uma janela de *ação.*
10. No âmbito **de Ações**, selecione Obter entidades (Armazenamento de **Mesa Azure)**.
11. Na janela de armazenamento de **mesa azul,** forneça informações para os seguintes campos e selecione **Criar:**

    * *Nome de ligação* - forneça um nome significativo para a ligação que está a estabelecer entre este fluxo e a Tabela Azure.
    * *Nome da conta* de armazenamento - forneça o nome da conta de armazenamento para a sua tabela Azure. Pode encontrar isso na página de chaves de **acesso** da conta de armazenamento.
    * *Chave de Armazenamento Partilhada* - forneça o valor-chave para a sua conta de loja para a sua tabela Azure. Pode encontrar isso na página de chaves de **acesso** da conta de armazenamento.

        ![Armazenamento de mesa azure.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

    Depois de clicar em Criar verá uma janela *De Obter entidades.* Aqui selecione **Mostrar opções avançadas** e fornecer informações para os seguintes campos:

       * *Tabela* - Selecione o nome do seu Armazenamento de Mesa Azure (a partir do passo 6 das instruções sobre como configurar uma tabela Azure). A próxima captura do ecrã mostra o pedido quando a tabela "marketplaceleads" é selecionada para este exemplo.

            ![Mesa Azure obter entidades.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

        * *Consulta de filtro* - Selecione este campo e colhe esta função no campo:`Timestamp gt datetime'@{body('Get_past_time')}'`

            ![Mesa Azure obter entidades - Filter Querry.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

12. Agora que completou a configuração da ligação à tabela Azure, selecione **Novo passo** para adicionar uma condição para digitalizar a tabela Azure para novas pistas. 

13. Na **janela Escolha uma** janela de ação, selecione **Ações**, e, em seguida, selecione o **controlo 'Condição'.**

    ![Mesa Azure - Escolha uma ação.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

14. Na janela **Condição,** selecione o campo **de valor Escolha um** campo de valor e, em seguida, selecione **Expressão** na janela popup.

15. Pasta `length(body('Get_entities')?['value'])` no campo ***fx.*** Selecione **OK** para adicionar esta função. 

16. Para terminar a configuração da condição:
    1. Selecione "é maior do que" da lista de abandono.
    2. Insira 0 como o valor

        ![Mesa Azure - Condição.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

Nos próximos passos, irá definir a ação a tomar com base no resultado da circunstância.

* Se a condição resolver **se não,** não faça nada.
* Se a condição se resolver **se sim,** desencadeie uma ação que ligue a sua conta office 365 para enviar um e-mail. 

17. Selecione **Adicionar uma ação** em se **sim**.

    ![Mesa Azure - Condição, **Se sim**.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

18. Selecione **Enviar um e-mail (Office 365 Outlook)**.

    ![Mesa Azure - Condição, **Se sim**, envie e-mail.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!Note]
    >Se pretender utilizar um fornecedor de e-mail diferente, procure e selecione Enviar uma notificação por e-mail (Mail) como ação. As instruções mostrar-lhe-ão como configurar usando o Office 365 Outlook, mas as instruções são semelhantes para um fornecedor de e-mail diferente.

19. Na janela **Do Office 365 Outlook,** forneça informações para os seguintes campos:

    1. **Para** - Inserir um endereço de e-mail para todos os que receberão esta notificação.
    1. **Assunto** - Forneça um assunto para o e-mail. Por exemplo: Novas pistas!
    1. **Corpo** - Adicione o texto que pretende incluir em cada e-mail (opcional) e, em seguida, colhe no corpo `body('Get_entities')?['value']`.

    >[!Note]
    >Pode inserir pontos de dados estáticos ou dinâmicos adicionais para o corpo deste e-mail.

    ![Mesa Azure - Condição, **Se sim**, janela do Office 365 Outlook.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

20. Selecione **Guardar** para guardar o fluxo. O Microsoft Flow irá testar automaticamente o fluxo de erros. Se não houver erros, o seu fluxo começa a funcionar depois de ser guardado.

A próxima captura do ecrã mostra um exemplo de como o fluxo final deve parecer.

![Um exemplo do fluxo final.](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Gerir o seu fluxo

Gerir o seu fluxo depois de correr é fácil. Tens controlo total sobre o teu fluxo. Por exemplo, pode detê-lo, editá-lo, ver um histórico de corridas, e obter análises. A próxima captura do ecrã mostra as opções disponíveis para gerir um fluxo. 

 ![Gerir um fluxo](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

O fluxo continua a funcionar até que o pare utilizando a opção desligar a **curva.**

Se não receber notificações de e-mail de chumbo, significa que novas pistas não foram adicionadas à tabela Azure. Se houver falhas de fluxo, receberá um e-mail como o exemplo na próxima captura do ecrã.

 ![Notificação de e-mail de falha de fluxo](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Configure a sua oferta para enviar pistas para a Tabela Azure

Quando estiver pronto para configurar as informações de gestão de chumbo para a sua oferta no portal editorial, siga os passos abaixo:

1. Navegue na página de **configuração** da Oferta para a sua oferta.
2. Selecione **Ligar** sob a secção de Gestão de Chumbo.
3. Na janela pop-up de detalhes da Ligação, **selecione Tabela Azure** para o **Destino de Chumbo**, e cola na cadeia de ligação da conta de armazenamento Azure que criou seguindo passos anteriores no campo de cadeia de ligação da conta de **armazenamento.**
4. Selecione **Guardar**. 

>[!Note]
>Tem de terminar de configurar o resto da oferta e publicá-la antes de poder receber pistas para a oferta.

Quando os cabos são gerados, a Microsoft envia pistas para a Tabela Azure. Se configurar um fluxo, um e-mail também será enviado para o endereço de e-mail que configurado.

![Gestão de oportunidades potenciais](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![Gestão de chumbo - detalhes de ligação](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![Gestão de chumbo - conta de armazenamento de detalhes de ligação](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

