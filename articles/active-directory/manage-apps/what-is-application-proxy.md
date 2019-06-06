---
title: Publicar aplicações no local com o Proxy de aplicações do Azure AD
description: Compreenda por que usar o Proxy de aplicações para publicar aplicativos da web no local externamente para usuários remotos. Saiba mais sobre a arquitetura do Proxy de aplicações, conectores, métodos de autenticação e benefícios de segurança.
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
ms.openlocfilehash: 66403a18be8337939d457c061b07de948c3e34e8
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730876"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>Com o Proxy de aplicações do Azure AD para publicar aplicações no local para os utilizadores remotos

Azure Active Directory (Azure AD) oferece vários recursos para proteger os utilizadores, aplicações e dados na cloud e no local. Em particular, a funcionalidade de Proxy de aplicações do Azure AD pode ser implementada por profissionais de TI que pretendem publicar externamente aplicações web no local. Os utilizadores remotos que precisam de acesso para aplicações internas, em seguida, podem acessá-los de forma segura.

A capacidade de aceder de forma segura a aplicações internas de fora da rede se torna ainda mais crítica no local de trabalho moderna. Com cenários, como BYOD (Bring Your Own Device) e dispositivos móveis, os profissionais de TI são desafiados a atender a dois objetivos:

* Capacite os utilizadores finais sejam produtivos em qualquer altura e em qualquer lugar
* Proteger ativos corporativos em todos os momentos

Muitas organizações que estão no controle e protegidos quando recursos existem dentro dos limites de suas redes corporativas. Mas, na área de trabalho digital de hoje em dia, esse limite expandiu-se com dispositivos móveis geridos e recursos e serviços na nuvem. Agora precisa de gerir a complexidade de proteger as identidades dos seus utilizadores e os dados armazenados em seus dispositivos e aplicações.

Talvez já estiver a utilizar do Azure AD para gerir os utilizadores na cloud que necessitam de aceder ao Office 365 e outras aplicações SaaS, bem como web aplicações alojadas no local. Se já tiver o Azure AD, pode aproveitar como plano de um controle para permitir o acesso seguro e totalmente integrado às suas aplicações no local. Ou, talvez ainda estiver Considerando uma mudança para a nuvem. Nesse caso, pode começar a sua jornada na cloud ao implementar o Proxy da aplicação e tomar a primeira etapa em direção à criação de uma base de identidade forte.

Embora não abrangente, a lista abaixo ilustra algumas das coisas que pode ativar através da implementação do Proxy de aplicações num cenário de coexistência híbrido:

* Publicar aplicações web no local externamente de uma forma simplificada sem uma rede de Perímetro
* Suporte a logon único (SSO) em dispositivos, recursos e as aplicações na cloud e no local
* Suporte multi-factor authentication para aplicações na cloud e no local
* Rapidamente tirar partido das funcionalidades de cloud com a segurança da Microsoft Cloud
* Centralize a gestão de conta de utilizador
* Centralizar o controle de identidade e segurança
* Adicionar ou remover o acesso de utilizador para aplicações com base na associação de grupo automaticamente

Este artigo explica como o Azure AD e o Proxy de aplicações oferecer aos usuários remotos uma experiência de início de sessão único (SSO). Os utilizadores liguem de forma segura a aplicações no local sem uma VPN ou servidores se encontrem dupla e regras de firewall. Este artigo ajuda-o a compreender como o Proxy de aplicações traz as capacidades e vantagens de segurança da nuvem para seus aplicativos de web no local. Ele também descreve a arquitetura e topologias que são possíveis.

## <a name="remote-access-in-the-past"></a>Acesso remoto no passado

Anteriormente, o seu plano de controlo para proteger os recursos internos dos invasores ao facilitar o acesso por utilizadores remotos era tudo na rede de Perímetro ou na rede de perímetro. Mas as soluções de proxy inverso implementadas na rede de Perímetro utilizado por clientes externos acedam aos recursos empresariais e de VPN não são adequadas para o mundo de cloud. Eles normalmente sofrem com as seguintes desvantagens:

