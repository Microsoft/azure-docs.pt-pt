---
title: Referência - estruturas de confiança no Azure Active Directory B2C | Documentos da Microsoft
description: Um tópico sobre as políticas personalizadas do Azure Active Directory B2C e o Framework de experiência de identidade.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 47e45a7dac8abc65f414fedd0fd910e3a7a78113
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508809"
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Definir as estruturas de confiança com o Framework de experiência de identidade do Azure AD B2C

O Azure Active Directory B2C (Azure AD B2C) políticas personalizadas que usam o Framework de experiência de identidade fornecem à sua organização com um serviço centralizado. Este serviço reduz a complexidade da Federação de identidades numa grande Comunidade de interesse. A complexidade é reduzida a uma relação de confiança único e uma troca de metadados de único.

O Azure AD B2C políticas personalizadas que utilizam o Framework de experiência de identidade para que possa responder às seguintes perguntas:

- Quais são legais, de segurança, privacidade e políticas de proteção de dados que devem ser seguidas?
- Quem são os contatos e quais são os processos para se tornar um participante acreditado?
- Quem são os fornecedores de informações de identidade acreditada (também conhecido como "fornecedores de afirmações") e o que fazer oferecem?
- Quem são as entidades confiadoras autorizadas (e, opcionalmente, o que elas exigem)?
- Quais são o technical "na conexão" requisitos de interoperabilidade para os participantes?
- Quais são as regras de operacionais "runtime" que devem ser impostas para a troca de informações de identidade digital?

Para responder a todas essas perguntas, construir as políticas personalizadas do Azure AD B2C que utilizam o uso de arquitetura de experiências de identidade a confiar Framework (TF). Vamos considerar essa construção e o que ele oferece.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Compreender as fundações de gestão de estrutura de confiança e a Federação

A estrutura de confiança é uma especificação de escrita da identidade, segurança, privacidade e ao qual os participantes uma Comunidade de interesse devem estar em conformidade com políticas de proteção de dados.

Identidade federada fornece uma base para atingir a garantia de identidade do utilizador final à escala da Internet. Delegação de gestão de identidades a terceiros, uma única identidade digital para um utilizador final pode ser reutilizada com várias entidades confiadoras.  

Garantia de identidade requer que Fornecedores de identidade (IdPs) e os fornecedores de atributo (AtPs) cumprem específicas de segurança, privacidade e políticas operacionais e práticas.  Se eles não podem realizar inspeções diretas, entidades confiadoras (RPs) devem desenvolver relações de confiança com os IdPs e AtPs que escolherem para trabalhar com.  

À medida que cresce o número de clientes e provedores de informações de identidade digital, é difícil continuar a gestão pairwise desses relacionamentos de confiança, ou até mesmo o exchange pairwise dos metadados técnicos necessários para conectividade de rede.  Os hubs de Federação obteve apenas limitado sucesso para resolver esses problemas.

### <a name="what-a-trust-framework-specification-defines"></a>Define que uma especificação de estrutura de confiança
TFs são linchpins do modelo de estrutura de confiança de Exchange aberto de identidade (OIX) em que cada Comunidade de interesse é regulada por uma determinada especificação TF. Define uma especificação de TF:

- **As métricas de segurança e privacidade para a Comunidade de interesse com a definição de:**
    - Os níveis de garantia (LOA) que são oferecidos/necessária para os participantes; Por exemplo, um conjunto ordenado de classificações de confiança da autenticidade das informações de identidade digital.
    - Os níveis de proteção (LOP) que são oferecidos/necessária para os participantes; Por exemplo, um conjunto ordenado de classificações de confiança para a proteção de informações de identidade digital que são processadas pelo participantes da Comunidade de interesse.

- **A descrição, as informações de identidade digital que tem oferecido/necessária por participantes**.

- **As políticas de técnicas para a produção e o consumo de informações de identidade digital e, portanto, para medir LOA e LOP. Estas políticas escritas incluem, geralmente, as seguintes categorias de políticas:**
    - Identidade protegendo contra políticas, por exemplo: *Até que ponto são informações de identidade de uma pessoa examinadas?*
    - Políticas de segurança, por exemplo: *Até que ponto são informações de integridade e a confidencialidade protegidos?*
    - Políticas de privacidade, por exemplo: *Qual o controle um utilizador tem sobre informações de identificação pessoas (PII)* ?
    - Políticas de persistência, por exemplo: *Se um fornecedor deixará de operações, como funciona a continuidade e a proteção da função PII?*

