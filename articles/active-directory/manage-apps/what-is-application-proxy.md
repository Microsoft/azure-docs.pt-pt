---
title: Publicar aplicações no local com procuração de aplicação ad ad azure
description: Compreenda por que utilizar o Application Proxy para publicar aplicações web no local externamente para utilizadores remotos. Conheça a arquitetura da Aplicação Proxy, conectores, métodos de autenticação e benefícios de segurança.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 05/31/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: d38cf25bb3b7622a0d444e4a71a4d62aafc053b6
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196455"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>Utilização de Procuração de Aplicação AD Azure para publicar aplicações no local para utilizadores remotos

O Azure Ative Directory (Azure AD) oferece muitas capacidades para proteger utilizadores, apps e dados na nuvem e no local. Em particular, a funcionalidade De Proxy de Aplicação AD Azure pode ser implementada por profissionais de TI que queiram publicar aplicações web no local externamente. Os utilizadores remotos que precisam de acesso a aplicações internas podem aceder-lhes de forma segura.

A capacidade de aceder de forma segura a aplicações internas de fora da sua rede torna-se ainda mais crítica no local de trabalho moderno. Com cenários como BYOD (Bring Your Own Device) e dispositivos móveis, os profissionais de TI são desafiados a cumprir dois objetivos:

* Capacitar os utilizadores finais a serem produtivos a qualquer momento e em qualquer lugar
* Proteja sempre os ativos corporativos

Muitas organizações acreditam que estão no controlo e protegidas quando os recursos existem dentro dos limites das suas redes corporativas. Mas no local de trabalho digital de hoje, essa fronteira expandiu-se com dispositivos móveis geridos e recursos e serviços na nuvem. Agora precisa de gerir a complexidade de proteger as identidades dos seus utilizadores e os dados armazenados nos seus dispositivos e aplicações.

Talvez já esteja a usar o Azure AD para gerir os utilizadores na nuvem que precisam de aceder ao Office 365 e a outras aplicações SaaS, bem como aplicações web alojadas no local. Se já tem AD Azure, pode alavancar como um plano de controlo para permitir um acesso perfeito e seguro às suas aplicações no local. Ou talvez ainda estejas a pensar em mudar-te para a nuvem. Em caso afirmativo, pode iniciar a sua viagem até à nuvem implementando a Application Proxy e dando o primeiro passo para a construção de uma forte base de identidade.

Embora não seja abrangente, a lista abaixo ilustra algumas das coisas que pode permitir implementando app proxy num cenário de coexistência híbrida:

* Publique aplicações web no local externamente de forma simplificada sem Um DMZ
* Suporte um único sign-on (SSO) através de dispositivos, recursos e aplicações na nuvem e no local
* Apoiar a autenticação de vários fatores para aplicações na nuvem e no local
* Aproveite rapidamente as funcionalidades da nuvem com a segurança do Microsoft Cloud
* Centralizar a gestão da conta de utilizador
* Centralizar o controlo da identidade e segurança
* Adicione ou remova automaticamente o acesso do utilizador a aplicações com base na adesão ao grupo

Este artigo explica como a Azure AD e a Application Proxy dão aos utilizadores remotos uma única experiência de inscrição (SSO). Os utilizadores conectam-se de forma segura a aplicações no local sem uma VPN ou servidores de dois homed e regras de firewall. Este artigo ajuda-o a entender como o Application Proxy traz as capacidades e vantagens de segurança da nuvem para as suas aplicações web no local. Também descreve a arquitetura e as topoologias que são possíveis.

## <a name="remote-access-in-the-past"></a>Acesso remoto no passado

Anteriormente, o seu plano de controlo para proteger os recursos internos dos atacantes, enquanto facilitava o acesso por utilizadores remotos, estava tudo na Rede DeSm, ou na rede de perímetro. Mas as soluções VPN e proxy inversa saqueadas no DMZ usadopor clientes externos para aceder a recursos corporativos não são adequadas ao mundo da nuvem. Normalmente sofrem das seguintes desvantagens:

