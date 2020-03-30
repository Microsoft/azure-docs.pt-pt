---
title: Acesso remoto a aplicações no local - Procuração de aplicação ad-ad-azure
description: A Procuração de Aplicações do Diretório Ativo Azure fornece acesso remoto seguro a aplicações web no local. Após uma única entrada no Azure AD, os utilizadores podem aceder a aplicações cloud e no local através de um URL externo ou de um portal de aplicações interna. Por exemplo, o Application Proxy pode fornecer acesso remoto e um único sinal para aplicações de ambiente de trabalho remoto, SharePoint, Equipas, Tableau, Qlik e aplicações de linha de negócios (LOB).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274838"
---
# <a name="remote-access-to-on-premises-applications-through-azure-active-directorys-application-proxy"></a>Acesso remoto a aplicações no local através do Proxy de Aplicação do Diretório Ativo azure 

A Procuração de Aplicações do Diretório Ativo Azure fornece acesso remoto seguro a aplicações web no local. Após uma única entrada no Azure AD, os utilizadores podem aceder a aplicações cloud e no local através de um URL externo ou de um portal de aplicações interna. Por exemplo, o Application Proxy pode fornecer acesso remoto e um único sinal para aplicações de ambiente de trabalho remoto, SharePoint, Equipas, Tableau, Qlik e aplicações de linha de negócios (LOB).

O Proxy de Aplicações do Azure Active Directory é:

- **Simples de usar.** Os utilizadores podem aceder às suas aplicações no local da mesma forma que acedem ao O365 e a outras aplicações SaaS integradas com a Azure AD. Não precisa de alterar ou atualizar as suas aplicações para trabalhar com o Proxy de Aplicações. 

- **Seguro.** Os pedidos no local podem utilizar os controlos de autorização do Azure e análises de segurança. Por exemplo, as aplicações no local podem utilizar o Acesso Condicional e a verificação em duas etapas. Aplicação Proxy não requer que abra ligações de entrada através da sua firewall.
 
- **Rentável**. As soluções no local normalmente exigem que você instale e mantenha zonas desmilitarizadas (DMZs), servidores de borda ou outras infraestruturas complexas. Aplicação Proxy corre na nuvem, o que facilita a utilização. Para utilizar o Proxy da Aplicação, não precisa de alterar a infraestrutura de rede ou de instalar aparelhos adicionais no seu ambiente no local.

## <a name="what-is-application-proxy"></a>O que é o Proxy de Aplicação?
Application Proxy é uma funcionalidade da AD Azure que permite aos utilizadores aceder em aplicações web no local a partir de um cliente remoto. O Proxy de Aplicação inclui tanto o serviço proxy de aplicação que funciona na nuvem, como o conector Proxy de aplicação que funciona num servidor no local. A Azure AD, o serviço Proxy de Aplicação e o conector Proxy de aplicação trabalham em conjunto para passar em segurança o sinal de entrada do utilizador da Azure AD para a aplicação web.

Aplicação Proxy trabalha com:

* Aplicações web que utilizam [autenticação integrada do Windows](application-proxy-configure-single-sign-on-with-kcd.md) para autenticação  
* Aplicações web que utilizam acesso baseado em formulárioou [baseado em cabeçalho](application-proxy-configure-single-sign-on-with-ping-access.md)  
* ApIs web que você quer expor a aplicações ricas em diferentes dispositivos  
* Aplicações hospedadas atrás de um [Gateway de Ambiente](application-proxy-integrate-with-remote-desktop-services.md) de Trabalho Remoto  
* Aplicações de clientes ricas que estão integradas com a Biblioteca de Autenticação de DirectórioAtivo Ativo (ADAL)

