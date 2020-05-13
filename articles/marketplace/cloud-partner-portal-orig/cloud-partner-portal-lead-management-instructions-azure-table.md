---
title: Armazenamento de mesa azure [ Mercado Azure
description: Configure a gestão de chumbo no armazenamento da Mesa Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: dsindona
ms.openlocfilehash: e65f2041cdb2e25341bfd63783c70ec09c1216b5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124708"
---
# <a name="lead-management-instructions-for-table-storage"></a>Instruções de gestão de chumbo para armazenamento de mesa

Este artigo descreve como configurar o armazenamento da Tabela Azure para gerir os leads de vendas. O armazenamento de mesaajuda-o a armazenar e modificar a informação do cliente.

## <a name="configure-table-storage"></a>Configure armazenamento de mesa

1. Se não tiver uma conta Azure, [crie uma conta de teste gratuita.](https://azure.microsoft.com/pricing/free-trial/)
1. Depois de a sua conta estar ativa, inscreva-se no [portal Azure.](https://portal.azure.com)
1. No portal Azure, siga estes passos:  
    1. Selecione **+Criar um recurso** no painel do lado esquerdo. O **novo** painel abrirá.
    1. No painel **Novo,** selecione **Armazenamento**. Uma lista **em destaque** será aberta no lado direito.
    1. Selecione **a conta de armazenamento**. Em seguida, siga as instruções na [Create a storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Criar uma conta de armazenamento do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    Para mais informações sobre contas de armazenamento, consulte [os tutoriais Quickstart.](https://docs.microsoft.com/azure/storage/) Para obter informações sobre preços, consulte [o preço de armazenamento do Azure.](https://azure.microsoft.com/pricing/details/storage/)

1. Aguarde até que a sua conta de armazenamento seja disponibilizada, o que normalmente demora alguns minutos. Em seguida, aceda à conta a partir da página inicial do portal Azure: Selecione **Ver todos os seus recursos** ou todos os **recursos** no painel de navegação.

    ![Aceda à sua conta de armazenamento Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. Do painel da sua conta de armazenamento, copie o fio de ligação da conta de armazenamento para a chave. Colá-lo no campo **De conexão** String para a conta de armazenamento no Portal do Parceiro cloud.

    Cadeia de ligação exemplo:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Chave de armazenamento azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Pode utilizar o [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) ou uma ferramenta semelhante para ver os dados no armazenamento da sua mesa. Também pode exportar dados a partir dele.

## <a name="use-microsoft-flow-with-table-storage-optional"></a>Utilize o Microsoft Flow com armazenamento de mesa *(opcional)*

Pode utilizar o [Microsoft Flow](https://docs.microsoft.com/flow/) para enviar automaticamente notificações quando um fio de chumbo é adicionado ao armazenamento da sua mesa. Se não tiver uma conta Microsoft Flow, [inscreva-se para uma conta gratuita](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Exemplo de notificação de chumbo

Este exemplo mostra como criar um fluxo básico. O fluxo envia automaticamente uma notificação por e-mail de hora a hora quando novos cabos são adicionados ao armazenamento da sua mesa.

1. Inscreva-se na sua conta Microsoft Flow.
1. No painel de navegação do lado esquerdo, selecione **Os meus fluxos.**
1. Na barra de navegação superior, **selecione +New**.  
1. A partir da lista de lançamentos, **selecione +Criar a partir de branco**.
1. Sob **Criar um fluxo a partir de branco,** selecione Criar a partir de **branco**.

   ![Criar um novo fluxo a partir do branco](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. Nos conectores e aciona a página de pesquisa, selecione **Triggers**.
1. Em **Gatilhos,** **selecione Recurrence**.
1. Na janela **Recurrence,** mantenha a definição padrão de **1** para **intervalo**. A partir da lista de abandono de **frequência,** selecione **Hora**.

   >[!NOTE] 
   >Este exemplo usa um intervalo de uma hora. Mas pode selecionar um intervalo e uma frequência que melhor se adaptem às necessidades do seu negócio.

   ![Definir uma frequência de 1 hora para recorrência](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. **Selecione +Novo passo**.
1. Procure **o tempo passado**e, em seguida, selecione **Passar o tempo** sob Escolha uma **ação**.

    ![Encontre e selecione a ação "passar o tempo"](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. Na janela de **tempo Get passado,** dero o **intervalo** para **1**.  A partir da lista de abandono da **unidade de tempo,** selecione **Hora**.
    >[!IMPORTANT] 
    >Certifique-se de que a unidade **intervalo** e **tempo** corresponde ao intervalo e à frequência configurado para recorrência (passo 8).

    ![Detete o intervalo de tempo para além do tempo](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >Pode verificar o seu fluxo a qualquer momento para verificar se cada passo está configurado corretamente: Selecione **verificador flow** a partir da barra de menu Flow.

No próximo conjunto de passos, ligue-se à sua mesa de armazenamento e configura a lógica de processamento para lidar com novos cabos.

1. Depois do passo de **tempo do Get,** selecione **+Novo passo,** e depois procure **por entidades Get**.
1. No âmbito **de Ações**, selecione **Obter entidades,** e, em seguida, selecione **Mostrar opções avançadas**.
1. Na janela **Get entidades,** preencha os seguintes campos:

   - **Tabela**: o nome do seu armazenamento de mesa. A imagem seguinte mostra "MarketPlaceLeads" introduzido:

     ![Escolha um valor personalizado para o nome da tabela Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Consulta do filtro**: Quando selecionar este campo, o ícone do **tempo Get passado** é exibido numa janela pop-up. Selecione **tempo passado** para usar este valor como carimbo de tempo para filtrar a consulta. Ou, pode colar a seguinte função no campo:
   
      `CreatedTime Timestamp gt '@{body('Get_past_time')}'` 

     ![Configurar a função de consulta de filtro](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. Selecione **Novo passo** para adicionar uma condição para digitalizar o armazenamento da sua mesa para novos cabos.

   ![Use "Novo passo" para adicionar uma condição para digitalizar o armazenamento de mesa](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. Na **janela Escolha uma** janela de ação, selecione **Ações**, e, em seguida, selecione Controlo de **Condições**.

     ![Adicione um controlo de condição](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. Na janela **Condição,** **selecione Escolha um valor**e, em seguida, selecione **Expressão** na janela pop-up.
1. Pasta `length(body('Get_entities')?['value'])` no campo ***fx.*** Selecione **OK** para adicionar esta função. 



     ![Adicione uma função à condição](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. Instale a ação a tomar com base no resultado da circunstância.

    1. Selecione **é maior do que** a partir da lista de lançamentos.
   1. Insira **0** como valor.

     ![Criar uma ação com base nos resultados da condição](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. Se a condição resolver o "Se não", não faça nada.

    Se a condição resolver para "Se sim", desencadeie uma ação que ligue a sua conta office 365 para enviar um e-mail:
   1. Selecionar **Adicionar uma ação**.
   1. Selecione **Enviar uma mensagem de e-mail**.
   1. No Enviar uma janela de **e-mail,** insira informações nos seguintes campos:

      - **Para**: um endereço de e-mail para todos os que receberão a notificação.
      - **Assunto**: um assunto para o e-mail. Por exemplo: *Novas pistas!*
      - **Corpo**: o texto que pretende incluir em cada e-mail (opcional). Colá-lo também `body('Get_entities')?['value']` em função de inserir informações de chumbo.

        >[!NOTE] 
        >Pode inserir pontos de dados estáticos ou dinâmicos adicionais no corpo do e-mail.

      ![Configurar e-mail para notificação de chumbo](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. Selecione **Guardar** para guardar o fluxo. O Microsoft Flow irá testá-lo automaticamente por erros. Se não houver erros, o seu fluxo começa a funcionar depois de ser guardado.

    A imagem que se segue mostra um exemplo de como o fluxo final deve parecer.

    [![Sequência final de fluxo](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    *(Selecione a imagem para ampliá-la.*)

### <a name="manage-your-flow"></a>Gerir o seu fluxo

É fácil gerir o seu fluxo depois de correr. Tens controlo total sobre o teu fluxo. Por exemplo, pode detê-lo, editá-lo, ver um histórico de corridas, e obter análises. A imagem que se segue mostra as opções de gestão de fluxos.

 ![Opções de gestão de fluxos](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

O fluxo continua a funcionar até selecionar desligar o **fluxo**.

Se não receber notificações de e-mail de chumbo, não foram adicionadas novas pistas ao armazenamento da sua mesa.
Receberá um e-mail como o seguinte exemplo se ocorrer uma falha de fluxo:

 ![Notificação de e-mail de falha de fluxo](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>Passos seguintes

[Configurar oportunidades potenciais](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