* Custos de hardware
* Manutenção da segurança (remendos, portas de monitorização, etc.)
* Autenticar utilizadores na borda
* Autenticar utilizadores a servidores web na rede de perímetro
* Manter o acesso VPN para utilizadores remotos com a distribuição e configuração do software de cliente VPN. Além disso, manter servidores unidos pelo domínio no DMZ, que podem ser vulneráveis a ataques externos.

No mundo atual, o Azure AD é mais adequado para controlar quem e o que entra na sua rede. A Procuração de Aplicações AD Azure integra-se com tecnologias modernas de autenticação e baseadas na nuvem, como aplicações SaaS e fornecedores de identidade. Esta integração permite que os utilizadores acedam a apps de qualquer lugar. Não só a App Proxy é mais adequada para o local de trabalho digital de hoje, como é mais segura do que as soluções de procuração inversa e inversa e mais fácil de implementar. Os utilizadores remotos podem aceder às suas aplicações no local da mesma forma que acedem ao O365 e a outras aplicações SaaS integradas com a AD Azure. Não precisa de alterar ou atualizar as suas aplicações para trabalhar com o Proxy de Aplicações. Além disso, o App Proxy não requer que abra ligações de entrada através da sua firewall. Com o App Proxy, basta defini-lo e esquecê-lo.

## <a name="the-future-of-remote-access"></a>O futuro do acesso remoto

