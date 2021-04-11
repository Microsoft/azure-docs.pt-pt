---
title: Como personalizar o seu Azure Ative Directory Verifiable Credentials (pré-visualização)
description: Este artigo mostra-lhe como criar a sua própria credencial verificável personalizada
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: c830f9c7edb252508824b3a92bd31b6fad31395d
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106170070"
---
# <a name="how-to-customize-your-verifiable-credentials-preview"></a>Como personalizar as suas credenciais verificáveis (pré-visualização)

As credenciais verificáveis são compostas por dois componentes, as regras e os ficheiros de exibição. O ficheiro de regras determina o que o utilizador precisa de fornecer antes de receber uma credencial verificável. O ficheiro de exibição controla a marca da credencial e do estilo das reclamações. Neste guia, explicaremos como modificar ambos os ficheiros para atender aos requisitos da sua organização. 

> [!IMPORTANT]
> Azure Ative Directory Verifiable Credentials está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="rules-file-requirements-from-the-user"></a>Ficha de Regras: Requisitos do utilizador

O ficheiro de regras é um ficheiro JSON simples que descreve propriedades importantes de credenciais verificáveis. Em particular, descreve como as alegações são usadas para povoar a sua credencial verificável.

Existem atualmente três tipos de entrada que estão disponíveis para configurar no ficheiro de regras. Estes tipos são utilizados pelo serviço de emissão de credenciais verificáveis para inserir reclamações numa credencial verificável e atestar essa informação com o seu DID. Seguem-se os três tipos com explicações.

- Ficha de ID
- Credenciais verificáveis através de uma apresentação verificável.
- Reclamações Self-Attested

**ID Token:** A aplicação da amostra e tutorial usam o ID Token. Quando esta opção estiver configurada, terá de fornecer um URI de configuração open ID Connect e incluir as alegações que devem ser incluídas no VC. O utilizador será solicitado a 'Iniciar sôs' na aplicação Authenticator para satisfazer este requisito e adicionar as reclamações associadas da sua conta. 

**Credenciais verificáveis:** O resultado final de um fluxo de emissão é produzir uma Credencial Verificável, mas também pode pedir ao utilizador para apresentar uma Credencial Verificável para emitir uma. O Ficheiro de Regras pode obter reclamações específicas da credencial verificável apresentada e incluir essas reclamações na credencial verificável recentemente emitida pela sua organização. 

**Self Attested Claims:** Quando esta opção for selecionada, o utilizador poderá digitar diretamente informações no Autenticador. Neste momento, as cordas são a única entrada suportada para reivindicações auto-atestas. 

![visão detalhada do cartão credencial verificável](media/credential-design/issuance-doc.png) 

**Reclamações estáticas:** Além disso, podemos declarar uma reclamação estática no ficheiro Regras, no entanto esta entrada não vem do utilizador. O Emitente define uma reclamação estática no ficheiro Regras e pareceria com qualquer outra reclamação na Credencial Verificável. Basta adicionar uma credencialSubject após vc.type e declarar o atributo e a reclamação. 

```json
"vc": {
    "type": [ "StaticClaimCredential" ],
    "credentialSubject": {
      "staticClaim": true,
      "anotherClaim": "Your Claim Here"
    },
  }
}
```


## <a name="input-type-id-token"></a>Tipo de entrada: ID Token

Para obter o ID Token como entrada, o ficheiro de regras precisa configurar o conhecido ponto final do sistema de identidade compatível com OIDC. Nesse sistema é necessário registar uma aplicação com as informações corretas dos exemplos de comunicação do [serviço Emitente](issuer-openid.md). Além disso, o client_id precisa de ser colocado no ficheiro de regras, bem como um parâmetro de âmbito deve ser preenchido com os âmbitos corretos. Por exemplo, o Azure Ative Directory precisa do âmbito de e-mail se quiser devolver uma reclamação de e-mail no token de ID.
```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
            "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["https://schema.org/EducationalCredential", "https://schemas.ed.gov/universityDiploma2020", "https://schemas.contoso.edu/diploma2020" ]
      }
    }
```

