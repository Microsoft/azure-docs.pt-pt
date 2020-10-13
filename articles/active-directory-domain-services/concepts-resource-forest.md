---
title: Conceitos de floresta de recursos para serviços de domínio Azure AD / Microsoft Docs
description: Saiba o que é uma floresta de recursos nos Serviços de Domínio do Diretório Ativo Azure e como beneficiam a sua organização em ambiente híbrido com opções limitadas de autenticação de utilizadores ou preocupações de segurança.
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: 1f8c09e7e260d22aa6864327d6a890ef3beaa039
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962518"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Conceitos e funcionalidades da floresta de recursos para serviços de domínio do diretório ativo Azure

A Azure Ative Directory Domain Services (Azure AD DS) proporciona uma experiência de inscrição para aplicações antigas, no local, linha de negócios. Os utilizadores, grupos e hashes de passwords de utilizadores no local e utilizadores da nuvem são sincronizados com o domínio gerido Azure AD DS. Estas hashes de palavra-passe sincronizadas são o que dá aos utilizadores um único conjunto de credenciais que podem usar para os AD DS, Microsoft 365 e Azure Ative Directory.

Embora seguros e ofereçam benefícios adicionais de segurança, algumas organizações não podem sincronizar essas palavras-passe do utilizador hashes para Azure AD ou Azure AD DS. Os utilizadores de uma organização podem não saber a sua palavra-passe porque apenas utilizam a autenticação de cartões inteligentes. Estas limitações impedem que algumas organizações utilizem a Azure AD DS para levantar e transferir aplicações clássicas no local para a Azure.

Para responder a estas necessidades e restrições, pode criar um domínio gerido que utilize uma floresta de recursos. Este artigo conceptual explica o que são as florestas e como confiam noutros recursos para fornecer um método de autenticação seguro.

## <a name="what-are-forests"></a>O que são florestas?

Uma *floresta* é uma construção lógica usada pelos Ative Directory Domain Services (AD DS) para agrupar um ou mais *domínios*. Os domínios armazenam então objetos para utilizador ou grupos e fornecem serviços de autenticação.

Num domínio gerido AZure AD DS, a floresta contém apenas um domínio. As florestas de DS AD no local muitas vezes contêm muitos domínios. Em grandes organizações, especialmente após fusões e aquisições, você pode acabar com múltiplas florestas no local que cada uma então contém vários domínios.

Por padrão, um domínio gerido é criado como uma floresta *de utilizadores.* Este tipo de floresta sincroniza todos os objetos a partir de Azure AD, incluindo quaisquer contas de utilizador criadas em um ambiente AD DS no local. As contas dos utilizadores podem autenticar-se diretamente contra o domínio gerido, de modo a iniciar seducação num VM de domínio. Uma floresta de utilizadores funciona quando os hashes de palavra-passe podem ser sincronizados e os utilizadores não estão a usar métodos exclusivos de entrada como a autenticação de cartões inteligentes.

Numa floresta de *recursos de* domínio gerida, os utilizadores autenticam-se sobre uma *confiança* florestal unidireccionada a partir dos seus DS AD no local. Com esta abordagem, os objetos de utilizador e as hashes de palavra-passe não são sincronizados com o domínio gerido. Os objetos e credenciais do utilizador só existem no local AD DS. Esta abordagem permite às empresas acolher recursos e plataformas de aplicação em Azure que dependem da autenticação clássica como LDAPS, Kerberos ou NTLM, mas quaisquer problemas ou preocupações de autenticação são removidos.

As florestas de recursos também fornecem a capacidade de levantar e deslocar as suas aplicações um componente de cada vez. Muitas aplicações antigas no local são multi-tiered, muitas vezes usando um servidor web ou frontal e muitos componentes relacionados com a base de dados. Estes níveis dificultam a elevação e deslocação de toda a aplicação para a nuvem num só passo. Com as florestas de recursos, pode levantar a sua aplicação para a nuvem numa abordagem faseada, o que facilita a deslocação da sua aplicação para Azure.

## <a name="what-are-trusts"></a>O que são os fundos?

