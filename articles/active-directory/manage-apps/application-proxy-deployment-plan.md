---
title: Planear uma implementação de Proxy de aplicações do Azure Active Directory
description: Um guia de ponto-a-ponto para planear a implementação do proxy de aplicações na sua organização
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04-04-2019
ms.author: barbaraselden
ms.reviewer: ''
ms.openlocfilehash: fe8f9f271599d688878d61aee64273690d02c2b8
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59685835"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Planear uma implementação do Proxy de aplicações do Azure AD

Proxy de aplicações do Azure Active Directory (Azure AD) é uma solução de acesso remoto seguro e económico para aplicações no local. Ele fornece um caminho de transição de imediato para as organizações "Cloud First" Gerir o acesso herdado para aplicações no local que ainda não capaz de usar protocolos modernos. Para obter informações introdutórias adicionais, consulte [o que é o Proxy de aplicações](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) e [como o aplicativo Proxy funciona](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy).

Este artigo inclui os recursos que necessários para planejar, operar e gerir o Proxy de aplicações do Azure AD. 

## <a name="plan-your-implementation"></a>Planeie a sua implementação

A secção seguinte fornece uma visão abrangente da chave de planeamento de elementos que definirá tendo em vista uma experiência de implantação eficiente. 

### <a name="prerequisites"></a>Pré-requisitos

Tem de cumprir os seguintes pré-requisitos antes de iniciar a sua implementação. Pode ver mais informações sobre como configurar o seu ambiente, incluindo estes pré-requisitos, nesta [tutorial](application-proxy-add-on-premises-application.md).

* **Conectores**: Os conectores são agentes leves que pode implementar em:
   * Hardware físico no local
   * Uma VM alojada em qualquer solução de hipervisor
   * Uma VM alojada no Azure para ativar a ligação de saída para o serviço de Proxy de aplicações.

Ver [compreender Proxy de aplicações do Azure AD conectores](application-proxy-connectors.md) para uma visão geral mais detalhada.

   * Aloja o conector tem [ser ativada para o TLS 1.2](application-proxy-add-on-premises-application.md) antes de instalar os conectores.

   * Se possível, implemente conectores no [mesma rede](application-proxy-network-topology.md) e segmento que os servidores de aplicativo web de back-end. É melhor implementar anfitriões de conector depois de concluir uma deteção de aplicações.

* **As definições de acesso de rede**: Conectores de Proxy de aplicações do AD do Azure [tentam ligar ao Azure através de HTTPS (porta TCP 443) e HTTP (porta TCP 80)](application-proxy-add-on-premises-application.md). 

   * Conector de terminação tráfego TLS não é suportado e irá impedir que os conectores de estabelecer um canal seguro com seus respectivos pontos de extremidade do Proxy de aplicações do Azure.

   * Evite todos os formulários de inspeção de inline em comunicações de saída de TLS entre conectores e o Azure. Inspeção interna entre as aplicações de um conector e back-end é possível, mas pode degradar a experiência do usuário e, como tal, não é recomendada.

   * Balanceamento de carga dos conectores do Proxy propriamente ditos também não é suportado, ou até mesmo necessárias.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Considerações importantes antes de configurar o Proxy de aplicações do Azure AD

Os seguintes requisitos principais têm de ser cumpridos para configurar e implementar o Proxy de aplicações do Azure AD.

*  **Integração do Azure**: Antes de implementar o proxy de aplicações, as identidades de utilizador tem de ser sincronizadas a partir de um diretório no local ou criadas diretamente dentro de seus inquilinos do Azure AD. Sincronização de identidade permite que o Azure AD para autenticar previamente os utilizadores antes de lhes conceder acesso ao Proxy de aplicação aplicações publicadas e para que as informações de identificador de utilizador necessários para efetuar logon único (SSO).

