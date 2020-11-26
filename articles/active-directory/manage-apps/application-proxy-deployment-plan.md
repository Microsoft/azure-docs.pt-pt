---
title: Planeie uma implementação de proxy de aplicação ativa Azure
description: Um guia de ponta a ponta para o planeamento da implementação do proxy de aplicação dentro da sua organização
services: active-directory
documentationcenter: azure
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: kenwith
ms.openlocfilehash: 9815237617566eda4759ecc31718786b6cb2cac8
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96176057"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Planear uma implementação do Proxy de Aplicações do Azure AD

A azure Ative Directory (Azure AD) Application Proxy é uma solução de acesso remoto segura e rentável para aplicações no local. Fornece um caminho de transição imediato para as organizações "Cloud First" gerirem o acesso a aplicações antigas no local que ainda não são capazes de usar protocolos modernos. Para obter informações introdutórias adicionais, consulte [O que é Proxy de Aplicação](./application-proxy.md).

O Proxy da aplicação é recomendado para dar aos utilizadores remotos acesso a recursos internos. O Application Proxy substitui a necessidade de um PROXY VPN ou procuração inversa para estes casos de utilização de acesso remoto. Não se destina a utilizadores que estejam na rede corporativa. Estes utilizadores que usam o Application Proxy para acesso intranet podem ter problemas de desempenho indesejáveis.

Este artigo inclui os recursos necessários para planear, operar e gerir o Azure AD Application Proxy.

## <a name="plan-your-implementation"></a>Planeie a sua implementação

A secção seguinte fornece uma visão ampla dos principais elementos de planeamento que o configurarão para uma experiência de implementação eficiente.

### <a name="prerequisites"></a>Pré-requisitos

Tem de cumprir os seguintes pré-requisitos antes de iniciar a sua implementação. Pode ver mais informações sobre a configuração do seu ambiente, incluindo estes pré-requisitos, neste [tutorial.](application-proxy-add-on-premises-application.md)

* **Conectores**: Os conectores são agentes leves que pode utilizar:
   * Hardware físico no local
   * Um VM hospedado dentro de qualquer solução de hipervisor
   * Um VM hospedado em Azure para permitir a ligação de saída ao serviço Application Proxy.

