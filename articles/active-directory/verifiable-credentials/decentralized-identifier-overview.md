---
title: Introdução ao Diretório Ativo Azure Credenciais Verificáveis (pré-visualização)
description: Uma visão geral Azure Verifiable Credenciais.
services: active-directory
author: barclayn
manager: daveba
editor: ''
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: overview
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 04b36b9b32e78016f693e61d40246776492be0e3
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222865"
---
# <a name="introduction-to-azure-active-directory-verifiable-credentials-preview"></a>Introdução ao Diretório Ativo Azure Credenciais Verificáveis (Pré-visualização)

> [!IMPORTANT]
> Azure Ative Directory Verifiable Credentials está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

As nossas vidas digitais e físicas estão cada vez mais ligadas às apps, serviços e dispositivos que usamos para aceder a um rico conjunto de experiências. Esta transformação digital permite-nos interagir com centenas de empresas e milhares de outros utilizadores de formas que antes eram inimagináveis.

Mas os dados de identidade têm sido muitas vezes expostos em falhas de segurança. Estas violações são impactantes para a vida das pessoas que afetam a nossa vida social, profissional e financeira. A Microsoft acredita que há uma maneira melhor. Cada pessoa tem direito a uma identidade que possui e controla, que armazena de forma segura elementos da sua identidade digital e preserva a privacidade. Este primer explica como estamos a unir as mãos a uma comunidade diversificada para construir uma solução aberta, confiável, interoperável e baseada em padrões de Identidade Descentralizada (DID) para indivíduos e organizações.

## <a name="why-we-need-decentralized-identity"></a>Por que precisamos de identidade descentralizada

Hoje usamos a nossa identidade digital no trabalho, em casa, e em todas as aplicações, serviços e dispositivos que utilizamos. É composto por tudo o que dizemos, fazemos e experimentamos nas nossas vidas — comprar bilhetes para um evento, hospedar-se num hotel ou até mesmo pedir almoço. Atualmente, a nossa identidade e todas as nossas interações digitais são propriedade e controladas por outros partidos, alguns dos quais nem sequer conhecemos.

Geralmente, os utilizadores concedem consentimento a várias apps e dispositivos. Esta abordagem requer um elevado grau de vigilância por parte do utilizador para rastrear quem tem acesso a que informação. Na frente empresarial, a colaboração com consumidores e parceiros requer orquestração de alto toque para trocar de forma segura dados de forma a manter a privacidade e a segurança para todos os envolvidos.

Acreditamos que um sistema de identidade descentralizada baseado em padrões pode desbloquear um novo conjunto de experiências que dão aos utilizadores e organizações um maior controlo sobre os seus dados e fornecer um maior grau de confiança e segurança para apps, dispositivos e fornecedores de serviços

## <a name="lead-with-open-standards"></a>Chumbo com padrões abertos

Estamos empenhados em trabalhar em estreita colaboração com clientes, parceiros e comunidade para desbloquear a próxima geração de experiências baseadas em Identidade Descentralizada, e estamos entusiasmados em associar-nos com os indivíduos e organizações que estão a fazer contribuições incríveis neste espaço. Para que o ecossistema DID cresça, as normas, os componentes técnicos e os resultados dos códigos devem ser de código aberto e acessíveis a todos.

A Microsoft está a colaborar ativamente com membros da Fundação de Identidade Descentralizada (DIF), do Grupo Comunitário de Credenciais W3C e da comunidade de identidade mais ampla. Trabalhamos com estes grupos para identificar e desenvolver padrões críticos, e os seguintes padrões foram implementados nos nossos serviços.

