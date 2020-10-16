---
title: Considerações de segurança para a Azure AD Application Proxy / Microsoft Docs
description: Cobre considerações de segurança para a utilização do Azure AD Application Proxy
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8051621cf05b0f8c387c41cf0b95bb32e15e667
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91825902"
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Considerações de segurança para aceder remotamente a apps com Proxy de aplicações AD AZure

Este artigo explica os componentes que funcionam para manter os seus utilizadores e aplicações seguros quando utiliza o Azure Ative Directory Application Proxy.

O diagrama seguinte mostra como o Azure AD permite um acesso remoto seguro às suas aplicações no local.

 ![Diagrama de acesso remoto seguro através do Azure AD Application Proxy](./media/application-proxy-security/secure-remote-access.png)

## <a name="security-benefits"></a>Benefícios de segurança

A Azure AD Application Proxy oferece os seguintes benefícios de segurança:

### <a name="authenticated-access"></a>Acesso autenticado 

Se optar por utilizar o Azure Ative Directory antes da autorização, só as ligações autenticadas podem aceder à sua rede.

O Azure AD Application Proxy conta com o serviço de fichas de segurança Azure AD (STS) para toda a autenticação.  A pré-autorização, pela sua própria natureza, bloqueia um número significativo de ataques anónimos, porque apenas as identidades autenticadas podem aceder à aplicação back-end.

Se escolher o Passthrough como o seu método de pré-autorização, não obtém este benefício. 

### <a name="conditional-access"></a>Acesso Condicional

Aplique controlos de política mais ricos antes de estabelecer ligações à sua rede.

Com [o Acesso Condicional,](../conditional-access/concept-conditional-access-cloud-apps.md)pode definir restrições sobre como os utilizadores podem aceder às suas aplicações. Pode criar políticas que restringem os ins-ins com base na localização, força da autenticação e perfil de risco do utilizador.