| Propriedade | Descrição |
| -------- | ----------- |
| `attestations.idTokens` | Um conjunto de fornecedores de identidade OpenID Connect que são suportados para obter informações do utilizador. |
| `...mapping` | Um objeto que descreve como as alegações em cada símbolo de ID são mapeadas para atributos na credencial verificável resultante. |
| `...mapping.{attribute-name}` | O atributo que deve ser povoado na credencial verificável resultante. |
| `...mapping.{attribute-name}.claim` | A reclamação em fichas de identificação cujo valor deve ser usado para povoar o atributo. |
| `...configuration` | A localização do documento de configuração do seu fornecedor de identidade. Este URL deve aderir à [norma OpenID Connect para metadados do fornecedor de identidade.](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata) O documento de configuração deve incluir os `issuer` `authorization_endpoint` campos, `token_endpoint` e `jwks_uri` campos. |
| `...client_id` | Identificação do cliente obtida durante o processo de registo do cliente. |
| `...scope` | Uma lista de âmbitos delimitados pelo espaço, o IDP, deve poder devolver as alegações corretas no token de identificação. |
| `...redirect_uri` | Deve sempre usar o `vcclient://openid/` valor. |
| `validityInterval` | Uma duração temporal, em segundos, representando a vida útil das suas credenciais verificáveis. Após este período de tempo, a credencial verificável deixará de ser válida. Omitir este valor significa que cada Credencial Verificável permanecerá válida até ser explicitamente revogada. |
| `vc.type` | Uma matriz de cordas indicando o esquema(s) que a sua Credencial Verificável satisfaz. Veja a secção abaixo para mais detalhes. |

### <a name="vctype-choose-credential-types"></a>vc.type: Escolha tipo credencial(s) 

Todas as credenciais verificáveis devem declarar o seu "tipo" no seu ficheiro de regras. O tipo de credencial distingue as suas credenciais verificáveis das credenciais emitidas por outras organizações e garante a interoperabilidade entre emitentes e verificadores. Para indicar um tipo de credencial, deve fornecer um ou mais tipos de credenciais que a credencial satisfaz. Cada tipo é representado por uma corda única - muitas vezes um URI será usado para garantir a singularidade global. O URI não precisa de ser endereçada; é tratado como uma corda. 

Como exemplo, uma credencial de diploma emitida pela Universidade Contoso pode declarar os seguintes tipos:

| Tipo | Objetivo |
| ---- | ------- |
| `https://schema.org/EducationalCredential` | Declara que os diplomas emitidos pela Universidade Contoso contêm atributos definidos pelo objeto de schema.org. `EducationaCredential` |
| `https://schemas.ed.gov/universityDiploma2020` | Declara que os diplomas emitidos pela Universidade contoso contêm atributos definidos pelo Departamento de Educação dos Estados Unidos. |
| `https://schemas.contoso.edu/diploma2020` | Declara que os diplomas emitidos pela Universidade Contoso contêm atributos definidos pela Universidade Contoso. |

Ao declarar os três tipos, os diplomas da Universidade contoso podem ser usados para satisfazer diferentes pedidos de verificadores. Um banco pode solicitar um conjunto de `EducationCredential` s a um utilizador, e o diploma pode ser usado para satisfazer o pedido. Mas a Associação de Ex-Alunos da Universidade de Contoso pode solicitar uma credencial de tipo `https://schemas.contoso.edu/diploma2020` , e o diploma também irá satisfazer o pedido.

Para garantir a interoperabilidade das suas credenciais, recomenda-se que trabalhe em estreita colaboração com organizações relacionadas para definir tipos de credenciais, esquemas e URIs para uso na sua indústria. Muitos organismos do setor fornecem orientações sobre a estrutura de documentos oficiais que podem ser reutilizados para a definição do conteúdo das credenciais verificáveis. Também deve trabalhar em estreita colaboração com os verificadores das suas credenciais para entender como pretendem solicitar e consumir as suas credenciais verificáveis.

## <a name="input-type-verifiable-credential"></a>Tipo de entrada: Credencial verificável

>[!NOTE]
>Os ficheiros de regras que pedem uma credencial verificável não utilizam o formato de troca de apresentação para solicitar credenciais. Isto será atualizado quando o Serviço de Emissão suportar a norma, O Manifesto Credencial. 

