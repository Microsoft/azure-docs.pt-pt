---
title: 'Sincronização Azure AD Connect: Alterar a conta de serviço ADSync | Microsoft Docs'
description: Este documento tópico descreve a chave de encriptação e como abandoná-la após a alteração da palavra-passe.
services: active-directory
keywords: Conta de serviço sincronizado Azure AD, senha
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/17/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8778e50dcb881647696c6e901bf1058b9d6ac43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720343"
---
# <a name="changing-the-adsync-service-account-password"></a>Alterar a senha de conta de serviço ADSync
Se alterar a palavra-passe da conta de serviço ADSync, o Serviço de Sincronização não poderá iniciar-se corretamente até ter abandonado a chave de encriptação e reiniciado a palavra-passe da conta de serviço ADSync. 

>[!IMPORTANT]
> Se utilizar o Connect com uma construção a partir de março de 2017 ou mais cedo, então não deverá redefinir a palavra-passe na conta de serviço, uma vez que o Windows destrói as chaves de encriptação por razões de segurança. Não é possível alterar a conta para qualquer outra conta sem reinstalar o Azure AD Connect. Se fizer o upgrade para uma construção a partir de abril de 2017 ou posterior, então é suportado para alterar a palavra-passe na conta de serviço, mas não pode alterar a conta utilizada. 

O Azure AD Connect, como parte dos Serviços de Sincronização, utiliza uma chave de encriptação para armazenar as palavras-passe da conta AD DS Connector e da conta de serviço ADSync.  Estas contas são encriptadas antes de serem armazenadas na base de dados. 

A chave de encriptação utilizada é protegida através da [Proteção de Dados do Windows (DPAPI)](/previous-versions/ms995355(v=msdn.10)). O DPAPI protege a chave de encriptação utilizando a **conta de serviço ADSync.** 

