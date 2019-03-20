---
title: 'Sincronização do Azure AD Connect:  Alterar a conta de serviço do Azure AD Connect Sync | Documentos da Microsoft'
description: Este documento de tópico descreve a chave de encriptação e como abandoná-lo Depois da palavra-passe é alterada.
services: active-directory
keywords: Conta de serviço de sincronização do Azure AD, a palavra-passe
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/31/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15d0d537a23e21eeda3b284e7ec706cde2b443e7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58014073"
---
# <a name="changing-the-azure-ad-connect-sync-service-account-password"></a>Alterar a senha de conta de serviço de sincronização do Azure AD Connect
Se alterar a senha de conta de serviço de sincronização do Azure AD Connect, o serviço de sincronização não será capaz de iniciar corretamente até ter abandonado a chave de encriptação e reinicializados a senha de conta de serviço de sincronização do Azure AD Connect. 

O Azure AD Connect, como parte dos serviços de sincronização utiliza uma chave de encriptação para armazenar as palavras-passe das contas de serviço do AD DS e AD do Azure.  Estas contas são encriptadas antes de serem armazenadas na base de dados. 

A chave de encriptação utilizada estiver protegida por [Windows Data Protection (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx). DPAPI protege a encriptação chaves utilizando o **palavra-passe da conta de serviço de sincronização do Azure AD Connect**. 

Se precisar de alterar a palavra-passe da conta de serviço pode utilizar os procedimentos [abandonar a chave de encriptação do Azure AD Connect Sync](#abandoning-the-azure-ad-connect-sync-encryption-key) para fazer isso.  Estes procedimentos também devem ser utilizados se precisar de abandonar a chave de encriptação por qualquer motivo.

## <a name="issues-that-arise-from-changing-the-password"></a>Problemas decorrentes de alterar a palavra-passe
Existem duas coisas que precisam ser feito quando alterar a palavra-passe da conta de serviço.

Em primeiro lugar, terá de alterar a palavra-passe sob o Gestor de controlo do serviço do Windows.  Até que este problema esteja resolvido, poderá ver seguintes erros:


- Se tentar iniciar o serviço de sincronização no Gestor de controlo de serviço do Windows, receberá o erro "**Windows não conseguiu iniciar o serviço do Microsoft Azure AD Sync no computador Local**". **Erro 1069: O serviço não foram iniciados devido a uma falha de início de sessão.** "
- No Visualizador de eventos do Windows, o registo de eventos do sistema contém um erro com **evento ID 7038** e a mensagem "**o serviço ADSync não foi possível iniciar sessão, tal como acontece com a palavra-passe atualmente configurada devido ao erro seguinte: O nome de utilizador ou palavra-passe está incorreta.** "

Em segundo lugar, em condições específicas, se a palavra-passe é atualizada, o serviço de sincronização pode já não obter a chave de encriptação por meio de DPAPI. Sem a chave de encriptação, o serviço de sincronização não é possível desencriptar as palavras-passe necessárias para sincronizar em locais AD e o Azure AD.
Verá erros, tais como:

- Em Gestor de controlo de serviço de Windows, se tentar iniciar o serviço de sincronização e ele não é possível obter a chave de encriptação, ele falha com o erro "<strong>Windows não conseguiu iniciar a sincronização do Microsoft Azure AD no computador Local. Para obter mais informações, consulte o registo de eventos do sistema. Se se tratar de um serviço de terceiros, contacte o fornecedor de serviço e consulte o código de erro específico do serviço-21451857952</strong>. "
- No Visualizador de eventos do Windows, o log de eventos do aplicativo contém um erro com **evento ID 6028** e a mensagem de erro *"não é possível aceder a chave de encriptação do servidor."*

Para garantir que não recebem esses erros, siga os procedimentos [abandonar a chave de encriptação do Azure AD Connect Sync](#abandoning-the-azure-ad-connect-sync-encryption-key) quando alterar a palavra-passe.
 
## <a name="abandoning-the-azure-ad-connect-sync-encryption-key"></a>Abandonar a chave de encriptação do Azure AD Connect Sync
>[!IMPORTANT]
>Os procedimentos seguintes aplicam-se apenas para o Azure AD Connect compilação 1.1.443.0 ou mais antigos.

Utilize os procedimentos seguintes para abandonar a chave de encriptação.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>O que fazer se precisar de abandonar a chave de encriptação

Se precisar de abandonar a chave de encriptação, utilize os procedimentos seguintes para conseguir isso.

1. [Parar o serviço de sincronização](#stop-the-synchronization-service)

1. [Abandonar a chave de encriptação existente](#abandon-the-existing-encryption-key)

2. [Forneça a palavra-passe da conta do AD DS](#provide-the-password-of-the-ad-ds-account)

3. [Reinicializar a palavra-passe da conta de sincronização do Azure AD](#reinitialize-the-password-of-the-azure-ad-sync-account)

4. [Iniciar o serviço de sincronização](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>Parar o serviço de sincronização
Em primeiro lugar pode parar o serviço no Gestor de controlo de serviço do Windows.  Certifique-se de que o serviço não está em execução durante a tentativa de pará-la.  Se for, aguarde que ele seja concluída e, em seguida, impedi-lo.


1. Aceda ao Gestor de controlo de serviço do Windows (início → serviços).
2. Selecione **Microsoft Azure AD Sync** e clique em parar.

#### <a name="abandon-the-existing-encryption-key"></a>Abandonar a chave de encriptação existente
Abandone a chave de encriptação existente para que essa nova chave de encriptação pode ser criado:

1. Inicie sessão no seu servidor de ligação do AD do Azure como administrador.

2. Inicie uma nova sessão do PowerShell.

3. Navegue até à pasta: `$env:Program Files\Microsoft Azure AD Sync\bin\`

4. Execute o comando: `./miiskmu.exe /a`

![Utilitário de chave de encriptação de sincronização do Azure AD Connect](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-account"></a>Forneça a palavra-passe da conta do AD DS
Como as palavras-passe existentes armazenadas no banco de dados já não podem ser desencriptadas, tem de fornecer o serviço de sincronização com a palavra-passe da conta do AD DS. O serviço de sincronização encripta as palavras-passe com a nova chave de encriptação:

1. Inicie o Synchronization Service Manager (serviço de sincronização do início →).
</br>![Gestor do serviço de sincronização](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. Vá para o **conectores** separador.
3. Selecione o **conector AD** que corresponde ao seu local AD. Se tiver mais de um conector do AD, repita os passos seguintes para cada um deles.
4. Sob **ações**, selecione **propriedades**.
5. Na caixa de diálogo pop-up, selecione **ligar à floresta do Active Directory**:
6. Introduza a palavra-passe da conta do AD DS no **palavra-passe** caixa de texto. Se não souber a palavra-passe, tem de defini-lo para um valor conhecido antes de executar este passo.
7. Clique em **OK** para guardar a nova palavra-passe e fechar a caixa de diálogo pop-up.
![Utilitário de chave de encriptação de sincronização do Azure AD Connect](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-azure-ad-sync-account"></a>Reinicializar a palavra-passe da conta de sincronização do Azure AD
Diretamente não é possível fornecer a palavra-passe da conta de serviço do Azure AD para o serviço de sincronização. Em vez disso, tem de utilizar o cmdlet **Add-ADSyncAADServiceAccount** reinicializar a conta de serviço do Azure AD. O cmdlet repõe a palavra-passe de conta e torna-o disponível para o serviço de sincronização:

1. Inicie uma nova sessão do PowerShell no servidor do Azure AD Connect.
2. Execute o cmdlet `Add-ADSyncAADServiceAccount`.
3. Na caixa de diálogo pop-up, forneça as credenciais de Administrador Global do Azure AD para o seu inquilino do Azure AD.
![Utilitário de chave de encriptação de sincronização do Azure AD Connect](./media/how-to-connect-sync-change-serviceacct-pass/key7.png)
4. Se tiver êxito, verá a linha de comandos do PowerShell.

#### <a name="start-the-synchronization-service"></a>Iniciar o serviço de sincronização
Agora que o serviço de sincronização tem acesso para a chave de encriptação e todas as palavras-passe que é necessário, pode reiniciar o serviço no Gestor de controlo de serviço do Windows:


1. Aceda ao Gestor de controlo de serviço do Windows (início → serviços).
2. Selecione **Microsoft Azure AD Sync** e clique em Restart.

## <a name="next-steps"></a>Passos Seguintes
**Tópicos de descrição geral**

* [Sincronização do Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)

* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
