---
title: Conceitos de floresta de recursos para serviços de domínio azure ad [ Microsoft Docs
description: Saiba o que é uma floresta de recursos nos Serviços de Domínio de Diretório Ativo Azure e como beneficiam a sua organização em ambiente híbrido com opções limitadas de autenticação de utilizadores ou preocupações de segurança.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: e0e5dde246dbcd5e5cb2e4ae923872a59a539d87
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476414"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Conceitos e funcionalidades da floresta de recursos para serviços de domínio de diretório ativo Azure

A Azure Ative Directory Domain Services (AD DS) fornece uma experiência de entrada para aplicações de legacy, on-premir, line-of-business. Os utilizadores, grupos e hashes de palavra-passe de utilizadores no local e na nuvem são sincronizados com o domínio gerido pelo Azure AD DS. Estas hashes de senha sincronizada são o que dá aos utilizadores um único conjunto de credenciais que podem usar para o AD DS no local, Office 365 e Azure Ative Directory.

Embora seja seguro e proporcione benefícios adicionais de segurança, algumas organizações não podem sincronizar essas palavras-passe de utilizador hashes para Azure AD ou Azure AD DS. Os utilizadores de uma organização podem não saber a sua palavra-passe porque apenas usam a autenticação do cartão inteligente. Estas limitações impedem algumas organizações de usar o Azure AD DS para levantar e transferir aplicações clássicas no local para o Azure.

Para responder a estas necessidades e restrições, pode criar um domínio gerido pelo Azure AD DS que utiliza uma floresta de recursos. Este artigo conceptual explica o que são as florestas e como confiam noutros recursos para fornecer um método de autenticação seguro. As florestas de recursos Azure AD DS estão atualmente em pré-visualização.

> [!IMPORTANT]
> As florestas de recursos Azure AD DS não suportam atualmente o Azure HDInsight ou o Azure Files. As florestas de utilizadores da AD DS de Azure predefinidas suportam ambos estes serviços adicionais.

## <a name="what-are-forests"></a>O que são florestas?

Uma *floresta* é uma construção lógica usada pelos Serviços de Domínio de Diretório Ativo (AD DS) para agrupar um ou mais *domínios.* Os domínios armazenam então objetos para utilizadores ou grupos e fornecem serviços de autenticação.

Em Azure AD DS, a floresta contém apenas um domínio. As florestas AD DS no local contêm frequentemente muitos domínios. Nas grandes organizações, especialmente após fusões e aquisições, você pode acabar com múltiplas florestas no local que cada uma contém vários domínios.

Por padrão, um domínio gerido por Azure AD DS é criado como uma floresta *de utilizadores.* Este tipo de floresta sincroniza todos os objetos da AD Azure, incluindo quaisquer contas de utilizador criadas num ambiente AD DS no local. As contas de utilizador podem autenticar diretamente contra o domínio gerido pelo Azure AD DS, como iniciar sessão num VM de domínio. Uma floresta de utilizadores funciona quando as hashes de senha podem ser sincronizadas e os utilizadores não estão a usar métodos exclusivos de entrada como a autenticação de cartões inteligentes.

Numa floresta de *recursos* Azure AD DS, os utilizadores autenticam sobre uma *confiança* florestal de sentido único a partir do seu DS A.DS no local. Com esta abordagem, os objetos de utilizador e os hashes de senha não são sincronizados com O DS Azure. Os objetos e credenciais do utilizador só existem no DS AD no local. Esta abordagem permite que as empresas acolhem recursos e plataformas de aplicação em Azure que dependem da autenticação clássica como LDAPS, Kerberos ou NTLM, mas quaisquer problemas ou preocupações de autenticação são removidos. As florestas de recursos Azure AD DS estão atualmente em pré-visualização.

As florestas de recursos também fornecem a capacidade de levantar e mudar as suas aplicações um componente de cada vez. Muitas aplicações antigas no local são multi-camadas, muitas vezes usando um servidor web ou frontal e muitos componentes relacionados com base de dados. Estes níveis dificultam o levantamento e a mudança de toda a aplicação para a nuvem num só passo. Com as florestas de recursos, pode levantar a sua aplicação para a nuvem em abordagem faseada, o que facilita a deslocação da sua aplicação para Azure.

## <a name="what-are-trusts"></a>O que são confiança?

As organizações que têm mais do que um domínio precisam frequentemente de que os utilizadores acedam a recursos partilhados num domínio diferente. O acesso a estes recursos partilhados requer que os utilizadores de um domínio autenticam para outro domínio. Para fornecer estas capacidades de autenticação e autorização entre clientes e servidores em diferentes domínios, deve haver uma *confiança* entre os dois domínios.

Com os fundos de domínio, os mecanismos de autenticação para cada domínio confiam nas autenticações provenientes do outro domínio. Os trusts ajudam a fornecer acesso controlado a recursos partilhados num domínio de recursos (o domínio *de confiança)* verificando que os pedidos de autenticação de entrada provêm de uma autoridade de confiança (o domínio *fidedigno).* Os trusts funcionam como pontes que só permitem que pedidos de autenticação validados viajem entre domínios.

A forma como um fundo passa os pedidos de autenticação depende de como é configurado. Os trusts podem ser configurados de uma das seguintes formas:

* **One way** - fornece acesso do domínio fidedigno aos recursos no domínio de confiança.
* **Bidirecional** - fornece acesso de cada domínio a recursos no outro domínio.

Os trusts também são configurados para lidar com relações adicionais de confiança de uma das seguintes formas:

* **Não transitivo** - A confiança existe apenas entre os dois domínios de parceiros de confiança.
* **Transitivo** - A confiança estende-se automaticamente a quaisquer outros domínios em que qualquer um dos parceiros confie.

Em alguns casos, as relações de confiança são estabelecidas automaticamente quando os domínios são criados. Outras vezes, deve escolher um tipo de confiança e estabelecer explicitamente as relações apropriadas. Os tipos específicos de confiança utilizados e a estrutura dessas relações de confiança dependem da forma como o serviço de diretório sino ativo é organizado, e se diferentes versões do Windows coexistem na rede.

## <a name="trusts-between-two-forests"></a>Confiançaentre duas florestas

Você pode estender os fundos de domínio dentro de uma única floresta para outra floresta, criando manualmente uma confiança florestal unidirecional ou bidirecional. Uma confiança florestal é uma confiança transitiva que existe apenas entre um domínio de raiz florestal e um segundo domínio de raiz florestal.

* Um fundo florestal de sentido único permite que todos os utilizadores de uma floresta confiem em todos os domínios da outra floresta.
* Uma confiança florestal bidirecional forma uma relação de confiança transitiva entre todos os domínios de ambas as florestas.

A transitividade dos fundos florestais limita-se aos dois parceiros florestais. O fundo florestal não se estende a florestas adicionais confiadas por nenhum dos parceiros.

![Diagrama de confiança florestal de Azure AD DS para a AD DS no local](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Pode criar diferentes configurações de domínio e confiança florestal dependendo da estrutura do Diretório Ativo da organização. A AD DS azure só apoia uma confiança florestal de sentido único. Nesta configuração, os recursos em Azure AD DS podem confiar em todos os domínios numa floresta no local.

## <a name="supporting-technology-for-trusts"></a>Tecnologia de apoio aos trusts

Os trusts usam vários serviços e funcionalidades, como o DNS para localizar controladores de domínio em florestas parceiras. Os trusts também dependem dos protocolos de autenticação NTLM e Kerberos e dos mecanismos de autorização e controlo de acesso baseados no Windows para ajudar a fornecer uma infraestrutura de comunicações segura em domínios e florestas de Directórioactivo Ativo. Os seguintes serviços e funcionalidades ajudam a apoiar relações de confiança bem sucedidas.

### <a name="dns"></a>DNS

A DS ad necessita de DNS para a localização e nomeação do controlador de domínio (DC). O seguinte suporte do DNS é fornecido para que a AD DS funcione com sucesso:

* Um serviço de resolução de nomes que permite aos anfitriões e serviços da rede localizar DCs.
* Uma estrutura de nomeação que permite a uma empresa refletir a sua estrutura organizacional nos nomes dos seus domínios de serviço de diretório.

Um espaço de nome de domínio DNS é geralmente implantado que espelha o espaço de nome de domínio AD DS. Se houver um espaço de nome DNS existente antes da implantação do DS AD, o espaço de nome DNS é tipicamente dividido para Ative Directory, e um subdomínio DNS e delegação para a raiz da floresta do Diretório Ativo é criado. Os nomes adicionais de domínio DNS são adicionados para cada domínio infantil do Diretório Ativo.

O DNS também é utilizado para apoiar a localização dos DCs de Diretório Ativo. As zonas DNS são povoadas com registos de recursos DNS que permitem aos anfitriões e serviços da rede localizar DCs de Diretório Ativo.

### <a name="applications-and-net-logon"></a>Aplicações e Logon Net

Ambas as aplicações e o serviço Net Logon são componentes do modelo de canal de segurança distribuído pelo Windows. As aplicações integradas com o Windows Server e o Ative Diretório utilizam protocolos de autenticação para comunicar com o serviço De Início de Sessão Net para que possa ser estabelecido um caminho seguro sobre o qual a autenticação possa ocorrer.

### <a name="authentication-protocols"></a>Protocolos de Autenticação

Os DCs do Diretório Ativo autenticam utilizadores e aplicações utilizando um dos seguintes protocolos:

* **Protocolo de autenticação da versão Kerberos 5**
    * O protocolo kerberos versão 5 é o protocolo de autenticação predefinido utilizado pelos computadores no local que executam o Windows e suportam sistemas operativos de terceiros. Este protocolo é especificado no RFC 1510 e está totalmente integrado com diretório ativo, bloco de mensagens de servidor (SMB), HTTP e chamada de procedimento remoto (RPC), bem como as aplicações de cliente e servidor que utilizam estes protocolos.
    * Quando o protocolo Kerberos é usado, o servidor não tem de contactar o DC. Em vez disso, o cliente recebe um bilhete para um servidor solicitando um de um DC no domínio da conta do servidor. O servidor valida então o bilhete sem consultar qualquer outra autoridade.
    * Se qualquer computador envolvido numa transação não suportar o protocolo da versão 5 kerberos, o protocolo NTLM é usado.

* **Protocolo de autenticação NTLM**
    * O protocolo NTLM é um protocolo clássico de autenticação de rede utilizado por sistemas operativos mais antigos. Por razões de compatibilidade, é utilizado por domínios Ative Directy para processar pedidos de autenticação de rede que provêm de aplicações concebidas para clientes e servidores anteriores baseados no Windows e sistemas operativos de terceiros.
    * Quando o protocolo NTLM é utilizado entre um cliente e um servidor, o servidor deve contactar um serviço de autenticação de domínio num DC para verificar as credenciais do cliente. O servidor autentica o cliente reencaminhando as credenciais do cliente para um DC no domínio da conta cliente.
    * Quando dois domínios ou florestas de Diretório Ativo estão ligados por um fundo fiduciário, os pedidos de autenticação feitos utilizando estes protocolos podem ser encaminhados para fornecer acesso aos recursos em ambas as florestas.

## <a name="authorization-and-access-control"></a>Controlo de autorização e acesso

As tecnologias de autorização e confiança trabalham em conjunto para fornecer uma infraestrutura de comunicações segura em domínios ou florestas de DirectórioActivo. A autorização determina qual o nível de acesso que um utilizador tem aos recursos num domínio. Os trusts facilitam a autorização transversal dos utilizadores, fornecendo um caminho para autenticar utilizadores noutros domínios para que os seus pedidos de recursos partilhados nesses domínios possam ser autorizados.

Quando um pedido de autenticação feito num domínio de confiança é validado pelo domínio fidedigno, é passado para o recurso-alvo. O recurso-alvo determina então se autoriza o pedido específico feito pelo utilizador, serviço ou computador no domínio fidedigno com base na sua configuração de controlo de acesso.

Os trusts fornecem este mecanismo para validar pedidos de autenticação que são passados para um domínio de confiança. Os mecanismos de controlo de acesso no computador de recurso determinam o nível final de acesso concedido ao solicitador no domínio fidedigno.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os fidedignos, veja como funcionam os [fundos da floresta em Azure AD DS?][concepts-trust]

Para começar com a criação de um domínio gerido azure AD DS com uma floresta de recursos, consulte [Criar e configurar um domínio gerido por Azure AD DS][tutorial-create-advanced]. Em seguida, pode [criar uma confiança florestal de saída para um domínio no local (pré-visualização)][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
