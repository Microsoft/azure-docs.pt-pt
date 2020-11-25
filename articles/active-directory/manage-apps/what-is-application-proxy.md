---
title: Publicar aplicativos no local com Proxy aplicação AD Azure
description: Compreenda porquê usar o Application Proxy para publicar aplicações web no local externamente para utilizadores remotos. Saiba mais sobre a arquitetura proxy de aplicação, conectores, métodos de autenticação e benefícios de segurança.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/31/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: bc3352dd57a76cbb0b38455adfa11987ec93781a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003026"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>Utilizar o Azure AD Application Proxy para publicar aplicações no local para utilizadores remotos

O Azure Ative Directory (Azure AD) oferece muitas capacidades para proteger utilizadores, apps e dados na nuvem e no local. Em particular, a funcionalidade Azure AD Application Proxy pode ser implementada por profissionais de TI que queiram publicar aplicações web no local externamente. Os utilizadores remotos que necessitem de acesso a aplicações internas podem então aceder-lhes de forma segura.

A capacidade de aceder de forma segura a aplicações internas de fora da sua rede torna-se ainda mais crítica no local de trabalho moderno. Com cenários como BYOD (Bring Your Own Device) e dispositivos móveis, os profissionais de TI são desafiados a cumprir dois objetivos:

* Capacitar os utilizadores finais para serem produtivos a qualquer momento e em qualquer lugar
* Proteger os ativos corporativos em todos os momentos

Muitas organizações acreditam que estão no controlo e protegidas quando existem recursos dentro dos limites das suas redes corporativas. Mas no local de trabalho digital de hoje, essa fronteira expandiu-se com dispositivos móveis geridos e recursos e serviços na nuvem. Agora é necessário gerir a complexidade de proteger as identidades e dados dos seus utilizadores armazenados nos seus dispositivos e apps.

Talvez já esteja a utilizar o AZure AD para gerir utilizadores na nuvem que precisam de aceder às aplicações do Microsoft 365 e de outras aplicações SaaS, bem como a aplicações web hospedadas no local. Se já tem Azure AD, pode alavancar-lo como um plano de controlo para permitir um acesso perfeito e seguro às suas aplicações no local. Ou talvez ainda estejas a pensar em mudar-te para a nuvem. Em caso afirmativo, pode iniciar a sua viagem até à nuvem implementando o Application Proxy e dando o primeiro passo para a construção de uma forte base de identidade.

Embora não seja abrangente, a lista abaixo ilustra algumas das coisas que pode permitir implementando app Proxy num cenário de coexistência híbrida:

* Publicar aplicações web no local externamente de forma simplificada sem um DMZ
* Suporte um único sign-on (SSO) através de dispositivos, recursos e aplicativos na nuvem e no local
* Suporte a autenticação multi-factor para apps na nuvem e no local
* Alavancar rapidamente as funcionalidades da nuvem com a segurança da Microsoft Cloud
* Centralizar a gestão da conta de utilizador
* Centralizar o controlo da identidade e da segurança
* Adicione ou remova automaticamente o acesso do utilizador a aplicações baseadas na adesão ao grupo

Este artigo explica como o Azure AD e o Application Proxy dão aos utilizadores remotos uma experiência única de ssto (SSO). Os utilizadores conectam-se de forma segura a aplicações no local sem um servidor VPN ou servidores de duas casas e regras de firewall. Este artigo ajuda-o a entender como o Application Proxy traz as capacidades e vantagens de segurança da nuvem para as suas aplicações web no local. Também descreve a arquitetura e as topologias que são possíveis.

## <a name="remote-access-in-the-past"></a>Acesso remoto no passado

Anteriormente, o seu plano de controlo para proteger os recursos internos dos atacantes, ao mesmo tempo que facilitava o acesso por utilizadores remotos, encontrava-se na DMZ, ou na rede de perímetros. Mas as soluções VPN e proxy reversas implementadas na DMZ usadas por clientes externos para aceder a recursos corporativos não são adequadas para o mundo da nuvem. Normalmente sofrem das seguintes desvantagens:

