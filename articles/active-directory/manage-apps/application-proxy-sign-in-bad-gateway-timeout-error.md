---
title: Não é possível aceder a este erro de aplicação corporativa com app Proxy
description: Como resolver problemas comuns de acesso com aplicações de Procuração de Aplicação AD Azure.
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
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74275458"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>Erro "Can't Access this Corporate Application" ao utilizar uma aplicação de procuração de aplicação

Este artigo ajuda-o a resolver problemas comuns para o erro "Esta aplicação corporativa não pode ser acedida" numa aplicação de Procuração de Aplicações AD Azure.

## <a name="overview"></a>Descrição geral

Quando vir este erro, encontre o código de estado na página de erro. Este código é provavelmente um dos seguintes códigos de estado:

- **Tempo de saída**de gateway : O serviço proxy de aplicação não consegue chegar ao conector. Este erro normalmente indica um problema com a atribuição do conector, o próprio conector ou as regras de ligação em torno do conector.
- **Bad Gateway**: O conector não consegue alcançar a aplicação de backend. Este erro pode indicar uma configuração errada da aplicação.
- **Proibido**: O utilizador não está autorizado a aceder à aplicação. Este erro pode ocorrer quando o utilizador não é atribuído à aplicação no Diretório Ativo do Azure, ou se no backend o utilizador não tiver autorização para aceder à aplicação.

Para encontrar o código, veja o texto na parte inferior à esquerda da mensagem de erro para o campo "Código de Estado". Procure também quaisquer dicas adicionais na parte inferior da página.

