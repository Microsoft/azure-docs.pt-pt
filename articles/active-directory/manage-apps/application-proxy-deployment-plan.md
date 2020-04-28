---
title: Planeie uma implementação de proxy de aplicação de diretório ativo Azure
description: Um guia de ponta a ponta para planear a implementação de procuração de aplicação dentro da sua organização
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: baselden
ms.reviewer: ''
ms.openlocfilehash: edd607c4d708df9fcfd3cbd5fdb71f0a7652d6c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80330916"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Planeie uma implementação de proxy de aplicação ad azure

O Azure Ative Directory (Azure AD) Application Proxy é uma solução de acesso remoto segura e rentável para aplicações no local. Fornece um caminho de transição imediata para as organizações "Cloud First" gerir o acesso a aplicações antigas no local que ainda não são capazes de usar protocolos modernos. Para obter informações introdutórias adicionais, consulte [o que é procuração](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)de aplicação .

Aplicação Proxy é recomendado para dar aos utilizadores remotos acesso a recursos internos. Aplicação Proxy substitui a necessidade de uma VPN ou procuração inversa para estes casos de utilização remota. Não se destina a utilizadores que se encontram na rede corporativa. Estes utilizadores que utilizam o Application Proxy para acesso intranet podem ter problemas de desempenho indesejáveis.

Este artigo inclui os recursos necessários para planear, operar e gerir a Procuração de Aplicação AD Azure. 

## <a name="plan-your-implementation"></a>Planeie a sua implementação

A secção seguinte fornece uma visão ampla dos elementos-chave de planeamento que o configurarão para uma experiência de implementação eficiente. 

### <a name="prerequisites"></a>Pré-requisitos

Tem de cumprir os seguintes pré-requisitos antes de iniciar a sua implementação. Pode ver mais informações sobre a configuração do seu ambiente, incluindo estes pré-requisitos, neste [tutorial.](application-proxy-add-on-premises-application.md)

* **Conectores**: Os conectores são agentes leves que pode implantar:
   * Hardware físico no local
   * Um VM hospedado dentro de qualquer solução hipervisor
   * Um VM hospedado em Azure para permitir a ligação de saída ao serviço Proxy de Aplicação.

* Consulte [os Conectores proxy da App AD Do Incompor](application-proxy-connectors.md) em Obter uma visão geral mais detalhada.

     * As máquinas de conectores devem [ser ativadas para TLS 1.2](application-proxy-add-on-premises-application.md) antes de instalar os conectores.

     * Se possível, implemente conectores na [mesma rede](application-proxy-network-topology.md) e segmento que os servidores de aplicações web back-end. É melhor implantar conectores depois de concluir uma descoberta de aplicações.
     * Recomendamos que cada grupo de conectores tenha pelo menos dois conectores para fornecer alta disponibilidade e escala. Ter três conectores é ideal para o caso de precisar de servir uma máquina em qualquer ponto. Reveja a tabela de capacidade do [conector](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning) para ajudar a decidir em que tipo de máquina instalar conectores. Quanto maior for a máquina, mais tampão e executante será o conector.

* **Definições de acesso**à rede : Os conectores de procuração de aplicação Azure AD [ligam-se ao Azure via HTTPS (Porta TCP 443) e HTTP (Porta TCP 80)](application-proxy-add-on-premises-application.md). 

   * O tráfego de conector TLS não é suportado e impedirá que os conectores estabeleçam um canal seguro com os respetivos pontos finais do Proxy da Aplicação Azure.

   * Evite todas as formas de inspeção em linha nas comunicações TLS de saída entre conectores e Azure. A inspeção interna entre um conector e aplicações de backend é possível, mas pode degradar a experiência do utilizador, e como tal, não é recomendado.

   * O equilíbrio de carga dos conectores também não é suportado, nem mesmo necessário.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Considerações importantes antes de configurar procuração de aplicação ad-ad azure

Os seguintes requisitos fundamentais devem ser cumpridos para configurar e implementar o Proxy de Aplicação AD Azure.

*  **Onboarding Azure**: Antes de implementar o proxy da aplicação, as identidades dos utilizadores devem ser sincronizadas a partir de um diretório no local ou criadas diretamente dentro dos seus inquilinos da AD Azure. A sincronização de identidade permite que a AD Azure autentique previamente os utilizadores antes de lhes conceder acesso às aplicações publicadas pela App Proxy e ter as informações necessárias para o identificador de utilizador para realizar um único registo (SSO).

