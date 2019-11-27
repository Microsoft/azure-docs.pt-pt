---
title: Não é possível acessar este erro de aplicativo corporativo com o aplicativo proxy de aplicativo
description: Como resolver problemas comuns de acesso com aplicações de Proxy de aplicações do Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d61f4b4bce9b8287dc13237f071684ea5d135fa
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275458"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>Erro "Não é possível aceder a esta aplicação empresarial" quando utilizar uma aplicação de Proxy de aplicações

Este artigo ajuda-o a resolver problemas comuns para o erro "não é possível aceder esta aplicação empresarial" num aplicativo de Proxy de aplicações do Azure AD.

## <a name="overview"></a>Descrição geral

Quando vir este erro, encontre o código de estado na página de erro. Esse código se tratar de um dos seguintes códigos de estado:

- **Tempo limite do gateway**: o serviço de proxy de aplicativo não consegue acessar o conector. Este erro normalmente indica um problema com a atribuição de conector, o conector propriamente dito, ou o funcionamento em rede regras sobre o conector.
- **Gateway inadequado**: o conector não pode acessar o aplicativo de back-end. Este erro poderá indicar uma configuração incorreta do aplicativo.
- **Proibido**: o usuário não está autorizado a acessar o aplicativo. Este erro pode acontecer quando o utilizador não está atribuído para a aplicação no Azure Active Directory, ou se o back-end, o utilizador não tem permissão para aceder à aplicação.

Para encontrar o código, observe o texto à esquerda na parte inferior da mensagem de erro para o campo de "Código de estado". Também procure qualquer dicas adicionais na parte inferior da página.