No local de trabalho digital de hoje, os utilizadores trabalham em qualquer lugar com vários dispositivos e aplicações. A única constante é a identidade do utilizador. É por isso que o primeiro passo para uma rede segura hoje é usar as capacidades de [gestão de identidade da Azure AD](https://docs.microsoft.com/azure/security/security-identity-management-overview) como o seu plano de controlo de segurança. Um modelo que utiliza a identidade como seu plano de controlo é tipicamente composto pelos seguintes componentes:

* Um fornecedor de identidade para acompanhar os utilizadores e informações relacionadas com o utilizador.
* Diretório de dispositivos para manter uma lista de dispositivos que têm acesso a recursos corporativos. Este diretório inclui informações correspondentes do dispositivo (por exemplo, tipo de dispositivo, integridade, etc.).
* Serviço de avaliação de políticas para determinar se um utilizador e um dispositivo estão em conformidade com a política definida pelos administradores de segurança.
* A capacidade de conceder ou negar o acesso a recursos organizacionais.

Com a Aplicação Proxy, a Azure AD mantém o registo dos utilizadores que precisam de aceder a aplicações web publicadas no local e na nuvem. Fornece um ponto de gestão central para essas aplicações. Apesar de não ser necessário, recomenda-se que também ative o Acesso Condicional Azure AD. Ao definir condições para a forma como os utilizadores autenticam e têm acesso, garante ainda que as pessoas certas têm acesso às aplicações.

**Nota:** É importante entender que o Proxy de Aplicação AD Azure pretende ser uma VPN ou uma substituição de procuração inversa para utilizadores de roaming (ou remoto) que precisam de acesso a recursos internos. Não é destinado a utilizadores internos na rede corporativa. Os utilizadores internos que utilizam desnecessariamente o Application Proxy podem introduzir problemas de desempenho inesperados e indesejáveis.

![Diretório Ativo Azure e todas as suas aplicações](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>Uma visão geral de como o App Proxy funciona

Application Proxy é um serviço Azure AD que configura no portal Azure. Permite-lhe publicar um ponto final de URL PÚBLICO/HTTPS externo na Nuvem Azure, que se conecta a um URL de servidor de aplicações interna na sua organização. Estas aplicações web no local podem ser integradas com a Azure AD para suportar um único sign-on. Os utilizadores finais podem então aceder às aplicações web no local da mesma forma que acedem ao Office 365 e a outras aplicações SaaS.

Os componentes desta funcionalidade incluem o serviço Application Proxy, que funciona na nuvem, o conector Application Proxy, que é um agente leve que funciona num servidor no local, e a Azure AD, que é o fornecedor de identidade. Os três componentes trabalham em conjunto para proporcionar ao utilizador uma única experiência de acesso às aplicações web no local.

Após a essão, os utilizadores externos podem aceder às aplicações web no local utilizando um URL familiar ou o painel de [acesso MyApps](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access) a partir do seu ambiente de trabalho ou dispositivos iOS/MAC. Por exemplo, o App Proxy pode fornecer acesso remoto e um único sinal para aplicações de ambiente de trabalho remoto, sites SharePoint, Tableau, Qlik, Outlook na web e aplicações de linha de negócios (LOB).

![Arquitetura de proxy de aplicação da AD Azure](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>Autenticação

Existem várias formas de configurar uma aplicação para um único sign-on e o método que selecionar depende da autenticação que a sua aplicação utiliza. Application Proxy suporta os seguintes tipos de aplicações:

* Aplicações Web
* ApIs web que você quer expor a aplicações ricas em diferentes dispositivos
* Aplicações hospedadas atrás de um Gateway de Ambiente de Trabalho Remoto
* Aplicações de clientes ricas que estão integradas com a Biblioteca de Autenticação de DirectórioAtivo Ativo (ADAL)

App Proxy trabalha com apps que utilizam o seguinte protocolo de autenticação nativa:

* **[Autenticação Integrada do Windows (IWA)](application-proxy-configure-single-sign-on-with-kcd.md).** Para a IWA, os conectores proxy de aplicação utilizam a Delegação Limitada kerberos (KCD) para autenticar os utilizadores na aplicação Kerberos.

App Proxy também suporta os seguintes protocolos de autenticação com integração de terceiros ou em cenários de configuração específicos:

* [**Autenticação baseada em cabeçalho.**](application-proxy-configure-single-sign-on-with-ping-access.md) Este método de início de sinalização utiliza um serviço de autenticação de terceiros chamado PingAccess e é utilizado quando a aplicação utiliza cabeçalhos para autenticação. Neste cenário, a autenticação é tratada pelo PingAccess.
* [**Autenticação baseada em formulários ou palavra-passe.**](application-proxy-configure-single-sign-on-password-vaulting.md) Com este método de autenticação, os utilizadores acedem à aplicação com um nome de utilizador e senha na primeira vez que acedem à mesmo. Após o primeiro início de inscrição, a Azure AD fornece o nome de utilizador e a palavra-passe para a aplicação. Neste cenário, a autenticação é tratada pela Azure AD.
* [**Autenticação SAML**](application-proxy-configure-single-sign-on-on-premises-apps.md). O sign-on único baseado em SAML é suportado para aplicações que utilizam protocolos SAML 2.0 ou WS-Federation. Com o único sinal da SAML, a Azure AD autentica a aplicação utilizando a conta Azure AD do utilizador.

Para obter mais informações sobre métodos suportados, consulte [Escolher um único método de inscrição](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

### <a name="security-benefits"></a>Benefícios de segurança

A solução de acesso remoto oferecida pela Application Proxy e pela Azure AD suporta maçada por vários benefícios de segurança que os clientes podem tirar partido, incluindo:

* **Acesso autenticado.** Aplicação Proxy é mais adequado para publicar aplicações com [pré-autenticação](application-proxy-security.md#authenticated-access) para garantir que apenas ligações autenticadas atingem a sua rede. Para aplicações publicadas com pré-autenticação, não é permitido o trânsito passar pelo serviço App Proxy para o seu ambiente no local, sem um token válido. A pré-autenticação, pela sua própria natureza, bloqueia um número significativo de ataques direcionados, uma vez que apenas identidades autenticadas podem aceder à aplicação backend.
* **Acesso Condicional**. Os controlos políticos mais ricos podem ser aplicados antes de serem estabelecidas ligações à sua rede. Com acesso condicional, pode definir restrições no tráfego que permite atingir a sua aplicação de backend. Cria políticas que restringem os sign-ins com base na localização, na força da autenticação e no perfil de risco do utilizador. À medida que o Acesso Condicional evolui, mais controlos estão a ser adicionados para fornecer segurança adicional, como a integração com o Microsoft Cloud App Security (MCAS). A integração do MCAS permite-lhe configurar uma aplicação no local para monitorização em [tempo real,](application-proxy-integrate-with-microsoft-cloud-application-security.md) aproveitando o Acesso Condicional para monitorizar e controlar sessões em tempo real com base nas políticas de Acesso Condicional.
* **Interrupção de trânsito.** Todo o tráfego para a aplicação backend é encerrado no serviço Proxy de Aplicação na nuvem enquanto a sessão é restabelecida com o servidor backend. Esta estratégia de ligação significa que os seus servidores de backend não estão expostos ao tráfego HTTP direto. Estão melhor protegidos contra ataques do DoS (negação de serviço) porque a sua firewall não está sob ataque.
* **Todos os acessos estão de saída.** Os conectores Proxy de Aplicação apenas utilizam ligações de saída ao serviço proxy de aplicação na nuvem sobre as portas 80 e 443. Sem ligações de entrada, não há necessidade de abrir portas de firewall para ligações ou componentes de entrada no DMZ. Todas as ligações estão de saída e sobre um canal seguro.
* **Inteligência baseada em Análise de Segurança e Machine Learning (ML).** Como faz parte do Diretório Ativo azure, a Application Proxy pode alavancar a Proteção de [Identidade Azure AD](https://docs.microsoft.com/azure/active-directory/identity-protection/overview) (requer [licenciamento Premium P2).](https://azure.microsoft.com/pricing/details/active-directory/) A Azure AD Identity Protection combina inteligência de segurança de aprendizagem automática com feeds de dados da Unidade de [Crimes Digitais](https://news.microsoft.com/stories/cybercrime/index.html) da Microsoft e do Microsoft Security [Response Center](https://www.microsoft.com/msrc) para identificar proativamente contas comprometidas. A Proteção de Identidade oferece proteção em tempo real contra inscrições de alto risco. Leva em consideração fatores como acessos de dispositivos infetados, através de redes anonizantes, ou de locais atípicos e improváveis para aumentar o perfil de risco de uma sessão. Este perfil de risco é utilizado para proteção em tempo real. Muitos destes relatórios e eventos já estão disponíveis através de uma API para integração com os seus sistemas SIEM.

* **Acesso remoto como serviço.** Não tem de se preocupar em manter e remendar servidores no local para permitir o acesso remoto. Application Proxy é um serviço à escala de internet que a Microsoft possui, pelo que obtém sempre os mais recentes patches de segurança e upgrades. O software não remendo ainda é responsável por um grande número de ataques. De acordo com o Departamento de Segurança Interna, [85% dos ataques direcionados são evitáveis.](https://www.us-cert.gov/ncas/alerts/TA15-119A) Com este modelo de serviço, já não é necessário carregar o pesado fardo de gerir os seus servidores de borda e esforçar-se para os corrigir conforme necessário.

* **Integração inoportuna.** Com intune, o tráfego corporativo é desviado separadamente do tráfego pessoal. Aplicação Proxy garante que o tráfego corporativo é autenticado. [O Proxy de aplicações e a](https://docs.microsoft.com/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers) capacidade do Navegador Gerido Intune também podem ser utilizados em conjunto para permitir aos utilizadores remotos aceder em segurança a websites internos a partir de dispositivos iOS e Android.

### <a name="roadmap-to-the-cloud"></a>Roteiro para a nuvem

Outro grande benefício da implementação da Application Proxy é estender a AD Azure ao seu ambiente no local. Na verdade, implementar o App Proxy é um passo fundamental para mover a sua organização e aplicações para a nuvem. Ao mover-se para a nuvem e longe da autenticação no local, reduz a sua pegada no local e utiliza as capacidades de gestão de identidade da Azure AD como seu plano de controlo. Com atualizações mínimas ou sem atualizações para aplicações existentes, tem acesso a capacidades de nuvem como inscrição única, autenticação multifactor e gestão central. Instalar os componentes necessários para o App Proxy é um processo simples para estabelecer uma estrutura de acesso remoto. E ao mover-se para a nuvem, tem acesso às mais recentes funcionalidades, atualizações e funcionalidades do Azure AD, como a elevada disponibilidade e a recuperação de desastres.

Para saber mais sobre a migração das suas apps para o Azure AD, consulte o livro branco do [Diretório Ativo Azure.](https://aka.ms/migrateapps/whitepaper)

## <a name="architecture"></a>Arquitetura

O diagrama seguinte ilustra em geral como os serviços de autenticação da AD Azure e a Procuração de Aplicação trabalham em conjunto para fornecer um único sinal às aplicações no local aos utilizadores finais.

![Fluxo de autenticação proxy de aplicação da AD Azure](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. Depois de o utilizador ter acedido à aplicação através de um ponto final, o utilizador é redirecionado para a página de entrada de ada d.I. Azure. Se configurar as políticas de Acesso Condicional, as condições específicas são verificadas neste momento para garantir que cumpre os requisitos de segurança da sua organização.
2. Após um início de sessão bem-sucedido, a Azure AD envia um sinal para o dispositivo cliente do utilizador.
3. O cliente envia o símbolo para o serviço Proxy de Aplicação, que recupera o nome principal do utilizador (UPN) e o nome principal de segurança (SPN) do símbolo.
4. Aplicação Proxy reencaminha o pedido, que é recolhido pelo [conector](application-proxy-connectors.md)Proxy de aplicação .
5. O conector executa qualquer autenticação adicional exigida em nome do utilizador ( Opcional dependendo do método de*autenticação),* solicita o ponto final interno do servidor de aplicação e envia o pedido para a aplicação no local.
6. A resposta do servidor de aplicações é enviada através do conector para o serviço Proxy de Aplicação.
7. A resposta é enviada do serviço Proxy de Aplicação para o utilizador.

|**Componente**|**Descrição**|
|:-|:-|
|Ponto Final|O ponto final é um URL ou um [portal de utilizador final](end-user-experiences.md). Os utilizadores podem chegar a aplicações fora da sua rede acedendo a um URL externo. Os utilizadores dentro da sua rede podem aceder à aplicação através de um URL ou de um portal de utilizador final. Quando os utilizadores vão a um destes pontos finais, autenticam-se em Azure AD e depois são encaminhados através do conector para a aplicação no local.|
|Azure AD|A Azure AD realiza a autenticação utilizando o diretório de inquilinos armazenado na nuvem.|
|Serviço proxy de aplicação|Este serviço de Procuração de Aplicações funciona na nuvem como parte da Azure AD. Transmite o sinal de sinal do utilizador para o Conector proxy da aplicação. Aplicação Proxy reencaminha quaisquer cabeçalhos acessíveis no pedido e define os cabeçalhos de acordo com o seu protocolo, para o endereço IP do cliente. Se o pedido de entrada ao representante já tiver esse cabeçalho, o endereço IP do cliente é adicionado ao fim da lista separada da vírposta que é o valor do cabeçalho.|
|Conector proxy de aplicação|O conector é um agente leve que funciona num Servidor windows dentro da sua rede. O conector gere a comunicação entre o serviço de procuração de aplicações na nuvem e a aplicação no local. O conector utiliza apenas ligações de saída, para que não tenha de abrir nenhuma porta de entrada ou colocar qualquer coisa no DMZ. Os conectores são apátridas e retiram informações da nuvem conforme necessário. Para obter mais informações sobre conectores, como a forma como equilibram e autenticam, consulte [conectores De Procuração de Aplicação AD Do Indisso.](application-proxy-connectors.md)|
|Diretório Ativo (AD)|O Diretório Ativo funciona no local para realizar a autenticação para contas de domínio. Quando o único sinal é configurado, o conector comunica com a AD para realizar qualquer autenticação adicional necessária.|
|Aplicação no local|Finalmente, o utilizador pode aceder a uma aplicação no local.|

A Procuração de Aplicações Azure AD é composta pelo serviço proxy baseado na nuvem e um conector no local. O conector ouve pedidos do serviço Proxy de Aplicação e trata das ligações às aplicações internas. É importante notar que todas as comunicações ocorrem sobre TLS, e sempre originam-se no conector do serviço Application Proxy. Ou seja, as comunicações estão apenas de saída. O conector utiliza um certificado de cliente para autenticar o serviço Proxy de Aplicação para todas as chamadas. A única exceção à garantia de ligação é a etapa inicial de configuração em que o certificado de cliente está estabelecido. Consulte o Proxy de Aplicação [Sob o capot](application-proxy-security.md#under-the-hood) para obter mais detalhes.

### <a name="application-proxy-connectors"></a>Conectores proxy de aplicação

[Os conectores Proxy](application-proxy-connectors.md) de aplicação são agentes leves implantados no local que facilitam a ligação de saída ao serviço proxy de aplicação na nuvem. Os conectores devem ser instalados num Servidor windows que tenha acesso à aplicação backend. Os utilizadores ligam-se ao serviço de nuvem App Proxy que encaminha o seu tráfego para as aplicações através dos conectores, conforme ilustrado abaixo.

![Ligações de rede de proxy de aplicação da AD Azure](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

A configuração e o registo entre um conector e o serviço Proxy da aplicação são realizados da seguinte forma:

1. O administrador de TI abre as portas 80 e 443 para o tráfego de saída e permite o acesso a vários URLs que são necessários pelo conector, pelo serviço App Proxy e pela Azure AD.
2. O administrador entra no portal Azure e executa um executável para instalar o conector num servidor Windows no local.
3. O conector começa a "ouvir" o serviço De procuração de aplicações.
4. O administrador adiciona a aplicação no local ao Azure AD e configura as definições como os URLs que os utilizadores precisam de se conectar às suas apps.

Para mais informações, consulte [Plan a Azure Application Proxy implementação](application-proxy-deployment-plan.md).

Recomenda-se que utilize sempre vários conectores para redundância e escala. Os conectores, em conjunto com o serviço, cuidam de todas as tarefas de alta disponibilidade e podem ser adicionados ou removidos dinamicamente. Cada vez que um novo pedido chega é encaminhado para um dos conectores que está disponível. Quando um conector está em funcionamento, permanece ativo à medida que se liga ao serviço. Se um conector estiver temporariamente indisponível, não responde a este tráfego. Os conectores não utilizados são marcados como inativos e removidos após 10 dias de inatividade.

Os conectores também pesquisam o servidor para saber se há uma versão mais recente do conector. Embora possa fazer uma atualização manual, os conectores atualizarão automaticamente enquanto o serviço de atualização do Coenetr proxy application estiver em funcionamento. Para os inquilinos com vários conectores, as atualizações automáticas visam um conector de cada vez em cada grupo para evitar o tempo de inatividade no seu ambiente.

**Nota:** Pode monitorizar a página de histórico da [versão](application-proxy-release-version-history.md) Proxy da aplicação a ser notificada quando as atualizações tiverem sido divulgadas através da subscrição do seu feed RSS.

Cada conector Proxy de Aplicação é atribuído a um grupo de [conector](application-proxy-connector-groups.md). Os conectores do mesmo grupo de conectores funcionam como uma única unidade para alta disponibilidade e equilíbrio de carga. Pode criar novos grupos, atribuir conectores no portal Azure e, em seguida, atribuir conectores específicos para servir aplicações específicas. Recomenda-se ter pelo menos dois conectores em cada grupo de conectores para uma elevada disponibilidade.

Os grupos de conector são úteis quando precisa de suportar os seguintes cenários:

* Publicação de aplicativos geográficos
* Segmentação/isolamento de aplicações
* Publicar aplicações web que executam na nuvem ou no local

Para obter mais informações sobre a escolha de onde instalar os seus conectores e otimizar a sua rede, consulte considerações de topologia da rede ao utilizar o Proxy de Aplicação de [Diretório Ativo Azure](application-proxy-network-topology.md).

## <a name="other-use-cases"></a>Outros casos de utilização

Até este momento, focamo-nos em usar o Application Proxy para publicar aplicações no local externamente, permitindo um único início de inscrição em todas as suas aplicações de cloud e no local. No entanto, existem outros casos de utilização para o Proxy da App que merecem ser mencionados. Os relatórios incluem:

* **Publique as APIs REST de forma segura.** Quando você tem lógica de negócio ou APIs correndo no local ou hospedado em máquinas virtuais na nuvem, Application Proxy fornece um ponto final público para acesso a API. O acesso final da API permite controlar a autenticação e a autorização sem necessitar de portas de entrada. Fornece segurança adicional através de funcionalidades Azure AD Premium, tais como autenticação multifactor e acesso condicional baseado em dispositivos para desktops, iOS, MAC e dispositivos Android usando Intune. Para saber mais, consulte [como permitir que as aplicações de clientes nativos interajam com aplicações de procuração](application-proxy-configure-native-client-application.md) e [protejam uma API utilizando o OAuth 2.0 com o Azure Ative Directory e a API Management.](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)
* **Serviços de ambiente** de trabalho remoto **(RDS)**. As implementações padrão de RDS requerem ligações de entrada abertas. No entanto, a [implementação RDS com Proxy](application-proxy-integrate-with-remote-desktop-services.md) de aplicação tem uma ligação de saída permanente do servidor que executa o serviço de conector. Desta forma, pode oferecer mais aplicações aos utilizadores finais através da publicação de aplicações no local através dos Serviços de Ambiente de Trabalho Remoto. Também pode reduzir a superfície de ataque da implantação com um conjunto limitado de controlos de verificação em duas etapas e controlos de acesso condicional ao RDS.
* **Publique aplicações que se ligam utilizando WebSockets**. O suporte com [a Qlik Sense](application-proxy-qlik.md) está em Pré-visualização Pública e será expandido para outras aplicações no futuro.
* **Permitir que as aplicações de clientes nativos interajam com aplicações por procuração.** Pode utilizar o Proxy de Aplicação AD Azure para publicar aplicações web, mas também pode ser usado para publicar aplicações de [clientes nativos](application-proxy-configure-native-client-application.md) que estão configuradas com a Biblioteca de Autenticação AD Azure (ADAL). As aplicações de clientes nativos diferem das aplicações web porque estão instaladas num dispositivo, enquanto as aplicações web são acedidas através de um navegador.

## <a name="conclusion"></a>Conclusão

A forma como trabalhamos e as ferramentas que usamos estão a mudar rapidamente. Com mais colaboradores a trazerem os seus próprios dispositivos para o trabalho e a utilização generalizada de aplicações de Software-as-a-Service (SaaS), a forma como as organizações gerem e asseguram os seus dados também deve evoluir. As empresas já não operam apenas dentro das suas próprias muralhas, protegidas por um fosso que rodeia a sua fronteira. Os dados viajam para mais locais do que nunca, tanto em ambientes no local como em ambientes de nuvem. Esta evolução tem ajudado a aumentar a produtividade e a capacidade de colaboração dos utilizadores, mas também torna a proteção de dados sensíveis mais desafiante.

Quer esteja a utilizar a AD Azure para gerir os utilizadores num cenário de coexistência híbrida ou se está interessado em iniciar a sua viagem até à nuvem, implementar a Aplicação AD Proxy do Azure pode ajudar a reduzir o tamanho da sua pegada no local, fornecendo acesso remoto como serviço.

As organizações devem começar hoje a aproveitar-se da App Proxy para aproveitar os seguintes benefícios:

* Publique aplicações no local externamente sem as despesas gerais associadas à manutenção de vpn tradicionais ou outras soluções de publicação web no local e abordagem DMZ
* Inscrição única em todas as aplicações, sejam elas office 365 ou outras aplicações SaaS e incluindo aplicações no local
* Segurança em escala de nuvem onde Azure AD aproveita telemetria do Office 365 para impedir acesso não autorizado
* Integração insintonizada para garantir a autenticação do tráfego corporativo
* Centralização da gestão da conta de utilizador
* Atualizações automáticas para garantir que tem os mais recentes patches de segurança
* Novas funcionalidades à medida que são lançadas; o mais recente apoio para a SAML única inscrição e gestão mais granular de cookies de aplicação

## <a name="next-steps"></a>Próximos passos

* Para obter informações sobre planeamento, operação e gestão de procuração de aplicação ad ad azure, consulte [Plan a Azure Application Proxy implementação](application-proxy-deployment-plan.md).
* Para agendar uma demonstração ao vivo ou obter um teste gratuito de 90 dias para avaliação, consulte [Começar com A Mobilidade Empresarial + Segurança](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial).
