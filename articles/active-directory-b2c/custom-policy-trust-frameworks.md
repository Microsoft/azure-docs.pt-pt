---
title: Visão geral da política personalizada Azure AD B2C | Microsoft Docs
description: Um tópico sobre as políticas personalizadas do Azure Ative Directory B2C e o Quadro de Experiência de Identidade.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 644192de74a888daa0391b31dd42eb6028403fd8
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98674479"
---
# <a name="azure-ad-b2c-custom-policy-overview"></a>Visão geral da política personalizada AZURE AD B2C

As políticas personalizadas são ficheiros de configuração que definem o comportamento do seu inquilino Azure Ative Directory B2C (Azure AD B2C). Enquanto [os fluxos de utilizador](user-flow-overview.md) são predefinidos no portal Azure AD B2C para as tarefas de identidade mais comuns. As políticas personalizadas podem ser totalmente editadas por um desenvolvedor de identidade para completar muitas tarefas diferentes.

Uma política personalizada é totalmente configurável, orientada por políticas que orquestra a confiança entre entidades em formatos de protocolo padrão como OpenID Connect, OAuth, SAML e algumas não-standard, por exemplo, trocas de sistema-sistema baseadas em REST API. A estrutura cria experiências fáceis de utilizar e etiquetas brancas.

Uma política personalizada é representada como um ou mais ficheiros com formato XML, que se referem uns aos outros numa cadeia hierárquica. Os elementos XML definem os tipos do edifício, a interação com o utilizador, e outras partes, e a lógica de negócio. 

## <a name="custom-policy-starter-pack"></a>Pacote de arranque de política personalizada

