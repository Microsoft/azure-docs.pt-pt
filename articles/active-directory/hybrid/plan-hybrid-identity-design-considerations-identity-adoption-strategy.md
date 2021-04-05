---
title: Design de identidade híbrida - estratégia de adoção Azure | Microsoft Docs
description: Com o controlo de acesso condicional, a Azure AD verifica as condições específicas que escolhe ao autenticar o utilizador e antes de permitir o acesso à aplicação.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f52e46ff9cab7d3d150af9fd7b4f1c432bec74b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836194"
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>Definir uma estratégia híbrida de adoção de identidade
Nesta tarefa, define a estratégia de adoção de identidade híbrida para a sua solução de identidade híbrida para satisfazer os requisitos de negócio que foram discutidos em:

* [Determinar as necessidades do negócio](plan-hybrid-identity-design-considerations-business-needs.md)
* [Determinar os requisitos de sincronização do diretório](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [Determinar requisitos de autenticação de vários fatores](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definir estratégia de necessidades de negócio
A primeira tarefa aborda a determinação das necessidades empresariais das organizações.  Isto pode ser muito amplo e pode ocorrer se não tiver cuidado.  No início, mantenha-o simples mas lembre-se sempre de planear um design que irá acomodar e facilitar a mudança no futuro.  Independentemente de se ser um design simples ou um design extremamente complexo, o Azure Ative Directory é a plataforma Microsoft Identity que suporta aplicações microsoft 365, Microsoft Online e cloud aware.

## <a name="define-an-integration-strategy"></a>Definir uma estratégia de integração
A Microsoft tem três cenários principais de integração que são identidades em nuvem, identidades sincronizadas e identidades federadas.  Devias planear adotar uma destas estratégias de integração.  A estratégia que escolhe pode variar e as decisões na escolha podem incluir, que tipo de experiência de utilizador quer fornecer, tem uma infraestrutura existente e qual é a mais rentável.  

![cenários de integração](./media/plan-hybrid-identity-design-considerations/integration-scenarios.png)

Os cenários definidos na figura acima são:

* **Identidades em nuvem**: estas são identidades que existem apenas na nuvem.  No caso da Azure AD, eles residiriam especificamente no seu diretório AD Azure.
* **Sincronizado:** estas são identidades que existem no local e na nuvem.  Utilizando o Azure AD Connect, estes utilizadores são criados ou associados às contas AD existentes.  O hash de palavra-passe do utilizador é sincronizado do ambiente no local para a nuvem no que é chamado de haxixe de palavra-passe.  Ao utilizar sincronizado, a única ressalva é que, se um utilizador for desativado no ambiente no local, pode demorar até três horas para que o estado da conta apareça no Azure AD.  Isto deve-se ao intervalo de tempo de sincronização.
* **Federado:** estas identidades existem tanto no local como na nuvem.  Utilizando o Azure AD Connect, estes utilizadores são criados ou associados às contas AD existentes.  

> [!NOTE]
> Para obter mais informações sobre as opções de Sincronização, leia [integrando as suas identidades no local com o Azure Ative Directory](whatis-hybrid-identity.md).
> 
> 

O quadro a seguir ajuda a determinar as vantagens e desvantagens de cada uma das seguintes estratégias:

| Estratégia | Vantagens | Desvantagens |
| --- | --- | --- |
| **Identidades em nuvem** |Mais fácil de gerir para uma pequena organização. <br> Nada para instalar no local. Não é necessário hardware adicional<br>Facilmente desativado se o utilizador deixar a empresa |Os utilizadores terão de fazer sedudas ao aceder às cargas de trabalho na nuvem <br> As palavras-passe podem ou não ser as mesmas para identidades em nuvem e no local |
| **Sincronizado** |A palavra-passe no local autentica tanto no local como nos diretórios em nuvem <br>Mais fácil de gerir para pequenas, médias ou grandes organizações <br>Os utilizadores podem ter um único sign-on (SSO) para alguns recursos <br> Método preferido da Microsoft para sincronização <br> Mais fácil de gerir |Alguns clientes podem estar relutantes em sincronizar os seus diretórios com a nuvem devido às políticas específicas da empresa |
| **Federados** |Os utilizadores podem ter um único sign-on (SSO) <br>Se um utilizador for encerrado ou sair, a conta pode ser imediatamente desativada e o acesso revogado,<br> Suporta cenários avançados que não podem ser realizados com sincronização |Mais passos para configurar e configurar <br> Maior manutenção <br> Pode exigir hardware adicional para a infraestrutura STS <br> Pode exigir hardware adicional para instalar o servidor da federação. É necessário software adicional se o AD FS for utilizado <br> Requerer uma configuração extensiva para sSO <br> Ponto crítico de falha se o servidor da federação estiver em baixo, os utilizadores não serão capazes de autenticar |

### <a name="client-experience"></a>Experiência do cliente
A estratégia que utiliza ditará a experiência de inscrição do utilizador.  As tabelas que se seguem fornecem-lhe informações sobre o que os utilizadores devem esperar que a sua experiência de inscrição seja.  Nem todos os fornecedores de identidade federados apoiam sSO em todos os cenários.

**Aplicações de rede doman e privadas:**

| Aplicação | Identidade sincronizada | Identidade federada |
| --- | --- | --- |
| Navegadores Web |Autenticação baseada em formulários |único sinal-on, às vezes necessário para fornecer iD de organização |
| Outlook |Pedir credenciais |Pedir credenciais |
| Skype para negócios (Lync) |Pedir credenciais |único sinal para Lync, credenciais solicitadas para Exchange |
| OneDrive para Empresas |Pedir credenciais |único sinal-on |
| Subscrição do Office Pro Plus |Pedir credenciais |único sinal-on |

**Fontes externas ou não fided quantos anos:**

| Aplicação | Identidade sincronizada | Identidade federada |
| --- | --- | --- |
| Navegadores Web |Autenticação baseada em formulários |Autenticação baseada em formulários |
| Outlook, Skype for Business (Lync), OneDrive for Business, Office subscription |Pedir credenciais |Pedir credenciais |
| Exchange ActiveSync |Pedir credenciais |único sinal para Lync, credenciais solicitadas para Exchange |
| Aplicações móveis |Pedir credenciais |Pedir credenciais |

Se determinou a partir da tarefa 1 que tem um IdP de terceiros ou que vai usar um para fornecer à federação a AD AZure, precisa de estar ciente das seguintes capacidades suportadas:

* Qualquer fornecedor SAML 2.0 que esteja em conformidade com o perfil SP-Lite pode suportar a autenticação da Azure AD e aplicações associadas
* Suporta a autenticação passiva, o que facilita a autenticação para OWA, SPO, etc.
* Os clientes exchange online podem ser suportados através do Perfil de Cliente Melhorado SAML 2.0 (ECP)

Também deve estar ciente das capacidades que não estarão disponíveis:

* Sem o apoio da WS-Trust/Federação, todos os outros clientes ativos quebram
  * Isto significa que nenhum cliente Lync, cliente OneDrive, Subscrição de Escritório, Office Mobile antes do Office 2016
* A transição do Office para a autenticação passiva permite-lhes suportar idPs SAML 2.0 puros, mas o suporte continuará a ser numa base cliente-a-cliente

> [!NOTE]
> Para a lista mais atualizada leia a [lista de compatibilidade da federação Azure AD](how-to-connect-fed-compatibility.md).
> 
> 

## <a name="define-synchronization-strategy"></a>Definir estratégia de sincronização
Nesta tarefa definirá as ferramentas que serão usadas para sincronizar os dados da organização no local para a nuvem e que topologia deve usar.  Porque, a maioria das organizações usa Ative Directory, a informação sobre a utilização do Azure AD Connect para responder às questões acima é fornecida em alguns detalhes.  Para ambientes que não têm Diretório Ativo, existe informação sobre a utilização do FIM 2010 R2 ou MIM 2016 para ajudar a planear esta estratégia.  No entanto, futuras versões do Azure AD Connect irão suportar diretórios LDAP, pelo que, dependendo da sua linha temporal, estas informações poderão ser capazes de ajudar.

### <a name="synchronization-tools"></a>Ferramentas de sincronização
Ao longo dos anos, várias ferramentas de sincronização têm existido e usado para vários cenários.  Atualmente, o Azure AD Connect é a ferramenta de eleição para todos os cenários suportados.  AAD Sync e DirSync também ainda estão por perto e podem até estar presentes no seu ambiente agora. 

> [!NOTE]
> Para obter as informações mais recentes sobre as capacidades suportadas de cada ferramenta, leia o artigo de comparação de ferramentas de integração do [Diretório.](plan-hybrid-identity-design-considerations-tools-comparison.md)  
> 
> 

### <a name="supported-topologies"></a>Topologias suportadas
Ao definir uma estratégia de sincronização, a topologia que é usada deve ser determinada. Dependendo da informação determinada no passo 2, pode determinar qual a topologia adequada a utilizar. A única floresta, a topologia AD única Ad é a mais comum e consiste de uma única floresta de Diretório Ativo e um único exemplo de Azure AD.  Isto vai ser usado na maioria dos cenários e é a topologia esperada quando se utiliza a instalação Azure AD Connect Express, como mostra a figura abaixo.

![Topologias apoiadas ](./media/plan-hybrid-identity-design-considerations/single-forest.png) Cenário único floresta é comum que grandes e mesmo pequenas organizações tenham múltiplas florestas, como mostra a Figura 5.

> [!NOTE]
> Para obter mais informações sobre as diferentes topologias da AD no local e a azure AD com a azure AD Connect sync leia o artigo [Topologias para Azure AD Connect](plan-connect-topologies.md).
> 
> 

![topologia multi-floresta](./media/plan-hybrid-identity-design-considerations/multi-forest.png) 

Cenário multi-florestal

Se for esse o caso, então a topologia AD única multi-floresta deve ser considerada se os seguintes itens forem verdadeiros:

* Os utilizadores têm apenas 1 identidade em todas as florestas – a secção de utilizadores que identificam de forma única abaixo descreve-a com mais detalhes.
* O utilizador autentica-se na floresta em que se encontra a sua identidade
* UPN e Source Anchor (id imutável) virão desta floresta
* Todas as florestas são acessíveis pelo Azure AD Connect – isto significa que não precisa de ser unida ao domínio e pode ser colocada num DMZ se isso facilitar.
* Os utilizadores têm apenas uma caixa de correio
* A floresta que acolhe a caixa de correio de um utilizador tem a melhor qualidade de dados para atributos visíveis na Lista de Endereços Globais de Intercâmbio (GAL)
* Se não houver caixa de correio no utilizador, então qualquer floresta pode ser usada para contribuir com estes valores
* Se tiver uma caixa de correio ligada, então também há outra conta numa floresta diferente usada para iniciar scontabilidade.

> [!NOTE]
> Os objetos que existem tanto no local como na nuvem estão "ligados" através de um identificador único. No contexto da Sincronização do Diretório, este identificador único é referido como o SourceAnchor. No contexto do Sign-On Único, este é referido como o ImuttableId. [Conceitos de design para Azure AD Connect](plan-connect-design-concepts.md#sourceanchor) para mais considerações sobre a utilização de SourceAnchor.
> 
> 

Se o acima não for verdade e tiver mais de uma conta ativa ou mais do que uma caixa de correio, o Azure AD Connect escolherá um e ignorará o outro.  Se tiver caixas de correio ligadas mas nenhuma outra conta, estas contas não serão exportadas para a Azure AD e esse utilizador não será membro de nenhum grupo.  Isto é diferente de como era no passado com o DirSync e é intencional para apoiar melhor estes cenários multi-florestais. Um cenário multi-florestal é mostrado na figura abaixo.

![vários inquilinos da AD AZure](./media/plan-hybrid-identity-design-considerations/multiforest-multipleAzureAD.png) 

**Multi-floresta multiple AD cenário AD**

Recomenda-se ter apenas um único diretório em Azure AD para uma organização, mas é apoiado que uma relação 1:1 é mantida entre um servidor de sincronização Azure AD Connect e um diretório AD Azure.  Para cada instância de Azure AD, você precisa de uma instalação de Azure AD Connect.  Além disso, a Azure AD, por design é isolada e os utilizadores em um caso de Azure AD não serão capazes de ver os utilizadores em outro caso.

É possível e suportado ligar uma instância no local do Ative Directory a vários diretórios Azure, como mostra a figura abaixo:

![filtragem única da floresta](./media/plan-hybrid-identity-design-considerations/single-forest-flitering.png) 

**Cenário de filtragem de uma única floresta**

Para tal, o seguinte deve ser verdade:

* Os servidores de sincronização Azure AD Connect devem ser configurados para filtragem, de modo a que cada um tenha um conjunto de objetos mutuamente exclusivo.  Isto feito, por exemplo, ao digitalizar cada servidor para um determinado domínio ou OU.
* Um domínio DNS só pode ser registado num único diretório AD Azure para que as UPNs dos utilizadores nas AD no local utilizem espaços de nome separados
* Os utilizadores de um caso de Azure AD só poderão ver os utilizadores a partir do seu caso.  Não poderão ver utilizadores nos outros casos
* Apenas um dos diretórios AD Azure pode permitir a Exchange hybrid com os in-in-ins AD
* A exclusividade mútua também se aplica à ressartação.  Isto faz com que algumas funcionalidades de recuo não suportados com esta topologia, uma vez que estas assumem uma única configuração no local.  O que está incluído:
  * Desacrevição de grupo com configuração padrão
  * Ressour do dispositivo

O seguinte não é apoiado e não deve ser escolhido como uma implementação:

* Não é suportado para ter vários servidores sincronizados Azure AD Connect conectando-se ao mesmo diretório AD Azure, mesmo que estejam configurados para sincronizar conjunto de objeto mutuamente exclusivo
* Não é suportado para sincronizar o mesmo utilizador para vários diretórios AZure AD. 
* Também não é suportado para fazer uma alteração de configuração para fazer com que os utilizadores de um AD Azure apareçam como contactos em outro diretório AD Azure. 
* Também não é suportado para modificar a sincronização Azure AD Connect para ligar a vários diretórios AD Azure.
* Os diretórios AD Azure são por design isolado. Não é suportado para alterar a configuração da sincronização Azure AD Connect para ler dados de outro diretório AD Azure numa tentativa de construir uma GAL comum e unificada entre os diretórios. Também não é suportado para exportar utilizadores como contactos para outro AD no local usando a sincronização Azure AD Connect.

> [!NOTE]
> Se a sua organização restringir os computadores da sua rede de ligação à Internet, este artigo lista os pontos finais (intervalos de endereços FQDNs, IPv4 e IPv6) que deve incluir nas listas de saída e na Internet Explorer Trusted Sites Zone de computadores clientes para garantir que os seus computadores podem utilizar com sucesso o Microsoft 365. Para mais informações leia [os intervalos de endereços URLs e IP do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Definir estratégia de autenticação de vários fatores
Nesta tarefa definirá a estratégia de autenticação multi-factor a utilizar.  A autenticação multi-factor Azure AD vem em duas versões diferentes.  Um é baseado em nuvem e o outro está no local baseado no servidor Azure MFA.  Com base na avaliação que fez acima, pode determinar qual a solução correta para a sua estratégia.  Utilize a tabela abaixo para determinar qual a melhor opção de design que melhor satisfaz os requisitos de segurança da sua empresa:

Opções de design de vários fatores:

| Ativo para garantir | MFA na nuvem | MFA no local |
| --- | --- | --- |
| Aplicativos da Microsoft |sim |sim |
| Aplicações SaaS na galeria de aplicações |sim |sim |
| Aplicações IIS publicadas através do Proxy de Aplicação do Azure AD |sim |sim |
| Aplicações IIS não publicadas através da App AD Proxy Azure |não |sim |
| Acesso remoto como VPN, RDG |não |sim |

Apesar de ter decidido uma solução para a sua estratégia, ainda precisa de utilizar a avaliação de cima sobre onde os seus utilizadores estão localizados.  Isto pode fazer com que a solução mude.  Utilize a tabela abaixo para o ajudar a determinar isto:

| Localização do utilizador | Opção de design preferida |
| --- | --- |
| Azure Active Directory |Multi-FactorAuthentication na nuvem |
| Azure AD e AD no local utilizando federação com o AD FS |Ambos |
| Azure AD e AD no local usando Azure AD Connect sem sincronização de senha |Ambos |
| Azure AD e no local usando Azure AD Connect com sincronização de senha |Ambos |
| No local, AD |Servidor Multi-Factor Authentication |

> [!NOTE]
> Deve também certificar-se de que a opção de design de autenticação multi-factor que selecionou suporta as funcionalidades necessárias para o seu design.  Para mais informações leia [Escolha a solução de segurança multi-factor para si](../authentication/concept-mfa-howitworks.md).
> 

## <a name="multi-factor-auth-provider"></a>Fornecedor multi-factor Auth
A autenticação multi-factor está disponível por padrão para administradores globais que tenham um inquilino do Azure Ative Directory. No entanto, se pretender alargar a autenticação de vários fatores a todos os seus utilizadores e/ou pretender que os seus administradores globais possam tirar partido de funcionalidades como o portal de gestão, saudações personalizadas e relatórios, então deve adquirir e configurar o Fornecedor de Autenticação Multi-Factor.

> [!NOTE]
> Deve também certificar-se de que a opção de design de autenticação multi-factor que selecionou suporta as funcionalidades necessárias para o seu design. 
> 
> 

## <a name="next-steps"></a>Passos seguintes
[Determinar os requisitos de proteção de dados](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Ver também
[Visão geral de considerações de design](plan-hybrid-identity-design-considerations-overview.md)