Se precisar de alterar a palavra-passe da conta de serviço, pode utilizar os procedimentos no [abandono da chave de encriptação da conta de serviço ADSync](#abandoning-the-adsync-service-account-encryption-key) para o conseguir.  Estes procedimentos também devem ser utilizados se precisar de abandonar a chave de encriptação por qualquer motivo.

## <a name="issues-that-arise-from-changing-the-password"></a>Problemas que surgem da alteração da palavra-passe
Há duas coisas que precisam de ser feitas quando muda a senha da conta de serviço.

Primeiro, tem de alterar a palavra-passe sob o Gestor de Controlo de Serviços do Windows.  Até que esta questão seja resolvida, verá os seguintes erros:


- Se tentar iniciar o Serviço de Sincronização no Gestor de Controlo de Serviços do Windows, recebe o erro "**O Windows não poderia iniciar o serviço Microsoft Azure AD Sync no Computador Local**". **Erro 1069: O serviço não começou devido a uma falha de início de saúde.**"
- No Windows Event Viewer, o registo de eventos do sistema contém um erro com **o ID do evento 7038** e a mensagem "**O serviço ADSync não foi capaz de iniciar sessão como com a palavra-passe atualmente configurada devido ao seguinte erro: O nome de utilizador ou palavra-passe está incorreto"**

Em segundo lugar, em condições específicas, se a palavra-passe for atualizada, o Serviço de Sincronização já não pode recuperar a chave de encriptação via DPAPI. Sem a chave de encriptação, o Serviço de Sincronização não pode desencriptar as palavras-passe necessárias para sincronizar de/para as instalações AD e AD AZure.
Verá erros como:

- Sob o Gestor de Controlo de Serviços do Windows, se tentar iniciar o Serviço de Sincronização e não conseguir recuperar a chave de encriptação, falha com erro "<strong>O Windows não poderia iniciar o Microsoft Azure AD Sync no Computador Local. Para mais informações, reveja o registo do Evento do Sistema. Se este for um serviço não Microsoft, contacte o fornecedor de serviços e consulte o código de erro específico do serviço -21451857952</strong>."
- No Windows Event Viewer, o registo do evento de aplicação contém um erro com **o ID do evento 6028** e a mensagem de erro *"A chave de encriptação do servidor não pode ser acedida."*

Para garantir que não recebe estes erros, siga os procedimentos de [abandono da chave de encriptação da conta de serviço ADSync](#abandoning-the-adsync-service-account-encryption-key) ao alterar a palavra-passe.
 
## <a name="abandoning-the-adsync-service-account-encryption-key"></a>Abandono da chave de encriptação da conta de serviço ADSync
>[!IMPORTANT]
>Os seguintes procedimentos aplicam-se apenas à construção Azure AD Connect 1.1.443.0 ou mais. Isto não pode ser usado para versões mais recentes do Azure AD Connect porque abandonar a chave de encriptação é manuseado pelo Azure AD ligar-se quando altera a palavra-passe da conta de serviço de sincronização de AD para que não sejam necessários os seguintes passos nas versões mais recentes.   

Utilize os seguintes procedimentos para abandonar a chave de encriptação.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>O que fazer se precisar abandonar a chave de encriptação

Se precisar de abandonar a chave de encriptação, utilize os seguintes procedimentos para o conseguir.

1. [Parar o Serviço de Sincronização](#stop-the-synchronization-service)

1. [Abandone a chave de encriptação existente](#abandon-the-existing-encryption-key)

2. [Fornecer a palavra-passe da conta AD DS Connector](#provide-the-password-of-the-ad-ds-connector-account)

3. [Reinitializar a palavra-passe da conta de serviço ADSync](#reinitialize-the-password-of-the-adsync-service-account)

4. [Iniciar o Serviço de Sincronização](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>Parar o Serviço de Sincronização
Primeiro pode parar o serviço no Gestor de Controlo de Serviços do Windows.  Certifique-se de que o serviço não está a funcionar quando tentar detê-lo.  Se for, espere até que termine e depois pare.


1. Aceda ao Gestor de Controlo de Serviços do Windows (START → Services).
2. Selecione **Microsoft Azure AD Sync** e clique em Stop.

#### <a name="abandon-the-existing-encryption-key"></a>Abandone a chave de encriptação existente
Abandone a chave de encriptação existente para que possa ser criada nova chave de encriptação:

1. Inscreva-se no seu Azure AD Connect Server como administrador.

2. Inicie uma nova sessão powerShell.

3. Navegue para a pasta: `'$env:ProgramFiles\Microsoft Azure AD Sync\bin\'`

4. Executar o comando: `./miiskmu.exe /a`

![Screenshot que mostra PowerShell depois de executar o comando.](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-connector-account"></a>Fornecer a palavra-passe da conta AD DS Connector
Como as palavras-passe existentes armazenadas dentro da base de dados já não podem ser desencriptadas, é necessário fornecer ao Serviço de Sincronização a palavra-passe da conta AD DS Connector. O Serviço de Sincronização encripta as palavras-passe utilizando a nova chave de encriptação:

1. Iniciar o Gestor de Serviços de Sincronização (START → Serviço de Sincronização).
</br>![Gestor de Serviço sincronizado](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. Vá ao **separador Conectores.**
3. Selecione o **Conector AD** que corresponde ao seu AD no local. Se tiver mais do que um conector AD, repita os seguintes passos para cada um deles.
4. Em **Ações**, selecione **Properties**.
5. No diálogo pop-up, selecione **Connect to Ative Directory Forest**:
6. Introduza a palavra-passe da conta DS AD na caixa de texto **password.** Se não sabe a sua palavra-passe, deve defini-la para um valor conhecido antes de realizar este passo.
7. Clique **em OK** para guardar a nova palavra-passe e feche o diálogo pop-up.
![Screenshot que mostra a página "Connect to Ative Directory Forest" na janela "Propriedades".](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-adsync-service-account"></a>Reinitializar a palavra-passe da conta de serviço ADSync
Não é possível fornecer diretamente a palavra-passe da conta de serviço Azure AD ao Serviço de Sincronização. Em vez disso, precisa de utilizar o **cmdlet Add-ADSyncAADServiceAccount** para reiniciar a conta de serviço Azure AD. O cmdlet reinicia a palavra-passe da conta e coloca-a à disposição do Serviço de Sincronização:

1. Inicie uma nova sessão PowerShell no servidor Azure AD Connect.
2. Executar cmdlet `Add-ADSyncAADServiceAccount` .
3. No diálogo pop-up, forneça as credenciais de administração Azure AD Global para o seu inquilino AZure AD.
![Azure AD Connect Sync Encryption Key Utility](./media/how-to-connect-sync-change-serviceacct-pass/key7.png)
4. Se for bem sucedido, verá o pedido de comando PowerShell.

#### <a name="start-the-synchronization-service"></a>Iniciar o Serviço de Sincronização
Agora que o Serviço de Sincronização tem acesso à chave de encriptação e a todas as palavras-passe de que necessita, pode reiniciar o serviço no Gestor de Controlo de Serviços do Windows:


1. Aceda ao Gestor de Controlo de Serviços do Windows (START → Services).
2. Selecione **Microsoft Azure AD Sync** e clique em Reiniciar.

## <a name="next-steps"></a>Passos seguintes
**Tópicos de visão geral**

* [Azure AD Connect sync: Entenda e personalize a sincronização](how-to-connect-sync-whatis.md)

* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
