---
title: Acesso remoto a aplicações no local - Azure AD Application Proxy
description: O Proxy de Aplicação do Azure Ative Directory fornece acesso remoto seguro a aplicações web no local. Após um único s-on para Azure AD, os utilizadores podem aceder a aplicações tanto em nuvem como no local através de um URL externo ou de um portal de aplicações internas. Por exemplo, o Application Proxy pode fornecer acesso remoto e um único acesso às aplicações Remote Desktop, SharePoint, Teams, Tableau, Qlik e linha de negócios (LOB).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 40472dc2446133ec23d1d62343a93d2d69fc90cd
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260151"
---
# <a name="remote-access-to-on-premises-applications-through-azure-ad-application-proxy"></a>Acesso remoto a aplicações no local através de um Proxy de Aplicações do Azure AD

O Proxy de Aplicação do Azure Ative Directory fornece acesso remoto seguro a aplicações web no local. Após um único s-on para Azure AD, os utilizadores podem aceder a aplicações tanto em nuvem como no local através de um URL externo ou de um portal de aplicações internas. Por exemplo, o Application Proxy pode fornecer acesso remoto e um único acesso às aplicações Remote Desktop, SharePoint, Teams, Tableau, Qlik e linha de negócios (LOB).

O Proxy de Aplicações do Azure AD é:

- **Simples de usar.** Os utilizadores podem aceder às suas aplicações no local da mesma forma que acedem ao Microsoft 365 e a outras aplicações SaaS integradas com AZure AD. Não precisa de alterar ou atualizar as suas aplicações para trabalhar com o Proxy de Aplicações.

- **Seguro**. As aplicações no local podem utilizar os controlos de autorização da Azure e a análise de segurança. Por exemplo, as aplicações no local podem utilizar acesso condicional e verificação em duas etapas. O Application Proxy não requer que abra as ligações de entrada através da sua firewall.

- **Rentável**. As soluções no local normalmente exigem que você crie e mantenha zonas desmilitarizadas (DMZs), servidores de borda ou outras infraestruturas complexas. O Proxy de aplicação funciona na nuvem, o que facilita a sua utilização. Para utilizar o Application Proxy, não precisa de alterar a infraestrutura de rede ou de instalar aparelhos adicionais no seu ambiente no local.

## <a name="what-is-application-proxy"></a>O que é o Proxy de Aplicação?
Application Proxy é uma funcionalidade do Azure AD que permite aos utilizadores acederem a aplicações web no local a partir de um cliente remoto. O Application Proxy inclui tanto o serviço Application Proxy que funciona na nuvem, como o conector Application Proxy que funciona num servidor no local. O Azure AD, o serviço Application Proxy e o conector Application Proxy trabalham em conjunto para passar de forma segura o token de inscrição do utilizador do Azure AD para a aplicação web.

Application Proxy trabalha com:

* Aplicações web que utilizam [autenticação integrada do Windows](application-proxy-configure-single-sign-on-with-kcd.md) para autenticação
* Aplicações web que usam acesso baseado em formulários ou [cabeçalho](./application-proxy-configure-single-sign-on-with-headers.md)
* APIs web que pretende expor a aplicações ricas em diferentes dispositivos
* Aplicações hospedadas atrás de um [Gateway de Desktop Remoto](application-proxy-integrate-with-remote-desktop-services.md)
* Aplicativos de clientes ricos que estão integrados com a Microsoft Authentication Library (MSAL)

O Application Proxy suporta uma única inscrição. Para obter mais informações sobre métodos suportados, consulte [escolher um único método de inscrição](sso-options.md#choosing-a-single-sign-on-method).

O Proxy da aplicação é recomendado para dar aos utilizadores remotos acesso a recursos internos. O Application Proxy substitui a necessidade de um VPN ou procuração inversa. Não se destina a utilizadores internos na rede corporativa.  Estes utilizadores que utilizam desnecessariamente o Application Proxy podem introduzir problemas de desempenho inesperados e indesejáveis.

## <a name="how-application-proxy-works"></a>Como funciona o Proxy de Aplicação

O diagrama seguinte mostra como a Azure AD e a Application Proxy trabalham em conjunto para fornecer um único sign-on para aplicações no local.

![Diagrama de procuração de aplicativo AzureAD](./media/application-proxy/azureappproxxy.png)

1. Depois de o utilizador ter acedido à aplicação através de um ponto final, o utilizador é direcionado para a página de entrada AZure AD.
2. Após uma sinstrução bem sucedida, a Azure AD envia um sinal para o dispositivo cliente do utilizador.
3. O cliente envia o token para o serviço Application Proxy, que recupera o nome principal do utilizador (UPN) e o nome principal de segurança (SPN) do token. O Application Proxy envia então o pedido para o conector Application Proxy.
4. Se tiver configurado uma única inscrição, o conector efetua qualquer autenticação adicional necessária em nome do utilizador.
5. O conector envia o pedido para o pedido no local.
6. A resposta é enviada através do serviço de conector e procuração de aplicação para o utilizador.

> [!NOTE]
> Tal como a maioria dos agentes híbridos AZure AD, o Conector Proxy de Aplicação não requer que abra ligações de entrada através da sua firewall. O tráfego do utilizador no passo 3 termina no Serviço de Procuração de Aplicações (em Azure AD). O Conector Proxy de Aplicação (no local) é responsável pelo resto da comunicação.
>


| Componente | Descrição |
| --------- | ----------- |
| Ponto final  | O ponto final é um URL ou um [portal de utilizador final](end-user-experiences.md). Os utilizadores podem chegar a aplicações fora da sua rede acedendo a um URL externo. Os utilizadores dentro da sua rede podem aceder à aplicação através de um URL ou de um portal de utilizador final. Quando os utilizadores vão a um destes pontos finais, autenticam-se em Azure AD e depois são encaminhados através do conector para a aplicação no local.|
| Azure AD | A Azure AD realiza a autenticação utilizando o diretório de inquilinos armazenado na nuvem. |
| Serviço de procuração de aplicação | Este serviço de Procuração de Aplicações funciona na nuvem como parte do Azure AD. Transmite o sinal de inscrição do utilizador para o Conector Proxy da aplicação. Application Proxy encaminha quaisquer cabeçalhos acessíveis no pedido e define os cabeçalhos de acordo com o seu protocolo, para o endereço IP do cliente. Se o pedido de entrada ao representante já tiver esse cabeçalho, o endereço IP do cliente é adicionado ao fim da lista separada de vírgula que é o valor do cabeçalho.|
| Conector Proxy de aplicação | O conector é um agente leve que funciona num Servidor Windows dentro da sua rede. O conector gere a comunicação entre o serviço Application Proxy na nuvem e a aplicação no local. O conector só utiliza ligações de saída, para que não tenha de abrir portas de entrada ou colocar nada na Zona Desmilitarizada. Os conectores são apátridas e retiram informações da nuvem se necessário. Para obter mais informações sobre conectores, como a forma como se load-balance e autentica, consulte [os conectores Proxy da aplicação AD Azure](application-proxy-connectors.md).|
| Diretório Ativo (AD) | O Ative Directory funciona no local para efetuar a autenticação para contas de domínio. Quando o único sinal de sação é configurado, o conector comunica com AD para efetuar qualquer autenticação adicional necessária.
| Aplicação no local | Por fim, o utilizador pode aceder a uma aplicação no local.

## <a name="next-steps"></a>Passos seguintes
Para começar a utilizar o Application Proxy, consulte [Tutorial: Adicione uma aplicação no local para acesso remoto através do Application Proxy](application-proxy-add-on-premises-application.md).