---
title: Instalar o Azure AD Connect com a base de dados ADSync existente | Documentos da Microsoft
description: Este tópico descreve como utilizar a base de dados ADSync existente.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/30/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dc6993586063c9c99a287c51d799b44f921768d
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286096"
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Instalar o Azure AD Connect com a base de dados ADSync existente
Azure AD Connect requer uma base de dados do SQL Server para armazenar dados. Pode utilizar o predefinido do que SQL Server 2012 Express LocalDB instalado com o Azure AD Connect ou utilizar a sua própria versão completa do SQL. Anteriormente, quando instalou o Azure AD Connect, uma nova base de dados com o nome ADSync sempre foi criado. Com o Azure AD Connect versão 1.1.613.0 (ou depois), tem a opção de instalação do Azure AD Connect ao apontá-la para uma base de dados ADSync existente.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Vantagens da utilização de uma base de dados ADSync existente
Ao apontar para uma base de dados ADSync existente:

- Exceto para informações de credenciais, configuração de sincronização armazenada na base de dados ADSync (incluindo as regras de sincronização personalizados, conectores, filtragem e configuração de funcionalidades opcionais) é automaticamente recuperada e usada durante a instalação . As credenciais utilizadas pelo Azure AD Connect para sincronizar as alterações no local com o AD e o Azure AD são encriptados e só pode ser acedido pelo servidor do Azure AD Connect anterior.
- Todos os dados de identidade (associados às metaverso e espaços conectores) e os cookies de sincronização armazenados na base de dados ADSync também são recuperados. O acabou de instalar o Azure AD Connect server pode continuar a sincronizar a partir de onde o servidor do Azure AD Connect anterior parou, em vez de ter a necessidade de executar uma sincronização completa.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Cenários onde é vantajoso utilizar uma base de dados ADSync existente
Esses benefícios são úteis nos seguintes cenários:


- Tem uma implementação existente do Azure AD Connect. O servidor do Azure AD Connect existente já não está a funcionar, mas o SQL server que contém a base de dados ADSync ainda estiver a funcionar. Pode instalar um novo servidor do Azure AD Connect e apontá-lo para a base de dados ADSync existente. 
- Tem uma implementação existente do Azure AD Connect. O SQL server que contém a base de dados ADSync já não está a funcionar. No entanto, tem um recente cópia de segurança da base de dados. Pode restaurar a base de dados ADSync primeiro para um novo servidor SQL. Depois disso, pode instalar um novo servidor do Azure AD Connect e apontá-lo para a base de dados restaurada do ADSync.
- Tem uma implementação existente do Azure AD Connect que está a utilizar o LocalDB. Devido ao limite de 10 GB imposto pelo LocalDB, que pretende migrar para o SQL completo. Pode criar cópias de segurança da base de dados ADSync da LocalDB e restaurá-lo para um SQL server. Depois disso, pode reinstalar um novo servidor do Azure AD Connect e apontá-lo para a base de dados restaurada do ADSync.
- Está a tentar configurar um servidor de teste e quer certificar-se de que corresponde a respetiva configuração do servidor do Active Directory atual. Pode criar cópias de segurança da base de dados ADSync e restaurá-lo para outro SQL server. Depois disso, pode reinstalar um novo servidor do Azure AD Connect e apontá-lo para a base de dados restaurada do ADSync.

## <a name="prerequisite-information"></a>Informações de pré-requisitos

Notas importantes para tirar tenha em atenção de antes de continuar:

- Certifique-se rever os pré-requisitos para instalar o Azure AD Connect no Hardware e a pré-requisitos e a conta e as permissões necessárias para instalar o Azure AD Connect. As permissões necessárias para instalar o Azure AD Connect com o modo de "utilizar a base de dados existente" é o mesmo que a instalação "personalizada".
- Implementar o Azure AD Connect em relação a um ADSync existente base de dados só é suportado com SQL completo. Não é suportada com o SQL Express LocalDB. Se tiver uma base de dados ADSync existente no LocalDB que deseja usar, tem primeiro de criar cópias de segurança da base de dados do ADSync (LocalDB) e restaurá-lo para o SQL completo. Depois disso, pode implementar o Azure AD Connect no banco de dados restaurado através deste método.
- A versão do Azure AD Connect utilizada para a instalação tem de cumprir os seguintes critérios:
    - 1.1.613.0 ou superior, e
    - Igual ou superior à versão do Azure AD Connect utilizado pela última vez com a base de dados do ADSync. Se a versão do Azure AD Connect utilizada para a instalação for superior à versão utilizada pela última vez com a base de dados ADSync, em seguida, uma sincronização completa poderá ser necessária.  Sincronização completa é necessária se existirem alterações de regra de esquema ou de sincronização entre as duas versões. 