* **Requisitos de Acesso Condicional**: Não recomendamos a utilização de Procuração de Aplicação para acesso intranet porque isso adiciona latência que terá impacto nos utilizadores. Recomendamos a utilização de Proxy de Aplicação com pré-autenticação e políticas de acesso condicional para acesso remoto a partir da internet.  Uma abordagem para proporcionar acesso condicional para uso intranet é modernizar aplicações para que possam autenticar diretamente com AAD. Consulte os [Recursos para aplicações migratórias para a AAD](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) para obter mais informações. 

* **Limites de serviço**: Para proteger contra o consumo excessivo de recursos por parte de inquilinos individuais, existem limites de estrangulamento definidos por aplicação e inquilino. Para ver estes limites consulte [os limites e restrições de serviço da Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions). Estes limites de estrangulamento baseiam-se numa referência muito acima do volume de utilização típico e fornecem um amplo tampão para a maioria das implementações.

* **Certificado público**: Se estiver a usar nomes de domínio personalizados, deve adquirir um certificado TLS/SSL. Dependendo dos seus requisitos organizacionais, obter um certificado pode demorar algum tempo e recomendamos iniciar o processo o mais cedo possível. A Procuração de Aplicações Azure suporta certificados padrão, [wildcard](application-proxy-wildcard.md)ou san. Para mais detalhes consulte [Configure domínios personalizados com procuração de aplicação ad azure](application-proxy-configure-custom-domain.md).

* **Requisitos de domínio**: Um único sinal nas suas aplicações publicadas utilizando a Delegação Limitada kerberos (KCD) requer que o servidor que executa o Connector e o servidor que executa a aplicação sejam integrados no domínio e parte do mesmo domínio ou domínios de confiança.
Para obter informações detalhadas sobre o tema, consulte [a KCD para um único registo](application-proxy-configure-single-sign-on-with-kcd.md) com o Application Proxy. O serviço de conector funciona no contexto do sistema local e não deve ser configurado para utilizar uma identidade personalizada.

* **Registos dNS para URLs**

   * Antes de utilizar domínios personalizados no Proxy de Aplicação, deve criar um registo CNAME em DNS público, permitindo que os clientes resolvam o URL externo definido personalizado para o endereço proxy de aplicação pré-definido. Se não criar um registo CNAME para uma aplicação que utilize um domínio personalizado impedirá os utilizadores remotos de se ligarem à aplicação. As etapas necessárias para adicionar registos CNAME podem variar de fornecedor dNS para fornecedor, por isso, aprenda a [gerir os registos dNS e os conjuntos de registos utilizando o portal Azure](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal).

   * Da mesma forma, os anfitriões do conector devem ser capazes de resolver o URL interno das aplicações que estão a ser publicadas.

* **Direitos e funções administrativas**

   * **A instalação do conector** requer direitos de administração locais para o servidor Windows em que está a ser instalado. Também requer um mínimo de uma função de Administrador de *Aplicação* para autenticar e registar a instância do conector ao seu inquilino Azure AD. 

   * **A publicação e a administração** de candidaturas requerem o papel de Administrador de *Aplicação.* Os Administradores de Aplicações podem gerir todas as aplicações no diretório, incluindo registos, definições sso, atribuições de utilizador e grupo e licenciamento, definições de Procuração de Aplicações e consentimento. Não concede a capacidade de gerir o Acesso Condicional. A função de Administrador de *Aplicação cloud* tem todas as capacidades do Administrador de Aplicação, exceto que não permite a gestão das definições de Proxy de Aplicação.

