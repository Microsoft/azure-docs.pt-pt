---
title: Mova a base de dados do Azure AD Connect do SQL Server Express para o SQL Server. | Microsoft Docs
description: Este documento descreve como mover a base de dados do Azure AD Connect do servidor SQL Server Express local para um SQL Server remoto.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeeb9989b6bf0233354d493f5dbb6916c806cfdb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076841"
---
# <a name="move-azure-ad-connect-database-from-sql-server-express-to-sql-server"></a>Mova a base de dados do Azure AD Connect do SQL Server Express para o SQL Server 

Este documento descreve como mover a base de dados do Azure AD Connect do servidor SQL Server Express local para um SQL Server remoto.  Pode utilizar os seguintes procedimentos abaixo para realizar esta tarefa.

## <a name="about-this-scenario"></a>Acerca deste cenário
Seguem-se algumas informações breves sobre este cenário.  Neste cenário, a versão do Azure AD Connect (1.1.819.0) está instalada num controlador de domínio do Windows Server 2016 único.  Está a utilizar o SQL Server 2012 Express Edition incorporado na respetiva base de dados.  A base de dados será movida para um servidor do SQL Server de 2017.

![](media/how-to-connect-install-move-db/move1.png)

## <a name="move-the-azure-ad-connect-database"></a>Mova a base de dados do Azure AD Connect
Utilize os seguintes passos para mover a base de dados do Azure AD Connect para um SQL Server remoto.

1. No servidor do Azure AD Connect, aceda a **Serviços** e pare o serviço **Microsoft Azure AD Sync**.
2. Localize a **% Program Files%\Microsoft do Azure AD Sync/dados/** pastas e copia o **ADSync.mdf** e **ADSync_log.ldf** ficheiros para o SQL Server remoto.
3. Reinicie o serviço **Microsoft Azure AD Sync** no servidor do Azure AD Connect.
4. Desinstale o Azure AD Connect ao aceder ao Painel de Controlo - Programas - Programas e Funcionalidades.  Selecione o Microsoft Azure AD Connect e clique em desinstalar na parte superior.
5. No SQL server remoto, abra o SQL Server Management Studio.
6. Nas Bases de dados, clique com o botão direito do rato e selecione Anexar.
7. No ecrã **Anexar Bases de Dados**, clique em **Adicionar** e navegue para o ficheiro ADSync.mdf.  Clique em **OK**.
   ![](media/how-to-connect-install-move-db/move2.png)

8. Depois de anexar a base de dados, volte para o servidor do Azure AD Connect e instale o Azure AD Connect.
9. Assim que a instalação do MSI estiver concluída, o assistente do Azure AD Connect é iniciado com a configuração do modo Express. Feche o ecrã ao clicar no ícone Sair.
   ![Bem-vindo](./media/how-to-connect-install-move-db/db1.png)
10. Inicie uma nova linha de comandos ou a sessão do PowerShell. Navegue até à pasta <drive>\program files\Microsoft Azure AD Connect. Execute o comando .\AzureADConnect.exe /useexistingdatabase para iniciar o assistente do Azure AD Connect no modo de configuração "Utilizar base de dados existente".
    ![PowerShell](./media/how-to-connect-install-move-db/db2.png)
11. É recebido com o ecrã de Boas-vindas ao Azure AD Connect. Depois de aceitar os termos de licenciamento e o aviso de privacidade, clique em **Continuar**.
    ![Bem-vindo](./media/how-to-connect-install-move-db/db3.png)
12. No ecrã **Instalar componentes necessários**, a opção **Utilizar um servidor existente do SQL Server** está ativada. Especifique o nome do servidor SQL que está a alojar a base de dados ADSync. Se a instância do motor SQL utilizada para alojar a base de dados ADSync não for a instância predefinida no servidor SQL, tem de especificar o nome da instância do motor SQL. Além disso, se a navegação do SQL Server não estiver ativada, também tem de especificar o número de porta da instância do motor SQL. Por exemplo:         
    ![Bem-vindo](./media/how-to-connect-install-move-db/db4.png)           

13. No ecrã **Ligar ao Azure AD**, tem de apresentar as credenciais de um administrador global do seu diretório do Azure AD. A recomendação é utilizar uma conta no domínio predefinido onmicrosoft.com. Esta conta é utilizada apenas para criar uma conta de serviço no Azure AD e deixa de ser utilizada uma vez concluído o assistente.
    ![Ligar](./media/how-to-connect-install-move-db/db5.png)
 
14. No ecrã **Ligar aos diretórios**, a floresta do AD existente configurada para a sincronização de diretórios está listada com um ícone vermelho junto à mesma. Para sincronizar alterações de uma floresta do AD local, precisa de uma conta do AD DS. O assistente do Azure AD Connect não consegue obter as credenciais da conta do AD DS armazenadas na base de dados do ADSync porque as credenciais estão encriptadas e só podem ser desencriptadas pelo servidor do Azure AD Connect anterior. Clique em **Alterar Credenciais** para especificar a conta do AD DS para a floresta do AD.
    ![Diretórios](./media/how-to-connect-install-move-db/db6.png)
 
 
15. Na caixa de diálogo pop-up, pode (i) apresentar uma credencial de Administrador Enterprise e permitir ao Azure AD Connect criar a conta do AD DS para si ou (ii) criar a conta do AD DS manualmente e dar a respetiva credencial ao Azure AD Connect. Assim que tiver selecionado uma opção e apresentado as credenciais exigidas, clique em **OK** para fechar a caixa de diálogo pop-up.
    ![Bem-vindo](./media/how-to-connect-install-move-db/db7.png)
 
 
16. Depois de apresentar as credenciais, o ícone com uma cruz vermelha é substituído por um ícone com um visto verde. Clique em **Seguinte**.
    ![Bem-vindo](./media/how-to-connect-install-move-db/db8.png)
 
 
17. No ecrã **Preparado para configurar**, clique em **Instalar**.
    ![Bem-vindo](./media/how-to-connect-install-move-db/db9.png)
 
 
18. Assim que a instalação estiver concluída, o servidor do Azure AD Connect é ativado automaticamente para o Modo de Teste. É recomendado que reveja se existem alterações inesperadas na configuração do servidor e nas exportações pendentes antes de desativar o Modo de Teste. 

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
- [Instalar o Azure AD Connect com uma base de dados ADSync existente](how-to-connect-install-existing-database.md)
- [Instalar o Azure AD Connect com permissões de administrador do SQL delegado](how-to-connect-install-sql-delegation.md)