* [Identificadores Descentralizados W3C](https://www.w3.org/TR/did-core/)
* [Credenciais verificáveis W3C](https://www.w3.org/TR/vc-data-model/)
* [DIF Sidetree](https://identity.foundation/sidetree/spec/)
* [Configuração DID bem conhecida do DIF](https://identity.foundation/specs/did-configuration/)
* [DIF DID-SIOP](https://identity.foundation/did-siop/)
* [Troca de Apresentação dif](https://identity.foundation/presentation-exchange/)


## <a name="what-are-dids"></a>O que são DIDs?

Antes de compreendermos os DIDs, ajuda a compará-los com os sistemas de identidade atuais. Os endereços de e-mail e os IDs da rede social são pseudónimos amigos do homem para colaboração, mas estão agora sobrecarregados para servir como pontos de controlo para o acesso de dados em muitos cenários além da colaboração. Isto cria um problema potencial, porque o acesso a estes IDs pode ser removido a qualquer momento por partes externas.

Os identificadores descentralizados (DIDs) são diferentes. Os DIDs são identificadores gerados pelo utilizador, auto-propriedade, globalmente únicos, enraizados em sistemas descentralizados como o ION. Possuem características únicas, como uma maior garantia de imutabilidade, resistência à censura e evasivação adulterada. Estes atributos são fundamentais para qualquer sistema de ID que se destinam a fornecer auto-propriedade e controlo do utilizador. 

A solução credencial verificável da Microsoft usa credenciais descentralizadas (DIDs) para assinar criptograficamente como prova de que um partido dependente (verificador) está a atestar informações que comprovam que são os proprietários de uma credencial verificável. Portanto, recomenda-se uma compreensão básica dos identificadores descentralizados para qualquer pessoa que crie uma solução credencial verificável baseada na oferta da Microsoft.
## <a name="what-are-verifiable-credentials"></a>O que são credenciais verificáveis?

 Usamos identificações no nosso dia-a-dia. Temos cartas de condução que usamos como prova da nossa capacidade de operar um carro. As universidades emitem diplomas que provam que atingimos um nível de educação. Utilizamos passaportes para provar quem somos às autoridades à medida que chegamos a outros países. O modelo de dados descreve como poderíamos lidar com este tipo de cenários ao trabalhar através da internet, mas de uma forma segura que respeite a privacidade dos utilizadores. Pode obter informações adicionais no [Modelo de Dados de Credenciais Verificáveis 1.0](https://www.w3.org/TR/vc-data-model/)

Em suma, as credenciais verificáveis são objetos de dados constituídos por alegações feitas pelo emitente que atestam informações sobre um assunto. Estas alegações são identificadas por esquema e incluem o emitente e o sujeito. O DID do emitente cria uma assinatura digital como prova de que atestam esta informação.


## <a name="how-does-decentralized-identity-work"></a>Como funciona a Identidade Descentralizada?

Precisamos de uma nova forma de identidade. Precisamos de uma identidade que reúna tecnologias e padrões para fornecer atributos identitários fundamentais como a auto-propriedade e a resistência à censura. Estas capacidades são difíceis de conseguir utilizando sistemas existentes.

Para cumprir estas promessas, precisamos de uma base técnica composta por sete inovações fundamentais. Uma inovação fundamental são os identificadores que são propriedade do utilizador, um agente de utilizadores para gerir chaves associadas a esses identificadores, e lojas de dados encriptadas e controladas pelo utilizador.

![visão geral do ambiente credencial verificável da Microsoft](media/decentralized-identifier-overview/microsoft-did-system.png)

**1. W3C Identificadores Descentralizados (DIDs)** Os utilizadores criam, possuem e controlam independentemente de qualquer organização ou governo. Os DIDs são identificadores globalmente exclusivos ligados a metadados descentralizados de infraestruturas de chaves públicas (DPKI) compostos por documentos JSON que contêm material chave público, descritores de autenticação e pontos finais de serviço.

**2. Sistema descentralizado: ION (Rede de Sobreposição de Identidade)** ION é uma rede aberta e sem permissão da Camada 2 baseada no protocolo Sidetree puramente determinístico, que não requer tokens especiais, validadores fidedignos ou outros mecanismos de consenso; a progressão linear da cadeia de tempo de Bitcoin é tudo o que é necessário para o seu funcionamento. Temos [um pacote npm aberto](https://www.npmjs.com/package/@decentralized-identity/ion-tools) para tornar o trabalho com a rede ION fácil de integrar nas suas apps e serviços. As bibliotecas incluem a criação de um novo DID, gerar chaves e ancorar o seu DID na blockchain Bitcoin. 

**3. Agente/Carteira do Utilizador DID: A aplicação do autenticador microsoft** permite que pessoas reais utilizem identidades descentralizadas e credenciais verificáveis. O autenticador cria DIDs, facilita pedidos de emissão e apresentação de credenciais verificáveis e gere a cópia de segurança da semente do seu DID através de um ficheiro de carteira encriptado.

**4. Microsoft Resolver** Uma API que se conecta ao nosso nó ION para procurar e resolver dIDs utilizando o ```did:ion``` método e devolver o Objeto de Documento DID (DDO). O DDO inclui metadados DPKI associados ao DID, tais como chaves públicas e pontos finais de serviço. 

**5. Serviço de Credenciais Verificadas Azure Ative Um Serviço** de Emissão e Verificação da API e SDK de código aberto para [credenciais verificáveis W3C](https://www.w3.org/TR/vc-data-model/) que são assinados com o ```did:ion``` método. Permitem que os proprietários de identidades gerem, apresentem e verifiquem as reclamações. Isto constitui a base de confiança entre os utilizadores dos sistemas.

## <a name="a-sample-scenario"></a>Um cenário de amostra

O cenário que usamos para explicar como os VCs funcionam envolve:

- Woodgrove Inc. uma empresa.
- Proseware, uma empresa que oferece descontos aos colaboradores da Woodgrove.
- Alice, uma funcionária da Woodgrove, Inc. que quer um desconto da Proseware



Hoje, Alice fornece um nome de utilizador e senha para iniciar sessão no ambiente em rede da Woodgrove. A Woodgrove está a implementar uma solução de VC para fornecer uma forma mais manejável para a Alice provar que é uma funcionária da Woodgrove. A Proseware está a usar uma solução VC compatível com a solução de VC da Woodgrove e aceitam credenciais emitidas pela Woodgrove como prova de emprego.

O emitente da credencial, Woodgrove Inc., cria uma chave pública e uma chave privada. A chave pública está armazenada no ION. Quando a chave é adicionada à infraestrutura, a entrada é registada num livro-razão descentralizado baseado em blockchain. O emitente fornece a Alice a chave privada que está armazenada numa aplicação de carteira. Cada vez que Alice usa com sucesso a chave privada, a transação é registada na aplicação da carteira.

![microsoft-did-overview](media/decentralized-identifier-overview/did-overview.png)

## <a name="roles-in-a-verifiable-credential-solution"></a>Funções numa solução credencial verificável 

Há três atores primários na solução credencial verificável. No seguinte diagrama:

- **Passo 1**, o **utilizador** solicita uma credencial verificável a um emitente.
- **Passo 2**, o **emitente** da credencial atesta que a prova que o utilizador forneceu é exata e cria uma credencial verificável assinada com o seu DID e o DID do utilizador é o assunto.
- **No passo 3,** o utilizador assina uma apresentação verificável (VP) com o seu DID e envia para o **verificador.** O verificador valida então a credencial, combinando com a chave pública colocada no DPKI.

Os papéis neste cenário são:

![papéis num ambiente credencial verificável](media/decentralized-identifier-overview/issuer-user-verifier.png)

**emitente** – O emitente é uma organização que cria uma solução de emissão solicitando informações de um utilizador. A informação é utilizada para verificar a identidade do utilizador. Por exemplo, a Woodgrove, Inc. tem uma solução de emissão que lhes permite criar e distribuir credenciais verificáveis (VCs) a todos os seus colaboradores. O funcionário utiliza a aplicação Authenticator para iniciar sômata com o seu nome de utilizador e senha, que passa um sinal de ID para o serviço de emissão. Uma vez que a Woodgrove, Inc. valida o token de ID apresentado, a solução de emissão cria um VC que inclui reclamações sobre o empregado e é assinado com a Woodgrove, Inc. DID. O trabalhador tem agora uma credencial verificável que é assinada pelo seu empregador, que inclui os empregados DID como o sujeito DID.  

**utilizador** – O utilizador é a pessoa ou entidade que está a solicitar um VC. Por exemplo, Alice é uma nova funcionária da Woodgrove, Inc. e foi previamente emitida a sua prova de credencial verificável de emprego. Quando Alice precisa de apresentar provas de emprego para obter um desconto na Proseware, ela pode conceder acesso à credencial na sua app Authenticator, assinando uma apresentação verificável que prove que Alice é a proprietária do DID. A Proseware é capaz de validar a credencial emitida pela Woodgrove, Inc.e Alice é a proprietária da credencial. 

**verificador** – O verificador é uma empresa ou entidade que precisa de verificar reclamações de um ou mais emitentes em que confiam. Por exemplo, a Proseware confia que a Woodgrove, Inc. faz um trabalho adequado de verificar a identidade dos seus colaboradores e emitir VCs autênticos e válidos. Quando Alice tentar encomendar o equipamento de que precisa para o seu trabalho, a Proseware usará padrões abertos como SIOP e Bolsa de Apresentação para solicitar credenciais ao Utilizador provando que são funcionários da Woodgrove, Inc. Por exemplo, a Proseware pode fornecer à Alice um link para um website com um código QR que ela verifica com a câmara do telefone. Isto inicia o pedido de um VC específico, que a Authenticator irá analisar e dar a Alice a capacidade de aprovar o pedido para provar o seu emprego à Proseware. O Proseware pode utilizar o serviço de credenciais verificáveis API ou SDK, para verificar a autenticidade da apresentação verificável. Com base nas informações fornecidas pela Alice, dão à Alice o desconto. Se outras empresas e organizações souberem que a Woodgrove, Inc. emite VCs aos seus colaboradores, também podem criar uma solução de verificação e utilizar a credencial verificável da Woodgrove, Inc. para oferecer ofertas especiais reservadas aos colaboradores da Woodgrove, Inc.

## <a name="next-steps"></a>Passos seguintes

Agora que sabe sobre DIDs e credenciais verificáveis, experimente-os seguindo o nosso artigo de início ou um dos nossos artigos fornecendo mais detalhes sobre conceitos de credenciais verificáveis.

- [Começar com credenciais verificáveis](get-started-verifiable-credentials.md)
- [Como personalizar as suas credenciais](credential-design.md)
- [Credenciais verificáveis FAQ](verifiable-credentials-faq.md)