Organizações que têm mais de um domínio muitas vezes precisam que os utilizadores acedam a recursos partilhados em um domínio diferente. O acesso a estes recursos partilhados requer que os utilizadores de um domínio autentem para outro domínio. Para fornecer estas capacidades de autenticação e autorização entre clientes e servidores em diferentes domínios, deve haver uma *confiança* entre os dois domínios.

Com a confiança do domínio, os mecanismos de autenticação de cada domínio confiam nas autenticações provenientes do outro domínio. Os trusts ajudam a fornecer acesso controlado a recursos partilhados num domínio de recursos (o domínio *de confiança) verificando* que os pedidos de autenticação recebidas provêm de uma autoridade de confiança (o domínio *fidedigno).* Os trusts funcionam como pontes que apenas permitem pedidos de autenticação validado viajar entre domínios.

A forma como um fundo passa pedidos de autenticação depende de como é configurado. Os fundos podem ser configurados de uma das seguintes formas:

* **Unidirecionais** - fornece acesso do domínio fidedigno aos recursos no domínio de confiança.
* **Bidirecionais** - fornece acesso de cada domínio a recursos no outro domínio.

Os fundos de investimento também são configurados para lidar com relações de confiança adicionais de uma das seguintes formas:

* **Não é transativa** - A confiança existe apenas entre os dois domínios de parceiros fiduciários.
* **Transitiva** - A confiança estende-se automaticamente a quaisquer outros domínios em que qualquer um dos parceiros confie.

Em alguns casos, as relações de confiança são estabelecidas automaticamente quando os domínios são criados. Outras vezes, deve escolher um tipo de confiança e estabelecer explicitamente as relações apropriadas. Os tipos específicos de fidedignidades utilizados e a estrutura dessas relações de confiança dependem da forma como o diretório de DS AD é organizado, e se diferentes versões do Windows coexistem na rede.

## <a name="trusts-between-two-forests"></a>Confiança entre duas florestas

Você pode estender as confianças de domínio dentro de uma única floresta para outra floresta, criando manualmente uma confiança florestal de sentido único ou bidirecionais. Uma confiança florestal é uma confiança transitiva que existe apenas entre um domínio de raiz florestal e um segundo domínio de raiz florestal.

* Um fundo florestal unidireccionário permite que todos os utilizadores de uma floresta confiem em todos os domínios da outra floresta.
* Um fundo florestal bidirecionais forma uma relação de confiança transitiva entre todos os domínios em ambas as florestas.

A transitividade dos fundos florestais limita-se aos dois parceiros florestais. O fundo florestal não se estende a florestas adicionais confiáveis por nenhum dos parceiros.