* **Certificado público**: Se estiver a utilizar nomes de domínio personalizado, tem de obter um certificado público emitido por uma autoridade de certificado fidedigno de terceiros. Dependendo dos requisitos organizacionais, obter um certificado pode demorar algum tempo e recomendamos que iniciar o processo o mais cedo possível. Proxy de aplicações do Azure suporta a norma [universais](application-proxy-wildcard.md), ou certificados baseados em SAN.

* **Requisitos de domínio**: Início de sessão único para seus aplicativos publicados usando Kerberos Constrained Delegation (KCD) requer que um anfitrião de conector está associado ao mesmo domínio do AD como os que está a ser publicação de aplicativos. Para obter informações detalhadas sobre o assunto, consulte [KCD para início de sessão único](application-proxy-configure-single-sign-on-with-kcd.md) proxy de aplicações. O serviço de conectores é executado no contexto do sistema local e não deve ser configurado para utilizar uma identidade personalizada.

* **Registos DNS para URLs**

   * Antes de utilizar domínios personalizados no Proxy de aplicações tem de criar um registo CNAME no DNS público, que permite aos clientes resolver o URL externo definido personalizado para o endereço de Proxy de aplicações predefinido. Falha ao criar um registo CNAME para uma aplicação que utiliza um domínio personalizado irá impedir que os usuários remotos estabeleçam uma ligação para a aplicação. Passos necessários para adicionar registos CNAME podem variar de DNS fornecedor para fornecedor, por isso, saiba como [gerir registos DNS e conjuntos de registos com o portal do Azure](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal).

   * Da mesma forma, os anfitriões de conector tem de ser capazes de resolver o URL interno de aplicativos a ser publicado.

* **Direitos administrativos e funções**

   * **Instalação de conector** requer direitos de administrador local para o servidor do Windows que está a ser instalado no. Também requer um mínimo de uma função de administrador de aplicação para autenticar e registe-se a instância do conector com o seu inquilino do Azure AD. 

   * **Publicação de aplicativos e de administração** requerem o *administrador da aplicação* função. Os administradores de aplicações podem gerir todas as aplicações no diretório, incluindo registos, as definições de SSO, utilizador e as atribuições de grupo e licenciamento, definições de Proxy de aplicações e consentimento. Ele não concede a capacidade de gerir o acesso condicional. O *administrador da aplicação Cloud* função tem todas as capacidades de administrador da aplicação, exceto que ela não permite a gestão das definições de Proxy de aplicações.

* **Licenciamento**: Proxy de aplicações está disponível através da subscrição do Azure AD básico. Consulte a [página de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) para uma lista completa das funcionalidades e opções de licenciamento. 

* Uma elevação de função pode ser necessário para obter direitos de administrador da aplicação através de [Privileged Identity Manager](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) (PIM), portanto, certifique-se a sua conta é elegível. 

### <a name="application-discovery"></a>Deteção de aplicação

Compile um inventário de todos os aplicativos de dentro do âmbito que estão a ser publicados através do Proxy de aplicações através da recolha as seguintes informações:

