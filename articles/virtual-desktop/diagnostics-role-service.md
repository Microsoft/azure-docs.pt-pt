---
title: Problemas de diagnóstico de desktop virtual do Windows - Azure
description: Como utilizar a funcionalidade de diagnóstico virtual do Windows Desktop para diagnosticar problemas.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 70676bd1a07acdfcbba071a906b390ed66d70074
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91279863"
---
# <a name="identify-and-diagnose-windows-virtual-desktop-issues"></a>Identificar e diagnosticar problemas de ambiente de trabalho virtual do Windows

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md).

O Windows Virtual Desktop oferece uma funcionalidade de diagnóstico que permite ao administrador identificar problemas através de uma única interface. Para saber mais sobre as capacidades de diagnóstico do Windows Virtual Desktop, consulte [Use Log Analytics para a funcionalidade de diagnóstico](diagnostics-log-analytics.md).

As ligações que não chegam ao Windows Virtual Desktop não aparecem nos resultados dos diagnósticos porque o próprio serviço de função de diagnóstico faz parte do Windows Virtual Desktop. Problemas de ligação virtual ao ambiente de trabalho do Windows podem ocorrer quando o utilizador final está a ter problemas de conectividade de rede.

## <a name="common-error-scenarios"></a>Cenários de erros comuns

A tabela WVDErrors rastreia erros em todos os tipos de atividade. A coluna chamada "ServiceError" fornece uma bandeira adicional marcada como "Verdadeiro" ou "Falso". Esta bandeira dir-lhe-á se o erro está relacionado com o serviço.

* Se o valor for "Verdadeiro", a equipa de serviço pode já ter investigado esta questão. Se isto tiver impacto na experiência do utilizador e aparecer um elevado número de vezes, recomendamos que envie um bilhete de suporte para o Windows Virtual Desktop.
* Se o valor for "Falso", isto pode ser uma configuração errada que podes corrigir. A mensagem de erro pode dar-lhe uma pista sobre por onde começar.

A tabela que se segue enumera erros comuns que os seus administradores podem encontrar.

>[!NOTE]
>Esta lista inclui a maioria dos erros comuns e é atualizada numa cadência regular. Para garantir que tem as informações mais atualizadas, certifique-se de voltar a verificar este artigo pelo menos uma vez por mês.

## <a name="management-errors"></a>Erros de gestão

|Mensagem de erro|Solução sugerida|
|---|---|
|Falhou na criação da chave de registo |O símbolo de inscrição não pôde ser criado. Tente criá-lo novamente com um prazo de validade mais curto (entre 1 hora e 1 mês). |
|Falha na eliminação da chave de registo|O símbolo do registo não podia ser apagado. Tente apagá-lo de novo. Se ainda não funcionar, use o PowerShell para verificar se o token ainda lá está. Se estiver lá, apague-o com o PowerShell.|
|Falhou em alterar o modo de drenagem do anfitrião da sessão |Não podia mudar o modo de drenagem no VM. Verifique o estado do VM. Se o VM não estiver disponível, o modo de drenagem não pode ser alterado.|
|Falha na desconexão das sessões do utilizador |Não consegui desligar o utilizador do VM. Verifique o estado do VM. Se o VM não estiver disponível, a sessão do utilizador não pode ser desligada. Se o VM estiver disponível, verifique o estado da sessão do utilizador para ver se está desligado. |
|Falhou em registar todos os utilizadores dentro do anfitrião da sessão |Não foi possível tirar os utilizadores do VM. Verifique o estado do VM. Se não estiver disponível, os utilizadores não podem ser assinados. Verifique o estado da sessão do utilizador para ver se já estão assinados. Pode forçar a assinatura com o PowerShell. |
|Falhou em não assinar o utilizador do grupo de aplicações|Não foi possível desacruçar um grupo de aplicações para um utilizador. Verifique se o utilizador está disponível no Azure AD. Verifique se o utilizador faz parte de um grupo de utilizadores ao qual o grupo de aplicações é publicado. |
|Houve um erro na recuperação dos locais disponíveis |Verifique a localização dos VM utilizados no assistente de piscina de criação. Se a imagem não estiver disponível nesse local, adicione a imagem nesse local ou escolha uma localização VM diferente. |

