---
title: Problemas de diagnóstico do Windows Virtual Desktop - Azure
description: Como utilizar a funcionalidade de diagnóstico do Windows Virtual Desktop para diagnosticar problemas.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cffc6393ef6f5c1a33be615d9d5d4b8729ab711f
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611862"
---
# <a name="identify-and-diagnose-issues"></a>Identificar e diagnosticar problemas

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização da primavera de 2020 com os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows. Se estiver a utilizar o lançamento do Windows Virtual Desktop Fall 2019 sem objetos do Gestor de Recursos Azure, consulte [este artigo](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md).
>
> A atualização Do Windows Virtual Desktop Spring 2020 encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-la para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

O Windows Virtual Desktop oferece uma funcionalidade de diagnóstico que permite ao administrador identificar problemas através de uma única interface. Para saber mais sobre as capacidades de diagnóstico do Windows Virtual Desktop, consulte [o Use Log Analytics para a funcionalidade](diagnostics-log-analytics.md)de diagnóstico .
  
As ligações que não chegam ao Windows Virtual Desktop não vão aparecer nos resultados de diagnóstico porque o próprio serviço de funções de diagnóstico faz parte do Windows Virtual Desktop. Os problemas de ligação ao Windows Virtual Desktop podem acontecer quando o utilizador final está a ter problemas de conectividade de rede.

## <a name="common-error-scenarios"></a>Cenários de erros comuns

Os cenários de erro são categorizados internamente para o serviço e externos ao Windows Virtual Desktop.

* Problema Interno: especifica cenários que não podem ser atenuados pelo cliente e que precisam de ser resolvidos como uma questão de suporte. Ao fornecer feedback através da Comunidade de Tecnologia de Ambiente de [Trabalho Virtual do Windows,](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)inclua o ID de correlação e o tempo aproximado de quando o problema ocorreu.
* Problema Externo: relacionar-se com cenários que podem ser atenuados pelo cliente. Estes são externos ao Windows Virtual Desktop.

A tabela que se segue enumera erros comuns que os seus administradores podem encontrar.

>[!NOTE]
>Esta lista inclui erros mais comuns e é atualizada numa cadência regular. Para garantir que tem as informações mais atualizadas, certifique-se de que volta a verificar este artigo pelo menos uma vez por mês.

## <a name="management-errors"></a>Erros de gestão

|Mensagem de erro|Solução sugerida|
|---|---|
|Falha na criação da chave de registo |O sinal não podia ser criado. Tente criá-lo novamente com um prazo de validade mais curto (entre 1 hora e 1 mês). |
|Falha na eliminação da chave de registo|Ficha de registo não podia ser apagada. Tente apagar de novo. Se ainda não funcionar, use a PowerShell para verificar se o token ainda lá está. Se estiver lá, apague-o com a PowerShell.|
|Falhou na alteração do modo de drenagem do hospedeiro da sessão |Não podia mudar o modo de drenagem no VM. Verifique o estado do VM. Se o VM não estiver disponível, o modo de drenagem não pode ser alterado.|
|Falha na desconexão das sessões do utilizador |Não consegui desconectar o utilizador do VM. Verifique o estado do VM. Se o VM não estiver disponível, a sessão de utilizador não pode ser desligada. Se o VM estiver disponível, verifique o estado da sessão do utilizador para ver se está desligado. |
|Não conseguiu desligar todos os utilizadores no âmbito do anfitrião da sessão |Não conseguiu contratar utilizadores para fora do VM. Verifique o estado do VM. Se não estiver disponível, os utilizadores não podem ser assinados. Verifique o estado da sessão do utilizador para ver se já estão assinados. Pode forçar a assinatura com a PowerShell. |
|Falha na não atribuição do utilizador do grupo de aplicações|Não podia despublicar um grupo de aplicações para um utilizador. Verifique se o utilizador está disponível no Azure AD. Verifique se o utilizador faz parte de um grupo de utilizadores para o qual o grupo de aplicações está publicado. |
|Houve um erro de recuperação dos locais disponíveis |Verifique a localização da VM utilizada no assistente de piscina de hospedeiro. Se a imagem não estiver disponível nesse local, adicione imagem nesse local ou escolha uma localização VM diferente. |

### <a name="external-connection-error-codes"></a>Códigos de erro de ligação externa

|Código numérico|Código de erro|Solução sugerida|
|---|---|---|
|-2147467259|ConexãoFalhadaFalha da relação fidedigna|O anfitrião da sessão não está corretamente acompanhado ao Diretório Ativo.|
|-2146233088|ConexãoFalhadaUserHasValidSessionButRdshIsUnhealthy|As ligações falharam porque o anfitrião da sessão não está disponível. Verifique a saúde do anfitrião da sessão.|
|-2146233088|ConexãoFalhada Falhado ClientDisconnect|Se vir este erro com frequência, certifique-se de que o computador do utilizador está ligado à rede.|
|-2146233088|ConexãoFailedNoHealthyRdshDisponível|A sessão a que o utilizador anfitrião tentou ligar-se não é saudável. Depura a máquina virtual.|
|-2146233088|ConexãoFalhadaUserNotAutorizado|O utilizador não tem permissão para aceder à aplicação ou ambiente de trabalho publicado. O erro pode aparecer após o administrador ter removido os recursos publicados. Peça ao utilizador que refresque o feed na aplicação Remote Desktop.|
|2|FileNotFound|A aplicação a que o utilizador tentou aceder está incorretamente instalada ou definida para um caminho incorreto.<br><br>Ao publicar novas aplicações enquanto o utilizador tem uma sessão ativa, o utilizador não poderá aceder a esta aplicação. A sessão deve ser encerrada e reiniciada antes de o utilizador poder aceder à aplicação. |
|3|Credenciais inválidas|O nome de utilizador ou palavra-passe que o utilizador introduziu não corresponde a quaisquer nomes de utilizador ou palavras-passe existentes. Reveja as credenciais para os tipografias e tente novamente.|
|8|Conexão Quebrada|A ligação entre o Cliente e o Gateway ou o Servidor caiu. Não é necessária nenhuma ação a não ser que aconteça inesperadamente.|
|14|Rede InesperadaDisconnect|A ligação à rede caiu. Peça ao utilizador para voltar a ligar-se.|
|24|ReverseConnectFailed|A máquina virtual hospedeira não tem linha direta de visão para RD Gateway. Certifique-se de que o endereço IP gateway pode ser resolvido.|

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre papéis dentro do Windows Virtual Desktop, consulte o ambiente de ambiente de [trabalho virtual do Windows](environment-setup.md).

Para ver uma lista de cmdlets PowerShell disponíveis para Windows Virtual Desktop, consulte a [referência PowerShell](/powershell/windows-virtual-desktop/overview).