| Tipo de informações| Informações a recolher |
|---|---|
| Tipo de Serviço| Por exemplo: SharePoint, SAP, CRM, aplicação Web personalizada, API |
| Plataforma de aplicações | Por exemplo: Windows IIS, Apache no Linux, Tomcat, NGINX |
| Associação ao domínio| Nome de domínio completamente qualificado do servidor Web (FQDN) |
| Localização da aplicação | Onde está localizado o servidor web ou de farm na sua infraestrutura |
| Acesso interno | O URL exato utilizado ao aceder a aplicação internamente. <br> Se um farm, o tipo de balanceamento de carga está em utilização? <br> Se o aplicativo desenha o conteúdo de origens diferentes em si.<br> Determine se o aplicativo opera sobre WebSockets. |
| Acesso externo | A solução de fornecedor que o aplicativo já está exposto à externamente. <br> O URL que pretende utilizar para acesso externo. Se o SharePoint, certifique-se de que os mapeamentos de acesso alternativo são configurados por [esta orientação](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings). Caso contrário, terá de definir URLs externos. |
| Certificado público | Se utilizar um domínio personalizado, obtenha um certificado com um nome de requerente correspondente. Se existir um certificado tenha em atenção o número de série e o local de onde podem ser obtido. |
| Tipo de autenticação| O tipo de autenticação suportado com o suporte de aplicação, como Basic, autenticação de integração do Windows, com base em formulários, com base no cabeçalho e afirmações. <br>Se a aplicação está configurada para ser executado sob uma conta de domínio específico, tenha em atenção o nome de domínio completamente qualificado (FQDN) da conta de serviço.<br> Se baseado em SAML, os URLs do identificador e resposta. <br> Se baseado em cabeçalho, escreva a solução de fornecedor e o requisito específico para lidar com autenticação. |
| Nome de grupo do conector | O nome lógico para o grupo de conectores que será designado para fornecer a conexão e o SSO para esta aplicação de back-end. |
| Acesso de utilizadores/grupos | Os utilizadores ou grupos de utilizadores que serão concedidos acesso externo à aplicação. |
| Requisitos adicionais | Tenha em atenção a qualquer acesso remoto adicional ou requisitos de segurança que devem ser decompostos em publicação da aplicação. |

