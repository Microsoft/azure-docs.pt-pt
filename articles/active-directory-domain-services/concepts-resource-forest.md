---
title: Conceitos de floresta de recursos para Azure AD Domain Services | Microsoft Docs
description: Saiba o que é uma floresta de recursos em Azure Active Directory Domain Services e como elas beneficiam sua organização no ambiente híbrido com opções de autenticação de usuário limitadas ou questões de segurança.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: a583e32cbc3d58d5dfc5616335b2f38ad20fac14
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233613"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Recursos e conceitos de floresta de recursos para Azure Active Directory Domain Services

O Azure Active Directory Domain Services (AD DS) fornece uma experiência de entrada para aplicativos de linha de negócios herdados e locais. Os usuários, grupos e hashes de senha de usuários locais e na nuvem são sincronizados com o domínio gerenciado AD DS do Azure. Esses hashes de senha sincronizados são o que fornece aos usuários um único conjunto de credenciais que eles podem usar para o AD DS local, o Office 365 e o Azure Active Directory.

Embora seguro e forneça benefícios de segurança adicionais, algumas organizações não podem sincronizar os hashes de senhas de usuário para o Azure AD ou o Azure AD DS. Os usuários em uma organização podem não saber sua senha porque usam apenas a autenticação de cartão inteligente. Essas limitações impedem que algumas organizações usem o AD DS do Azure para migrar e deslocar aplicativos clássicos locais para o Azure.

Para atender a essas necessidades e restrições, você pode criar um domínio gerenciado AD DS do Azure que usa uma floresta de recursos. Este artigo conceitual explica quais são as florestas e como elas confiam em outros recursos para fornecer um método de autenticação seguro. Atualmente, as florestas de recursos do Azure AD DS estão em versão prévia.

> [!IMPORTANT]
> Atualmente, as florestas de recursos do Azure AD DS não dão suporte a arquivos Azure HDInsight ou Azure. As florestas de usuário padrão do Azure AD DS dão suporte a esses dois serviços adicionais.

## <a name="what-are-forests"></a>O que são florestas?

Uma *floresta* é uma construção lógica usada por Active Directory Domain Services (AD DS) para agrupar um ou mais *domínios*. Em seguida, os domínios armazenam objetos para usuários ou grupos e fornecem serviços de autenticação.

No Azure AD DS, a floresta contém apenas um domínio. As florestas AD DS locais geralmente contêm muitos domínios. Em grandes organizações, especialmente depois de fusões e aquisições, você pode acabar com várias florestas locais que contêm vários domínios.

Por padrão, um domínio gerenciado do Azure AD DS é criado como uma floresta de *usuário* . Esse tipo de floresta sincroniza todos os objetos do Azure AD, incluindo qualquer conta de usuário criada em um ambiente de AD DS local. As contas de usuário podem se autenticar diretamente no domínio gerenciado AD DS do Azure, como para entrar em uma VM ingressada no domínio. Uma floresta de usuário funciona quando os hashes de senha podem ser sincronizados e os usuários não estão usando métodos de entrada exclusivos, como a autenticação de cartão inteligente.

Em uma floresta de *recursos* do Azure AD DS, os usuários se autenticam por meio de uma *relação de confiança* de floresta unidirecional de suas AD DS locais. Com essa abordagem, os objetos de usuário e os hashes de senha não são sincronizados com o Azure AD DS. Os objetos de usuário e as credenciais existem somente no AD DS local. Essa abordagem permite que as empresas hospedem recursos e plataformas de aplicativos no Azure que dependem da autenticação clássica, tais LDAPs, Kerberos ou NTLM, mas quaisquer problemas de autenticação ou preocupações são removidos. Atualmente, as florestas de recursos do Azure AD DS estão em versão prévia.

As florestas de recursos também fornecem a capacidade de mover e deslocar seus aplicativos um componente por vez. Muitos aplicativos locais herdados são de várias camadas, geralmente usando um servidor Web ou front-end e muitos componentes relacionados ao banco de dados. Essas camadas dificultam a elevação e a mudança do aplicativo inteiro para a nuvem em uma única etapa. Com as florestas de recursos, você pode levantar seu aplicativo para a nuvem em uma abordagem em fases, o que facilita a movimentação de seu aplicativo para o Azure.

