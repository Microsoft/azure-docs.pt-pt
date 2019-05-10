---
title: Acesso remoto às aplicações no local - Proxy de aplicações do Azure Active Directory | Microsoft Docx
description: Proxy de aplicações do Azure do Active Directory fornece acesso remoto seguro a aplicações web no local. Após um início de sessão único para o Azure AD, os utilizadores podem aceder aplicações na cloud e no local através de um URL externo ou um portal de aplicação interna. Por exemplo, o Proxy da aplicação pode fornecer acesso remoto e início de sessão único para o ambiente de trabalho remoto, SharePoint, Teams, Tableau, Qlik e aplicativos linha de negócios (LOB).
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: celested
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: e490ed9d104fc039a79083a94ddaebdeba766d2a
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506645"
---
# <a name="remote-access-to-on-premises-applications-through-azure-active-directorys-application-proxy"></a>Acesso remoto às aplicações no local através do Proxy de aplicações do Azure Active Directory 

Proxy de aplicações do Azure do Active Directory fornece acesso remoto seguro a aplicações web no local. Após um início de sessão único para o Azure AD, os utilizadores podem aceder aplicações na cloud e no local através de um URL externo ou um portal de aplicação interna. Por exemplo, o Proxy da aplicação pode fornecer acesso remoto e início de sessão único para o ambiente de trabalho remoto, SharePoint, Teams, Tableau, Qlik e aplicativos linha de negócios (LOB).

Proxy de aplicações do Azure AD é:

- **Simples de usar**. Os utilizadores podem aceder as aplicações no local da mesma forma que aceder ao Office 365 e outras aplicações SaaS integradas com o Azure AD. Não precisa de alterar ou atualizar seus aplicativos para trabalhar com o Proxy de aplicações. 

- **Proteger**. Aplicações no local podem utilizar os controlos de autorização e análises de segurança do Azure. Por exemplo, as aplicações no local podem utilizar o acesso condicional e verificação de dois passos. Proxy de aplicações não requer a abertura de ligações de entrada através da firewall.
 
- **Económico**. Soluções no local, normalmente, tem de configurar e manter desmilitarizada zonas (DMZs), os servidores de borda ou outras infraestruturas complexas. Proxy da aplicação é executada na cloud, o que torna fácil de usar. Para utilizar o Proxy de aplicações, não precisa de alterar a infraestrutura de rede ou instalar aplicações adicionais no seu ambiente no local.

## <a name="what-is-application-proxy"></a>O que é o Proxy de aplicações?
Proxy de aplicações é uma funcionalidade do Azure AD que permite aos utilizadores aceder a aplicações web no local a partir de um cliente remoto. Proxy de aplicações inclui o serviço de Proxy da aplicação que é executado na cloud e o conector de Proxy da aplicação que é executado num servidor no local. O Azure AD, o serviço de Proxy de aplicações e o trabalho de conector de Proxy de aplicações em conjunto para ser transmitido em segurança o token de início de sessão utilizador do Azure AD para a aplicação web.

Proxy de aplicações funciona com:

* Aplicações Web que utilizem [a autenticação integrada do Windows](application-proxy-configure-single-sign-on-with-kcd.md) para autenticação  
* Aplicativos que usam baseada em formulários da Web ou [baseado em cabeçalho](application-proxy-configure-single-sign-on-with-ping-access.md) acesso  
* APIs que deseja expor para aplicativos avançados em diferentes dispositivos para a Web  
* Aplicativos hospedados por trás de um [Gateway de ambiente de trabalho remoto](application-proxy-integrate-with-remote-desktop-services.md)  
* Aplicações de cliente rico que estão integradas com o Active Directory Authentication Library (ADAL)

