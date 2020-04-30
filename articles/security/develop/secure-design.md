---
title: Aplicações seguras de design no Microsoft Azure
description: Este artigo discute as melhores práticas a considerar durante as fases de exigência e conceção do seu projeto de aplicação web.
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
ms.openlocfilehash: c9e3cfa689f2e528f4d20e796017ae9d91c29fe2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81461723"
---
# <a name="design-secure-applications-on-azure"></a>Aplicações seguras de design no Azure
Neste artigo apresentamos atividades e controlos de segurança a considerar quando projeta aplicações para a nuvem. Os recursos de formação, juntamente com questões de segurança e conceitos a considerar durante os requisitos e fases de design do Microsoft [Security Development Lifecycle (SDL).](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) O objetivo é ajudá-lo a definir atividades e serviços Azure que você pode usar para projetar uma aplicação mais segura.

As seguintes fases SDL são abrangidas neste artigo:

- Formação
- Requisitos
- Design

## <a name="training"></a>Formação
Antes de começar a desenvolver a sua aplicação na nuvem, tenha tempo para entender a segurança e privacidade no Azure. Ao dar este passo, pode reduzir o número e a gravidade das vulnerabilidades exploráveis na sua aplicação. Estará mais preparado para reagir adequadamente ao cenário de ameaça em constante mudança.