![Exemplo: Erro de tempo de saída de gateway](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

Para obter mais detalhes sobre como resolver os problemas da causa destes erros e mais detalhes sobre as correções sugeridas, consulte a secção correspondente abaixo.

## <a name="gateway-timeout-errors"></a>Erros de timeout gateway

Ocorre um intervalo de entrada quando o serviço tenta chegar ao conector e não consegue dentro da janela de tempo. Este erro é normalmente causado por uma aplicação atribuída a um Grupo de Conectores sem conectores de trabalho, ou algumas portas exigidas pelo Conector não estão abertas.

## <a name="bad-gateway-errors"></a>Erros de Gateway maus

Um erro de gateway grave indica que o conector não consegue alcançar a aplicação de backend. certifique-se de que publicou a aplicação correta. Erros comuns que causam este erro são:

- Um erro ou erro na URL interna
- Não publicar a raiz da aplicação. Por exemplo, <http://expenses/reimbursement> publicar mas tentar aceder<http://expenses>
- Problemas com a configuração da Delegação Limitada kerberos (KCD)
- Problemas com a aplicação backend

## <a name="forbidden-errors"></a>Erros proibidos

Se vir um erro proibido, o utilizador não foi designado para a aplicação. Este erro pode estar no Diretório Ativo azure ou na aplicação backend.

Para saber como atribuir utilizadores à aplicação no Azure, consulte a documentação de [configuração.](application-proxy-add-on-premises-application.md#test-the-application)

Se confirmar que o utilizador está atribuído à aplicação no Azure, verifique a configuração do utilizador na aplicação backend. Se estiver a utilizar a Delegação Limitada kerberos/autenticação integrada do Windows, consulte a página KCD Troubleshoot para obter orientações.

## <a name="check-the-applications-internal-url"></a>Verifique o URL interno da aplicação

Como primeiro passo rápido, verifique duas vezes e corrija o URL interno abrindo a aplicação através de **Aplicações Empresariais**e, em seguida, selecionando o menu Proxy de **Aplicação.** Verifique se o URL interno é o utilizado na sua rede de instalações para aceder à aplicação.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Verifique se a aplicação está atribuída a um Grupo de Conector em funcionamento

Para verificar se a aplicação é atribuída a um Grupo de Conector de trabalho:

1. Abra a aplicação no portal indo para **o Azure Ative Directory,** clicando em **Aplicações Empresariais,** em **seguida, todas as aplicações.** Abra a aplicação e, em seguida, selecione **Application Proxy** do menu esquerdo.
1. Olhe para o campo do Grupo De Conector. Se não houver conectores ativos no grupo, verá um aviso. Se não vir avisos, avance para verificar se todas as portas necessárias são permitidas.
1. Se o Grupo Connector estiver a mostrar errado, utilize a queda para selecionar o grupo correto e confirme que já não vê quaisquer avisos. Se o Grupo de Conectores pretendido estiver a mostrar, clique na mensagem de aviso para abrir a página com a gestão do Connector.
1. A partir daqui, há algumas maneiras de perfurar mais:

   - Mova um Conector ativo para o grupo: Se tiver um Conector ativo que deve pertencer a este grupo e tiver uma linha de visão para a aplicação de backend do alvo, pode mover o Conector para o grupo designado. Para isso, clique no Conector. No campo "Connector Group", utilize a gota para selecionar o grupo correto e clique em guardar.
   - Baixe um novo Conector para esse grupo: A partir desta página, pode obter o link para [descarregar um novo Conector](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download). Instale o Conector numa máquina com linha direta de visão para a aplicação backend. Normalmente, o Conector é instalado no mesmo servidor que a aplicação. Utilize o link de descarregamento Do Conector para descarregar um conector na máquina-alvo. Em seguida, clique no Conector e use a queda do "Connector Group" para se certificar de que pertence ao grupo certo.
   - Investigue um Conector inativo: Se um conector mostrar-se inativo, não consegue chegar ao serviço. Este erro deve-se normalmente ao bloqueio de algumas portas necessárias. Para resolver este problema, passe para verificar se todas as portas necessárias são permitidas.

Depois de utilizar estas medidas para garantir que a aplicação é atribuída a um grupo com Conectores de trabalho, teste novamente a aplicação. Se ainda não estiver a funcionar, continue para a próxima secção.

## <a name="check-all-required-ports-are-open"></a>Verifique se todas as portas necessárias estão abertas

Para verificar se todas as portas necessárias estão abertas, consulte a documentação sobre as portas de abertura. Se todas as portas necessárias estiverem abertas, passe para a secção seguinte.

## <a name="check-for-other-connector-errors"></a>Verifique se existem outros erros do Conector

Se nenhum dos acima resolvidos resolver o problema, o próximo passo é procurar problemas ou erros com o próprio Conector. Pode ver alguns erros comuns no [documento Troubleshoot.](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors)

Também pode olhar diretamente para os registos do Conector para identificar quaisquer erros. Muitas das mensagens de erro partilham recomendações específicas para correções. Para ver os registos, consulte a documentação dos [conectores](application-proxy-connectors.md#under-the-hood).

## <a name="additional-resolutions"></a>Resoluções adicionais

Se o que precede não resolveu o problema, há algumas causas possíveis diferentes. Para identificar a questão:

Se a sua aplicação estiver configurada para utilizar a Autenticação Integrada do Windows (IWA), teste a aplicação sem um único sinal. Caso contrário, passe para o parágrafo seguinte. Para verificar a aplicação sem um único sinal, abra a sua aplicação através de **Aplicações Empresariais** e vá ao menu **De Entrada Única.** Altere a queda de "Autenticação Integrada do Windows" para "Azure AD single sign-on desativado".

Agora abra um navegador e tente aceder novamente à aplicação. Deve ser solicitado para autenticação e entrar na aplicação. Se conseguir autenticar, o problema é com a configuração da Delegação Limitada Kerberos (KCD) que permite o único sinal. Para mais informações, consulte a página KCD Troubleshoot.

Se continuar a ver o erro, vá à máquina onde o Conector está instalado, abra um browser e tente alcançar o URL interno utilizado para a aplicação. O Conector age como outro cliente da mesma máquina. Se não conseguir chegar à aplicação, investigue por que razão essa máquina não consegue aceder à aplicação ou utilizar um conector num servidor capaz de aceder à aplicação.

Se conseguir chegar à aplicação a partir dessa máquina, procure problemas ou erros com o próprio Conector. Pode ver alguns erros comuns no [documento Troubleshoot.](application-proxy-troubleshoot.md#connector-errors) Também pode olhar diretamente para os registos do Conector para identificar quaisquer erros. Muitas das nossas mensagens de erro podem partilhar recomendações mais específicas para correções. Para saber como ver os registos, consulte a documentação dos [nossos conectores.](application-proxy-connectors.md#under-the-hood)

## <a name="next-steps"></a>Passos seguintes

[Compreender os conectores de procuração de aplicação da AD Azure](application-proxy-connectors.md)
