---
title: Perguntas frequentes - Azure Verifiable Credentials (pré-visualização)
description: Encontre respostas a perguntas comuns sobre credenciais verificáveis
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 3c43cfb537c84fb25a6bf879d32a8034342ff605
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280722"
---
# <a name="frequently-asked-questions-faq"></a>Perguntas Frequentes (FAQ)

Esta página contém perguntas comumente sobre credenciais verificáveis e identidade descentralizada. As perguntas são organizadas nas seguintes secções.

- [Vocabulário e básicos](#the-basics)
- [Questões conceptuais sobre identidade descentralizada](#conceptual-questions)
- [Perguntas sobre a utilização da pré-visualização de Credenciais Verificáveis](#using-the-preview)

> [!IMPORTANT]
> Azure Ative Directory Verifiable Credentials está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="the-basics"></a>Noções básicas

### <a name="what-is-a-did"></a>O que é um DID? 

Os identificadores de identificação descentralizada (DIDs) são identificadores que podem ser utilizados para garantir o acesso aos recursos, assinar e verificar credenciais e facilitar a troca de dados de aplicação. Ao contrário dos nomes de utilizador tradicionais e endereços de e-mail, os DIDs são propriedade e controlados pela própria entidade (seja uma pessoa, dispositivo ou empresa). Os DIDs existem independentemente de qualquer organização externa ou intermediário de confiança. [A especificação do identificador descentralizado W3C](https://www.w3.org/TR/did-core/) explica-o mais detalhadamente.

### <a name="why-do-we-need-a-did"></a>Por que precisamos de um DID?

A confiança digital exige fundamentalmente que os participantes possuam e controlem as suas identidades, e a identidade começa no identificador.
Numa era diária, violações e ataques de sistemas de grande escala a potes de mel identificadores centralizados, a identidade descentralizada está a tornar-se uma necessidade crítica de segurança para os consumidores e as empresas.
Os indivíduos que possuem e controlam as suas identidades são capazes de trocar dados e provas verificáveis. Um ambiente de credencial distribuído permite a automatização de muitos processos de negócio que são atualmente manuais e de mão de obra intensiva.

### <a name="what-is-a-verifiable-credential"></a>O que é uma credencial verificável? 

As credenciais fazem parte do nosso dia-a-dia; as cartas de condução são utilizadas para afirmar que somos capazes de operar um veículo a motor, que os diplomas universitários podem ser usados para afirmar o nosso nível de educação, e os passaportes emitidos pelo governo permitem-nos viajar entre países. As Credenciais Verificáveis fornecem um mecanismo para expressar este tipo de credenciais na Web de uma forma que é criptograficamente segura, respeitando a privacidade e verificável pela máquina. [A especificação de credenciais verificáveis W3C](https://www.w3.org/TR/vc-data-model//) explica-o mais detalhadamente.


## <a name="conceptual-questions"></a>Questões conceptuais

### <a name="what-happens-when-a-user-loses-their-phone-can-they-recover-their-identity"></a>O que acontece quando um utilizador perde o telemóvel? Podem recuperar a sua identidade?

Existem múltiplas formas de oferecer um mecanismo de recuperação aos utilizadores, cada um com as suas próprias trocas. Estamos atualmente a avaliar opções e a desenhar abordagens de recuperação que oferecem comodidade e segurança, respeitando a privacidade e a auto-soberania de um utilizador.

### <a name="how-can-a-user-trust-a-request-from-an-issuer-or-verifier-how-do-they-know-a-did-is-the-real-did-for-an-organization"></a>Como pode um utilizador confiar num pedido de um emitente ou verificador? Como é que eles sabem que um DID é o verdadeiro DID para uma organização?

Implementámos [a especificação de configuração did bem conhecida da Fundação de Identidade Descentralizada,](https://identity.foundation/.well-known/resources/did-configuration/) a fim de ligar um DID a um sistema altamente conhecido, nomes de domínio. Cada DID criado utilizando o Azure Ative Directory Verifiable Credentials tem a opção de incluir um nome de domínio raiz que será codificado no Documento DID. Siga o artigo intitulado [Link your Domain to your Distributed Identifier](how-to-dnsbind.md) para saber mais.  

### <a name="why-does-the-verifiable-credential-preview-use-ion-as-its-did-method-and-therefore-bitcoin-to-provide-decentralized-public-key-infrastructure"></a>Porque é que a pré-visualização da Credencial Verificável utiliza o ION como método DID e, portanto, a Bitcoin para fornecer infraestruturas de chave pública descentralizadas?

ION é uma rede descentralizada de identificador descentralizado e descentralizada expansível Camada 2 que funciona no topo da Bitcoin. Alcança a escalabilidade sem incluir um símbolo criptoativo especial, validadores fidedignos ou mecanismos de consenso centralizados. Utilizamos o Bitcoin para o substrato base da Camada 1 devido à força da rede descentralizada para proporcionar um alto grau de imutabilidade para um sistema de registo de eventos cronológicos.

## <a name="using-the-preview"></a>Usando a pré-visualização

### <a name="why-must-i-use-nodejs-for-the-verifiable-credentials-preview-any-plans-for-other-programming-languages"></a>Por que devo usar NodeJS para a pré-visualização de Credenciais Verificáveis? Algum plano para outras linguagens de programação? 

Escolhemos o NodeJS porque é uma plataforma muito popular para desenvolvedores de aplicações. Vamos lançar uma API de Repouso que permitirá aos desenvolvedores emitir e verificar credenciais. 

### <a name="is-any-of-the-code-used-in-the-preview-open-source"></a>Algum do código é usado na fonte aberta de pré-visualização?

Sim! Os seguintes repositórios são os componentes de origem aberta dos nossos serviços.

1. [SideTree, no GitHub](https://github.com/decentralized-identity/sidetree)
2. O [VC SDK para nó, no GitHub](https://github.com/microsoft/VerifiableCredentials-Verification-SDK-Typescript)
3. Um [Android SDK para construir carteiras de identidade descentralizadas, no GitHub](https://github.com/microsoft/VerifiableCredential-SDK-Android)
4. Um [iOS SDK para construir carteiras de identidade descentralizadas, no GitHub](https://github.com/microsoft/VerifiableCredential-SDK-iOS)


## <a name="what-are-the-licensing-requirements"></a>Quais são os requisitos de licenciamento?

Uma licença Azure AD P2 é necessária para utilizar a pré-visualização de Credenciais Verificáveis. Este é um requisito temporário, pois esperamos que os preços para este serviço sejam cobrados com base no uso. 


## <a name="next-steps"></a>Passos seguintes

- [Como personalizar o seu Diretório Ativo Azure Credenciais Verificáveis](credential-design.md)
