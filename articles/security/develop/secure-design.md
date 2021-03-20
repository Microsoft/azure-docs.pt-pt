---
title: Design aplicações seguras no Microsoft Azure
description: Este artigo discute as melhores práticas a ter em conta durante as fases de requisito e design do seu projeto de aplicação web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 743412b7602e5781911cdf190e41a5ee15bfddd4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96487682"
---
# <a name="design-secure-applications-on-azure"></a>Design aplicações seguras em Azure
Neste artigo apresentamos atividades de segurança e controlos a ter em conta quando desenha aplicações para a nuvem. São abrangidos os recursos de formação, juntamente com questões de segurança e conceitos a ter em conta durante as fases de requisitos e de conceção do Ciclo de Vida para o Desenvolvimento de Segurança da Microsoft [(SDL).](/previous-versions/windows/desktop/cc307891(v=msdn.10)) O objetivo é ajudá-lo a definir atividades e serviços Azure que você pode usar para desenhar uma aplicação mais segura.

Neste artigo, são abrangidas as seguintes fases SDL:

- Formação
- Requisitos
- Design

## <a name="training"></a>Formação
Antes de começar a desenvolver a sua aplicação em nuvem, tenha tempo para entender a segurança e privacidade no Azure. Ao dar este passo, pode reduzir o número e a gravidade das vulnerabilidades exploráveis na sua aplicação. Estará mais preparado para reagir adequadamente à paisagem de ameaças em constante mudança.

