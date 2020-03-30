---
title: Considerações de segurança para a Procuração de Aplicação da AD Azure [ Microsoft Docs
description: Cobre considerações de segurança para a utilização de procuração de aplicação ad-a.azure
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fd6794bafc3c209032f32626e8c46b51769d05e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481233"
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Considerações de segurança para aceder remotamente a apps com procuração de aplicação ad-ad-azure

Este artigo explica os componentes que funcionam para manter os seus utilizadores e aplicações seguros quando utiliza o Proxy de Aplicação de Diretório Ativo Azure.

O diagrama que se segue mostra como o Azure AD permite um acesso remoto seguro às aplicações no local.

 ![Diagrama de acesso remoto seguro através de Procuração de Aplicação AD Azure](./media/application-proxy-security/secure-remote-access.png)

## <a name="security-benefits"></a>Benefícios de segurança

A Procuração de Aplicações Azure AD oferece os seguintes benefícios de segurança:

### <a name="authenticated-access"></a>Acesso autenticado 

Se optar por utilizar a pré-autenticação do Diretório Ativo Azure, apenas as ligações autenticadas podem aceder à sua rede.

A Procuração de Aplicações Azure AD baseia-se no serviço de fichas de segurança Azure AD (STS) para toda a autenticação.  A pré-autenticação, pela sua própria natureza, bloqueia um número significativo de ataques anónimos, porque só identidades autenticadas podem aceder à aplicação back-end.

Se escolher a Passthrough como o seu método de pré-autenticação, não obtém este benefício. 

### <a name="conditional-access"></a>Acesso Condicional

Aplique controlos políticos mais ricos antes de estabelecer ligações à sua rede.

Com [acesso condicional,](../conditional-access/overview.md)pode definir restrições sobre o tráfego que é permitido aceder às suas aplicações de back-end. Pode criar políticas que restringem os sign-ins com base na localização, na força da autenticação e no perfil de risco do utilizador.

Também pode utilizar o Acesso Condicional para configurar as políticas de autenticação multi-factor, adicionando outra camada de segurança às autenticações do utilizador. Além disso, as suas aplicações também podem ser encaminhadas para o Microsoft Cloud App Security via Azure AD Conditional Access para fornecer monitorização e controloem em tempo real, através de políticas de [acesso](https://docs.microsoft.com/cloud-app-security/access-policy-aad) e [sessão](https://docs.microsoft.com/cloud-app-security/session-policy-aad)

### <a name="traffic-termination"></a>Interrupção de trânsito

Todo o tráfego está terminado na nuvem.

Como o Proxy de Aplicação AD Azure é um proxy inverso, todo o tráfego para aplicações de back-end é encerrado no serviço. A sessão só pode ser restabelecida com o servidor back-end, o que significa que os seus servidores de back-end não estão expostos ao tráfego HTTP direto. Esta configuração significa que está melhor protegido de ataques direcionados.

### <a name="all-access-is-outbound"></a>Todo o acesso está de saída 

Não precisas de abrir ligações de entrada para a rede corporativa.

Os conectores Proxy de aplicação utilizam apenas ligações de saída ao serviço de procuração de aplicações Da AD Azure, o que significa que não há necessidade de abrir portas de firewall para ligações de entrada. Os proxies tradicionais exigiam uma rede de perímetro (também conhecida como *DMZ,* *zona desmilitarizada*, ou *sub-rede rastreada*) e permitiam o acesso a ligações não autenticadas na borda da rede. Este cenário exigia investimentos em produtos de firewall de aplicação web para analisar o tráfego e proteger o ambiente. Com o Application Proxy, não precisa de uma rede de perímetro porque todas as ligações estão de saída e ocorrem sobre um canal seguro.

Para obter mais informações sobre conectores, consulte [Conectores proxy de aplicação ad azure](application-proxy-connectors.md).

### <a name="cloud-scale-analytics-and-machine-learning"></a>Análise à escala de nuvem e aprendizagem automática 

Obter proteção de segurança de ponta.

Como faz parte do Diretório Ativo do Azure, o Application Proxy pode alavancar a Proteção de [Identidade Azure AD,](../active-directory-identityprotection.md)com dados do Microsoft Security Response Center e da Unidade de Crimes Digitais. Juntos, identificamos proactivamente contas comprometidas e oferecemos proteção contra inscrições de alto risco. Temos em conta numerosos fatores para determinar quais as tentativas de entrada são de alto risco. Estes fatores incluem a sinalização de dispositivos infetados, redes anoonísta e locais atípicos ou improváveis.

Muitos destes relatórios e eventos já estão disponíveis através de uma API para integração com os seus sistemas de informação de segurança e gestão de eventos (SIEM).

### <a name="remote-access-as-a-service"></a>Acesso remoto como serviço

Não tens de te preocupar em manter e remendar servidores no local.

O software não remendo ainda é responsável por um grande número de ataques. O Azure AD Application Proxy é um serviço à escala da Internet que a Microsoft detém, pelo que obtém sempre os mais recentes patches de segurança e atualizações.

Para melhorar a segurança das aplicações publicadas pela Azure AD Application Proxy, bloqueamos os robôs web crawler de indexar e arquivar as suas aplicações. Cada vez que um robô web crawler tenta recuperar as definições do robô para uma `User-agent: * Disallow: /`aplicação publicada, application Proxy responde com um ficheiro robots.txt que inclui .

#### <a name="azure-ddos-protection-service"></a>Serviço de proteção Azure DDoS

As aplicações publicadas através do Application Proxy estão protegidas contra ataques de Negação de Serviço Distribuído (DDoS). **A proteção Azure DDoS** é um serviço oferecido com a plataforma Azure para proteger os seus recursos Azure de negação de ataques de serviço. O nível de serviço **Básico** está automaticamente ativado, proporcionando uma monitorização de tráfego sempre ativa e uma mitigação em tempo real de ataques comuns ao nível da rede. Um nível **Standard** também está disponível, oferecendo capacidades adicionais de mitigação que são sintonizadas especificamente aos recursos da Rede Virtual Azure. Para mais detalhes, consulte a [visão geral da Norma de Proteção DDoS azure](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

## <a name="under-the-hood"></a>Os bastidores

A Procuração de Aplicação Azure AD é constituída por duas partes:

* O serviço baseado na nuvem: Este serviço funciona em Azure, e é onde são feitas as ligações cliente/utilizador externos.
* [O conector no local](application-proxy-connectors.md): Um componente no local, o conector ouve pedidos do serviço de procuração de aplicações AD Azure e trata as ligações às aplicações internas. 

É estabelecido um fluxo entre o conector e o serviço proxy de aplicação quando:

* O conector está preparado primeiro.
* O conector retira informações de configuração do serviço Proxy de Aplicação.
* Um utilizador acede a uma aplicação publicada.

>[!NOTE]
>Todas as comunicações ocorrem sobre TLS, e elas sempre originam-se no conector do serviço Proxy de Aplicação. O serviço está apenas de saída.

O conector utiliza um certificado de cliente para autenticar o serviço Proxy de Aplicação para quase todas as chamadas. A única exceção a este processo é a etapa inicial de configuração, onde o certificado de cliente está estabelecido.

### <a name="installing-the-connector"></a>Instalação do conector

Quando o conector é configurado pela primeira vez, os seguintes eventos de fluxo ocorrem:

1. O registo do conector ao serviço ocorre como parte da instalação do conector. Os utilizadores são solicitados a introduzir as suas credenciais de administração Azure AD.O símbolo adquirido a partir desta autenticação é então apresentado ao serviço de Procuração de Aplicações AD Azure.
2. O serviço De Procuração de Aplicações avalia o símbolo. Verifica se o utilizador é administrador da empresa no inquilino.Se o utilizador não for administrador, o processo é encerrado.
3. O conector gera um pedido de certificado de cliente e passa-o, juntamente com o símbolo, para o serviço Proxy de Aplicação. O serviço, por sua vez, verifica o símbolo e assina o pedido de certificado de cliente.
4. O conector utiliza o certificado de cliente para futura comunicação com o serviço Proxy de Aplicação.
5. O conector executa uma recolha inicial dos dados de configuração do sistema a partir do serviço utilizando o seu certificado de cliente, estando agora pronto a receber pedidos.

### <a name="updating-the-configuration-settings"></a>Atualizar as definições de configuração

Sempre que o serviço Proxy de Aplicação atualiza as definições de configuração, os seguintes eventos de fluxo ocorrem:

1. O conector liga-se ao ponto final de configuração dentro do serviço Proxy de Aplicação utilizando o seu certificado de cliente.
2. Após a validação do certificado de cliente, o serviço Proxy de aplicação devolve dados de configuração ao conector (por exemplo, o grupo de conector do que o conector deve fazer parte).
3. Se o certificado atual tiver mais de 180 dias, o conector gera um novo pedido de certificado, que atualiza efetivamente o certificado de cliente a cada 180 dias.

### <a name="accessing-published-applications"></a>Acesso a aplicações publicadas

Quando os utilizadores acedem a uma aplicação publicada, os seguintes eventos ocorrem entre o serviço Proxy de Aplicação e o conector Proxy de Aplicação:

1. O serviço autentica o utilizador para a app
2. O serviço coloca um pedido na fila do conector
3. Um conector processa o pedido da fila
4. O conector aguarda uma resposta
5. O serviço transmite dados ao utilizador

Para saber mais sobre o que acontece em cada um destes passos, continue a ler.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. O serviço autentica o utilizador para a aplicação

Se configurar a aplicação para utilizar a Passthrough como método de pré-autenticação, os passos nesta secção são ignorados.

Se configurar a aplicação para pré-autenticar com o Azure AD, os utilizadores são redirecionados para o Azure AD STS para autenticar, e os seguintes passos ocorrem:

1. Procuração de aplicação verifica quaisquer requisitos de política de acesso condicional para a aplicação específica. Este passo garante que o utilizador foi designado para a aplicação. Se for necessária uma verificação em duas etapas, a sequência de autenticação solicita ao utilizador um segundo método de autenticação.

2. Depois de todas as verificações terem sido aprovadas, o Azure AD STS emite um sinal assinado para a aplicação e redireciona o utilizador de volta para o serviço Proxy de aplicação.

3. A Procuração de Aplicações verifica que o token foi emitido para a aplicação correta. Também realiza outros controlos, tais como garantir que o símbolo foi assinado pela Azure AD, e que ainda se encontra dentro da janela válida.

4. Aplicação Proxy define um cookie de autenticação encriptado para indicar que a autenticação na aplicação ocorreu. O cookie inclui um carimbo de tempo de validade baseado no símbolo da Azure AD e outros dados, como o nome do utilizador em que a autenticação se baseia. O cookie é encriptado com uma chave privada conhecida apenas pelo serviço Proxy de Aplicação.

5. Aplicação Proxy redireciona o utilizador de volta para o URL originalmente solicitado.

Se alguma parte das etapas de pré-autenticação falhar, o pedido do utilizador é negado e o utilizador é mostrado uma mensagem indicando a origem do problema.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. O serviço coloca um pedido na fila do conector

Os conectores mantêm uma ligação de saída aberta ao serviço proxy de aplicação. Quando um pedido chega, o serviço faz fila o pedido numa das ligações abertas para o conector recolher.

O pedido inclui itens da aplicação, tais como os cabeçalhos de pedido, dados do cookie encriptado, o utilizador que faz o pedido e o ID de pedido. Embora os dados do cookie encriptado sejam enviados com o pedido, o cookie de autenticação em si não é.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. O conector processa o pedido da fila. 

Com base no pedido, a Application Proxy realiza uma das seguintes ações:

* Se o pedido for uma operação simples (por exemplo, não existem dados dentro do corpo como é com um pedido DE *GET* RESTful), o conector faz uma ligação ao recurso interno alvo e depois aguarda uma resposta.

* Se o pedido tiver dados associados a ele no organismo (por exemplo, uma operação RESTful *POST),* o conector faz uma ligação de saída utilizando o certificado de cliente para a instância proxy de aplicação. Faz com que esta ligação solicite os dados e abra uma ligação com o recurso interno. Depois de receber o pedido do conector, o serviço Proxy de Aplicação começa a aceitar conteúdo do utilizador e reencaminha os dados para o conector. O conector, por sua vez, encaminha os dados para o recurso interno.

#### <a name="4-the-connector-waits-for-a-response"></a>4. O conector aguarda uma resposta.

Após o pedido e transmissão de todos os conteúdos para a parte de trás, o conector aguarda uma resposta.

Depois de receber uma resposta, o conector faz uma ligação de saída ao serviço Proxy de Aplicação, para devolver os dados do cabeçalho e começar a transmitir os dados de devolução.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. O serviço transmite dados ao utilizador. 

Pode ocorrer aqui algum processamento da aplicação. Se configurar o Application Proxy para traduzir cabeçalhos ou URLs na sua aplicação, esse processamento acontece conforme necessário durante este passo.


## <a name="next-steps"></a>Passos seguintes

[Considerações de topologia de rede ao usar o Proxy de Aplicação AD Azure](application-proxy-network-topology.md)

[Compreender os conectores de procuração de aplicação da AD Azure](application-proxy-connectors.md)
