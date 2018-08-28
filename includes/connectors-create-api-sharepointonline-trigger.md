Neste exemplo, mostrarei como utilizar o **o SharePoint Online - quando é criado um novo item** acionador para iniciar um fluxo de trabalho de aplicação lógica quando é criado um novo item numa lista do SharePoint Online.

> [!NOTE]
> Será solicitado que inicie sessão sua conta do SharePoint se não tiver já criado uma *ligação* ao SharePoint Online.  
> 
> 

1. Introduza *sharepoint* na caixa de pesquisa no designer de aplicações lógicas, em seguida, selecione a **SharePoint Online - quando é criado um novo item** acionador.  
   ![Imagem de Acionador online do SharePoint ](./media/connectors-create-api-sharepointonline/trigger-1.png)  
2. O **quando é criado um novo item** controlo é apresentado.  
   ![Imagem de Acionador online do SharePoint 2](./media/connectors-create-api-sharepointonline/trigger-2.png)   
3. Selecione um **URL do Site**. Este é o site online do SharePoint que pretende monitorizar a existência de novos itens acionar o fluxo de trabalho.  
   ![Imagem de Acionador online do SharePoint 3](./media/connectors-create-api-sharepointonline/trigger-3.png)   
4. Selecione um **nome da lista**. Esta é a lista no site do SharePoint Online que pretende monitorizar a existência de novos itens que irão acionar o fluxo de trabalho.  
   ![Imagem de Acionador online do SharePoint 4](./media/connectors-create-api-sharepointonline/trigger-4.png)   

Neste momento, a aplicação lógica foi configurada com um acionador que vai iniciar uma execução de outros acionadores e ações do fluxo de trabalho. Isto ocorrerá sempre que é criado um novo item numa lista do SharePoint Online que selecionou.  

