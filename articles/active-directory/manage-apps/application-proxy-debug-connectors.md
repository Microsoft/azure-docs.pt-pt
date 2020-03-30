---
title: Conectores Proxy de Aplicação Debug - Diretório Ativo Azure / Microsoft Docs
description: Problemas de depuração com conectores proxy de aplicação azure Ative (Azure AD).
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
ms.openlocfilehash: c041578932bd33eb0a2d3afc18a35c2c0458dc8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72311844"
---
# <a name="debug-application-proxy-connector-issues"></a>Depurar problemas de conectores do Proxy de Aplicações 

Este artigo ajuda-o a resolver problemas com conectores de proxy de aplicação do Azure Ative Directory (Azure AD). Se estiver a utilizar o serviço Proxy de Aplicação para acesso remoto a uma aplicação web no local, mas está com problemas de ligação à aplicação, utilize este fluxograma para depurar problemas de conector. 

## <a name="before-you-begin"></a>Antes de começar

Este artigo assume que instalou o conector Proxy de Aplicação e está a ter um problema. Ao resolver problemas de procuração de aplicação, recomendamos que comece com este fluxo de resolução de problemas para determinar se os conectores proxy da aplicação estão configurados corretamente. Se ainda tiver problemas em ligar-se à aplicação, siga o fluxo de resolução de problemas nos [problemas de aplicação de Proxy de Aplicação de Aplicação de Debug](application-proxy-debug-apps.md).  


Para obter mais informações sobre o Proxy de Aplicação e utilizando os seus conectores, consulte:

- [Acesso remoto a aplicações no local através de Procuração de Aplicações](application-proxy.md)
- [Conectores proxy de aplicação](application-proxy-connectors.md)
- [Instalar e registar um conector](application-proxy-add-on-premises-application.md)
- [Problemas de procuração de aplicação de problemas e mensagens de erro](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Flowchart para problemas de conector

Este fluxograma acompanha-o através dos degraus para depurar algumas das questões mais comuns do conector. Para mais detalhes sobre cada passo, consulte a tabela seguindo o fluxograma.

![Flowchart mostrando passos para depurar um conector](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | Ação | Descrição | 
|---------|---------|---------|
|1 | Encontre o grupo de conector atribuído à app | Provavelmente tem um conector instalado em vários servidores, caso em que os conectores devem ser [atribuídos a grupos de conectores](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups). Para saber mais sobre grupos de conectores, consulte [Publicar aplicações em redes e locais separados utilizando grupos de conector](application-proxy-connector-groups.md). |
|2 | Instale o conector e atribua um grupo | Se não tiver um conector instalado, consulte [Instalar e registar um conector](application-proxy-add-on-premises-application.md#install-and-register-a-connector).<br></br> Se tiver problemas na instalação do conector, consulte [problema de instalação do Conector](application-proxy-connector-installation-problem.md).<br></br> Se o conector não for atribuído a um grupo, consulte [atribuir o conector a um grupo](application-proxy-connector-groups.md#create-connector-groups).<br></br>Se a aplicação não for atribuída a um grupo de conector, consulte atribuir a aplicação a um grupo de [conector](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups).|
|3 | Efetue um teste de porta no servidor do conector | No servidor do conector, faça um teste de porta utilizando [telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) ou outra ferramenta de teste de porta para verificar se as portas 443 e 80 estão abertas.|
|4 | Configure os domínios e portas | [Certifique-se de que os seus domínios e portas estão configurados corretamente](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) Para que o conector funcione corretamente, existem certas portas que devem estar abertas e URLs a que o servidor deve ser capaz de aceder. |
|5 | Verifique se um proxy de back-end está em uso | Verifique se os conectores estão a utilizar servidores proxy de back-end ou a convidá-los. Para mais detalhes, consulte problemas de procuração de [conectores de Problemas e problemas](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues)de conectividade de serviço . |
|6 | Atualize o conector e o atualizador para utilizar o proxy back-end | Se estiver a ser utilizado um proxy de back-end, deverá certificar-se de que o conector está a usar o mesmo proxy. Para mais detalhes sobre a resolução de problemas e configuração de conectores para trabalhar com servidores proxy, consulte [Trabalhar com servidores proxy existentes no local](application-proxy-configure-connectors-with-proxy-servers.md). |
|7 | Carregue o URL interno da aplicação no servidor do conector | No servidor do conector, carregue o URL interno da aplicação. |
|8 | Verifique a conectividade interna da rede | Há um problema de conectividade na sua rede interna que este fluxo de depuração é incapaz de diagnosticar. A aplicação deve ser acessível internamente para que os conectores funcionem. Pode ativar e visualizar registos de eventos de conectores descritos nos [conectores proxy](application-proxy-connectors.md#under-the-hood)da aplicação . |
|9 | Prolonge o valor de tempo na parte de trás | Nas **Definições Adicionais** para a sua aplicação, altere a definição de tempo de **aplicação backend** para **Long**. Consulte [adicionar uma aplicação no local ao Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). |
|10 | Se persistirem problemas, temas específicos de fluxo, app de revisão e fluxos de depuração SSO | Utilize a [aplicação Proxy de aplicação de depuração](application-proxy-debug-apps.md) de aplicações de problemas. |

## <a name="next-steps"></a>Passos seguintes


* [Publicar aplicações em redes e locais separados utilizando grupos de conector](application-proxy-connector-groups.md)
* [Trabalhar com servidores proxy existentes no local](application-proxy-configure-connectors-with-proxy-servers.md)
* [Erro de procuração de aplicação de resolução de problemas e erro de conector](application-proxy-troubleshoot.md)
* [Como instalar silenciosamente o Conector proxy de aplicação ad azure](application-proxy-register-connector-powershell.md)
