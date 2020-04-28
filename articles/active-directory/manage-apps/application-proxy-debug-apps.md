---
title: Aplicações proxy de aplicação de depuração - Diretório Ativo Azure / Microsoft Docs
description: Problemas de depuração com aplicações de Proxy de Aplicação de Diretório Ativo Azure (Azure AD).
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
ms.openlocfilehash: 575891d99c077299f5e7abf008c1ebb2b158373f
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74382079"
---
# <a name="debug-application-proxy-application-issues"></a>Depurar problemas de aplicações do Proxy de Aplicações 

Este artigo ajuda-o a resolver problemas com aplicações de Procuração de Aplicação de Proxy de Diretório Ativo Azure (Azure AD). Se estiver a utilizar o serviço Application Proxy para acesso remoto a uma aplicação web no local, mas está com dificuldades em ligar-se à aplicação, utilize este fluxograma para depurar problemas de aplicação. 

## <a name="before-you-begin"></a>Antes de começar

Ao resolver problemas de procuração de aplicação, recomendamos que comece com os conectores. Em primeiro lugar, siga o fluxo de resolução de problemas nos [problemas do Conector proxy](application-proxy-debug-connectors.md) de aplicação para se certificar de que os conectores proxy da aplicação estão configurados corretamente. Se ainda tiver problemas, volte a este artigo para resolver o pedido.  

Para mais informações sobre o Proxy de Aplicação, consulte:

- [Acesso remoto a aplicações no local através de Procuração de Aplicações](application-proxy.md)
- [Conectores proxy de aplicação](application-proxy-connectors.md)
- [Instalar e registar um conector](application-proxy-add-on-premises-application.md)
- [Problemas de procuração de aplicação de problemas e mensagens de erro](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Flowchart para problemas de aplicação

Este fluxograma acompanha-o através dos passos para depurar algumas das questões mais comuns com a ligação à aplicação. Para mais detalhes sobre cada passo, consulte a tabela seguindo o fluxograma.

![Flowchart mostrando passos para depurar uma aplicação](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | Ação | Descrição | 
|---------|---------|---------|
|1 | Abra um navegador, aceda à app e introduza as suas credenciais | Tente usar as suas credenciais para iniciar sessão na app e verifique se quaisquer erros relacionados com o utilizador, como [esta aplicação corporativa não pode ser acedida](application-proxy-sign-in-bad-gateway-timeout-error.md). |
|2 | Verifique a atribuição do utilizador à aplicação | Certifique-se de que a sua conta de utilizador tem permissão para aceder à aplicação a partir de dentro da rede corporativa e, em seguida, testar a sessão na app seguindo os passos em [Testar a aplicação](application-proxy-add-on-premises-application.md#test-the-application). Se persistirem problemas de inscrição, consulte [como resolver erros de inscrição](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).  |
|3 | Abra um navegador e tente aceder à app | Se aparecer imediatamente um erro, verifique se o Proxy da Aplicação está configurado corretamente. Para mais detalhes sobre mensagens de erro específicas, consulte problemas de [procuração de aplicação de resolução de problemas e mensagens de erro](application-proxy-troubleshoot.md).  |
|4 | Verifique a configuração do seu domínio personalizado ou atire problemas ao erro | Se a página não for apresentada de todo, certifique-se de que o seu domínio personalizado está configurado corretamente através da revisão [do Trabalho com domínios personalizados](application-proxy-configure-custom-domain.md).<br></br>Se a página não carregar e aparecer uma mensagem de erro, problemas resolvem o erro referindo-se aos problemas de procuração de aplicação de resolução de [problemas e mensagens de erro](application-proxy-troubleshoot.md). <br></br>Se demorar mais de 20 segundos para aparecer uma mensagem de erro, pode haver problema de conectividade. Vá ao artigo de resolução de problemas dos [conectores de aplicação de depuração.](application-proxy-debug-connectors.md)  |
|5 | Se as questões persistirem, vá para a depuração do conector | Pode haver um problema de conectividade entre o proxy e o conector ou entre o conector e a parte de trás. Vá ao artigo de resolução de problemas dos [conectores de aplicação de depuração.](application-proxy-debug-connectors.md) |
|6 | Publique todos os recursos, verifique as ferramentas de desenvolvimento do navegador e corrija links | Certifique-se de que o caminho editorial inclui todas as imagens, scripts e folhas de estilo necessárias para a sua aplicação. Para mais detalhes, consulte [Adicionar uma aplicação no local ao Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). <br></br>Utilize as ferramentas de desenvolvimento do navegador (ferramentas F12 no Internet Explorer ou Microsoft Edge) e verifique se os problemas de publicação descritos na [página da Aplicação não se exibem corretamente](application-proxy-page-appearance-broken-problem.md). <br></br>As opções de revisão para a resolução de links quebrados em [Links na página não funcionam](application-proxy-page-links-broken-problem.md). |
|7 | Verifique a latência da rede | Se a página carregar lentamente, aprenda sobre formas de minimizar a latência da rede em [Considerações para reduzir a latência](application-proxy-network-topology.md#considerations-for-reducing-latency). | 
|8 | Consulte ajuda adicional para resolução de problemas | Se persistirem problemas, encontre artigos adicionais de resolução de problemas na documentação de resolução de [problemas do Proxy](application-proxy-troubleshoot.md)de Aplicação . |

## <a name="next-steps"></a>Passos seguintes


* [Publicar aplicações em redes e locais separados utilizando grupos de conector](application-proxy-connector-groups.md)
* [Trabalhar com servidores proxy existentes no local](application-proxy-configure-connectors-with-proxy-servers.md)
* [Erro de procuração de aplicação de resolução de problemas e erro de conector](application-proxy-troubleshoot.md)
* [Como instalar silenciosamente o Conector proxy de aplicação ad azure](application-proxy-register-connector-powershell.md)
