---
title: Conectores de procuração de aplicação de depurg - Azure Ative Directory | Microsoft Docs
description: Problemas de depuração com conectores de procuração de aplicação Azure Ative (Azure AD).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/21/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: c656bfe7f9cc8923859a99f31210cd554aa66a41
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99256971"
---
# <a name="debug-application-proxy-connector-issues"></a>Depurar problemas de conectores do Proxy de Aplicações 

Este artigo ajuda-o a resolver problemas com conectores de aplicação Proxy da aplicação Azure Ative(Azure AD). Se estiver a utilizar o serviço Application Proxy para acesso remoto a uma aplicação web no local, mas está com dificuldades em ligar-se à aplicação, use este fluxograma para depurar problemas de conector. 

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que instalou o conector Application Proxy e está com um problema. Ao resolver problemas problemas de aplicação Proxy, recomendamos que comece com este fluxo de resolução de problemas para determinar se os conectores Proxy da aplicação estão configurados corretamente. Se ainda tiver problemas em ligar-se à aplicação, siga o fluxo de resolução de problemas em problemas de [aplicação de aplicação de depuração](application-proxy-debug-apps.md).  


Para obter mais informações sobre o Application Proxy e utilizar os seus conectores, consulte:

- [Acesso remoto a aplicações no local através do Application Proxy](application-proxy.md)
- [Conectores Proxy de aplicação](application-proxy-connectors.md)
- [Instalar e registar um conector](application-proxy-add-on-premises-application.md)
- [Resolver problemas do Proxy de Aplicações e as mensagens de erro](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Fluxograma para problemas de conector

Este fluxograma acompanha-o através dos passos para depurar alguns dos problemas de conector mais comuns. Para mais detalhes sobre cada passo, consulte a tabela seguindo o fluxograma.

![Fluxograma mostrando passos para depurar um conector](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

| Passo | Ação | Descrição |
|---------|---------|---------|
|1 | Encontre o grupo de conector atribuído à app | Provavelmente tem um conector instalado em vários servidores, caso em que os conectores devem ser [atribuídos a grupos de conector](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups). Para saber mais sobre grupos de conectores, consulte [publicar aplicações em redes e locais separados utilizando grupos de conectores](application-proxy-connector-groups.md). |
|2 | Instale o conector e atribua um grupo | Se não tiver um conector instalado, consulte [instalar e registar um conector](application-proxy-add-on-premises-application.md#install-and-register-a-connector).<br></br> Se tiver problemas na instalação do conector, consulte [o Problema de instalação do Conector](application-proxy-connector-installation-problem.md).<br></br> Se o conector não for atribuído a um grupo, consulte [atribuir o conector a um grupo](application-proxy-connector-groups.md#create-connector-groups).<br></br>Se a aplicação não for atribuída a um grupo de conector, consulte [atribuir a aplicação a um grupo de conector](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups).|
|3 | Realizar um teste de porta no servidor do conector | No servidor do conector, faça um teste de porta utilizando [telnet](/windows-server/administration/windows-commands/telnet) ou outra ferramenta de teste de porta para verificar se as portas [443 e 80 estão abertas](application-proxy-add-on-premises-application.md#open-ports).|
|4 | Configure os domínios e portas | [Certifique-se de que os seus domínios e portas estão configurados corretamente](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) Para que o conector funcione corretamente, existem certas portas que devem estar abertas e URLs a que o seu servidor deve ter acesso. |
|5 | Verifique se um representante de back-end está a ser utilizado | Verifique se os conectores estão a utilizar servidores de procuração de back-end ou a contorná-los. Para mais informações, consulte [problemas de procuração de conector de resolução de problemas e problemas de conectividade de serviço](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues). |
|6 | Atualize o conector e o atualizador para utilizar o proxy back-end | Se um representante de back-end estiver a ser utilizado, deverá certificar-se de que o conector está a usar o mesmo representante. Para obter detalhes sobre a resolução de problemas e configurar conectores para trabalhar com servidores proxy, consulte [Trabalhar com servidores proxy existentes no local](application-proxy-configure-connectors-with-proxy-servers.md). |
|7 | Carregue o URL interno da aplicação no servidor do conector | No servidor do conector, carregue o URL interno da aplicação. |
|8 | Verifique a conectividade interna da rede | Há um problema de conectividade na sua rede interna que este fluxo de depuragem é incapaz de diagnosticar. A aplicação deve ser acessível internamente para que os conectores funcionem. Pode ativar e visualizar registos de eventos de conector, conforme descrito nos [conectores de procuração de aplicação](application-proxy-connectors.md#under-the-hood). |
|9 | Alonge o valor de tempo na parte de trás | Nas **Definições Adicionais** para a sua aplicação, altere a definição **de tempo de aplicação backend** para **Long**. Consulte [uma aplicação no local ao Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). |
|10 | Se as questões persistirem, direcione problemas específicos de fluxo, app de revisão e fluxos de depurações SSO | Utilize a [aplicação Debug Application Proxy que emite](application-proxy-debug-apps.md) fluxo de resolução de problemas. |

## <a name="next-steps"></a>Passos seguintes


* [Publicar aplicações em redes e locais separados utilizando grupos de conector](application-proxy-connector-groups.md)
* [Trabalhar com servidores proxy existentes no local](application-proxy-configure-connectors-with-proxy-servers.md)
* [Erros de procuração e conector de aplicação de resolução de problemas](application-proxy-troubleshoot.md)
* [Como instalar silenciosamente o Conector Proxy da Aplicação AD AZure](application-proxy-register-connector-powershell.md)