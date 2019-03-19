---
title: Tabela do Azure | Documentos da Microsoft
description: Configure a gestão de oportunidades potenciais para a tabela do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 31a800e851eb8a2f533cc8170856b16cd43c21cf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57850722"
---
# <a name="lead-management-instructions-for-azure-table"></a>Levar as instruções de gestão para a tabela do Azure

Este artigo descreve como configurar tabelas do Azure para armazenar oportunidades potenciais de venda. Tabela do Azure permite-lhe armazenar e personalizar as informações do cliente.

## <a name="to-configure-azure-table"></a>Para configurar tabelas do Azure

1.  Se não tiver uma conta do Azure, pode [criar uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

2.  Após a sua conta do Azure Active Directory, inicie sessão para o [portal do Azure](https://portal.azure.com).
3.  No portal do Azure, crie uma conta de armazenamento. Captura de ecrã seguinte mostra como criar uma conta de armazenamento. Para obter mais informações sobre os preços de armazenamento, consulte [preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/).

    ![Passos para criar uma conta de armazenamento do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

4.  Copie a cadeia de ligação de conta de armazenamento para a chave e cole-o para o **cadeia de ligação de conta de armazenamento** campo no Portal de parceiros de nuvem. É um exemplo de um sting de ligação `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net `
    
    ![Chave de armazenamento do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Pode usar [Explorador de armazenamento do Azure](https://azurestorageexplorer.codeplex.com/) ou qualquer outra ferramenta para ver os dados na sua tabela de armazenamento. Também pode exportar os dados na tabela do Azure.
dados.

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>**(Opcional)**  Utilize o Microsoft Flow com uma tabela do Azure

Pode usar [Microsoft Flow](https://docs.microsoft.com/flow/) para automatizar notificações sempre que uma oportunidade potencial é adicionada à tabela do Azure. Se não tiver um tem uma conta, pode [Inscreva-se numa conta gratuita](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Levar o exemplo de notificação

Utilize este exemplo como um guia para criar um fluxo simples que automaticamente envia uma notificação por e-mail quando uma nova oportunidade potencial é adicionada a uma tabela do Azure. Este exemplo configura uma recorrência para enviar informações de oportunidades potenciais a cada hora, se o armazenamento de tabela é atualizado.

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

   - **Consulta de filtro** – clica neste campo e Get anteriores tempo ícone é apresentado numa janela de pop-up. Selecione **tempo passado** usá-lo como timestamp para filtrar a consulta. Em alternativa, pode colar esta função no campo: `gt datetime'@{body('Get_past_time')}'`

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
    - **Corpo**:   Adicione o texto que pretende incluir no cada e-mail (opcional) e, em seguida, cole no corpo `('Get_entities')?['value']` como uma função para inserir informações de oportunidades potenciais.

      >[!NOTE] 
      >Pode inserir pontos de dados estáticos ou dinâmicos adicionais para o corpo deste e-mail.

       ![Configurar o e-mail de notificação de oportunidades potenciais](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

13. Selecione **guardar** para guardar o fluxo. Microsoft Flow irá automaticamente testar o fluxo de erros. Caso haja algum erro, o seu fluxo começa a ser executado depois de guardar.

Captura de ecrã seguinte mostra um exemplo de como o fluxo final deve ser.

 ![Sequência de fluxo final](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

### <a name="managing-your-flow"></a>Gerir o fluxo

Gerir o fluxo depois que for executado é fácil.  Tem controle completo sobre o fluxo. Por exemplo, pode pará-la, editá-lo, ver um histórico de execuções e obter análises. Captura de ecrã seguinte mostra as opções que estão disponíveis para gerir um fluxo. 

 ![Gerir um fluxo](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

O fluxo se mantém operacional até que, impedi-lo utilizando o **desativar fluxo** opção.

Se não consegue quaisquer notificações de e-mail de oportunidades potenciais, significa que novas oportunidades potenciais ainda não foram adicionados à tabela do Azure. Se existirem quaisquer falhas de fluxo, irá receber um e-mail semelhante ao exemplo na captura de ecrã seguinte.

 ![Notificação de e-mail de falha de fluxo](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>Passos Seguintes

[Configurar oportunidades potenciais](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)