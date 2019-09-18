---
title: Depurar aplicativos de proxy de aplicativo-Azure Active Directory | Microsoft Docs
description: Problemas de depuração com aplicativos de proxy de aplicativo Azure Active Directory (Azure AD).
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
ms.openlocfilehash: 06b8edcb0f912bfd35137e197253b20b9459448f
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057751"
---
# <a name="debug-application-proxy-application-issues"></a>Depurar problemas de aplicativo de proxy de aplicativo 

Este artigo ajuda você a solucionar problemas com aplicativos de proxy de aplicativo Azure Active Directory (Azure AD). Se você estiver usando o serviço de proxy de aplicativo para acesso remoto a um aplicativo Web local, mas estiver tendo problemas para se conectar ao aplicativo, use este fluxograma para depurar problemas de aplicativos. 

## <a name="before-you-begin"></a>Antes de começar

Ao solucionar problemas de proxy de aplicativo, recomendamos que você comece com os conectores. Primeiro, siga o fluxo de solução de problemas em [depurar problemas do conector de proxy de aplicativo](application-proxy-debug-connectors.md) para garantir que os conectores de proxy de aplicativo estejam configurados corretamente. Se você ainda tiver problemas, retorne a este artigo para solucionar problemas do aplicativo.  

Para obter mais informações sobre o proxy de aplicativo, consulte:

- [Acesso remoto a aplicativos locais por meio do proxy de aplicativo](application-proxy.md)
- [Conectores de proxy de aplicativo](application-proxy-connectors.md)
- [Instalar e registrar um conector](application-proxy-add-on-premises-application.md)
- [Resolver problemas do Proxy de aplicações e mensagens de erro](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Fluxograma para problemas de aplicativo

Este fluxograma orienta você pelas etapas para depurar alguns dos problemas mais comuns com a conexão com o aplicativo. Para obter detalhes sobre cada etapa, consulte a tabela após o fluxograma.

![Fluxograma mostrando as etapas para depurar um aplicativo](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | Action | Descrição | 
|---------|---------|---------|
|1 | Abra um navegador, acesse o aplicativo e insira suas credenciais | Tente usar suas credenciais para entrar no aplicativo e verificar se há erros relacionados ao usuário, como [este aplicativo corporativo não pode ser acessado](application-proxy-sign-in-bad-gateway-timeout-error.md). |
|2 | Verificar a atribuição de usuário para o aplicativo | Verifique se sua conta de usuário tem permissão para acessar o aplicativo de dentro da rede corporativa e, em seguida, teste a entrada no aplicativo seguindo as etapas em [testar o aplicativo](application-proxy-add-on-premises-application.md#test-the-application). Se os problemas de entrada persistirem, consulte [como solucionar problemas de erros de entrada](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).  |
|3 | Abra um navegador e tente acessar o aplicativo | Se um erro aparecer imediatamente, verifique se o proxy de aplicativo está configurado corretamente. Para obter detalhes sobre mensagens de erro específicas, consulte [solucionar problemas de proxy de aplicativo e mensagens de erro](application-proxy-troubleshoot.md).  |
|4 | Verifique a configuração do domínio personalizado ou solucione o erro | Se a página não for exibida, verifique se o seu domínio personalizado está configurado corretamente examinando o [trabalho com domínios personalizados](application-proxy-configure-custom-domain.md).<br></br>Se a página não for carregada e uma mensagem de erro for exibida, solucione o erro fazendo referência a problemas de [proxy de aplicativo e mensagens de erro](application-proxy-troubleshoot.md). <br></br>Se demorar mais de 20 segundos para que uma mensagem de erro seja exibida, pode haver um problema de conectividade. Vá para o artigo [depurar conectores de proxy de aplicativo](application-proxy-debug-connectors.md) de solução de problemas.  |
|5 | Se os problemas persistirem, vá para depuração de conector | Pode haver um problema de conectividade entre o proxy e o conector ou entre o conector e o back-end. Vá para o artigo [depurar conectores de proxy de aplicativo](application-proxy-debug-connectors.md) de solução de problemas. |
|6 | Publicar todos os recursos, verificar as ferramentas de desenvolvedor do navegador e corrigir links | Verifique se o caminho de publicação inclui todas as imagens, scripts e folhas de estilo necessárias para seu aplicativo. Para obter detalhes, consulte [Adicionar um aplicativo local ao Azure ad](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). <br></br>Use as ferramentas de desenvolvedor do navegador (Ferramentas F12 no Internet Explorer ou Microsoft Edge) e verifique se os problemas de publicação, conforme descrito na [página do aplicativo, não são exibidos corretamente](application-proxy-page-appearance-broken-problem.md). <br></br>As opções de revisão para resolver links desfeitos em [links na página não funcionam](application-proxy-page-links-broken-problem.md). |
|7 | Verificar latência de rede | Se a página for carregada lentamente, saiba mais sobre maneiras de minimizar a latência de rede em [considerações para reduzir a latência](application-proxy-network-topology.md#considerations-for-reducing-latency). | 
|8 | Consulte ajuda adicional de solução de problemas | Se os problemas persistirem, Encontre artigos adicionais de solução de problemas na [documentação de solução de problemas de proxy de aplicativo](application-proxy-page-appearance-broken-problem.md). |

## <a name="next-steps"></a>Passos Seguintes


* [Publicar aplicações em redes separadas e localizações através de grupos de conectores](application-proxy-connector-groups.md)
* [Trabalhar com servidores de proxy no local existentes](application-proxy-configure-connectors-with-proxy-servers.md)
* [Resolver erros de Proxy de aplicações e o conector](application-proxy-troubleshoot.md)
* [Como instalar automaticamente o conector de Proxy de aplicações do Azure AD](application-proxy-register-connector-powershell.md)
