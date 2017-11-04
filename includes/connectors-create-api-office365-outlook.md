#### <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Azure; Pode criar um [conta gratuita](https://azure.microsoft.com/free)
* Um [do Office 365](https://office365.com) conta  

Antes de utilizar a sua conta Office 365 numa aplicação lógica, autorize a aplicação lógica para ligar à sua conta Office 365. Pode fazê-facilmente na sua aplicação lógica no portal do Azure.  

Autorize a aplicação lógica para ligar à sua conta Office 365 através dos seguintes passos:

1. Crie uma aplicação lógica. No designer Logic Apps, selecione **Mostrar Microsoft APIs geridas** na lista pendente lista e, em seguida, introduza "office 365" na caixa de pesquisa. Selecione um dos acionadores ou ações:  
    ![Passo de criação de ligação do Office 365](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  
2. Se ainda não criou anteriormente todas as ligações ao Office 365, lhe for pedido para iniciar sessão com as suas credenciais do Office 365:  
    ![Passo de criação de ligação do Office 365](./media/connectors-create-api-office365-outlook/office365-signin.png)  
3. Selecione **sessão**e introduza o nome de utilizador e palavra-passe. Selecione **sessão**:  
    ![Passo de criação de ligação do Office 365](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)
   
    Estas credenciais são utilizadas para autorizar a aplicação lógica para ligar a e aceder à sua conta Office 365. 
4. Tenha em atenção de que a ligação foi criada. Agora, continue com os outros passos na sua aplicação lógica:   
    ![Passo de criação de ligação do Office 365](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  

