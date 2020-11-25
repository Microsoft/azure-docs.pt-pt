---
title: Privacidade do Utilizador e Autenticação Pass-through do Diretório Ativo Azure Microsoft Docs
description: Este artigo trata da autenticação pass-through do Azure Ative (Azure AD) e da conformidade do RGPD.
services: active-directory
keywords: Azure AD Connect Authentication Pass-through, RGPD, componentes necessários para Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 396344ba90aa3850d7d23dc40d6df95f6d1f6c3f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996581"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Privacidade do Utilizador e Autenticação Pass-through do Azure Active Directory


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Descrição geral

A autenticação Ad AD Ad cria o seguinte tipo de registo, que pode conter Dados Pessoais:

- Azure AD Connect ficheiros de registo de vestígios.
- Ficheiros de registo de registo de registo de registo de agentes de autenticação.
- Ficheiros de registo do Windows Event.

Melhorar a privacidade do utilizador para a autenticação pass-through de duas formas:

1.  A pedido, extrair dados para uma pessoa e remover dados dessa pessoa das instalações.
2.  Certifique-se de que nenhum dado é retido para além de 48 horas.

Recomendamos vivamente a segunda opção, uma vez que é mais fácil de implementar e manter. Seguem-se as instruções para cada tipo de registo:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Eliminar ficheiros de registo de registo de rastreio Azure AD Connect

Verifique o conteúdo da pasta **%ProgramData%\AADConnect** e elimine o conteúdo do registo de vestígios **(ficheiros \* trace-.log)** desta pasta no prazo de 48 horas após a instalação ou atualização do Azure AD Connect ou modificar a configuração de autenticação Pass-through, uma vez que esta ação pode criar dados cobertos pelo RGPD.

>[!IMPORTANT]
>Não elimine o ficheiro **PersistedState.xml** nesta pasta, uma vez que este ficheiro é utilizado para manter o estado da instalação anterior do Azure AD Connect e é utilizado quando uma instalação de upgrade é feita. Este ficheiro nunca conterá quaisquer dados sobre uma pessoa e nunca deverá ser eliminado.

Pode rever e eliminar estes ficheiros de registo de vestígios utilizando o Windows Explorer ou pode utilizar o seguinte script PowerShell para executar as ações necessárias:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Guarde o guião num ficheiro com o ". Extensão PS1. Executar este guião conforme necessário.

Para saber mais sobre os requisitos relacionados com o RGPD Azure AD, consulte [este artigo](reference-connect-user-privacy.md).

### <a name="delete-authentication-agent-event-logs"></a>Apagar registos de eventos do Agente de Autenticação

Este produto também pode criar **Registos de Eventos do Windows.** Para saber mais, leia [este artigo.](/windows/win32/wes/windows-event-log)

Para visualizar registos relacionados com o Agente de Autenticação Pass-through, abra a aplicação **do Observador de Eventos** no servidor e verifique em **Registos de Aplicação e Serviço\Microsoft\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

### <a name="delete-authentication-agent-trace-log-files"></a>Eliminar ficheiros de registo de registo de registo de registo de agente de autenticação

Deve verificar regularmente o conteúdo de **%ProgramData%\Microsoft\Microsoft\Azure AD Connect Authentication Agent\Trace** e eliminar o conteúdo desta pasta a cada 48 horas. 

>[!IMPORTANT]
>Se o serviço Agente de Autenticação estiver em execução, não poderá eliminar o ficheiro de registo atual na pasta. Pare o serviço antes de tentar novamente. Para evitar falhas de inscrição no utilizador, já deve ter configurado a Autenticação Pass-through para [uma elevada disponibilidade](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

Pode rever e eliminar estes ficheiros utilizando o Windows Explorer ou pode utilizar o seguinte script para executar as ações necessárias:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

Para agendar este roteiro para correr a cada 48 horas siga estes passos:

1.  Guarde o guião num ficheiro com o ". Extensão PS1.
2.  Abra **o Painel de Controlo** e clique no Sistema e **Segurança.**
3.  No título **Ferramentas Administrativas,** clique em "**Agendar Tarefas**".
4.  No **Task Scheduler**, clique à direita na "**Task Schedule Library**" e clique em " Criar tarefa **Básica...**".
5.  Introduza o nome para a nova tarefa e clique em **Seguinte**.
6.  Selecione "**Daily**" para o **Gatilho de Tarefa** e clique em **Seguinte**.
7.  Desaprote a recorrência para dois dias e clique **em Seguinte**.
8.  Selecione "**Iniciar um programa**" como ação e clique em **Seguinte**.
9.  Tipo "**PowerShell**" na caixa para o Programa/script, e na caixa rotulada "**Adicionar argumentos (opcional)**", insira o caminho completo para o script que criou anteriormente e, em seguida, clique em **Seguinte**.
10. O ecrã seguinte mostra um resumo da tarefa que está prestes a criar. Verifique os valores e clique **em Terminar** para criar a tarefa:
 
### <a name="note-about-domain-controller-logs"></a>Nota sobre registos de controlador de domínio

Se o registo de auditoria estiver ativado, este produto poderá gerar registos de segurança para os seus Controladores de Domínio. Para saber mais sobre a configuração das políticas de auditoria, leia este [artigo.](/previous-versions/tn-archive/dd277403(v=technet.10))

## <a name="next-steps"></a>Passos seguintes
* [Reveja a política de privacidade da Microsoft no Trust Center](https://www.microsoft.com/trustcenter)
* [**Resolução de problemas**](tshoot-connect-pass-through-authentication.md) - Aprenda a resolver problemas comuns com a funcionalidade.