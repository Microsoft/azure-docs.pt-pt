---
title: Não é possível aceder a este erro de Aplicação Corporativa com app Proxy
description: Como resolver problemas de acesso comuns com aplicações Proxy de aplicação AD AD Azure.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2019
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18c3b0397a0ad3e9e368d1a14e20a999ced5545b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88690277"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>Erro "Não pode aceder a esta aplicação corporativa" ao utilizar uma aplicação Proxy de aplicação

Este artigo ajuda-o a resolver problemas comuns para o erro "Esta aplicação corporativa não pode ser acedida" numa aplicação de procuração de aplicação AD AD Azure.

## <a name="overview"></a>Descrição geral

Quando vir este erro, encontre o código de estado na página de erro. Este código é provavelmente um dos seguintes códigos de estado:

- **Tempo de gateway**: O serviço Application Proxy não consegue chegar ao conector. Este erro normalmente indica um problema com a atribuição do conector, o próprio conector ou as regras de ligação em rede em torno do conector.
- **Bad Gateway**: O conector não consegue alcançar a aplicação de backend. Este erro pode indicar uma configuração errada da aplicação.
- **Proibido**: O utilizador não está autorizado a aceder à aplicação. Este erro pode ocorrer quer quando o utilizador não está atribuído à aplicação no Azure Ative Directory, quer se no backend o utilizador não tiver permissão para aceder à aplicação.

Para encontrar o código, olhe para o texto na parte inferior esquerda da mensagem de erro para o campo "Código de Estado". Procure também quaisquer dicas adicionais na parte inferior da página.

