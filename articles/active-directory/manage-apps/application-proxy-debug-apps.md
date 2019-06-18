---
title: Depurar aplicações de Proxy de aplicações - Azure Active Directory | Documentos da Microsoft
description: Depure problemas com aplicações de Proxy de aplicações do Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: d0a12bde119e9dae3f950603fac4bce060bb5f91
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66172269"
---
# <a name="debug-application-proxy-application-issues"></a>Depurar problemas de aplicação do Proxy de aplicações 

Este artigo ajuda-o a resolver problemas com aplicações de Proxy de aplicações do Azure Active Directory (Azure AD). Se estiver a utilizar o serviço de Proxy de aplicações para acesso remoto a uma aplicação de web no local, mas estiver a ter problemas em ligar para o aplicativo, use este fluxograma para depurar problemas de aplicativos. 

## <a name="before-you-begin"></a>Antes de começar

Quando a resolução de problemas de Proxy de aplicações, recomendamos que comece com os conectores. Em primeiro lugar, siga o fluxo de resolução de problemas na [conector de Proxy de aplicações de depurar problemas](application-proxy-debug-connectors.md) para se certificar de que os conectores de Proxy de aplicações estão configuradas corretamente. Se ainda estiver a ter problemas, regresse a este artigo para resolver problemas da aplicação.  

Para obter mais informações sobre o Proxy de aplicações, consulte:

- [Acesso remoto às aplicações no local através do Proxy de aplicações](application-proxy.md)
- [Conectores de Proxy de aplicação](application-proxy-connectors.md)
- [Instalar e registar um conector](application-proxy-add-on-premises-application.md)
- [Resolver problemas do Proxy de aplicações e mensagens de erro](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Fluxograma para problemas de aplicativos

Este fluxograma orienta-o através dos passos para depuração alguns dos problemas mais comuns com a ligação à aplicação. Para obter detalhes sobre cada passo, consulte a tabela a seguir o fluxograma.

![Fluxograma que mostra os passos para depurar uma aplicação](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | Ação | Descrição | 
|---------|---------|---------|
|1 | Abra um browser, aceder à aplicação e introduza as suas credenciais | Tente utilizar as suas credenciais para iniciar sessão para a aplicação e verifique a existência de quaisquer erros relacionados com o usuário, como [não é possível aceder esta aplicação empresarial](application-proxy-sign-in-bad-gateway-timeout-error.md). |
|2 | Certifique-se de atribuição de utilizadores para a aplicação | Certifique-se de sua conta de utilizador tem permissão para aceder à aplicação a partir de dentro da rede empresarial e, em seguida, testar a iniciar sessão aplicação ao seguir os passos em [testar o aplicativo](application-proxy-add-on-premises-application.md#test-the-application). Se o início de sessão problemas persistirem, veja [como resolver erros de início de sessão](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-troubleshoot-sign-in-errors).  |
|3 | Abra um browser e tente aceder à aplicação | Se um erro aparece imediatamente, verifique se o Proxy de aplicações está configurado corretamente. Para obter detalhes sobre mensagens de erro específicas, consulte [problemas de resolução de problemas de Proxy de aplicações e mensagens de erro](application-proxy-troubleshoot.md).  |
|4 | Verifique a configuração do seu domínio personalizado ou o erro de resolução de problemas | Se a página não apresenta nada, certifique-se de que o seu domínio personalizado está corretamente configurado ao rever [trabalhar com domínios personalizados](application-proxy-configure-custom-domain.md).<br></br>Se a página não for carregado e é apresentada uma mensagem de erro, resolver problemas relacionados com o erro consultando [problemas de resolução de problemas de Proxy de aplicações e mensagens de erro](application-proxy-troubleshoot.md). <br></br>Se demorar mais tempo do que 20 segundos para uma mensagem de erro seja apresentado, pode haver problema de conectividade. Vá para o [conectores de Proxy de aplicações de depurar](application-proxy-debug-connectors.md) artigo de resolução de problemas.  |
|5 | Se os problemas persistirem, vá para a depuração do conector | Pode haver um problema de conectividade entre o proxy e o conector ou entre o conector e o back-end. Vá para o [conectores de Proxy de aplicações de depurar](application-proxy-debug-connectors.md) artigo de resolução de problemas. |
|6 | Publicar todos os recursos, ferramentas de programação do browser de verificar e corrigir os links | Certifique-se de que o caminho de publicação inclui todas as imagens necessárias, scripts e folhas de estilo para a sua aplicação. Para obter detalhes, consulte [adicionar uma aplicação no local ao Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). <br></br>Utilizar ferramentas de programação (F12 ferramentas no Internet Explorer ou o Microsoft Edge) e a verificação do navegador para publicar problemas, conforme descrito em [página do aplicativo não é apresentados corretamente](application-proxy-page-appearance-broken-problem.md). <br></br>Reveja as opções para resolver ligações danificadas no [Links na página não funcionam](application-proxy-page-links-broken-problem.md). |
|7 | Verificação de latência de rede | Se a página for carregada lentamente, saiba mais sobre formas para minimizar a latência de rede no [considerações para reduzir a latência](application-proxy-network-topology.md#considerations-for-reducing-latency). | 
|8 | Consulte a ajuda de resolução de problemas adicionais | Se os problemas persistirem, localize artigos de resolução de problemas adicionais na [documentação de resolução de problemas de Proxy de aplicações](application-proxy-page-appearance-broken-problem.md). |

## <a name="next-steps"></a>Passos Seguintes


* [Publicar aplicações em redes separadas e localizações através de grupos de conectores](application-proxy-connector-groups.md)
* [Trabalhar com servidores de proxy no local existentes](application-proxy-configure-connectors-with-proxy-servers.md)
* [Resolver erros de Proxy de aplicações e o conector](application-proxy-troubleshoot.md)
* [Como instalar automaticamente o conector de Proxy de aplicações do Azure AD](application-proxy-register-connector-powershell.md)
