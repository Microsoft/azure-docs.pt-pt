---
title: Resolver problemas de políticas personalizadas no Azure Active Directory B2C | Documentos da Microsoft
description: Saiba mais sobre abordagens para resolver erros ao trabalhar com as políticas personalizadas no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b33b76175558c71720c15a2a4e206e26a60f1f95
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60317722"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Resolver problemas de políticas personalizadas do Azure AD B2C e arquitetura de experiências de identidade

Se utilizar o Azure Active Directory B2C (Azure AD B2C) as políticas personalizadas, podem ocorrer desafios de configurar a estrutura de experiência de identidade no seu formato XML de linguagem de política.  Aprender a escrever as políticas personalizadas pode ser como aprender uma nova linguagem. Neste artigo, descrevemos ferramentas e dicas que podem ajudá-lo rapidamente detetar e resolvem problemas. 

> [!NOTE]
> Este artigo se concentra na resolução de problemas de sua configuração de política personalizada do Azure AD B2C. Ele não resolve a aplicação da entidade confiadora de terceiros ou a respetiva biblioteca de identidade.

## <a name="xml-editing"></a>Edição de XML

O erro mais comuns na configuração de políticas personalizadas é incorretamente formatada em XML. Um bom editor de XML é quase essencial. Um bom editor de XML apresenta o XML nativamente, colore o conteúdo, preenche termos comuns, mantém os elementos XML indexados e pode validar com o esquema. Aqui estão dois de nossos editores XML favoritos:

* [Visual Studio Code](https://code.visualstudio.com/)
* [Bloco de notas + +](https://notepad-plus-plus.org/)

Validação do esquema XML identifica os erros antes de carregar o ficheiro XML. Na pasta raiz do pacote de iniciante, obtém a definição do esquema XML TrustFrameworkPolicy_0.3.0.0.xsd. Para obter mais informações, na documentação do seu editor de XML, procure *ferramentas XML* e *validação XML*.

Pode achar uma revisão das regras XML útil. O Azure AD B2C rejeita qualquer XML formatação erros que Deteta. Ocasionalmente, um formato incorreto XML pode fazer com que as mensagens de erro que são um engano.

## <a name="upload-policies-and-policy-validation"></a>Políticas de carregamento e validação de diretivas

 Validação de carregamento do ficheiro XML é automática. A maioria dos erros com que o carregamento falhe. Validação inclui o ficheiro de política que está a carregar. Ele também inclui a cadeia de arquivos, que o ficheiro de carregamento refere-se (o ficheiro de política de terceiros entidade confiadora, o arquivo de extensões e o ficheiro de base). 
 
 Erros de validação comuns incluem o seguinte:

Trecho de código de erro: `... makes a reference to ClaimType with id "displaName" but neither the policy nor any of its base policies contain such an element`
* O valor de ClaimType pode estar mal escrito ou não existe no esquema.
* ClaimType valores tem de ser definidos em, pelo menos, um dos ficheiros na política. 
    Por exemplo: `<ClaimType Id="socialIdpUserId">`
* Se ClaimType é definido no arquivo de extensões, mas também é utilizado um valor de TechnicalProfile no ficheiro de base, carregar o ficheiro de base resulta num erro.

Trecho de código de erro: `...makes a reference to a ClaimsTransformation with id...`
* As causas para o erro podem ser o mesmo que o erro de ClaimType.

Trecho de código de erro: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`
* Verifique se o TenantId valor no **\<TrustFrameworkPolicy\>** e **\<BasePolicy\>** elementos corresponde ao seu inquilino de destino do Azure AD B2C.  

## <a name="troubleshoot-the-runtime"></a>Resolver problemas relacionados com o tempo de execução

* Uso `Run Now` e `https://jwt.io` para testar as suas políticas independentemente do seu aplicativo web ou móvel. Este Web site funciona como um aplicativo de parte confiável. Ele exibe o conteúdo do JSON Web Token (JWT) que é gerado pela sua política do Azure AD B2C. Para criar um aplicativo de teste no Framework de experiência de identidade, utilize os seguintes valores:
    * Nome: TestApp
    * Web aplicação/API Web: Não
    * Cliente nativo: Não

* Para rastrear a troca de mensagens entre o browser cliente e o Azure AD B2C, utilize [Fiddler](https://www.telerik.com/fiddler). Ele pode ajudá-lo a obter uma indicação de onde o percurso do utilizador está a falhar em suas etapas de orquestração.

* Na **modo de desenvolvimento**, utilize **Application Insights** para rastrear a atividade do seu percurso de utilizador do Framework de experiência de identidade. Na **modo de desenvolvimento**, pode observar que a troca de afirmações entre o Framework de experiência de identidade e de vários fornecedores de afirmações que são definidos por perfis técnicos, como fornecedores de identidade, serviços baseados na API, o Diretório de utilizador do Azure AD B2C e outros serviços, como o Azure várias autenticação Multifator.  

## <a name="recommended-practices"></a>Práticas recomendadas

**Manter várias versões do seus cenários. Agrupá-los num projeto com a sua aplicação.** A base, extensões e da entidade confiadora de terceiros de ficheiros é diretamente dependente entre si. Guarde-as como um grupo. À medida que novos recursos são adicionados para as suas políticas, manter as versões de trabalho separado. Versões de trabalho da fase no seu próprio ficheiro de sistema com o código do aplicativo que interagem com.  Seus aplicativos podem invocar muitos da entidade confiadora terceiros políticas diferentes num inquilino. Que estes podem ficar dependentes das afirmações que esperam a partir das suas políticas do Azure AD B2C.

**Desenvolver e testar os perfis técnicos com jornadas de usuário conhecido.** Utilize políticas de pacote de iniciante testado para configurar seus perfis técnicos. Testá-los em separado antes de incorporá-los em seu próprio jornadas de utilizador.

**Desenvolva e teste jornadas de utilizador com perfis técnicos testados.** Altere os passos de orquestração de um percurso do utilizador de forma incremental. Construir progressivamente os seus cenários pretendidos.

## <a name="next-steps"></a>Passos Seguintes

* No GitHub, transfira o [active-directory-b2c-custom-policy-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) ficheiro. zip.