Application Proxy suporta um único sinal. Para obter mais informações sobre métodos suportados, consulte [Escolher um único método de inscrição](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

Aplicação Proxy é recomendado para dar aos utilizadores remotos acesso a recursos internos. Aplicação Proxy substitui a necessidade de uma VPN ou procuração inversa. Não se destina a utilizadores internos na rede corporativa.  Estes utilizadores que utilizam desnecessariamente o Application Proxy podem introduzir problemas de desempenho inesperados e indesejáveis.

## <a name="how-application-proxy-works"></a>Como funciona o Proxy de Aplicação

O diagrama seguinte mostra como o Azure AD e o Application Proxy trabalham em conjunto para fornecer um único sinal às aplicações no local.

![Diagrama de proxy de aplicação azuread](./media/application-proxy/azureappproxxy.png)

1. Depois de o utilizador ter acedido à aplicação através de um ponto final, o utilizador é direcionado para a página de entrada de ada d.ida e venda. 
2. Após um início de sessão bem-sucedido, a Azure AD envia um sinal para o dispositivo cliente do utilizador.
3. O cliente envia o símbolo para o serviço Proxy de Aplicação, que recupera o nome principal do utilizador (UPN) e o nome principal de segurança (SPN) do símbolo. Aplicação Proxy envia então o pedido para o conector Proxy de Pedido.
4. Se tiver configurado um único sinal, o conector executa qualquer autenticação adicional necessária em nome do utilizador.
5. O conector envia o pedido para o pedido no local.  
6. A resposta é enviada através do serviço de conector e proxy de aplicação ao utilizador.

| Componente | Descrição |
| --------- | ----------- |
| Ponto Final  | O ponto final é um URL ou um [portal de utilizador final](end-user-experiences.md). Os utilizadores podem chegar a aplicações fora da sua rede acedendo a um URL externo. Os utilizadores dentro da sua rede podem aceder à aplicação através de um URL ou de um portal de utilizador final. Quando os utilizadores vão a um destes pontos finais, autenticam-se em Azure AD e depois são encaminhados através do conector para a aplicação no local.|
| Azure AD | A Azure AD realiza a autenticação utilizando o diretório de inquilinos armazenado na nuvem. |
| Serviço proxy de aplicação | Este serviço de Procuração de Aplicações funciona na nuvem como parte da Azure AD. Transmite o sinal de sinal do utilizador para o Conector proxy da aplicação. Aplicação Proxy reencaminha quaisquer cabeçalhos acessíveis no pedido e define os cabeçalhos de acordo com o seu protocolo, para o endereço IP do cliente. Se o pedido de entrada ao representante já tiver esse cabeçalho, o endereço IP do cliente é adicionado ao fim da lista separada da vírposta que é o valor do cabeçalho.|
| Conector proxy de aplicação | O conector é um agente leve que funciona num Servidor windows dentro da sua rede. O conector gere a comunicação entre o serviço de procuração de aplicações na nuvem e a aplicação no local. O conector utiliza apenas ligações de saída, para que não tenha de abrir nenhuma porta de entrada ou colocar qualquer coisa no DMZ. Os conectores são apátridas e retiram informações da nuvem conforme necessário. Para obter mais informações sobre conectores, como a forma como equilibram e autenticam, consulte [conectores De Procuração de Aplicação AD Do Indisso.](application-proxy-connectors.md)|
| Diretório Ativo (AD) | O Diretório Ativo funciona no local para realizar a autenticação para contas de domínio. Quando o único sinal é configurado, o conector comunica com a AD para realizar qualquer autenticação adicional necessária.
| Aplicação no local | Finalmente, o utilizador pode aceder a uma aplicação no local. 

## <a name="next-steps"></a>Passos seguintes
Para começar a utilizar o Proxy da Aplicação, consulte [Tutorial: Adicione uma aplicação no local para acesso remoto através de Procuração](application-proxy-add-on-premises-application.md)de Aplicações . 

Para obter as últimas novidades e atualizações, consulte o [blog Application Proxy](https://blogs.technet.com/b/applicationproxyblog/)


