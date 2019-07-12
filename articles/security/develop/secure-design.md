---
title: Conceber aplicações seguras no Microsoft Azure
description: Este artigo discute as melhores práticas a considerar durante as fases de requisito e design de seu projeto de aplicativo web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 12b9793cabb261368c437bd2ae2dbb39cf078bef
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653290"
---
# <a name="design-secure-applications-on-azure"></a>Design aplicações seguras no Azure
Neste artigo, apresentamos as atividades de segurança e controlos a ter em consideração ao conceber aplicações para a cloud. Recursos, juntamente com perguntas de segurança e conceitos para considerar durante os requisitos e design fases do Microsoft de formação [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) são abordados. O objetivo é ajudar a definir as atividades e serviços do Azure que pode usar para projetar um aplicativo mais seguro.

As seguintes fases SDL são abordadas neste artigo:

- Formação
- Requisitos
- Design

## <a name="training"></a>Formação
Antes de começar a desenvolver a sua aplicação na cloud, demorar algum tempo para compreender a segurança e privacidade no Azure. Ao seguir este passo, pode reduzir o número e a gravidade das vulnerabilidades exploráveis em seu aplicativo. Estará mais bem preparado para reagir de forma adequada para o Panorama de ameaças constante mudança.

Utilize os seguintes recursos durante a fase de treinamento para se familiarizar com os serviços do Azure que estão disponíveis para desenvolvedores e com melhores práticas de segurança no Azure:

  - [Guia do programador para o Azure](https://azure.microsoft.com/campaigns/developer-guide/) mostra-lhe como começar com o Azure. O Guia mostra-lhe os serviços que pode utilizar para executar as suas aplicações, armazenar os seus dados, incorporar inteligência, criar aplicações de IoT e implementar as soluções de forma mais eficiente e segura.

  - [Guia de introdução para programadores do Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide) fornece informações essenciais para os desenvolvedores que buscam para começar a utilizar a plataforma do Azure para necessidades de seu desenvolvimento.

  - [SDKs e ferramentas](https://docs.microsoft.com/azure/index#pivot=sdkstools) descreve as ferramentas que estão disponíveis no Azure.

  - [Os serviços do Azure DevOps](https://docs.microsoft.com/azure/devops/) fornece ferramentas de colaboração de desenvolvimento. As ferramentas incluem pipelines de alto desempenho, repositórios de Git gratuitos, quadros de Kanban configuráveis e teste de carga abrangente de automatizado e baseado na nuvem.
    O [central de recursos de DevOps](https://docs.microsoft.com/azure/devops/learn/) combina os nossos recursos de aprendizado do DevOps práticas, Git controle de versão, métodos ágeis, como Trabalhamos com DevOps da Microsoft e como pode avaliar o seu próprio progressão de DevOps.

  - [Top 5 de segurança de itens a considerar antes de enviar para produção](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) mostra-lhe como ajudar a proteger as suas aplicações web no Azure e proteger as suas aplicações contra ataques de aplicativo de web mais comum e perigosas.

  - [Secure DevOps Kit para o Azure](https://azsk.azurewebsites.net/index.html) é uma coleção de scripts, ferramentas, extensões e automatizações de fluxos que atende às abrangente do Azure recursos e subscrição necessidades de segurança das equipas de DevOps que utilizam a automatização extensa. O Secure DevOps Kit para o Azure pode mostram-lhe como integrar perfeitamente segurança em seus fluxos de trabalho de DevOps nativos. O kit aborda ferramentas como testes de verificação de segurança (SVTs), que podem ajudar os desenvolvedores escrevem códigos seguros e testar a configuração segura das suas aplicações na cloud nas fases de desenvolvimento de codificação e antecipada.

  - [Melhores práticas de segurança para soluções do Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) fornece uma coleção de segurança melhores práticas para utilizar como conceber, implementar e gerir soluções na cloud ao utilizar o Azure.

## <a name="requirements"></a>Requisitos
A fase de definição de requisitos é uma etapa crucial para definir o que é o seu aplicativo e o que isso será feito quando ele for lançado. A fase de requisitos é também um tempo para pensar sobre os controlos de segurança que criará na sua aplicação. Durante esta fase, também começar os passos que irá demorar em todo o SDL para garantir que liberar e implementar uma aplicação segura.

### <a name="consider-security-and-privacy-issues"></a>Considere problemas de segurança e privacidade
Nesta fase é o melhor momento para considerar os problemas de privacidade e de segurança básico. Definir níveis aceitáveis de segurança e privacidade no início de um projeto de ajuda uma equipe:

- Compreenda os riscos associados a problemas de segurança.
- Identifique e corrija bugs de segurança durante o desenvolvimento.
- Aplicam-se estabelecidos níveis de segurança e privacidade ao longo de todo o projeto.

Quando escreve os requisitos para a sua aplicação, certifique-se de que considere controlos de segurança que podem ajudar a manter a sua aplicação e os dados protegidos.

### <a name="ask-security-questions"></a>Faça perguntas de segurança
Faça perguntas de segurança, como:

  - O meu aplicativo contém dados confidenciais?

  - O meu aplicativo recolher ou armazenar dados que exige que eu aderir aos padrões da indústria e programas de conformidade, como o [Federal financeiros instituição exame Council (FFIEC)](https://docs.microsoft.com/azure/security/blueprints/ffiec-analytics-overview) ou o [indústria de cartões de pagamento Data Security Standards (DSS da PCI)](https://docs.microsoft.com/azure/security/blueprints/pcidss-analytics-overview)?

  - O meu aplicativo recolher ou contêm dados confidenciais do pessoal ou cliente que podem ser utilizados, por conta própria ou com as outras informações, para identificar, contactar ou localizar uma única pessoa?

  - Meu aplicativo recolher ou conter dados que podem ser usados para acessar o médico de uma pessoa, instituições de ensino, financeiro, ou informações de emprego? Identificando a sensibilidade dos seus dados durante a fase de requisitos ajuda-o a classificar os dados e identificar o método de proteção de dados que irá utilizar para a sua aplicação.

  - Onde e como são armazenados os meus dados? Considere como irá monitorizar os serviços de armazenamento que a aplicação utiliza para efetuar quaisquer alterações inesperadas (por exemplo, tempos de resposta mais lentas). Será capaz de modo a influenciar o registo para recolher dados mais detalhados e analisar um problema em profundidade?

  - Meu aplicativo será disponibilizado ao público (na internet) ou apenas internamente? Se seu aplicativo está disponível ao público, como proteger os dados que podem ser recolhidos de que está a ser usado da forma errada? Se seu aplicativo só está disponível internamente, considere quem na sua organização deve ter acesso à aplicação e o tempo que devem ter acesso.

  - Compreende o modelo de identidade antes de começar a criação do seu aplicativo? Como determinará que os utilizadores são realmente quem afirma estão e quais um utilizador está autorizado a fazer?

  - Meu aplicativo realiza tarefas confidenciais ou importantes (por exemplo, a transferência de dinheiro, desbloquear as portas ou fornecer medicina)?
    Considere como irá validar que o utilizador que executa uma tarefa confidencial está autorizado a executar a tarefa e como irá autenticar a que a pessoa é que eles dizem que o são. Autorização (AuthZ) é o ato de conceder uma permissão de principal de segurança autenticado para fazer algo. A autenticação (AuthN) é o ato de desafiador uma entidade para credenciais legítimas.

  - Meu aplicativo realiza quaisquer atividades de risco de software, como permitir que os utilizadores de carregar ou transferir os ficheiros ou outros dados? Se a aplicação efetuar atividades de risco, considere como seu aplicativo irá proteger os usuários do processamento de dados ou ficheiros maliciosos.

### <a name="review-owasp-top-10"></a>10 de principais OWASP de revisão
Considere examinar a [ <span class="underline">riscos de segurança do OWASP Top 10 Application</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).
OWASP Top 10 aborda os riscos de segurança críticos para aplicativos web.
Reconhecimento de um desses riscos de segurança pode ajudá-lo a tomar decisões de design e de requisito que minimizar esses riscos em seu aplicativo.

É importante a pensar sobre controles de segurança para impedir violações.
No entanto, também queira [assumida](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops) irá ocorrer. Supondo que uma violação ajuda a responder a algumas perguntas importantes sobre a segurança com antecedência, pelo que não precisam ser respondidas numa emergência:

  - Como será a detetar um ataque?

  - O que fará se houver um ataque ou violação?

  - Como eu vou recuperar a partir de ataque, como dados vazando ou violação?

## <a name="design"></a>Design

A fase de design é fundamental para o estabelecimento de práticas recomendadas para design e as especificações funcionais. Também é fundamental para a execução de análise de riscos que o ajuda a mitigar problemas de segurança e privacidade em todo o projeto.

Quando tiver requisitos de segurança no local e utilizar os conceitos de design seguro, pode evitar ou minimizar as oportunidades de uma falha de segurança. Uma falha de segurança é uma falha no design do aplicativo que poderão permitir que um utilizador efetuar ações maliciosas ou inesperadas após o lançamento do seu aplicativo.

Durante a fase de design, também pensar sobre como pode aplicar a segurança em camadas; um nível de defesa não é necessariamente suficiente. O que acontece se um atacante obtém anteriores a firewall de aplicações web (WAF)? Pretende que o outro controle de segurança em vigor para se Defender contra esse ataque.

Com isso em mente, vamos abordar os seguintes conceitos de design seguro e os controlos de segurança que deve resolver ao projetar aplicativos seguros:

- Utilize uma biblioteca de codificação segura e uma arquitetura de software.
- Verificar a existência de componentes vulneráveis.
- Utilize a Modelagem de ameaças durante o design do aplicativo.
- Reduza a superfície de ataque.
- Adote uma política de identidade como perímetro segurança principal.
- Exigir a reautenticação para transações importantes.
- Utilize uma solução de gestão de chaves para proteger chaves, credenciais e outros segredos.
- Protege dados confidenciais.
- Implementar medidas isento de falhas.
- Tire partido de erro e processamento de exceções.
- Utilize alertas e logs.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Utilizar uma biblioteca de codificação segura e uma arquitetura de software

Para o desenvolvimento, utilize uma biblioteca de codificação segura e uma arquitetura de software que tem incorporado a segurança. Os desenvolvedores podem usar existente, comprovado recursos (encriptação, limpeza de entrada, codificação de saída, chaves ou cadeias de ligação e tudo o que seria considerado um controle de segurança) em vez de desenvolver os controlos de segurança a partir do zero. Isto ajuda a proteger contra falhas de implementação e de design relacionados à segurança.

Certifique-se de que está a utilizar a versão mais recente de sua estrutura e todas as funcionalidades de segurança que estão disponíveis no framework. A Microsoft oferece uma abrangente [conjunto de ferramentas de desenvolvimento](https://azure.microsoft.com/product-categories/developer-tools/) para todos os desenvolvedores, trabalhando em qualquer plataforma ou linguagem, para fornecer aplicações na cloud. Pode programar com a linguagem da sua preferência ao escolher de várias [SDKs](https://azure.microsoft.com/downloads/).
Pode tirar partido de ambientes completos de desenvolvimento integrado (IDEs) e editores de que tem capacidades de depuração e suporte do Azure incorporado avançadas.

A Microsoft oferece diversas [linguagens, arquiteturas e ferramentas](https://docs.microsoft.com/azure/index#pivot=sdkstools&panel=sdkstools-all) que pode usar para desenvolver aplicações no Azure. Um exemplo é [do Azure para programadores de .NET e .NET Core](https://docs.microsoft.com/dotnet/azure/). Para cada idioma e a estrutura que oferecemos, encontrará guias de introdução, tutoriais e referências de API para o ajudar a começar rapidamente.

O Azure oferece uma variedade de serviços que pode utilizar para alojar as aplicações web e Web sites. Estes serviços permitem-lhe programar na sua linguagem favorita, independentemente de ser .NET, .NET Core, Java, Ruby, node. js, PHP ou Python.
[Aplicações de Web do serviço de aplicações do Azure](https://docs.microsoft.com/azure/app-service/app-service-web-overview) (aplicações Web) é um destes serviços.

As aplicações Web adiciona o poder do Microsoft Azure à sua aplicação. Ele inclui a segurança, balanceamento de carga, dimensionamento automático e gestão automatizada. Também pode tirar partido das capacidades de DevOps nas aplicações Web, como gestão de pacotes, ambientes, domínios personalizados, certificados SSL/TLS e implementação contínua a partir do Azure DevOps, GitHub, Docker Hub e outras origens de teste.

O Azure oferece outros serviços que pode utilizar para alojar as aplicações web e Web sites. Para a maioria dos cenários, as Web Apps são a melhor escolha. Para uma arquitetura de serviço micro, considere [do Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric).
Se precisar de mais controlo sobre as VMs em que o seu código é executado, considere as [Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/).
Para obter mais informações sobre como escolher entre estes serviços do Azure, consulte um [comparação do serviço de aplicações do Azure, máquinas virtuais, Service Fabric e serviços Cloud](https://docs.microsoft.com/azure/app-service/choose-web-site-cloud-service-vm).

### <a name="apply-updates-to-components"></a>Aplicar atualizações de componentes

Para evitar vulnerabilidades, deve continuamente de inventário de seus componentes do lado do cliente e servidor (por exemplo, estruturas e bibliotecas) e as respetivas dependências para atualizações. São continuamente lançadas novas vulnerabilidades e versões de software atualizado. Certifique-se de que tem um plano em curso para monitorizar, fazer a triagem e aplicar atualizações ou configuração for alterada para as bibliotecas e os componentes que utilizar.

Consulte a [Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) página no [usando componentes com vulnerabilidades conhecidas](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) para sugestões de ferramentas. Também pode subscrever alertas por e-mail relativos a vulnerabilidades de segurança que estão relacionados aos componentes de que utilizar.

### <a name="use-threat-modeling-during-application-design"></a>Utilizar a Modelagem de ameaças durante o design do aplicativo

A Modelagem de ameaças é o processo de identificar potenciais ameaças de segurança para seus negócios e a aplicação e, em seguida, garantindo que as atenuações apropriadas estão em vigor. O SDL Especifica que as equipes devem participar de modelagem de ameaças durante a fase de design, quando a resolução de problemas potenciais é relativamente fácil e económica. Usando modelagem na fase de design pode reduzir significativamente o custo total de desenvolvimento de ameaças.

Para ajudar a facilitar o processo de modelagem de ameaças, projetamos o [ferramenta de modelagem de ameaças do SDL](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) com especialistas que não são de segurança em mente. Esta ferramenta facilita a Modelagem de ameaças para todos os desenvolvedores ao fornecer uma orientação clara sobre como criar e analisar os modelos de ameaças.

O design do aplicativo de modelagem e enumerar [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) ameaças — Spoofing, violação, rejeição, divulgação de informações, Denial of Service e elevação de privilégios — em toda a confiança de todos os limites provou uma forma eficaz para capturar erros de design desde o início. A tabela seguinte lista as ameaças STRIDE e fornece algumas mitigações de exemplo que utilizam recursos fornecidos pelo Azure. Essas atenuações não funcionarão em todas as situações.

| Ameaças | Propriedade de segurança | Potenciais atenuação de plataforma do Azure |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Spoofing               | Authentication        | [Exigir ligações HTTPS](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio). |
| Adulteração              | Integridade             | Valide certificados SSL/TLS. Aplicativos que usam o SSL/TLS completamente tem de verificar os certificados X.509 das entidades que se ligam ao. Utilizar certificados do Azure Key Vault para [gerenciar seu x509 certificados](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates). |
| Rejeição            | Não rejeição       | Ativar o Azure [monitorização e diagnóstico](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).|
| Divulgação de Informações | Confidencialidade       | Encriptar dados confidenciais [Inativos](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest) e [em trânsito](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices#protect-data-in-transit). |
| Denial of Service (DoS)      | Disponibilidade          | Monitorizar métricas de desempenho para negação potencial de condições do serviço. Implemente filtros de conexão. [O Azure DDoS protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview#next-steps), juntamente com práticas recomendadas de design de aplicativo, que fornece a defesa contra ataques DDoS.|
| Elevação de privilégio | Autorização         | Utilizar o Azure Active Directory <span class="underline"> </span> [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure).|

### <a name="reduce-your-attack-surface"></a>Reduzir a superfície de ataque

Uma superfície de ataque é o total de soma de onde poderão ocorrer potenciais vulnerabilidades. Neste artigo, vamos nos concentrar na superfície de ataque de um aplicativo.
O foco é uma aplicação a proteger contra ataques. É uma forma simples e rápida para minimizar a superfície de ataque remover recursos não utilizados e código de seu aplicativo. Mais pequeno mais pequeno de aplicação, a superfície de ataque. Por exemplo, remova:

- Código para funcionalidades que ainda não lançadas ainda.
- Depurar o código de suporte.
- Interfaces de rede e os protocolos que não estão a ser utilizado ou que foram preterido.
- Máquinas virtuais e outros recursos que não estiver a utilizar.

Fazer uma limpeza regular dos seus recursos e garantir que remove o código não utilizado são maneiras excelentes de Certifique-se de que existem menos oportunidades para atores maliciosos a ataques.

É uma forma mais detalhada e detalhada para reduzir a superfície de ataque concluir uma análise da superfície de ataque. Uma análise da superfície de ataque ajuda-o a mapear as partes de um sistema que precisam ser revisto e testado para vulnerabilidades de segurança.

O objetivo de uma análise da superfície de ataque é compreender as áreas de risco num aplicativo para que os programadores e especialistas de segurança estão atento a que partes do aplicativo são abertos a ataques. Em seguida, pode encontrar formas de minimizar este Roteiro potencial, quando e como a superfície de ataque é alterado e o que isso significa que de uma perspectiva de risco.

Uma análise da superfície de ataque ajuda a identificar:

- As funções e partes do sistema, precisa revisar e testar vulnerabilidades de segurança.
- Áreas de alto risco de código que necessitam de proteção de defesa em profundidade (partes do sistema que terá de se Defender).
- Quando alterar a superfície de ataque e ter de atualizar uma avaliação de ameaças.

Diminuindo as oportunidades dos invasores um potencial ponto fraco ou vulnerabilidade exige que analisar de forma detalhada a superfície de ataque geral do seu aplicativo. Ele também inclui a desativação ou restringir o acesso a serviços do sistema, aplicando o princípio de privilégio mínimo, e empregar defesas em camadas sempre que possível.

Vamos discutir [conduzir uma revisão da superfície de ataque](secure-develop.md#conduct-attack-surface-review) durante a fase de verificação do SDL.

> [!NOTE]
> **O que é a diferença entre a Modelagem de ameaças e análise da superfície de ataque?**
A Modelagem de ameaças é o processo de identificar potenciais ameaças de segurança à sua aplicação e garantir que as atenuações apropriadas contra as ameaças estão em vigor. Análise da superfície de ataque identifica as áreas de alto risco de código que estejam abertas a ataques. Ela envolve a encontrar maneiras de se Defender áreas de alto risco da sua aplicação e revisar e testar essas áreas de código antes de implementar a aplicação.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Adotar uma política de identidade como perímetro segurança principal

Ao conceber aplicações na cloud, é importante expandir seu escopo de perímetro de segurança de uma abordagem centrada em rede para uma abordagem centrada em identidade. Historicamente, o perímetro de segurança no local primário era a rede de uma organização. A maioria das estruturas de segurança no local utilizam a rede, como o pivô de segurança principal. Para aplicações na cloud, é mais bem atendidas Considerando identidade como perímetro segurança principal.

Coisas que pode fazer para desenvolver uma abordagem centrada em identidade ao desenvolvimento de aplicativos web:

- Impor o multi-factor authentication para os utilizadores.
- Utilize plataformas fortes de autenticação e autorização.
- Aplique o princípio de privilégio mínimo.
- Implementar o acesso just-in-time.

#### <a name="enforce-multi-factor-authentication-for-users"></a>Impor o multi-factor authentication para utilizadores

Utilize a autenticação de dois fatores. Autenticação de dois fatores é o padrão atual para autenticação e autorização porque evita as vulnerabilidades de segurança inerentes à tipos de nome de utilizador e palavra-passe de autenticação. Acesso para as interfaces de gestão do Azure (remoto do Azure portal/PowerShell) e aos serviços ao cliente deve ser criado e configurado para utilizar [multi-factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication).

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Utilizar plataformas fortes de autenticação e autorização

Utilize mecanismos de autenticação e autorização fornecidos pela plataforma, em vez de código personalizado. Isto acontece porque a desenvolver o código de autenticação personalizados pode ser sujeita a erros. Código comercial (por exemplo, da Microsoft), muitas vezes, é amplamente revisado quanto à segurança. [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é a solução do Azure para gestão de identidades e acesso. Estes serviços e ferramentas do Azure AD ajudam com o desenvolvimento seguro:

- [Azure plataforma de identidade (Azure AD para programadores) do AD](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform) é um serviço de identidade de cloud utilizada pelos desenvolvedores para criar aplicações com segurança sessão dos utilizadores. O Azure AD auxilia os programadores que estão a criar aplicações de (LOB) do inquilino único, a linha de negócio e desenvolvedores que buscam para desenvolver aplicações multi-inquilino. Além do básico início de sessão, os aplicativos que são criados ao utilizar o Azure AD podem chamar APIs da Microsoft e APIs personalizadas que são criadas com a plataforma do Azure AD. A plataforma de identidade do Azure AD suporta protocolos de norma da indústria, como o OAuth 2.0 e OpenID Connect.

- [O Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/) é um serviço de gestão de identidade pode utilizar para personalizar e controlar como os clientes se inscrevem, inicie sessão e gerem os respetivos perfis quando utilizam seus aplicativos. Isto inclui aplicações que são desenvolvidas para iOS, Android e .NET, entre outros. O Azure AD B2C permite estas ações ao proteger as identidades de cliente.

#### <a name="apply-the-principle-of-least-privilege"></a>Aplique o princípio de privilégio mínimo

O conceito de [menor privilégio](https://en.wikipedia.org/wiki/Principle_of_least_privilege) significa fornecer aos usuários o nível preciso de acesso e o controlo que precisam para fazer seu trabalho e nada mais.

Um desenvolvedor de software precisaria de direitos de administrador de domínio? Um assistente administrativo teria acesso aos controles administrativas no respetivo computador pessoal? Avaliar o acesso a software não é diferente. Se usar [controlo de acesso baseado em funções (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) para oferecer capacidades diferentes de usuários e autoridade na sua aplicação, não daria a todos os utilizadores aceder a tudo. Ao limitar o acesso para o que é necessário para cada função, limitar o risco de um problema de segurança ocorrer.

Certifique-se de que seu aplicativo impõe [menor privilégio](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) ao longo de seus padrões de acesso.

> [!NOTE]
>  As regras de privilégio mínimo tem de se aplicam ao software e para as pessoas a criação de software. Os desenvolvedores de software podem ser um grande risco à segurança de TI, se eles recebem muito acesso. As conseqüências podem ser graves se um desenvolvedor tem más intenções ou receberem muito acesso. Recomendamos que as regras de privilégio mínimo aplicado aos desenvolvedores em todo o ciclo de vida de desenvolvimento.

#### <a name="implement-just-in-time-access"></a>Implementar o acesso just-in-time

Implemente *just-in-time* acesso (JIT) para reduzir ainda mais o tempo de exposição dos privilégios. Uso [do Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#stage-3-build-visibility-and-take-full-control-of-admin-activity) para:

- Dê aos utilizadores as permissões que precisam apenas JIT.
- Atribua funções para um período de tempo abreviado com confiança de que os privilégios são revogados automaticamente.

### <a name="require-re-authentication-for-important-transactions"></a>Exigir a reautenticação para transações importantes

[Falsificação de pedidos entre sites entre](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (também conhecido como *XSRF* ou *CSRF*) é um ataque contra aplicações web alojadas em que uma aplicação web maliciosos influencia a interação entre o browser cliente e uma web aplicação que confia esse navegador. Ataques de falsificação de solicitação são possíveis porque os navegadores da web enviam alguns tipos de tokens de autenticação automaticamente com cada solicitação para um Web site.
Essa forma de exploração é também conhecido como um *ataque de um clique* ou *seqüestro de sessão* porque o ataque tira partido do usuário do autenticado anteriormente sessão.

A melhor forma de se Defender contra esse tipo de ataque é pedir ao utilizador para algo que apenas o utilizador pode fornecer antes de todas as transações importantes, como uma compra, a desativação de conta ou uma alteração de palavra-passe. Pode pedir ao utilizador para introduzir a palavra-passe, conclua um captcha ou submeta um token secreto que só o utilizador terá de. A abordagem mais comum é o token secreto.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Utilizar uma solução de gestão de chaves para proteger chaves, credenciais e outros segredos

Perda de chaves e as credenciais é um problema comum. A única coisa que pior do que perder as chaves e as credenciais é fazer com que uma entidade não autorizada obter acesso aos mesmos. Os atacantes podem tirar partido das técnicas automatizadas e manuais, para localizar as chaves e segredos armazenados nos repositórios de código como o GitHub. Não coloque a chaves e segredos nestes repositórios de código público ou em qualquer outro servidor.

Sempre coloca suas chaves, certificados, segredos e as cadeias de ligação numa solução de gestão de chaves. Pode utilizar uma solução centralizada em que as chaves e segredos são armazenados em módulos de segurança de hardware (HSMs). O Azure disponibiliza um HSM na cloud com o [do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis).

O Key Vault é um *arquivo de segredos*: é um serviço de nuvem centralizado para armazenar segredos da aplicação. Key Vault mantém os seus dados confidenciais seguros manter segredos da aplicação num único local central e fornecendo acesso seguro, o controle de permissões e o registo de acesso.

Segredos são armazenados num indivíduo *cofres*. Cada cofre tem sua própria configuração e políticas de segurança para controlar o acesso. Introdução aos seus dados através de uma API REST ou um SDK que está disponível para a maioria das linguagens de programação do cliente.

> [!IMPORTANT]
> O Azure Key Vault foi concebido para armazenar segredos de configuração para aplicações de servidor. Não se destina para armazenar dados pertencentes a utilizadores da aplicação. Isso é refletido no suas características de desempenho, a API e o modelo de custos.
>
> Dados de utilizador devem ser armazenados em outro lugar, como numa instância de base de dados do Azure SQL que tem a encriptação de dados transparente (TDE) ou uma conta de armazenamento que utiliza o Azure Storage Service Encryption. Segredos que são utilizados pela sua aplicação para aceder a esses arquivos de dados podem ser mantidos no Azure Key Vault.

### <a name="protect-sensitive-data"></a>Proteger dados confidenciais

Proteção de dados é uma parte essencial da sua estratégia de segurança.
Classificar os dados e identificar a proteção de dados precisam de ajuda a conceber a sua aplicação com a segurança dos dados em mente. Classificar (categorizar) armazenados dados pelo sensibilidade e o impacto comercial ajuda os desenvolvedores a determinar os riscos associados a dados.

Ao conceber os formatos de dados da etiqueta todos os dados aplicáveis como confidencial. Certifique-se de que a aplicação trata os dados aplicáveis como confidencial. Essas práticas podem ajudar a proteger os seus dados confidenciais:

- Utilize a encriptação.
- Evite embutir no código segredos, como chaves e palavras-passe.
- Certifique-se de que os controlos de acesso e auditoria estão em vigor.

#### <a name="use-encryption"></a>A utilização de encriptação

Proteção de dados deve ser uma parte essencial da sua estratégia de segurança.
Se os dados são armazenados numa base de dados ou move e volta entre locais, utilize a encriptação de [dados em repouso](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest) (enquanto estiver no banco de dados) e a encriptação dos [dados em trânsito](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices#protect-data-in-transit) (no seu caminho de e para o usuário, a base de dados, uma API ou ponto final de serviço). Recomendamos que utilize sempre os protocolos SSL/TLS para trocar dados. Certifique-se de que utilize a versão mais recente do TLS para a encriptação (atualmente, esta é a versão 1.2).

#### <a name="avoid-hard-coding"></a>Evitar embutir no código

Algumas coisas nunca devem ser codificada no seu software. Alguns exemplos são os nomes de anfitrião ou IP endereços, URLs, endereços de e-mail, nomes de utilizador, palavras-passe, chaves de conta de armazenamento e outras chaves criptográficas. Pondere implementar requisitos em todo o que pode ou não pode ser codificada no seu código, incluindo nas secções de comentário do seu código.

Quando colocar comentários em seu código, certifique-se de que não guarde quaisquer informações confidenciais. Isto inclui o seu endereço de e-mail, palavras-passe, cadeias de ligação, informações sobre a aplicação que só deve ser conhecida por alguém na sua organização e qualquer outra coisa que pode dar a um invasor se beneficiarão atacar a sua aplicação ou a organização .

Basicamente, partem do princípio de que tudo no seu projeto de desenvolvimento será conhecimento público quando for implementada. Evite a inclusão de dados confidenciais de qualquer tipo no projeto.

Anteriormente, discutimos [do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis). Pode utilizar o Key Vault para armazenar segredos, como chaves e palavras-passe em vez de embutir no código-los. Quando utilizar o Key Vault em combinação com identidades geridas para recursos do Azure, aplicação web do Azure pode aceder a valores de configuração secreta fácil e segura sem armazenar segredos em sua configuração ou o controlo de origem. Para obter mais informações, consulte [gerir segredos nas suas aplicações de servidor com o Azure Key Vault](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/).

### <a name="implement-fail-safe-measures"></a>Implementar medidas de isento de falhas

Seu aplicativo deve ser capaz de lidar com [erros](https://docs.microsoft.com/dotnet/standard/exceptions/) que ocorrem durante a execução de uma forma consistente. A aplicação deverá capturar todos os erros e qualquer uma falha seguro ou fechado.

Também deve garantir que os erros são registados com o contexto de usuário suficientes para identificar atividade suspeita ou maliciosa. Devem ser retidos os registos de um tempo suficiente permitir análises forenses atrasado. Registos devem estar num formato que pode ser facilmente consumido por uma solução de gestão do registo. Certifique-se de que são acionados alertas para erros relacionados com segurança. Registo e a monitorização insuficiente permitem aos atacantes ainda mais sistemas de ataque e manter a persistência.

### <a name="take-advantage-of-error-and-exception-handling"></a>Tire partido de erro e manipulação de exceção

Implementar correto de erros e [manipulação de exceção](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions) é uma parte importante de defesa de codificação. Processamento de exceções e erros são críticas para que um sistema seguro e confiável. Erros no tratamento de erros podem levar a diferentes tipos de vulnerabilidades de segurança, como o vazamento de informações para invasores e ajudar os atacantes saber mais sobre a plataforma e o design.

Certifique-se de que:

- Lidar com exceções de forma centralizada para evitar duplicado [try/catch blocos](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) no código.

- Todos os comportamentos inesperados são tratados dentro do aplicativo.

- As mensagens que são apresentadas aos utilizadores não vazam dados críticos, mas fornecem informações suficientes para explicar o problema.

- Exceções são registadas e que fornecem informações suficientes para equipes de resposta de incidente ou de gestão forense investigar.

[O Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) proporciona uma experiência de primeira classe para [tratamento de erros e exceções](https://docs.microsoft.com/azure/logic-apps/logic-apps-exception-handling) que são causadas por sistemas dependentes. Pode utilizar o Logic Apps para criar fluxos de trabalho para automatizar tarefas e processos que se integram aplicações, dados, sistemas e serviços em empresas e organizações.

### <a name="use-logging-and-alerting"></a>Registo de utilização e os alertas

[Registo](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) sua segurança emite para investigações de segurança e acionar alertas sobre problemas para se certificar de que as pessoas conhecer os problemas em tempo hábil. Ative auditoria e registo em todos os componentes. Registos de auditoria devem capturar o contexto de utilizador e identificar todos os eventos importantes.

Verifique que não a iniciar sessão quaisquer dados confidenciais que um usuário envia para o seu site. Exemplos de dados confidenciais:

- Credenciais de utilizador
- Números de previdência social ou outras informações de identificação
- Números de cartão de crédito ou outras informações financeiras
- Informações de estado de funcionamento
- As chaves privadas ou outros dados que podem ser utilizados para desencriptar informações encriptadas
- Informações de sistema ou aplicativo que podem ser utilizadas para atacar com mais eficiência a aplicação

Certifique-se de que a aplicação monitoriza os eventos de gestão de utilizador, tais como inícios de sessão do utilizador com êxito ou falhada, reposições de palavra-passe, as alterações de palavra-passe, o bloqueio de conta e registro do usuário. O registo para esses eventos ajuda-o a detetar e reagir a comportamentos potencialmente suspeitos. Ele também permite-lhe recolher dados de operações, como quem está a aceder a aplicação.

## <a name="next-steps"></a>Passos Seguintes
Nos seguintes artigos, recomendamos que os controlos de segurança e atividades que podem ajudá-lo a desenvolver e implantar aplicativos seguros.

- [Desenvolver aplicações seguras](secure-develop.md)
- [Implemente aplicações seguras](secure-deploy.md)