O [pacote de arranque](custom-policy-get-started.md#get-the-starter-pack) de política personalizada Azure AD B2C vem com várias políticas pré-construídas para que você vá rapidamente. Cada um destes pacotes de arranque contém o menor número de perfis técnicos e viagens de utilizador necessárias para alcançar os cenários descritos:

- **LocalAccounts** - Permite a utilização apenas de contas locais.
- **SocialAccounts** - Permite a utilização apenas de contas sociais (ou federadas).
- **SocialAndLocalAccounts** - Permite o uso de contas locais e sociais. A maioria das nossas amostras referem-se a esta política.
- **SocialAndLocalAccountsWithMFA** - Permite opções de autenticação social, local e multi-fatores.

## <a name="understanding-the-basics"></a>Compreender o básico 

### <a name="claims"></a>Afirmações

Uma reclamação fornece armazenamento temporário de dados durante uma execução política Azure AD B2C. Pode armazenar informações sobre o utilizador, como o nome próprio, apelido ou qualquer outra reclamação obtida do utilizador ou de outros sistemas (trocas de reclamações). O [esquema de reclamações](claimsschema.md) é o lugar onde declara as suas reivindicações. 

Quando a apólice é executada, a Azure AD B2C envia e recebe reclamações de e de partidos internos e externos e, em seguida, envia um subconjunto destas reivindicações para a sua aplicação do partido dependente como parte do token. As reclamações são utilizadas desta forma: 

- Uma reclamação é guardada, lida ou atualizada contra o objeto do utilizador do diretório.
- Uma reclamação é recebida de um fornecedor de identidade externo.
- As reclamações são enviadas ou recebidas através de um serviço de API REST personalizado.
- Os dados são recolhidos como reclamações do utilizador durante os fluxos de perfis de inscrição ou de edição.

### <a name="manipulating-your-claims"></a>Manipular as suas reivindicações

As [transformações de sinistros](claimstransformations.md) são funções predefinidas que podem ser usadas para converter uma determinada reivindicação em outra, avaliar uma reclamação ou definir um valor de reclamação. Por exemplo, adicionar um item a uma coleção de cordas, alterar o caso de uma cadeia, ou avaliar uma reclamação de dados e tempo. Uma transformação de sinistros especifica um método de transformação. 

### <a name="customize-and-localize-your-ui"></a>Personalize e localize o seu UI

Quando pretender recolher informações dos seus utilizadores apresentando uma página no seu navegador, utilize o [perfil técnico autoafirmado.](self-asserted-technical-profile.md) Pode editar o seu perfil técnico autoafirmado para [adicionar reclamações e personalizar a entrada do utilizador](./configure-user-input.md).

Para [personalizar a interface do utilizador](customize-ui-with-html.md) para o seu perfil técnico autoafirmado, especifica um URL no elemento de [definição](contentdefinitions.md) de conteúdo com conteúdo HTML personalizado. No perfil técnico autoafirmado, aponta-se para este ID de definição de conteúdo.

Para personalizar cordas específicas da linguagem, utilize o elemento [de localização.](localization.md) Uma definição de conteúdo pode conter uma referência [de localização](localization.md) que especifica uma lista de recursos localizados para carregar. O Azure AD B2C funde elementos de interface de utilizador com o conteúdo HTML que é carregado a partir do seu URL e, em seguida, exibe a página para o utilizador. 

## <a name="relying-party-policy-overview"></a>Visão geral da política partidária de confiar

Uma aplicação de parte dependente, ou no protocolo SAML conhecido como prestador de serviços, chama a [política do partido dependente](relyingparty.md) para executar uma viagem específica do utilizador. A política do partido em gestão especifica a jornada do utilizador a executar, e a lista de reclamações que o símbolo inclui. 

![Diagrama mostrando o fluxo de execução da política](./media/custom-policy-trust-frameworks/custom-policy-execution.png)

Todas as aplicações de partes que usam a mesma política, recebem as mesmas reclamações simbólicas, e o utilizador passa pela mesma viagem de utilizador.

### <a name="user-journeys"></a>Viagens de utilizador

[As viagens de utilizador](userjourneys.md) permitem definir a lógica de negócio com o caminho pelo qual o utilizador seguirá para ter acesso à sua aplicação. O utilizador é levado através da viagem do utilizador para recuperar as reclamações que devem ser apresentadas à sua aplicação. Uma viagem de utilizador é construída a partir de uma sequência de passos de [orquestração](userjourneys.md#orchestrationsteps). Um utilizador deve chegar ao último passo para adquirir um token. 

O seguinte descreve como pode adicionar passos de orquestração à política [de pacote de iniciação de contas sociais e locais.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts) Aqui está um exemplo de uma chamada de API REST que foi adicionada.

![viagem personalizada do utilizador](media/custom-policy-trust-frameworks/user-journey-flow.png)


### <a name="orchestration-steps"></a>Etapas de orquestração

O passo de orquestração refere-se a um método que implementa o seu propósito ou funcionalidade pretendido. Este método chama-se [perfil técnico.](technicalprofiles.md) Quando a sua viagem de utilizador precisa de se ramificar para melhor representar a lógica do negócio, o passo de orquestração refere-se à [sub-viagem.](subjourneys.md) Uma sub-jornada contém o seu próprio conjunto de passos de orquestração.

Um utilizador deve alcançar o último passo de orquestração na jornada do utilizador para adquirir um token. Mas os utilizadores podem não precisar de viajar por todos os passos de orquestração. Os passos de orquestração podem ser executados condicionalmente com base em [condições prévias definidas](userjourneys.md#preconditions) no passo de orquestração. 

Após a conclusão de um passo de orquestração, o Azure AD B2C armazena as reclamações outputted no **saco de reclamações**. As reclamações no saco de reclamações podem ser utilizadas por quaisquer outras etapas de orquestração na viagem do utilizador.

O diagrama que se segue mostra como os passos de orquestração da viagem do utilizador podem aceder ao saco de reclamações.

![Viagem de utilizador Azure AD B2C](media/custom-policy-trust-frameworks/user-journey-diagram.png)

### <a name="technical-profile"></a>Perfil técnico

Um perfil técnico fornece uma interface para comunicar com diferentes tipos de partes. Uma viagem de utilizador combina chamar perfis técnicos através de passos de orquestração para definir a sua lógica de negócio.

Todos os tipos de perfis técnicos partilham o mesmo conceito. Envias pedidos de entrada, geres a transformação de reclamações e comunicas com a parte configurada. Após o processo estar concluído, o perfil técnico devolve os pedidos de saída ao saco de reclamações. Para mais informações, consulte [a visão geral dos perfis técnicos](technicalprofiles.md)

### <a name="validation-technical-profile"></a>Perfil técnico de validação

Quando um utilizador interage com a interface do utilizador, pode querer validar os dados recolhidos. Para interagir com o utilizador, deve ser utilizado um [perfil técnico autoafirmado.](self-asserted-technical-profile.md)

Para validar a entrada do utilizador, é chamado um perfil técnico de [validação](validation-technical-profile.md) do perfil técnico autoafirmado. 

Um perfil técnico de validação é um método para chamar qualquer perfil técnico não interativo. Neste caso, o perfil técnico pode devolver reclamações de saída ou uma mensagem de erro. A mensagem de erro é entregue ao utilizador no ecrã, permitindo que o utilizador redoça.

O diagrama que se segue ilustra como o Azure AD B2C utiliza um perfil técnico de validação para validar as credenciais do utilizador.

![Diagrama de perfil técnico de validação](media/custom-policy-trust-frameworks/validation-technical-profile.png)

## <a name="inheritance-model"></a>Modelo de herança

Cada pacote de arranque inclui os seguintes ficheiros:

- Um ficheiro **Base** que contém a maioria das definições. Para ajudar na resolução de problemas e na manutenção a longo prazo das suas políticas, recomenda-se que faça um número mínimo de alterações neste ficheiro.
- Um ficheiro **Extensões** que contém as alterações de configuração únicas para o seu inquilino. Este ficheiro de política é derivado do ficheiro Base. Utilize este ficheiro para adicionar uma nova funcionalidade ou sobrepor-se à funcionalidade existente. Por exemplo, utilize este ficheiro para federar com novos fornecedores de identidade.
- Um ficheiro **Relying Party (RP)** que é o único ficheiro focado em tarefas que é invocado diretamente pela aplicação do partido, como as aplicações web, mobile ou desktop. Cada tarefa única, como inscrição, inscrição, reset de password ou edição de perfil, requer o seu próprio ficheiro de política do partido. Este ficheiro de política é derivado do ficheiro de extensões.

O modelo de herança é o seguinte:

- A política infantil a qualquer nível pode herdar da política dos pais e alargá-la adicionando novos elementos.
- Para cenários mais complexos, pode adicionar mais nível de habitação (até 5 no total)
- Pode adicionar mais políticas partidárias de dependência. Por exemplo, apagar a minha conta, alterar um número de telefone, a política do partido de singing SAML e muito mais.

O diagrama que se segue mostra a relação entre os ficheiros políticos e as aplicações do partido em gestão.

![Diagrama mostrando o modelo de herança de política de confiança](media/custom-policy-trust-frameworks/policies.png)


## <a name="guidance-and-best-practices"></a>Documentação de orientação e melhores práticas

### <a name="best-practices"></a>Melhores práticas

Dentro de uma política personalizada Azure AD B2C, pode integrar a sua própria lógica de negócio para construir as experiências do utilizador que necessita e alargar a funcionalidade do serviço. Temos um conjunto de boas práticas e recomendações para começar.

- Crie a sua lógica dentro da **política de extensão,** ou **retransmite a política partidária**. Pode adicionar novos elementos, que irão sobrepor-se à política de base, fazendo referência ao mesmo ID. Isto irá permitir-lhe escalar o seu projeto, facilitando o upgrade da política de base mais tarde se a Microsoft lançar novos packs de arranque.
- Dentro da **política de base,** recomendamos vivamente evitar fazer quaisquer alterações.  Quando necessário, faça comentários onde as alterações são feitas.
- Quando estiver a sobrevaver um elemento, como metadados de perfil técnico, evite copiar todo o perfil técnico da política base. Em vez disso, copie apenas a secção necessária do elemento. Consulte [a verificação de e-mail desativada](./disable-email-verification.md) para um exemplo de como fazer a alteração.
- Para reduzir a duplicação de perfis técnicos, onde a funcionalidade principal é partilhada, utilize a [inclusão do perfil técnico.](technicalprofiles.md#include-technical-profile)
- Evite escrever para o diretório AD Azure durante a entrada, o que pode levar a problemas de estrangulamento.
- Se a sua política tiver dependências externas, como a REST API garante que estão altamente disponíveis.
- Para uma melhor experiência do utilizador, certifique-se de que os seus modelos HTML personalizados são implantados globalmente usando [a entrega de conteúdo on-line](../cdn/index.yml). A Azure Content Delivery Network (CDN) permite reduzir os tempos de carga, poupar largura de banda e capacidade de resposta rápida.
- Se quiser alterar a viagem do utilizador. Copie toda a viagem do utilizador da política de base para a política de extensão. Forneça um ID de viagem de utilizador único para a viagem de utilizador que copiou. Em seguida, na [política do partido em apoio,](relyingparty.md)altere o elemento de [viagem do utilizador predefinido](relyingparty.md#defaultuserjourney) para apontar para a nova jornada do utilizador.

## <a name="troubleshooting"></a>Resolução de problemas

Ao desenvolver-se com as políticas Azure AD B2C, poderá encontrar erros ou exceções durante a execução da sua viagem de utilizador. O pode ser investigado usando Insights de Aplicação.

- Integrar Insights de Aplicação com Azure AD B2C para [diagnosticar exceções](troubleshoot-with-application-insights.md).
- A [extensão Azure AD B2C para código VS](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) pode ajudá-lo a aceder e [visualizar os registos](https://github.com/azure-ad-b2c/vscode-extension/blob/master/src/help/app-insights.md) com base num nome e hora de política.
- O erro mais comum na configuração de políticas personalizadas é o XML formatado de formação inadequada. Utilize a [validação do esquema XML](troubleshoot-custom-policies.md) para identificar erros antes de carregar o seu ficheiro XML.

## <a name="continuous-integration"></a>Integração contínua

Utilizando um pipeline de integração e entrega contínua (CI/CD) que instalou em Azure Pipelines, pode [incluir as suas políticas personalizadas Azure AD B2C na sua entrega de software](deploy-custom-policies-devops.md) e automatização de controlo de código. À medida que se implanta em diferentes ambientes AD B2C Ad, por exemplo dev, teste e produção, recomendamos que remova os processos manuais e realize testes automatizados utilizando gasodutos Azure.

## <a name="prepare-your-environment"></a>Preparar o ambiente

Você começa com a política personalizada Azure AD B2C:

1. [Criar um inquilino do Azure AD B2C](tutorial-create-tenant.md)
1. [Registar uma aplicação web](tutorial-register-applications.md) utilizando o portal Azure. Então, poderá testar a sua apólice.
1. Adicione as [chaves de política necessárias](custom-policy-get-started.md#add-signing-and-encryption-keys) e [registe aplicações do Quadro de Experiência de Identidade](custom-policy-get-started.md#register-identity-experience-framework-applications)
1. [Obtenha o pacote de arranque de política Azure AD B2C](custom-policy-get-started.md#get-the-starter-pack) e faça o upload para o seu inquilino. 
1. Depois de fazer o upload do pacote inicial, [teste a sua política de inscrição ou de inscrição](custom-policy-get-started.md#test-the-custom-policy)
1. Recomendamos que descarregue e instale [o Código do Estúdio Visual](https://code.visualstudio.com/) (Código VS). O Visual Studio Code é um editor de código fonte simples, mas poderoso, que é executado no seu ambiente de trabalho e está disponível para Windows, macOS e Linux. Com o Código VS pode editar os ficheiros XML de política personalizada Azure AD B2C.
1. Para navegar rapidamente através das políticas personalizadas AZURE AD B2C, recomendamos que instale a [extensão AD B2C do Azure para o Código VS](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)
 
## <a name="next-steps"></a>Passos seguintes

Depois de configurar e testar a sua política Azure AD B2C, pode começar a personalizar a sua política. Veja os seguintes artigos para aprender a:

- [Adicione reclamações e personalize a entrada do utilizador](./configure-user-input.md) usando políticas personalizadas. Aprenda a definir uma reclamação, adicione uma reclamação à interface do utilizador personalizando alguns dos perfis técnicos do pacote inicial.
- [Personalize a interface](customize-ui-with-html.md) de utilizador da sua aplicação utilizando uma política personalizada. Aprenda a criar o seu próprio conteúdo HTML e personalize a definição de conteúdo.
- [Localize a interface](./language-customization.md) de utilizador da sua aplicação utilizando uma política personalizada. Aprenda a configurar a lista de línguas apoiadas e forneça rótulos específicos da linguagem, adicionando o elemento de recursos localizado.
- Durante o desenvolvimento e teste da sua política pode [desativar a verificação de e-mail.](./disable-email-verification.md) Saiba como substituir um metadados de perfil técnico.
- [Instale o sôm-in com uma conta google](./identity-provider-google.md) utilizando políticas personalizadas. Saiba como criar um novo fornecedor de reclamações com perfil técnico OAuth2. Em seguida, personalize a viagem do utilizador para incluir a opção de inscrição no Google.
- Para diagnosticar problemas com as suas políticas personalizadas pode [recolher registos B2C do Azure Ative Directory com Insights de Aplicação.](troubleshoot-with-application-insights.md) Saiba como adicionar novos perfis técnicos e configuure a sua política partidária de retransmissão.