---
title: Tabela do Azure | Azure Marketplace
description: Configure o gerenciamento de leads para a tabela do Azure.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 7151be3ac9f55825fd2e9dde35c9afda6a30726a
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902640"
---
# <a name="configure-lead-management-using-an-azure-table"></a>Configurar o gerenciamento de leads usando uma tabela do Azure

Se o seu sistema de gerenciamento de relacionamento com o cliente (CRM) não tiver suporte explícito no Partner Center para receber leads do Azure Marketplace e do AppSource, você poderá usar uma tabela do Azure para lidar com esses leads. Você pode optar por exportar os dados e importá-los para o sistema CRM. As instruções neste artigo orientarão você pelo processo de criação de uma conta de armazenamento do Azure e uma tabela do Azure sob essa conta. Além disso, você pode criar um novo fluxo usando Microsoft Flow para enviar uma notificação por email quando sua oferta receber um cliente potencial.

## <a name="configure-azure-table"></a>Configurar tabela do Azure

1. Se você não tiver uma conta do Azure, poderá [criar uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
2. Depois que sua conta do Azure estiver ativa, entre no [portal do Azure](https://portal.azure.com).
3. No portal do Azure, crie uma conta de armazenamento usando o procedimento a seguir.  
    1. Selecione **+ criar um recurso** na barra de menus à esquerda.  O **novo** painel (folha) será exibido à direita.
    2. Selecione **armazenamento** no painel **novo** .  Uma lista em **destaque** é exibida à direita.
    3. Selecione a **conta de armazenamento** para iniciar a criação da conta.  Siga as instruções no artigo [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

        ![Etapas para criar uma conta de armazenamento do Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        Para obter mais informações sobre contas de armazenamento, selecione [tutorial de início rápido](https://docs.microsoft.com/azure/storage/).  Para obter mais informações sobre preços de armazenamento, consulte [preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/).

4. Aguarde até que sua conta de armazenamento seja provisionada, um processo que normalmente leva alguns minutos.  Em seguida, acesse sua conta de armazenamento na **Home** page do portal do Azure selecionando **Ver todos os seus recursos** ou selecionando **todos os recursos** na barra de menus de navegação à esquerda da portal do Azure.

    ![Acessar sua conta de armazenamento do Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

5. No painel da sua conta de armazenamento, selecione **chaves de acesso** e copie o valor da *cadeia de conexão* para a chave. Salve esse valor, pois esse é o valor da *cadeia de conexão da conta de armazenamento* que você precisará fornecer no portal de publicação para receber clientes potenciais para sua oferta do Marketplace. 

    Um exemplo de um Stinger de conexão é:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

    ![Chave de armazenamento do Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

6. No painel da sua conta de armazenamento, selecione **tabelas** e selecione **+ tabela** para criar uma tabela. Insira um nome para a tabela e selecione **OK**. Salve esse valor, pois será necessário se você quiser configurar um MS Flow para receber notificações por email quando os clientes potenciais forem recebidos.

    ![Tabelas do Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    Você pode usar o [Gerenciador de armazenamento do Azure](https://archive.codeplex.com/?p=azurestorageexplorer) ou qualquer outra ferramenta para ver os dados em sua tabela de armazenamento. Você também pode exportar os dados na tabela do Azure. 

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>Adicional Usar Microsoft Flow com uma tabela do Azure  

Você pode usar [Microsoft Flow](https://docs.microsoft.com/flow/) para automatizar as notificações toda vez que um cliente potencial for adicionado à tabela do Azure. Se você não tiver uma conta, poderá se [inscrever para obter uma conta gratuita](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Exemplo de notificação de Lead

Use este exemplo como um guia para criar um fluxo simples que envia automaticamente uma notificação por email quando um novo cliente potencial é adicionado a uma tabela do Azure. Este exemplo configura uma recorrência para enviar informações do Lead a cada hora se o armazenamento de tabela for atualizado.

1. Entre na sua conta do Microsoft Flow.
2. Na barra de navegação à esquerda, selecione **meus fluxos**.
3. Na barra de navegação superior, selecione **+ novo**.  
4. Na lista suspensa, selecione **+ agendado-de em branco**

   ![Meus fluxos * * + agendados-de em branco * *](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

5.  Na janela *criar um fluxo agendado* em *repetir a cada* , selecione "1" para o intervalo e "hora" para frequência. Além disso, dê um nome ao fluxo, se desejar. Selecione **Criar**.

    >[!Note]
    >Embora este exemplo use um intervalo de 1 hora, você pode selecionar o intervalo e a frequência que é melhor para suas necessidades de negócios.

    ![Crie um fluxo agendado.](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

6. Selecione **+ nova etapa**.
7. Na janela *escolher uma ação* , pesquise "obter última vez" e selecione **obter o tempo passado** em ações.

   ![Escolha uma ação.](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

8. Na janela **obter tempo anterior** , defina o **intervalo** como 1. Na lista suspensa **unidade de tempo** , selecione **hora**.

    >[!Important]
    >Verifique se esse intervalo e unidade de tempo correspondem ao intervalo e à frequência configurados para recorrência na etapa 5.

    ![Definir intervalo de obtenção de tempo anterior](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

>[!TIP] 
>Você pode verificar seu fluxo a qualquer momento para verificar se cada etapa está configurada corretamente. Para verificar o fluxo, selecione **Verificador de fluxo** na barra de menus do fluxo.

No próximo conjunto de etapas, você se conectará à tabela do Azure e configurará a lógica de processamento para lidar com novos clientes potenciais.

9. Após a etapa obter hora anterior, selecione **+ nova etapa**e, em seguida, pesquise "obter entidades" na janela *escolher uma ação* .
10. Em **ações**, selecione **obter entidades (armazenamento de tabelas do Azure)** .
11. Na janela **armazenamento de tabelas do Azure** , forneça informações para os seguintes campos e selecione **criar**:

    * *Nome da conexão* – forneça um nome significativo para a conexão que você está estabelecendo entre esse fluxo e a tabela do Azure.
    * *Nome da conta de armazenamento* – forneça o nome da conta de armazenamento para sua tabela do Azure. Você pode encontrá-la na página chaves de **acesso** da conta de armazenamento.
    * *Chave de armazenamento compartilhado* – forneça o valor de chave para sua conta de armazenamento para sua tabela do Azure. Você pode encontrá-la na página chaves de **acesso** da conta de armazenamento.

        ![Armazenamento de tabelas do Azure.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

    Depois de clicar em criar, você verá uma janela *obter entidades* . Aqui, selecione **Mostrar opções avançadas** e forneça informações para os seguintes campos:

       * *Tabela* – selecione o nome do armazenamento de tabelas do Azure (da etapa 6 de instruções sobre como configurar uma tabela do Azure). A próxima captura de tela mostra o prompt quando a tabela "marketplaceleads" é selecionada para este exemplo.

            ![Entidades Get de tabela do Azure.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

        * *Consulta de filtro* – Selecione este campo e cole essa função no campo:`Timestamp gt datetime'@{body('Get_past_time')}'`

            ![Tabela do Azure obter entidades-filtrar querry.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

12. Agora que você concluiu a configuração da conexão com a tabela do Azure, selecione **nova etapa** para adicionar uma condição para verificar a tabela do Azure em busca de novos clientes potenciais. 

13. Na janela **escolher uma ação** , selecione **ações**e, em seguida, selecione o **controle condição**.

    ![Tabela do Azure-escolha uma ação.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

14. Na janela **condição** , selecione o campo **escolher um valor** e, em seguida, selecione **expressão** na janela pop-up.

15. Cole `length(body('Get_entities')?['value'])` no campo ***FX*** . Selecione **OK** para adicionar essa função. 

16. Para concluir a configuração da condição:
    1. Selecione "é maior que" na lista suspensa.
    2. Insira 0 como o valor

        ![Tabela-condição do Azure.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

Nas próximas etapas, você configurará a ação a ser tomada com base no resultado da condição.

* Se a condição for resolvida como **se não**, não faça nada.
* Se a condição for resolvida como **Sim**, dispare uma ação que conecta sua conta do Office 365 para enviar um email. 

17. Selecione **Adicionar uma ação** em **se sim**.

    ![Tabela do Azure-condição, * * se sim * *.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

18. Selecione **enviar um email (Outlook do Office 365)** .

    ![Tabela do Azure-condição, * * se Sim * *, enviar email.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!Note]
    >Se você quiser usar uma pesquisa de provedor de email diferente e selecionar enviar uma notificação por email (email) como a ação em vez disso. As instruções lhe mostrarão como configurar usando o Outlook do Office 365, mas as instruções são semelhantes para um provedor de email diferente.

19. Na janela do **Outlook do Office 365** , forneça informações para os seguintes campos:

    1. **Para** -Insira um endereço de email para todos que receberão essa notificação.
    1. **Assunto** – forneça um assunto para o email. Por exemplo: Novos leads!
    1. **Corpo** – adicione o texto que você deseja incluir em cada email (opcional) e cole no corpo `body('Get_entities')?['value']`.

    >[!Note]
    >Você pode inserir pontos de dados estáticos ou dinâmicos adicionais no corpo deste email.

    ![Tabela do Azure-condição, * * se Sim * *, janela do Outlook do Office 365.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

20. Selecione **salvar** para salvar o fluxo. Microsoft Flow testará automaticamente o fluxo em busca de erros. Se não houver nenhum erro, seu fluxo começará a ser executado após ser salvo.

A próxima captura de tela mostra um exemplo de como deve ser a aparência do fluxo final.

![Um exemplo do fluxo final.](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Gerenciar seu fluxo

O gerenciamento do fluxo após sua execução é fácil. Você tem controle total sobre o fluxo. Por exemplo, você pode interrompê-lo, editá-lo, ver um histórico de execuções e obter análises. A próxima captura de tela mostra as opções disponíveis para gerenciar um fluxo. 

 ![Gerenciando um fluxo](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

O fluxo continua em execução até que você o interrompa usando a opção **Ativar fluxo desligado** .

Se você não estiver obtendo notificações por email de Lead, isso significa que novos clientes potenciais não foram adicionados à tabela do Azure. Se houver alguma falha de fluxo, você receberá um email como o exemplo na próxima captura de tela.

 ![Notificação de email de falha do fluxo](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Configurar sua oferta para enviar clientes potenciais para a tabela do Azure

Quando você estiver pronto para configurar as informações de gerenciamento de Lead para sua oferta no portal de publicação, siga as etapas abaixo:

1. Navegue até a página de **instalação da oferta** para sua oferta.
2. Selecione **conectar** na seção Gerenciamento de leads.
3. Na janela pop-up detalhes da conexão, selecione **tabela do Azure** para o **destino do cliente potencial**e cole a cadeia de conexão da conta de armazenamento do Azure criada seguindo as etapas anteriores na **cadeia de conexão da conta de armazenamento** campo.
4. Selecione **Guardar**. 

>[!Note]
>Você deve concluir a configuração do restante da oferta e publicá-la antes de receber clientes potenciais para a oferta.

Quando os clientes potenciais são gerados, a Microsoft envia os clientes potenciais para a tabela do Azure. Se você configurou um fluxo, um email também será enviado para o endereço de email que você configurou.

![Gestão de oportunidades potenciais](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![Gerenciamento de Lead-detalhes da conexão](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![Gerenciamento de Lead-conta de armazenamento de detalhes de conexão](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