* Custos de hardware
* Manutenção da segurança (remendos, portas de monitorização, etc.)
* Autenticação de utilizadores na borda
* Autenticação de utilizadores para servidores web na rede de perímetro
* Manter o acesso VPN para utilizadores remotos com a distribuição e configuração do software do cliente VPN. Além disso, manter servidores unidos ao domínio na DMZ, que podem ser vulneráveis a ataques externos.

No mundo de hoje em nuvem, o AZure AD é mais adequado para controlar quem e o que entra na sua rede. O Azure AD Application Proxy integra-se com tecnologias modernas de autenticação e nuvem, como aplicações SaaS e fornecedores de identidade. Esta integração permite que os utilizadores acedam a apps a partir de qualquer lugar. Não só o App Proxy é mais adequado para o local de trabalho digital de hoje, como é mais seguro do que as soluções VPN e proxy reversa e mais fácil de implementar. Os utilizadores remotos podem aceder às suas aplicações no local da mesma forma que acedem à Microsoft e a outras aplicações SaaS integradas com Azure AD. Não precisa de alterar ou atualizar as suas aplicações para trabalhar com o Proxy de Aplicações. Além disso, o App Proxy não requer que abra ligações de entrada através da sua firewall. Com o App Proxy, basta defini-lo e esquecê-lo.

## <a name="the-future-of-remote-access"></a>O futuro do acesso remoto

No local de trabalho digital de hoje, os utilizadores trabalham em qualquer lugar com vários dispositivos e apps. A única constante é a identidade do utilizador. É por isso que o primeiro passo para uma rede segura hoje é usar as capacidades [de gestão de identidade da AZure](../../security/fundamentals/identity-management-overview.md) como seu plano de controlo de segurança. Um modelo que usa a identidade como seu plano de controlo é tipicamente composto pelos seguintes componentes:

* Um fornecedor de identidade para acompanhar os utilizadores e informações relacionadas com o utilizador.
* Diretório de dispositivos para manter uma lista de dispositivos que têm acesso a recursos corporativos. Este diretório inclui informações correspondentes do dispositivo (por exemplo, tipo de dispositivo, integridade, etc.).
* Serviço de avaliação de políticas para determinar se um utilizador e dispositivo está em conformidade com a política definida pelos administradores de segurança.
* A capacidade de conceder ou negar o acesso a recursos organizacionais.

Com o Application Proxy, o Azure AD acompanha os utilizadores que precisam de aceder a aplicações web publicadas no local e na nuvem. Fornece um ponto de gestão central para essas aplicações. Embora não seja necessário, recomenda-se que também ative o Acesso Condicional Azure AD. Ao definir condições para a autenticação e o acesso dos utilizadores, garante ainda que as pessoas certas têm acesso às aplicações.

**Nota:** É importante entender que o Azure AD Application Proxy se destina a substituir VPN ou proxy reverso para utilizadores de roaming (ou remoto) que precisam de acesso a recursos internos. Não se destina a utilizadores internos na rede corporativa. Os utilizadores internos que utilizam desnecessariamente o Proxy da aplicação podem introduzir problemas de desempenho inesperados e indesejáveis.