* Consulte [Os Conectores Proxy Proxy da App AD Azure](application-proxy-connectors.md) para obter uma visão geral mais detalhada.

     * As máquinas de conector devem [ser ativadas para o TLS 1.2](application-proxy-add-on-premises-application.md) antes de instalar os conectores.

     * Se possível, implemente os conectores na [mesma rede](application-proxy-network-topology.md) e segmento que os servidores de aplicações web de back-end. É melhor implantar conectores depois de completar uma descoberta de aplicações.
     * Recomendamos que cada grupo de conector tenha pelo menos dois conectores para fornecer alta disponibilidade e escala. Ter três conectores é ótimo no caso de precisar de servir uma máquina em qualquer ponto. Reveja a [tabela de capacidade do conector](./application-proxy-connectors.md#capacity-planning) para ajudar a decidir que tipo de máquina instalar conectores ligados. Quanto maior for a máquina, mais tampão e executante será o conector.

* **Definições de acesso** à rede : Conectores Azure AD Application Proxy [ligam-se ao Azure via HTTPS (Porta TCP 443) e HTTP (Porta TCP 80)](application-proxy-add-on-premises-application.md).

   * O tráfego de ligação de terminação TLS não é suportado e impedirá que os conectores estabeleçam um canal seguro com os respetivos pontos finais Azure App Proxy.

   * Evite todas as formas de inspeção em linha nas comunicações TLS de saída entre conectores e Azure. A inspeção interna entre um conector e aplicações de backend é possível, mas pode degradar a experiência do utilizador, e como tal, não é recomendado.

   * O equilíbrio de carga dos próprios conectores também não é suportado, nem sequer necessário.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Considerações importantes antes de configurar o Proxy de aplicação AD Azure

Os seguintes requisitos fundamentais devem ser cumpridos para configurar e implementar o Azure AD Application Proxy.

*  **Azure onboarding**: Antes de implementar o proxy da aplicação, as identidades do utilizador devem ser sincronizadas a partir de um diretório no local ou criadas diretamente dentro dos seus inquilinos AZure AD. A sincronização de identidade permite que a Azure AD pré-autenticar os utilizadores antes de lhes conceder acesso às aplicações publicadas pela App Proxy e ter as informações necessárias do identificador do utilizador para realizar um único sinal de acesso (SSO).

* **Requisitos de acesso condicional**: Não recomendamos a utilização de Proxy de aplicação para acesso intranet, porque isto adiciona latência que irá afetar os utilizadores. Recomendamos a utilização de Aplicações Proxy com políticas de pré-autenticação e acesso condicional para acesso remoto a partir da internet.  Uma abordagem para fornecer acesso condicional para uso intranet é modernizar aplicações para que possam autenticar diretamente com AAD. Consulte os [Recursos para aplicações migratórias para a AAD](./migration-resources.md) para obter mais informações.

* **Limites de serviço :** Para proteger contra o consumo excessivo de recursos por inquilinos individuais, existem limites de estrangulamento estabelecidos por pedido e inquilino. Para ver estes limites consulte os [limites e restrições de serviço Azure AD](../enterprise-users/directory-service-limits-restrictions.md). Estes limites de estrangulamento baseiam-se num benchmark muito acima do volume de utilização típico e fornecem um tampão amplo para a maioria das implementações.

* **Certificado público**: Se estiver a utilizar nomes de domínio personalizados, tem de adquirir um certificado TLS/SSL. Dependendo dos seus requisitos organizacionais, obter um certificado pode demorar algum tempo e recomendamos que comece o processo o mais cedo possível. A Azure Application Proxy suporta certificados standard, [wildcard](application-proxy-wildcard.md)ou SAN. Para mais detalhes consulte [Configure domínios personalizados com Proxy de aplicação AD AD Azure](application-proxy-configure-custom-domain.md).

* **Requisitos de domínio**: O único sinal de inscrição nas suas aplicações publicadas utilizando a Delegação Restrita Kerberos (KCD) requer que o servidor que executa o Conector e o servidor que executa a aplicação sejam de domínio e parte do mesmo domínio ou domínio de confiança.
Para obter informações detalhadas sobre o tópico, consulte [o KCD para obter um único s-on](application-proxy-configure-single-sign-on-with-kcd.md) com o Application Proxy. O serviço de conector funciona no contexto do sistema local e não deve ser configurado para utilizar uma identidade personalizada.

* **Registos DNS para URLs**

   * Antes de utilizar domínios personalizados no Application Proxy, tem de criar um registo CNAME em DNS público, permitindo que os clientes resolvam o URL externo definido sob o endereço Proxy de aplicação pré-definido. Não criar um registo CNAME para uma aplicação que utilize um domínio personalizado impedirá os utilizadores remotos de se ligarem à aplicação. As medidas necessárias para adicionar registos CNAME podem variar de fornecedor de DNS a fornecedor, por isso aprenda a [gerir registos de DNS e recordes utilizando o portal Azure](../../dns/dns-operations-recordsets-portal.md).

   * Da mesma forma, os anfitriões de conector devem ser capazes de resolver o URL interno das aplicações que estão a ser publicadas.

* **Direitos e funções administrativas**

   * **A instalação do conector** requer direitos de administração locais para o servidor Windows em que está a ser instalado. Também requer um mínimo de uma função *de Administrador de Aplicação* para autenticar e registar a instância do conector para o seu inquilino AZure AD.

   * **A publicação e administração de aplicações** requerem a *função de Administrador de Aplicação.* Os Administradores de Aplicações podem gerir todas as aplicações no diretório, incluindo registos, definições de SSO, atribuições de utilizador e grupo e licenciamento, definições de Procuração de Aplicações e consentimento. Não dá a capacidade de gerir o Acesso Condicional. A função *de Administrador de Aplicação cloud* tem todas as capacidades do Administrador de Aplicação, exceto que não permite a gestão das definições de Procuração de Aplicações.

* **Licenciamento**: A Aplicação Proxy está disponível através de uma assinatura Azure AD Premium. Consulte a página de preços do [Diretório Ativo Azure](https://azure.microsoft.com/pricing/details/active-directory/) para obter uma lista completa de opções e funcionalidades de licenciamento.

### <a name="application-discovery"></a>Descoberta de Aplicações

Compilar um inventário de todas as aplicações em âmbito que estão a ser publicadas através do Application Proxy recolhendo as seguintes informações:

| Tipo de Informação| Informação para recolher |
|---|---|
| Tipo de Serviço| Por exemplo: SharePoint, SAP, CRM, Custom Web Application, API |
| Plataforma de aplicação | Por exemplo: Windows IIS, Apache em Linux, Tomcat, NGINX |
| Associação ao domínio| O nome de domínio totalmente qualificado do servidor web (FQDN) |
| Localização da aplicação | Onde o servidor web ou a quinta estão localizados na sua infraestrutura |
| Acesso interno | O URL exato utilizado ao aceder à aplicação internamente. <br> Se uma fazenda, que tipo de equilíbrio de carga está em uso? <br> Se a aplicação extrai conteúdo de outras fontes que não a própria.<br> Determine se a aplicação funciona através de WebSockets. |
| Acesso externo | A solução do fornecedor a que a aplicação já está exposta externamente. <br> O URL que pretende utilizar para acesso externo. Se o SharePoint, certifique-se de que os mapeamentos de acesso alternativos são configurados por [esta orientação.](/SharePoint/administration/configure-alternate-access-mappings) Caso contrário, terá de definir URLs externos. |
| Certificado público | Se utilizar um domínio personalizado, obtenha um certificado com um nome de sujeito correspondente. se existir um certificado, note-se o número de série e a localização a partir do local onde pode ser obtido. |
| Tipo de autenticação| O tipo de autenticação suportado pelo suporte de aplicação, como Basic, Autenticação de Integração do Windows, baseada em formulários, baseada em cabeçalhos e reclamações. <br>Se a aplicação estiver configurada para ser executada numa conta de domínio específica, note o Nome de Domínio Totalmente Qualificado (FQDN) da conta de serviço.<br> Se saml-based, o identificador e urLs de resposta. <br> Se estiver à base de cabeçalho, a solução do fornecedor e requisitos específicos para o manuseamento do tipo de autenticação. |
| Nome do grupo do conector | O nome lógico para o grupo de conectores que serão designados para fornecer o canal e SSO a esta aplicação de backend. |
| Acesso de utilizadores/grupos | Os utilizadores ou grupos de utilizadores que terão acesso externo à aplicação. |
| Requisitos adicionais | Note quaisquer requisitos adicionais de acesso remoto ou segurança que devem ser considerados para a publicação da aplicação. |

Pode descarregar esta [folha de cálculo de inventário de aplicações](https://aka.ms/appdiscovery) para inventariar as suas aplicações.

### <a name="define-organizational-requirements"></a>Definir requisitos organizacionais

Seguem-se áreas para as quais deve definir os requisitos de negócio da sua organização. Cada área contém exemplos de requisitos

 **Acesso**

* Utilizadores remotos com dispositivos ligados ao domínio ou Azure AD aderidos a dispositivos podem aceder de forma segura às aplicações publicadas com um único sign-on sem emenda (SSO).

* Os utilizadores remotos com dispositivos pessoais aprovados podem aceder de forma segura às aplicações publicadas desde que estejam inscritos no MFA e tenham registado a aplicação microsoft Authenticator no seu telemóvel como um método de autenticação.

**Governação**

* Os administradores podem definir e monitorizar o ciclo de vida das atribuições do utilizador às aplicações publicadas através do Application Proxy.

**Segurança**

* Apenas os utilizadores atribuídos a aplicações através de membros do grupo ou individualmente podem aceder a essas aplicações.

**Desempenho**

* Não existe uma degradação do desempenho da aplicação em comparação com o acesso à aplicação da rede interna.

**Experiência do Utilizador**

* Os utilizadores estão cientes de como aceder às suas aplicações utilizando URLs familiares da empresa em qualquer plataforma do dispositivo.

**Auditoria**
* Os administradores são capazes de auditar a atividade de acesso ao utilizador.


### <a name="best-practices-for-a-pilot"></a>Melhores práticas para um piloto

Determine o tempo e esforço necessários para encomendar plenamente um único pedido de acesso remoto com um único sign-on (SSO). Faça-o executando um piloto que considere a sua descoberta inicial, publicação e testes gerais. A utilização de uma simples aplicação web baseada no IIS que já está pré-configurada para autenticação integrada do Windows (IWA) ajudaria a estabelecer uma linha de base, uma vez que esta configuração requer o mínimo esforço para pilotar com sucesso o acesso remoto e sSO com sucesso.

Os seguintes elementos de design devem aumentar o sucesso da sua implementação piloto diretamente num inquilino de produção.

**Gestão do conector:**

* Os conectores desempenham um papel fundamental no fornecimento do canal no local para as suas aplicações. A utilização do grupo de conector **Predefinidos** é adequada para os testes piloto iniciais das aplicações publicadas antes de as encomendarem para a produção. As aplicações testadas com sucesso podem ser transferidas para grupos de conector de produção.

**Gestão de aplicações:**

* A sua força de trabalho é mais provável que se lembre de que uma URL externa é familiar e relevante. Evite publicar a sua aplicação utilizando os nossos sufixos de msappproxy.net ou onmicrosoft.com pré-definidos. Em vez disso, forneça um domínio verificado de alto nível familiar, prefixado com um nome de hospedeiro lógico, como *intranet.<customers_domain>.com*.

* Restringir a visibilidade do ícone da aplicação piloto a um grupo piloto escondendo o seu ícone de lançamento forma o portal Azure MyApps. Quando estiver pronto para a produção, pode estender a app ao seu respetivo público-alvo, seja no mesmo inquilino de pré-produção, quer publicando também a aplicação no seu arrendatário de produção.

**Definições únicas de sinalização**: Algumas definições de SSO têm dependências específicas que podem levar tempo a configurar, de modo a evitar atrasos de controlo de alterações, garantindo que as dependências são abordadas com antecedência. Isto inclui anfitriões de conector de ligação de domínio para executar SSO usando Kerberos Constrained Delegação (KCD) e cuidar de outras atividades demoradas. Por exemplo, configurar uma instância de acesso ping, se precisar de SSO baseado em cabeçalho.

**TLS Entre o anfitrião do conector e a aplicação alvo**: A segurança é fundamental, pelo que o TLS entre o anfitrião do conector e as aplicações-alvo devem ser sempre utilizados. Especialmente se a aplicação web for configurada para a autenticação baseada em formulários (FBA), uma vez que as credenciais de utilizador são efetivamente transmitidas em texto claro.

**Implementar incrementalmente e testar cada passo**.
Realizar testes funcionais básicos após a publicação de uma aplicação para garantir que todos os requisitos do utilizador e da empresa sejam cumpridos seguindo as instruções abaixo:

1. Teste e valide o acesso geral à aplicação web com pré-autenticação desativada.
2. Se for bem sucedido, ative a pré-autenticação e atribua utilizadores e grupos. Teste e valida o acesso.
3. Em seguida, adicione o método SSO para a sua aplicação e teste novamente para validar o acesso.
4. Aplicar as políticas de Acesso Condicional e MFA conforme necessário. Teste e valida o acesso.

**Ferramentas de resolução de problemas**: Ao resolver problemas, comece sempre por validar o acesso à aplicação publicada do navegador no anfitrião do conector e confirme que a aplicação funciona como esperado. Quanto mais simples for a sua configuração, mais fácil de determinar a causa raiz, por isso considere tentar reproduzir problemas com uma configuração mínima, como usar apenas um conector e nenhum SSO. Em alguns casos, ferramentas de depuração web como o Fiddler de Telerik podem revelar-se indispensáveis para resolver problemas de acesso ou problemas de conteúdo em aplicações acedidas através de um proxy. O Fiddler também pode atuar como um proxy para ajudar a rastrear e depurar tráfego para plataformas móveis como iOS e Android, e praticamente qualquer coisa que possa ser configurada para a rota através de um proxy. Consulte o [guia de resolução de problemas](application-proxy-troubleshoot.md) para obter mais informações.

## <a name="implement-your-solution"></a>Implementar a sua solução

### <a name="deploy-application-proxy"></a>Implementar procuração de aplicativos

As etapas para implementar o seu Application Proxy estão abrangidas por este [tutorial para adicionar uma aplicação no local para acesso remoto.](application-proxy-add-on-premises-application.md) Se a instalação não for bem sucedida, selecione  **Proxy aplicação de resolução de problemas**  no portal ou utilize o guia de resolução de [problemas para problemas com a instalação do Conector de Agente de Procuração de Aplicações](application-proxy-connector-installation-problem.md).

### <a name="publish-applications-via-application-proxy"></a>Publicar candidaturas via Application Proxy

As aplicações de publicação assumem que satisfez todos os requisitos prévios e que tem vários conectores que aparecem como registados e ativos na página Proxy da Aplicação.

Também pode publicar aplicações utilizando [o PowerShell](/powershell/module/azuread/?view=azureadps-2.0-preview).

Abaixo estão algumas das melhores práticas a seguir ao publicar uma aplicação:

* **Utilizar grupos de conector**: Atribua um grupo de conector designado para a publicação de cada aplicação respetiva. Recomendamos que cada grupo de conector tenha pelo menos dois conectores para fornecer alta disponibilidade e escala. Ter três conectores é ótimo no caso de precisar de servir uma máquina em qualquer ponto. Além disso, consulte [publicar aplicações em redes e locais separados utilizando grupos de conector](application-proxy-connector-groups.md) para ver como também pode utilizar grupos de conector para segmentar os seus conectores por rede ou localização.

* **Definir tempo de aplicação backend**: Esta definição é útil em cenários onde a aplicação pode exigir mais de 75 segundos para processar uma transação de cliente. Por exemplo, quando um cliente envia uma consulta para uma aplicação web que funciona como uma extremidade frontal para uma base de dados. A frontal parte envia esta consulta para o seu servidor de base de dados back-end e aguarda uma resposta, mas quando recebe uma resposta, o lado cliente do tempo de conversação sai. Definir o tempo limite para Long fornece 180 segundos para que as transações mais longas completem.

* **Utilize tipos de cookies apropriados**

   * **COOKIE HTTP-Only**: Providencia segurança adicional ao ter o Application Proxy incluir a bandeira HTTPOnly em cabeçalhos de resposta HTTP de cookie de set-cookie. Esta definição ajuda a mitigar explorações como scripts de cross-site (XSS). Deixe este conjunto para O para clientes/agentes de utilizador que requerem acesso ao cookie da sessão. Por exemplo, o cliente RDP/MTSC conecta-se a um Gateway de Desktop Remoto publicado via App Proxy.

   * **Cookie Seguro**: Quando um cookie é definido com o atributo Secure, o agente do utilizador (app do lado do cliente) só incluirá o cookie em pedidos HTTP se o pedido for transmitido através de um canal protegido TLS. Isto ajuda a mitigar o risco de um cookie ser comprometido através de canais de texto claros, pelo que deve ser ativado.

   * **Cookie Persistente**: Permite que o cookie de sessão de procuração de aplicação persista entre os fechos do navegador, permanecendo válido até que expire ou seja eliminado. Utilizado para cenários em que uma aplicação rica como o office acede a um documento dentro de uma aplicação web publicada, sem que o utilizador seja re-solicitado para a autenticação. No entanto, ative com cuidado, uma vez que os cookies persistentes podem, em última análise, deixar um serviço em risco de acesso não autorizado, se não forem utilizados em conjunto com outros controlos compensatórios. Esta definição deve ser usada apenas para aplicações mais antigas que não possam partilhar cookies entre processos. É melhor atualizar a sua aplicação para lidar com a partilha de cookies entre processos em vez de usar esta definição.

* **Traduzir URLs em Cabeçalhos**: Você permite isto para cenários onde DNS interno não pode ser configurado para combinar com o espaço de nome público da organização (também conhecido como DNS Split). A menos que a sua aplicação exija o cabeçalho de anfitrião original no pedido do cliente, deixe este valor definido para Sim. A alternativa é fazer com que o conector utilize o FQDN no URL interno para encaminhamento do tráfego real, e o FQDN no URL externo, como cabeçalho do hospedeiro. Na maioria dos casos, esta alternativa deve permitir que a aplicação funcione normalmente, quando acedida remotamente, mas os seus utilizadores perdem os benefícios de ter uma correspondência dentro & URL externo.

* **Traduzir URLs no Corpo de Aplicações**: Ligue a tradução do link do Corpo de Aplicação para uma aplicação quando pretender que os links dessa aplicação sejam traduzidos em respostas ao cliente. Se ativada, esta função proporciona uma melhor tentativa de esforço para traduzir todas as ligações internas que a App Proxy encontra nas respostas HTML e CSS sendo devolvidas aos clientes. É útil na publicação de apps que contenham links de nome absoluto ou NetBIOS codificados no conteúdo, ou aplicações com conteúdo que se liga a outras aplicações no local.

Para cenários em que uma aplicação publicada se ligue a outras aplicações publicadas, permita a tradução de links para cada aplicação para que tenha controlo sobre a experiência do utilizador ao nível das aplicações.

Por exemplo, suponha que tem três aplicações publicadas através do Application Proxy que todos se ligam entre si: Benefícios, Despesas e Viagem, além de uma quarta aplicação, Feedback, que não é publicada através do Application Proxy.

![Imagem 1](media/App-proxy-deployment-plan/link-translation.png)

Quando permite a tradução de links para a aplicação Benefits, as ligações a Despesas e Viagens são redirecionadas para os URLs externos para essas aplicações, para que os utilizadores que acedam às aplicações de fora da rede corporativa possam aceder a elas. As ligações entre despesas e viagens de volta a Benefícios não funcionam porque a tradução de links não foi ativada para essas duas aplicações. O link para feedback não é redirecionado porque não existe URL externo, pelo que os utilizadores que usam a app Benefits não poderão aceder à aplicação de feedback de fora da rede corporativa. Consulte informações detalhadas sobre [a tradução de ligações e outras opções de redirecionamento.](application-proxy-configure-hard-coded-link-translation.md)

### <a name="access-your-application"></a>Aceda à sua aplicação

Existem várias opções para gerir o acesso aos recursos publicados pela App Proxy, por isso escolha o mais adequado para o seu cenário e necessidades de escalabilidade. As abordagens comuns incluem: usar grupos no local que estão a ser sincronizados via Azure AD Connect, criar Grupos Dinâmicos em AZure AD com base em atributos do utilizador, utilizando grupos de self-service que são geridos por um proprietário de recursos, ou uma combinação de todos eles. Consulte os recursos ligados para os benefícios de cada um.

A forma mais direta de atribuir aos utilizadores o acesso a uma aplicação é ir para as opções **de Utilizadores e Grupos** a partir do painel esquerdo da sua aplicação publicada e atribuir diretamente grupos ou indivíduos.

![Imagem 24](media/App-proxy-deployment-plan/add-user.png)

Também pode permitir que os utilizadores tenham acesso autosserviço à sua aplicação, atribuindo um grupo do qual não são atualmente membros e configurando as opções de autosserviço.

![Imagem 25](media/App-proxy-deployment-plan/allow-access.png)

Se estiverem ativados, os utilizadores poderão então entrar no portal myApps e solicitar acesso, e ser automaticamente aprovados e adicionados ao grupo de self-service já autorizado, ou precisarão da aprovação de um aprovador designado.

Os utilizadores convidados também podem ser [convidados a aceder a aplicações internas publicadas através da Aplicação Proxy através do Azure AD B2B](../external-identities/add-users-information-worker.md).

Para aplicações que normalmente são acessíveis de forma anónima, não requerendo autenticação, pode preferir desativar a opção localizada nas **Propriedades** da aplicação.

![Imagem 26](media/App-proxy-deployment-plan/assignment-required.png)


Deixar esta opção definida para Não permite que os utilizadores acedam à aplicação no local através do Azure AD App Proxy sem permissões, por isso use com cuidado.

Uma vez publicada a sua aplicação, deve ser acessível digitando o seu URL externo num browser ou pelo seu ícone em [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

### <a name="enable-pre-authentication"></a>Ativar a pré-autenticação

Verifique se a sua aplicação está acessível através do Application Proxy acedendo-a através do URL externo.

1. Navegue para aplicações **Azure Ative Directory**  >  **Enterprise**  >  **Todas as aplicações** e escolha a app que pretende gerir.

2. Selecione **Application Proxy**.

3. No campo **Pré-Autenticação,** utilize a lista de dropdown para selecionar **o Diretório Ativo Azure** e selecione **Guardar**.

Com a pré-autenticação ativada, o Azure AD irá desafiar os utilizadores primeiro para a autenticação e se a única súbton estiver configurada, a aplicação back-end também verificará o utilizador antes de o acesso à aplicação ser concedido. A alteração do modo de pré-autenticação de Passthrough para Azure AD também configura o URL externo com HTTPS, pelo que qualquer aplicação inicialmente configurada para HTTPS será agora assegurada com HTTPS.

### <a name="enable-single-sign-on"></a>Ativar Sign-On individuais

O SSO fornece a melhor experiência e segurança possível do utilizador, porque os utilizadores só precisam de fazer sedúns uma vez ao aceder ao Azure AD. Uma vez que um utilizador tenha pré-autenticado, o SSO é realizado pelo conector Proxy da aplicação proxy que autentica para a aplicação no local, em nome do utilizador. A aplicação backend processa o login como se fosse o próprio utilizador.

A escolha da opção **Passthrough** permite que os utilizadores acedam à aplicação publicada sem nunca terem de autenticar a AZure AD.

A realização de SSO só é possível se o Azure AD conseguir identificar o utilizador que solicita o acesso a um recurso, pelo que a sua aplicação deve ser configurada para pré-autenticar os utilizadores com Azure AD no acesso ao funcionamento do SSO, caso contrário as opções SSO serão desativadas.

Leia [o único sinal de acesso às aplicações em Azure AD](what-is-single-sign-on.md) para ajudá-lo a escolher o método SSO mais adequado ao configurar as suas aplicações.

###  <a name="working-with-other-types-of-applications"></a>Trabalhar com outros tipos de aplicações

O Azure AD Application Proxy também pode suportar aplicações que tenham sido desenvolvidas para utilizar a Biblioteca de Autenticação da [Microsoft (MSAL)](../develop/v2-overview.md). Suporta aplicações de clientes nativos consumindo fichas emitidas pela Azure AD recebidas na informação de cabeçalho do pedido do cliente para realizar a pré-autenticação em nome dos utilizadores.

Leia [a publicação de aplicações de clientes nativos e móveis](./application-proxy-configure-native-client-application.md) e [aplicações baseadas em sinistros](./application-proxy-configure-for-claims-aware-applications.md) para saber sobre as configurações disponíveis do Application Proxy.

### <a name="use-conditional-access-to-strengthen-security"></a>Utilizar acesso condicional para reforçar a segurança

A segurança da aplicação requer um conjunto avançado de capacidades de segurança que podem proteger e responder a ameaças complexas no local e na nuvem. Os atacantes obtêm mais frequentemente acesso à rede corporativa através de credenciais de utilizador fracas, padrão ou roubadas.  A segurança baseada na identidade da Microsoft reduz o uso de credenciais roubadas gerindo e protegendo identidades privilegiadas e não privilegiadas.

As seguintes capacidades podem ser usadas para suportar o Proxy de aplicação AD AD do Azure:

* Acesso Condicional baseado no utilizador e na localização: Mantenha os dados sensíveis protegidos limitando o acesso do utilizador com base na geolocalização ou num endereço IP com [políticas de acesso condicional baseadas na localização.](../conditional-access/location-condition.md)

* Acesso Condicional baseado no dispositivo: Garantir que apenas dispositivos matriculados, aprovados e compatíveis podem aceder a dados corporativos com [acesso condicional baseado no dispositivo.](../conditional-access/require-managed-devices.md)

* Acesso Condicional baseado em aplicações: O trabalho não tem de parar quando um utilizador não está na rede corporativa. [Acesso seguro a aplicativos de nuvem corporativa e no local](../conditional-access/app-based-conditional-access.md) e manter o controlo com acesso condicional.

* Acesso Condicional baseado em risco: Proteja os seus dados de hackers maliciosos com uma [política de acesso condicional baseada no risco](https://www.microsoft.com/cloud-platform/conditional-access) que pode ser aplicada a todas as aplicações e a todos os utilizadores, seja no local ou na nuvem.

* Azure AD My Apps: Com o seu serviço Proxy de aplicação implantado, e aplicações publicadas de forma segura, oferecem aos seus utilizadores um simples hub para descobrir e aceder a todas as suas aplicações. Aumentar a produtividade com capacidades de self-service, como a capacidade de solicitar o acesso a novas apps e grupos ou gerir o acesso a esses recursos em nome de outros, através das [Minhas Apps.](https://aka.ms/AccessPanelDPDownload)

## <a name="manage-your-implementation"></a>Gerir a sua implementação

### <a name="required-roles"></a>Funções necessárias

A Microsoft defende o princípio de conceder o mínimo privilégio possível para executar as tarefas necessárias com a Azure AD. [Reveja as diferentes funções Azure que estão disponíveis](../roles/permissions-reference.md) e escolha a certa para responder às necessidades de cada persona. Algumas funções podem ter de ser aplicadas temporariamente e removidas após a colocação concluída.

| Papel de negócio| Tarefas empresariais| Funções do Azure AD |
|---|---|---|
| Administração de mesa de ajuda | Normalmente limitado a problemas de relatórios de utilizador final qualificados e a executar tarefas limitadas, como alterar as palavras-passe dos utilizadores, invalidar tokens de atualização e monitorizar a saúde do serviço. | Administrador helpdesk |
| Administrador de identidade| Leia o sinal de Azure AD em relatórios e registos de auditoria para depurar problemas relacionados com a App Proxy.| Leitor de segurança |
| Proprietário de aplicação| Criar e gerir todos os aspetos das aplicações empresariais, registos de candidaturas e configurações de procuração de aplicações.| Administrador de Aplicação |
| Administração de infraestruturas | Proprietário de capotamento de certificado | Administrador de Aplicação |

Minimizar o número de pessoas que têm acesso a informações ou recursos seguros ajudará a reduzir a chance de um ator malicioso obter acesso não autorizado, ou um utilizador autorizado inadvertidamente impactando um recurso sensível.

No entanto, os utilizadores ainda precisam de realizar operações privilegiadas diárias, pelo que a aplicação de políticas de [Gestão de Identidade Privilegiada](../privileged-identity-management/pim-configure.md) baseadas no tempo (JIT) para fornecer acesso privilegiado a pedido aos recursos Azure e a AZure AD é a nossa abordagem recomendada para gerir eficazmente o acesso administrativo e a auditoria.

### <a name="reporting-and-monitoring"></a>Relatórios e monitorização

A Azure AD fornece informações adicionais sobre o uso da aplicação da sua organização e saúde operacional através [de registos e relatórios](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)de auditoria. O Proxy da aplicação também facilita a monitorização dos conectores a partir do portal AD AD do Azure e dos Registos de Eventos do Windows.

#### <a name="application-audit-logs"></a>Registos de auditoria de aplicação

Estes registos fornecem informações detalhadas sobre logins a aplicações configuradas com o Application Proxy e o dispositivo e o utilizador que acede à aplicação. [Os registos de auditoria](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) estão localizados no portal Azure e na [API de Auditoria](/graph/api/resources/directoryaudit?view=graph-rest-beta) para exportação. Além disso, [os relatórios de utilização e insights](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context) também estão disponíveis para a sua aplicação.

#### <a name="application-proxy-connector-monitoring"></a>Monitorização do conector de procuração de aplicação

Os conectores e o serviço cuidam de todas as tarefas de alta disponibilidade. Pode monitorizar o estado dos seus conectores a partir da página 'Procuração de aplicação' no Portal AD Azure. Para obter mais informações sobre a manutenção do conector consulte [Understand Azure AD Application Proxy Connectors](./application-proxy-connectors.md#maintenance).

![Exemplo: Conectores Proxy de aplicação AD AZure](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Registos de eventos do Windows e contadores de desempenho

Os conectores têm registos de administração e de sessão. Os registos de administração incluem eventos-chave e os seus erros. Os registos da sessão incluem todas as transações e os seus dados de processamento. Os registos e balcões estão localizados em Registos de Eventos do Windows para obter mais informações ver [Conectores de Aplicação AD Azure.](./application-proxy-connectors.md#under-the-hood) Siga este [tutorial para configurar fontes de dados de registo de eventos no Azure Monitor](../../azure-monitor/platform/data-sources-windows-events.md).

### <a name="troubleshooting-guide-and-steps"></a>Guia e passos de resolução de problemas

Saiba mais sobre questões comuns e como resolvê-las com o nosso guia para resolver mensagens [de](application-proxy-troubleshoot.md) erro.

Os seguintes artigos abrangem cenários comuns que também podem ser usados para criar guias de resolução de problemas para a sua organização de suporte.

* [Problema ao apresentar a página da aplicação](application-proxy-page-appearance-broken-problem.md)
* [O carregamento da aplicação é demasiado demorado](application-proxy-page-load-speed-problem.md)
* [As ligações na página da aplicação não funcionam](application-proxy-page-links-broken-problem.md)
* [Que portas devo abrir para a minha aplicação](application-proxy-add-on-premises-application.md)
* [Não existe nenhum conector que funcione num grupo de conectores da minha aplicação](application-proxy-connectivity-no-working-connector.md)
* [Configurar no portal de administração](application-proxy-config-how-to.md)
* [Configurar o início de sessão único na minha aplicação](application-proxy-config-sso-how-to.md)
* [Problema ao criar uma aplicação no portal de administração](application-proxy-config-problem.md)
* [Configurar Delegação Restrita de Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Configurar com o PingAccess](./application-proxy-ping-access-publishing-guide.md)
* [Erro "Não é Possível Aceder a esta Aplicação Empresarial"](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Problema ao instalar o Conector do Agente do Proxy de Aplicações](application-proxy-connector-installation-problem.md)
* [Problema ao iniciar sessão](application-sign-in-problem-on-premises-application-proxy.md)