```json
{
    "attestations": {
      "presentations": [
        {
          "mapping": {
            "first_name": {
              "claim": "$.vc.credentialSubject.firstName",
            },
            "last_name": {
              "claim": "$.vc.credentialSubject.lastName",
              "indexed": true
            }
          },
          "credentialType": "VerifiedCredentialNinja",
          "contracts": [
            "https://beta.did.msidentity.com/v1.0/3c32ed40-8a10-465b-8ba4-0b1e86882668/verifiableCredential/contracts/VerifiedCredentialNinja"
          ],
          "issuers": [
            {
              "iss": "did:ion:123"
            }
          ]
        }
      ]
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }
  ```

| Propriedade | Descrição |
| -------- | ----------- |
| `attestations.presentations` | Uma série de credenciais verificáveis sendo solicitadas como entradas. |
| `...mapping` | Um objeto que descreve como as alegações em cada credencial verificada são mapeadas para atributos na credencial verificável resultante. |
| `...mapping.{attribute-name}` | O atributo que deve ser povoado na credencial verificável resultante. |
| `...mapping.{attribute-name}.claim` | A alegação na Credencial Verificável cujo valor deve ser usado para povoar o atributo. |
| `...mapping.{attribute-name}.indexed` | Apenas um pode ser habilitado por credencial verificável para guardar para revogar. Por favor, consulte o [artigo sobre como revogar uma credencial](how-to-issuer-revoke.md) para mais informações. |
| `credentialType` | O tipo de credencial da Credencial Verificável que está a pedir ao utilizador para apresentar. |
| `contracts` | O URI do contrato no portal do Serviço credencial verificável. |
| `issuers.iss` | O emitente fez para a Credencial Verificável ser solicitado ao utilizador. |
| `validityInterval` | Uma duração temporal, em segundos, representando a vida útil das suas credenciais verificáveis. Após este período de tempo, a Credencial Verificável deixará de ser válida. Omitir este valor significa que cada Credencial Verificável permanecerá válida até ser explicitamente revogada. |
| `vc.type` | Uma matriz de cordas indicando o esquema(s) que a sua credencial verificável satisfaz. |


## <a name="input-type-self-attested-claims"></a>Tipo de entrada: Self-Attested Reclamações

Durante o fluxo de emissão, o utilizador pode ser solicitado a inserir algumas informações auto-atestadas. A partir de agora, o único tipo de entrada é uma 'corda'. 
```json
{
  "attestations": {
    "selfIssued": {
      "mapping": {
        "alias": {
          "claim": "name"
        }
      },
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }



```
| Propriedade | Descrição |
| -------- | ----------- |
| `attestations.selfIssued` | Uma série de alegações auto-emitidas que requerem entrada do utilizador. |
| `...mapping` | Um objeto que descreve como as alegações auto-emitidas são mapeadas para atributos na credencial verificável resultante. |
| `...mapping.alias` | O atributo que deve ser povoado na credencial verificável resultante. |
| `...mapping.alias.claim` | A alegação na Credencial Verificável cujo valor deve ser usado para povoar o atributo. |
| `validityInterval` | Uma duração temporal, em segundos, representando a vida útil das suas credenciais verificáveis. Após este período de tempo, a Credencial Verificável deixará de ser válida. Omitir este valor significa que cada Credencial Verificável permanecerá válida até ser explicitamente revogada. |
| `vc.type` | Uma matriz de cordas indicando o esquema(s) que a sua Credencial Verificável satisfaz. |


## <a name="display-file-verifiable-credentials-in-microsoft-authenticator"></a>Display File: credenciais verificáveis no Microsoft Authenticator

Credenciais verificáveis oferecem um conjunto limitado de opções que podem ser usadas para refletir a sua marca. Este artigo fornece instruções sobre como personalizar as suas credenciais, e as melhores práticas para desenhar credenciais que ficam ótimas uma vez emitidas aos utilizadores.

