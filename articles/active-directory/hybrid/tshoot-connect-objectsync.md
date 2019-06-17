---
title: 'Azure AD Connect: Resolver problemas de sincronização de objetos | Documentos da Microsoft'
description: Este tópico fornece os passos sobre como resolver problemas com a sincronização de objeto utilizando a tarefa de resolução de problemas.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e56d4d94e38e5095ef2223d0cc2875cbf1dcd46
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64919124"
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Resolver problemas de sincronização de objetos com o Azure AD Connect sync
Este artigo fornece passos de resolução de problemas com sincronização de objetos, utilizando a tarefa de resolução de problemas. Para ver a resolução de problemas de funcionamento no Azure Active Directory (Azure AD) Connect, assista [este breve vídeo](https://aka.ms/AADCTSVideo).

## <a name="troubleshooting-task"></a>Tarefas de resolução de problemas
Para o Azure AD Connect implementação com a versão 1.1.749.0 ou superior, utilize a tarefa de resolução de problemas no Assistente para resolver problemas de sincronização de objeto. Para versões anteriores, tente resolver manualmente, conforme descrito [aqui](tshoot-connect-object-not-syncing.md).

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>Executar a tarefa de resolução de problemas do Assistente
Para executar a tarefa de resolução de problemas do assistente, execute os seguintes passos:

1.  Abra uma nova sessão do Windows PowerShell no seu servidor do Azure AD Connect com a execução como opção de administrador.
2.  Execute `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted`.
3.  Inicie o Assistente do Azure AD Connect.
4.  Navegue para a página de tarefas adicionais, selecione a resolução de problemas e clique em seguinte.
5.  Na página de resolução de problemas, clique em Iniciar para iniciar o menu de resolução de problemas no PowerShell.
6.  No menu principal, selecione a resolver problemas de sincronização de objetos.
![Resolver problemas de sincronização de objetos](media/tshoot-connect-objectsync/objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>Parâmetros de entrada de resolução de problemas
Os seguintes parâmetros de entrada necessários para a tarefa de resolução de problemas:
1.  **Nome distinto do objeto** – este é o nome distinto do objeto que tem de resolução de problemas
2.  **Nome do conector AD** – este é o nome da floresta do AD onde reside o objeto acima.
3.  Credenciais de administrador global de inquilino do Azure AD ![credenciais de administrador global](media/tshoot-connect-objectsync/objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Compreender os resultados da tarefa de resolução de problemas
A tarefa de resolução de problemas realiza as seguintes verificações:

1.  Erro de correspondência de UPN de detectar se o objeto está sincronizado com o Azure Active Directory
2.  Verifique se o objeto é filtrado devido a filtragem de domínio
3.  Verifique se o objeto é filtrado devido à filtragem de UO
4.  Verifique se a sincronização de objetos está bloqueada devido a uma caixa de correio ligada
5. Verifique se o objeto está grupo dinâmico de distribuição que não deve ser sincronizado

O resto desta secção descreve os resultados específicos que são devolvidos pela tarefa. Em cada caso, a tarefa fornece uma análise seguida as ações recomendadas para resolver o problema.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Erro de correspondência de UPN de detectar se o objeto está sincronizado com o Azure Active Directory
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>Sufixo do UPN não for verificado com o inquilino do Azure AD
Quando UserPrincipalName (UPN) / sufixo do ID de início de sessão alternativo não é verificado com o inquilino do Azure AD, em seguida, Azure Active Directory substitui os sufixos do UPN com o nome de domínio predefinido "onmicrosoft.com".

![Substitui o UPN do Azure AD](media/tshoot-connect-objectsync/objsynch2.png)

### <a name="changing-upn-suffix-from-one-federated-domain-to-another-federated-domain"></a>Alterar o sufixo de UPN de um domínio federado para outro domínio federado
O Azure Active Directory não permite a sincronização de UserPrincipalName (UPN) / Federado de alteração de sufixo do ID de início de sessão alternativo de um domínio federado para outro domínio. Isto aplica-se a domínios, que são confrontados com o inquilino do Azure AD e tem o tipo de autenticação como federados.

![Sem sincronização UPN de um domínio federado para outro](media/tshoot-connect-objectsync/objsynch3.png) 

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>O Azure AD inquilino DirSync funcionalidade 'SynchronizeUpnForManagedUsers' está desativada
Quando a funcionalidade de DirSync de inquilino do Azure AD 'SynchronizeUpnForManagedUsers' está desativada, o Azure Active Directory não permite atualizações de sincronização a ID de início de sessão de UserPrincipalName/alternativa para contas de utilizador licenciado com a autenticação gerida.

![SynchronizeUpnForManagedUsers](media/tshoot-connect-objectsync/objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>Objeto é filtrado devido a filtragem de domínio
### <a name="domain-is-not-configured-to-sync"></a>Domínio não está configurado para sincronização
Objeto está fora do âmbito devido a domínio não configurado. No exemplo abaixo, o objeto é âmbito fora de sincronização como o domínio a que pertence a é filtrado de sincronização.

![Domínio não está configurado para sincronização](media/tshoot-connect-objectsync/objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>Domínio está configurado para sincronizar, mas está em falta passos de perfis de execução/executado
Objeto está fora do escopo como o domínio está em falta executar passos de perfis/executado. No exemplo abaixo, o objeto é o âmbito de sincronização, como o domínio a que pertence a falta de passos de execução para o perfil de execução de importação completa.
![perfis de execução em falta](media/tshoot-connect-objectsync/objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>Objeto é filtrado devido à filtragem de UO
O objeto é o âmbito de sincronização devido à configuração de filtragem de UO. No exemplo abaixo, o objeto pertence à UO = NoSync, DC = bvtadwbackdc, DC = com.  Essa UO não está incluído no âmbito de sincronização.</br>

![UO](./media/tshoot-connect-objectsync/objsynch7.png)

## <a name="linked-mailbox-issue"></a>Problema de caixa de correio ligado
Uma caixa de correio vinculada deve para ser associada a uma conta de principal externa localizada noutra floresta de conta confiável. Se não existe nenhum desse conta principal externa, o Azure AD Connect não irá sincronizar o utilizador conta corresponde à caixa de correio ligada na floresta do Exchange para o inquilino do Azure AD.</br>
![Caixa de correio ligada](./media/tshoot-connect-objectsync/objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>Problema de grupo dinâmico de distribuição
Devido a várias diferenças entre locais do Active Directory e Azure Active Directory, Azure AD Connect não sincroniza grupos dinâmicos de distribuição para o inquilino do Azure AD.

![Grupo de distribuição dinâmico](./media/tshoot-connect-objectsync/objsynch13.png)

## <a name="html-report"></a>Relatório de HTML
Além de analisar o objeto, a tarefa de resolução de problemas também gera um relatório em HTML que tem tudo sobre o objeto. Este relatório em HTML pode ser partilhado com a equipa de suporte para fazer ainda mais a resolver problemas, se necessário.

![Relatório de HTML](media/tshoot-connect-objectsync/objsynch8.png)

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
