---
title: 'Tutorial: Configuração de PHS como backup para AD FS em Azure AD Connect [ Microsoft Docs'
description: Demonstra como ligar a sincronização de hash de palavra-passe como cópia de segurança e para AD FS.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "64918996"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Tutorial: Configuração de PHS como cópia de segurança para AD FS em Azure AD Connect

O seguinte tutorial irá acompanhá-lo através da configuração da sincronização de hash password como uma cópia de segurança e fail-over para AD FS.  Este documento também demonstrará como ativar a sincronização de hash de palavra-passe como o método de autenticação primária, se a AD FS falhou ou se tornou indisponível.

>[!NOTE] 
>Embora estes passos sejam geralmente realizados em situações de emergência ou paragem, recomenda-se que teste estes passos e verifique os seus procedimentos antes de ocorrer uma paragem.

>[!NOTE]
>Caso não tenha acesso ao servidor Azure AD Connect ou o servidor não tenha acesso à internet, pode contactar o [Microsoft Support](https://support.microsoft.com/en-us/contactus/) para ajudar nas alterações do lado da AD Azure.

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial baseia-se no [Tutorial: Federar um único ambiente florestal ad para a nuvem](tutorial-federation.md) e é um per-requisite antes de tentar este tutorial.  Se ainda não tiver concluído este tutorial, faça-o antes de tentar os passos deste documento.

>[!IMPORTANT]
>Antes de mudar para PHS deve criar uma cópia de segurança do seu ambiente AD FS.  Isto pode ser feito utilizando a [ferramenta de restauro rápido AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool#how-to-use-the-tool).

## <a name="enable-phs-in-azure-ad-connect"></a>Ativar PHS em Azure AD Connect
O primeiro passo, agora que temos um ambiente Azure AD Connect que está a usar a federação, é ligar a sincronização de hash de senha e permitir que o Azure AD Connect sincronize os hashes.

Faça o seguinte:

1.  Clique duas vezes no ícone Azure AD Connect que foi criado no ambiente de trabalho
2.  Clique em **Configurar**.
3.  Na página de tarefas Adicionais, selecione **Personalizar opções** de sincronização e clicar **em Seguinte**.
4.  Introduza o nome de utilizador e a palavra-passe para o seu administrador global.  Esta conta foi criada [aqui](tutorial-federation.md#create-a-global-administrator-in-azure-ad) no tutorial anterior.
5.  No ecrã **Connect your directdirecties,** clique em **Next**.
6.  No ecrã de **filtragem Domain e U,** clique em **Next**.
7.  No ecrã **de funcionalidades Opcionais,** verifique a sincronização do **hash password** e clique **em Next**.
![Selecionar](media/tutorial-phs-backup/backup1.png)</br>
8.  No **pronto para configurar o** ecrã clique **em Configurar**.
9.  Assim que a configuração estiver concluída, clique em **Sair**.
10. Já está!  Acabou-se.  A sincronização de hash de palavra-passe irá agora ocorrer e pode ser usada como cópia de segurança se a AD FS ficar indisponível.

## <a name="switch-to-password-hash-synchronization"></a>Mude para sincronização de hash de senha
Agora, vamos mostrar-lhe como mudar para a sincronização de hash de senha. Antes de começar, considere em que condições deve fazer a troca. Não faça a troca por razões temporárias, como uma falha de rede, um pequeno problema de AD FS, ou um problema que afeta um subconjunto dos seus utilizadores. Se decidir fazer a troca porque corrigir o problema demorará muito tempo, faça o seguinte:

> [!IMPORTANT]
> Esteja ciente de que levará algum tempo para que as hashes de senha sincronizem para a AD Azure.  Isto significa que pode demorar 3 horas para as sincronizações completarem e antes de começar a autenticar usando as hashes de senha.

1. Clique duas vezes no ícone Azure AD Connect que foi criado no ambiente de trabalho
2.  Clique em **Configurar**.
3.  Selecione Alterar o **sessão do utilizador** e clique **em Next**.
![Alterar](media/tutorial-phs-backup/backup2.png)</br>
4.  Introduza o nome de utilizador e a palavra-passe para o seu administrador global.  Esta conta foi criada [aqui](tutorial-federation.md#create-a-global-administrator-in-azure-ad) no tutorial anterior.
5.  No ecrã de entrada do **Utilizador,** selecione **Password Hash Synchronization** e faça um check-in na caixa de contas do **utilizador Não converter.**  
6.  Deixe o predefinido **Ativar um único sinal** selecionado e clicar **em Seguinte**.
7.  No ecrã **de ativação individual** clique em **Seguinte**.
8.  No **ecrã Ready to configure,** clique em **Configurar**.
9.  Assim que a configuração estiver completa, clique em **Sair**.
10. Os utilizadores podem agora utilizar as suas palavras-passe para iniciar sessão nos serviços Azure e Azure.

## <a name="test-signing-in-with-one-of-our-users"></a>Teste de sessão com um dos nossos utilizadores

1. Navegue para[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Inscreva-se numa conta de utilizador que foi criada no nosso novo inquilino.  Terá de iniciar sessão utilizando ouser@domain.onmicrosoft.comseguinte formato: ( ). Utilize a mesma palavra-passe que o utilizador usa para iniciar sessão no local.</br>
   ![Verificar](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="switch-back-to-federation"></a>Mude de volta para a federação
Vamos mostrar-lhe como voltar para a federação.  Para isso, faça o seguinte:

1.  Clique duas vezes no ícone Azure AD Connect que foi criado no ambiente de trabalho
2.  Clique em **Configurar**.
3.  Selecione Alterar o **sessão do utilizador** e clique **em Next**.
4.  Introduza o nome de utilizador e a palavra-passe para o seu administrador global.  Esta é a conta que foi criada [aqui](tutorial-federation.md#create-a-global-administrator-in-azure-ad) no tutorial anterior.
5.  No ecrã **de iniciar sessão** do Utilizador, selecione **Federação com AD FS** e clique **em Seguinte**.  
6. Na página de credenciais do Administrador de Domínio, introduza o nome de utilizador e senha do administrador de contos e clique em **Seguinte.**
7. No ecrã da quinta AD FS, clique em **Next**.
8. No ecrã de **domínio Azure AD,** selecione o domínio a partir do drop-down e clique **em Next**.
9. No **ecrã Ready to configure,** clique em **Configurar**.
10. Assim que a configuração estiver completa, clique em **Seguinte**.
![Configurar](media/tutorial-phs-backup/backup4.png)</br>
11. No ecrã de **conectividade da federação verificar,** clique em **Verificar**.  Pode ser necessário configurar os registos DNS (adicione registos A e AAAA) para que isto esteja concluído com sucesso.
![Verificar](media/tutorial-phs-backup/backup5.png)</br>
12. Clique em **Saída**.

## <a name="reset-the-ad-fs-and-azure-trust"></a>Redefinir a confiança da AD FS e azure
Agora temos de redefinir a confiança entre a AD FS e o Azure.

1.  Clique duas vezes no ícone Azure AD Connect que foi criado no ambiente de trabalho
2.  Clique em **Configurar**.
3.  Selecione Gerir a **Federação** e clique **em Seguinte**.
4.  Selecione **Reset Azure AD trust** e clique **em Next**.
![Repor](media/tutorial-phs-backup/backup6.png)</br>
5.  No ecrã **Connect to Azure AD** introduza o nome de utilizador e a palavra-passe para o seu administrador global.
6.  No ecrã **Connect to AD FS,** introduza o nome de utilizador e senha do administrador de contose clique em **Seguinte.**
7.  No ecrã dos **Certificados,** clique em **Seguinte**.

## <a name="test-signing-in-with-one-of-our-users"></a>Teste de sessão com um dos nossos utilizadores

1.  Navegue para[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Inscreva-se com uma conta de utilizador que foi criada no nosso novo inquilino.  Terá de iniciar sessão utilizando ouser@domain.onmicrosoft.comseguinte formato: ( ). Utilize a mesma palavra-passe que o utilizador utiliza para iniciar sessão no local.
![Verificar](media/tutorial-password-hash-sync/verify1.png)

Agora criou com sucesso um ambiente de identidade híbrida que pode usar para testar e familiarizar-se com o que o Azure tem para oferecer.

## <a name="next-steps"></a>Passos seguintes


- [Hardware e pré-requisitos](how-to-connect-install-prerequisites.md) 
- [Configurações expressas](how-to-connect-install-express.md)
- [Sincronização de hash de palavra-passe](how-to-connect-password-hash-synchronization.md)
