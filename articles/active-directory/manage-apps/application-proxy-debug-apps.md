---
title: Aplicações de Procuração de Aplicações Debug - Azure Ative Directory / Microsoft Docs
description: Problemas de depuração com aplicações de procuração de aplicações Azure Ative (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/21/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: e61ea30f01e8ae141d24c9bd91b08edef4dbe74e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85555064"
---
# <a name="debug-application-proxy-application-issues"></a>Depurar problemas de aplicações do Proxy de Aplicações 

Este artigo ajuda-o a resolver problemas com aplicações de procuração de aplicações Azure Ative (Azure AD). Se estiver a utilizar o serviço Application Proxy para acesso remoto a uma aplicação web no local, mas está com dificuldades em ligar-se à aplicação, use este fluxograma para depurar problemas de aplicação. 

## <a name="before-you-begin"></a>Antes de começar

Ao resolver problemas problemas de aplicação proxy, recomendamos que comece com os conectores. Em primeiro lugar, siga o fluxo de resolução de problemas nos [problemas do Conector Proxy da aplicação de depuração](application-proxy-debug-connectors.md) para garantir que os conectores Proxy da aplicação estão configurados corretamente. Se ainda tiver problemas, volte a este artigo para resolver problemas na aplicação.  

Para obter mais informações sobre o Application Proxy, consulte:

- [Acesso remoto a aplicações no local através do Application Proxy](application-proxy.md)
- [Conectores Proxy de aplicação](application-proxy-connectors.md)
- [Instalar e registar um conector](application-proxy-add-on-premises-application.md)
- [Resolver problemas do Proxy de Aplicações e as mensagens de erro](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Fluxograma para problemas de aplicação

Este fluxograma acompanha-o através dos passos para depurar algumas das questões mais comuns com a ligação à aplicação. Para mais detalhes sobre cada passo, consulte a tabela seguindo o fluxograma.

![Flowchart mostrando passos para depurar uma aplicação](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

| Passo | Ação | Descrição |
|---------|---------|---------|
|1 | Abra um navegador, aceda à app e insira as suas credenciais | Tente usar as suas credenciais para iniciar seduca na aplicação e verifique se há erros relacionados com o utilizador, como [esta aplicação corporativa não pode ser acedida.](application-proxy-sign-in-bad-gateway-timeout-error.md) |
|2 | Verifique a atribuição do utilizador à aplicação | Certifique-se de que a sua conta de utilizador tem permissão para aceder à aplicação a partir de dentro da rede corporativa e, em seguida, testar a sessão na aplicação seguindo os passos no [Teste da aplicação.](application-proxy-add-on-premises-application.md#test-the-application) Se persistirem problemas de inscrição, consulte [como resolver os erros de inscrição de problemas](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).  |
|3 | Abra um navegador e tente aceder à app | Se aparecer imediatamente um erro, verifique se o Proxy da aplicação está configurado corretamente. Para obter detalhes sobre mensagens de erro específicas, consulte [problemas de procuração de aplicação de resolução de problemas e mensagens de erro](application-proxy-troubleshoot.md).  |
|4 | Verifique a configuração de domínio personalizado ou resolva o erro | Se a página não apresentar, certifique-se de que o seu domínio personalizado está configurado corretamente, revendo [o Trabalho com domínios personalizados](application-proxy-configure-custom-domain.md).<br></br>Se a página não carregar e aparecer uma mensagem de erro, resolva o erro referindo-se aos problemas de procuração de  [aplicação de resolução de problemas e mensagens de erro](application-proxy-troubleshoot.md). <br></br>Se demorar mais de 20 segundos para aparecer uma mensagem de erro, pode haver problema de conectividade. Aceda ao artigo de resolução de problemas da [Debug Application Proxy.](application-proxy-debug-connectors.md)  |
|5 | Se as questões persistirem, vá para a depuragem do conector | Pode haver um problema de conectividade entre o proxy e o conector ou entre o conector e a extremidade traseira. Aceda ao artigo de resolução de problemas da [Debug Application Proxy.](application-proxy-debug-connectors.md) |
|6 | Publique todos os recursos, verifique as ferramentas de desenvolvedor de navegador e corrija links | Certifique-se de que o caminho de publicação inclui todas as imagens, scripts e folhas de estilo necessários para a sua aplicação. Para mais detalhes, consulte [uma aplicação no local ao Azure AD.](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) <br></br>Utilize as ferramentas de desenvolvimento do navegador (ferramentas F12 no Internet Explorer ou Microsoft Edge) e verifique se os problemas de publicação descritos na [página de Aplicação não são apresentados corretamente](application-proxy-page-appearance-broken-problem.md). <br></br>As opções de revisão para resolver links [quebrados em Links na página não funcionam](application-proxy-page-links-broken-problem.md). |
|7 | Verifique a latência da rede | Se a página carregar lentamente, aprenda sobre formas de minimizar a latência da rede em [Considerações para reduzir a latência](application-proxy-network-topology.md#considerations-for-reducing-latency). | 
|8 | Consulte ajuda adicional para a resolução de problemas | Se as questões persistirem, encontre artigos adicionais de resolução de problemas na [documentação de resolução de problemas da Aplicação Proxy](application-proxy-troubleshoot.md). |

## <a name="next-steps"></a>Passos seguintes


* [Publicar aplicações em redes e locais separados utilizando grupos de conector](application-proxy-connector-groups.md)
* [Trabalhar com servidores proxy existentes no local](application-proxy-configure-connectors-with-proxy-servers.md)
* [Erros de procuração e conector de aplicação de resolução de problemas](application-proxy-troubleshoot.md)
* [Como instalar silenciosamente o Conector Proxy da Aplicação AD AZure](application-proxy-register-connector-powershell.md)