![Exemplo: erro de tempo limite de gateway](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

Para obter detalhes sobre como resolver a causa destes erros e obter mais detalhes sobre as correções sugeridas, consulte a seção correspondente abaixo.

## <a name="gateway-timeout-errors"></a>Erros de tempo limite do gateway

Um tempo limite do gateway ocorre quando o serviço tenta contactar o conector e não conseguir dentro da janela de tempo limite. Este erro é habitualmente causado por uma aplicação atribuída a um grupo de conectores com conectores a funcionar ou algumas portas exigidas pelo conector não estão abertas.

## <a name="bad-gateway-errors"></a>Erros de Gateway incorretos

Um erro de gateway inválido indica que o conector não consegue aceder à aplicação de back-end. Certifique-se de que tiver publicado a aplicação correta. Erros comuns que fazem com que este erro são:

- Um erro de digitação ou um erro no URL interno
- Não publicar a raiz do aplicativo. Por exemplo, publicar <http://expenses/reimbursement>, mas tentar acessar <http://expenses>
- Problemas com a configuração de Kerberos Constrained Delegation (KCD)
- Problemas com a aplicação de back-end

## <a name="forbidden-errors"></a>Proibido erros

Se vir um erro proibido, o utilizador não foi atribuído à aplicação. Este erro pode ser no Azure Active Directory ou na aplicação de back-end.

Para saber como atribuir usuários ao aplicativo no Azure, consulte a documentação de [configuração](application-proxy-add-on-premises-application.md#test-the-application).

Se confirmar que é atribuída ao utilizador para a aplicação no Azure, verifique a configuração de utilizador da aplicação de back-end. Se estiver a utilizar autenticação de Windows de integrado/delegação restrita de Kerberos, consulte a página de resolução de problemas de KCD de mensagens em fila para obter diretrizes.

## <a name="check-the-applications-internal-url"></a>Verifique o URL interno do aplicativo

Como uma primeira etapa rápida, verifique e corrija a URL interna abrindo o aplicativo por meio de **aplicativos empresariais**e, em seguida, selecionando o menu **proxy de aplicativo** . Verifique se a URL interna é a usada de sua rede local para acessar o aplicativo.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Verifique a aplicação é atribuída a um grupo de conectores em funcionamento

Para verificar a aplicação é atribuído a um grupo de conectores em funcionamento:

1. Abra o aplicativo no portal acessando **Azure Active Directory**, clicando em **aplicativos empresariais**e em **todos os aplicativos.** Abra o aplicativo e, em seguida, selecione **proxy de aplicativo** no menu à esquerda.
1. Veja o campo de grupo do conector. Se não existirem não existem conectores ativos no grupo, verá um aviso. Se você não vir nenhum aviso, passe para verificar se todas as portas necessárias são permitidas.
1. Se o problema é mostrar o grupo do conector, utilize a menu pendente para selecionar o grupo correto e confirme que já não vê quaisquer avisos. Se o grupo do conector prevista é apresentada, clique a mensagem de aviso para abrir a página com a gestão do conector.
1. A partir daqui, existem algumas formas de fazer uma busca ainda mais:

   - Mova um conector do Active Directory para o grupo: Se tiver um conector do Active Directory deve pertencer a este grupo e com a linha de visão para a aplicação de back-end de destino, pode mover o conector para o grupo atribuído. Para tal, clique o conector. No campo do "Grupo de conectores", use a menu pendente para selecionar o grupo correto e clique em Guardar.
   - Baixar um novo conector para esse grupo: nessa página, você pode obter o link para [baixar um novo conector](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download). Instale o conector num computador com a linha de visão direta para a aplicação de back-end. Normalmente, o conector é instalado no mesmo servidor que o aplicativo. Utilize a ligação de conector de transferência para transferir um conector para o computador de destino. Em seguida, clique o conector e utilize o menu pendente "conector do grupo" para se certificar de que ele pertence ao grupo certo.
   - Investigar um conector inativo: se um conector mostra como inativo, não conseguir contactar o serviço. Este erro é normalmente devido a algumas portas necessárias a ser bloqueadas. Para resolver esse problema, vá para verificar se todas as portas necessárias são permitidas.

Depois de utilizar estes passos para garantir que a aplicação é atribuída a um grupo com a trabalhar de conectores, teste o aplicativo novamente. Se ainda não está funcionando, avance para a secção seguinte.

## <a name="check-all-required-ports-are-open"></a>Verifique se todas as portas necessárias estão abertas

Para verificar-se de que todas as necessárias portas estão abertas, consulte a documentação sobre como abrir portas. Se as portas necessárias estiverem abertas, mova para a secção seguinte.

## <a name="check-for-other-connector-errors"></a>Verificação de outros erros de conector

Se nenhum deles resolver o problema, a próxima etapa é procurar problemas ou erros com o conector propriamente dito. Você pode ver alguns erros comuns no [documento de solução de problemas](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors).

Também pode ver diretamente os registos do conector para identificar eventuais erros. Muitas das mensagens de erro partilham recomendações específicas para correções. Para exibir os logs, consulte a [documentação conectores](application-proxy-connectors.md#under-the-hood).

## <a name="additional-resolutions"></a>Resoluções de adicionais

Se o procedimento acima não corrigir o problema, há algumas causas possíveis diferentes. Para identificar o problema:

Se seu aplicativo estiver configurado para utilizar a autenticação integrada do Windows (IWA), teste o aplicativo sem início de sessão único. Caso contrário, mover para o próximo parágrafo. Para verificar o aplicativo sem logon único, abra o aplicativo por meio de **aplicativos empresariais** e vá para o menu de **logon único** . Altere a lista pendente de "Autenticação integrada do Windows" para "Do Azure AD início de sessão único desativado".

Agora, abra um browser e tente aceder novamente à aplicação. Deve ser pedida a autenticação e obter no aplicativo. Se é possível efetuar a autenticação, o problema é com a configuração de Kerberos Constrained Delegation (KCD) que permite o início de sessão único. Para obter mais informações, consulte a página resolução de problemas de KCD.

Se continuar a ver o erro, vá para a máquina em que o conector é instalado, abra um browser e tentam aceder a URL interna utilizada para a aplicação. O conector funciona como outro cliente a partir do mesmo computador. Se não é possível aceder à aplicação, investigar por que é possível aceder à aplicação ou utilizar um conector num servidor que é capaz de aceder à aplicação que a máquina.

Se pode entrar nessa máquina, a aplicação para procurar problemas ou erros com o conector propriamente dito. Você pode ver alguns erros comuns no [documento de solução de problemas](application-proxy-troubleshoot.md#connector-errors). Também pode ver diretamente os registos do conector para identificar eventuais erros. Muitas das nossas mensagens de erro poderá partilhar recomendações mais específicas para correções. Para saber como exibir os logs, consulte a [documentação de nossos conectores](application-proxy-connectors.md#under-the-hood).

## <a name="next-steps"></a>Passos seguintes

[Entender os conectores de Proxy de Aplicativo do AD do Azure](application-proxy-connectors.md)