![Exemplo: Erro de tempo limite gateway](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

Para obter detalhes sobre como resolver a causa principal destes erros e mais detalhes sobre as correções sugeridas, consulte a secção correspondente abaixo.

## <a name="gateway-timeout-errors"></a>Erros de tempo de gateway

Ocorre um tempo limite de gateway quando o serviço tenta chegar ao conector e é incapaz de dentro da janela de tempo. Este erro é normalmente causado por uma aplicação atribuída a um Grupo de Conector sem conectores de funcionamento, ou algumas portas exigidas pelo Conector não estão abertas.

## <a name="bad-gateway-errors"></a>Erros do Bad Gateway

Um erro de porta de entrada errado indica que o conector não consegue alcançar a aplicação de backend. certifique-se de que publicou a aplicação correta. Erros comuns que causam este erro são:

- Um erro ou erro na URL interna
- Não publicar a raiz da aplicação. Por exemplo, publicar `http://expenses/reimbursement` mas tentar aceder `http://expenses`
- Problemas com a configuração da Delegação Restrita kerberos (KCD)
- Problemas com a aplicação backend

## <a name="forbidden-errors"></a>Erros proibidos

Se vir um erro proibido, o utilizador não foi designado para a aplicação. Este erro pode estar no Azure Ative Directory ou na aplicação backend.

Para saber como atribuir os utilizadores à aplicação em Azure, consulte a documentação de [configuração](application-proxy-add-on-premises-application.md#test-the-application).

Se confirmar que o utilizador está atribuído à aplicação em Azure, verifique a configuração do utilizador na aplicação backend. Se estiver a utilizar a Delegação Restrita Kerberos/Autenticação Integrada do Windows, consulte a página de resolução de problemas da KCD para obter orientações.

## <a name="check-the-applications-internal-url"></a>Verifique o URL interno da aplicação

Como primeiro passo rápido, verifique e corrija o URL interno abrindo a aplicação através **de Aplicações Empresariais,** selecionando depois o menu **Application Proxy.** Verifique se o URL interno é o utilizado na sua rede de instalações para aceder à aplicação.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Verifique se a aplicação está atribuída a um Grupo de Conector que trabalha

Para verificar se o pedido é atribuído a um grupo de conector em funcionamento:

1. Abra a aplicação no portal indo para **Azure Ative Directory,** clicando em **Aplicações empresariais,** em seguida, **todas as aplicações.** Abra a aplicação e, em seguida, selecione **Application Proxy** a partir do menu esquerdo.
1. Olhe para o campo do Grupo Connector. Se não houver conectores ativos no grupo, vê-se um aviso. Se não vir nenhum aviso, passe para verificar se todas as portas necessárias são [permitidas.](application-proxy-add-on-premises-application.md)
1. Se o Grupo de Conector errado estiver a aparecer, utilize a redução para selecionar o grupo correto e confirme que já não vê avisos. Se o Grupo de Conector pretendido estiver a aparecer, clique na mensagem de aviso para abrir a página com a gestão do Conector.
1. A partir daqui, há algumas maneiras de perfurar mais:

   - Mover um Conector ativo para o grupo: Se tiver um Conector ativo que deve pertencer a este grupo e tiver uma linha de visão para a aplicação de backend do alvo, pode mover o Conector para o grupo designado. Para tal, clique no Conector. No campo "Grupo de Conector", utilize o drop-down para selecionar o grupo correto e clique em guardar.
   - Faça o download de um novo Conector para esse grupo: A partir desta página, você pode obter o link para [baixar um novo Conector](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download). Instale o Conector numa máquina com linha de visão direta para a aplicação backend. Normalmente, o Conector é instalado no mesmo servidor que a aplicação. Utilize o link de download Connector para transferir um conector para a máquina alvo. Em seguida, clique no Conector e use o drop-down "Connector Group" para se certificar de que pertence ao grupo certo.
   - Investigue um Conector inativo: Se um conector mostrar como inativo, não consegue chegar ao serviço. Este erro deve-se normalmente ao bloqueio de algumas portas necessárias. Para resolver este problema, avance para verificar se todas as portas necessárias são permitidas.

Depois de utilizar estas etapas para garantir que a aplicação é atribuída a um grupo com conectores de trabalho, teste novamente a aplicação. Se ainda não estiver a funcionar, continue para a secção seguinte.

## <a name="check-all-required-ports-are-open"></a>Verifique se todas as portas necessárias estão abertas

Verifique se todas as portas necessárias estão abertas. Para as portas necessárias, consulte a secção de portas abertas do [Tutorial: Adicione uma aplicação no local para acesso remoto através do Application Proxy in Azure Ative Directory](application-proxy-add-on-premises-application.md). Se todas as portas necessárias estiverem abertas, mova-se para a secção seguinte.

## <a name="check-for-other-connector-errors"></a>Verifique se existem outros erros do conector

Se nenhum dos acima referidos resolver o problema, o próximo passo é procurar problemas ou erros com o próprio Conector. Pode ver alguns erros comuns no [documento de resolução de problemas](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors).

Também pode olhar diretamente para os registos do Conector para identificar quaisquer erros. Muitas das mensagens de erro partilham recomendações específicas para correções. Para visualizar os registos, consulte a documentação dos [conectores](application-proxy-connectors.md#under-the-hood).

## <a name="additional-resolutions"></a>Resoluções Adicionais

Se o acima não resolveu o problema, há algumas causas possíveis diferentes. Para identificar o problema:

Se a sua aplicação estiver configurada para utilizar a Autenticação Integrada do Windows (IWA), teste a aplicação sem uma única sação. Se não, passe para o parágrafo seguinte. Para verificar a aplicação sem um único s-on, abra a sua aplicação através **de Aplicações Enterprise** e vá ao menu **'Sign-On' único.** Altere o drop-down de "Autenticação Integrada do Windows" para "Azure AD single sign-on desativado".

Agora abra um navegador e tente aceder novamente à aplicação. Deve ser solicitado para autenticação e entrar na aplicação. Se conseguir autenticar, o problema é a configuração da Delegação Restrita kerberos (KCD) que permite a única inscrição. Para mais informações, consulte a página de resolução de problemas da KCD.

Se continuar a ver o erro, vá à máquina onde o Conector está instalado, abra um browser e tente alcançar o URL interno utilizado para a aplicação. O Conector age como outro cliente da mesma máquina. Se não conseguir contactar a aplicação, investigue por que razão essa máquina não consegue chegar à aplicação ou utilize um conector num servidor capaz de aceder à aplicação.

Se conseguir chegar à aplicação a partir dessa máquina, procure problemas ou erros com o próprio Conector. Pode ver alguns erros comuns no [documento de resolução de problemas](application-proxy-troubleshoot.md#connector-errors). Também pode olhar diretamente para os registos do Conector para identificar quaisquer erros. Muitas das nossas mensagens de erro podem partilhar recomendações mais específicas para correções. Para aprender a ver os registos, consulte a [documentação dos nossos conectores.](application-proxy-connectors.md#under-the-hood)

## <a name="next-steps"></a>Passos seguintes

[Compreenda os conectores Proxy de aplicação AD Azure](application-proxy-connectors.md)
