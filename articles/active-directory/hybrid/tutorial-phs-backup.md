---
title: 'Tutorial: Configurar o PHS como backup para AD FS em Azure AD Connect | Microsoft Docs'
description: Demonstra como ligar a sincronização de haxixe de palavra-passe como uma cópia de segurança e para o AD FS.
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
ms.openlocfilehash: 3d9f59906c566d80344891c0796a85b0a4972e68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91313098"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Tutorial: Configuração phs como backup para AD FS em Azure AD Connect

O seguinte tutorial irá acompanhá-lo através da configuração da sincronização de haxixe de palavra-passe como uma cópia de segurança e desafinação para AD FS.  Este documento também demonstrará como ativar a sincronização de haxixe de palavra-passe como o método de autenticação primária, se a AD FS falhou ou se ficou indisponível.

>[!NOTE] 
>Embora estes passos sejam geralmente realizados em situações de emergência ou de paragem, é aconselhável testar estes passos e verificar os seus procedimentos antes de ocorrer uma paragem.

>[!NOTE]
>No caso de não ter acesso ao servidor AZURE AD Connect ou se o servidor não tiver acesso à internet, pode contactar o [Microsoft Support](https://support.microsoft.com/en-us/contactus/) para ajudar nas alterações ao lado Azure AD.

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial baseia-se no [Tutorial: Federa um único ambiente florestal de AD para a nuvem](tutorial-federation.md) e é um per-requisito antes de tentar este tutorial.  Se não tiver concluído este tutorial, faça-o antes de tentar os passos deste documento.

>[!IMPORTANT]
>Antes de mudar para PHS deve criar uma cópia de segurança do seu ambiente AD FS.  Isto pode ser feito utilizando a [Ferramenta de Restauro Rápido AD FS](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool#how-to-use-the-tool).

## <a name="enable-phs-in-azure-ad-connect"></a>Ativar PHS em Azure AD Connect
O primeiro passo, agora que temos um ambiente Azure AD Connect que está a usar a federação, é ligar a sincronização de haxixe de palavra-passe e permitir que o Azure AD Connect sincronize os haxixes.

Faça o seguinte:

1.  Clique duas vezes no ícone Azure AD Connect que foi criado no ambiente de trabalho
2.  Clique em **Configurar**.
3.  Na página de tarefas adicionais, **selecione Personalize as opções de sincronização** e clique em **Seguinte**.
4.  Introduza o nome de utilizador e a palavra-passe para o seu administrador global.  Esta conta foi criada [aqui](tutorial-federation.md#create-a-global-administrator-in-azure-ad) no tutorial anterior.
5.  No ecrã **'Ligar os directórios',** clique em **'Seguinte'.**
6.  No ecrã **de filtragem Domain e OU,** clique em **Seguinte**.
7.  No ecrã **de funcionalidades opcionais,** verifique **a sincronização do hash da palavra-passe** e clique em **Seguinte**.
![Selecionar](media/tutorial-phs-backup/backup1.png)</br>
8.  No ecrã **pronto a configurar** clique em **Configurar**.
9.  Assim que a configuração estiver concluída, clique em **Sair**.
10. Já está!  Acabou-se.  A sincronização de hash de palavra-passe ocorrerá agora e pode ser usada como uma cópia de segurança se o FS AD ficar indisponível.

## <a name="switch-to-password-hash-synchronization"></a>Mude para a sincronização de haxixe de palavra-passe
Agora, vamos mostrar-lhe como mudar para a sincronização de hash password. Antes de começar, considere em que condições deve escame a troca. Não faça a troca por razões temporárias, como uma falha de rede, um pequeno problema de AD FS, ou um problema que afeta um subconjunto dos seus utilizadores. Se decidir fazer a troca porque a correção do problema demorará muito tempo, faça o seguinte:

> [!IMPORTANT]
> Esteja ciente de que levará algum tempo para que a palavra-passe tenha sincronizado com a Azure AD.  Isto significa que pode demorar 3 horas para que as sincronizações se concretizem e antes de começar a autenticar usando as hashes de palavra-passe.

1. Clique duas vezes no ícone Azure AD Connect que foi criado no ambiente de trabalho
2.  Clique em **Configurar**.
3.  Selecione **Alterar o pedido de s indicador** e clicar em **Seguinte**.
![Alteração](media/tutorial-phs-backup/backup2.png)</br>
4.  Introduza o nome de utilizador e a palavra-passe para o seu administrador global.  Esta conta foi criada [aqui](tutorial-federation.md#create-a-global-administrator-in-azure-ad) no tutorial anterior.
5.  No ecrã **de entrada de dados** do Utilizador, selecione a **Sincronização de Password Hash** e faça uma verificação na caixa **de contas do utilizador não convertida.**  
6.  Deixar o predefinido **Ativar o único sinal** de inscrição selecionado e clicar em **Seguinte**.
7.  No ecrã **de inscrição única ativar** clique em **Seguinte**.
8.  No ecrã **Pronto para configurar,** clique em **Configurar**.
9.  Uma vez concluída a configuração, clique em **Sair**.
10. Os utilizadores podem agora utilizar as suas palavras-passe para iniciar seduca nos serviços Azure e Azure.

## <a name="test-signing-in-with-one-of-our-users"></a>Iniciar sessão de teste com um dos nossos utilizadores

1. Navegue para [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Inscreva-se com uma conta de utilizador que foi criada no nosso novo inquilino.  Terá de iniciar sção utilizando o seguinte formato: user@domain.onmicrosoft.com (). Utilize a mesma palavra-passe que o utilizador utiliza para iniciar sins insição no local.</br>
   ![Screenshot que mostra uma mensagem bem sucedida ao testar a placa dentro ](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="switch-back-to-federation"></a>Voltar para a federação
Agora, vamos mostrar-lhe como voltar para a federação.  Para isso, faça o seguinte:

1.  Clique duas vezes no ícone Azure AD Connect que foi criado no ambiente de trabalho
2.  Clique em **Configurar**.
3.  Selecione **Alterar o pedido de s indicador** e clicar em **Seguinte**.
4.  Introduza o nome de utilizador e a palavra-passe para o seu administrador global.  Esta é a conta que foi criada [aqui](tutorial-federation.md#create-a-global-administrator-in-azure-ad) no tutorial anterior.
5.  No ecrã **de inscrição** do Utilizador, selecione **Federação com FS AD** e clique em **Seguinte**.  
6. Na página de credenciais do Administrador de Domínio, insira o nome de utilizador e palavra-passe do administrador contoso\Administrador e clique em **Seguinte.**
7. No ecrã da quinta AD FS, clique em **Seguinte**.
8. No ecrã de **domínio AZure AD,** selecione o domínio a partir do drop-down e clique em **Seguinte**.
9. No ecrã **Pronto para configurar,** clique em **Configurar**.
10. Uma vez concluída a configuração, clique em **Seguinte**.
![Configurar](media/tutorial-phs-backup/backup4.png)</br>
11. No ecrã de conectividade da **federação,** clique em **Verificar .**  Pode ser necessário configurar registos DNS (adicionar registos A e AAAA) para que este seja concluído com sucesso.
![Screenshot que mostra o ecrã de conectividade da federação verificar e o botão Verificar.](media/tutorial-phs-backup/backup5.png)</br>
12. Clique **em Saída**.

## <a name="reset-the-ad-fs-and-azure-trust"></a>Repor a confiança AD FS e Azure
Agora precisamos repor a confiança entre a AD FS e o Azure.

1.  Clique duas vezes no ícone Azure AD Connect que foi criado no ambiente de trabalho
2.  Clique em **Configurar**.
3.  **Selecione Gerir Federação** e clique em **Seguinte**.
4.  Selecione **Reset Azure AD trust** e clique em **Seguinte**.
![Repor](media/tutorial-phs-backup/backup6.png)</br>
5.  No ecrã **AD Do Ligar ao Azure** introduza o nome de utilizador e a palavra-passe para o seu administrador global.
6.  No ecrã **Connect to AD FS,** insira o nome de utilizador e palavra-passe do administrador contoso\Administrador e clique em **Seguinte.**
7.  No ecrã **certificados,** clique em **Seguinte**.

## <a name="test-signing-in-with-a-user"></a>Iniciar sessão de teste com um utilizador

1.  Navegue para [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Iniciar sôms com uma conta de utilizador que foi criada no nosso novo inquilino.  Terá de iniciar s-in utilizando o seguinte formato: user@domain.onmicrosoft.com (). Utilize a mesma palavra-passe que o utilizador utiliza para iniciar sômposições no local.
![Verificação](media/tutorial-password-hash-sync/verify1.png)

Você agora criou com sucesso um ambiente de identidade híbrida que você pode usar para testar e familiarizar-se com o que Azure tem para oferecer.

## <a name="next-steps"></a>Passos seguintes


- [Hardware e pré-requisitos](how-to-connect-install-prerequisites.md) 
- [Definições expressas](how-to-connect-install-express.md)
- [Sincronização de hash de palavra-passe](how-to-connect-password-hash-synchronization.md)