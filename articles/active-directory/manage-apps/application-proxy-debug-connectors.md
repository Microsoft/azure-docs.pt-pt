---
title: Depurar conectores de proxy de aplicativo-Azure Active Directory | Microsoft Docs
description: Problemas de depuração com conectores de proxy de aplicativo Azure Active Directory (Azure AD).
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
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311844"
---
# <a name="debug-application-proxy-connector-issues"></a>Depurar problemas do conector de proxy de aplicativo 

Este artigo ajuda você a solucionar problemas com os conectores de proxy de aplicativo do Azure Active Directory (Azure AD). Se você estiver usando o serviço de proxy de aplicativo para acesso remoto a um aplicativo Web local, mas estiver tendo problemas para se conectar ao aplicativo, use este fluxograma para depurar problemas de conector. 

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você instalou o conector de proxy de aplicativo e está tendo um problema. Ao solucionar problemas de proxy de aplicativo, recomendamos que você comece com esse fluxo de solução de problemas para determinar se os conectores de proxy de aplicativo estão configurados corretamente. Se você ainda estiver tendo problemas para se conectar ao aplicativo, siga o fluxo de solução de problemas em depurar aplicativos de [proxy de aplicativo](application-proxy-debug-apps.md).  


Para obter mais informações sobre o proxy de aplicativo e o uso de seus conectores, consulte:

- [Acesso remoto a aplicativos locais por meio do proxy de aplicativo](application-proxy.md)
- [Conectores de proxy de aplicativo](application-proxy-connectors.md)
- [Instalar e registrar um conector](application-proxy-add-on-premises-application.md)
- [Solucionar problemas de proxy de aplicativo e mensagens de erro](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Fluxograma para problemas de conector

Este fluxograma orienta você pelas etapas para depurar alguns dos problemas mais comuns do conector. Para obter detalhes sobre cada etapa, consulte a tabela após o fluxograma.

![Fluxograma mostrando as etapas para depurar um conector](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | Ação | Descrição | 
|---------|---------|---------|
|1 | Localizar o grupo de conectores atribuído ao aplicativo | Você provavelmente tem um conector instalado em vários servidores; nesse caso, os conectores devem ser [atribuídos a grupos de conectores](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups). Para saber mais sobre grupos de conectores, confira [publicar aplicativos em redes e locais separados usando grupos de conectores](application-proxy-connector-groups.md). |
|2 | Instalar o conector e atribuir um grupo | Se você não tiver um conector instalado, consulte [instalar e registrar um conector](application-proxy-add-on-premises-application.md#install-and-register-a-connector).<br></br> Se você estiver tendo problemas ao instalar o conector, consulte [problema ao instalar o conector](application-proxy-connector-installation-problem.md).<br></br> Se o conector não estiver atribuído a um grupo, consulte [atribuir o conector a um grupo](application-proxy-connector-groups.md#create-connector-groups).<br></br>Se o aplicativo não estiver atribuído a um grupo de conectores, consulte [atribuir o aplicativo a um grupo de conectores](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups).|
|3 | Executar um teste de porta no servidor do conector | No servidor do conector, execute um teste de porta usando [Telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) ou outra ferramenta de teste de porta para verificar se as portas 443 e 80 estão abertas.|
|4 | Configurar os domínios e as portas | [Verifique se os domínios e as portas estão configurados corretamente](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) Para que o conector funcione corretamente, há determinadas portas que devem ser abertas e URLs que o servidor deve poder acessar. |
|5 | Verificar se um proxy de back-end está em uso | Verifique se os conectores estão usando servidores proxy de back-end ou ignorando-os. Para obter detalhes, consulte [solucionar problemas de proxy do conector e problemas de conectividade do serviço](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues). |
|6 | Atualizar o conector e o atualizador para usar o proxy de back-end | Se um proxy de back-end estiver em uso, você desejará verificar se o conector está usando o mesmo proxy. Para obter detalhes sobre como solucionar problemas e configurar conectores para trabalhar com servidores proxy, consulte [trabalhar com servidores proxy locais existentes](application-proxy-configure-connectors-with-proxy-servers.md). |
|7 | Carregar a URL interna do aplicativo no servidor do conector | No servidor do conector, carregue a URL interna do aplicativo. |
|8 | Verificar conectividade de rede interna | Há um problema de conectividade em sua rede interna que esse fluxo de depuração não pode diagnosticar. O aplicativo deve ser acessível internamente para que os conectores funcionem. Você pode habilitar e exibir os logs de eventos do conector conforme descrito em [conectores de proxy de aplicativo](application-proxy-connectors.md#under-the-hood). |
|9 | Aumentar o valor de tempo limite no back-end | Nas **configurações adicionais** para seu aplicativo, altere a configuração de **tempo limite do aplicativo back-end** para **longa**. Consulte [Adicionar um aplicativo local ao Azure ad](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). |
|10 | Se os problemas persistirem, direcione os problemas de fluxo específicos, examine os fluxos de depuração de aplicativo e SSO | Use o fluxo de solução de problemas de [aplicativos de proxy de aplicativo de depuração](application-proxy-debug-apps.md) . |

## <a name="next-steps"></a>Passos seguintes


* [Publicar aplicativos em redes e locais separados usando grupos de conectores](application-proxy-connector-groups.md)
* [Trabalhar com servidores proxy locais existentes](application-proxy-configure-connectors-with-proxy-servers.md)
* [Solucionar problemas de proxy de aplicativo e erros de conector](application-proxy-troubleshoot.md)
* [Como instalar silenciosamente o conector de Proxy de Aplicativo do AD do Azure](application-proxy-register-connector-powershell.md)