## <a name="what-are-trusts"></a>O que são relações de confiança?

As organizações que têm mais de um domínio geralmente precisam que os usuários acessem recursos compartilhados em um domínio diferente. O acesso a esses recursos compartilhados requer que os usuários em um domínio se autentiquem em outro domínio. Para fornecer esses recursos de autenticação e autorização entre clientes e servidores em domínios diferentes, deve haver uma *relação de confiança* entre os dois domínios.

Com as relações de confiança de domínio, os mecanismos de autenticação para cada domínio confiam nas autenticações provenientes do outro domínio. As relações de confiança ajudam a fornecer acesso controlado a recursos compartilhados em um domínio de recurso (o domínio *confiante* ), verificando se as solicitações de autenticação de entrada são provenientes de uma autoridade confiável (o domínio *confiável* ). As relações de confiança atuam como pontes que só permitem a viagem de solicitações de autenticação validadas entre domínios.

Como uma relação de confiança passa solicitações de autenticação depende de como ela está configurada. As relações de confiança podem ser configuradas de uma das seguintes maneiras:

* **Unidirecional** – fornece acesso do domínio confiável para recursos no domínio confiante.
* **Bidirecional** – fornece acesso de cada domínio aos recursos no outro domínio.

As relações de confiança também são configuradas para lidar com relações de confiança adicionais de uma das seguintes maneiras:

* Não- **transitório** – a relação de confiança existe somente entre os dois domínios de parceiro de confiança.
* **Transitiva** -a confiança se estende automaticamente a quaisquer outros domínios nos quais um dos parceiros confie.

Em alguns casos, as relações de confiança são automaticamente estabelecidas quando os domínios são criados. Em outras ocasiões, você deve escolher um tipo de confiança e estabelecer explicitamente as relações apropriadas. Os tipos específicos de confianças usados e a estrutura dessas relações de confiança dependem de como o serviço de diretório Active Directory é organizado e se diferentes versões do Windows coexistem na rede.

## <a name="trusts-between-two-forests"></a>Relações de confiança entre duas florestas

Você pode estender as relações de confiança de domínio em uma única floresta para outra floresta criando manualmente uma relação de confiança de floresta unidirecional ou bidirecional. Uma relação de confiança de floresta é uma relação de confiança transitiva que existe somente entre um domínio raiz de floresta e um segundo domínio raiz de floresta.

* Uma relação de confiança de floresta unidirecional permite que todos os usuários em uma floresta confiem em todos os domínios da outra floresta.
* Uma confiança de floresta bidirecional forma uma relação de confiança transitiva entre cada domínio em ambas as florestas.

A transitividade de relações de confiança de floresta é limitada aos dois parceiros de floresta. A relação de confiança de floresta não se estende a florestas adicionais confiáveis por qualquer um dos parceiros.

