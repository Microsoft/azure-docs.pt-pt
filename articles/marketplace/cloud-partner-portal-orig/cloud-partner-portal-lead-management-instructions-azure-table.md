---
title: Tabela do Azure | O Azure Marketplace
description: Configure a gestão de oportunidades potenciais para a tabela do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: pabutler
ms.openlocfilehash: a1bcab9816627b453ba8b20b7bcd9402c2dfd151
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240522"
---
# <a name="lead-management-instructions-for-azure-table"></a>Levar as instruções de gestão para tabelas do Azure

Este artigo descreve como configurar tabelas do Azure para armazenar oportunidades potenciais de venda. Tabela do Azure permite-lhe armazenar e personalizar as informações do cliente.


## <a name="how-to-configure-azure-table"></a>Como configurar tabelas do Azure

1. Se não tiver uma conta do Azure, pode [criar uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
2. Após a sua conta do Azure Active Directory, inicie sessão para o [portal do Azure](https://portal.azure.com).
3. No portal do Azure, crie uma conta de armazenamento utilizando o procedimento seguinte.  
    1. Selecione **+ criar um recurso** na barra de menus à esquerda.  O **New** será apresentado o painel (painel) à direita.
    2. Selecione **armazenamento** no **New** painel.  R **em destaque** é apresentada a lista à direita.
    3. Selecione **conta de armazenamento** para iniciar a criação da conta.  Siga as instruções no artigo [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Passos para criar uma conta de armazenamento do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    Para obter mais informações sobre contas de armazenamento, selecione [tutorial de início rápido](https://docs.microsoft.com/azure/storage/).  Para obter mais informações sobre os preços de armazenamento, consulte [preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/).

4. Aguarde até que a sua conta de armazenamento é aprovisionada, um processo que normalmente demora alguns minutos.  Em seguida, aceder à sua conta de armazenamento do **home page** página do portal do Azure ao selecionar **ver todos os seus recursos** ou ao selecionar **todos os recursos** no painel de navegação à esquerda barra de menus do portal do Azure.

    ![Aceder à sua conta de armazenamento do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

5. Do seu painel de conta de armazenamento, copie a cadeia de ligação de conta de armazenamento para a chave e cole-o para o **cadeia de ligação de conta de armazenamento** campo no Portal de parceiros de nuvem. Um exemplo de um sting de ligação é:

```sql
DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
```

  ![Chave de armazenamento do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Pode usar [Explorador de armazenamento do Azure](https://azurestorageexplorer.codeplex.com/) ou outra ferramenta semelhante para ver os dados na sua tabela de armazenamento. Também pode exportar os dados de tabelas do Azure.


## <a name="use-microsoft-flow-with-an-azure-table-optional"></a>Utilizar o Microsoft Flow com uma tabela do Azure (*opcional*) 

Pode usar [Microsoft Flow](https://docs.microsoft.com/flow/) para automatizar notificações sempre que uma oportunidade potencial é adicionada à tabela do Azure. Se não tiver uma conta, pode [Inscreva-se numa conta gratuita](https://flow.microsoft.com/).


### <a name="lead-notification-example"></a>Levar o exemplo de notificação

Utilize este exemplo como um guia para criar um fluxo básico que automaticamente envia uma notificação por e-mail quando uma nova oportunidade potencial é adicionada a uma tabela do Azure. Este exemplo configura uma recorrência para enviar informações de oportunidades potenciais a cada hora, se o armazenamento de tabela é atualizado.

1. Inicie sessão sua conta do Microsoft Flow.
2. Na barra de navegação esquerdo, selecione **os meus fluxos**.
3. Na barra de navegação superior, selecione **+ novo**.  
4. Na lista pendente, selecione **+ criar do zero**
5. Em criar um fluxo do zero, selecione **criar do zero**.

   ![Crie um novo fluxo do zero](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

6. Na página de pesquisa de acionadores e conectores, selecione **Acionadores**.
7. Sob **Acionadores**, selecione **periodicidade**.
8. Na **periodicidade** janela, mantenha a predefinição de 1 para **intervalo**. Partir do **frequência** lista pendente, selecione **hora**.

   >[!NOTE] 
   >Embora este exemplo utiliza um intervalo de 1 hora, pode selecionar o intervalo e a frequência com que é melhor para suas necessidades empresariais.

   ![Definir a frequência de 1 hora de periodicidade](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

9. Selecione **+ novo passo**.
10. Procure "Obter tempo passado" e, em seguida, selecione **obter tempo passado** em ações. 

    ![Localize e selecione passam da ação de tempo](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

11. Na **obter tempo passado** janela, definida a **intervalo** como 1.  Partir do **unidade de tempo** lista pendente, selecione **hora**.
    >[!IMPORTANT] 
    >Certifique-se de que corresponde desta unidade de intervalo e a hora, o intervalo e a frequência que configurou para a periodicidade.

    ![Conjunto get anteriores intervalo de tempo](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >Pode verificar o fluxo em qualquer altura para verificar que cada passo está configurado corretamente. Para verificar o fluxo, selecione **Verificador de fluxo** na barra de menus de fluxo.

No próximo conjunto de passos, vai ligar à sua tabela do Azure e configure a lógica de processamento para manipular novas oportunidades potenciais.

1. Depois do Get última etapa de tempo, selecione **+ novo passo**e, em seguida, procure "Entidades Get".
2. Sob **ações**, selecione **obter entidades**e, em seguida, selecione **Mostrar opções avançadas**.
3. Na **obter entidades** janela, fornecer informações para os seguintes campos:

   - **Tabela** – introduza o nome do seu armazenamento de tabelas do Azure. Captura de ecrã seguinte mostra a linha de comandos quando "MarketPlaceLeads" é introduzida para este exemplo. 

     ![Escolha um valor personalizado para o nome da tabela do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Consulta de filtro** – clique neste campo e o **obter tempo passado** ícone é apresentado numa janela de pop-up. Selecione **tempo passado** usá-lo como timestamp para filtrar a consulta. Em alternativa, pode colar a seguinte função no campo: createdTime `gt datetime'@{body('Get_past_time')}'` 

     ![Configurar a função de consulta de filtro](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

4. Selecione **novo passo** para adicionar uma condição para verificar a tabela do Azure para novas oportunidades potenciais.

   ![Utilize o passo de novo para adicionar uma condição para verificar a tabela do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

5. Na **escolher uma ação** janela, selecione **ações**e, em seguida, selecione o **condição** controle.

     ![Adicionar controlo de condição](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

6. Na **condição** janela, selecione a **escolher um valor** campo e, em seguida, selecione **expressão** na janela de pop-up.
7. Colar `length(body('Get_entities')?['value'])` para o ***fx*** campo. Selecione **OK** para adicionar esta função. Para concluir a configuração da condição:

   - Selecione "é superior a" na lista pendente.
   - Introduza 0 como o valor 

     ![Adicionar uma função para a condição](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

8. Configure a ação a efetuar com base no resultado da condição.

     ![Configure a ação com base nos resultados da condição](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

9. Se a condição for resolvida como **se não**, não fazer nada. 
10. Se a condição for resolvida como **em caso afirmativo**, acionar uma ação que se liga a sua conta do Office 365 para enviar um e-mail. Selecione **adicionar uma ação**.
11. Selecione **enviar um e-mail**. 
12. Na **enviar um e-mail** janela, fornecer informações para os seguintes campos:

    - **Para** -introduza um endereço de e-mail para todos os utilizadores que vão obter esta notificação.
    - **Assunto** – forneça um assunto do e-mail. Por exemplo: Novas oportunidades potenciais!
    - **Corpo**:   Adicione o texto que pretende incluir no cada e-mail (opcional) e, em seguida, cole no corpo `body('Get_entities')?['value']` como uma função para inserir informações de oportunidades potenciais.

      >[!NOTE] 
      >Pode inserir pontos de dados estáticos ou dinâmicos adicionais para o corpo deste e-mail.

      ![Configurar o e-mail de notificação de oportunidades potenciais](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

13. Selecione **guardar** para guardar o fluxo. Microsoft Flow irá automaticamente testar o fluxo de erros. Caso haja algum erro, o seu fluxo começa a ser executado depois de guardar.

Captura de ecrã seguinte mostra um exemplo de como o fluxo final deve ser.

[![Sequência de fluxo final](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

(*Clique na imagem para ampliá-la.* )


### <a name="manage-your-flow"></a>Gerir o fluxo

Gerir o fluxo depois que for executado é fácil.  Tem controle completo sobre o fluxo. Por exemplo, pode pará-la, editá-lo, ver um histórico de execuções e obter análises. Captura de ecrã seguinte mostra as opções que estão disponíveis para gerir um fluxo. 

 ![Gerir um fluxo](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

O fluxo se mantém operacional até que, impedi-lo utilizando o **desativar fluxo** opção.

Se não consegue quaisquer notificações de e-mail de oportunidades potenciais, significa que novas oportunidades potenciais ainda não foram adicionados à tabela do Azure. Se existirem quaisquer falhas de fluxo, irá receber um e-mail semelhante ao exemplo na captura de ecrã seguinte.

 ![Notificação de e-mail de falha de fluxo](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)


## <a name="next-steps"></a>Passos Seguintes

[Configurar oportunidades potenciais](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