* Custos de hardware
* Manter a segurança (aplicação de patches, monitorização de portas, etc.)
* Autenticar os utilizadores na periferia
* Autenticar os usuários aos servidores web na rede de perímetro
* Manter o acesso VPN para usuários remotos com a distribuição e a configuração do software de cliente VPN. Além disso, manutenção de servidores associados a um domínio na rede de Perímetro, que pode ser vulneráveis a ataques exteriores.

No mundo de cloud-first de hoje, o Azure AD é mais adequado para controlar quem sincroniza e o que entra na sua rede. Proxy de aplicações do Azure AD integra-se a autenticação moderna e tecnologias baseadas na nuvem, como aplicações SaaS e fornecedores de identidade. Esta integração permite aos utilizadores aceder às aplicações a partir de qualquer lugar. Não só é mais adequado do Proxy de aplicações para a área de trabalho digital de hoje, é mais segura do que a VPN e soluções de proxy inverso e mais fácil de implementar. Os utilizadores remotos podem aceder as aplicações no local da mesma forma que aceder ao Office 365 e outras aplicações SaaS integradas com o Azure AD. Não precisa de alterar ou atualizar seus aplicativos para trabalhar com o Proxy de aplicações. Além disso, o Proxy de aplicação não requer a abertura de ligações de entrada através da firewall. Com o Proxy de aplicação, basta defini-lo e esqueça.

## <a name="the-future-of-remote-access"></a>O futuro do acesso remoto