![Diagrama de confiança de floresta do Azure AD DS para o local AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Você pode criar diferentes configurações de confiança de domínio e floresta dependendo da estrutura de Active Directory da organização. O Azure AD DS dá suporte apenas a uma relação de confiança unidirecional. Nessa configuração, os recursos no Azure AD DS podem confiar em todos os domínios em uma floresta local.

## <a name="supporting-technology-for-trusts"></a>Tecnologia de suporte para relações de confiança

As relações de confiança usam vários serviços e recursos, como o DNS, para localizar controladores de domínio em florestas de parceiros. As relações de confiança também dependem de protocolos de autenticação NTLM e Kerberos e de autorização baseada em Windows e mecanismos de controle de acesso para ajudar a fornecer uma infraestrutura de comunicações segura entre Active Directory domínios e florestas. Os seguintes serviços e recursos ajudam a dar suporte a relações de confiança bem-sucedidas.

### <a name="dns"></a>DNS

AD DS precisa de DNS para o local e a nomenclatura do DC (controlador de domínio). O seguinte suporte do DNS é fornecido para AD DS funcionar com êxito:

* Um serviço de resolução de nomes que permite que os hosts de rede e serviços localizem DCs.
* Uma estrutura de nomenclatura que permite que uma empresa reflita sua estrutura organizacional nos nomes de seus domínios de serviço de diretório.

Um namespace de domínio DNS geralmente é implantado que espelha o namespace de domínio AD DS. Se houver um namespace DNS existente antes da implantação de AD DS, o namespace DNS será normalmente particionado para Active Directory, e um subdomínio e delegação de DNS para a raiz da floresta Active Directory será criado. Nomes de domínio DNS adicionais são então adicionados para cada Active Directory domínio filho.

O DNS também é usado para dar suporte ao local de DCs do Active Directory. As zonas DNS são preenchidas com registros de recursos DNS que permitem que os serviços e hosts de rede localizem Active Directory DCs.

### <a name="applications-and-net-logon"></a>Aplicativos e logon de rede

Os aplicativos e o serviço de logon de rede são componentes do modelo de canal de segurança distribuída do Windows. Os aplicativos integrados ao Windows Server e Active Directory usam protocolos de autenticação para se comunicarem com o serviço de logon de rede para que um caminho protegido possa ser estabelecido em relação à qual a autenticação pode ocorrer.

### <a name="authentication-protocols"></a>Protocolos de Autenticação

Active Directory DCs autenticam usuários e aplicativos usando um dos seguintes protocolos:

* **Protocolo de autenticação Kerberos versão 5**
    * O protocolo Kerberos versão 5 é o protocolo de autenticação padrão usado por computadores locais que executam o Windows e oferecem suporte a sistemas operacionais de terceiros. Esse protocolo é especificado no RFC 1510 e é totalmente integrado com Active Directory, protocolo SMB, HTTP e RPC (chamada de procedimento remoto), bem como os aplicativos cliente e servidor que usam esses protocolos.
    * Quando o protocolo Kerberos é usado, o servidor não precisa entrar em contato com o controlador de domínio. Em vez disso, o cliente recebe um tíquete para um servidor solicitando um de um DC no domínio da conta do servidor. Em seguida, o servidor valida o tíquete sem consultar nenhuma outra autoridade.
    * Se qualquer computador envolvido em uma transação não oferecer suporte ao protocolo Kerberos versão 5, o protocolo NTLM será usado.

* **Protocolo de autenticação NTLM**
    * O protocolo NTLM é um protocolo de autenticação de rede clássico usado por sistemas operacionais mais antigos. Por motivos de compatibilidade, ele é usado por Active Directory domínios para processar solicitações de autenticação de rede provenientes de aplicativos criados para clientes e servidores baseados no Windows anteriores e sistemas operacionais de terceiros.
    * Quando o protocolo NTLM é usado entre um cliente e um servidor, o servidor deve contatar um serviço de autenticação de domínio em um DC para verificar as credenciais do cliente. O servidor autentica o cliente encaminhando as credenciais do cliente para um DC no domínio da conta do cliente.
    * Quando dois domínios Active Directory ou florestas são conectados por uma relação de confiança, as solicitações de autenticação feitas usando esses protocolos podem ser roteadas para fornecer acesso aos recursos em ambas as florestas.

## <a name="authorization-and-access-control"></a>Autorização e controle de acesso

As tecnologias de autorização e confiança funcionam em conjunto para fornecer uma infraestrutura de comunicações protegida entre Active Directory domínios ou florestas. A autorização determina o nível de acesso que um usuário tem aos recursos em um domínio. As relações de confiança facilitam a autorização entre domínios de usuários fornecendo um caminho para autenticar usuários em outros domínios para que suas solicitações a recursos compartilhados nesses domínios possam ser autorizadas.

Quando uma solicitação de autenticação feita em um domínio confiável é validada pelo domínio confiável, ela é passada para o recurso de destino. O recurso de destino então determina se deve autorizar a solicitação específica feita pelo usuário, serviço ou computador no domínio confiável com base em sua configuração de controle de acesso.

As relações de confiança fornecem esse mecanismo para validar solicitações de autenticação que são passadas para um domínio confiável. Mecanismos de controle de acesso no computador de recursos determinam o nível final de acesso concedido ao solicitante no domínio confiável.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre relações de confiança, consulte [como as relações de confiança de floresta funcionam no Azure AD DS?][concepts-trust]

Para começar a criar um domínio gerenciado do Azure AD DS com uma floresta de recursos, consulte [criar e configurar um domínio gerenciado do azure AD DS][tutorial-create-advanced]. Em seguida, você pode [criar uma relação de confiança de floresta de saída para um domínio local (versão prévia)][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