As credenciais verificáveis emitidas aos utilizadores são apresentadas como cartões no Microsoft Authenticator. Como administrador, pode escolher a cor do cartão, ícone e cordas de texto para combinar com a marca da sua organização.

![documentação de emissão](media/credential-design/detailed-view.png) 

Os cartões também contêm campos personalizáveis que pode usar para que os utilizadores saibam a finalidade do cartão, os atributos que contém e muito mais.

## <a name="create-a-credential-display-file"></a>Criar um ficheiro de exibição credencial

Tal como o ficheiro de regras, o ficheiro de exibição é um simples ficheiro JSON que descreve como o conteúdo das suas credenciais verificáveis deve ser apresentado na aplicação Microsoft Authenticator. 

>[!NOTE]
> Neste momento, este modelo de exibição é utilizado apenas pelo Microsoft Authenticator.

O ficheiro de exibição tem a seguinte estrutura.

```json
{
    "default": {
      "locale": "en-US",
      "card": {
        "title": "University Graduate",
        "issuedBy": "Contoso University",
        "backgroundColor": "#212121",
        "textColor": "#FFFFFF",
        "logo": {
          "uri": "https://contoso.edu/images/logo.png",
          "description": "Contoso University Logo"
        },
        "description": "This digital diploma is issued to students and alumni of Contoso University."
      },
      "consent": {
        "title": "Do you want to get your digital diploma from Contoso U?",
        "instructions": "Please log in with your Contoso U account to receive your digital diploma."
      },
      "claims": {
        "vc.credentialSubject.name": {
          "type": "String",
          "label": "Name"
        }
      }
    }
}
```

| Propriedade | Descrição |
| -------- | ----------- |
| `locale` | A linguagem da Credencial Verificável. Reservado para uso futuro. | 
| `card.title` | Apresenta o tipo de credencial ao utilizador. Comprimento máximo recomendado de 25 caracteres. | 
| `card.issuedBy` | Apresenta o nome da organização emissora ao utilizador. Comprimento máximo recomendado de 40 caracteres. |
| `card.backgroundColor` | Determina a cor de fundo do cartão, em formato hexáxinho. Um gradiente subtil será aplicado a todos os cartões. |
| `card.textColor` | Determina a cor de texto do cartão, em formato hexágeo. Recomendado para usar preto ou branco. |
| `card.logo` | Um logotipo que é apresentado no cartão. A URL fornecida deve ser publicamente endereçada. Altura máxima recomendada de 36 px e largura máxima de 100 px independentemente do tamanho do telefone. Recomendar PNG com fundo transparente. | 
| `card.description` | Texto suplementar apresentado ao lado de cada cartão. Pode ser usado para qualquer propósito. Comprimento máximo recomendado de 100 caracteres. |
| `consent.title` | Texto suplementar apresentado quando um cartão está a ser emitido. Usado para fornecer detalhes sobre o processo de emissão. Comprimento recomendado de 100 caracteres. |
| `consent.instructions` | Texto suplementar apresentado quando um cartão está a ser emitido. Usado para fornecer detalhes sobre o processo de emissão. Comprimento recomendado de 100 caracteres. |
| `claims` | Permite-lhe fornecer etiquetas para atributos incluídos em cada credencial. |
| `claims.{attribute}` | Indica o atributo da credencial a que se aplica a etiqueta. |
| `claims.{attribute}.type` | Indica o tipo de atributo. Atualmente só apoiamos 'String'. |
| `claims.{attribute}.label` | O valor que deve ser usado como etiqueta para o atributo, que aparecerá no Autenticador. Isto talvez diferente do rótulo que foi usado no ficheiro de regras. Comprimento máximo recomendado de 40 caracteres. |

>[!note]
  >Se uma reclamação for incluída no ficheiro de regras e depois omitida no ficheiro de exibição, existem dois tipos diferentes de experiências. No iOS, a alegação não será exibida na secção de detalhes mostrada na imagem acima, enquanto no Android a reclamação será mostrada.  

## <a name="next-steps"></a>Passos seguintes

Agora você tem uma melhor compreensão do design credencial verificável e como você pode criar o seu próprio para atender suas necessidades.

- [Exemplos de comunicação de serviço de emitentes](issuer-openid.md)