### <a name="connection-error-codes"></a>Códigos de erro de ligação

|Código numérico|Código de erro|Solução sugerida|
|---|---|---|
|-2147467259|ConexãoFailedAdTrustedRelationshipFailure|O anfitrião da sessão não está corretamente associado ao Ative Directory.|
|-2146233088|ConexãoFailedUserHasValidSessionButRdshIsUnhealthy|As ligações falharam porque o anfitrião da sessão não está disponível. Verifique a saúde do anfitrião da sessão.|
|-2146233088|ConexãoFailedClientDisconnect|Se vir este erro com frequência, certifique-se de que o computador do utilizador está ligado à rede.|
|-2146233088|ConexãoFailedNoHealthyRdsh Disponível|A sessão a que o utilizador anfitrião tentou ligar não é saudável. Depurar a máquina virtual.|
|-2146233088|ConexãoFailedUserNotAuthorized|O utilizador não tem permissão para aceder à aplicação ou ambiente de trabalho publicado. O erro pode aparecer após o administrador ter removido os recursos publicados. Peça ao utilizador para refrescar o feed na aplicação Remote Desktop.|
|2|FileNotFound|A aplicação a que o utilizador tentou aceder está incorretamente instalada ou definida para um caminho incorreto.<br><br>Ao publicar novas aplicações enquanto o utilizador tiver uma sessão ativa, o utilizador não poderá aceder a esta aplicação. A sessão deve ser encerrada e reiniciada antes que o utilizador possa aceder à aplicação. |
|3|InválidosCredis|O nome de utilizador ou palavra-passe que o utilizador introduziu não corresponde a nomes de utilizador ou palavras-passe existentes. Reveja as credenciais para tipografias e tente novamente.|
|8|ConexãoBroken|A ligação entre Cliente e Gateway ou Servidor caiu. Não é necessária ação a não ser que aconteça inesperadamente.|
|14|UnexpectedNetworkDisconnect|A ligação à rede caiu. Peça ao utilizador para voltar a ligar.|
|24|ReverseConnectFailed|A máquina virtual hospedeira não tem linha de visão direta para RD Gateway. Certifique-se de que o endereço IP gateway pode ser resolvido.|

## <a name="error-cant-add-user-assignments-to-an-app-group"></a>Erro: Não é possível adicionar atribuições de utilizadores a um grupo de aplicações

Depois de atribuir um utilizador a um grupo de aplicações, o portal Azure apresenta um aviso que diz "Sessão Terminando" ou "Problemas de autenticação experimentais - extensão Microsoft_Azure_WVD". A página de atribuição então não carrega, e depois disso, as páginas param de carregar em todo o portal Azure (por exemplo, Monitor Azure, Log Analytics, Service Health, e assim por diante).

**Causa:** Há um problema com a política de acesso condicional. O portal Azure está a tentar obter um símbolo para o Microsoft Graph, que está dependente do SharePoint Online. O cliente tem uma política de acesso condicional chamada "Microsoft Office 365 Data Storage Terms of Use" que exige que os utilizadores aceitem os termos de uso para aceder ao armazenamento de dados. No entanto, ainda não assinaram, por isso o portal Azure não consegue obter o sinal.

**Correção:** Antes de iniciar sessão no portal Azure, o administrador primeiro precisa de se inscrever no SharePoint e aceitar os Termos de Utilização. Depois disso, devem poder inscrever-se no portal Azure como é normal.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as funções dentro do Windows Virtual Desktop, consulte o [ambiente de trabalho virtual do Windows.](environment-setup.md)

Para ver uma lista de cmdlets powerShell disponíveis para o Windows Virtual Desktop, consulte a [referência PowerShell](/powershell/windows-virtual-desktop/overview).