Utilize os seguintes recursos durante a fase de formação para se familiarizar com os serviços Azure que estão disponíveis para os desenvolvedores e com as melhores práticas de segurança em Azure:

  - [O guia do desenvolvedor para a Azure](https://azure.microsoft.com/campaigns/developer-guide/) mostra-lhe como começar com o Azure. O guia mostra-lhe quais os serviços que pode utilizar para executar as suas aplicações, armazenar os seus dados, incorporar inteligência, construir aplicações IoT e implementar as suas soluções de forma mais eficiente e segura.

  - [O guia de arranque para os desenvolvedores do Azure](../../guides/developer/azure-developer-guide.md) fornece informações essenciais para os desenvolvedores que procuram começar a usar a plataforma Azure para as suas necessidades de desenvolvimento.

  - [SDKs e ferramentas](../../index.yml?pivot=sdkstools) descrevem as ferramentas disponíveis no Azure.

  - [A Azure DevOps Services](/azure/devops/) fornece ferramentas de colaboração de desenvolvimento. As ferramentas incluem oleodutos de alto desempenho, repositórios de Git gratuitos, placas kanban configuráveis e testes de carga automatizados e baseados em nuvem.
    O [DevOps Resource Center](/azure/devops/learn/) combina os nossos recursos para aprender práticas de DevOps, controlo de versão Git, métodos ágeis, como trabalhamos com DevOps na Microsoft, e como você pode avaliar a sua própria progressão de DevOps.

  - [Os 5 principais itens de segurança a ter em conta antes de pressionar a produção](/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) mostram-lhe como ajudar a proteger as suas aplicações web no Azure e proteger as suas aplicações contra os ataques de aplicações web mais comuns e perigosos.

  - [Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) é uma coleção de scripts, ferramentas, extensões e automações que atendem às necessidades abrangentes de subscrição e segurança de recursos das equipas de DevOps que utilizam uma automatização extensiva. O Secure DevOps Kit for Azure pode mostrar-lhe como integrar a segurança suavemente nos seus fluxos de trabalho nativos de DevOps. O kit aborda ferramentas como testes de verificação de segurança (SVTs), que podem ajudar os desenvolvedores a escrever código seguro e testar a configuração segura das suas aplicações em nuvem nas fases de codificação e desenvolvimento precoce.

  - [As melhores práticas de segurança para soluções Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) fornecem uma coleção de boas práticas de segurança para usar à medida que projeta, implementa e gere as suas soluções em nuvem utilizando o Azure.

## <a name="requirements"></a>Requisitos
A fase de definição de requisitos é um passo crucial na definição do que é a sua aplicação e do que fará quando for lançada. A fase de requisitos é também um momento para pensar nos controlos de segurança que irás construir na tua aplicação. Durante esta fase, também inicia os passos que irá tomar ao longo do SDL para garantir que liberta e implementa uma aplicação segura.

### <a name="consider-security-and-privacy-issues"></a>Considere questões de segurança e privacidade
Esta fase é a melhor altura para considerar questões fundamentais de segurança e privacidade. Definir níveis aceitáveis de segurança e privacidade no início de um projeto ajuda uma equipa:

- Compreender os riscos associados a questões de segurança.
- Identifique e corrija bugs de segurança durante o desenvolvimento.
- Aplicar níveis estabelecidos de segurança e privacidade em todo o projeto.

Ao escrever os requisitos para a sua aplicação, não se esqueça de considerar controlos de segurança que possam ajudar a manter a sua aplicação e dados seguros.

### <a name="ask-security-questions"></a>Faça perguntas de segurança
Faça perguntas de segurança como:

  - A minha aplicação contém dados sensíveis?

  - A minha aplicação recolhe ou armazena dados que me obrigam a aderir aos padrões do setor e a programas de conformidade como o [Federal Financial Institution Examination Council (FFIEC)](/previous-versions/azure/security/blueprints/ffiec-analytics-overview) ou o Payment Card Industry Data Security Standards [(PCI DSS)](/previous-versions/azure/security/blueprints/pcidss-analytics-overview)?

  - A minha aplicação recolhe ou contém dados pessoais ou de clientes sensíveis que podem ser utilizados, por si só ou com outras informações, para identificar, contactar ou localizar uma única pessoa?

  - A minha candidatura recolhe ou contém dados que podem ser utilizados para aceder à informação médica, educacional, financeira ou de emprego de um indivíduo? Identificar a sensibilidade dos seus dados durante a fase de requisitos ajuda-o a classificar os seus dados e a identificar o método de proteção de dados que utilizará para a sua aplicação.

  - Onde e como são armazenados os meus dados? Considere como irá monitorizar os serviços de armazenamento que a sua aplicação utiliza para quaisquer alterações inesperadas (como tempos de resposta mais lentos). Poderá influenciar o registo para recolher dados mais detalhados e analisar um problema em profundidade?

  - A minha candidatura estará disponível para o público (na internet) ou apenas internamente? Se a sua aplicação está disponível para o público, como proteger os dados que podem ser recolhidos de serem utilizados de forma errada? Se a sua aplicação estiver disponível internamente apenas, considere quem na sua organização deve ter acesso à aplicação e quanto tempo deverá ter acesso.

  - Compreende o seu modelo de identidade antes de começar a desenhar a sua candidatura? Como vai determinar que os utilizadores são quem dizem ser e o que um utilizador está autorizado a fazer?

  - A minha aplicação executa tarefas sensíveis ou importantes (como transferir dinheiro, abrir portas ou entregar medicamentos)?
    Considere como irá validar que o utilizador que executa uma tarefa sensível está autorizado a executar a tarefa e como autenticará que a pessoa é quem diz ser. Autorização (AuthZ) é o ato de conceder a um principal de segurança autenticado permissão para fazer algo. A autenticação (AuthN) é o ato de desafiar uma parte por credenciais legítimas.

  - A minha aplicação realiza alguma atividade de software de risco, como permitir que os utilizadores carreguem ou descarreguem ficheiros ou outros dados? Se a sua aplicação realizar atividades de risco, considere como a sua aplicação irá proteger os utilizadores de lidar com ficheiros ou dados maliciosos.

### <a name="review-owasp-top-10"></a>Rever o top 10 do OWASP
Considere rever os [<span class="underline">10 principais riscos de segurança da aplicação da OWASP.</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
O OWASP Top 10 aborda riscos críticos de segurança para aplicações web.
A consciência destes riscos de segurança pode ajudá-lo a tomar decisões exigentes e de design que minimizem estes riscos na sua aplicação.

É importante pensar nos controlos de segurança para evitar falhas.
No entanto, também pretende assumir que irá ocorrer [uma violação.](/azure/devops/learn/devops-at-microsoft/security-in-devops) Assumindo que uma violação ajuda a responder a algumas questões importantes sobre segurança com antecedência, para que não tenham de ser respondidas numa emergência:

  - Como vou detetar um ataque?

  - O que vou fazer se houver um ataque ou uma violação?

  - Como vou recuperar do ataque como fugas de dados ou adulteração?

## <a name="design"></a>Design

A fase de conceção é fundamental para o estabelecimento de boas práticas para design e especificações funcionais. Também é fundamental para a realização de análises de risco que ajudam a mitigar questões de segurança e privacidade ao longo de um projeto.

Quando tiver os requisitos de segurança em vigor e utilizar conceitos de design seguros, pode evitar ou minimizar oportunidades para uma falha de segurança. Uma falha de segurança é um descuido no design da aplicação que pode permitir que um utilizador execute ações maliciosas ou inesperadas após o lançamento da sua aplicação.

Durante a fase de design, pense também em como pode aplicar segurança em camadas; um nível de defesa não é necessariamente suficiente. O que acontece se um intruso passar pela firewall da sua aplicação web (WAF)? Queres outro controlo de segurança para te defenderes do ataque.

Com isto em mente, discutimos os seguintes conceitos de design seguro e os controlos de segurança que deve abordar quando conceber aplicações seguras:

- Utilize uma biblioteca de codificação segura e uma estrutura de software.
- Procure por componentes vulneráveis.
- Utilize modelação de ameaças durante o design da aplicação.
- Reduza a sua superfície de ataque.
- Adote uma política de identidade como o perímetro de segurança primário.
- Requerem a reautensão para transações importantes.
- Use uma solução de gestão chave para proteger chaves, credenciais e outros segredos.
- Proteja os dados sensíveis.
- Implementar medidas de segurança.
- Aproveite o erro e o tratamento de exceções.
- Utilize o registo e o alerta.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Use uma biblioteca de codificação segura e uma estrutura de software

Para o desenvolvimento, utilize uma biblioteca de codificação segura e uma estrutura de software que tenha incorporado a segurança. Os desenvolvedores podem usar funcionalidades existentes e comprovadas (encriptação, saneamento de entrada, codificação de saída, chaves ou cordas de conexão, e qualquer outra coisa que seja considerado um controlo de segurança) em vez de desenvolver controlos de segurança a partir do zero. Isto ajuda a proteger contra falhas de design e implementação relacionadas com a segurança.

Certifique-se de que está a usar a versão mais recente da sua estrutura e todas as funcionalidades de segurança que estão disponíveis no quadro. A Microsoft oferece um conjunto abrangente [de ferramentas de desenvolvimento](https://azure.microsoft.com/product-categories/developer-tools/) para todos os desenvolvedores, trabalhando em qualquer plataforma ou idioma, para fornecer aplicações na nuvem. Pode codificar com o idioma à sua escolha, escolhendo entre [vários SDKs.](https://azure.microsoft.com/downloads/)
Você pode aproveitar ambientes de desenvolvimento integrados completos (IDEs) e editores que têm capacidades avançadas de depuração e suporte Azure incorporado.

A Microsoft oferece uma variedade de [idiomas, quadros e ferramentas](../../index.yml?panel=sdkstools-all&pivot=sdkstools) que pode usar para desenvolver aplicações no Azure. Um exemplo é [Azure para desenvolvedores .NET e .NET Core](/dotnet/azure/). Para cada idioma e enquadramento que oferecemos, você encontrará quickstarts, tutoriais e referências API para ajudá-lo a começar rapidamente.

O Azure oferece uma variedade de serviços que pode usar para hospedar websites e aplicações web. Estes serviços permitem-lhe desenvolver na sua língua preferida, seja .NET, .NET Core, Java, Ruby, Node.js, PHP ou Python.
[Azure App Service Web Apps](../../app-service/overview.md) (Web Apps) é um destes serviços.

Web Apps adiciona o poder do Microsoft Azure à sua aplicação. Inclui segurança, equilíbrio de carga, autoscalagem e gestão automatizada. Você também pode aproveitar as capacidades de DevOps em Aplicações Web, como gestão de pacotes, ambientes de encenação, domínios personalizados, certificados SSL/TLS, e implementação contínua de Azure DevOps, GitHub, Docker Hub, e outras fontes.

O Azure oferece outros serviços que pode utilizar para hospedar websites e aplicações web. Para a maioria dos cenários, as Web Apps são a melhor escolha. Para uma arquitetura de micro serviço, considere [a Azure Service Fabric.](https://azure.microsoft.com/documentation/services/service-fabric)
Se precisar de mais controlo sobre as VMs em que o seu código é executado, considere as [Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/).
Para obter mais informações sobre como escolher entre estes serviços Azure, consulte uma [comparação entre o Azure App Service, Máquinas Virtuais, Tecido de Serviço e Serviços cloud.](/azure/architecture/guide/technology-choices/compute-decision-tree)

### <a name="apply-updates-to-components"></a>Aplicar atualizações aos componentes

Para evitar vulnerabilidades, deve inventariar continuamente os componentes do lado do cliente e do servidor (por exemplo, quadros e bibliotecas) e as suas dependências para atualizações. Novas vulnerabilidades e versões de software atualizadas são lançadas continuamente. Certifique-se de que tem um plano em curso para monitorizar, triagem e aplicar atualizações ou alterações de configuração nas bibliotecas e componentes que utiliza.

Consulte a página [open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) sobre a [utilização de componentes com vulnerabilidades conhecidas](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) para sugestões de ferramentas. Também pode subscrever alertas de email para vulnerabilidades de segurança que estejam relacionadas com os componentes que utiliza.

### <a name="use-threat-modeling-during-application-design"></a>Use modelação de ameaças durante o design da aplicação

A modelação de ameaças é o processo de identificação de potenciais ameaças à segurança para o seu negócio e aplicação, e, em seguida, garantir que estão em vigor as devidas mitigações. O SDL especifica que as equipas devem empenhar-se na modelação de ameaças durante a fase de conceção, quando a resolução de potenciais problemas é relativamente fácil e rentável. A utilização de modelos de ameaças na fase de design pode reduzir consideravelmente o seu custo total de desenvolvimento.

Para facilitar o processo de modelação de ameaças, concebemos a [Ferramenta de Modelação de Ameaças SDL](threat-modeling-tool.md) com especialistas em não segurança em mente. Esta ferramenta facilita a modelação de ameaças para todos os desenvolvedores, fornecendo orientações claras sobre como criar e analisar modelos de ameaças.

Modelar o design da aplicação e enumerar ameaças [de STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) — Falsificação, Adulteração, Repudiação, Divulgação de Informação, Negação de Serviço e Elevação de Privilégios — através de todos os limites de confiança provou ser uma forma eficaz de apanhar erros de design desde cedo. A tabela que se segue lista as ameaças stride e dá alguns exemplos de mitigação que usam funcionalidades fornecidas pela Azure. Estas atenuações não vão funcionar em todas as situações.

| Ameaça | Propriedade de segurança | Potencial atenuação da plataforma Azure |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Spoofing               | Autenticação        | [Requerem ligações HTTPS](/aspnet/core/security/enforcing-ssl?tabs=visual-studio&view=aspnetcore-2.1). |
| Adulteração              | Integridade             | Validar certificados SSL/TLS. As aplicações que utilizam o SSL/TLS devem verificar plenamente os certificados X.509 das entidades a que se ligam. Utilize certificados Azure Key Vault para [gerir os seus certificados x509](../../key-vault/general/about-keys-secrets-certificates.md). |
| Rejeição            | Não rejeição       | Ativar [a monitorização e diagnósticos do](/azure/architecture/best-practices/monitoring)Azure .|
| Divulgação de Informações | Confidencialidade       | Criptografe dados sensíveis [em repouso](../fundamentals/encryption-atrest.md) e [em trânsito.](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) |
| Negação de Serviço      | Disponibilidade          | Monitorize as métricas de desempenho para potenciais condições de negação de serviço. Implementar filtros de ligação. [A proteção Azure DDoS,](../../ddos-protection/ddos-protection-overview.md#next-steps)combinada com as melhores práticas de design de aplicações, fornece defesa contra ataques DDoS.|
| Elevação de Privilégios | Autorização         | Utilizar a Azure <span class="underline"></span> Ative Directory [Gestão de Identidade Privilegiada](../../active-directory/privileged-identity-management/pim-configure.md).|

### <a name="reduce-your-attack-surface"></a>Reduza a sua superfície de ataque

Uma superfície de ataque é a soma total de onde podem ocorrer potenciais vulnerabilidades. Neste artigo, focamo-nos na superfície de ataque de uma aplicação.
O foco está em proteger uma aplicação de ataque. Uma forma simples e rápida de minimizar a sua superfície de ataque é remover recursos e códigos não reutilizados da sua aplicação. Quanto menor a sua aplicação, menor a sua superfície de ataque. Por exemplo, remover:

- Código para funcionalidades que ainda não lançou.
- Depurando código de apoio.
- Interfaces de rede e protocolos que não são usados ou que foram depreciados.
- Máquinas virtuais e outros recursos que não está a usar.

Fazer uma limpeza regular dos seus recursos e garantir que remove o código não-uso são ótimas formas de garantir que há menos oportunidades para os atores mal-intencionados atacarem.

Uma forma mais detalhada e aprofundada de reduzir a sua superfície de ataque é completar uma análise de superfície de ataque. Uma análise de superfície de ataque ajuda-o a mapear as partes de um sistema que precisa de ser revisto e testado para vulnerabilidades de segurança.

O objetivo de uma análise de superfície de ataque é compreender as áreas de risco numa aplicação para que os desenvolvedores e especialistas em segurança estejam cientes de que partes da aplicação estão abertas a atacar. Depois, pode encontrar formas de minimizar este potencial, rastrear quando e como a superfície de ataque muda, e o que isso significa de uma perspetiva de risco.

Uma análise de superfície de ataque ajuda-o a identificar:

- Funções e partes do sistema que precisa de rever e testar vulnerabilidades de segurança.
- Áreas de código de alto risco que requerem proteção aprofundada da defesa (partes do sistema que precisa de defender).
- Quando alterar a superfície de ataque e precisar de refrescar uma avaliação de ameaça.

Reduzir oportunidades para os atacantes explorarem um potencial ponto fraco ou vulnerabilidade requer que você analise cuidadosamente a superfície de ataque geral da sua aplicação. Inclui também a desativação ou restrição do acesso aos serviços de sistema, a aplicação do princípio do menor privilégio e a utilização de defesas em camadas sempre que possível.

Discutimos [a realização de uma revisão](secure-develop.md#conduct-attack-surface-review) da superfície de ataque durante a fase de verificação do SDL.

> [!NOTE]
> **Qual é a diferença entre modelar ameaças e analisar superfícies de ataque?**
A modelação de ameaças é o processo de identificação de potenciais ameaças à segurança à sua aplicação e de garantir que estão em vigor as devidas mitigações contra as ameaças. A análise da superfície de ataque identifica áreas de código de alto risco que estão abertas a ataques. Envolve encontrar formas de defender áreas de alto risco da sua aplicação e rever e testar essas áreas de código antes de implementar a aplicação.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Adotar uma política de identidade como o perímetro de segurança primário

Quando desenha aplicações em nuvem, é importante expandir o foco do seu perímetro de segurança de uma abordagem centrada na rede para uma abordagem centrada na identidade. Historicamente, o perímetro de segurança primário no local era uma rede de organização. A maioria dos designs de segurança no local usam a rede como o pivô de segurança primário. Para aplicações em nuvem, você é melhor servido considerando a identidade como o perímetro de segurança principal.

Coisas que pode fazer para desenvolver uma abordagem centrada na identidade para desenvolver aplicações web:

- Impor a autenticação de vários fatores para os utilizadores.
- Utilize plataformas de autenticação e autorização fortes.
- Aplique o princípio do menor privilégio.
- Implementar acesso just-in-time.

#### <a name="enforce-multi-factor-authentication-for-users"></a>Impor a autenticação de vários fatores para os utilizadores

Utilize a autenticação de dois fatores. A autenticação de dois fatores é a norma atual para a autenticação e autorização, pois evita as deficiências de segurança inerentes ao nome de utilizador e tipos de senha de autenticação. O acesso às interfaces de gestão Azure (portal Azure/remote PowerShell) e aos serviços virados para o cliente deve ser concebido e configurado para utilizar a [Autenticação Multi-Factor Azure](../../active-directory/authentication/concept-mfa-howitworks.md).

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Utilize plataformas fortes de autenticação e autorização

Utilize mecanismos de autenticação e autorização fornecidos pela plataforma em vez de código personalizado. Isto porque o desenvolvimento de código de autenticação personalizado pode ser propenso a erros. O código comercial (por exemplo, da Microsoft) é frequentemente revisto por segurança. [O Azure Ative Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) é a solução Azure para a gestão de identidade e acesso. Estas ferramentas e serviços AD AZure ajudam no desenvolvimento seguro:

- [A plataforma de identidade da Microsoft](../../active-directory/develop/index.yml) é um conjunto de componentes que os desenvolvedores usam para construir aplicações que assinam de forma segura nos utilizadores. A plataforma ajuda os desenvolvedores que estão a construir aplicações de inquilinos individuais, linha de negócios (LOB) e desenvolvedores que procuram desenvolver aplicações multi-arrendatários. Além do teste básico de sismo, as aplicações construídas através da plataforma de identidade da Microsoft podem ligar para apis da Microsoft e APIs personalizados. A plataforma de identidade da Microsoft suporta protocolos padrão da indústria como OAuth 2.0 e OpenID Connect.

- [O Azure Ative Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml) é um serviço de gestão de identidade que pode utilizar para personalizar e controlar a forma como os clientes se inscrevem, se inscrevem e gerem os seus perfis quando utilizam as suas aplicações. Isto inclui aplicações que são desenvolvidas para iOS, Android e .NET, entre outras. O Azure AD B2C permite estas ações ao mesmo tempo que protege as identidades dos clientes.

#### <a name="apply-the-principle-of-least-privilege"></a>Aplicar o princípio do menor privilégio

O conceito de [menor privilégio](https://en.wikipedia.org/wiki/Principle_of_least_privilege) significa dar aos utilizadores o nível preciso de acesso e controlo de que necessitam para fazerem o seu trabalho e nada mais.

Um desenvolvedor de software precisaria de direitos de administração de domínio? Um assistente administrativo precisaria de acesso aos controlos administrativos no seu computador pessoal? Avaliar o acesso ao software não é diferente. Se utilizar o [controlo de acesso baseado em funções (Azure RBAC)](../../role-based-access-control/overview.md) para dar aos utilizadores diferentes habilidades e autoridade na sua aplicação, não daria a todos acesso a tudo. Ao limitar o acesso ao que é necessário para cada função, limita-se o risco de ocorrência de um problema de segurança.

Certifique-se de que a sua aplicação impõe [menos privilégios](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) ao longo dos seus padrões de acesso.

> [!NOTE]
> As regras de menor privilégio têm de se aplicar ao software e às pessoas que criam o software. Os desenvolvedores de software podem ser um grande risco para a segurança de TI se lhes for dado demasiado acesso. As consequências podem ser severas se um desenvolvedor tiver intenções maliciosas ou tiver demasiado acesso. Recomendamos que as regras de menor privilégio sejam aplicadas aos desenvolvedores ao longo do ciclo de vida do desenvolvimento.

#### <a name="implement-just-in-time-access"></a>Implementar acesso just-in-time

Implementar o acesso *just-in-time* (JIT) para reduzir ainda mais o tempo de exposição dos privilégios. Utilize [a Azure AD Gestão de Identidade Privilegiada](../../active-directory/roles/security-planning.md#stage-3-take-control-of-admin-activity) para:

- Dê aos utilizadores as permissões de que precisam apenas de JIT.
- Atribua funções por uma duração reduzida com confiança de que os privilégios são revogados automaticamente.

### <a name="require-re-authentication-for-important-transactions"></a>Exigir a reautensão para transações importantes

[A falsificação de pedidos de cross-site](/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (também conhecida como *XSRF* ou *CSRF*) é um ataque contra aplicações hospedadas na Web em que uma aplicação web maliciosa influencia a interação entre um navegador de cliente e uma aplicação web que confia nesse navegador. Os ataques de falsificação de pedidos de sites são possíveis porque os navegadores da Web enviam automaticamente alguns tipos de fichas de autenticação a cada pedido para um website.
Esta forma de exploração também é conhecida como um *ataque de um clique* ou uma *sessão de equitação* porque o ataque aproveita a sessão anteriormente autenticada do utilizador.

A melhor forma de se defender deste tipo de ataque é pedir ao utilizador algo que só o utilizador pode fornecer antes de cada transação importante, como uma compra, desativação de conta ou uma alteração de senha. Pode pedir ao utilizador para reentrar na sua palavra-passe, completar um captcha ou enviar um sinal secreto que só o utilizador teria. A abordagem mais comum é o símbolo secreto.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Use uma solução de gestão chave para garantir chaves, credenciais e outros segredos

Perder chaves e credenciais é um problema comum. A única coisa pior do que perder as chaves e as credenciais é ter uma parte não autorizada a ter acesso a elas. Os atacantes podem aproveitar técnicas automatizadas e manuais para encontrar chaves e segredos que são armazenados em repositórios de código como o GitHub. Não coloque chaves e segredos nestes repositórios de código público ou em qualquer outro servidor.

Coloque sempre as suas chaves, certificados, segredos e fios de ligação numa solução de gestão chave. Pode utilizar uma solução centralizada na qual as chaves e os segredos são armazenados em módulos de segurança de hardware (HSMs). O Azure fornece-lhe um HSM na nuvem com cofre [de chaves Azure](../../key-vault/general/overview.md).

Key Vault é uma *loja secreta:* é um serviço de nuvem centralizado para armazenar segredos de aplicações. O Key Vault mantém os seus dados confidenciais seguros, mantendo os segredos da aplicação num único local central e fornecendo acesso seguro, controlo de permissões e acesso ao registo.

Os segredos estão guardados em *cofres* individuais. Cada cofre tem as suas próprias políticas de configuração e segurança para controlar o acesso. Você chega aos seus dados através de uma API REST ou através de um cliente SDK que está disponível para a maioria das linguagens de programação.

> [!IMPORTANT]
> O Azure Key Vault foi concebido para armazenar segredos de configuração para aplicações de servidores. Não se destina a armazenar dados que pertençam a utilizadores de aplicações. Tal reflete-se nas suas características de desempenho, na API e no modelo de custos.
>
> Os dados do utilizador devem ser armazenados noutro local, como numa instância da Base de Dados Azure SQL que tenha encriptação de dados transparente (TDE) ou numa conta de armazenamento que utilize encriptação do Serviço de Armazenamento Azure. Os segredos que são usados pela sua aplicação para aceder a estas lojas de dados podem ser guardados no Cofre da Chave Azure.

### <a name="protect-sensitive-data"></a>Proteger dados sensíveis

Proteger dados é uma parte essencial da sua estratégia de segurança.
Classificar os seus dados e identificar as suas necessidades de proteção de dados ajuda-o a conceber a sua aplicação com a segurança dos dados em mente. Classificar (categorizar) dados armazenados por sensibilidade e impacto de negócio ajuda os desenvolvedores a determinar os riscos associados aos dados.

Rotular todos os dados aplicáveis como sensíveis quando conceber os seus formatos de dados. Certifique-se de que a aplicação trata os dados aplicáveis como sensíveis. Estas práticas podem ajudá-lo a proteger os seus dados sensíveis:

- Use encriptação.
- Evite segredos de codificação rígido, como chaves e senhas.
- Certifique-se de que os controlos de acesso e a auditoria estão em vigor.

#### <a name="use-encryption"></a>Use encriptação

Proteger os dados deve ser uma parte essencial da sua estratégia de segurança.
Se os seus dados forem armazenados numa base de dados ou se se deslocar para trás e para a frente entre locais, utilize a encriptação de [dados em repouso](../fundamentals/encryption-atrest.md) (enquanto estiver na base de [dados)](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) e a encriptação de dados em trânsito (a caminho de e para o utilizador, a base de dados, uma API ou ponto final de serviço). Recomendamos que utilize sempre os protocolos SSL/TLS para a troca de dados. Certifique-se de que utiliza a versão mais recente do TLS para encriptação (atualmente, esta é a versão 1.2).

#### <a name="avoid-hard-coding"></a>Evite codificação dura

Algumas coisas nunca devem ser codificadas no seu software. Alguns exemplos são nomes de anfitriões ou endereços IP, URLs, endereços de e-mail, nomes de utilizador, palavras-passe, chaves de conta de armazenamento e outras teclas criptográficas. Considere os requisitos de implementação em torno do que pode ou não ser codificado no seu código, incluindo nas secções de comentários do seu código.

Quando colocar comentários no seu código, certifique-se de que não guarda nenhuma informação sensível. Isto inclui o seu endereço de e-mail, palavras-passe, cadeias de ligação, informações sobre a sua aplicação que só seriam conhecidas por alguém da sua organização, e qualquer outra coisa que possa dar a um intruso uma vantagem no ataque à sua aplicação ou organização.

Basicamente, assuma que tudo no seu projeto de desenvolvimento será do conhecimento público quando for implementado. Evite incluir dados sensíveis de qualquer tipo no projeto.

Mais cedo, discutimos [o Azure Key Vault.](../../key-vault/general/overview.md) Pode usar o Key Vault para armazenar segredos como chaves e senhas em vez de os codificar. Quando utiliza o Key Vault em combinação com identidades geridas para recursos Azure, a sua aplicação web Azure pode aceder aos valores de configuração secreta de forma fácil e segura sem armazenar segredos no seu controlo de origem ou configuração. Para saber mais, consulte [Gerir segredos nas aplicações do seu servidor com o Azure Key Vault](/learn/modules/manage-secrets-with-azure-key-vault/).

### <a name="implement-fail-safe-measures"></a>Implementar medidas de segurança contra falhas

A sua aplicação deve ser capaz de lidar com [erros](/dotnet/standard/exceptions/) que ocorram durante a execução de forma consistente. A aplicação deve apanhar todos os erros e falhar em segurança ou fechada.

Também deve certificar-se de que os erros são registados com um contexto de utilizador suficiente para identificar atividades suspeitas ou maliciosas. Os registos devem ser conservados por um tempo suficiente para permitir análises forenses atrasadas. Os registos devem estar num formato que pode ser facilmente consumido por uma solução de gestão de registos. Certifique-se de que são desencadeados alertas para erros relacionados com a segurança. A falta de registo e monitorização permite que os atacantes ataquem mais sistemas e mantenham a persistência.

### <a name="take-advantage-of-error-and-exception-handling"></a>Aproveite o tratamento de erros e de exceções

A implementação de erros corretos e [o manuseamento](/dotnet/standard/exceptions/best-practices-for-exceptions) de exceções é uma parte importante da codificação defensiva. O tratamento de erros e exceções é fundamental para tornar um sistema fiável e seguro. Erros no manuseamento de erros podem levar a diferentes tipos de vulnerabilidades de segurança, como a fuga de informação aos atacantes e ajudar os atacantes a entender mais sobre a sua plataforma e design.

Certifique-se de que:

- Lida com exceções de forma centralizada para evitar blocos de [tentativa/captura](/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) duplicados no código.

- Todos os comportamentos inesperados são tratados dentro da aplicação.

- As mensagens que são apresentadas aos utilizadores não divulgam dados críticos, mas fornecem informações suficientes para explicar o problema.

- As exceções são registadas e que fornecem informações suficientes para que as equipas forenses ou de resposta a incidentes investiguem.

[A Azure Logic Apps](../../logic-apps/logic-apps-overview.md) fornece uma experiência de primeira classe para [lidar com erros e exceções](../../logic-apps/logic-apps-exception-handling.md) que são causadas por sistemas dependentes. Pode utilizar as Aplicações Lógicas para criar fluxos de trabalho para automatizar tarefas e processos que integrem apps, dados, sistemas e serviços entre empresas e organizações.

### <a name="use-logging-and-alerting"></a>Utilizar registo e alerta

[Informe](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) os seus problemas de segurança para investigações de segurança e desencadeie alertas sobre questões para garantir que as pessoas saibam dos problemas em tempo útil. Ativar a auditoria e o registo de todos os componentes. Os registos de auditoria devem capturar o contexto do utilizador e identificar todos os eventos importantes.

Verifique se não regista quaisquer dados sensíveis que um utilizador submeta ao seu site. Exemplos de dados sensíveis incluem:

- Credenciais de utilizador
- Números da Segurança Social ou outras informações identificantes
- Números de cartões de crédito ou outras informações financeiras
- Informações de saúde
- Chaves privadas ou outros dados que podem ser usados para desencriptar informações encriptadas
- Informações de sistema ou aplicações que podem ser usadas para atacar mais eficazmente a aplicação

Certifique-se de que a aplicação monitoriza eventos de gestão de utilizadores, tais como logins de utilizador bem sucedidos e falhados, resets de palavra-passe, alterações de passwords, bloqueio de conta e registo do utilizador. Registar estes eventos ajuda a detetar e a reagir a comportamentos potencialmente suspeitos. Também permite recolher dados de operações, como quem está a aceder à aplicação.

## <a name="next-steps"></a>Passos seguintes
Nos seguintes artigos, recomendamos controlos de segurança e atividades que possam ajudá-lo a desenvolver e implementar aplicações seguras.

- [Desenvolver aplicações seguras](secure-develop.md)
- [Implementar aplicações seguras](secure-deploy.md)