- A base de dados ADSync utilizado deve conter um Estado de sincronização que é relativamente recente. A última atividade de sincronização com a base de dados ADSync existente deve ser dentro de três últimas semanas.
- Ao instalar o Azure AD Connect com o método "utilizar a base de dados existente", o método de início de sessão configurado no servidor do Azure AD Connect anterior não é preservado. Além disso, não é possível configurar o método de início de sessão durante a instalação. Só pode configurar o método de início de sessão após a conclusão da instalação.
- Não pode ter vários servidores do Azure AD Connect a partilhar o mesmo banco de dados do ADSync. O método "utilizar a base de dados existente" permite-lhe reutilizar a base de dados ADSync existente com um novo servidor do Azure AD Connect. Não suporta a partilha.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>Passos para instalar o Azure AD Connect com o modo de "utilizar a base de dados existente"
1.  Transferir o instalador do Azure AD Connect (Azureadconnect) para o servidor do Windows. Clique duas vezes o instalador do Azure AD Connect para iniciar a instalação do Azure AD Connect.
2.  Assim que a instalação do MSI estiver concluída, o assistente do Azure AD Connect é iniciado com a configuração do modo Express. Feche o ecrã ao clicar no ícone Sair.
![Bem-vindo](./media/how-to-connect-install-existing-database/db1.png)
3.  Inicie uma nova linha de comandos ou a sessão do PowerShell. Navegue até à pasta "C:\Program Files\Microsoft Azure Active Directory Connect". Execute o comando .\AzureADConnect.exe /useexistingdatabase para iniciar o assistente do Azure AD Connect no modo de configuração "Utilizar base de dados existente".

> [!NOTE]
> Utilizar o comutador **/UseExistingDatabase** apenas quando a base de dados já contém dados de uma instalação anterior do Azure AD Connect. Por exemplo, quando estiver movendo de uma base de dados local para uma base de dados completa do SQL Server ou quando o servidor do Azure AD Connect foi reconstruído e restaurar uma cópia de segurança SQL da base de dados ADSync de uma instalação anterior do Azure AD Connect. Se a base de dados está vazia, ou seja, ele não contém dados de uma instalação anterior do Azure AD Connect, ignore este passo.

![PowerShell](./media/how-to-connect-install-existing-database/db2.png)
1. É recebido com o ecrã de Boas-vindas ao Azure AD Connect. Depois de aceitar os termos de licenciamento e o aviso de privacidade, clique em **Continuar**.
   ![Bem-vindo](./media/how-to-connect-install-existing-database/db3.png)
1. No ecrã **Instalar componentes necessários**, a opção **Utilizar um servidor existente do SQL Server** está ativada. Especifique o nome do servidor SQL que está a alojar a base de dados ADSync. Se a instância do motor SQL utilizada para alojar a base de dados ADSync não for a instância predefinida no servidor SQL, tem de especificar o nome da instância do motor SQL. Além disso, se a navegação do SQL Server não estiver ativada, também tem de especificar o número de porta da instância do motor SQL. Por exemplo:         
   ![Bem-vindo](./media/how-to-connect-install-existing-database/db4.png)           

1. No ecrã **Ligar ao Azure AD**, tem de apresentar as credenciais de um administrador global do seu diretório do Azure AD. A recomendação é utilizar uma conta no domínio predefinido onmicrosoft.com. Esta conta é utilizada apenas para criar uma conta de serviço no Azure AD e deixa de ser utilizada uma vez concluído o assistente.
   ![Ligar](./media/how-to-connect-install-existing-database/db5.png)
 
1. No ecrã **Ligar aos diretórios**, a floresta do AD existente configurada para a sincronização de diretórios está listada com um ícone vermelho junto à mesma. Para sincronizar alterações de uma floresta do AD local, precisa de uma conta do AD DS. O assistente do Azure AD Connect não consegue obter as credenciais da conta do AD DS armazenadas na base de dados do ADSync porque as credenciais estão encriptadas e só podem ser desencriptadas pelo servidor do Azure AD Connect anterior. Clique em **Alterar Credenciais** para especificar a conta do AD DS para a floresta do AD.
   ![Diretórios](./media/how-to-connect-install-existing-database/db6.png)
 
 
