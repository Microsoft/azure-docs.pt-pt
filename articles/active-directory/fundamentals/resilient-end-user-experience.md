---
title: Experiência resiliente de utilizador final usando Azure AD B2C | Microsoft Docs
description: Métodos para construir resiliência na experiência do utilizador final usando Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6896a812ec173994a1299a28ff2e99a0f351391
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724446"
---
# <a name="resilient-end-user-experience"></a>Experiência resiliente do utilizador final

A experiência de inscrição e de inscrição no utilizador final é composta pelos seguintes elementos:

- As interfaces com as que o utilizador interage – tais como CSS, HTML e JavaScript

- Os fluxos de utilizador e as políticas personalizadas que cria – tais como inscrição, inscrição e edição de perfil

- Os fornecedores de identidade (IDPs) para a sua aplicação – como nome de utilizador/palavra-passe de conta local, Outlook, Facebook e Google

![Imagem mostra componentes de experiência do utilizador final](media/resilient-end-user-experiences/end-user-experience-architecture.png)

## <a name="choose-between-user-flow-and-custom-policy"></a>Escolha entre o fluxo do utilizador e a política personalizada  

Para ajudá-lo a configurar as tarefas de identidade mais comuns, o Azure AD B2C fornece fluxos de utilizador configuráveis [incorporados](../../active-directory-b2c/user-flow-overview.md). Você também pode construir suas próprias [políticas personalizadas,](../../active-directory-b2c/custom-policy-overview.md)que lhe oferecem a máxima flexibilidade. No entanto, é recomendado usar políticas personalizadas apenas para abordar cenários complexos.

### <a name="how-to-decide-between-user-flow-and-custom-policy"></a>Como decidir entre o fluxo do utilizador e a política personalizada

Escolha fluxos de utilizador incorporados se os seus requisitos de negócio puderem ser cumpridos por eles. Uma vez amplamente testado pela Microsoft, pode minimizar os testes necessários para validar o nível de aplicação funcional, desempenho ou escala destes fluxos de utilizador de identidade. Ainda precisa de testar as suas aplicações para funcionalidade, desempenho e escala.

Caso [escolha políticas personalizadas](../../active-directory-b2c/custom-policy-get-started.md) devido aos requisitos do seu negócio, certifique-se de que realiza testes de nível de política para testes funcionais, de desempenho ou escala, além de testes ao nível da aplicação.

