---
title: Depurar conectores de Proxy de aplicações - Azure Active Directory | Documentos da Microsoft
description: Depure problemas com os conectores de Proxy de aplicações do Azure Active Directory (Azure AD).
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
ms.openlocfilehash: c3088ae777fe1a64be218105d36fdb9e01d7b798
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66172239"
---
# <a name="debug-application-proxy-connector-issues"></a>Depurar problemas de conector de Proxy de aplicações 

Este artigo ajuda-o a resolver problemas com conectores de Proxy de aplicações do Azure Active Directory (Azure AD). Se estiver a utilizar o serviço de Proxy de aplicações para acesso remoto a uma aplicação de web no local, mas estiver a ter problemas em ligar à aplicação, utilize este fluxograma para depurar problemas de conector. 

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que instalou o conector do Proxy de aplicações e que está a ter um problema. Quando a resolução de problemas de Proxy de aplicações, recomendamos que comece com este fluxo de resolução de problemas para determinar se os conectores de Proxy de aplicações estão configuradas corretamente. Se ainda estiver a ter problemas em ligar à aplicação, siga o fluxo de resolução de problemas na [Proxy de aplicações de depurar problemas de aplicativos](application-proxy-debug-apps.md).  


Para obter mais informações sobre o Proxy de aplicações e utilizar os seus conectores, consulte:

- [Acesso remoto às aplicações no local através do Proxy de aplicações](application-proxy.md)
- [Conectores de Proxy de aplicação](application-proxy-connectors.md)
- [Instalar e registar um conector](application-proxy-add-on-premises-application.md)
- [Resolver problemas do Proxy de aplicações e mensagens de erro](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Fluxograma de problemas do conector

Este fluxograma orienta-o através dos passos para depuração alguns dos problemas mais comuns do conector. Para obter detalhes sobre cada passo, consulte a tabela a seguir o fluxograma.

![Fluxograma que mostra os passos para a depuração de um conector](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | Acção | Descrição | 
|---------|---------|---------|
|1 | Encontrar o grupo de conectores atribuído à aplicação | Provavelmente terá um conector instalado em vários servidores, caso em que os conectores devem estar [atribuídas a grupos de conector](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups). Para saber mais sobre os grupos de conector, veja [publicar aplicações em redes separadas e localizações de utilização de grupos de conector](application-proxy-connector-groups.md). |
|2 | Instalar o conector e atribuir um grupo | Se não tiver um conector instalado, veja [instale e registe um conector](application-proxy-add-on-premises-application.md#install-and-register-a-connector).<br></br>Se o conector não está atribuído a um grupo, consulte [atribuir o conector a um grupo](application-proxy-connector-groups.md#create-connector-groups).<br></br>Se a aplicação não está atribuída a um grupo de conectores, consulte [atribuir a aplicação a um grupo de conectores](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups).|
|3 | Executar um teste de porta no servidor do conector | No servidor do conector, execute um teste de porta utilizando [telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) ou outros testes de porta ferramenta para verificar se as portas 443 e 80 estão abertas.|
|4 | Configurar as portas e domínios | [Certifique-se de que seus domínios e as portas estão configuradas corretamente](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) para o conector funcione corretamente, há determinadas portas que tem de estar abertas e os URLs que o servidor tem de ser capaz de aceder. |
|5 | Verifique se um proxy de back-end está em utilização | Verifique se os conectores estão a utilizar servidores de proxy de back-end ou ignorando-los. Para obter detalhes, consulte [resolver problemas do proxy de conector e problemas de conectividade do serviço](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues). |
|6 | Atualizar o conector e o atualizador para usar o proxy de back-end | Se um proxy de back-end está a ser utilizado, desejará para se certificar de que o conector está a utilizar o proxy do mesmo. Para obter detalhes sobre a resolução de problemas e configuração de conectores para trabalhar com servidores proxy, consulte [funcionam com o existente no local servidores proxy](application-proxy-configure-connectors-with-proxy-servers.md). |
|7 | Carregar o URL da aplicação interno no servidor do conector | No servidor do conector, carregar o URL interno da aplicação. |
|8 | Verifique a conectividade de rede interna | Existe um problema de conectividade na sua rede interna que este fluxo de depuração não é possível ao diagnosticar. A aplicação tem de estar acessível internamente para os conectores trabalhar. Pode ativar e ver registos de eventos do conector, conforme descrito em [conectores de Proxy de aplicações](application-proxy-connectors.md#under-the-hood). |
|9 | Aumentar o valor de tempo limite no back-end | Na **definições adicionais** para a sua aplicação, altere a **tempo limite da aplicação de back-end** na definição **longo**. Ver [adicionar uma aplicação no local ao Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). |
|10 | Se os problemas persistirem, direcionar os problemas de fluxo específico, a SSO depuração fluxos e aplicações de revisão | Utilize o [Proxy de aplicações de depurar problemas de aplicativos](application-proxy-debug-apps.md) resolução de problemas de fluxo. |

## <a name="next-steps"></a>Passos Seguintes


* [Publicar aplicações em redes separadas e localizações através de grupos de conectores](application-proxy-connector-groups.md)
* [Trabalhar com servidores de proxy no local existentes](application-proxy-configure-connectors-with-proxy-servers.md)
* [Resolver erros de Proxy de aplicações e o conector](application-proxy-troubleshoot.md)
* [Como instalar automaticamente o conector de Proxy de aplicações do Azure AD](application-proxy-register-connector-powershell.md)
