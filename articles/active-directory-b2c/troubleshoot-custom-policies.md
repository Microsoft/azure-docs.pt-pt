---
title: Políticas personalizadas de resolução de problemas no Diretório Ativo Azure B2C
description: Aprenda sobre abordagens para resolver erros ao trabalhar com políticas personalizadas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2f65e98cec04991fe9edef1b81bcb3ecc3d93d76
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78186370"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Problemática Azure AD B2C políticas personalizadas e Quadro de Experiência de Identidade

Se utilizar políticas personalizadas de Diretório Ativo Azure B2C (Azure AD B2C), poderá experimentar desafios que configuram o Quadro de Experiência de Identidade no seu formato XML de linguagem política. Aprender a escrever políticas personalizadas pode ser como aprender uma nova linguagem. Neste artigo, descrevemos algumas ferramentas e dicas que podem ajudá-lo a descobrir e resolver problemas.

Este artigo centra-se na resolução de problemas da sua configuração de política personalizada Azure AD B2C. Não aborda a aplicação do partido que confia ou a sua biblioteca de identidade.

## <a name="xml-editing"></a>Edição XML

O erro mais comum na criação de políticas personalizadas é o XML mal formatado. Um bom editor xml é quase essencial. Exibe conteúdo de códigos de cores XML, preenchimento de termos comuns, mantém os elementos XML indexados e pode validar contra um esquema XML.

Dois dos nossos editores favoritos são [Visual Studio Code](https://code.visualstudio.com/) e [Notepad++](https://notepad-plus-plus.org/).

A validação do esquema XML identifica erros antes de carregar o ficheiro XML. Na pasta raiz do pacote de [arranque,](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)obtenha o ficheiro de definição de esquema XML *TrustFrameworkPolicy_0.3.0.0.xsd*. Para saber como utilizar o ficheiro esquema XSD para validação no seu editor, procure *ferramentas XML* e *validação XML* ou similar na documentação do editor.

Pode achar útil uma revisão das regras do XML. O Azure AD B2C rejeita quaisquer erros de formatação XML que detete. Ocasionalmente, o XML mal formatado pode causar mensagens de erro que são enganosas.

## <a name="upload-policies-and-policy-validation"></a>Políticas de upload e validação de políticas

A validação do ficheiro de política XML é executada automaticamente no upload. A maioria dos erros faz com que o upload falhe. A validação inclui o ficheiro de política que está a carregar. Também inclui a cadeia de ficheiros a que o ficheiro de upload se refere (o ficheiro de política do partido, o ficheiro de extensões e o ficheiro base).

Os erros comuns de validação incluem:

> Corte de erro:`...makes a reference to ClaimType with id "displayName" but neither the policy nor any of its base policies contain such an element`

* O valor Do ClaimType pode ser mal escrito ou não existe no esquema.
* Os valores do ClaimType devem ser definidos em pelo menos um dos ficheiros da apólice.
    Por exemplo: `<ClaimType Id="issuerUserId">`
* Se o ClaimType for definido no ficheiro de extensões, mas também for utilizado num valor de Perfil Técnico no ficheiro base, o upload do ficheiro base resulta num erro.

> Corte de erro:`...makes a reference to a ClaimsTransformation with id...`

* As causas deste erro podem ser as mesmas que para o erro do ClaimType.

> Corte de erro:`Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`

* Verifique se o valor `<TrustFrameworkPolicy\>` do `<BasePolicy\>` TenantId no e elementos corresponde ao seu alvo Azure AD AD AD B2C inquilino.

## <a name="troubleshoot-the-runtime"></a>Problemas de resolução do tempo de corrido

* Use o `https://jwt.ms` Run **agora** e para testar as suas políticas independentemente da sua aplicação web ou móvel. Este site funciona como uma aplicação de partido de base. Exibe o conteúdo do token web JSON (JWT) que é gerado pela sua política Azure AD AD B2C.

    Para criar uma aplicação de `https://jwt.ms` teste que possa redirecionar para a inspeção simbólica:

    [!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

* Para rastrear a troca de mensagens entre o seu navegador cliente e o Azure AD B2C, utilize [o Fiddler](https://www.telerik.com/fiddler). Pode ajudá-lo a obter uma indicação de onde a sua viagem de utilizador está falhando nos seus passos de orquestração.

* No **modo de Desenvolvimento,** utilize os Insights da [Aplicação](troubleshoot-with-application-insights.md) para rastrear a atividade da sua viagem de utilizador do Quadro de Experiência seleção de Identidade. No **modo de Desenvolvimento,** pode observar a troca de reclamações entre o Quadro de Experiência seletiva e os vários fornecedores de sinistros que são definidos por perfis técnicos, tais como fornecedores de identidade, serviços baseados em API, o diretório de utilizadores Azure AD B2C, e outros serviços, como a Autenticação Multi-Factor Azure.

## <a name="recommended-practices"></a>Práticas recomendadas

**Mantenha várias versões dos seus cenários. Agrupa-os num projeto com a sua aplicação.** A base, as extensões e os ficheiros partidários dependem uns dos outros. Salve-os como um grupo. À medida que as novas funcionalidades são adicionadas às suas políticas, mantenha versões de trabalho separadas. Fase de versões de trabalho no seu próprio sistema de ficheiros com o código de aplicação com o que interagem. Suas candidaturas podem invocar muitas políticas partidárias diferentes num inquilino. Podem ficar dependentes das alegações que esperam das suas políticas Azure AD B2C.

**Desenvolver e testar perfis técnicos com viagens de utilizador conhecidas.** Utilize políticas de pacotes de arranque testados para configurar os seus perfis técnicos. Teste-os separadamente antes de os incorporar nas suas próprias viagens de utilizador.

**Desenvolver e testar as viagens dos utilizadores com perfis técnicos testados.** Mude os passos de orquestração de uma viagem de utilizador de forma incremental. Construa progressivamente os seus cenários pretendidos.

## <a name="next-steps"></a>Passos seguintes

Disponível no GitHub, descarregue o [active-directy-b2c-custom-policy-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) .zip archive. Também pode clonar o repositório:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
```