![Diagrama de confiança florestal de Azure AD DS para as AD DS no local](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Pode criar diferentes configurações de domínio e confiança florestal dependendo da estrutura AD DS da organização. A Azure AD DS só apoia uma confiança florestal unidireccionada. Nesta configuração, os recursos no domínio gerido podem confiar em todos os domínios numa floresta no local.

## <a name="supporting-technology-for-trusts"></a>Tecnologia de apoio a fidedignidades

Os trusts usam vários serviços e funcionalidades, tais como DNS para localizar controladores de domínio em florestas parceiras. Os fundos de investimento também dependem dos protocolos de autenticação NTLM e Kerberos e dos mecanismos de autorização e controlo de acesso baseados no Windows para ajudar a fornecer uma infraestrutura de comunicações segura em domínios ED DS e florestas. Os seguintes serviços e funcionalidades ajudam a suportar relações de confiança bem sucedidas.

### <a name="dns"></a>DNS

A DS AD precisa de DNS para a localização e nomeação do controlador de domínio (DC). O seguinte apoio do DNS é fornecido para que a AD DS funcione com sucesso:

* Um serviço de resolução de nomes que permite aos anfitriões e serviços da rede localizar DCs.
* Uma estrutura de nomeação que permite a uma empresa refletir a sua estrutura organizacional em nome dos seus domínios de serviço de diretório.

Um espaço de nome de domínio DNS é geralmente implantado que espelha o espaço de nome de domínio AD DS. Se houver um espaço de nome DNS existente antes da implantação do DS AD, o espaço de nome DNS é tipicamente dividido para DS AD, e um subdomínio DNS e delegação para a raiz da floresta AD DS é criado. São adicionados nomes de domínio DNS adicionais para cada domínio infantil AD DS.

O DNS também é usado para suportar a localização de DS DS DCs AD. As zonas DNS são povoadas com registos de recursos DNS que permitem aos anfitriões e serviços de rede localizar DS DS DS DCs.

### <a name="applications-and-net-logon"></a>Aplicações e Início de Síl em rede

Ambas as aplicações e o serviço Net Logon são componentes do modelo de canal de segurança distribuído pelo Windows. As aplicações integradas com o Windows Server e o AD DS utilizam protocolos de autenticação para comunicar com o serviço Net Logon para que possa ser estabelecido um caminho seguro sobre o qual a autenticação pode ocorrer.

### <a name="authentication-protocols"></a>Protocolos de Autenticação

Os DCs AD autenticam utilizadores e aplicações utilizando um dos seguintes protocolos:

* **Protocolo de autenticação da versão 5 kerberos**
    * O protocolo kerberos version 5 é o protocolo de autenticação predefinido utilizado por computadores no local que executam o Windows e suportam sistemas operativos de terceiros. Este protocolo é especificado no RFC 1510 e está totalmente integrado com DS AD, bloco de mensagens de servidor (SMB), HTTP e chamada de procedimento remoto (RPC), bem como aplicações de cliente e servidor que utilizam estes protocolos.
    * Quando o protocolo Kerberos é usado, o servidor não tem de contactar a DC. Em vez disso, o cliente recebe um bilhete para um servidor solicitando um de um DC no domínio da conta do servidor. Em seguida, o servidor valida o bilhete sem consultar qualquer outra autoridade.
    * Se algum computador envolvido numa transação não apoiar o protocolo kerberos da versão 5, o protocolo NTLM é utilizado.

* **Protocolo de autenticação NTLM**
    * O protocolo NTLM é um protocolo clássico de autenticação de rede utilizado por sistemas operativos mais antigos. Por razões de compatibilidade, é utilizado pelos domínios AD DS para processar pedidos de autenticação de rede que vêm de aplicações concebidas para clientes e servidores anteriores baseados no Windows e sistemas operativos de terceiros.
    * Quando o protocolo NTLM é utilizado entre um cliente e um servidor, o servidor deve contactar um serviço de autenticação de domínio num DC para verificar as credenciais do cliente. O servidor autentica o cliente reencaminha as credenciais do cliente para um DC no domínio da conta do cliente.
    * Quando dois domínios ou florestas de DS DS da AD são ligados por um fundo fiduciário, os pedidos de autenticação feitos através destes protocolos podem ser encaminhados para fornecer acesso a recursos em ambas as florestas.

## <a name="authorization-and-access-control"></a>Autorização e controlo de acessos

As tecnologias de autorização e confiança trabalham em conjunto para fornecer uma infraestrutura de comunicações segura em domínios ou florestas de DS AD. A autorização determina o nível de acesso que um utilizador tem aos recursos num domínio. Os trusts facilitam a autorização de domínio cruzado dos utilizadores, fornecendo um caminho para autenticar utilizadores noutros domínios para que os seus pedidos de recursos partilhados nesses domínios possam ser autorizados.

Quando um pedido de autenticação feito num domínio de confiança é validado pelo domínio fidedigno, é passado para o recurso alvo. O recurso-alvo determina então se autoriza o pedido específico feito pelo utilizador, serviço ou computador no domínio fidedigno com base na sua configuração de controlo de acesso.

Os trusts fornecem este mecanismo para validar pedidos de autenticação que são passados para um domínio de confiança. Os mecanismos de controlo de acesso no computador de recurso determinam o nível final de acesso concedido ao solicitador no domínio fidedigno.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os fidedignos, veja [como funcionam os fundos florestais em Azure AD DS?][concepts-trust]

Para começar com a criação de um domínio gerido com uma floresta de recursos, consulte [Criar e configurar um domínio gerido Azure AD DS][tutorial-create-advanced]. Em seguida, pode [criar uma confiança florestal de saída para um domínio no local.][create-forest-trust]

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