Proxy de aplicações suporta início de sessão único. Para obter mais informações sobre métodos suportados, consulte [escolher um método de início de sessão único](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

Proxy de aplicações é recomendado para fornecer acesso de usuários remotos externos a recursos internos. Proxy da aplicação substitui a necessidade de uma VPN ou o proxy inverso. Não se destina para usuários internos na rede empresarial.  Os utilizadores internos que utilizam desnecessariamente o Proxy de aplicações podem introduzir problemas de desempenho inesperados e indesejáveis.

## <a name="how-application-proxy-works"></a>Como funciona o Proxy de aplicações

O seguinte diagrama mostra como o Azure AD e o Proxy de aplicações funcionam em conjunto para fornecer início de sessão único para aplicações no local.

![Diagrama de Proxy de aplicações do AzureAD](./media/application-proxy/azureappproxxy.png)

1. Depois do usuário acessa o aplicativo por meio de um ponto de extremidade, o utilizador é direcionado para a página de início de sessão do Azure AD. 
2. Depois de um sessão com êxito-, do Azure AD envia um token para o dispositivo do utilizador cliente.
3. O cliente envia o token para o serviço de Proxy de aplicações, que obtém o nome principal de utilizador (UPN) e o nome principal de segurança (SPN) do token. Proxy de aplicações, em seguida, envia o pedido para o conector do Proxy de aplicações.
4. Se tiver configurado o início de sessão único, o conector efetua qualquer autenticação adicional necessária em nome do utilizador.
5. O conector envia o pedido para a aplicação no local.  
6. A resposta é enviada através do conector e o serviço Proxy de aplicações para o usuário.

| Componente | Descrição |
| --------- | ----------- |
| Ponto Final  | O ponto final é um URL ou um [portal de utilizador final](end-user-experiences.md). Os utilizadores possam contactar aplicativos enquanto fora da sua rede ao aceder a um URL externo. Os utilizadores na sua rede possam aceder à aplicação através de um URL ou um portal de utilizador final. Quando os utilizadores aceder a um desses pontos de extremidade, eles se autenticar no Azure AD e, em seguida, são encaminhados através do conector para o aplicativo no local.|
| Azure AD | O Azure AD realiza a autenticação que utiliza o diretório de inquilinos armazenado na nuvem. |
| Serviço de Proxy de aplicações | Este serviço de Proxy de aplicações é executado na cloud como parte do Azure AD. Ele passa o token de início de sessão do utilizador para o conector do Proxy de aplicações. Proxy de aplicação encaminha quaisquer cabeçalhos acessíveis na solicitação e define os cabeçalhos de acordo com o protocolo, como o endereço IP do cliente. Se a solicitação de entrada para o proxy já tiver esse cabeçalho, o endereço IP do cliente é adicionado ao final da lista separada por vírgulas que é o valor do cabeçalho.|
| Conector do Proxy de aplicações | O conector é um agente leve que é executado no Windows Server no interior da rede. O conector gerencia a comunicação entre o serviço de Proxy de aplicações na cloud e a aplicação no local. O conector utiliza apenas as ligações de saída, para que não tenha de abrir portas de entrada nem colocar nada num DMZ. Os conectores são sem monitoração de estado e extraem informações a partir da cloud conforme necessário. Para obter mais informações sobre conectores, como como eles balanceamento de carga e autenticar, consulte [conectores de Proxy de aplicações do AD Azure compreender](application-proxy-connectors.md).|
| Do Active Directory (AD) | Do Active Directory é executado no local para efetuar a autenticação para contas de domínio. Quando o início de sessão único é configurado, o conector comunica com o AD para efetuar qualquer autenticação adicional necessária.
| Aplicação no local | Por fim, o utilizador é capaz de aceder a uma aplicação no local. 

## <a name="next-steps"></a>Passos Seguintes
Para começar a utilizar o Proxy de aplicações, veja [Tutorial: Adicionar uma aplicação no local para acesso remoto através do Proxy de aplicações](application-proxy-add-on-premises-application.md). 

Para as mais recentes notícias e atualizações, consulte o [blog de Proxy de aplicações](https://blogs.technet.com/b/applicationproxyblog/)


