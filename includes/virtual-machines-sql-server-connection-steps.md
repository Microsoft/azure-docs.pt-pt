---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 297317ff33d88d6390220980ef35f2538579e310
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67184200"
---
### <a name="open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine"></a>Abrir portas TCP na firewall do Windows para a instância predefinida do Motor de Base de Dados
1. Ligue-se à máquina virtual com o Ambiente de Trabalho Remoto. Para obter instruções detalhadas sobre como ligar à VM, veja [Abrir uma VM do SQL com o Ambiente de Trabalho Remoto](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md#remotedesktop).
2. Uma vez inscrito, no ecrã Iniciar, digite **WF.msc,** e bata no ENTER.
   
    ![Iniciar o Programa de Firewall](./media/virtual-machines-sql-server-connection-steps/12Open-WF.png)
3. Na **Firewall do Windows com Segurança Avançada**, no painel esquerdo, clique com o botão direito do rato em **Regras de Entrada** e, em seguida, clique em **Nova Regra** no painel de ação.
   
    ![Nova Regra](./media/virtual-machines-sql-server-connection-steps/13New-FW-Rule.png)
4. Na caixa de diálogo **Assistente de Nova Regra de Entrada**, em **Tipo de Regra**, selecione **Porta** e, em seguida, clique em **Seguinte**.
5. Na caixa de diálogo **Protocolo e Portas**, utilize o **TCP** predefinido. Na caixa **Portas locais específicas**, escreva o número de porta da instância do Motor de Base de Dados (**1433** para a instância predefinida ou a sua escolha para a porta privada no passo do ponto final).
   
    ![Porta TCP 1433](./media/virtual-machines-sql-server-connection-steps/14Port-1433.png)
6. Clique em **Seguinte**.
7. Na caixa de diálogo **Ação**, selecione **Permitir a ligação** e, em seguida, clique em **Seguinte**.
   
    **Nota de Segurança:** selecionar **Permitir a ligação se for segura** pode fornecer segurança adicional. Selecione esta opção se quiser configurar opções de segurança adicionais no seu ambiente.
   
    ![Permitir Ligações](./media/virtual-machines-sql-server-connection-steps/15Allow-Connection.png)
8. Na caixa de diálogo **Perfil**, selecione **Público**, **Privado** e **Domínio**. Em seguida, clique em **Seguinte**.
   
    **Nota de segurança:** selecionar **Público** permite o acesso pela Internet. Sempre que for possível, selecione um perfil mais restritivo.
   
    ![Perfil Público](./media/virtual-machines-sql-server-connection-steps/16Public-Private-Domain-Profile.png)
9. Na caixa de diálogo **Nome**, escreva um nome e descrição para esta regra e, em seguida, clique em **Concluir**.
   
    ![Nome da Regra](./media/virtual-machines-sql-server-connection-steps/17Rule-Name.png)

Abra portas adicionais para outros componentes conforme necessário. Para obter mais informações, veja [Configurar a Firewall do Windows para Permitir o Acesso do SQL Server](https://msdn.microsoft.com/library/cc646023.aspx).

### <a name="configure-sql-server-to-listen-on-the-tcp-protocol"></a>Configurar o SQL Server para escutar o protocolo TCP

[!INCLUDE [Enable TCP](virtual-machines-sql-server-connection-tcp-protocol.md)]

### <a name="configure-sql-server-for-mixed-mode-authentication"></a>Configurar o SQL Server para autenticação em modo misto
O Motor de Base de Dados do SQL Server não pode utilizar a Autenticação do Windows sem um ambiente de domínio. Para ligar ao Motor de Base de Dados a partir de outro computador, configure o SQL Server para autenticação em modo misto. A autenticação em modo misto permite a Autenticação do SQL Server e a Autenticação do Windows.

> [!NOTE]
> Configurar a autenticação em modo misto poderá não ser necessário se tiver configurado uma Rede Virtual do Azure com um ambiente de domínio configurado.
> 
> 

1. Enquanto estiver ligado à máquina virtual, na página Início, escreva **SQL Server Management Studio** e clique no ícone selecionado.
   
    Na primeira vez que abre o Management Studio, este tem de criar os utilizadores do ambiente do Management Studio. Esta operação poderá demorar alguns tempo.
2. O Management Studio apresenta a caixa de diálogo **Ligar ao Servidor**. Na caixa **Nome do servidor**, escreva o nome da máquina virtual a ligar ao Motor de Base de Dados com o Object Explorer (em vez do nome da máquina virtual, também pode utilizar **(local)** ou um caráter de ponto como **Nome do servidor**). **Selecione A autenticação do Windows**e deixe ***your_VM_name\your_local_administrator*** na caixa de **nomes do Utilizador.** Clique em **Ligar**.
   
    ![Ligar ao Servidor](./media/virtual-machines-sql-server-connection-steps/19Connect-to-Server.png)
3. No Object Explorer do SQL Server Management Studio, clique com o botão direito do rato no nome da instância do SQL Server (o nome de máquina virtual) e, em seguida, clique em **Propriedades**.
   
    ![Propriedades do Servidor](./media/virtual-machines-sql-server-connection-steps/20Server-Properties.png)
4. Na página **Segurança**, em **Autenticação do servidor**, selecione o **modo Autenticação do SQL Server e do Windows** e, em seguida, clique em **OK**.
   
    ![Selecionar Modo de Autenticação](./media/virtual-machines-sql-server-connection-steps/21Mixed-Mode.png)
5. Na caixa de diálogo do SQL Server Management Studio, clique em **OK** para reconhecer o requisito de reiniciar o SQL Server.
6. No Object Explorer, clique com o botão direito do rato no seu servidor e, em seguida, clique em **Reiniciar** (se o SQL Server Agent estiver em execução, também terá de o reiniciar).
   
    ![Reiniciar](./media/virtual-machines-sql-server-connection-steps/22Restart2.png)
7. Na caixa de diálogo do SQL Server Management Studio, clique em **Sim** para confirmar que quer reiniciar o SQL Server.

### <a name="create-sql-server-authentication-logins"></a>Criar inícios de sessão de autenticação do SQL Server
Para ligar ao Motor de Base de Dados a partir de outro computador, tem de criar pelo menos um início de sessão de autenticação do SQL Server.

1. No Object Explorer do SQL Server Management Studio, expanda a pasta da instância de servidor na qual quer criar o novo início de sessão.
2. Clique com o botão direito do rato na pasta **Segurança**, aponte para **Novo** e selecione **Início de sessão...**.
   
    ![Novo Início de Sessão](./media/virtual-machines-sql-server-connection-steps/23New-Login.png)
3. Na caixa de diálogo **Início de sessão - Novo**, na página **Geral**, introduza o nome do novo utilizador na caixa **Nome do início de sessão**.
4. Selecione **Autenticação do SQL Server**.
5. Na caixa **Palavra-passe**, introduza uma palavra-passe para o novo utilizador. Introduza essa palavra-passe novamente na caixa **Confirmar Palavra-passe**.
6. Selecione as opções de imposição de palavra-passe necessárias (**Impor política de palavras-passe**, **Impor expiração da palavra-passe** e **O utilizador tem de alterar a palavra-passe no próximo início de sessão**). Se este início de sessão for para sua utilização pessoal, não precisa de exigir a alteração da palavra-passe no próximo início de sessão.
7. Na lista **Base de dados predefinida**, selecione uma base de dados predefinida para o início de sessão. **mestre** é a predefinição desta opção. Se ainda não criou uma base de dados de utilizador, deixe esta opção definida como **mestre**.
   
    ![Propriedades do Início de Sessão](./media/virtual-machines-sql-server-connection-steps/24Test-Login.png)
8. Se este for o primeiro início de sessão que está a criar, poderá querer designar este início de sessão como administrador do SQL Server. Se for este o caso, na página **Funções de Servidor**, marque a opção **sysadmin**.
   
   > [!NOTE]
   > Os membros da função de servidor fixa sysadmin têm controlo total sobre o Motor de Base de Dados. Deve restringir cuidadosamente os membros desta função.
   > 
   > 
   
   ![sysadmin](./media/virtual-machines-sql-server-connection-steps/25sysadmin.png)
9. Clique em OK.

Para obter mais informações sobre os inícios de sessão do SQL Server, veja [Criar um Início de Sessão](https://msdn.microsoft.com/library/aa337562.aspx).

