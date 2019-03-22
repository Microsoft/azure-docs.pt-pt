---
title: Autenticação de pass-through do Active Directory privacidade do utilizador e do Azure | Documentos da Microsoft
description: Este artigo trata da conformidade de autenticação de pass-through do Azure Active Directory (Azure AD) e o GDPR.
services: active-directory
keywords: Autenticação do Azure AD Connect pass-through, com o GDPR, necessário componentes para o Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1a7b740a6b248a12fa3d95f85f602ef7a8b2fa5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58098064"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Autenticação de pass-through do Active Directory do Azure e de privacidade do utilizador


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Descrição geral

A autenticação pass-through do AD do Azure cria o tipo de registo seguinte, que pode conter dados pessoais:

- Ficheiros de registo de rastreio do Azure AD Connect.
- Ficheiros de registo de rastreio de agente de autenticação.
- Ficheiros de registo de eventos do Windows.

Melhore a privacidade dos utilizadores para a autenticação pass-through de duas formas:

1.  Mediante solicitação, extrair dados para uma pessoa e remover dados de que a pessoa das instalações.
2.  Certifique-se de que nenhum dado é mantido para além de 48 horas.

Recomendamos vivamente a segunda opção, pois é mais fácil de implementar e manter. Seguem-se as instruções para cada tipo de registo:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Eliminar ficheiros de registo de rastreio do Azure AD Connect

Verifique o conteúdo do **%ProgramData%\AADConnect** conteúdo de registo de pasta e eliminar o rastreio (**rastreio -\*. log** ficheiros) dessa pasta dentro de 48 horas de instalar ou atualizar o Azure AD Connect ou modificar a configuração de autenticação pass-through, esta ação pode criar dados cobertos pelo GDPR.

>[!IMPORTANT]
>Não elimine o **PersistedState.xml** ficheiros nesta pasta, como este ficheiro é utilizado para manter o estado da instalação anterior do Azure AD Connect e é utilizado quando uma instalação de atualização é feita. Este ficheiro nunca irá conter todos os dados sobre uma pessoa e nunca deve ser eliminado.

Pode rever e eliminar estes ficheiros de registo de rastreio com o Explorador do Windows ou pode utilizar o seguinte script do PowerShell para efetuar as ações necessárias:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Guarde o script num arquivo com o ". PS1 "extensão. Execute este script conforme necessário.

Para saber mais sobre relacionados com os requisitos do Azure AD Connect com o GDPR, consulte [este artigo](reference-connect-user-privacy.md).

### <a name="delete-authentication-agent-event-logs"></a>Eliminar registos de eventos do agente de autenticação

Também pode criar este produto **registos de eventos do Windows**. Para obter mais informações, leia [este artigo](https://msdn.microsoft.com/library/windows/desktop/aa385780(v=vs.85).aspx).

Para ver registos relacionados com o agente de autenticação pass-through, abra a **Visualizador de eventos** aplicação do servidor e a verificação em **aplicação e serviço Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin** .

### <a name="delete-authentication-agent-trace-log-files"></a>Eliminar ficheiros de registo de rastreio do agente de autenticação

Deve verificar regularmente o conteúdo do <strong>%ProgramData%\Microsoft\Azure AD ligar Agent\Trace de autenticação\</ strong > e elimine o conteúdo desta pasta cada 48 horas. 

>[!IMPORTANT]
>Se estiver a executar o serviço de agente de autenticação, não poderá eliminar o ficheiro de registo atual na pasta. Pare o serviço antes de tentar novamente. Para evitar falhas de início de sessão de utilizador, deve ter configurado a autenticação pass-through para [elevada disponibilidade](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

Pode rever e eliminar estes ficheiros ao utilizar o Explorador do Windows ou pode utilizar o seguinte script para executar as ações necessárias:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

Para agendar este script para executar cada 48 horas, siga estes passos:

1.  Guarde o script num arquivo com o ". PS1 "extensão.
2.  Open **painel de controlo** e clique em **sistema e segurança**.
3.  Sob o **ferramentas administrativas** cabeçalho, clique em "**agendamento de tarefas**".
4.  Na **agendador de tarefas**, clique com o botão direito em "**biblioteca de agendamento de tarefas**"e clique em"**criar tarefa básica...** ".
5.  Introduza o nome para a nova tarefa e clique em **seguinte**.
6.  Selecione "**diária**" para o **acionador de tarefa** e clique em **seguinte**.
7.  Defina a periodicidade para dois dias e clique em **seguinte**.
8.  Selecione "**iniciar um programa**" como a ação e clique em **próxima**.
9.  Tipo de "**PowerShell**"na caixa para o programa/script e na caixa de com o nome"**Adicione argumentos (opcional)**", introduza o caminho completo para o script que criou anteriormente, em seguida, clique em **próxima**.
10. O ecrã seguinte mostra um resumo da tarefa que está prestes a criar. Verifique os valores e clique em **concluir** para criar a tarefa:
 
### <a name="note-about-domain-controller-logs"></a>Tenha em atenção sobre os registos de controlador de domínio

Se o registo de auditoria é ativado, este produto pode gerar registos de segurança para os controladores de domínio. Para saber mais sobre como configurar políticas de auditoria, leia isto [artigo](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Passos Seguintes
* [Rever a política de Privacy da Microsoft no Centro de fidedignidade](https://www.microsoft.com/trustcenter)
* [**Resolução de problemas** ](tshoot-connect-pass-through-authentication.md) -Saiba como resolver problemas comuns com a funcionalidade.
