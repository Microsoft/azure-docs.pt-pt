---
title: Problemas de diagnóstico de desktop virtual do Windows - Azure
description: Como utilizar a funcionalidade de diagnóstico virtual do Windows Desktop para diagnosticar problemas.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2ead16c655d4790e81931371e67da8106dabf83e
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85200550"
---
# <a name="identify-and-diagnose-issues"></a>Identificar e diagnosticar problemas

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização primavera 2020 com objetos de desktop virtual do Windows Manager do Azure. Se estiver a utilizar o desbloqueio virtual do Windows Desktop Fall 2019 sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md).
>
> A atualização Virtual Desktop Spring 2020 do Windows está atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos a sua utilização para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Windows Virtual Desktop oferece uma funcionalidade de diagnóstico que permite ao administrador identificar problemas através de uma única interface. Para saber mais sobre as capacidades de diagnóstico do Windows Virtual Desktop, consulte [Use Log Analytics para a funcionalidade de diagnóstico](diagnostics-log-analytics.md).

As ligações que não chegam ao Windows Virtual Desktop não aparecem nos resultados dos diagnósticos porque o próprio serviço de função de diagnóstico faz parte do Windows Virtual Desktop. Problemas de ligação virtual ao ambiente de trabalho do Windows podem ocorrer quando o utilizador final está a ter problemas de conectividade de rede.

## <a name="common-error-scenarios"></a>Cenários de erros comuns

Os cenários de erro são categorizados internamente ao serviço e externos ao Windows Virtual Desktop.

* Problema Interno: especifica cenários que não podem ser atenuados pelo cliente e que precisam de ser resolvidos como um problema de suporte. Ao fornecer feedback através da [Comunidade Virtual desktop Tech do Windows,](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)inclua o ID de correlação e o período de tempo aproximado de quando o problema ocorreu.
* Questão Externa: relacionar-se com cenários que podem ser atenuados pelo cliente. Estes são externos ao Windows Virtual Desktop.

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

### <a name="external-connection-error-codes"></a>Códigos de erro de ligação externa

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

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as funções dentro do Windows Virtual Desktop, consulte o [ambiente de trabalho virtual do Windows.](environment-setup.md)

Para ver uma lista de cmdlets powerShell disponíveis para o Windows Virtual Desktop, consulte a [referência PowerShell](/powershell/windows-virtual-desktop/overview).