- **Os perfis técnicos para a produção e o consumo de informações de identidade digital. Estes perfis incluem:**
    - Interfaces de âmbito para os quais informações de identidade digital estão disponíveis num LOA especificado.
    - Requisitos técnicos de interoperabilidade do on-the-wire.

- **As descrições das várias funções que podem realizar os participantes da Comunidade e as qualificações necessárias para atender a essas funções.**

Assim, uma especificação de TF controla como as informações de identidade são trocadas entre os participantes da Comunidade de interesse: entidades confiadoras, identidade e provedores de atributo e verificadores de atributo.

Uma especificação de TF é um ou vários documentos que servem como uma referência para a governação de Comunidade de interesse que regula a asserção e o consumo de informações de identidade digital na Comunidade. É um conjunto documentado de políticas e procedimentos criados para estabelecer confiança nas identidades digitais, que são utilizados para transações online entre os membros de uma Comunidade de interesse.  

Em outras palavras, uma especificação de TF define as regras para a criação de um ecossistema de identidades federadas viável para uma Comunidade.

Atualmente, não há amplo acordo sobre o benefício de tal abordagem. É claro que confiança especificações do framework facilitam o desenvolvimento de ecossistemas de identidade digital com verificáveis características de segurança, controle e a privacidade, que significa que eles podem ser reutilizados em várias comunidades de interesse.

Para que o motivo, as políticas personalizadas do Azure AD B2C que usam o Framework de experiência de identidade utiliza a especificação de como a base de sua representação de dados para um TF para facilitar a interoperabilidade.  

Políticas do Azure AD B2C personalizado que tiram partido da arquitetura de experiências de identidade representam uma especificação de TF como uma combinação de dados humanas e legível por máquina. Algumas seções deste modelo (normalmente secções que são mais dedicadas a governação) são representadas como referências a documentação publicada de política de segurança e privacidade, juntamente com os procedimentos relacionados (se houver). Outras seções descrevem detalhadamente as regras de configuração do tempo de execução e metadados que facilitam a automatização operacional.

## <a name="understand-trust-framework-policies"></a>Compreender as políticas de estrutura de confiança

Em termos de implementação, a especificação de TF consiste num conjunto de políticas que permitem o controle completo sobre as experiências e comportamentos de identidade.  Políticas do Azure AD B2C personalizadas que tiram partido da arquitetura de experiências de identidade permitem-lhe criar e criar seu próprio TF por meio dessas políticas declarativas que pode definir e configurar:

- A referência de documento ou referências que definem o ecossistema de identidades federadas da Comunidade que se relaciona com o TF. Eles são links para a documentação de TF. As regras de operacionais (predefinidos) "runtime", ou as jornadas de utilizador que automatizarem e/ou controlam o exchange e o uso de afirmações. Estes jornadas de utilizador estão associadas um LOA (e um LOP). Uma política, por conseguinte, pode ter jornadas de utilizador com diferentes LOAs (e LOPs).

- Os fornecedores de identidade e o atributo ou os fornecedores de afirmações, da Comunidade de interesse e os perfis técnicos suportam juntamente com a acreditação LOA/LOP (fora de banda) que está relacionado aos mesmos.

- A integração com o verificadores de atributo ou fornecedores de afirmações.

- As entidades confiadoras da Comunidade (por inferência de tipos).

- Os metadados para estabelecer comunicações de rede entre os participantes. Estes metadados, juntamente com os perfis técnicos, são utilizados durante uma transação para ligar a "na conexão" interoperabilidade entre a entidade confiadora e outros participantes da Comunidade.

- A conversão de protocolo, se houver (por exemplo, o SAML 2.0, o OAuth2, WS-Federation e OpenID Connect).

- Os requisitos de autenticação.

- A orquestração multifator se existirem.

- Um esquema partilhado para todas as afirmações que estão disponíveis e mapeamentos aos participantes de uma Comunidade de interesse.