Também pode utilizar o Acesso Condicional para configurar políticas de autenticação multi-factor, adicionando outra camada de segurança às autenticações do seu utilizador. Além disso, as suas aplicações também podem ser encaminhadas para o Microsoft Cloud App Security via Azure AD Conditional Access para fornecer monitorização e controlos em tempo real, através de políticas de [acesso](https://docs.microsoft.com/cloud-app-security/access-policy-aad) e [sessão](https://docs.microsoft.com/cloud-app-security/session-policy-aad)

### <a name="traffic-termination"></a>Interrupção do tráfego

Todo o tráfego está terminado na nuvem.

Como o Azure AD Application Proxy é um proxy reverso, todo o tráfego para aplicações back-end é encerrado no serviço. A sessão só pode ser restabelecida com o servidor back-end, o que significa que os seus servidores de back-end não estão expostos ao tráfego HTTP direto. Esta configuração significa que está mais bem protegido contra ataques direcionados.

### <a name="all-access-is-outbound"></a>Todo o acesso é de saída 

Não precisas de abrir ligações à rede corporativa.

Os conectores Proxy de aplicação apenas utilizam ligações de saída para o serviço Azure AD Application Proxy, o que significa que não há necessidade de abrir portas de firewall para ligações de entrada. Os proxies tradicionais necessitavam de uma rede de perímetro (também conhecida como *DMZ*, *zona desmilitarizada,* ou *sub-rede rastreada*) e permitiam o acesso a ligações não autenticadas na borda da rede. Este cenário exigia investimentos em produtos de firewall de aplicações web para analisar o tráfego e proteger o ambiente. Com o Application Proxy, você não precisa de uma rede de perímetro porque todas as ligações são de saída e ocorrem sobre um canal seguro.

Para obter mais informações sobre conectores, consulte [os conectores Proxy da aplicação AD Azure](application-proxy-connectors.md).

### <a name="cloud-scale-analytics-and-machine-learning"></a>Análise em nuvem e aprendizagem automática 

Obter proteção de segurança de ponta.

Por fazer parte do Azure Ative Directory, o Application Proxy pode alavancar [a Azure AD Identity Protection](../active-directory-identityprotection.md), com dados do Microsoft Security Response Center e da Unidade de Crimes Digitais. Juntos identificamos proactivamente contas comprometidas e oferecemos proteção contra entradas de alto risco. Temos em conta numerosos fatores para determinar quais as tentativas de entrada em vigor de alto risco. Estes fatores incluem a sinalização de dispositivos infetados, redes anonimizantes e locais atípicos ou improváveis.

Muitos destes relatórios e eventos já estão disponíveis através de uma API para integração com os seus sistemas de informação de segurança e gestão de eventos (SIEM).

### <a name="remote-access-as-a-service"></a>Acesso remoto como serviço

Não tens de te preocupar em manter e remendar servidores no local.

O software não remutado ainda é responsável por um grande número de ataques. O Azure AD Application Proxy é um serviço à escala de Internet que a Microsoft possui, pelo que obtém sempre os mais recentes patches e upgrades de segurança.

Para melhorar a segurança das aplicações publicadas pela Azure AD Application Proxy, bloqueamos os robôs web crawler de indexar e arquivar as suas aplicações. Cada vez que um robô web crawler tenta recuperar as definições do robô para uma aplicação publicada, o Application Proxy responde com um ficheiro robots.txt que inclui `User-agent: * Disallow: /` .

#### <a name="azure-ddos-protection-service"></a>Serviço de proteção Azure DDoS

As aplicações publicadas através do Application Proxy estão protegidas contra ataques de Negação de Serviço Distribuído (DDoS). Esta proteção é gerida pela Microsoft e é ativada automaticamente em todos os nossos datacenters. O serviço de proteção Azure DDoS fornece monitorização de tráfego sempre ligado e mitigação em tempo real de ataques comuns ao nível da rede. 

## <a name="under-the-hood"></a>Os bastidores

Azure AD Application Proxy é composto por duas partes:

* O serviço baseado na nuvem: Este serviço funciona em Azure, e é onde são feitas as ligações cliente/utilizador externos.
* [O conector no local](application-proxy-connectors.md): Um componente no local, o conector ouve pedidos do serviço Azure AD Application Proxy e trata das ligações às aplicações internas. 

É estabelecido um fluxo entre o conector e o serviço Application Proxy quando:

* O conector é primeiro configurado.
* O conector retira informações de configuração do serviço Application Proxy.
* Um utilizador acede a uma aplicação publicada.

>[!NOTE]
>Todas as comunicações ocorrem sobre o TLS, e são sempre originárias do conector do serviço Application Proxy. O serviço é apenas de saída.

O conector utiliza um certificado de cliente para autenticar o serviço de Procuração de Aplicações para quase todas as chamadas. A única exceção a este processo é o primeiro passo de configuração, onde o certificado de cliente é estabelecido.

### <a name="installing-the-connector"></a>Instalação do conector

Quando o conector é configurado pela primeira vez, ocorrem os seguintes eventos de fluxo:

1. O registo do conector ao serviço ocorre como parte da instalação do conector. Os utilizadores são solicitados a introduzir as suas credenciais de administração Azure AD.O símbolo adquirido a partir desta autenticação é então apresentado ao serviço de Procuração de Aplicações AD Azure.
2. O serviço Application Proxy avalia o símbolo. Verifica se o utilizador é administrador da empresa no arrendatário.Se o utilizador não for administrador, o processo será encerrado.
3. O conector gera um pedido de certificado de cliente e passa-o, juntamente com o token, para o serviço Application Proxy. O serviço, por sua vez, verifica o token e assina o pedido de certificado do cliente.
4. O conector utiliza o certificado de cliente para futura comunicação com o serviço Application Proxy.
5. O conector realiza uma retirada inicial dos dados de configuração do sistema a partir do serviço utilizando o seu certificado de cliente, estando agora pronto para receber pedidos.

### <a name="updating-the-configuration-settings"></a>Atualizar as definições de configuração

Sempre que o serviço Application Proxy atualiza as definições de configuração, ocorrem os seguintes eventos de fluxo:

1. O conector liga-se ao ponto final de configuração dentro do serviço Application Proxy utilizando o seu certificado de cliente.
2. Após a validação do certificado do cliente, o serviço Application Proxy devolve os dados de configuração ao conector (por exemplo, o grupo de conector do qual o conector deve fazer parte).
3. Se o certificado atual tiver mais de 180 dias, o conector gera um novo pedido de certificado, que atualiza efetivamente o certificado do cliente a cada 180 dias.

### <a name="accessing-published-applications"></a>Aceder a aplicações publicadas

Quando os utilizadores acedem a uma aplicação publicada, os seguintes eventos ocorrem entre o serviço Application Proxy e o conector Application Proxy:

1. O serviço autentica o utilizador para a aplicação
2. O serviço coloca um pedido na fila do conector
3. Um conector processa o pedido a partir da fila
4. O conector aguarda uma resposta
5. O serviço transmite dados ao utilizador

Para saber mais sobre o que acontece em cada um destes passos, continue a ler.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. O serviço autentica o utilizador para a aplicação

Se configurar a aplicação para utilizar o Passthrough como método de pré-autorização, os passos nesta secção são ignorados.

Se configurar a aplicação para pré-autorizar com AZure AD, os utilizadores são redirecionados para o Azure AD STS para autenticar, e os seguintes passos ocorrem:

1. Aplicação Proxy verifica quaisquer requisitos da política de acesso condicional para a aplicação específica. Este passo garante que o utilizador foi atribuído à aplicação. Se for necessária uma verificação em duas etapas, a sequência de autenticação solicita ao utilizador um segundo método de autenticação.

2. Depois de todos os controlos terem passado, o Azure AD STS emite um token assinado para a aplicação e redireciona o utilizador de volta para o serviço De procuração de aplicação.

3. O Pedido Proxy verifica que o token foi emitido para a aplicação correta. Realiza também outras verificações, tais como a garantia de que o token foi assinado pela Azure AD, e que ainda se encontra dentro da janela válida.

4. O Application Proxy define um cookie de autenticação encriptado para indicar que a autenticação da aplicação ocorreu. O cookie inclui um prazo de validade que é baseado no token da AD AZure e outros dados, como o nome de utilizador em que a autenticação é baseada. O cookie é encriptado com uma chave privada conhecida apenas pelo serviço Application Proxy.

5. O Application Proxy redireciona o utilizador de volta para o URL originalmente solicitado.

Se alguma parte das etapas de pré-autorização falhar, o pedido do utilizador é negado e o utilizador é mostrado uma mensagem indicando a origem do problema.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. O serviço coloca um pedido na fila do conector

Os conectores mantêm uma ligação de saída aberta ao serviço Application Proxy. Quando um pedido chega, o serviço faz fila com o pedido numa das ligações abertas para o conector recolher.

O pedido inclui itens da aplicação, tais como os cabeçalhos de pedido, dados do cookie encriptado, o utilizador que faz o pedido e o ID do pedido. Embora os dados do cookie encriptado seja enviado com o pedido, o cookie de autenticação em si não é.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. O conector processa o pedido a partir da fila. 

Com base no pedido, a Application Proxy realiza uma das seguintes ações:

* Se o pedido for uma operação simples (por exemplo, não há dados dentro do corpo como é com um pedido RESTful *GET),* o conector faz uma ligação ao recurso interno do alvo e, em seguida, aguarda uma resposta.

* Se o pedido tiver dados associados ao mesmo no organismo (por exemplo, uma operação RESTful *POST),* o conector faz uma ligação de saída utilizando o certificado de cliente para a instância Proxy de aplicação. Faz esta ligação para solicitar os dados e abrir uma ligação ao recurso interno. Depois de receber o pedido do conector, o serviço Application Proxy começa a aceitar o conteúdo do utilizador e encaminha os dados para o conector. O conector, por sua vez, reencaminha os dados para o recurso interno.

#### <a name="4-the-connector-waits-for-a-response"></a>4. O conector aguarda uma resposta.

Após o pedido e transmissão de todo o conteúdo para a parte de trás, o conector aguarda uma resposta.

Depois de receber uma resposta, o conector faz uma ligação de saída ao serviço Application Proxy, para devolver os detalhes do cabeçalho e começar a transmitir os dados de devolução.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. O serviço transmite dados ao utilizador. 

Pode ocorrer aqui algum processamento da aplicação. Se configurar o Application Proxy para traduzir cabeçalhos ou URLs na sua aplicação, esse processamento acontece conforme necessário durante este passo.


## <a name="next-steps"></a>Passos seguintes

[Considerações de topologia de rede ao utilizar o Proxy de Aplicação AD AZure](application-proxy-network-topology.md)

[Compreenda os conectores Proxy de aplicação AD Azure](application-proxy-connectors.md)
