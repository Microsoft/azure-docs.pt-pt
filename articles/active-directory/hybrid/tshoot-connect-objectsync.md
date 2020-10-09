---
title: 'Azure AD Connect: Sincronização de objetos de resolução de problemas Microsoft Docs'
description: Este tópico fornece passos para como resolver problemas com a sincronização de objetos usando a tarefa de resolução de problemas.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73d4239dd34f2a64aa7b3edbf88bad4348e01291
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85356207"
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Troubleshoot object synchronization with Azure AD Connect sync (Resolver problemas de sincronização de objetos com a sincronização do Azure AD Connect)
Este artigo fornece passos para resolver problemas com a sincronização de objetos utilizando a tarefa de resolução de problemas. Para ver como funciona a resolução de problemas no Azure Ative Directory (Azure AD) Connect, veja [este pequeno vídeo](https://aka.ms/AADCTSVideo).

## <a name="troubleshooting-task"></a>Tarefa de resolução de problemas
Para a implementação do Azure AD Connect com a versão 1.1.749.0 ou superior, utilize a tarefa de resolução de problemas no assistente para resolver os problemas de sincronização de objetos. Para versões anteriores, por favor, a resolução de problemas seja manualmente como descrito [aqui](tshoot-connect-object-not-syncing.md).

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>Executar a tarefa de resolução de problemas no assistente
Para executar a tarefa de resolução de problemas no assistente, execute os seguintes passos:

1.  Abra uma nova sessão Windows PowerShell no seu servidor Azure AD Connect com a opção Executar como Administrador.
2.  Corra `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted` . .
3.  Inicie o assistente Azure AD Connect.
4.  Navegue na página Tarefas Adicionais, selecione Troubleshoot e clique em Seguinte.
5.  Na página troubleshooting, clique em Lançamento para iniciar o menu de resolução de problemas em PowerShell.
6.  No menu principal, selecione Troubleshoot Object Synchronization.
![Sincronização de objetos de resolução de problemas](media/tshoot-connect-objectsync/objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>Parâmetros de entrada de resolução de problemas
Os seguintes parâmetros de entrada são necessários pela tarefa de resolução de problemas:
1.  **Nome Distinto de Objeto** – Este é o nome distinto do objeto que precisa de resolução de problemas
2.  Nome do **Conector AD** – Este é o nome da floresta AD onde reside o objeto acima.
3.  Azure AD inquilino global administrador credencia ![ credenciais de administrador global](media/tshoot-connect-objectsync/objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Compreender os resultados da tarefa de resolução de problemas
A tarefa de resolução de problemas executa as seguintes verificações:

1.  Detete incompatibilidade da UPN se o objeto estiver sincronizado com o Azure Ative Directory
2.  Verifique se o objeto é filtrado devido à filtragem do domínio
3.  Verifique se o objeto é filtrado devido à filtragem da U
4.  Verifique se a sincronização de objetos está bloqueada devido a uma caixa de correio ligada
5. Verifique se o objeto é um grupo de distribuição dinâmico que não deve ser sincronizado

O resto desta secção descreve resultados específicos que são devolvidos pela tarefa. Em cada caso, a tarefa fornece uma análise seguida de ações recomendadas para resolver o problema.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Detete a incompatibilidade da UPN se o objeto estiver sincronizado com o Azure Ative Directory
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>UPN Sufixo NÃO é verificado com Azure AD Inquilino
Quando o nome de utilizadorPrincipalName (UPN)/Sufixo de ID de login alternativo não é verificado com o Inquilino AZURE AD, então o Azure Ative Directory substitui os sufixos UPN pelo nome de domínio predefinido "onmicrosoft.com".

![Azure AD substitui UPN](media/tshoot-connect-objectsync/objsynch2.png)

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Azure AD Inquilino DirSync Recurso 'SynchronizeUpnForManagedUsers' está desativado
Quando o Azure AD Tenant DirSync Feature 'SynchronizeUpnForManagedUsers' é desativado, o Azure Ative Directory não permite atualizações de sincronização ao UserPrincipalName/Alternate Login ID para contas de utilizador licenciadas com autenticação gerida.

![SynchronizeUpnForManagedUsers](media/tshoot-connect-objectsync/objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>O objeto é filtrado devido à filtragem do domínio
### <a name="domain-is-not-configured-to-sync"></a>O domínio não está configurado para sincronizar
O objeto está fora de alcance devido ao domínio não estar configurado. No exemplo abaixo, o objeto está fora do âmbito de sincronização, uma vez que o domínio a que pertence é filtrado da sincronização.

![O domínio não está configurado para sincronizar](media/tshoot-connect-objectsync/objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>O domínio está configurado para sincronizar, mas falta perfis de execução/passos de execução
O objeto está fora de alcance à medida que o domínio falta perfis de execução/passos de execução. No exemplo abaixo, o objeto está fora do âmbito de sincronização, uma vez que o domínio a que pertence está a perder passos de execução para o perfil de execução de Importação Completa.
![perfis de execução em falta](media/tshoot-connect-objectsync/objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>O objeto é filtrado devido à filtragem da U
O objeto está fora do âmbito de sincronização devido à configuração de filtragem da U. No exemplo abaixo, o objeto pertence a OU=NoSync,DC=bvtadwbackdc,DC=com.  Esta UO não está incluída no âmbito de sincronização.</br>

![OU](./media/tshoot-connect-objectsync/objsynch7.png)

## <a name="linked-mailbox-issue"></a>Emissão de caixa de correio ligada
Uma caixa de correio ligada deve estar associada a uma conta principal externa localizada em outra floresta de conta fidedigna. Se não existir tal conta principal externa, então o Azure AD Connect não sincronizará a conta de utilizador corresponde à caixa de correio ligada na floresta de câmbio ao inquilino AD AZure.</br>
![Caixa de correio ligada](./media/tshoot-connect-objectsync/objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>Emissão do Grupo de Distribuição Dinâmica
Devido a várias diferenças entre o Ative Directory e o Azure Ative Directory, o Azure AD Connect não sincroniza grupos de distribuição dinâmicos para o inquilino AD AZure.

![Grupo de Distribuição Dinâmica](./media/tshoot-connect-objectsync/objsynch13.png)

## <a name="html-report"></a>Relatório HTML
Além de analisar o objeto, a tarefa de resolução de problemas também gera um relatório HTML que tem tudo o que se sabe sobre o objeto. Este relatório HTML pode ser partilhado com a equipa de apoio para fazer mais resolução de problemas, se necessário.

![Relatório HTML](media/tshoot-connect-objectsync/objsynch8.png)

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