![Diretório Ativo Azure e todas as suas apps](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>Uma visão geral de como funciona o App Proxy

Application Proxy é um serviço AD Azure que configura no portal Azure. Permite-lhe publicar um ponto final de URL http/HTTPS externo na Nuvem Azure, que se conecta a um URL de servidor de aplicações interna na sua organização. Estas aplicações web no local podem ser integradas com Azure AD para suportar um único s-on. Os utilizadores finais podem então aceder a aplicações web no local da mesma forma que acedem ao Microsoft 365 e a outras aplicações SaaS.

Os componentes desta funcionalidade incluem o serviço Application Proxy, que funciona na nuvem, o conector Application Proxy, que é um agente leve que funciona num servidor no local, e o Azure AD, que é o fornecedor de identidade. Os três componentes trabalham em conjunto para proporcionar ao utilizador uma única experiência de acesso às aplicações web no local.

Após a sua sessão, os utilizadores externos podem aceder a aplicações web no local utilizando um URL familiar ou [as minhas apps](../user-help/my-apps-portal-end-user-access.md) a partir dos seus dispositivos de desktop ou iOS/MAC. Por exemplo, o App Proxy pode fornecer acesso remoto e um único acesso ao Ambiente de Trabalho Remoto, sites SharePoint, Tableau, Qlik, Outlook na web e aplicações de linha de negócios (LOB).

![Azure AD Application Proxy arquitetura](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>Autenticação

Existem várias formas de configurar uma aplicação para um único sign-on e o método que seleciona depende da autenticação que a sua aplicação utiliza. O Application Proxy suporta os seguintes tipos de aplicações:

* Aplicações Web
* APIs web que pretende expor a aplicações ricas em diferentes dispositivos
* Aplicações hospedadas atrás de um Gateway de Desktop Remoto
* Aplicativos de clientes ricos que estão integrados com a [Microsoft Authentication Library (MSAL)](../develop/v2-overview.md)

App Proxy trabalha com aplicações que usam o seguinte protocolo de autenticação nativa:

* **[Autenticação integrada do Windows (IWA)](application-proxy-configure-single-sign-on-with-kcd.md).** Para a IWA, os conectores Proxy de aplicação utilizam a Delegação Restrita Kerberos (KCD) para autenticar os utilizadores na aplicação Kerberos.

O App Proxy também suporta os seguintes protocolos de autenticação com integração de terceiros ou em cenários de configuração específicos:

* [**Autenticação baseada em cabeçalhos**](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-headers). Este método de inscrição utiliza um serviço de autenticação de terceiros chamado PingAccess e é utilizado quando a aplicação utiliza cabeçalhos para autenticação. Neste cenário, a autenticação é gerida pelo PingAccess.
* [**Autenticação baseada em formulários ou palavra-passe.**](application-proxy-configure-single-sign-on-password-vaulting.md) Com este método de autenticação, os utilizadores assinam a aplicação com um nome de utilizador e senha na primeira vez que acedem à sua aplicação. Após o primeiro início de sação, o Azure AD fornece o nome de utilizador e a palavra-passe à aplicação. Neste cenário, a autenticação é tratada pela Azure AD.
* [**Autenticação SAML**](application-proxy-configure-single-sign-on-on-premises-apps.md). O sign-on único baseado em SAML é suportado para aplicações que usam protocolos SAML 2.0 ou WS-Federation. Com um único sinal de SATURA, o Azure AD autentica-se na aplicação utilizando a conta Azure AD do utilizador.

Para obter mais informações sobre métodos suportados, consulte [escolher um único método de inscrição](sso-options.md#choosing-a-single-sign-on-method).

### <a name="security-benefits"></a>Benefícios de segurança

A solução de acesso remoto oferecida pela Application Proxy e pela Azure AD suporta vários benefícios de segurança que os clientes podem tirar partido, incluindo:

* **Acesso autenticado**. O Application Proxy é o mais adequado para publicar aplicações com [pré-autenticação](application-proxy-security.md#authenticated-access) para garantir que apenas as ligações autenticadas atingem a sua rede. Para aplicações publicadas com pré-autenticação, não é permitido o tráfego passar pelo serviço App Proxy para o seu ambiente no local, sem um token válido. A pré-autenticação, pela sua própria natureza, bloqueia um número significativo de ataques direcionados, uma vez que apenas as identidades autenticadas podem aceder à aplicação backend.
* **Acesso Condicional**. Os controlos de política mais ricos podem ser aplicados antes de serem estabelecidas as ligações à sua rede. Com acesso condicional, pode definir restrições no tráfego que permite aceder à sua aplicação backend. Cria políticas que restringem os insí de sessão com base na localização, força da autenticação e perfil de risco do utilizador. À medida que o Acesso Condicional evolui, mais controlos estão a ser adicionados para fornecer segurança adicional, como integração com a Microsoft Cloud App Security (MCAS). A integração do MCAS permite-lhe configurar uma aplicação no local para monitorização em [tempo real,](application-proxy-integrate-with-microsoft-cloud-application-security.md) aproveitando o Acesso Condicional às sessões de monitorização e controlo em tempo real com base em políticas de Acesso Condicional.
* **Interrupção do tráfego**. Todo o tráfego para a aplicação backend é encerrado no serviço Application Proxy na nuvem enquanto a sessão é restabelecida com o servidor backend. Esta estratégia de ligação significa que os seus servidores backend não estão expostos ao tráfego HTTP direto. Estão melhor protegidos contra ataques do DoS (negação de serviço) porque a sua firewall não está a ser atacada.
* **Todos os acessos são de saída.** Os conectores Application Proxy utilizam apenas ligações de saída para o serviço Application Proxy na nuvem sobre as portas 80 e 443. Sem ligações de entrada, não há necessidade de abrir portas de firewall para ligações ou componentes de entrada na Zona Desmilitarizada. Todas as ligações são de saída e sobre um canal seguro.
* **Inteligência baseada em Análise de Segurança e Aprendizagem automática (ML).** Por fazer parte do Azure Ative Directory, o Application Proxy pode alavancar [a Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) (requer licenciamento Premium [P2).](https://azure.microsoft.com/pricing/details/active-directory/) A Azure AD Identity Protection combina inteligência de segurança de aprendizagem automática com feeds de dados da Unidade de [Crimes Digitais](https://news.microsoft.com/stories/cybercrime/index.html) da Microsoft e do Microsoft Security [Response Center](https://www.microsoft.com/msrc) para identificar proativamente contas comprometidas. A Proteção de Identidade oferece proteção em tempo real contra inscrições de alto risco. Toma em consideração fatores como os acessos a dispositivos infetados, através de redes anonimizantes, ou de locais atípicos e improváveis para aumentar o perfil de risco de uma sessão. Este perfil de risco é utilizado para proteção em tempo real. Muitos destes relatórios e eventos já estão disponíveis através de uma API para integração com os seus sistemas SIEM.

* **Acesso remoto como serviço**. Não tem de se preocupar em manter e remendar servidores no local para permitir o acesso remoto. Application Proxy é um serviço de escala de internet que a Microsoft possui, por isso obtém sempre os mais recentes patches de segurança e atualizações. O software não remutado ainda é responsável por um grande número de ataques. De acordo com o Departamento de Segurança Interna, [85% dos ataques direcionados são evitáveis.](https://www.us-cert.gov/ncas/alerts/TA15-119A) Com este modelo de serviço, já não é necessário carregar o pesado fardo de gerir os seus servidores de borda e lutar para os corrigir conforme necessário.

* **Integração intune.** Com a Intune, o tráfego corporativo é encaminhado separadamente do tráfego pessoal. A Application Proxy garante que o tráfego corporativo é autenticado. [A aplicação Proxy e a capacidade do Navegador Gerido Intune](/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers) também podem ser usadas em conjunto para permitir que os utilizadores remotos acedam de forma segura a sites internos a partir de dispositivos iOS e Android.

### <a name="roadmap-to-the-cloud"></a>Roteiro para a nuvem

Outro grande benefício da implementação da Aplicação Proxy é estender o Azure AD ao seu ambiente no local. Na verdade, implementar o App Proxy é um passo fundamental para mover a sua organização e aplicações para a nuvem. Ao mover-se para a nuvem e para longe da autenticação no local, reduz a sua pegada no local e utiliza as capacidades de gestão de identidade da Azure AD como seu plano de controlo. Com atualizações mínimas ou sem atualizações para as aplicações existentes, tem acesso a capacidades de cloud, como a autenticação de cada sinal, a autenticação multi-fator e a gestão central. Instalar os componentes necessários para app Proxy é um processo simples para estabelecer uma estrutura de acesso remoto. E ao mover-se para a nuvem, você tem acesso às mais recentes funcionalidades, atualizações e funcionalidades do AZure, como alta disponibilidade e recuperação de desastres.

Para saber mais sobre a migração das suas apps para Azure AD, consulte o Livro Branco [do Diretório Ativo migrando para o Azure Ative.](https://aka.ms/migrateapps/whitepaper)

## <a name="architecture"></a>Arquitetura

O diagrama que se segue ilustra em geral como os serviços de autenticação AD da Azure e o Application Proxy trabalham em conjunto para fornecer aplicações únicas de inscrição no local aos utilizadores finais.

![Fluxo de autenticação de procuração de aplicação Azure AD](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. Depois de o utilizador ter acedido à aplicação através de um ponto final, o utilizador é redirecionado para a página de insusição Azure. Se configurar políticas de acesso condicional, as condições específicas são verificadas neste momento para garantir que cumpre os requisitos de segurança da sua organização.
2. Após uma sinstrução bem sucedida, a Azure AD envia um sinal para o dispositivo cliente do utilizador.
3. O cliente envia o token para o serviço Application Proxy, que recupera o nome principal do utilizador (UPN) e o nome principal de segurança (SPN) do token.
4. O Application Proxy reencaminha o pedido, que é recolhido pelo [conector](application-proxy-connectors.md)Application Proxy .
5. O conector realiza qualquer autenticação adicional necessária em nome do utilizador ( Opcional dependendo do método de *autenticação),* solicita o ponto final interno do servidor de aplicações e envia o pedido para a aplicação no local.
6. A resposta do servidor de aplicações é enviada através do conector para o serviço Application Proxy.
7. A resposta é enviada do serviço Application Proxy para o utilizador.

|**Componente**|**Descrição**|
|:-|:-|
|Ponto final|O ponto final é um URL ou um [portal de utilizador final](end-user-experiences.md). Os utilizadores podem chegar a aplicações fora da sua rede acedendo a um URL externo. Os utilizadores dentro da sua rede podem aceder à aplicação através de um URL ou de um portal de utilizador final. Quando os utilizadores vão a um destes pontos finais, autenticam-se em Azure AD e depois são encaminhados através do conector para a aplicação no local.|
|Azure AD|A Azure AD realiza a autenticação utilizando o diretório de inquilinos armazenado na nuvem.|
|Serviço de procuração de aplicação|Este serviço de Procuração de Aplicações funciona na nuvem como parte do Azure AD. Transmite o sinal de inscrição do utilizador para o Conector Proxy da aplicação. Application Proxy encaminha quaisquer cabeçalhos acessíveis no pedido e define os cabeçalhos de acordo com o seu protocolo, para o endereço IP do cliente. Se o pedido de entrada ao representante já tiver esse cabeçalho, o endereço IP do cliente é adicionado ao final da lista separada por vírgula que é o valor do cabeçalho.|
|Conector Proxy de aplicação|O conector é um agente leve que funciona num Servidor Windows dentro da sua rede. O conector gere a comunicação entre o serviço Application Proxy na nuvem e a aplicação no local. O conector só utiliza ligações de saída, para que não tenha de abrir portas de entrada ou colocar nada na Zona Desmilitarizada. Os conectores são apátridas e retiram informações da nuvem se necessário. Para obter mais informações sobre conectores, como a forma como se load-balance e autentica, consulte [os conectores Proxy da aplicação AD Azure](application-proxy-connectors.md).|
|Diretório Ativo (AD)|O Ative Directory funciona no local para efetuar a autenticação para contas de domínio. Quando o único sinal de sação é configurado, o conector comunica com AD para efetuar qualquer autenticação adicional necessária.|
|Aplicação no local|Por fim, o utilizador pode aceder a uma aplicação no local.|

O Azure AD Application Proxy é composto pelo serviço de procuração de aplicações baseado na nuvem e um conector no local. O conector ouve pedidos do serviço Application Proxy e trata das ligações com as aplicações internas. É importante notar que todas as comunicações ocorrem sobre o TLS, e sempre têm origem no conector do serviço Application Proxy. Ou seja, as comunicações são apenas de saída. O conector utiliza um certificado de cliente para autenticar o serviço de Procuração de Aplicações para todas as chamadas. A única exceção à garantia de ligação é o primeiro passo de configuração onde o certificado do cliente é estabelecido. Consulte o Application Proxy [Under the hood](application-proxy-security.md#under-the-hood) para obter mais detalhes.

### <a name="application-proxy-connectors"></a>Conectores Proxy de aplicação

[Os conectores Proxy de aplicação](application-proxy-connectors.md) são agentes leves implantados no local que facilitam a ligação de saída ao serviço Application Proxy na nuvem. Os conectores devem ser instalados num Servidor windows que tenha acesso à aplicação backend. Os utilizadores conectam-se ao serviço de cloud Proxy da App que encaminha o seu tráfego para as aplicações através dos conectores, conforme ilustrado abaixo.

![Ligações de rede de procuração de aplicação Azure AD](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

A configuração e o registo entre um conector e o serviço App Proxy é realizado da seguinte forma:

1. O administrador de TI abre as portas 80 e 443 para o tráfego de saída e permite o acesso a vários URLs que são necessários pelo conector, o serviço App Proxy e Azure AD.
2. O administrador assina no portal Azure e executa um executável para instalar o conector num servidor Windows no local.
3. O conector começa a "ouvir" o serviço App Proxy.
4. O administrador adiciona a aplicação no local ao Azure AD e configura as definições, como os URLs, que os utilizadores de URLs precisam de se conectar às suas apps.

Para obter mais informações, consulte [Plan a Azure AD Application Proxy deployment](application-proxy-deployment-plan.md).

Recomenda-se que implante sempre vários conectores para redundância e escala. Os conectores, em conjunto com o serviço, cuidam de todas as tarefas de alta disponibilidade e podem ser adicionados ou removidos dinamicamente. Cada vez que um novo pedido chega é encaminhado para um dos conectores que está disponível. Quando um conector está em funcionamento, permanece ativo à medida que se liga ao serviço. Se um conector estiver temporariamente indisponível, não responde a este tráfego. Os conectores não reutilizados são marcados como inativos e removidos após 10 dias de inatividade.

Os conectores também pesquisam o servidor para saber se existe uma versão mais recente do conector. Embora possa fazer uma atualização manual, os conectores serão atualizados automaticamente desde que o serviço de atualização do conector de aplicação da aplicação esteja em execução. Para os inquilinos com vários conectores, as atualizações automáticas direcionam-se para um conector de cada grupo para evitar tempo de inatividade no seu ambiente.

**Nota:** Pode monitorizar a [página de histórico](application-proxy-release-version-history.md) da versão Proxy da aplicação para ser notificada quando as atualizações tiverem sido lançadas, subscrevendo o seu feed RSS.

Cada conector Proxy de aplicação é atribuído a um [grupo de conector](application-proxy-connector-groups.md). Os conectores do mesmo grupo de conector atuam como uma única unidade para uma elevada disponibilidade e equilíbrio de carga. Pode criar novos grupos, atribuir-lhes conectores no portal Azure e, em seguida, atribuir conectores específicos para servir aplicações específicas. Recomenda-se ter pelo menos dois conectores em cada grupo de conector para uma alta disponibilidade.

Os grupos de conector são úteis quando precisa de suportar os seguintes cenários:

* Publicação de aplicativos geográficos
* Segmentação/isolamento de aplicações
* Publicar aplicativos web em execução na nuvem ou no local

Para obter mais informações sobre a escolha de onde instalar os seus conectores e otimizar a sua rede, consulte [considerações de topologia da Rede ao utilizar o Azure Ative Directory Application Proxy](application-proxy-network-topology.md).

## <a name="other-use-cases"></a>Outros casos de uso

Até este momento, focamo-nos em usar o Application Proxy para publicar aplicações no local externamente, permitindo um único acesso a todas as suas aplicações de cloud e on-in. No entanto, existem outros casos de uso para App Proxy que vale a pena mencionar. Estas incluem:

* **Publicar securely REST APIs**. Quando tem lógica de negócio ou APIs a funcionar no local ou hospedado em máquinas virtuais na nuvem, o Application Proxy fornece um ponto final público para acesso a API. O acesso ao ponto final da API permite controlar a autenticação e a autorização sem exigir portas de entrada. Fornece segurança adicional através de funcionalidades Azure AD Premium, como a autenticação de vários fatores e acesso condicional baseado em dispositivos para desktops, iOS, MAC e Dispositivos Android que utilizam dispositivos Intune. Para saber mais, consulte [Como permitir que as aplicações de clientes nativos interajam com aplicações de procuração](application-proxy-configure-native-client-application.md) e [protejam uma API utilizando o OAuth 2.0 com Ozure Ative Directory e API Management.](../../api-management/api-management-howto-protect-backend-with-aad.md)
* **Serviços remotos de desktop** **(RDS)**. As implementações padrão de RDS requerem ligações de entrada abertas. No entanto, a [implementação de RDS com Application Proxy](application-proxy-integrate-with-remote-desktop-services.md) tem uma ligação de saída permanente a partir do servidor que executa o serviço de conector. Desta forma, pode oferecer mais aplicações aos utilizadores finais publicando aplicações no local através de Serviços de Desktop Remoto. Também pode reduzir a superfície de ataque da implementação com um conjunto limitado de verificação em duas etapas e controlos de acesso condicional a RDS.
* **Publique aplicações que se conectem através de WebSockets**. O suporte com [a Qlik Sense](application-proxy-qlik.md) está em Visualização Pública e será expandido para outras aplicações no futuro.
* **Permitir que as aplicações de clientes nativos interajam com aplicações proxy**. Pode utilizar o Azure AD Application Proxy para publicar aplicações web, mas também pode ser usado para publicar [aplicações de clientes nativos](application-proxy-configure-native-client-application.md) que estão configuradas com a Biblioteca de Autenticação AD AD (ADAL). As aplicações de clientes nativos diferem das aplicações web porque estão instaladas num dispositivo, enquanto as aplicações web são acedidas através de um navegador.

## <a name="conclusion"></a>Conclusão

A forma como trabalhamos e as ferramentas que utilizamos estão a mudar rapidamente. Com mais colaboradores a trazerem os seus próprios dispositivos para o trabalho e o uso generalizado de aplicações Software-as-a-Service (SaaS), a forma como as organizações gerem e protegem os seus dados também deve evoluir. As empresas já não operam apenas dentro das suas próprias muralhas, protegidas por um fosso que rodeia a sua fronteira. Os dados viajam para mais locais do que nunca, tanto no local como em ambientes em nuvem. Esta evolução tem ajudado a aumentar a produtividade e a capacidade de colaboração dos utilizadores, mas também torna a proteção de dados sensíveis mais desafiante.

Se está a utilizar a Azure AD para gerir os utilizadores num cenário de coexistência híbrida ou se está interessado em iniciar a sua viagem até à nuvem, implementar o Azure AD Application Proxy pode ajudar a reduzir o tamanho da sua pegada no local, fornecendo acesso remoto como serviço.

As organizações devem começar a aproveitar-se hoje da App Proxy para tirar partido dos seguintes benefícios:

* Publicar aplicativos no local externamente sem a sobrecarga associada à manutenção de soluções tradicionais de VPN ou outras soluções de publicação web no local e abordagem DMZ
* Único s-on para todas as aplicações, sejam elas microsoft 365 ou outras aplicações SaaS e incluindo aplicações no local
* Segurança na escala de nuvem onde a Azure AD aproveita a telemetria microsoft 365 para impedir o acesso não autorizado
* Integração intune para garantir a autenticação do tráfego corporativo
* Centralização da gestão da conta de utilizador
* Atualizações automáticas para garantir que tem os mais recentes patches de segurança
* Novas funcionalidades à medida que são lançadas; o mais recente suporte para a gestão única de saml e mais gestão granular de cookies de aplicação

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre planeamento, operação e gestão da aplicação Azure AD, consulte [Plan a Azure AD Application Proxy deployment](application-proxy-deployment-plan.md).
* Para agendar uma demonstração ao vivo ou obter um teste gratuito de 90 dias para avaliação, consulte [Começar com a Mobilidade Empresarial + Segurança.](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial)