---
title: Acesso remoto a aplicativos locais – Azure Proxy de Aplicativo do AD
description: O proxy de aplicativo do Azure Active Directory fornece acesso remoto seguro a aplicativos Web locais. Após um logon único no Azure AD, os usuários podem acessar aplicativos na nuvem e no local por meio de uma URL externa ou um portal de aplicativo interno. Por exemplo, o proxy de aplicativo pode fornecer acesso remoto e logon único para Área de Trabalho Remota, SharePoint, Teams, tableau, Qlik Sense e aplicativos LOB (linha de negócios).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4780786f0caea2c211b6b93fb0736feaade8de80
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274838"
---
# <a name="remote-access-to-on-premises-applications-through-azure-active-directorys-application-proxy"></a>Acesso remoto a aplicativos locais por meio do proxy de aplicativo Azure Active Directory 

O proxy de aplicativo do Azure Active Directory fornece acesso remoto seguro a aplicativos Web locais. Após um logon único no Azure AD, os usuários podem acessar aplicativos na nuvem e no local por meio de uma URL externa ou um portal de aplicativo interno. Por exemplo, o proxy de aplicativo pode fornecer acesso remoto e logon único para Área de Trabalho Remota, SharePoint, Teams, tableau, Qlik Sense e aplicativos LOB (linha de negócios).

Proxy de aplicações do Azure AD é:

- **Simples de usar**. Os usuários podem acessar seus aplicativos locais da mesma forma que acessam o O365 e outros aplicativos SaaS integrados ao Azure AD. Não precisa de alterar ou atualizar seus aplicativos para trabalhar com o Proxy de aplicações. 

- **Proteger**. Os aplicativos locais podem usar os controles de autorização do Azure e a análise de segurança. Por exemplo, aplicativos locais podem usar o acesso condicional e a verificação em duas etapas. O proxy de aplicativo não exige que você abra conexões de entrada por meio do firewall.
 
- **Econômico.** As soluções locais normalmente exigem que você configure e mantenha zonas desmilitarizadas (DMZs), servidores de borda ou outras infraestruturas complexas. O proxy de aplicativo é executado na nuvem, o que o torna fácil de usar. Para usar o proxy de aplicativo, você não precisa alterar a infraestrutura de rede nem instalar dispositivos adicionais no seu ambiente local.

## <a name="what-is-application-proxy"></a>O que é o Proxy de Aplicação?
O proxy de aplicativo é um recurso do Azure AD que permite que os usuários acessem aplicativos Web locais por meio de um cliente remoto. O proxy de aplicativo inclui o serviço de proxy de aplicativo que é executado na nuvem e o conector de proxy de aplicativo que é executado em um servidor local. O Azure AD, o serviço de proxy de aplicativo e o conector de proxy de aplicativo trabalham juntos para passar com segurança o token de logon do usuário do Azure AD para o aplicativo Web.

O proxy de aplicativo funciona com:

* Aplicativos Web que usam a [autenticação integrada do Windows](application-proxy-configure-single-sign-on-with-kcd.md) para autenticação  
* Aplicativos Web que usam acesso baseado em formulário ou em [cabeçalho](application-proxy-configure-single-sign-on-with-ping-access.md)  
* APIs que deseja expor para aplicativos avançados em diferentes dispositivos para a Web  
* Aplicativos hospedados atrás de um [Gateway de área de trabalho remota](application-proxy-integrate-with-remote-desktop-services.md)  
* Aplicações de cliente rico que estão integradas com o Active Directory Authentication Library (ADAL)

O proxy de aplicativo dá suporte ao logon único. Para obter mais informações sobre os métodos com suporte, consulte [escolhendo um método de logon único](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

O proxy de aplicativo é recomendado para conceder aos usuários remotos acesso a recursos internos. O proxy de aplicativo substitui a necessidade de um proxy reverso ou VPN. Ele não se destina a usuários internos na rede corporativa.  Esses usuários que desnecessariamente usam o proxy de aplicativo podem introduzir problemas de desempenho inesperados e indesejáveis.

## <a name="how-application-proxy-works"></a>Como o proxy de aplicativo funciona

O diagrama a seguir mostra como o Azure AD e o proxy de aplicativo trabalham juntos para fornecer logon único a aplicativos locais.

![Diagrama de Proxy de aplicações do AzureAD](./media/application-proxy/azureappproxxy.png)

1. Depois do usuário acessa o aplicativo por meio de um ponto de extremidade, o utilizador é direcionado para a página de início de sessão do Azure AD. 
2. Após uma entrada bem-sucedida, o Azure AD envia um token para o dispositivo cliente do usuário.
3. O cliente envia o token para o serviço de proxy de aplicativo, que recupera o nome principal do usuário (UPN) e o SPN (nome da entidade de segurança) do token. Em seguida, o proxy de aplicativo envia a solicitação ao conector de proxy de aplicativo.
4. Se tiver configurado o início de sessão único, o conector efetua qualquer autenticação adicional necessária em nome do utilizador.
5. O conector envia o pedido para a aplicação no local.  
6. A resposta é enviada por meio do conector do e do serviço de proxy de aplicativo para o usuário.

| Componente | Descrição |
| --------- | ----------- |
| Ponto Final  | O ponto de extremidade é uma URL ou um [portal do usuário final](end-user-experiences.md). Os utilizadores possam contactar aplicativos enquanto fora da sua rede ao aceder a um URL externo. Os utilizadores na sua rede possam aceder à aplicação através de um URL ou um portal de utilizador final. Quando os utilizadores aceder a um desses pontos de extremidade, eles se autenticar no Azure AD e, em seguida, são encaminhados através do conector para o aplicativo no local.|
| Azure AD | O AD do Azure executa a autenticação usando o diretório de locatário armazenado na nuvem. |
| Serviço de proxy de aplicativo | Esse serviço de proxy de aplicativo é executado na nuvem como parte do Azure AD. Ele passa o token de logon do usuário para o conector de proxy de aplicativo. O proxy de aplicativo encaminha todos os cabeçalhos acessíveis na solicitação e define os cabeçalhos de acordo com seu protocolo para o endereço IP do cliente. Se a solicitação de entrada para o proxy já tiver esse cabeçalho, o endereço IP do cliente será adicionado ao final da lista separada por vírgulas que é o valor do cabeçalho.|
| Conector de proxy de aplicativo | O conector é um agente leve que é executado em um Windows Server dentro de sua rede. O conector gerencia a comunicação entre o serviço de proxy de aplicativo na nuvem e o aplicativo local. O conector usa somente conexões de saída, de modo que você não precisa abrir nenhuma porta de entrada nem colocar nada na DMZ. Os conectores são sem monitoração de estado e extraem informações a partir da cloud conforme necessário. Para obter mais informações sobre conectores, como o balanceamento de carga e a autenticação, consulte [entender os conectores de proxy de aplicativo do AD do Azure](application-proxy-connectors.md).|
| Active Directory (AD) | Active Directory é executado localmente para realizar a autenticação para contas de domínio. Quando o logon único é configurado, o conector se comunica com o AD para executar qualquer autenticação adicional necessária.
| Aplicação no local | Por fim, o usuário é capaz de acessar um aplicativo local. 

## <a name="next-steps"></a>Passos seguintes
Para começar a usar o proxy de aplicativo, consulte [tutorial: adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo](application-proxy-add-on-premises-application.md). 

Para obter as últimas notícias e atualizações, consulte o [blog do proxy de aplicativo](https://blogs.technet.com/b/applicationproxyblog/)