* **Licenciamento**: Application Proxy está disponível através de uma subscrição Azure AD Premium. Consulte a página de [Preços do Diretório Ativo Azure](https://azure.microsoft.com/pricing/details/active-directory/) para obter uma lista completa de opções e funcionalidades de licenciamento.  

### <a name="application-discovery"></a>Descoberta de Aplicações

Compile um inventário de todas as aplicações no âmbito que estão a ser publicadas via Procuração de Aplicação, recolhendo as seguintes informações:

| Tipo de informação| Informações para recolher |
|---|---|
| Tipo de Serviço| Por exemplo: SharePoint, SAP, CRM, Aplicação Web Personalizada, API |
| Plataforma de aplicação | Por exemplo: Windows IIS, Apache em Linux, Tomcat, NGINX |
| Associação ao domínio| Nome de domínio totalmente qualificado do servidor web (FQDN) |
| Localização da candidatura | Onde o servidor web ou quinta está localizado na sua infraestrutura |
| Acesso interno | O URL exato utilizado no acesso interno à aplicação. <br> Se uma fazenda, que tipo de equilíbrio de carga está em uso? <br> Se a aplicação extrai conteúdo de outras fontes que não a si mesma.<br> Determine se a aplicação funciona através de WebSockets. |
| Acesso externo | A solução do fornecedor a que a aplicação já está exposta externamente. <br> O URL que pretende utilizar para acesso externo. Se o SharePoint, certifique-se de que os Mapeamentos de Acesso Alternativos estão configurados por [esta orientação](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings). Caso contrário, terá de definir URLs externos. |
| Certificado público | Se utilizar um domínio personalizado, obtenha um certificado com um nome de assunto correspondente. se existir um certificado, note-se o número de série e a localização a partir do local onde pode ser obtido. |
| Tipo de autenticação| O tipo de autenticação suportado pelo suporte à aplicação, como A autenticação básica, de integração do Windows, baseada em formulários, baseado em cabeçalhos e reclamações. <br>Se a aplicação estiver configurada para ser executada sob uma conta de domínio específica, note o Nome de Domínio Totalmente Qualificado (FQDN) da conta de serviço.<br> Se for baseado em SAML, o identificador e os URLs de resposta. <br> Se for baseada em cabeçalho, a solução do fornecedor e o requisito específico para manusear o tipo de autenticação. |
| Nome do grupo do conector | O nome lógico para o grupo de conectores que será designado para fornecer a conduta e SSO a esta aplicação de backend. |
| Acesso a Utilizadores/Grupos | Os utilizadores ou grupos de utilizadores que terão acesso externo à aplicação. |
| Requisitos adicionais | Note quaisquer requisitos adicionais de acesso remoto ou de segurança que devem ser considerados para a publicação da aplicação. |

Pode descarregar esta folha de cálculo de inventário de [aplicações](https://aka.ms/appdiscovery) para inventariar as suas apps.

### <a name="define-organizational-requirements"></a>Definir requisitos organizacionais

As seguintes são áreas para as quais deve definir os requisitos de negócio da sua organização. Cada área contém exemplos de requisitos

 **Acesso**

* Utilizadores remotos com domínio aderido ou Dispositivos AD Azure unidos podem aceder às aplicações publicadas de forma segura com um único sinal individual sem emenda (SSO).

* Os utilizadores remotos com dispositivos pessoais aprovados podem aceder de forma segura às aplicações publicadas desde que estejam inscritos no MFA e tenham registado a aplicação Microsoft Authenticator no seu telemóvel como um método de autenticação.

**Governação** 

* Os administradores podem definir e monitorizar o ciclo de vida das atribuições dos utilizadores às aplicações publicadas através do Application Proxy.

**Segurança**

* Apenas os utilizadores atribuídos a aplicações através de membros do grupo ou individualmente podem aceder a essas aplicações.

**Desempenho**

* Não existe degradação do desempenho da aplicação em comparação com o acesso à aplicação da rede interna.

**Experiência do Utilizador**

* Os utilizadores estão cientes de como aceder às suas aplicações utilizando URLs familiares da empresa em qualquer plataforma de dispositivos.

**Auditoria**
* Os administradores podem auditar a atividade de acesso ao utilizador.


### <a name="best-practices-for-a-pilot"></a>Boas práticas para um piloto

Determine o tempo e o esforço necessários para encomendar plenamente um único pedido de acesso remoto com um único letão (SSO). Faça-o executando um piloto que considere a sua descoberta inicial, publicação e testes gerais. A utilização de uma aplicação web baseada no IIS que já está pré-configurada para autenticação integrada do Windows (IWA) ajudaria a estabelecer uma linha de base, uma vez que esta configuração requer um esforço mínimo para pilotar com sucesso o acesso remoto e o SSO.

Os seguintes elementos de design devem aumentar o sucesso da sua implementação piloto diretamente num inquilino de produção.  

**Gestão do conector:**  

* Os conectores desempenham um papel fundamental no fornecimento das condutas no local às suas aplicações. A utilização do grupo de conector **Predefinido** é adequada para os testes piloto iniciais das aplicações publicadas antes de as encomendar à produção. As aplicações testadas com sucesso podem então ser transferidas para grupos de conector de produção.

**Gestão de aplicações:**

* A sua força de trabalho é mais provável que se lembre que um URL externo é familiar e relevante. Evite publicar a sua aplicação utilizando os nossos sufixos pré-definidos msappproxy.net ou onmicrosoft.com. Em vez disso, forneça um domínio verificado de alto nível familiar, pré-fixado com um nome de anfitrião lógico como *intranet.<customers_domain>.com*.

* Restringir a visibilidade do ícone da aplicação piloto a um grupo piloto, escondendo o seu ícone de lançamento do portal Azure MyApps. Quando estiver pronto para produção, pode analisar a app para o seu respetivo público-alvo, seja no mesmo inquilino pré-produção, quer publicando também a aplicação no seu inquilino de produção.

**Definições únicas de inscrição**: Algumas definições de SSO têm dependências específicas que podem levar tempo a configurar, por isso evite os atrasos de controlo de mudança, garantindo que as dependências são abordadas com antecedência. Isto inclui anfitriões de conector de ligação de domínio para executar SSO usando a Delegação Limitada Kerberos (KCD) e cuidar de outras atividades que demoram tempo. Por exemplo, configurar uma instância de acesso PING, se precisar de SSO baseado em cabeçalho.

**TLS Entre o anfitrião do conector e**a aplicação do alvo : A segurança é primordial, pelo que o TLS entre o anfitrião do conector e as aplicações-alvo deve ser sempre utilizado. Especialmente se a aplicação web estiver configurada para autenticação baseada em formulários (FBA), uma vez que as credenciais do utilizador são então transmitidas eficazmente em texto claro.

**Implementar incrementalmente e testar cada passo**. Realizar testes funcionais básicos após a publicação de uma aplicação para garantir que todos os requisitos do utilizador e da empresa são cumpridos seguindo as instruções abaixo:

1. Teste e valide o acesso geral à aplicação web com pré-autenticação desativada.
2. Se for bem sucedido, ative a pré-autenticação e atribua utilizadores e grupos. Teste e valide o acesso.
3. Em seguida, adicione o método SSO para a sua aplicação e teste novamente para validar o acesso.
4. Aplicar as políticas de Acesso Condicional e MFA conforme necessário. Teste e valide o acesso.

**Ferramentas de resolução de problemas**: Quando resolver problemas, comece sempre por validar o acesso à aplicação publicada a partir do navegador no anfitrião do conector, e confirme que a aplicação funciona como esperado. Quanto mais simples for a sua configuração, mais fácil determinar a causa raiz, por isso considere tentar reproduzir problemas com uma configuração mínima, como usar apenas um único conector e sem SSO. Em alguns casos, ferramentas de depuração web como o Violinista de Telerik podem revelar-se indispensáveis para resolver problemas de acesso ou problemas de conteúdo em aplicações acedidas através de um proxy. O Fiddler também pode atuar como um proxy para ajudar a rastrear e depurar o tráfego para plataformas móveis como iOS e Android, e praticamente tudo o que pode ser configurado para a rota através de um proxy. Consulte o guia de resolução de [problemas](application-proxy-troubleshoot.md) para mais informações.

## <a name="implement-your-solution"></a>Implementar a sua solução

### <a name="deploy-application-proxy"></a>Implementar procuração de aplicações

As medidas para implementar o seu Proxy de Aplicação estão cobertas neste [tutorial para adicionar uma aplicação no local para acesso remoto](application-proxy-add-on-premises-application.md). Se a instalação não for bem sucedida, selecione **Troubleshoot Application Proxy** no portal ou utilize o guia de resolução de [problemas para problemas com a instalação do Conector de Agente proxy](application-proxy-connector-installation-problem.md)da aplicação .

### <a name="publish-applications-via-application-proxy"></a>Publicar aplicações via Procuração de Aplicações

As aplicações de publicação assumem que preencheu todos os pré-requisitos e que tem vários conectores que mostram como registados e ativos na página Proxy de Aplicação.

Também pode publicar aplicações utilizando o [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview).

Abaixo estão algumas boas práticas a seguir ao publicar uma aplicação:

* **Utilizar Grupos de Conector**: Atribuir um grupo de conector que tenha sido designado para publicação de cada aplicação respetiva. Recomendamos que cada grupo de conectores tenha pelo menos dois conectores para fornecer alta disponibilidade e escala. Ter três conectores é ideal para o caso de precisar de servir uma máquina em qualquer ponto. Além disso, consulte a Publicação de [aplicações em redes e locais separados utilizando grupos de conectores](application-proxy-connector-groups.md) para ver como também pode utilizar grupos de conectores para segmentar os seus conectores por rede ou localização.

* Definir tempo limite de **aplicação de backend**: Esta definição é útil em cenários em que a aplicação pode exigir mais de 75 segundos para processar uma transação de cliente. Por exemplo, quando um cliente envia uma consulta a uma aplicação web que funciona como uma frente para uma base de dados. A parte frontal envia esta consulta para o seu servidor de base de dados back-end e aguarda uma resposta, mas quando recebe uma resposta, o lado cliente da conversa tem tempo. A definição do prazo para Long fornece 180 segundos para que as transações mais longas se completem.

* **Utilizar tipos de cookies apropriados**

   * **Cookie http-only**: Fornece segurança adicional, tendo o Proxy de aplicação incluir a bandeira HTTPOnly em cabeçalhos de resposta HTTP de set-cookie. Esta definição ajuda a mitigar explorações como scripts cross-site (XSS). Deixe este conjunto para Não para clientes/agentes de utilizador que necessitem de acesso ao cookie de sessão. Por exemplo, o cliente RDP/MTSC que se conecta a um Gateway de Ambiente de Trabalho Remoto publicado via App Proxy.

   * **Cookie seguro**: Quando um cookie é definido com o atributo Seguro, o agente de utilizador (aplicação do lado do cliente) só incluirá o cookie em pedidos HTTP se o pedido for transmitido através de um canal seguro TLS. Isto ajuda a mitigar o risco de um cookie ser comprometido através de canais de texto claros, por isso deve ser ativado.

   * **Cookie persistente**: Permite que o cookie de sessão proxy de aplicação persista entre os encerramentos do navegador, permanecendo válido até que expire ou seja eliminado. Usado para cenários em que uma aplicação rica como o office acede a um documento dentro de uma aplicação web publicada, sem que o utilizador seja resolicitado para autenticação. No entanto, ative com cautela, uma vez que os cookies persistentes podem, em última análise, deixar um serviço em risco de acesso não autorizado, se não for utilizado em conjunto com outros controlos compensatórios. Esta definição só deve ser utilizada para aplicações mais antigas que não possam partilhar cookies entre processos. É melhor atualizar a sua aplicação para lidar com a partilha de cookies entre processos em vez de usar esta definição.

* **Traduza URLs em Cabeçalhos**: Permite-o para cenários em que o DNS interno não pode ser configurado para corresponder ao espaço de nomepúblico da organização (também conhecido como DNS split). A menos que o seu pedido exija o cabeçalho original do anfitrião no pedido do cliente, deixe este valor definido para Sim. A alternativa é que o conector utilize o FQDN no URL interno para o encaminhamento do tráfego real, e o FQDN no URL externo, como cabeçalho do anfitrião. Na maioria dos casos, esta alternativa deve permitir que a aplicação funcione normalmente, quando acedida remotamente, mas os seus utilizadores perdem os benefícios de ter uma correspondência dentro & URL externo.

* **Tradução de URLs no Corpo**de Aplicação : Ligue a tradução de link do Corpo de Aplicação para uma aplicação quando quiser que os links dessa aplicação sejam traduzidos em respostas ao cliente. Se ativada, esta função proporciona uma melhor tentativa de esforço para traduzir todas as ligações internas que a App Proxy encontra nas respostas HTML e CSS sendo devolvidas aos clientes. É útil publicar aplicações que contenham links de nome absoluto ou netBIOS de código duro no conteúdo, ou aplicações com conteúdo que se ligue a outras aplicações no local.

Para cenários em que uma aplicação publicada se liga a outras aplicações publicadas, ative a tradução de links para cada aplicação para que tenha controlo sobre a experiência do utilizador a nível por app.

Por exemplo, suponha que tem três aplicações publicadas através do Application Proxy que todas se ligam entre si: Benefícios, Despesas e Viagens, além de uma quarta aplicação, Feedback, que não é publicada através do Application Proxy.

![Imagem 1](media/App-proxy-deployment-plan/link-translation.png)

Quando permite a tradução de links para a aplicação Benefits, as ligações para Despesas e Viagens são redirecionadas para os URLs externos para essas aplicações, para que os utilizadores que acedam às aplicações de fora da rede corporativa possam aceder às mesmas. As ligações das Despesas e viagens de volta aos Benefícios não funcionam porque a tradução de links não foi ativada para essas duas aplicações. O link para feedback não é redirecionado porque não há URL externo, pelo que os utilizadores que usam a aplicação Benefits não poderão aceder à aplicação de feedback de fora da rede corporativa. Consulte informações detalhadas sobre a tradução de [ligações e outras opções de redirecionamento.](application-proxy-configure-hard-coded-link-translation.md)

### <a name="access-your-application"></a>Aceda à sua aplicação

Existem várias opções para gerir o acesso aos recursos publicados pela App Proxy, por isso escolha as mais adequadas para o seu cenário e necessidades de escalabilidade. As abordagens comuns incluem: usar grupos no local que estão a ser sincronizados através do Azure AD Connect, criando Grupos Dinâmicos em AD Azure com base em atributos de utilizador, utilizando grupos de self-service que são geridos por um proprietário de recursos, ou uma combinação de todos estes. Consulte os recursos ligados para os benefícios de cada um.

A forma mais direta de atribuir aos utilizadores o acesso a uma aplicação é indo para as opções de **Utilizadores e Grupos** a partir do painel esquerdo da sua aplicação publicada e atribuindo diretamente grupos ou indivíduos.

![Imagem 24](media/App-proxy-deployment-plan/add-user.png)

Também pode permitir que os utilizadores possam aceder ao autosserviço à sua aplicação, atribuindo um grupo que não são atualmente membros e configurando as opções de self-service.

![Imagem 25](media/App-proxy-deployment-plan/allow-access.png)

Se ativado, os utilizadores poderão então entrar no portal MyApps e solicitar o acesso, e ser ão automaticamente aprovados e adicionados ao grupo de self-service já permitido, ou precisarão da aprovação de um aprovador designado.

Os utilizadores convidados também podem ser [convidados a aceder a aplicações internas publicadas via Application Proxy através do Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker).

Para aplicações no local que normalmente são acessíveis anonimamente, não requerendo autenticação, pode preferir desativar a opção localizada nas **Propriedades**da aplicação .

![Imagem 26](media/App-proxy-deployment-plan/assignment-required.png)


Deixar esta opção definida para Não permite que os utilizadores acedam à aplicação no local através do Azure AD App Proxy sem permissões, por isso, use com cuidado.

Uma vez publicada a sua aplicação, deve ser acessível digitando o seu URL externo num browser ou pelo seu ícone em [https://myapps.microsoft.com](https://myapps.microsoft.com/).

### <a name="enable-pre-authentication"></a>Ativar a pré-autenticação

Verifique se a sua aplicação está acessível através do Proxy de Aplicação, acedendo-a através do URL externo. 

1. Navegue para**aplicações** >  **Da Azure Ative Directory** > Enterprise**Todas as aplicações** e escolha a app que pretende gerir.

2. Selecione **Procuração de Aplicação**.

3. No campo **de Pré-Autenticação,** utilize a lista de dropdown para selecionar **o Diretório Ativo Azure**, e selecione **Save**.

Com a pré-autenticação ativada, o Azure AD irá desafiar os utilizadores primeiro para a autenticação e se o único sinal de entrada estiver configurado, a aplicação back-end também verificará o utilizador antes de ser concedido o acesso à aplicação. A alteração do modo de pré-autenticação de Passthrough para Azure AD também configura o URL externo com HTTPS, pelo que qualquer aplicação inicialmente configurada para HTTP será agora protegida com HTTPS.

### <a name="enable-single-sign-on"></a>Ativar um único sign-on

O SSO fornece a melhor experiência e segurança possível do utilizador, uma vez que os utilizadores só precisam de iniciar sessão uma vez ao aceder ao Azure AD. Uma vez pré-autenticado um utilizador, o SSO é realizado pelo conector Proxy de aplicação autenticando-se na aplicação no local, em nome do utilizador. A aplicação backend processa o login como se fosse o próprio utilizador. 

A escolha da opção **Passthrough** permite que os utilizadores acedam à aplicação publicada sem nunca terem de autenticar a Azure AD.

A realização do SSO só é possível se o Azure AD conseguir identificar o utilizador que solicita o acesso a um recurso, pelo que a sua aplicação deve ser configurada para pré-autenticar utilizadores com AD Azure no acesso ao SSO para funcionar, caso contrário as opções SSO serão desativadas.

Leia o único sessão de [inscrição em aplicações em AD Azure](what-is-single-sign-on.md) para ajudá-lo a escolher o método SSO mais adequado ao configurar as suas aplicações.

###  <a name="working-with-other-types-of-applications"></a>Trabalhar com outros tipos de aplicações

O Procurador de Aplicação Azure AD também pode suportar aplicações que foram desenvolvidas para usar a nossa Biblioteca de Autenticação AD Azure[(ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)ou a Microsoft Authentication Library[(MSAL).](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/) Suporta aplicações de clientes nativos consumindo a AD Azure emitida sintetizadoras recebidas na informação do cliente para realizar a pré-autenticação em nome dos utilizadores.

Leia a publicação de aplicações de [clientes nativos e móveis](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client) e [aplicações baseadas em reivindicações](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps) para conhecer as configurações disponíveis do Proxy de Aplicação.

### <a name="use-conditional-access-to-strengthen-security"></a>Utilizar acesso condicional para reforçar a segurança

A segurança da aplicação requer um conjunto avançado de capacidades de segurança que possam proteger e responder a ameaças complexas no local e na nuvem. Os atacantes obtêm, na maioria das vezes, acesso à rede corporativa através de credenciais de utilizador fracas, por defeito ou roubadas.  A segurança baseada na identidade da Microsoft reduz o uso de credenciais roubadas, gerindo e protegendo identidades privilegiadas e não privilegiadas.

As seguintes capacidades podem ser utilizadas para suportar o Proxy de Aplicação AD Azure:

* Acesso condicional baseado no utilizador e na localização: Mantenha os dados sensíveis protegidos limitando o acesso do utilizador com base na geolocalização ou num endereço IP com [políticas de acesso condicional baseadas na localização.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)

* Acesso Condicional baseado no dispositivo: Certifique-se de que apenas dispositivos matriculados, aprovados e conformes podem aceder a dados corporativos com [acesso condicional baseado no dispositivo](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications).

* Acesso Condicional baseado em aplicações: O trabalho não tem de parar quando um utilizador não está na rede corporativa. [Acesso seguro a aplicações corporativas de nuvem e no local](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) e manter o controlo com acesso condicional.

* Acesso Condicional baseado em risco: Proteja os seus dados de hackers maliciosos com uma [política de acesso condicional baseada no risco](https://www.microsoft.com/cloud-platform/conditional-access) que pode ser aplicada a todas as aplicações e a todos os utilizadores, seja no local ou na nuvem.

* Painel de Acesso Azure AD: Com o seu serviço proxy de aplicação implementado, e aplicações publicadas de forma segura, ofereça aos seus utilizadores um simples hub para descobrir e aceder a todas as suas aplicações. Aumentar a produtividade com capacidades de self-service, como a capacidade de solicitar acesso a novas apps e grupos ou gerir o acesso a esses recursos em nome de outros, através do Painel de [Acesso.](https://aka.ms/AccessPanelDPDownload)

## <a name="manage-your-implementation"></a>Gerir a sua implementação

### <a name="required-roles"></a>Funções necessárias

A Microsoft defende o princípio de conceder o menor privilégio possível para executar as tarefas necessárias com a AD Azure. [Reveja as diferentes funções azure que estão disponíveis](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) e escolha a certa para responder às necessidades de cada persona. Algumas funções podem ter de ser aplicadas temporariamente e removidas após a conclusão da implantação.

| Papel de negócio| Tarefas empresariais| Funções do Azure AD |
|---|---|---|
| Administração de secretária de ajuda | Normalmente limitado a problemas reportados pelo utilizador final e executando tarefas limitadas, tais como alterar as palavras-passe dos utilizadores, invalidar fichas de atualização e monitorizar a saúde do serviço. | Administrador de Helpdesk |
| Administrador de identidade| Leia o sinal de anúncio do Azure em relatórios e registos de auditoria para depurar questões relacionadas com a App Proxy.| Leitor de segurança |
| Proprietário de candidatura| Criar e gerir todos os aspetos das aplicações empresariais, registos de aplicações e configurações de procuração de aplicações.| Administrador de aplicação |
| Administração de infraestruturas | Proprietário de capotamento de certificado | Administrador de aplicação |

Minimizar o número de pessoas que têm acesso a informações ou recursos seguros ajudará a reduzir a possibilidade de um ator malicioso obter acesso não autorizado, ou um utilizador autorizado inadvertidamente com impacto num recurso sensível. 
 
No entanto, os utilizadores ainda precisam de realizar operações privilegiadas do dia-a-dia, pelo que impor políticas de [gestão](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) de identidade privilegiada baseadas em tempo justo (JIT) para fornecer acesso privilegiado a pedido aos recursos Azure e AD Azure é a nossa abordagem recomendada para gerir eficazmente o acesso administrativo e a auditoria.

### <a name="reporting-and-monitoring"></a>Relatórios e monitorização

A Azure AD fornece informações adicionais sobre o uso de aplicações da sua organização e saúde operacional através de [registos e relatórios](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)de auditoria. Aplicação Proxy também facilita a monitorização de conectores do portal Azure AD e registos de eventos do Windows.

#### <a name="application-audit-logs"></a>Registos de auditoria de aplicação

Estes registos fornecem informações detalhadas sobre logins a aplicações configuradas com proxy de aplicação e o dispositivo e o utilizador que acede à aplicação. [Os registos](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) de auditoria estão localizados no portal Azure e na [Auditoria API](https://docs.microsoft.com/graph/api/resources/directoryaudit?view=graph-rest-beta) para exportação. Além disso, os relatórios de [utilização e insights](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context) também estão disponíveis para a sua aplicação.

#### <a name="application-proxy-connector-monitoring"></a>Monitorização do Conector proxy da aplicação

Os conectores e o serviço cuidam de todas as tarefas de alta disponibilidade. Pode monitorizar o estado dos seus conectores a partir da página Proxy de Aplicação no Portal AD Azure. Para obter mais informações sobre a manutenção do conector, consulte [Conectores proxy de aplicação ad azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#maintenance).

![Exemplo: Conectores de proxy de aplicação da AD Azure](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Registos de eventos do Windows e contadores de desempenho

Os conectores têm registos de administração e sessão. Os registos de administração incluem eventos-chave e seus erros. Os registos da sessão incluem todas as transações e os seus detalhes de processamento. Os registos e balcões estão localizados nos Registos de Eventos do Windows para obter mais informações ver [Understand Azure Application Proxy Connectors](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#under-the-hood). Siga este tutorial para configurar fontes de dados de registo de [eventos no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events).

### <a name="troubleshooting-guide-and-steps"></a>Guia e passos de resolução de problemas

Saiba mais sobre questões comuns e como resolvê-las com o nosso guia para [resolução](application-proxy-troubleshoot.md) de mensagens de erro. 

Os seguintes artigos abrangem cenários comuns que também podem ser usados para criar guias de resolução de problemas para a sua organização de apoio. 

* [Problema ao apresentar a página da aplicação](application-proxy-page-appearance-broken-problem.md)
* [O carregamento da aplicação é demasiado demorado](application-proxy-page-load-speed-problem.md)
* [As ligações na página da aplicação não funcionam](application-proxy-page-links-broken-problem.md)
* [Que portas devo abrir para a minha aplicação](application-proxy-connectivity-ports-how-to.md)
* [Não existe nenhum conector que funcione num grupo de conectores da minha aplicação](application-proxy-connectivity-no-working-connector.md)
* [Configurar no portal de administração](application-proxy-config-how-to.md)
* [Configurar o início de sessão único na minha aplicação](application-proxy-config-sso-how-to.md)
* [Problema ao criar uma aplicação no portal de administração](application-proxy-config-problem.md)
* [Configurar Delegação Restrita de Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Configurar com o PingAccess](application-proxy-back-end-ping-access-how-to.md)
* [Erro "Não é Possível Aceder a esta Aplicação Empresarial"](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Problema ao instalar o Conector do Agente do Proxy de Aplicações](application-proxy-connector-installation-problem.md)
* [Problema ao iniciar sessão](application-sign-in-problem-on-premises-application-proxy.md)