Na área de trabalho digital de hoje em dia, os usuários trabalham em qualquer lugar com vários dispositivos e aplicações. A única constante é a identidade do utilizador. É por isso que o primeiro passo para uma rede segura hoje é usar [gestão de identidades do Azure AD](https://docs.microsoft.com/azure/security/security-identity-management-overview) capacidades do seu plano de controlo de segurança. Um modelo que utiliza a identidade como o seu plano de controlo é normalmente composto pelos seguintes componentes:

* Um fornecedor de identidade para controlar os utilizadores e informações relacionadas ao usuário.
* Diretório de dispositivo para manter uma lista de dispositivos que tenham acesso aos recursos empresariais. Este diretório inclui informações sobre o dispositivo correspondente (por exemplo, digite de dispositivo, integridade etc.).
* Serviço de avaliação da política para determinar se um utilizador e o dispositivo está em conformidade com a política estipulada pelos administradores de segurança.
* A capacidade de conceder ou negar o acesso aos recursos organizacionais.

Com o Proxy de aplicações, do Azure AD mantém controle dos usuários que precisam de aceder ao web aplicações publicadas no local e na cloud. Ele fornece um ponto de gestão central para essas aplicações. Embora não seja necessário, recomenda-se que também de ativar o acesso condicional do Azure AD. Ao definir condições para como os utilizadores autenticarem e obterem acesso, ainda mais Certifique-se que as pessoas certas tenham acesso às aplicações.

**Nota:** É importante compreender que Proxy de aplicações do Azure AD destina-se como uma VPN ou substituição de proxy inverso para os utilizadores de roaming (ou remotos) que necessitam de aceder aos recursos internos. Não se destina para usuários internos na rede empresarial. Os utilizadores internos que utilizam desnecessariamente o Proxy de aplicações podem introduzir problemas de desempenho inesperados e indesejáveis.

![O Azure Active Directory e todas as suas aplicações](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>Uma descrição geral do funcionamento do Proxy de aplicações

Proxy de aplicações é um serviço do Azure AD que configurar no portal do Azure. Permite-lhe publicar um ponto de extremidade HTTP/HTTPS URL externo público na nuvem do Azure, o que se liga a um URL do servidor de aplicação interna na sua organização. Estas aplicações de web no local podem ser integradas com o Azure AD para suportar o início de sessão único. Os utilizadores finais podem então acesso no local aplicações web da mesma forma que aceder ao Office 365 e outras aplicações SaaS.

Componentes desta funcionalidade incluem o serviço de Proxy de aplicações, que é executado na cloud, o conector do Proxy de aplicações, o que é um agente leve que é executado num servidor no local e o Azure AD, o que é o fornecedor de identidade. Todos os três componentes funcionam em conjunto para fornecer o utilizador com uma experiência de início de sessão único para aceder a aplicações no local web.

Depois de iniciar sessão, utilizadores externos podem aceder a aplicações web no local usando uma URL familiar ou o [painel de acesso de MyApps](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access) partir dos seus dispositivos de ambiente de trabalho ou iOS/MAC. Por exemplo, o Proxy de aplicação pode fornecer acesso remoto e único início de sessão remoto para ambiente de trabalho, o SharePoint sites, Tableau, Qlik, Outlook na web e aplicativos do linha de negócio (LOB).

![Arquitetura de Proxy de aplicações do AD do Azure](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>Autenticação

Existem várias formas de configurar uma aplicação para início de sessão único e o método que selecionar depende da autenticação na que sua aplicação utiliza. Proxy de aplicações suporta os seguintes tipos de aplicativos:

* Aplicações Web
* APIs que deseja expor para aplicativos avançados em diferentes dispositivos para a Web
* Aplicações alojadas atrás de um Gateway de ambiente de trabalho remoto
* Aplicações de cliente rico que estão integradas com o Active Directory Authentication Library (ADAL)

Proxy de aplicações funciona com aplicações que utilizam o protocolo de autenticação nativo seguintes:

* **[Autenticação do Windows (IWA) integrada](application-proxy-configure-single-sign-on-with-kcd.md).** Para IWA, os conectores de Proxy da aplicação utilizam a delegação restrita de Kerberos (KCD) para autenticar utilizadores para a aplicação de Kerberos.

Proxy de aplicações também suporta os seguintes protocolos de autenticação com a integração de terceiros ou em cenários de configuração específicas:

* [**Autenticação baseada em cabeçalho**](application-proxy-configure-single-sign-on-with-ping-access.md). Este método de início de sessão utiliza um serviço de autenticação de terceiros chamado PingAccess e é utilizado quando o aplicativo usa cabeçalhos para autenticação. Neste cenário, a autenticação é processada pelo PingAccess.
* [**Autenticação baseada em formulários ou palavra-passe**](application-proxy-configure-single-sign-on-password-vaulting.md). Com este método de autenticação, os utilizadores iniciam sessão aplicação com um nome de utilizador e palavra-passe na primeira vez que acederem ao mesmo. Após o primeiro início de sessão, o Azure AD fornece o nome de utilizador e palavra-passe para a aplicação. Neste cenário, a autenticação é processada pelo Azure AD.
* [**Autenticação SAML**](application-proxy-configure-single-sign-on-on-premises-apps.md). Baseado em SAML início de sessão único é suportada para aplicações que utilizam SAML 2.0 ou protocolos WS-Federation. Com o SAML início de sessão único, o Azure AD autentica para o aplicativo usando a conta de utilizador do Azure AD.

Para obter mais informações sobre métodos suportados, consulte [escolher um método de início de sessão único](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

### <a name="security-benefits"></a>Benefícios de segurança

A solução de acesso remoto oferecida pelo Proxy de aplicações e o Azure AD suporta vários benefícios de segurança, os clientes podem tirar partido, incluindo:

* **Acesso autenticado**. Proxy de aplicações é mais adequado para publicar aplicações com o [pré-autenticação](application-proxy-security.md#authenticated-access) para garantir que apenas as ligações autenticadas atingir a sua rede. Para as aplicações publicadas com pré-autenticação, não existe tráfego é permitido para passar através do serviço de Proxy de aplicações para seu ambiente no local, sem um token válido. Pré-autenticação, por sua própria natureza, bloqueia um número significativo de ataques direcionados, como apenas identidades autenticadas podem aceder à aplicação de back-end.
* **Acesso condicional**. Controlos de política mais avançados podem ser aplicados antes de que são estabelecidas ligações a sua rede. Com o acesso condicional, pode definir restrições sobre o tráfego que permitem a pressionar a sua aplicação de back-end. Pode criar políticas que restringem o inícios de sessão com base na localização, força da autenticação e perfil de risco do utilizador. À medida que o acesso condicional evolui, mais controles estão a ser adicionados para fornecer segurança adicional, como a integração com o Microsoft Cloud App Security (MCAS). Integração de MCAS permite-lhe configurar uma aplicação no local para [monitorização em tempo real](application-proxy-integrate-with-microsoft-cloud-application-security.md) ao tirar partido do acesso condicional para monitorizar e controlar sessões em tempo real com base nas políticas de acesso condicional.
* **Terminação de tráfego**. Todo o tráfego para a aplicação de back-end é terminado no serviço de Proxy de aplicações na cloud, enquanto a sessão novamente é estabelecida com o servidor de back-end. Esta estratégia de ligação, significa que seus servidores não são expostos para direcionar o tráfego HTTP de back-end. Eles estão melhor protegidos contra ataques direcionados DoS (negação de serviço), uma vez que a firewall não está sob ataque.
* **Todo o acesso é de saída**. Os conectores de Proxy de aplicações só utilizam ligações de saída para o serviço de Proxy de aplicações na cloud através das portas 80 e 443. Sem ligações de entrada, não há necessidade de abrir portas de firewall para ligações de entrada ou de componentes na rede de Perímetro. Todas as ligações são saída e ao longo de um canal seguro.
* **Análise de segurança e de Machine Learning (ML) com base em inteligência**. Porque faz parte do Azure Active Directory, o Proxy de aplicações podem tirar partido [do Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview) (requer [Premium P2 licenciamento](https://azure.microsoft.com/pricing/details/active-directory/)). O Azure AD Identity Protection combina a inteligência de segurança de aprendizagem automática com feeds de dados a partir da Microsoft [Digital Crimes Unit](https://news.microsoft.com/stories/cybercrime/index.html) e [Microsoft Security Response Center](https://www.microsoft.com/msrc) para identificar proativamente contas comprometidas. A proteção de identidade oferece proteção em tempo real a partir de inícios de sessão de alto risco. Demora em fatores de consideração, como de acessos de dispositivos infetados, através de redes anonymizing, ou a partir de localizações atípicas e pouco prováveis para aumentar o perfil de risco de uma sessão. Este perfil de risco é utilizado para a proteção em tempo real. Muitos desses relatórios e eventos já estão disponíveis através de uma API para a integração com os seus sistemas SIEM.

* **Acesso remoto como um serviço**. Não precisa se preocupar sobre a manutenção e aplicação de patches de servidores no local para permitir o acesso remoto. Proxy de aplicações é um serviço de dimensionamento de internet que é proprietário a Microsoft, para que tenha sempre as mais recentes patches de segurança e atualizações. Sem patch software ainda totaliza um grande número de ataques. De acordo com o departamento de segurança interna, até [85 por cento de ataques direcionados estão prevenir o agravamento](https://www.us-cert.gov/ncas/alerts/TA15-119A). Com esse modelo de serviço, não precisa de executar a carga pesada de gerenciar seus servidores de borda deixa de poder e misturar aplicar um patch-las conforme necessário.

* **Integração do Intune**. Com o Intune, o tráfego empresarial é encaminhado em separado do tráfego pessoa. Proxy de aplicações garante que o tráfego empresarial é autenticado. [Proxy de aplicações e o Intune Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers) recurso também pode ser usado em conjunto para permitir que os utilizadores remotos aceder de forma segura sites internos de dispositivos iOS e Android.

### <a name="roadmap-to-the-cloud"></a>Plano para a cloud

Outra vantagem importante da implementação de Proxy de aplicações é a extensão do Azure AD para o seu ambiente no local. Na verdade, a implementação do Proxy de aplicações é uma etapa importante da mudança de sua organização e aplicações para a nuvem. Ao mover para a cloud e para fora de autenticação no local, reduzir sua pegada no local e utilizar capacidades de gestão de identidades do Azure AD como o seu plano de controlo. Com o mínimo ou não existem atualizações para os aplicativos existentes, tem acesso a recursos como única início de sessão, autenticação multifator e gestão central de nuvem. Instalar os componentes necessários ao Proxy de aplicação é um processo simples para o estabelecimento de uma estrutura de acesso remoto. E, ao mover para a nuvem, terá acesso para as mais recentes funcionalidades do Azure AD, atualizações e funcionalidades, como elevada disponibilidade e a recuperação após desastre.

Para saber mais sobre como migrar as suas aplicações para o Azure AD, veja a [migrar seus aplicativos para o Azure Active Directory](https://aka.ms/migrateapps/whitepaper) white paper.

## <a name="architecture"></a>Arquitetura

O diagrama seguinte ilustra, em geral, serviços de autenticação como o Azure AD e funcionam de Proxy de aplicações em conjunto para fornecer início de sessão único para aplicações no local para os utilizadores finais.

![Fluxo de autenticação de Proxy de aplicações do AD do Azure](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. Depois do usuário acessa o aplicativo por meio de um ponto de extremidade, o utilizador é redirecionado para a página de início de sessão do Azure AD. Se tiver configurado políticas de acesso condicional, as condições específicas são verificadas neste momento para se certificar de que está em conformidade com requisitos de segurança da sua organização.
2. Depois de um sessão com êxito-, do Azure AD envia um token para o dispositivo do utilizador cliente.
3. O cliente envia o token para o serviço de Proxy de aplicações, que obtém o nome principal de utilizador (UPN) e o nome principal de segurança (SPN) do token.
4. Proxy de aplicações por sua vez encaminha a solicitação, o que é recolhida pelo Proxy de aplicações [conector](application-proxy-connectors.md).
5. O conector efetua qualquer autenticação adicional necessária em nome do utilizador (*opcional, dependendo do método de autenticação*), o ponto final interno do servidor de aplicativos de pedidos e envia o pedido para no local aplicação.
6. A resposta do servidor de aplicativos é enviada através do conector para o serviço de Proxy de aplicações.
7. A resposta é enviada a partir do serviço de Proxy de aplicações para o usuário.

|**Componente**|**Descrição**|
|:-|:-|
|Ponto Final|O ponto final é um URL ou um [portal de utilizador final](end-user-experiences.md). Os utilizadores possam contactar aplicativos enquanto fora da sua rede ao aceder a um URL externo. Os utilizadores na sua rede possam aceder à aplicação através de um URL ou um portal de utilizador final. Quando os utilizadores aceder a um desses pontos de extremidade, eles se autenticar no Azure AD e, em seguida, são encaminhados através do conector para o aplicativo no local.|
|Azure AD|O Azure AD realiza a autenticação que utiliza o diretório de inquilinos armazenado na nuvem.|
|Serviço de Proxy de aplicações|Este serviço de Proxy de aplicações é executado na cloud como parte do Azure AD. Ele passa o token de início de sessão do utilizador para o conector do Proxy de aplicações. Proxy de aplicação encaminha quaisquer cabeçalhos acessíveis na solicitação e define os cabeçalhos de acordo com o protocolo, como o endereço IP do cliente. Se a solicitação de entrada para o proxy já tiver esse cabeçalho, o endereço IP do cliente é adicionado ao final da lista separada por vírgulas que é o valor do cabeçalho.|
|Conector do Proxy de aplicações|O conector é um agente leve que é executado no Windows Server no interior da rede. O conector gerencia a comunicação entre o serviço de Proxy de aplicações na cloud e a aplicação no local. O conector utiliza apenas as ligações de saída, para que não tenha de abrir portas de entrada nem colocar nada num DMZ. Os conectores são sem monitoração de estado e extraem informações a partir da cloud conforme necessário. Para obter mais informações sobre conectores, como como eles balanceamento de carga e autenticar, consulte [conectores de Proxy de aplicações do AD Azure compreender](application-proxy-connectors.md).|
|Do Active Directory (AD)|Do Active Directory é executado no local para efetuar a autenticação para contas de domínio. Quando o início de sessão único é configurado, o conector comunica com o AD para efetuar qualquer autenticação adicional necessária.|
|Aplicação no local|Por fim, o utilizador é capaz de aceder a uma aplicação no local.|

Proxy de aplicações do Azure AD é constituído por serviço de Proxy de aplicações baseado na nuvem e um conector no local. O conector está à escuta de pedidos de serviço de Proxy de aplicações e lida com ligações para os aplicativos internos. É importante observar que todas as comunicações ocorrem através de SSL e, sempre se origina no conector para o serviço de Proxy de aplicações. Ou seja, as comunicações são saídas apenas. O conector utiliza um certificado de cliente para autenticação no serviço de Proxy de aplicações para todas as chamadas. A única exceção a segurança de ligação é a etapa de configuração inicial em que o certificado de cliente é estabelecido. Consulte o Proxy de aplicações [bastidores](application-proxy-security.md#under-the-hood) para obter mais detalhes.

### <a name="application-proxy-connectors"></a>Conectores de Proxy de aplicação

[Conectores de Proxy de aplicação](application-proxy-connectors.md) são simples agentes implementados no local que facilitam a ligação de saída para o serviço de Proxy de aplicações na cloud. Os conectores tem de ser instalados num servidor do Windows que tenha acesso à aplicação de back-end. Os utilizadores estabelecem ligação ao serviço cloud do Proxy de aplicações que as rotas o tráfego para as aplicações através de conectores, como ilustrado abaixo.

![Ligações de rede de Proxy de aplicações do AD do Azure](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

Configuração e registo entre um conector e o serviço de Proxy de aplicação é feito da seguinte maneira:

1. O administrador de TI abre as portas 80 e 443 para tráfego de saída e permite o acesso a várias URLs que são necessários para o conector, o serviço de Proxy de aplicação e o Azure AD.
2. O administrador inicia sessão no portal do Azure e executa um executável para instalar o conector num servidor do Windows no local.
3. O conector é iniciado de "ouvir" para o serviço de Proxy de aplicações.
4. O administrador adiciona o aplicativo no local para o Azure AD e configura as definições, tais como os utilizadores de URLs precisam se conectar a suas aplicações.

Para obter mais informações, consulte [planejar uma implantação de Proxy de aplicações do Azure AD](application-proxy-deployment-plan.md).

Recomenda-se que implemente sempre vários conectores para redundância e dimensionamento. Os conectores, em conjunto com o serviço, cuidar de todas as tarefas de elevada disponibilidade e podem ser adicionados ou removidos dinamicamente. Sempre que uma nova solicitação chegar é encaminhado para um dos conectores que está disponível. Quando um conector está em execução, permanece ativa como se liga ao serviço. Se um conector está temporariamente indisponível, ele não responde a este tráfego. Conectores não utilizadas são marcados como inativos e removidos depois de 10 dias de inatividade.

Conectores também sonde o servidor para descobrir se existe uma versão mais recente do conector. Embora possa fazer uma atualização manual, os conectores serão atualizadas automaticamente, desde que o serviço Atualizador do conector de Proxy de aplicação está em execução. Inquilinos com múltiplos conectores, as atualizações automáticas como destino um conector numa altura em cada grupo para impedir que o tempo de inatividade no seu ambiente.

**Nota**: Pode monitorizar o Proxy de aplicações [página de histórico de versão](application-proxy-release-version-history.md) para ser notificado quando foram lançadas atualizações ao subscrever o feed RSS.

Cada conector de Proxy da aplicação é atribuída a um [grupo de conectores](application-proxy-connector-groups.md). Os conectores no mesmo grupo de conector atuam como uma unidade única para elevada disponibilidade e balanceamento de carga. Pode criar novos grupos, atribuir-lhes conectores no portal do Azure e depois atribuir conectores específicos para servir aplicativos específicos. É recomendado ter, pelo menos, dois conectores em cada grupo do conector para elevada disponibilidade.

Grupos de conexão são úteis quando precisa para suportar os cenários seguintes:

* Publicação da aplicação geográfica
* Segmentação/isolamento de aplicações
* Publicação de aplicações web em execução na cloud ou no local

Para obter mais informações sobre como escolher onde pretende instalar os conectores e otimizar a sua rede, consulte [considerações sobre a topologia de rede ao utilizar o Proxy de aplicações do Azure Active Directory](application-proxy-network-topology.md).

## <a name="other-use-cases"></a>Outros casos de utilização

Até aqui, focámo-com o Proxy de aplicações para publicar aplicações no local externamente ao ativar início de sessão único para todas as suas aplicações na cloud e no local. No entanto, existem outros casos de utilização para o Proxy de aplicação que valem a pena mencionar. Eles incluem:

* **Publicar em segurança API de REST**. Quando tem a lógica de negócios ou APIs em execução no local ou alojado em máquinas virtuais na cloud, o Proxy de aplicações fornece um ponto final público para o acesso à API. Acesso de ponto final de API permite-lhe controlo autenticação e autorização sem a necessidade de portas de entrada. Ele fornece segurança adicional por meio de recursos do Azure AD Premium, tais como autenticação multifator e acesso condicional com base no dispositivo para desktops, iOS, MAC e dispositivos Android com o Intune. Para obter mais informações, consulte [como permitir que aplicativos de cliente nativo interagir com aplicativos de proxy](application-proxy-configure-native-client-application.md) e [proteger uma API com OAuth 2.0 com o Azure Active Directory e gestão de API](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad).
* **Os serviços de ambiente de trabalho remoto** **(RDS)** . As implementações de RDS padrão exigem ligações de entrada abertas. No entanto, o [implementação de RDS com o Proxy de aplicações](application-proxy-integrate-with-remote-desktop-services.md) tem uma ligação de saída permanente de servidor que executa o serviço de conectores. Dessa forma, pode oferecer mais aplicações aos utilizadores finais por através dos serviços de ambiente de trabalho remoto a publicação de aplicações no local. Também pode reduzir a superfície de ataque da implementação com um conjunto limitado de verificação de dois passos e controlos de acesso condicional para RDS.
* **Publicar aplicações que se ligam através de WebSockets**. O suporte acerca [Qlik Sense](application-proxy-qlik.md) está em pré-visualização pública e será expandida para outras aplicações no futuro.
* **Permitir que os aplicativos de cliente nativo interagir com aplicativos de proxy**. Pode utilizar o Proxy de aplicações do Azure AD para publicar aplicações web, mas ele também pode ser utilizado para publicar [aplicações clientes nativas](application-proxy-configure-native-client-application.md) que estão configurados com o Azure AD Authentication Library (ADAL). Aplicações clientes nativas diferem das aplicações web, porque estão instaladas num dispositivo, enquanto as aplicações web são acedidas através de um browser.

## <a name="conclusion"></a>Conclusão

A forma como Trabalhamos e as ferramentas que usamos estão mudando rapidamente. Com mais funcionários levem os seus dispositivos para trabalhar e o uso abrangente de aplicativos de Software-como-serviço (SaaS), as organizações de forma gerem e proteger os dados também devem evoluir. As empresas deixará de funcionar unicamente nos seus próprios paredes, protegidos por um fosso que envolve o respetivo limite. Dados circulam na mais localizações que nunca – no local e ambientes na cloud. Esta evolução ajudou a aumentar a produtividade dos usuários e a capacidade de colaborar, mas ele também torna a proteção dos dados confidencial mais desafiador.

Se utilizar atualmente o Azure AD para gerir utilizadores num cenário de coexistência híbrida ou esteja interessado em estabelecer a sua jornada na cloud, a implementação de Proxy de aplicações do Azure AD pode ajudar a reduzir o tamanho de seus locais requisitos de espaço, fornecendo remoto como um serviço de acesso.

As organizações devem começar a tirar partido do Proxy de aplicações hoje mesmo para tirar partido das vantagens seguintes:

* Publicar aplicações no local externamente sem a sobrecarga associada à manutenção VPN tradicional ou outra soluções e abordagem de rede de Perímetro de publicação na web no local
* Início de sessão único para todos os aplicativos, sejam elas do Office 365 ou outras aplicações SaaS e aplicações no local, incluindo
* Segurança de escala de cloud em que o Azure AD tira partido da telemetria do Office 365 para impedir o acesso não autorizado
* Integração do Intune para garantir que o tráfego empresarial é autenticada
* Centralização da gestão de contas de utilizador
* As atualizações automáticas para garantir que tem os patches de segurança mais recentes
* Novos recursos à medida que são lançadas; a gestão mais recente, sendo o suporte para SAML início de sessão único e mais granular de cookies de aplicação

## <a name="next-steps"></a>Passos Seguintes

* Para obter informações sobre o planeamento, operando e gerenciando o Proxy de aplicações do Azure AD, consulte [planejar uma implantação de Proxy de aplicações do Azure AD](application-proxy-deployment-plan.md).
* Para agendar uma demonstração em direto ou obtenha uma avaliação gratuita de 90 dias para avaliação, veja [introdução ao Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial).