Utilize os seguintes recursos durante a fase de formação para se familiarizar com os serviços Azure que estão disponíveis para os desenvolvedores e com as melhores práticas de segurança no Azure:

  - [O guia do desenvolvedor para o Azure](https://azure.microsoft.com/campaigns/developer-guide/) mostra-lhe como começar com o Azure. O guia mostra quais os serviços que pode utilizar para executar as suas aplicações, armazenar os seus dados, incorporar inteligência, construir aplicações IoT e implementar as suas soluções de forma mais eficiente e segura.

  - [O Get Start Guide para os desenvolvedores](../../guides/developer/azure-developer-guide.md) do Azure fornece informações essenciais para os desenvolvedores que procuram começar a usar a plataforma Azure para as suas necessidades de desenvolvimento.

  - [Os SDKs e as ferramentas](https://docs.microsoft.com/azure/index?pivot=sdkstools) descrevem as ferramentas disponíveis no Azure.

  - [A Azure DevOps Services](https://docs.microsoft.com/azure/devops/) fornece ferramentas de colaboração para o desenvolvimento. As ferramentas incluem oleodutos de alto desempenho, repositórios Git gratuitos, placas Kanban configuráveis e testes extensivos de carga automatizados e baseados na nuvem.
    O [DevOps Resource Center](https://docs.microsoft.com/azure/devops/learn/) combina os nossos recursos para aprender práticas de DevOps, controlo de versão Git, métodos ágeis, como trabalhamos com devOps na Microsoft, e como pode avaliar a sua própria progressão de DevOps.

  - [Os 5 principais itens](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) de segurança a considerar antes de avançar para a produção mostram-lhe como ajudar a proteger as suas aplicações web no Azure e proteger as suas aplicações contra os ataques de aplicações web mais comuns e perigosos.

  - [Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) é uma coleção de scripts, ferramentas, extensões e automações que atende às necessidades abrangentes de subscrição e segurança de recursos azure das equipas da DevOps que utilizam uma automação extensiva. O Kit Secure DevOps para azure pode mostrar-lhe como integrar a segurança suavemente nos seus fluxos de trabalho nativos de DevOps. O kit aborda ferramentas como testes de verificação de segurança (SVTs), que podem ajudar os desenvolvedores a escrever código seguro e testar a configuração segura das suas aplicações em nuvem nas fases de codificação e desenvolvimento precoce.

  - [As melhores práticas de segurança para soluções Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) fornecem uma coleção de boas práticas de segurança para usar como projetar, implementar e gerir as suas soluções em nuvem utilizando o Azure.

## <a name="requirements"></a>Requisitos
A fase de definição de requisitos é um passo crucial na definição do que é a sua aplicação e o que fará quando for lançada. A fase de requisitos é também um momento para pensar nos controlos de segurança que irá desenvolver na sua aplicação. Durante esta fase, também inicia os passos que irá tomar ao longo do SDL para garantir que liberta e implementa uma aplicação segura.

### <a name="consider-security-and-privacy-issues"></a>Considere questões de segurança e privacidade
Esta fase é a melhor altura para considerar questões fundamentais de segurança e privacidade. Definir níveis aceitáveis de segurança e privacidade no início de um projeto ajuda uma equipa:

- Compreender os riscos associados a questões de segurança.
- Identifique e corrija bugs de segurança durante o desenvolvimento.
- Aplicar níveis estabelecidos de segurança e privacidade em todo o projeto.

Ao escrever os requisitos para a sua aplicação, certifique-se de considerar os controlos de segurança que podem ajudar a manter a sua aplicação e dados seguros.

### <a name="ask-security-questions"></a>Faça perguntas de segurança
Faça perguntas de segurança como:

  - A minha aplicação contém dados sensíveis?

  - A minha aplicação recolhe ou armazena dados que me obrigam a aderir aos padrões do setor e programas de conformidade como o [Federal Financial Institution Examination Council (FFIEC)](../blueprints/ffiec-analytics-overview.md) ou as Normas de Segurança de Dados da Indústria de Cartões de Pagamento [(PCI DSS)](../blueprints/pcidss-analytics-overview.md)?

  - A minha aplicação recolhe ou contém dados pessoais ou clientes sensíveis que podem ser utilizados, por si só ou com outras informações, para identificar, contactar ou localizar uma única pessoa?

  - A minha candidatura recolhe ou contém dados que podem ser usados para aceder à informação médica, educacional, financeira ou de emprego de um indivíduo? Identificar a sensibilidade dos seus dados durante a fase de requisitos ajuda-o a classificar os seus dados e a identificar o método de proteção de dados que utilizará para a sua aplicação.

  - Onde e como os meus dados são armazenados? Considere como irá monitorizar os serviços de armazenamento que a sua aplicação utiliza para quaisquer alterações inesperadas (como tempos de resposta mais lentos). Poderá influenciar a exploração madeireira para recolher dados mais detalhados e analisar um problema em profundidade?

  - A minha candidatura estará disponível para o público (na internet) ou apenas internamente? Se a sua candidatura está disponível para o público, como protege os dados que podem ser recolhidos de serem usados de forma errada? Se a sua candidatura estiver disponível apenas internamente, considere quem na sua organização deve ter acesso à aplicação e quanto tempo devem ter acesso.

  - Entende o seu modelo de identidade antes de começar a desenhar a sua candidatura? Como vai determinar que os utilizadores são quem dizem ser e o que um utilizador está autorizado a fazer?

  - A minha aplicação realiza tarefas sensíveis ou importantes (como transferir dinheiro, desbloquear portas ou entregar medicamentos)?
    Considere como irá validar que o utilizador que executa uma tarefa sensível está autorizado a executar a tarefa e como irá autenticar que a pessoa é quem diz ser. A autorização (AuthZ) é o ato de conceder uma autorização autenticada para fazer algo. A autenticação (AuthN) é o ato de desafiar um partido para credenciais legítimas.

  - A minha aplicação executa alguma atividade de software arriscada, como permitir que os utilizadores carreguem ou descarreguem ficheiros ou outros dados? Se a sua aplicação realizar atividades de risco, considere como a sua aplicação irá proteger os utilizadores de manipular ficheiros ou dados maliciosos.

### <a name="review-owasp-top-10"></a>Reveja o top 10 da OWASP
Considere rever os riscos de segurança de [<span class="underline">aplicações oWASP Top 10</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).
O Top 10 da OWASP aborda riscos críticos de segurança para aplicações web.
A consciencialização destes riscos de segurança pode ajudá-lo a tomar decisões de exigência e design que minimizem estes riscos na sua aplicação.

Pensar em controlos de segurança para evitar violações é importante.
No entanto, também pretende assumir que ocorrerá [uma violação.](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops) Assumindo que uma violação ajuda a responder a algumas questões importantes sobre segurança antecipadamente, para que não tenham de ser respondidas numa emergência:

  - Como vou detetar um ataque?

  - O que vou fazer se houver um ataque ou uma violação?

  - Como vou recuperar do ataque como fugas de dados ou adulteração?

## <a name="design"></a>Design

A fase de conceção é fundamental para o estabelecimento de boas práticas para o design e especificações funcionais. Também é fundamental para a realização de análises de risco que ajudam a mitigar questões de segurança e privacidade ao longo de um projeto.

Quando tiver requisitos de segurança e utilizar conceitos de design seguros, pode evitar ou minimizar oportunidades para uma falha de segurança. Uma falha de segurança é um descuido no design da aplicação que pode permitir que um utilizador realize ações maliciosas ou inesperadas após a sua aplicação ser lançada.

Durante a fase de design, pense também em como pode aplicar segurança em camadas; um nível de defesa não é necessariamente suficiente. O que acontece se um intruso passar pela firewall da sua aplicação web (WAF)? Quer outro controlo de segurança para se defender do ataque.

Com isto em mente, discutimos os seguintes conceitos de design seguros e os controlos de segurança que deve abordar quando conceber aplicações seguras:

- Use uma biblioteca de codificação segura e uma estrutura de software.
- Procure componentes vulneráveis.
- Utilize modelação de ameaça durante o design da aplicação.
- Reduza a sua superfície de ataque.
- Adote uma política de identidade como o perímetro de segurança primário.
- Exigir a reautenticação para transações importantes.
- Utilize uma solução de gestão chave para proteger chaves, credenciais e outros segredos.
- Proteja dados sensíveis.
- Implementar medidas de segurança.
- Aproveite o erro e o manuseamento de exceções.
- Utilize o registo e o alerta.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Use uma biblioteca de codificação segura e uma estrutura de software

Para o desenvolvimento, utilize uma biblioteca de codificação segura e um quadro de software que tenha incorporado a segurança. Os desenvolvedores podem usar funcionalidades comprovadas e existentes (encriptação, saneamento de entrada, codificação de saída, chaves ou cordas de ligação, e qualquer outra coisa que seja considerada um controlo de segurança) em vez de desenvolver controlos de segurança do zero. Isto ajuda a proteger contra falhas de design e implementação relacionadas com a segurança.

Certifique-se de que está a usar a versão mais recente da sua estrutura e todas as funcionalidades de segurança disponíveis no quadro. A Microsoft oferece um conjunto abrangente [de ferramentas de desenvolvimento](https://azure.microsoft.com/product-categories/developer-tools/) para todos os desenvolvedores, trabalhando em qualquer plataforma ou idioma, para fornecer aplicações na nuvem. Pode codificar com a linguagem à sua escolha escolhendo entre [vários SDKs](https://azure.microsoft.com/downloads/).
Você pode aproveitar os ambientes de desenvolvimento integrados completos (IDEs) e editores que têm capacidades avançadas de depuração e suporte azure incorporado.

A Microsoft oferece uma variedade de [idiomas, quadros e ferramentas](https://docs.microsoft.com/azure/index?pivot=sdkstools&panel=sdkstools-all) que pode usar para desenvolver aplicações no Azure. Um exemplo é [o Azure para desenvolvedores .NET e .NET Core](https://docs.microsoft.com/dotnet/azure/). Para cada linguagem e enquadramento que oferecemos, você encontrará quickstarts, tutoriais e referências API para ajudá-lo a começar rapidamente.

O Azure oferece uma variedade de serviços que pode utilizar para hospedar websites e aplicações web. Estes serviços permitem-lhe desenvolver-se na sua língua preferida, seja .NET, .NET Core, Java, Ruby, Node.js, PHP ou Python.
[As Aplicações Web do Azure App Service](../../app-service/overview.md) (Web Apps) é um desses serviços.

As Web Apps adicionam a potência do Microsoft Azure à sua aplicação. Inclui segurança, equilíbrio de carga, autoscalcificação e gestão automatizada. Também pode aproveitar as capacidades da DevOps em Aplicações Web, como gestão de pacotes, ambientes de encenação, domínios personalizados, certificados SSL/TLS e implantação contínua de Azure DevOps, GitHub, Docker Hub e outras fontes.

O Azure oferece outros serviços que pode utilizar para hospedar websites e aplicações web. Para a maioria dos cenários, as Web Apps são a melhor escolha. Para uma arquitetura de micro serviço, considere [Azure Service Fabric.](https://azure.microsoft.com/documentation/services/service-fabric)
Se precisar de mais controlo sobre as VMs em que o seu código é executado, considere as [Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/).
Para obter mais informações sobre como escolher entre estes serviços Azure, consulte uma comparação entre estes serviços [Azure App Service, Virtual Machines, Service Fabric e Cloud Services](/azure/architecture/guide/technology-choices/compute-decision-tree).

### <a name="apply-updates-to-components"></a>Aplicar atualizações aos componentes

Para evitar vulnerabilidades, deve fazer um inventário contínuo tanto dos componentes do lado do cliente como do servidor (por exemplo, quadros e bibliotecas) e suas dependências para atualizações. Novas vulnerabilidades e versões de software atualizadas são lançadas continuamente. Certifique-se de que tem um plano em curso para monitorizar, triagem e aplicar atualizações ou alterações de configuração nas bibliotecas e componentes que utiliza.

Consulte a página [Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) sobre a utilização de componentes com [vulnerabilidades conhecidas](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) para sugestões de ferramentas. Também pode subscrever alertas de e-mail para vulnerabilidades de segurança relacionadas com componentes que utiliza.

### <a name="use-threat-modeling-during-application-design"></a>Utilize modelação de ameaça durante o design da aplicação

A modelação de ameaças é o processo de identificação de potenciais ameaças à segurança para o seu negócio e aplicação, e, em seguida, garantir que estão em vigor mitigações adequadas. O SDL especifica que as equipas devem envolver-se em modelação de ameaças durante a fase de conceção, quando a resolução de potenciais problemas é relativamente fácil e rentável. A utilização de modelação de ameaças na fase de design pode reduzir consideravelmente o seu custo total de desenvolvimento.

Para ajudar a facilitar o processo de modelação de ameaças, desenhamos a Ferramenta de Modelação de [Ameaças SDL](threat-modeling-tool.md) com especialistas não-de segurança em mente. Esta ferramenta facilita a modelação de ameaças para todos os desenvolvedores, fornecendo orientações claras sobre como criar e analisar modelos de ameaça.

Modelar o design da aplicação e enumerar ameaças [stride](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) — Falsificação, Adulteração, Repúdio, Divulgação de Informação, Negação de Serviço e Elevação de Privilégios — através de todos os limites de confiança, provou ser uma forma eficaz de apanhar erros de design desde cedo. A tabela seguinte enumera as ameaças do STRIDE e dá alguns exemplos de atenuações que utilizam as funcionalidades fornecidas pelo Azure. Estas mitigações não funcionarão em todas as situações.

| Ameaça | Propriedade de segurança | Potencial mitigação da plataforma Azure |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Spoofing               | Autenticação        | [Requerer ligações HTTPS](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio). |
| Adulteração              | Integridade             | Valide os certificados SSL/TLS. As aplicações que utilizam o SSL/TLS devem verificar plenamente os certificados X.509 das entidades a que se ligam. Utilize certificados Azure Key Vault para [gerir os seus certificados x509](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-certificates). |
| Repúdio            | Não-repústação       | Ativar [a monitorização e diagnóstico](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)do Azure.|
| Divulgação de Informação | Confidencialidade       | Criptografe dados sensíveis [em repouso](../fundamentals/encryption-atrest.md) e [em trânsito.](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) |
| Negação de Serviço      | Disponibilidade          | Monitorize as métricas de desempenho para potenciais condições de negação das condições de serviço. Implementar filtros de ligação. [A proteção Azure DDoS,](../../virtual-network/ddos-protection-overview.md#next-steps)combinada com as melhores práticas de design de aplicações, fornece defesa contra ataques DDoS.|
| Elevação do Privilégio | Autorização         | Utilize <span class="underline"> </span> [a Gestão](../../active-directory/privileged-identity-management/pim-configure.md)de Identidade Privilegiada do Diretório Ativo Azure.|

### <a name="reduce-your-attack-surface"></a>Reduza a sua superfície de ataque

Uma superfície de ataque é a soma total de onde podem ocorrer potenciais vulnerabilidades. Neste artigo, focamo-nos na superfície de ataque de uma aplicação.
O foco está em proteger uma aplicação de ataque. Uma forma simples e rápida de minimizar a sua superfície de ataque é remover recursos e código não utilizados da sua aplicação. Quanto menor for a tua aplicação, menor é a tua superfície de ataque. Por exemplo, remova:

- Código para funcionalidades que ainda não lançou.
- Depurando o código de apoio.
- Interfaces de rede e protocolos que não são usados ou que tenham sido depreciados.
- Máquinas virtuais e outros recursos que não estás a usar.

Fazer limpeza regular dos seus recursos e garantir que remove código não utilizado são ótimas formas de garantir que há menos oportunidades para atores maliciosos atacarem.

Uma forma mais detalhada e aprofundada de reduzir a sua superfície de ataque é completar uma análise superficial de ataque. Uma análise de superfície de ataque ajuda-o a mapear as partes de um sistema que precisa de ser revisto e testado para vulnerabilidades de segurança.

O objetivo de uma análise superficial de ataque é compreender as áreas de risco de uma aplicação para que os desenvolvedores e especialistas em segurança estejam cientes de que partes da aplicação estão abertas a atacar. Depois, pode encontrar formas de minimizar este potencial, rastrear quando e como a superfície de ataque muda, e o que isso significa do ponto de vista do risco.

Uma análise superficial de ataque ajuda-o a identificar:

- Funções e partes do sistema que precisa de rever e testar vulnerabilidades de segurança.
- Áreas de código de alto risco que requerem proteção em profundidade (partes do sistema que precisa de defender).
- Quando alterar a superfície de ataque e precisar de refrescar uma avaliação de ameaça.

Reduzir as oportunidades para os atacantes explorarem um potencial ponto fraco ou vulnerabilidade requer que analise minuciosamente a superfície de ataque global da sua aplicação. Inclui também a desativação ou a restrição do acesso aos serviços do sistema, a aplicação do princípio do menor privilégio e a utilização de defesas em camadas sempre que possível.

Discutimos a realização de [uma revisão](secure-develop.md#conduct-attack-surface-review) da superfície de ataque durante a fase de verificação do SDL.

> [!NOTE]
> **Qual é a diferença entre modelação de ameaças e análise de superfície de ataque?**
A modelação de ameaças é o processo de identificação de potenciais ameaças à segurança da sua aplicação e de garantir que estão em vigor mitigações adequadas contra as ameaças. A análise da superfície de ataque identifica áreas de código de alto risco que estão abertas a ataques. Envolve encontrar formas de defender áreas de alto risco da sua aplicação e rever e testar essas áreas de código antes de implementar a aplicação.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Adotar uma política de identidade como o perímetro de segurança principal

Quando desenha aplicações em nuvem, é importante expandir o foco do seu perímetro de segurança de uma abordagem centrada na rede para uma abordagem centrada na identidade. Historicamente, o perímetro de segurança primário no local era uma rede de uma organização. A maioria dos desenhos de segurança no local usam a rede como o pivô de segurança principal. Para aplicações em nuvem, você é melhor servido considerando a identidade como o perímetro de segurança primário.

Coisas que pode fazer para desenvolver uma abordagem centrada na identidade para desenvolver aplicações web:

- Impor a autenticação de vários fatores para os utilizadores.
- Utilize plataformas de autenticação e autorização fortes.
- Aplicar o princípio do menor privilégio.
- Implementar acesso just-in-time.

#### <a name="enforce-multi-factor-authentication-for-users"></a>Impor a autenticação de vários fatores para os utilizadores

Use a autenticação de dois fatores. A autenticação de dois fatores é a norma atual para a autenticação e autorização porque evita as falhas de segurança inerentes ao nome de utilizador e tipos de autenticação de palavra-passe. O acesso às interfaces de gestão Azure (portal Azure/PowerShell remoto) e aos serviços voltados para o cliente deve ser concebido e configurado para utilizar a [Autenticação Multi-Factor Azure](../../active-directory/authentication/concept-mfa-howitworks.md).

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Utilize plataformas de autenticação e autorização fortes

Utilize mecanismos de autenticação e autorização fornecidos pela plataforma em vez de código personalizado. Isto porque o desenvolvimento do código de autenticação personalizado pode ser propenso a erros. O código comercial (por exemplo, da Microsoft) é frequentemente amplamente revisto para segurança. O [Azure Ative Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) é a solução Azure para a gestão de identidade e acesso. Estas ferramentas e serviços da AD Azure ajudam no desenvolvimento seguro:

- A plataforma de [identidade Azure AD (Azure AD para programadores)](../../active-directory/develop/about-microsoft-identity-platform.md) é um serviço de identidade na nuvem que os desenvolvedores usam para construir aplicações que assinam de forma segura nos utilizadores. A Azure AD auxilia os desenvolvedores que estão a construir aplicações de um único inquilino, linha de negócios (LOB) e desenvolvedores que procuram desenvolver aplicações multi-inquilinos. Além do início de sessão básica, as aplicações que são construídas utilizando o Azure AD podem ligar para as APIs da Microsoft e APIs personalizadas que são construídas na plataforma Azure AD. A plataforma de identidade Azure AD suporta protocolos padrão da indústria como o OAuth 2.0 e o OpenID Connect.

- [O Azure Ative Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml) é um serviço de gestão de identidade que pode utilizar para personalizar e controlar a forma como os clientes se inscrevem, se inscrevem e gerem os seus perfis quando utilizam as suas aplicações. Isto inclui aplicações que são desenvolvidas para iOS, Android e .NET, entre outras. O Azure AD B2C permite estas ações protegendo as identidades dos clientes.

#### <a name="apply-the-principle-of-least-privilege"></a>Aplicar o princípio do menor privilégio

O conceito de [menor privilégio](https://en.wikipedia.org/wiki/Principle_of_least_privilege) significa dar aos utilizadores o nível exato de acesso e controlo de que necessitam para fazerem o seu trabalho e nada mais.

Um desenvolvedor de software precisaria de direitos de administração de domínio? Um assistente administrativo precisaria de acesso a controlos administrativos no seu computador pessoal? Avaliar o acesso ao software não é diferente. Se utilizar o [controlo de acesso baseado em papéis (RBAC)](../../role-based-access-control/overview.md) para dar aos utilizadores diferentes habilidades e autoridade na sua aplicação, não daria a todos acesso a tudo. Ao limitar o acesso ao que é necessário para cada função, limita-se o risco de ocorrência de um problema de segurança.

Certifique-se de que a sua aplicação aplica menos [privilégios](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) em todos os seus padrões de acesso.

> [!NOTE]
>  As regras de menor privilégio têm de se aplicar ao software e às pessoas que criam o software. Os desenvolvedores de software podem ser um grande risco para a segurança de TI se lhes for dado demasiado acesso. As consequências podem ser graves se um desenvolvedor tiver intenções maliciosas ou tiver demasiado acesso. Recomendamos que as regras de menor privilégio sejam aplicadas aos desenvolvedores ao longo do ciclo de vida de desenvolvimento.

#### <a name="implement-just-in-time-access"></a>Implementar acesso just-in-time

Implementar o acesso *just-in-time* (JIT) para reduzir ainda mais o tempo de exposição dos privilégios. Utilize a Gestão de Identidade Privilegiada da [Azure AD](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#stage-3-build-visibility-and-take-full-control-of-admin-activity) para:

- Dê aos utilizadores as permissões de que só precisam de JIT.
- Atribuir funções por uma duração reduzida com confiança de que os privilégios são revogados automaticamente.

### <a name="require-re-authentication-for-important-transactions"></a>Exigir a autenticação para transações importantes

[A falsificação de pedidos de sites transversais](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (também conhecida como *XSRF* ou *CSRF*) é um ataque contra aplicações web hospedadas em que uma aplicação web maliciosa influencia a interação entre um navegador de cliente e uma aplicação web que confia nesse navegador. Os ataques de falsificação de pedidos de sites são possíveis porque os navegadores da Web enviam automaticamente alguns tipos de fichas de autenticação com cada pedido para um website.
Esta forma de exploração também é conhecida como um *ataque de um clique* ou uma sessão de *condução* porque o ataque tira partido da sessão previamente autenticada do utilizador.

A melhor maneira de se defender deste tipo de ataque é pedir ao utilizador algo que só o utilizador pode fornecer antes de cada transação importante, como uma compra, desativação de conta ou uma alteração de senha. Pode pedir ao utilizador que reintroduza a sua palavra-passe, complete uma captcha ou envie um símbolo secreto que apenas o utilizador teria. A abordagem mais comum é o símbolo secreto.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Use uma solução de gestão chave para proteger chaves, credenciais e outros segredos

Perder chaves e credenciais é um problema comum. A única coisa pior do que perder as chaves e as credenciais é ter uma parte não autorizada a ter acesso a elas. Os atacantes podem aproveitar as técnicas automatizadas e manuais para encontrar chaves e segredos que são armazenados em repositórios de código como o GitHub. Não coloque chaves e segredos nestes repositórios de código público ou em qualquer outro servidor.

Coloque sempre as chaves, certificados, segredos e cordas de ligação numa solução de gestão chave. Pode utilizar uma solução centralizada na qual chaves e segredos são armazenados em módulos de segurança de hardware (HSMs). O Azure fornece-lhe um HSM na nuvem com [o Cofre de Chaves Azure.](../../key-vault/general/overview.md)

Key Vault é uma *loja secreta:* é um serviço de nuvem centralizado para armazenar segredos de aplicação. O Key Vault mantém os seus dados confidenciais seguros, mantendo os segredos da aplicação num único local central e fornecendo acesso seguro, controlo de permissões e registo de acesso.

Segredos são guardados em *cofres*individuais. Cada cofre tem a sua própria configuração e políticas de segurança para controlar o acesso. Você chega aos seus dados através de uma API REST ou através de um SDK cliente que está disponível para a maioria dos idiomas de programação.

> [!IMPORTANT]
> O Azure Key Vault foi concebido para armazenar segredos de configuração para aplicações de servidores. Não se destina a armazenar dados que pertençam aos utilizadores de aplicações. Tal reflete-se nas suas características de desempenho, na API e no modelo de custos.
>
> Os dados dos utilizadores devem ser armazenados noutros locais, como numa instância de Base de Dados Azure SQL que tenha encriptação transparente de dados (TDE) ou numa conta de armazenamento que utilize encriptação do Serviço de Armazenamento Azure. Os segredos que são utilizados pela sua aplicação para aceder a estas lojas de dados podem ser guardados no Cofre chave do Azure.

### <a name="protect-sensitive-data"></a>Proteger dados sensíveis

Proteger dados é uma parte essencial da sua estratégia de segurança.
Classificar os seus dados e identificar as suas necessidades de proteção de dados ajuda-o a conceber a sua aplicação com segurança de dados em mente. Classificar (categorizando) os dados armazenados por sensibilidade e impacto empresarial ajuda os desenvolvedores a determinar os riscos associados aos dados.

Rotule todos os dados aplicáveis como sensíveis quando conceber os seus formatos de dados. Certifique-se de que a aplicação trata os dados aplicáveis como sensíveis. Estas práticas podem ajudá-lo a proteger os seus dados sensíveis:

- Use encriptação.
- Evite segredos de codificação rígidos como chaves e senhas.
- Certifique-se de que os controlos de acesso e a auditoria estão em vigor.

#### <a name="use-encryption"></a>Usar encriptação

Proteger dados deve ser uma parte essencial da sua estratégia de segurança.
Se os seus dados forem armazenados numa base de dados ou se se deslocarem para trás e para a frente entre locais, utilize a encriptação de [dados em repouso](../fundamentals/encryption-atrest.md) (enquanto estiver na base de dados) e a encriptação de dados em [trânsito](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) (a caminho de e para o utilizador, a base de dados, uma API ou ponto final do serviço). Recomendamos que utilize sempre protocolos SSL/TLS para trocar dados. Certifique-se de que utiliza a versão mais recente do TLS para encriptação (atualmente, esta é a versão 1.2).

#### <a name="avoid-hard-coding"></a>Evite codificação dura

Algumas coisas nunca devem ser codificadas no seu software. Alguns exemplos são nomes de anfitriões ou endereços IP, URLs, endereços de e-mail, nomes de utilizador, palavras-passe, chaves de conta de armazenamento e outras chaves criptográficas. Considere implementar requisitos em torno do que pode ou não ser codificado no seu código, incluindo nas secções de comentários do seu código.

Quando colocar comentários no seu código, certifique-se de que não guarda nenhuma informação sensível. Isto inclui o seu endereço de e-mail, palavras-passe, cordas de ligação, informações sobre a sua aplicação que só seriam conhecidas por alguém da sua organização, e qualquer outra coisa que possa dar a um intruso uma vantagem em atacar a sua aplicação ou organização.

Basicamente, assuma que tudo no seu projeto de desenvolvimento será do conhecimento público quando for implementado. Evite incluir dados sensíveis de qualquer tipo no projeto.

Há pouco, discutimos [o Cofre chave Azure.](../../key-vault/general/overview.md) Podes usar o Key Vault para guardar segredos como chaves e senhas em vez de os codificares. Quando utiliza o Key Vault em combinação com identidades geridas para recursos Azure, a sua aplicação web Azure pode aceder a valores de configuração secretos de forma fácil e segura sem guardar quaisquer segredos no seu controlo ou configuração de origem. Para saber mais, consulte [Gerir segredos nas suas aplicações de servidor com o Azure Key Vault](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/).

### <a name="implement-fail-safe-measures"></a>Implementar medidas de segurança de falha

A sua aplicação deve ser capaz de lidar com [erros ocorridos](https://docs.microsoft.com/dotnet/standard/exceptions/) durante a execução de forma consistente. A aplicação deve apanhar todos os erros e falhar em segurança ou fechada.

Deve também certificar-se de que os erros são registados com contexto de utilizador suficiente para identificar atividades suspeitas ou maliciosas. Os registos devem ser conservados por um tempo suficiente para permitir análises forenses atrasadas. Os registos devem estar num formato que pode ser facilmente consumido por uma solução de gestão de registos. Certifique-se de que os alertas para erros relacionados com a segurança são desencadeados. A exploração madeireira e monitorização insuficientes permite que os atacantes ataquem mais sistemas e mantenham a persistência.

### <a name="take-advantage-of-error-and-exception-handling"></a>Aproveite o erro e o tratamento de exceção

A implementação de erros corretos e [de manipulação de exceções](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions) é uma parte importante da codificação defensiva. O erro e o manuseamento de exceções são fundamentais para tornar um sistema fiável e seguro. Erros no manuseamento de erros podem levar a diferentes tipos de vulnerabilidades de segurança, tais como fugas de informação aos atacantes e ajudar os atacantes a entender mais sobre a sua plataforma e design.

Certifique-se de que:

- Lida com exceções de forma centralizada para evitar bloqueios duplicados de [tentativa/captura](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) no código.

- Todos os comportamentos inesperados são tratados dentro da aplicação.

- As mensagens que são exibidas aos utilizadores não divulgam dados críticos, mas fornecem informações suficientes para explicar o problema.

- As exceções são registadas e fornecem informações suficientes para que as equipas forenses ou de resposta a incidentes investiguem.

[A Azure Logic Apps](../../logic-apps/logic-apps-overview.md) proporciona uma experiência de primeira classe para [lidar com erros e exceções](../../logic-apps/logic-apps-exception-handling.md) que são causadas por sistemas dependentes. Pode utilizar apps lógicas para criar fluxos de trabalho para automatizar tarefas e processos que integram apps, dados, sistemas e serviços entre empresas e organizações.

### <a name="use-logging-and-alerting"></a>Utilize a exploração madeireira e alerta

[Inicie](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) os seus problemas de segurança para investigações de segurança e desencadeie alertas sobre questões para garantir que as pessoas saibam dos problemas em tempo útil. Ativar a auditoria e o registo em todos os componentes. Os registos de auditoria devem capturar o contexto do utilizador e identificar todos os eventos importantes.

Verifique se não regista quaisquer dados sensíveis que um utilizador submeta ao seu site. Exemplos de dados sensíveis incluem:

- Credenciais de utilizador
- Números da Segurança Social ou outras informações de identificação
- Números de cartão de crédito ou outras informações financeiras
- Informação sobre saúde
- Chaves privadas ou outros dados que podem ser usados para desencriptar informações encriptadas
- Informações do sistema ou aplicação que podem ser usadas para atacar mais eficazmente a aplicação

Certifique-se de que a aplicação monitoriza eventos de gestão de utilizadores, tais como logins de utilizador bem sucedidos e falhados, resets de passwords, alterações de password, bloqueio de conta e registo do utilizador. A exploração de registos para estes eventos ajuda-o a detetar e a reagir a comportamentos potencialmente suspeitos. Também lhe permite recolher dados de operações, como quem está a aceder à aplicação.

## <a name="next-steps"></a>Passos seguintes
Nos seguintes artigos, recomendamos controlos de segurança e atividades que podem ajudá-lo a desenvolver e implementar aplicações seguras.

- [Desenvolver aplicações seguras](secure-develop.md)
- [Implementar aplicações seguras](secure-deploy.md)
