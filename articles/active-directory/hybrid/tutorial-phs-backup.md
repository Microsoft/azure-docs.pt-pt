---
title: 'Tutorial:  Configurar o PHS como cópia de segurança para o AD FS no Azure AD Connect | Documentos da Microsoft'
description: Demonstra como ativar a sincronização de hash de palavra-passe como uma cópia de segurança e para o AD FS.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e5ad7badfa44a006fd7e71d3b0e42ee95ac698d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918996"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Tutorial:  Configurar o PHS como cópia de segurança para o AD FS no Azure AD Connect

O tutorial seguinte irá guiá-lo ao configurar a sincronização de hash de palavra-passe como uma cópia de segurança e a ativação pós-falha para o AD FS.  Este documento também demonstrarei como ativar a sincronização de hash de palavra-passe como o método de autenticação primária, se o AD FS tem falhou ou se tornar indisponível.

>[!NOTE] 
>Embora estes passos são normalmente executados durante a emergência ou situações de indisponibilidade, recomenda-se que estes passos de teste e verificar seus procedimentos antes de ocorrer uma falha.

>[!NOTE]
>No caso de não tem acesso ao servidor do Azure AD Connect ou o servidor não tem acesso à internet, pode contactar [Support da Microsoft](https://support.microsoft.com/en-us/contactus/) para ajudá-lo com as alterações no lado do Azure AD.

## <a name="prerequisites"></a>Pré-requisitos
Neste tutorial tem por base o [Tutorial: Federar um ambiente de floresta único do AD para a cloud](tutorial-federation.md) e é um por-requisitos antes de tentar este tutorial.  Se não tiver concluído este tutorial, faça-o antes de repetir os passos neste documento.

>[!IMPORTANT]
>Antes de mudar para o PHS deve criar uma cópia de segurança do seu ambiente do AD FS.  Isso pode ser feito com o [AD FS rápida restaurar ferramenta](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool#how-to-use-the-tool).

## <a name="enable-phs-in-azure-ad-connect"></a>Ativar o PHS no Azure AD Connect
É a primeira etapa, agora que temos um ambiente do Azure AD Connect que está a utilizar o Federação ativar a sincronização de hash de palavra-passe e permitir que o Azure AD Connect para sincronizar os hashes.

Faça o seguinte:

1.  Faça duplo clique no ícone do Azure AD Connect que foi criado no ambiente de trabalho
2.  Clique em **Configurar**.
3.  Na página tarefas adicionais, selecione **personalizar as opções de sincronização** e clique em **próxima**.
4.  Introduza o nome de utilizador e palavra-passe para o seu administrador global.  Esta conta foi criada [aqui](tutorial-federation.md#create-a-global-administrator-in-azure-ad) no tutorial anterior.
5.  Sobre o **ligar os seus diretórios** ecrã, clique em **próxima**.
6.  Sobre o **domínio e UO filtragem** ecrã, clique em **próxima**.
7.  Sobre o **funcionalidades opcionais** ecrã, verificação **sincronização de hash de palavra-passe** e clique em **seguinte**.
![Selecionar](media/tutorial-phs-backup/backup1.png)</br>
8.  Sobre o **pronto para configurar** ecrã clique **configurar**.
9.  Uma vez concluída a configuração, clique em **saída**.
10. Já está!  Isso é tudo.  Sincronização de hash de palavra-passe agora ocorrerá e pode ser utilizada como uma cópia de segurança se do AD FS ficar indisponível.

## <a name="switch-to-password-hash-synchronization"></a>Mude para a sincronização de hash de palavra-passe
Agora, mostraremos como mudar para a sincronização de hash de palavra-passe. Antes de começar, considere condições sob as quais deve efetuar o comutador. Não fazer a transição por motivos de temporários, como uma falha de rede, um problema mínimo do AD FS ou um problema que afeta um subconjunto dos seus utilizadores. Se optar por fazer a transição, porque a corrigir o problema irá demorar demasiado tempo, efetue o seguinte:

> [!IMPORTANT]
> Lembre-se de que irá demorar algum tempo para que a palavra-passe hashes para sincronizar com o Azure AD.  Isso significa que poderá demorar até 3 horas para que as sincronizações concluir e antes de iniciar a autenticação com os hashes de palavra-passe.

1. Faça duplo clique no ícone do Azure AD Connect que foi criado no ambiente de trabalho
2.  Clique em **Configurar**.
3.  Selecione **alterar utilizador inicie sessão** e clique em **próxima**.
![Alteração](media/tutorial-phs-backup/backup2.png)</br>
4.  Introduza o nome de utilizador e palavra-passe para o seu administrador global.  Esta conta foi criada [aqui](tutorial-federation.md#create-a-global-administrator-in-azure-ad) no tutorial anterior.
5.  Na **sessão do utilizador** ecrã, selecione **sincronização de Hash de palavra-passe** e coloque uma marca no **não converter contas de utilizador** caixa.  
6.  Deixe a predefinição **ativar o início de sessão único** selecionada e clique em **próxima**.
7.  Sobre o **ativar o início de sessão único** ecrã clique **seguinte**.
8.  Sobre o **pronto para configurar** ecrã, clique em **configurar**.
9.  Assim que a configuração estiver concluída, clique em **saída**.
10. Os utilizadores podem agora utilizar as palavras-passe para iniciar sessão no Azure e Azure services.

## <a name="test-signing-in-with-one-of-our-users"></a>Testar início de sessão com um dos nossos usuários

1. Navegue para [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Inicie sessão com uma conta de utilizador que foi criada no nosso novo inquilino.  Terá de iniciar sessão com o seguinte formato: (user@domain.onmicrosoft.com). Utilize a mesma palavra-passe que o utilizador utiliza para iniciar sessão no local.</br>
   ![Certifique-se](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="switch-back-to-federation"></a>Mude novamente para Federação
Agora, mostraremos como regressar à Federação.  Para tal, faça o seguinte:

1.  Faça duplo clique no ícone do Azure AD Connect que foi criado no ambiente de trabalho
2.  Clique em **Configurar**.
3.  Selecione **alterar utilizador inicie sessão** e clique em **próxima**.
4.  Introduza o nome de utilizador e palavra-passe para o seu administrador global.  Esta é a conta que foi criada [aqui](tutorial-federation.md#create-a-global-administrator-in-azure-ad) no tutorial anterior.
5.  Sobre o **sessão do utilizador** ecrã, selecione **federação com o AD FS** e clique em **seguinte**.  
6. Na página de credenciais de administrador de domínio, introduza o nome de utilizador de contoso\Administrator e a palavra-passe e clique em **seguinte.**
7. No ecrã de farm do AD FS, clique em **seguinte**.
8. Sobre o **domínio do Azure AD** ecrã, selecione o domínio na lista suspensa e clique em **próxima**.
9. Sobre o **pronto para configurar** ecrã, clique em **configurar**.
10. Assim que a configuração estiver concluída, clique em **seguinte**.
![Configurar](media/tutorial-phs-backup/backup4.png)</br>
11. Sobre o **verificar a conectividade de Federação** ecrã, clique em **Verifique se**.  Terá de configurar os registos DNS (adicionar registos a e AAAA) para que isso seja concluído com êxito.
![Certifique-se](media/tutorial-phs-backup/backup5.png)</br>
12. Clique em **saída**.

## <a name="reset-the-ad-fs-and-azure-trust"></a>Repor o AD FS e a confiança do Azure
Agora, é necessário repor a fidedignidade entre o AD FS e o Azure.

1.  Faça duplo clique no ícone do Azure AD Connect que foi criado no ambiente de trabalho
2.  Clique em **Configurar**.
3.  Selecione **gerir o Federação** e clique em **próxima**.
4.  Selecione **confiança de reposição do Azure AD** e clique em **próxima**.
![Reset](media/tutorial-phs-backup/backup6.png)</br>
5.  Sobre o **ligar para o Azure AD** ecrã introduza o nome de utilizador e palavra-passe para o seu administrador global.
6.  Sobre o **ligar ao AD FS** ecrã, introduza o nome de utilizador de contoso\Administrator e a palavra-passe e clique em **seguinte.**
7.  Sobre o **certificados** ecrã, clique em **próxima**.

## <a name="test-signing-in-with-one-of-our-users"></a>Testar início de sessão com um dos nossos usuários

1.  Navegue para [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Inicie sessão com uma conta de utilizador que foi criada no nosso novo inquilino.  Terá de início de sessão com o seguinte formato: (user@domain.onmicrosoft.com). Utilizar a mesma palavra-passe que o utilizador utiliza para iniciar sessão no local.
![Certifique-se](media/tutorial-password-hash-sync/verify1.png)

Agora tem com êxito um ambiente de identidade híbrida que pode utilizar para testar e familiarizar-se com o Azure tem para oferecer a configuração.

## <a name="next-steps"></a>Passos Seguintes


- [Hardware e pré-requisitos](how-to-connect-install-prerequisites.md) 
- [Definições rápidas](how-to-connect-install-express.md)
- [Sincronização de hash de palavra-passe](how-to-connect-password-hash-synchronization.md)
