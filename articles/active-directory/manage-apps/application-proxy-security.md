---
title: Considerações de segurança para o Proxy de aplicações do Azure AD | Documentos da Microsoft
description: Aborda considerações de segurança para utilizar o Proxy de aplicações do Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2017
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7bb9fc806779565581fa7667749402f5608edd80
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438079"
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Considerações de segurança para aceder a aplicações remotamente com o Proxy de aplicações do Azure AD

Este artigo explica os componentes que funcionam para manter os seus utilizadores e aplicações seguros ao utilizar o Proxy de aplicações do Azure Active Directory.

O diagrama seguinte mostra como o Azure AD permite o acesso remoto seguro às suas aplicações no local.

 ![Diagrama de acesso remoto seguro através do Proxy de aplicações do Azure AD](./media/application-proxy-security/secure-remote-access.png)

## <a name="security-benefits"></a>Benefícios de segurança

Proxy de aplicações do Azure AD oferece as seguintes vantagens de segurança:

### <a name="authenticated-access"></a>Acesso autenticado 

Se optar por utilizar o Azure Active Directory pré-autenticação, em seguida, apenas ligações de autenticado podem aceder à sua rede.

Proxy de aplicações do Azure AD depende do serviço de token de segurança (STS) do Azure AD para toda a autenticação.  Pré-autenticação, por sua própria natureza, bloqueia um número significativo de ataques de anónimos, uma vez que apenas as identidades autenticadas podem aceder à aplicação de back-end.

Se escolher o pass-through como seu método de pré-autenticação, não obtém este benefício. 

### <a name="conditional-access"></a>Acesso condicional

Aplica controlos de política mais sofisticados para que são estabelecidas ligações a sua rede.

Com o [acesso condicional](../conditional-access/overview.md), pode definir restrições no qual o tráfego tem permissão para aceder às suas aplicações de back-end. Pode criar políticas que restringem o inícios de sessão com base na localização, força da autenticação e perfil de risco do utilizador.