É possível transferir este [folha de cálculo de inventário de aplicativo](https://aka.ms/appdiscovery) para Inventariar as suas aplicações.

### <a name="define-organizational-requirements"></a>Definir os requisitos da organização

Seguem-se as áreas para o qual deve definir os requisitos de negócios da sua organização. Cada área contém exemplos de requisitos

 **Acesso**

* Domínio e utilizadores do Azure AD podem aceder às aplicações publicadas com segurança com o simples início de sessão único (SSO) quando os dispositivos associados no usando qualquer associados a um domínio ou do Azure AD.

* Os utilizadores com dispositivos pessoais aprovados podem aceder em segurança a aplicações publicadas desde que eles estão inscritos no MFA e registou a aplicação Microsoft Authenticator no seu telemóvel como método de autenticação.

**Governação** 

* Os administradores possam definir e monitorizar o ciclo de vida de atribuições de utilizador para aplicações publicadas através do Proxy de aplicações.

**Segurança**

* Apenas os utilizadores atribuídos aos aplicativos por meio de associação de grupo ou individualmente, podem acessar esses aplicativos.

**Performance** (Desempenho)

* Não há nenhuma degradação de desempenho do aplicativo em comparação comparada acesso à aplicação a partir da rede interna.

**Experiência de utilizador**

* Os utilizadores têm conhecimento de como acessar seus aplicativos, utilizando URLs de empresa familiar em qualquer plataforma de dispositivo.

**Auditoria**
* Os administradores são capazes de auditar a atividade de acesso do utilizador.


### <a name="best-practices-for-a-pilot"></a>Melhores práticas para um piloto

Determine a quantidade de tempo e esforço necessários para commission totalmente um único aplicativo para acesso remoto com o início de sessão único (SSO). Para tal, executar um piloto que considera a deteção inicial, publicação e teste geral. Através de uma aplicação web simples baseado no IIS, que já está pré-configurado para autenticação de integrada Windows (IWA) poderiam ajudar a estabelecer uma linha de base, como esta configuração requer um mínimo de esforço para acesso remoto com êxito de piloto e SSO.

Os seguintes elementos de design devem aumentar o sucesso da sua implementação piloto diretamente de um inquilino de produção.  

**Gestão do conector**:  

* Conectores exercem um papel fundamental no fornecimento de tornaram às suas aplicações no local. Utilizar o **predefinido** grupo do conector é adequado para piloto testes iniciais de aplicações publicadas antes comissionamento-los em produção. Aplicativos testados com êxito, em seguida, podem ser movidos para os grupos de conectores de produção.

**Gestão de aplicações**:

* Sua força de trabalho é mais provável que não se esqueça de que um URL externo lhe sejam familiares e relevantes. Evite a publicação da sua aplicação com o nosso sufixos msappproxy.net ou onmicrosoft.com predefinidos. Em vez disso, forneça um familiar verificado domínio de nível superior, com um nome de anfitrião de lógica, como o prefixo *intranet. < customers_domain > .com*.

* Restringir a visibilidade do ícone do aplicativo piloto para um grupo piloto, ocultando a seu formato de ícone de lançamento do portal do Azure MyApps. Quando estiver pronto para produção pode definir o âmbito de aplicação para seu respectivo público alvo, no mesmo inquilino de pré-produção ou ao também publicar a aplicação no seu inquilino de produção.

**Configurações de logon de único**: Algumas definições de SSO têm dependências específicas que podem demorar tempo a configurar, por isso, evite atrasos ao garantir que as dependências são resolvidos antes do tempo de controle de alterações. Isto inclui os anfitriões de conector para executar o SSO usando Kerberos Constrained Delegation (KCD) e responsável pelo outras atividades demoradas de adesão ao domínio. Configurar uma instância de acesso de PING, por exemplo, se precisar de SSO com base no cabeçalho.

**SSL entre o anfitrião do conector e o aplicativo de destino**: A segurança é fundamental, para que o TLS entre as aplicações de anfitrião e o destino de conector devem ser usados. Especialmente se o aplicativo web está configurado para autenticação baseada em formulários (FBA), como credenciais de utilizador, em seguida, são efetivamente transmitidas em texto não encriptado.

**Implementar de forma incremental e a testar cada passo**. Realize testes funcionais básicos depois de publicar uma aplicação para se certificar de que todos os requisitos de utilizador e à empresa são cumpridos, seguindo as instruções abaixo:

1. Testar e validar o acesso geral para a aplicação web com a pré-autenticação desativada.
2. Se tiver êxito ative pré-autenticação e atribuir utilizadores e grupos. Testar e validar o acesso.
3. Em seguida, adicione o método SSO para a sua aplicação e testar novamente para validar o acesso.
4. Aplicam-se de que o acesso condicional e políticas MFA conforme necessário. Testar e validar o acesso.

**Ferramentas de resolução de problemas**: Ao resolver problemas, sempre comece por validar o acesso para a aplicação publicada a partir do browser no anfitrião do conector e confirme que o aplicativo funciona conforme esperado. O mais simples a configuração, mais fácil para determinar a causa de raiz, por isso considere tentar reproduzir problemas com uma configuração mínima, como o uso de um único conector e nenhum SSO. Em alguns casos, a depuração ferramentas como o Fiddler da Telerik web pode ser indispensável para resolver problemas de acesso ou o conteúdo em aplicativos acessados por meio de um proxy. Fiddler pode também agir como um proxy para ajudar a rastreamento e depuração de tráfego para plataformas móveis como iOS e Android, e praticamente qualquer coisa que pode ser configurada para encaminhar através de um proxy. Consulte a [guia de resolução de problemas](/application-proxy-troubleshoot.md) para obter mais informações.

## <a name="implement-your-solution"></a>Implementar a sua solução

### <a name="deploy-application-proxy"></a>Implementar o Proxy de aplicações

Os passos para implementar o Proxy de aplicações são abordados nesta [para adicionar uma aplicação no local para acesso remoto](application-proxy-add-on-premises-application.md). Se a instalação não for bem-sucedida, selecione **resolver problemas relacionados com o Proxy de aplicações** no portal ou utilize o guia de resolução de problemas[surgirem problemas de instalar o conector de agente de Proxy de aplicação](application-proxy-connector-installation-problem.md).

### <a name="publish-applications-via-application-proxy"></a>Publicar aplicações através do Proxy de aplicações

Publicação de aplicações parte do princípio de que cumpriu todos os pré-requisitos e de que tem vários conectores que mostra como registrado e Active Directory na página do Proxy de aplicações.

Também pode publicar aplicações utilizando [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview).

Seguem-se algumas das melhores práticas a seguir ao publicar uma aplicação:

* **Utilizar grupos de Conetor**: Atribua um grupo de conectores que foi designado para a publicação de cada respetiva aplicação.

* **Definir o tempo limite da aplicação de back-end**: Esta definição é útil em cenários em que o aplicativo pode necessitar de mais de 75 segundos processar uma transação do cliente. Por exemplo quando um cliente envia uma consulta para um aplicativo web que atua como um front-end para uma base de dados. O front-end envia esta consulta para o respetivo servidor de base de dados de back-end e aguarda uma resposta, mas quando que for recebida uma resposta, o lado do cliente da conversa exceder o tempo limite. Definir o tempo limite como há muito tempo oferece para transações mais de 180 segundos para concluir.

* **Usar tipos de Cookie apropriado**

   * **Apenas HTTP Cookie**: Fornece segurança adicional, fazendo com que o Proxy de aplicações incluem o sinalizador de HTTPOnly em cabeçalhos de resposta HTTP de set-cookie. Esta definição ajuda a mitigar as explorações, como a criação de scripts entre sites (XSS). Deixe esta definida como não para os agentes de clientes/utilizadores que necessitam de acesso para o cookie de sessão. Por exemplo, o cliente RDP/MTSC ligar a um Gateway de ambiente de trabalho remoto publicadas através do Proxy de aplicações.

   * **Proteger o Cookie**: Quando um cookie é definido com o atributo seguro, o agente de utilizador (aplicação do lado do cliente) apenas incluirá o cookie em pedidos de HTTP se o pedido é transmitido através de um canal seguro TLS. Isto ajuda a mitigar o risco de um cookie que está a ser comprometido através de canais de texto não encriptado, pelo que deve ser ativada.

   * **Cookie persistente**: Permite que o cookie de sessão do Proxy de aplicações manter entre fechamentos de navegador pelo restante válido até expirar ou for eliminado. Utilizado para cenários em que um aplicativo avançado, como o office acessa um documento dentro de uma aplicação web publicada, sem que o utilizador voltar a ser pedido a autenticação. Ativar com cuidado, no entanto, como cookies persistentes pode, por fim, deixe um serviço em risco de acesso não autorizado, se não utilizado em conjunto com outros Controles de compensação. Esta definição só deve ser utilizada para aplicativos mais antigos que não é possível partilhar cookies entre processos. É melhor atualizar a sua aplicação para processar cookies partilha entre processos em vez de utilizar esta definição.

* **Traduzir URLs em cabeçalhos**: Ativar esta opção para cenários em que o DNS interno não pode ser configurado para corresponder ao espaço de nomes pública da organização (também conhecidas como DNS de divisão). A menos que seu aplicativo requer o cabeçalho de anfitrião original no pedido do cliente, deixe este valor definido como Yes. A alternativa é fazer com o conector, utilize o FQDN no URL interno para o encaminhamento do tráfego real e o FQDN no URL externo, como o cabeçalho de anfitrião. Na maioria dos casos essa alternativa deve permitir que a aplicação a funcionar normalmente, quando acessado remotamente, mas os seus utilizadores perdem os benefícios de ter uma correspondência dentro e fora da URL.

* **Traduzir URLs no corpo do aplicativo**: Ative a conversão de link de corpo de aplicação para uma aplicação quando pretender que as ligações a partir dessa aplicação para ser convertido em respostas de volta ao cliente. Se estiver ativada, esta função fornece uma tentativa de esforço melhor de todas as ligações internas do Proxy de aplicação encontra-se em respostas HTML e CSS a ser retornadas para os clientes a tradução. É útil quando publicar aplicações que contenham hard-coded absoluto ou NetBIOS shortname links no conteúdo ou aplicações com o conteúdo que liga a outras aplicações no local.

Para cenários onde uma ligações de aplicações publicadas para si aplicações publicadas, ative a conversão de link ou cada aplicativo, para que tem controle sobre a experiência do usuário no nível por aplicação.

Por exemplo, suponha que tem três aplicações publicadas através do Proxy de aplicações que todos ligar entre si: Benefícios, despesas e viagens, além de uma aplicação de quarta, comentários, que não não publicado através do Proxy de aplicações.

![Imagem 1](media/App-proxy-deployment-plan/link-translation.png)

Ao ativar a conversão de link para a aplicação de benefícios, as ligações para as despesas e viagens são redirecionadas para os URLs externos para essas aplicações, para que os utilizadores que acedem as aplicações de fora da rede empresarial podem acessá-los. Links de despesas e viagem para a benefícios não funcionam porque a conversão de link ainda não foi ativada para essas duas aplicações. A ligação para comentários não seja redirecionada porque não existe nenhuma URL externo, para que os utilizadores a utilizar a aplicação de benefícios não vai conseguir aceder à aplicação de comentários a partir de fora da rede empresarial. Ver informações detalhadas sobre [ligar a tradução e outras opções de redirecionamento](application-proxy-configure-hard-coded-link-translation.md).

### <a name="access-your-application"></a>Aceder à sua aplicação

Existem várias opções para gerir o acesso ao Proxy de aplicação recursos publicados, pelo que deve escolher o mais adequado às suas necessidades de determinado cenário e a escalabilidade. Abordagens comuns incluem: utilizar grupos no local que está a ser sincronizados através do Azure AD Connect, criar grupos dinâmicos no Azure AD com base nos atributos de utilizador, através de grupos Self-serviços que são geridos por um proprietário do recurso ou uma combinação de todos estes. Consulte os recursos ligados para obter os benefícios de cada.

A forma mais direta de atribuir utilizadores acesso a uma aplicação vai para o **utilizadores e grupos** opções no painel esquerdo de seu aplicativo publicado e atribuir diretamente grupos ou indivíduos.

![Imagem 24](media/App-proxy-deployment-plan/add-user.png)

Também pode permitir aos utilizadores acesso self-service para a sua aplicação ao atribuir um grupo que não são atualmente membro de e configurar as opções de Self-Service.

![25 de imagem](media/App-proxy-deployment-plan/allow-access.png)

Se estiver ativada, os utilizadores, em seguida, serão podiam acessar o portal e pedir o acesso My Apps e ser automática aprovado e adicionado à grupos de self-service já permitidos ou a necessidade de aprovação de um aprovador designado.

Também podem ser utilizadores convidados [convidado para aceder a aplicações internas publicadas através do Proxy de aplicações através do Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker).

Para aplicações no local que são normalmente acessíveis anonimamente, que exigem nenhuma autenticação, pode desativar a opção localizada do aplicativo **propriedades**.

![26 de imagem](media/App-proxy-deployment-plan/assignment-required.png)


Deixar esta opção definida como não permite aos utilizadores aceder à aplicação no local através do Proxy de aplicação do Azure AD sem permissões, por isso, utilize com cuidado.

Assim que a sua aplicação for publicada, deve ser acessível, escrevendo o respetivo URL externo num navegador ou por seu ícone na [ https://myapps.microsoft.com ](https://myapps.microsoft.com/).

### <a name="enable-pre-authentication"></a>Ativar pré-autenticação

Certifique-se de que a sua aplicação está acessível através do Proxy de aplicações. 

1. Navegue para **do Azure Active Directory** > **aplicações empresariais** > **todas as aplicações** e escolha a aplicação que pretende gerir.

2. Selecione **Proxy de aplicações**.

3. Na **pré-autenticação** campo, utilize a lista pendente para selecionar **Azure Active Directory**e selecione **guardar**.

Com a pré-autenticação ativada, o Azure AD irá desafiá-lo para a autenticação e, em seguida, a aplicação de back-end deve também desafiá-lo se precisar de autenticação. Alterar a pré-autenticação de Passthrough para o Azure AD também configura o URL externo com HTTPS, para que qualquer aplicativo inicialmente configurado para HTTP irá agora ser protegido por HTTPS.

### <a name="enable-single-sign-on"></a>Ativar o início de sessão único

SSO fornece a melhor experiência de utilizador possível e a segurança, pois os usuários só precisam de iniciar sessão uma vez ao aceder ao Azure AD. Assim que um utilizador tenha pré-autenticado, SSO é executado a autenticação de conector de Proxy de aplicações para a aplicação no local, em nome do utilizador. A aplicação de back-end processa o início de sessão como se fosse o utilizador propriamente ditas. 

Escolher o **pass-through** opção permite aos utilizadores aceder à aplicação publicada sem ter de se autenticar com o Azure AD.

Realizar SSO apenas é possível que se do Azure AD pode identificar o utilizador pedir acesso a um recurso, para que seu aplicativo deve ser configurado para autenticar previamente os utilizadores durante o acesso para SSO para a função, caso contrário, as opções de SSO serão desativadas.

Leia [início de sessão único para aplicações no Azure AD](what-is-single-sign-on.md) para ajudar a escolher o método mais apropriado de SSO quando configurar as suas aplicações.

###  <a name="working-with-other-types-of-applications"></a>Trabalhar com outros tipos de aplicativos

Proxy de aplicações do Azure AD também pode oferecer suporte a aplicativos que foram desenvolvidos para utilizar a nossa biblioteca de autenticação do Azure AD ([ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)) ou Microsoft Authentication Library ([MSAL](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/)). Suporta aplicações de cliente nativo, o consumo do Azure AD emitido tokens recebidos nas informações de cabeçalho do pedido de cliente para executar a pré-autenticação em nome dos utilizadores.

Leia [publicação de aplicações de cliente nativo e dispositivos móveis](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client) e [aplicativos baseados em declarações](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps) para saber mais sobre as configurações disponíveis do Proxy de aplicações.

### <a name="use-conditional-access-to-strengthen-security"></a>Utilizar o acesso condicional para reforçar a segurança

Segurança de aplicação requer um conjunto avançado de capacidades de segurança que pode proteger contra e responder a complexas ameaças no local e na cloud. Os atacantes com mais freqüência obtém acesso à rede empresarial através de fracas, predefinidas ou as credenciais de utilizador roubado.  Segurança condicionada por identidade do Microsoft reduz a utilização de credenciais roubadas e protegendo as identidades privilegiadas e sem privilégios.

As seguintes capacidades podem ser utilizadas para suportar o Proxy de aplicações do Azure AD:

* Utilizador e o acesso condicional com base na localização: Manter os dados confidenciais protegidos ao limitar o acesso de utilizador com base na localização geográfica ou um endereço IP com [políticas de acesso condicional com base na localização](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

* Acesso condicional com base no dispositivo: Certifique-se de que apenas os dispositivos inscritos, aprovados e em conformidade podem aceder a dados empresariais com [acesso condicional com base no dispositivo](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications).

* Acesso condicional baseado na aplicação: Trabalho não tem de parar quando um utilizador não está na rede empresarial. [Proteger o acesso a aplicações empresariais de cloud e no local](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) e manter o controle com o acesso condicional.

* Acesso condicional baseado no risco: Proteger os dados de hackers mal-intencionados com um [política de acesso condicional baseado no risco](https://www.microsoft.com/cloud-platform/conditional-access) que podem ser aplicadas a todas as aplicações e todos os utilizadores, quer no local ou na cloud.

* Painel de aplicações do Azure AD: Com o seu serviço de Proxy de aplicações implementadas e aplicações publicadas com segurança, proporcionar aos usuários um hub simple para detetar e aceder a todos os seus aplicativos. Aumentar a produtividade com capacidades self-service, como a capacidade de solicitar acesso a aplicações de novas e grupos ou gerir o acesso a estes recursos em nome de outros, através da [painel de acesso](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>Gerir a sua implementação

### <a name="required-roles"></a>Funções necessárias

Microsoft defende o princípio de conceder os menores privilégios possíveis para efetuar tarefas necessárias com o Azure AD. [Reveja as diferentes funções do Azure que estão disponíveis](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) e escolher a ferramenta correta para atender às necessidades de cada pessoa. Algumas funções poderão ter de ser aplicadas temporariamente e removidos depois de concluída a implementação.

| Função de negócio| Tarefas de negócios| Funções do Azure AD |
|---|---|---|
| Ajudar o administrador de suporte técnico | Normalmente, limitadas para qualificar utilizador final reportados problemas e efetuar tarefas limitadas, como alterar as senhas de usuários, invalidando tokens de atualização e monitorização do Estado de funcionamento do serviço. | Administrador de Suporte Técnico |
| Administrador de identidade| Problemas relacionados com a sessão de AD Azure de leitura em relatórios e os registos de auditoria para depurar o Proxy de aplicações.| Leitor de segurança |
| Proprietário da aplicação| Criar e gerir todos os aspetos de aplicações empresariais, registos de aplicação e as definições de proxy da aplicação.| Administrador da aplicação |
| Administrador de infraestrutura | Proprietário de Rollover de certificado | Administrador da aplicação |

Minimizar o número de pessoas que têm acesso para proteger informações ou recursos o ajudará a reduzir a possibilidade de um ator mal-intencionados de obter acesso não autorizado ou um utilizador autorizado inadvertidamente afetar um recurso confidencial. 
 
No entanto, os utilizadores ainda precisam de realizar operações privilegiadas do dia a dia, portanto, impondo just-in-time (JIT) com base [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) políticas para fornecer a pedido com acesso privilegiado ao recursos do Azure e o Azure AD é nossa recomendado abordagem para gerir o acesso administrativo e auditoria com eficiência.

### <a name="reporting-and-monitoring"></a>Relatórios e monitorização

Azure AD pode fornecer informações adicionais sobre a utilização e estado de funcionamento operacional através de registos de auditoria e relatórios de provisionamento de usuários de sua organização. 

#### <a name="application-audit-logs"></a>Registos de auditoria de aplicação

Estes registos detalham inícios de sessão para aplicações configuradas com o Proxy de aplicações, bem como informações sobre o dispositivo e utilizador acesso à aplicação. Eles estão localizados no portal do Azure e na API de auditoria.

#### <a name="windows-event-logs-and-performance-counters"></a>Registos de eventos do Windows e de contadores de desempenho

Conectores têm admin e sessão de registos. Os registos de administrador incluem eventos principais e os erros. Os registos de sessão incluem todas as transações e os respetivos detalhes de processamento. Os registos e contadores estão localizados em registos de eventos do Windows e siga isso [tutorial para configurar origens de dados de registo de eventos no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events).

### <a name="troubleshooting-guide-and-steps"></a>Guia de resolução de problemas e os passos

Saiba mais sobre problemas comuns e como resolvê-los com o nosso guia para [resolução de problemas](application-proxy-troubleshoot.md) mensagens de erro. 

Estes artigos abrangem os cenários comuns, mas também pode criar suas próprias guias de resolução de problemas para a sua organização de suporte. 

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
* [Não é possível aceder este erro de aplicação empresarial](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Problema ao instalar o Conector do Agente do Proxy de Aplicações](application-proxy-connector-installation-problem.md)
* [Problema de início de sessão](application-sign-in-problem-on-premises-application-proxy.md)
