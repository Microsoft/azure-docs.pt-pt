---
title: Solucionar problemas de políticas personalizadas no Azure Active Directory B2C
description: Saiba mais sobre as abordagens para resolver erros ao trabalhar com políticas personalizadas no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4893025b7d54dad1f1da6c5967d3c1dec99b499b
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71826909"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Solucionar problemas Azure AD B2C políticas personalizadas e estrutura de experiência de identidade

Se você usar políticas personalizadas do Azure Active Directory B2C (Azure AD B2C), poderá enfrentar desafios para configurar a estrutura de experiência de identidade em seu formato XML de linguagem de política. Aprender a escrever políticas personalizadas pode ser como aprender uma nova linguagem. Neste artigo, descrevemos algumas ferramentas e dicas que podem ajudá-lo a descobrir e resolver problemas.

Este artigo se concentra na solução de problemas de sua configuração de política personalizada Azure AD B2C. Ele não aborda o aplicativo de terceira parte confiável ou sua biblioteca de identidades.

## <a name="xml-editing"></a>Edição XML

O erro mais comum na configuração de políticas personalizadas é o XML formatado incorretamente. Um bom editor de XML é praticamente essencial. Ele exibe o XML nativamente, o conteúdo de códigos de cor, preenche previamente os termos comuns, mantém os elementos XML indexados e pode validar em um esquema XML.

Dois de nossos editores favoritos são [Visual Studio Code](https://code.visualstudio.com/) e o [bloco de notas + +](https://notepad-plus-plus.org/).

A validação de esquema XML identifica os erros antes de carregar o arquivo XML. Na pasta raiz do [pacote inicial](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack), obtenha o arquivo de definição de esquema XML *trustframeworkpolicy_ 0.3.0.0. xsd*. Para saber como usar o arquivo de esquema XSD para validação em seu editor, procure *ferramentas XML* e validação de *XML* ou semelhante na documentação do editor.

Você pode encontrar uma revisão das regras XML úteis. Azure AD B2C rejeita quaisquer erros de formatação XML detectados. Ocasionalmente, o XML formatado incorretamente pode causar mensagens de erro enganosas.

## <a name="upload-policies-and-policy-validation"></a>Carregar políticas e validação de política

A validação do arquivo de política XML é executada automaticamente no carregamento. A maioria dos erros faz com que o carregamento falhe. A validação inclui o arquivo de política que você está carregando. Ele também inclui a cadeia de arquivos à qual o arquivo de carregamento se refere (o arquivo de política de terceira parte confiável, o arquivo de extensões e o arquivo base).

Os erros comuns de validação incluem o seguinte:

> Trecho de erro: `...makes a reference to ClaimType with id "displayName" but neither the policy nor any of its base policies contain such an element`

* O valor de ClaimType pode estar incorreto ou não existir no esquema.
* Os valores de ClaimType devem ser definidos em pelo menos um dos arquivos na política.
    Por exemplo: `<ClaimType Id="issuerUserId">`
* Se ClaimType for definido no arquivo de extensões, mas ele também for usado em um valor TechnicalProfile no arquivo base, o carregamento do arquivo base resultará em um erro.

> Trecho de erro: `...makes a reference to a ClaimsTransformation with id...`

* As causas desse erro podem ser as mesmas para o erro ClaimType.

> Trecho de erro: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`

* Verifique se o valor de Tenantid nos elementos `<TrustFrameworkPolicy\>` e `<BasePolicy\>` correspondem ao seu locatário de Azure AD B2C de destino.

## <a name="troubleshoot-the-runtime"></a>Solucionar problemas do tempo de execução

* Use **executar agora** e `https://jwt.ms` para testar suas políticas independentemente de seu aplicativo Web ou móvel. Este site age como um aplicativo de terceira parte confiável. Ele exibe o conteúdo do JSON Web token (JWT) que é gerado pela sua política de Azure AD B2C.

    Para criar um aplicativo de teste que possa redirecionar para `https://jwt.ms` para inspeção de token:

    [!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

* Para rastrear a troca de mensagens entre o navegador do cliente e Azure AD B2C, use o [Fiddler](https://www.telerik.com/fiddler). Ele pode ajudá-lo a obter uma indicação de onde o percurso do usuário está falhando em suas etapas de orquestração.

* No **modo de desenvolvimento**, use [Application insights](active-directory-b2c-troubleshoot-custom.md) para rastrear a atividade do percurso do usuário da estrutura de experiência de identidade. No **modo de desenvolvimento**, você pode observar a troca de declarações entre a estrutura de experiência de identidade e os vários provedores de declarações que são definidos por perfis técnicos, como provedores de identidade, serviços baseados em API, o usuário Azure ad B2C diretório e outros serviços, como a autenticação multifator do Azure.

## <a name="recommended-practices"></a>Práticas recomendadas

**Keep várias versões de seus cenários. Agrupe-os em um projeto com seu aplicativo.** A base, as extensões e os arquivos de terceira parte confiável são diretamente dependentes uns dos outros. Salve-os como um grupo. À medida que novos recursos são adicionados às suas políticas, mantenha versões de trabalho separadas. Preparar versões de trabalho em seu próprio sistema de arquivos com o código do aplicativo com o qual interagem. Seus aplicativos podem invocar várias políticas de terceira parte confiável diferentes em um locatário. Eles podem se tornar dependentes das declarações que esperam de suas políticas de Azure AD B2C.

**Desenvolva e teste perfis técnicos com jornadas conhecidos do usuário.** Use as políticas testadas do pacote inicial para configurar seus perfis técnicos. Teste-os separadamente antes de incorporá-los a seus próprios percursos do usuário.

**Desenvolva e teste viagens de usuários com perfis técnicos testados.** Altere as etapas de orquestração de uma jornada do usuário de forma incremental. Crie progressivamente seus cenários pretendidos.

## <a name="next-steps"></a>Passos seguintes

Disponível no GitHub, baixe o arquivo [Active-Directory-B2C-Custom-Policy-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) . zip. Você também pode clonar o repositório:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
```
