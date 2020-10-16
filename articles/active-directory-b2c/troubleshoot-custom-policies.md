---
title: Resolução de problemas de políticas personalizadas no Azure Ative Directory B2C
description: Saiba mais sobre abordagens para resolver erros ao trabalhar com políticas personalizadas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 08/13/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c1b51792c86cfce15fa718040dfcbcc13997ee26
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85384962"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Resolução de problemas Azure AD B2C políticas personalizadas e enquadramento de experiência de identidade

Se utilizar as políticas personalizadas do Azure Ative Directory B2C (Azure AD B2C), poderá experimentar desafios na criação do Quadro de Experiência de Identidade no seu formato XML de linguagem política. Aprender a escrever políticas personalizadas pode ser como aprender uma nova linguagem. Neste artigo, descrevemos algumas ferramentas e dicas que podem ajudá-lo a descobrir e resolver problemas.

Este artigo centra-se na resolução de problemas da sua configuração de política personalizada Azure AD B2C. Não se dirige à aplicação do partido ou à sua biblioteca de identidade.

## <a name="xml-editing"></a>Edição XML

O erro mais comum na configuração de políticas personalizadas é o XML formatado de formação inadequada. Um bom editor de XML é quase essencial. Exibe XML de forma nativa, o conteúdo de códigos de cores, pré-preenche termos comuns, mantém os elementos XML indexados e pode validar contra um esquema XML.

Dois dos nossos editores favoritos são [Visual Studio Code](https://code.visualstudio.com/) e [Notepad++](https://notepad-plus-plus.org/).

A validação do esquema XML identifica erros antes de carregar o seu ficheiro XML. Na pasta raiz do [pacote de arranque,](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)obtenha o ficheiro de definição de esquema XML *TrustFrameworkPolicy_0.3.0.0.xsd*. Para saber como usar o ficheiro de esquema XSD para validação no seu editor, procure *ferramentas XML* e *validação XML* ou similar na documentação do editor.

Pode achar útil uma revisão das regras XML. A Azure AD B2C rejeita quaisquer erros de formatação XML que detete. Ocasionalmente, o XML formatado incorretamente pode causar mensagens de erro que são enganosas.

## <a name="upload-policies-and-policy-validation"></a>Fazer upload de políticas e validação de políticas

A validação do ficheiro de política XML é executada automaticamente no upload. A maioria dos erros faz com que o upload falhe. A validação inclui o ficheiro de política que está a enviar. Também inclui a cadeia de ficheiros a que o ficheiro de upload se refere (o ficheiro de política do partido, o ficheiro de extensões e o ficheiro base).

Os erros de validação comuns incluem:

> Erro: `...makes a reference to ClaimType with id "displayName" but neither the policy nor any of its base policies contain such an element`

* O valor ClaimType pode ser mal escrito, ou não existe no esquema.
* Os valores de ClaimType devem ser definidos em pelo menos um dos ficheiros da apólice.
    Por exemplo: `<ClaimType Id="issuerUserId">`
* Se o ClaimType for definido no ficheiro de extensões, mas também for utilizado num valor TécnicoProfile no ficheiro base, o upload do ficheiro base resulta num erro.

> Erro: `...makes a reference to a ClaimsTransformation with id...`

* As causas deste erro podem ser as mesmas que para o erro do ClaimType.

> Erro: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`

* Verifique se o valor do TenantId no `<TrustFrameworkPolicy\>` e `<BasePolicy\>` elementos correspondem ao seu inquilino Azure AD B2C.

## <a name="troubleshoot-the-runtime"></a>Resolução de problemas no tempo de execução

* Use **Executar agora** e testar as suas políticas `https://jwt.ms` independentemente da sua aplicação web ou móvel. Este site funciona como uma aplicação de partidos dependentes. Exibe o conteúdo do token web JSON (JWT) que é gerado pela sua política Azure AD B2C.

    Para criar uma aplicação de teste que possa redirecionar `https://jwt.ms` para inspeção simbólica:

    [!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

* Para rastrear a troca de mensagens entre o seu navegador cliente e o Azure AD B2C, utilize [o Fiddler](https://www.telerik.com/fiddler). Pode ajudá-lo a obter uma indicação de onde a sua viagem de utilizador está a falhar nos seus passos de orquestração.

* No **modo Desenvolvimento,** utilize [o Application Insights](troubleshoot-with-application-insights.md) para rastrear a atividade da sua jornada de utilizador do Quadro de Experiências de Identidade. No **modo desenvolvimento,** pode observar a troca de reclamações entre o Quadro de Experiência de Identidade e os vários fornecedores de sinistros que são definidos por perfis técnicos, tais como fornecedores de identidade, serviços baseados em API, o diretório de utilizadores Azure AD B2C, e outros serviços, como a Autenticação Multi-Factor Azure.

## <a name="recommended-practices"></a>Práticas recomendadas

**Mantenha várias versões dos seus cenários. Agrupá-los num projeto com a sua aplicação.** A base, as extensões e os ficheiros do partido dependem diretamente uns dos outros. Salve-os como um grupo. À medida que novas funcionalidades são adicionadas às suas políticas, mantenha versões de trabalho separadas. Versões de trabalho de fase no seu próprio sistema de ficheiros com o código de aplicação com o que interagem. Suas candidaturas podem invocar muitas políticas partidárias diferentes num inquilino. Podem ficar dependentes das alegações que esperam das suas políticas Azure AD B2C.

**Desenvolver e testar perfis técnicos com viagens de utilizador conhecidas.** Utilize as políticas de pacote de arranque testados para configurar os seus perfis técnicos. Teste-os separadamente antes de incorporá-los nas suas próprias viagens de utilizador.

**Desenvolver e testar viagens de utilizador com perfis técnicos testados.** Alterar gradualmente os passos de orquestração de uma viagem de utilizador. Construa progressivamente os seus cenários pretendidos.

## <a name="next-steps"></a>Passos seguintes

Disponível no GitHub, descarregue o arquivo [active-directório-b2c-custom-policy-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) .zip. Também pode clonar o repositório:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
```