1. Na caixa de diálogo pop-up, pode (i) apresentar uma credencial de Administrador Enterprise e permitir ao Azure AD Connect criar a conta do AD DS para si ou (ii) criar a conta do AD DS manualmente e dar a respetiva credencial ao Azure AD Connect. Assim que tiver selecionado uma opção e apresentado as credenciais exigidas, clique em **OK** para fechar a caixa de diálogo pop-up.
   ![Bem-vindo](./media/how-to-connect-install-existing-database/db7.png)
 
 
1. Depois de apresentar as credenciais, o ícone com uma cruz vermelha é substituído por um ícone com um visto verde. Clique em **Seguinte**.
   ![Bem-vindo](./media/how-to-connect-install-existing-database/db8.png)
 
 
1. No ecrã **Preparado para configurar**, clique em **Instalar**.
   ![Bem-vindo](./media/how-to-connect-install-existing-database/db9.png)
 
 
1. Assim que a instalação estiver concluída, o servidor do Azure AD Connect é ativado automaticamente para o Modo de Teste. É recomendado que reveja se existem alterações inesperadas na configuração do servidor e nas exportações pendentes antes de desativar o Modo de Teste. 

## <a name="post-installation-tasks"></a>Tarefas de pós-instalação
Ao restaurar uma cópia de segurança da base de dados criada por uma versão do Azure AD Connect anterior 1.2.65.0, o servidor de preparação selecionará automaticamente um método de início de sessão do **não Configure**. Embora a sincronização de hash de palavra-passe e as preferências de repetição de escrita de palavra-passe serão restauradas, em seguida, tem de alterar o método de início de sessão de acordo com as outras políticas em vigor para o servidor de sincronização do Active Directory.  Falha ao concluir estes passos pode impedir os utilizadores se inscrevam no deve este servidor é ativada.  

Utilize a tabela abaixo para verificar quaisquer passos adicionais que são necessários.

|Funcionalidade|Passos|
|-----|-----|
|Sincronização de Hash de Palavra-passe| as definições de repetição de escrita de palavra-passe e sincronização de Hash de palavra-passe são totalmente restauradas para versões do Azure AD Connect a partir do 1.2.65.0.  Se restaurar com uma versão mais antiga do Azure AD Connect, reveja as definições da opção de sincronização para estas funcionalidades para se certificar de que corresponde ao seu servidor de sincronização do Active Directory.  Não existem outros passos de configuração devem ser necessários.|
|Federação com o AD FS|Autenticações do Azure irão continuar a utilizar a política do AD FS configurada para o seu servidor de sincronização do Active Directory.  Se utilizar o Azure AD Connect para gerir o farm do AD FS, opcionalmente, pode alterar o método de início de sessão para a Federação do AD FS em preparação para o servidor em espera, tornando-se a instância de sincronização do Active Directory.   Se as opções de dispositivo estão ativadas no servidor de sincronização do Active Directory, configure essas opções neste servidor ao executar a tarefa "Configurar opções de dispositivo".|
|Autenticação pass-through e ambiente de trabalho Single Sign-On|Atualize o método de início de sessão para corresponderem à configuração no seu servidor de sincronização do Active Directory.  Se não for seguida antes de promover o servidor para autenticação primária, pass-through, juntamente com o início de sessão único totalmente integrado no será desativada e o seu inquilino pode ser bloqueado se não tiver sincronização de hash de palavra-passe como cópia de segurança de sessão de opção. Tenha também em atenção que ao ativar a autenticação pass-through no modo de teste, um novo agente de autenticação será instalado, registado e será executado como um agente de elevada disponibilidade que irá aceitar pedidos de início de sessão.|
|Federação com o PingFederate|Autenticações do Azure irão continuar a utilizar a política de PingFederate configurada para o seu servidor de sincronização do Active Directory.  Opcionalmente, pode alterar o método de início de sessão para PingFederate em preparação para o servidor em espera, tornando-se a instância de sincronização do Active Directory.  Este passo pode ser adiado até que é necessário federar domínios adicionais com PingFederate.|

## <a name="next-steps"></a>Passos Seguintes

- Agora que já tem o Azure AD Connect instalado, pode [verificar a instalação e atribuir licenças](how-to-connect-post-installation.md).
- Saiba mais acerca destas funcionalidades que foram ativadas com a instalação: [Impedir eliminações acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md) e [do Azure AD Connect Health](how-to-connect-health-sync.md).
- Saiba mais acerca destes tópicos comuns: [agendador e como acionar a sincronização](how-to-connect-sync-feature-scheduler.md).
- Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