Veja o artigo que [compara fluxos de utilizadores e polícias personalizados](../../active-directory-b2c/custom-policy-overview.md#comparing-user-flows-and-custom-policies) para ajudá-lo a decidir.

## <a name="choose-multiple-idps"></a>Escolha vários IDPs

Quando utilizar um [fornecedor de identidade externo,](../../active-directory-b2c/technical-overview.md#external-identity-providers) como o Facebook, certifique-se de ter um plano de recuo no caso de o fornecedor externo ficar indisponível.

### <a name="how-to-set-up-multiple-idps"></a>Como configurar vários IDPs

Como parte do processo de registo do fornecedor de identidade externo, inclua uma reclamação de identidade verificada, como o número de telemóvel do utilizador ou o endereço de e-mail. Empenhar os pedidos verificados no diretório Azure AD B2C subjacente. Se o fornecedor externo não estiver disponível, volte à reclamação de identidade verificada e volte ao número de telefone como método de autenticação. Outra opção é enviar ao utilizador uma senha única para permitir que o utilizador entre..

 Siga estes passos para [construir caminhos alternativos de autenticação:](https://github.com/azure-ad-b2c/samples/tree/master/policies/idps-filter)

 1. Configure a sua política de inscrição para permitir a inscrição por conta local e IDPs externos.

 2. Configurar uma política de perfil para permitir que os utilizadores [liguem a outra identidade à sua conta](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/account-linking) depois de iniciarem a sua sessão.

 3. Notifique e permita que os utilizadores [mudem para um IDP alternativo](../../active-directory-b2c/customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) durante uma paragem.

## <a name="availability-of-multi-factor-authentication"></a>Disponibilidade de autenticação multi-factor

Ao utilizar um [serviço telefónico para autenticação multi-factor (MFA), certifique-se](../../active-directory-b2c/phone-authentication.md)de considerar um prestador de serviços alternativo. O telco local ou o prestador de serviços telefónicos podem sofrer interrupções no seu serviço.

### <a name="how-to-choose-an-alternate-mfa"></a>Como escolher um MFA alternativo  

O serviço Azure AD B2C utiliza um fornecedor de MFA baseado em telefone incorporado, para fornecer códigos de acesso únicos (OTPs) baseados no tempo. É sob a forma de uma chamada de voz e mensagem de texto para o número de telefone pré-registado do utilizador. Estão disponíveis os seguintes métodos alternativos para vários cenários:

Ao utilizar **os fluxos dos utilizadores,** existem dois métodos para construir resiliência:

- Alterar a **configuração** do fluxo do utilizador : Ao detetar uma rutura na entrega OTP baseada no telefone, altere o método de entrega OTP de base telefônica para e-mail e reimplante o fluxo do utilizador, deixando as aplicações inalteradas.

![screenshot mostra inscrição de insusição](media/resilient-end-user-experiences/create-sign-in.png)

- **Alterar aplicações**: Para cada tarefa de identidade, como inscrição e inscrição, defina dois conjuntos de fluxos de utilizador. Configurar primeiro conjunto para usar OTP baseado em telefone e o segundo para OTP baseado em e-mail. Ao detetar uma rutura na entrega OTP baseada no telefone, altere e reimplante as aplicações para mudar do primeiro conjunto de fluxos de utilizador para o segundo, deixando os fluxos do utilizador inalterados.  

Ao utilizar **políticas personalizadas,** existem quatro métodos para construir resiliência. A lista abaixo está na ordem de complexidade e você precisará de redistribuir políticas atualizadas.

- **Ativar a seleção do utilizador de OTP ou OTP baseada em e-mail:** Expor ambas as opções aos utilizadores e permitir que os utilizadores selecionem uma das opções. Não há necessidade de fazer alterações às políticas ou aplicações.

- **Altere dinamicamente entre o OTP baseado no telefone e o OTP baseado em e-mail:** Colete informações de telefone e e-mail no momento da inscrição. Defina a política personalizada com antecedência para a comutação condicional durante uma interrupção do telefone, desde a entrega OTP baseada em telefone até à entrega de OTP baseada em e-mail. Não há necessidade de fazer alterações às políticas ou aplicações.

- **Utilize uma aplicação Autenticadora**: Atualize a política personalizada para utilizar uma [aplicação autenticadora](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-mfa-totp). Se o seu MFA normal for baseado em telefone ou por e-mail OTP, reenfectue as suas políticas personalizadas para mudar para usar a aplicação Authenticator.

>[!Note]
>Os utilizadores precisam de configurar a integração da aplicação Authenticator durante a inscrição.

- **Utilizar questões de segurança**: Se nenhum dos métodos acima referidos for aplicável, implemente questões de segurança como uma cópia de segurança. Configurar Questões de Segurança para os utilizadores durante a edição de embarque ou perfil e armazenar as respostas numa base de dados separada que não seja o diretório. Este método não satisfaz o requisito do MFA de "algo que você tem" por exemplo, telefone, mas oferece um "algo que você sabe".

## <a name="use-a-content-delivery-network"></a>Utilize uma rede de entrega de conteúdos

As redes de entrega de conteúdos (CDNs) são melhores performantes e menos dispendiosas do que as lojas blob para armazenamento de UI de fluxo de utilizador personalizado. O conteúdo da página web é entregue mais rapidamente a partir de uma rede geograficamente distribuída de servidores altamente disponíveis.  

Teste periodicamente a disponibilidade do seu CDN e o desempenho da distribuição de conteúdos através de cenários de ponta a ponta e testes de carga. Se está a planear um próximo aumento devido à promoção ou tráfego de férias, reveja as suas estimativas para testes de carga.
  
## <a name="next-steps"></a>Próximos passos

- [Recursos de resiliência para desenvolvedores Azure AD B2C](resilience-b2c.md)
  
  - [Interfaces resilientes com processos externos](resilient-external-processes.md)
  - [Resiliência através das melhores práticas do desenvolvedor](resilience-b2c-developer-best-practices.md)
  - [Resiliência através da monitorização e análise](resilience-with-monitoring-alerting.md)
- [Construa resiliência na sua infraestrutura de autenticação](resilience-in-infrastructure.md)
- [Aumentar a resiliência da autenticação e autorização nos seus pedidos](resilience-app-development-overview.md)