Também pode utilizar o acesso condicional para configurar políticas de multi-factor Authentication, adicionando outra camada de segurança ao seu autenticações de utilizador. Além disso, seus aplicativos também podem ser roteados para a Microsoft Cloud App Security através do acesso condicional do Azure AD para fornecer monitorização em tempo real e controles, via [acesso](https://docs.microsoft.com/cloud-app-security/access-policy-aad) e [sessão](https://docs.microsoft.com/cloud-app-security/session-policy-aad) políticas

### <a name="traffic-termination"></a>Terminação de tráfego

Todo o tráfego é terminado na cloud.

Como o Proxy de aplicações do Azure AD é um proxy inverso, todo o tráfego para aplicações de back-end é terminado no serviço. A sessão pode obter restabelecida apenas com o servidor de back-end, o que significa que os seus servidores de back-end não são expostas para direcionar o tráfego HTTP. Esta configuração significa que estão melhor protegidos contra ataques direcionados.

### <a name="all-access-is-outbound"></a>Todo o acesso é de saída 

Não precisa de abrir ligações de entrada à rede empresarial.

Conectores de Proxy de aplicação utilizam apenas ligações de saída para o serviço de Proxy de aplicações do Azure AD, o que significa que não há necessidade de abrir portas de firewall para ligações de entrada. Proxies tradicionais necessária numa rede de perímetro (também conhecido como *DMZ*, *zona desmilitarizada*, ou *sub-rede filtrada*) e permissão de acesso a ligações não autenticadas na borda da rede. Este cenário necessários investimentos em produtos de firewall de aplicações web para analisar o tráfego e proteger o ambiente. Com o Proxy de aplicações, não necessita de uma rede de perímetro porque todas as ligações são saídas e ocorrem através de um canal seguro.

Para obter mais informações sobre conectores, consulte [conectores de Proxy de aplicações do AD Azure compreender](application-proxy-connectors.md).

### <a name="cloud-scale-analytics-and-machine-learning"></a>Análise de escala da cloud e machine learning 

Obtenha proteção de segurança de vanguarda.

Porque faz parte do Azure Active Directory, o Proxy de aplicações podem tirar partido [do Azure AD Identity Protection](../active-directory-identityprotection.md), com dados do Microsoft Security Response Center e da unidade de Crimes digitais. Em conjunto, proativamente identificar contas comprometidas e oferecer proteção contra inícios de sessão de alto risco. Vamos levar em conta vários fatores para determinar quais tentativas de início de sessão são de alto risco. Estes fatores incluem dispositivos de sinalização infectado, redes anonymizing e localizações atípicas ou improvável.

Muitos desses relatórios e eventos já estão disponíveis através de uma API para a integração com suas informações de segurança e sistemas de gestão (SIEM) de eventos.

### <a name="remote-access-as-a-service"></a>Acesso remoto como um serviço

Não precisa se preocupar sobre a manutenção e aplicação de patches de servidores no local.

Sem patch software ainda totaliza um grande número de ataques. Proxy de aplicações do Azure AD é um serviço de escala da Internet que é proprietário a Microsoft, para que tenha sempre as mais recentes patches de segurança e atualizações.

Para melhorar a segurança de aplicações publicadas pelo Proxy de aplicações do Azure AD, será feito um bloqueio robôs de crawler de web de indexação e arquivamento de seus aplicativos. Sempre que um robô de crawler web tenta obter as definições o robô para uma aplicação publicada, o Proxy de aplicações responde com um ficheiro de robots.txt que inclui `User-agent: * Disallow: /`.

### <a name="ddos-prevention"></a>Prevenção contra DDOS

Aplicações publicadas através do Proxy de aplicações estão protegidas contra ataques de Distributed negação de serviço (DDOS).

O serviço de Proxy da aplicação monitoriza a quantidade de tráfego a tentar aceder a aplicações e rede. Se o número de dispositivos que pedem acesso remoto às suas aplicações picos, Microsoft regula o acesso à sua rede. 

Microsoft monitoriza os padrões de tráfego para aplicações individuais e para a sua subscrição, como um todo. Se um aplicativo recebe superior normais pedidos, pedidos de acesso a essa aplicação serão negados durante um curto período de tempo. Se receber superior do que as solicitações normais na sua subscrição de toda, as solicitações para aceder a qualquer uma das suas aplicações são negadas. Essa medida preventiva mantém seus servidores de aplicações de que está a ser sobrecarregado com pedidos de acesso remoto, para que os utilizadores no local podem manter a aceder aos seus aplicativos. 

## <a name="under-the-hood"></a>Nos bastidores

Proxy de aplicações do Azure AD consiste em duas partes:

* O serviço baseado na nuvem: Este serviço é executado no Azure e é onde as ligações de cliente/utilizador externo são efetuadas.
* [O conector no local](application-proxy-connectors.md): Um componente no local, o conector de escuta de pedidos das ligações de serviço e identificadores de Proxy de aplicações do Azure AD para os aplicativos internos. 

Um fluxo entre o conector e o serviço de Proxy de aplicações é estabelecido quando:

* O conector é configurado em primeiro lugar.
* O conector recebe informações de configuração do serviço Proxy de aplicações.
* Um utilizador acede a uma aplicação publicada.

>[!NOTE]
>Toda a comunicação ocorre através de SSL e, se originam sempre o conector para o serviço de Proxy de aplicações. O serviço é de saída apenas.

O conector utiliza um certificado de cliente para autenticação no serviço de Proxy de aplicações para quase todas as chamadas. A única exceção a este processo é a etapa de configuração inicial, onde o certificado de cliente é estabelecido.

### <a name="installing-the-connector"></a>Instalar o conector

Quando o conector é configurado em primeiro lugar, os eventos de fluxo seguintes ocorrem:

1. O registo do conector para o serviço ocorre como parte da instalação do conector. É pedido aos utilizadores a introduzam as credenciais de administrador do Azure AD. O token adquirido desta autenticação, em seguida, é apresentado para o serviço de Proxy de aplicações do Azure AD.
2. O serviço de Proxy de aplicações avalia o token. Ele verifica se o usuário é um administrador de empresa no inquilino. Se o utilizador não for um administrador, o processo é encerrado.
3. O conector gera um pedido de certificado de cliente e transmite a ela, juntamente com o token, para o serviço de Proxy de aplicações. Por sua vez, o serviço verifica o token e inicia o pedido de certificado de cliente.
4. O conector utiliza o certificado de cliente para a comunicação futura com o serviço de Proxy de aplicações.
5. O conector efetua uma solicitação inicial dos dados de configuração de sistema do serviço com o seu certificado de cliente e está agora pronto para fazer pedidos.

### <a name="updating-the-configuration-settings"></a>A atualizar as definições de configuração

Sempre que o serviço de Proxy de aplicações atualiza as definições de configuração, os eventos de fluxo seguintes ocorrem:

1. O conector liga-se para o ponto final de configuração no âmbito do serviço de Proxy de aplicações com o seu certificado de cliente.
2. Depois do certificado de cliente é validado, o serviço de Proxy da aplicação devolve dados de configuração para o conector (por exemplo, o grupo do conector que o conector, deve fazer parte da).
3. Se o certificado atual é a mais de 180 dias, o conector gera um novo pedido de certificado, que efetivamente atualiza o certificado de cliente a cada 180 dias.

### <a name="accessing-published-applications"></a>Aceder a aplicações publicadas

Quando os utilizadores acedem uma aplicação publicada, os seguintes eventos acontecem entre o serviço de Proxy de aplicações e o conector do Proxy de aplicações:

1. O serviço autentica o utilizador da aplicação
2. O serviço faz uma solicitação na fila de conector
3. Um conector processa a solicitação da fila
4. O conector aguarda uma resposta
5. O serviço transmite os dados para o utilizador

Para saber mais sobre o que acontece em cada uma dessas etapas, mantenha a leitura.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. O serviço autentica o utilizador da aplicação

Se tiver configurado a aplicação para utilizar o pass-through como seu método de pré-autenticação, os passos nesta secção são ignorados.

Se tiver configurado a aplicação para preauthenticate com o Azure AD, os utilizadores são redirecionados para o STS do AD do Azure para autenticar e os passos seguintes ocorrem:

1. Proxy da aplicação verifica a existência de quaisquer requisitos de política de acesso condicional para o aplicativo específico. Este passo garante que o utilizador foi atribuído à aplicação. Se for necessária a verificação de dois passos, a sequência de autenticação pede ao utilizador para um segundo método de autenticação.

2. Depois de todas as verificações tenham passado, o Azure AD STS emite um token assinado para o aplicativo e redireciona o usuário voltar para o serviço de Proxy de aplicações.

3. Proxy da aplicação verifica se o token foi emitido para a aplicação correta. Ele executa outras verificações também, como garantir que o token foi assinado pelo Azure AD e, se não ainda dentro da janela de válido.

4. Conjuntos de Proxy de aplicações tiver ocorrido um cookie de autenticação criptografado para indicar que a autenticação à aplicação. O cookie inclui um timestamp de expiração que se baseia o token do Azure AD e outros dados, como o nome de utilizador que a autenticação se baseia. O cookie é encriptado com uma chave particular conhecida apenas para o serviço de Proxy de aplicações.

5. Proxy da aplicação redireciona o utilizador novamente para o URL pedido inicialmente.

Se qualquer parte dos passos pré-autenticação falhar, o pedido do utilizador é negado e o utilizador é apresentado uma mensagem a indicar a origem do problema.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. O serviço faz uma solicitação na fila de conector

Conectores de manter uma ligação de saída aberta para o serviço de Proxy de aplicações. Quando uma solicitação chega, o serviço coloca em fila a solicitação em um das conexões abertas para o conector de retirada.

O pedido inclui itens a partir da aplicação, como os cabeçalhos de pedido, dados de cookie encriptado, o utilizador que torna o pedido e o ID do pedido. Embora os dados a partir do cookie encriptado são enviados com o pedido, o cookie de autenticação em si não é.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. O conector processar o pedido da fila. 

Com base na solicitação, o Proxy da aplicação executa uma das seguintes ações:

* Se o pedido é uma operação simples (por exemplo, não há nenhum dado dentro do corpo como está com um RESTful *obter* pedido), o conector faz uma ligação para o recurso interno de destino e, em seguida, aguarda uma resposta.

* Se o pedido tem dados associados a ele no corpo (por exemplo, um RESTful *POST* operação), o conector faz uma ligação de saída com o certificado de cliente para a instância do Proxy de aplicações. Faz esta ligação para pedir os dados e abrir uma ligação para o recurso interno. Depois de receber o pedido do conector do, o serviço de Proxy da aplicação começa a aceitar o conteúdo do usuário e encaminha dados para o conector. O conector, por sua vez, encaminha os dados para o recurso interno.

#### <a name="4-the-connector-waits-for-a-response"></a>4. O conector aguarda uma resposta.

Depois do pedido e transmissão de todo o conteúdo para o back-end estiver concluída, o conector aguarda uma resposta.

Depois de receber uma resposta, o conector faz uma ligação de saída para o serviço de Proxy de aplicações, para devolver os detalhes do cabeçalho e iniciar a transmissão em fluxo os dados retornados.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. O serviço transmite os dados para o usuário. 

Algum processamento do aplicativo pode ocorrer aqui. Se tiver configurado o Proxy de aplicações para traduzir cabeçalhos ou URLs em seu aplicativo, esse processamento ocorre conforme necessário durante este passo.


## <a name="next-steps"></a>Passos Seguintes

[Considerações de topologia de rede ao utilizar o Proxy de aplicações do Azure AD](application-proxy-network-topology.md)

[Compreender os conectores de Proxy de aplicações do Azure AD](application-proxy-connectors.md)