- Todas as afirmações transformações, juntamente com a minimização de dados neste contexto, para manter o exchange e a utilização de afirmações.

- A ligação e a encriptação.

- O armazenamento de afirmações.

### <a name="understand-claims"></a>Compreender as afirmações

> [!NOTE]
> Referimos coletivamente para todos os possíveis tipos de informações de identidade que podem ser trocadas como "afirmações": declarações sobre a credencial de autenticação do utilizador final, verificar a identidade, dispositivo de comunicação, localização física, atributos, de identificação pessoal e assim por diante.  
>
> Usamos as termo "afirmações" – em vez de "atributos-", porque nas transações online, estes artefactos de dados não são fatos que podem ser verificados diretamente pela entidade confiadora. Em vez disso, eles são asserções ou declarações, sobre factos para o qual a entidade confiadora deve desenvolver uma segurança suficiente para conceder a transação de pedido do utilizador final.  
>
> Também utilizamos o termo "afirmações" uma vez que as políticas personalizadas do Azure AD B2C que usam o Framework de experiência de identidade foram concebidas para simplificar a troca de todos os tipos de informações de identidade digital de forma consistente, independentemente se o protocolo subjacente é definido pela obtenção de autenticação ou o atributo de utilizador.  Da mesma forma, usamos o termo "fornecedores de afirmações" coletivamente referir-se a fornecedores de identidade, os provedores de atributo e verificadores de atributo, não queremos distinguir entre suas funções específicas.   

Assim, definem como as informações de identidade são trocadas entre uma entidade confiadora, identidade e provedores de atributo e verificadores de atributo. Elas controlam que identidade e provedores de atributo são necessários para a autenticação de uma terceira parte. Eles devem ser considerados como uma linguagem específica do domínio (DSL), ou seja, uma linguagem de computador que tem especializadas para um domínio de aplicativo em particular com a herança, *se* declarações, polimorfismo.

Estas políticas constituem a parte legível por máquina a construção TF nas políticas de personalizado do Azure AD B2C, aproveitando o Framework de experiência de identidade. Eles incluem todos os detalhes operacionais, incluindo os metadados e perfis técnicos, definições de esquema de afirmações, as funções de transformação de afirmações e jornadas de utilizador que são preenchidas para facilitar a orquestração operacional e a automatização de fornecedores de afirmações.  

Eles devem para ser *vida documentos* porque existe uma boa chance de que seu conteúdo será alterado ao longo do tempo sobre os Active Directory participantes declarados nas políticas. Também existe o potencial que poderão alterar os termos e condições, por ser um participante.  

Configuração de Federação e de manutenção são simplificados amplamente por entidades confiadoras de Reconfigurações de confiança e a conectividade contínuas de proteção, como verificadores/fornecedores de afirmações diferentes aderir ou sair (a Comunidade representada pelo) o conjunto de políticas.

A interoperabilidade é outro desafio significativo. Verificadores/fornecedores de afirmações adicionais devem ser integradas, como entidades confiadoras são dificilmente oferecerá suporte a todos os protocolos necessários. As políticas personalizadas do Azure AD B2C resolvem esse problema, oferecendo suporte a protocolos de norma da indústria e aplicando jornadas de utilizador específico para transpor pedidos quando confiantes e provedores de atributo não suportam o mesmo protocolo.  

Jornadas utilizador incluem perfis de protocolo e metadados que são utilizadas para ligar a "na conexão" interoperabilidade entre a entidade confiadora e outros participantes. Também existem regras de tempo de execução operacional que são aplicadas a mensagens da solicitação/resposta de exchange de informações de identidade para impor a conformidade com as políticas publicadas como parte da especificação TF. A idéia de jornadas de usuário é fundamental para a personalização da experiência do cliente. Ele também esclarece como o sistema funciona ao nível do protocolo.

Dessa forma, portais e aplicações de terceiros entidade confiadora podem, dependendo de seu contexto, invocar as políticas personalizadas do Azure AD B2C que aproveitam o Framework de experiência de identidade, passando o nome de uma política específica e obter exatamente o comportamento e as informações do exchange eles querem sem qualquer tempo, a confusão ou o risco.
