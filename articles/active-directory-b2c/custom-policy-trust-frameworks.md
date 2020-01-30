---
title: Referência - quadros de confiança no Diretório Ativo Azure B2C  Microsoft Docs
description: Um tema sobre as políticas personalizadas do Azure Ative Directory B2C e o Quadro de Experiência de Identidade.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e96ddcb904bbda6c3123ffc9d3da50ff80823689
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849165"
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Definir Quadros de Confiança com Quadro de Experiência de Identidade Azure AD B2C

As políticas personalizadas do Azure Ative Directory B2C (Azure AD B2C) que utilizam o Quadro de Experiência de Identidade fornecem à sua organização um serviço centralizado. Este serviço reduz a complexidade da federação identitária numa grande comunidade de interesses. A complexidade é reduzida a uma única relação de confiança e a uma única troca de metadados.

As políticas personalizadas azure AD B2C utilizam o Quadro de Experiência de Identidade para lhe permitir responder às seguintes questões:

- Quais são as políticas legais, de segurança, privacidade e proteção de dados que devem ser cumpridas?
- Quem são os contactos e quais são os processos para se tornar um participante credenciado?
- Quem são os fornecedores de informação de identidade acreditados (também conhecidos como "fornecedores de sinistros") e o que oferecem?
- Quem são os partidos de base acreditados (e opcionalmente, o que precisam)?
- Quais são os requisitos técnicos de interoperabilidade "no fio" para os participantes?
- Quais são as regras operacionais de "tempo de funcionamento" que devem ser aplicadas para a troca de informação de identidade digital?

Para responder a todas estas questões, as políticas personalizadas Azure AD B2C que utilizam o Quadro de Experiência de Identidade utilizam a construção trust Framework (TF). Vamos considerar esta construção e o que ela fornece.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Compreender o Quadro fiduciário e a fundação de gestão da federação

O Quadro Fiduciário é uma especificação escrita das políticas de identidade, segurança, privacidade e proteção de dados às quais os participantes numa comunidade de interesse devem estar em conformidade.

A identidade federada fornece uma base para alcançar a garantia de identidade do utilizador final à escala da Internet. Ao delegar a gestão de identidade a terceiros, uma única identidade digital para um utilizador final pode ser reutilizada com vários interessados.  

A garantia de identidade requer que os fornecedores de identidade (IDPs) e os fornecedores de atributos (ATPs) aderem a políticas e práticas específicas de segurança, privacidade e operacionais.  Se não conseguirem realizar inspeções diretas, os partidos que contam (RPs) devem desenvolver relações de confiança com os IDPs e atPs com os quais optam por trabalhar.  

À medida que o número de consumidores e fornecedores de informação de identidade digital aumenta, é difícil continuar a gestão em pares destas relações de confiança, ou mesmo a troca dupla dos metadados técnicos que são necessários para a conectividade da rede.  Os centros da Federação só alcançaram um sucesso limitado na resolução destes problemas.

### <a name="what-a-trust-framework-specification-defines"></a>O que uma especificação de quadro de confiança define
Os TFs são os eixos do modelo de quadro de confiança da Open Identity Exchange (OIX), onde cada comunidade de interesse é regida por uma especificação específica da TF. Tal especificação TF define:

- **As métricas de segurança e privacidade para a comunidade de interesse com a definição de:**
    - Os níveis de garantia (LOA) oferecidos/exigidos pelos participantes; por exemplo, um conjunto ordenado de classificações de confiança para a autenticidade da informação de identidade digital.
    - Os níveis de proteção (LOP) oferecidos/exigidos pelos participantes; por exemplo, um conjunto ordenado de classificações de confiança para a proteção de informações de identidade digital que é tratado pelos participantes na comunidade de interesse.

- **A descrição da informação de identidade digital que é oferecida/exigida pelos participantes.**

- **As políticas técnicas de produção e consumo de informação sobre identidade digital, bem como a medição da LOA e da LOP. Estas políticas escritas normalmente incluem as seguintes categorias de políticas:**
    - Políticas de revisão de identidade, por exemplo: *Quão fortemente a informação de identidade de uma pessoa é examinada?*
    - Políticas de segurança, por exemplo: *Quão fortemente a integridade da informação e a confidencialidade estão protegidas?*
    - Políticas de privacidade, por exemplo: *Que controlo tem um utilizador sobre informações pessoais identificáveis (PII)* ?
    - Políticas de sobrevivência, por exemplo: *Se um fornecedor cessa as suas operações, como funciona a continuidade e a proteção do PII?*

- **Os perfis técnicos para a produção e consumo de informação de identidade digital. Estes perfis incluem:**
    - Interfaces de âmbito para as quais a informação de identidade digital está disponível numa LOA especificada.
    - Requisitos técnicos para a interoperabilidade no cabo.

- **As descrições das várias funções que os participantes na comunidade podem desempenhar e as qualificações que são necessárias para desempenhar essas funções.**

Assim, uma especificação TF rege a forma como a informação de identidade é trocada entre os participantes da comunidade de interesses: partes dependentes, fornecedores de identidade e atributos, e verificadores de atributos.

Uma especificação TF é um ou vários documentos que servem de referência para a governação da comunidade de interesse que regula a afirmação e consumo de informação de identidade digital dentro da comunidade. É um conjunto documentado de políticas e procedimentos projetados para estabelecer confiança nas identidades digitais que são usadas para transações online entre membros de uma comunidade de interesse.  

Por outras palavras, uma especificação TF define as regras para a criação de um ecossistema de identidade federado viável para uma comunidade.

Atualmente, existe um acordo generalizado sobre o benefício de tal abordagem. Não há dúvida de que as especificações de quadro de confiança facilitam o desenvolvimento de ecossistemas de identidade digital com características verificáveis de segurança, garantia e privacidade, o que significa que podem ser reutilizados em várias comunidades de interesse.

Por essa razão, as políticas personalizadas Azure AD B2C que utilizam o Quadro de Experiência de Identidade utilizam a especificação como base da sua representação de dados para um TF para facilitar a interoperabilidade.  

As políticas personalizadas Azure AD B2C que alavancam o Quadro de Experiência de Identidade representam uma especificação TF como uma mistura de dados humanos e legíveis por máquinas. Algumas secções deste modelo (tipicamente secções mais orientadas para a governação) são representadas como referências à documentação publicada sobre segurança e política de privacidade, juntamente com os procedimentos conexos (se houver). Outras secções descrevem detalhadamente os metadados de configuração e as regras de tempo de funcionamento que facilitam a automatização operacional.

## <a name="understand-trust-framework-policies"></a>Compreender as políticas do Quadro de Confiança

Em termos de implementação, a especificação TF consiste num conjunto de políticas que permitem o controlo total sobre comportamentos e experiências identitárias.  As políticas personalizadas azure AD B2C que alavancam o Quadro de Experiência de Identidade permitem-lhe autor e criar o seu próprio TF através de políticas declarativas que podem definir e configurar:

- A referência do documento ou referências que definem o ecossistema de identidade federado da comunidade que se relaciona com o TF. São ligações à documentação da TF. As regras (predefinidas) operacionais de "tempo de funcionamento" ou as viagens de utilizador que automatizam e/ou controlam a troca e utilização das reclamações. Estas viagens de utilizador estão associadas a uma LOA (e a LOP). Uma política pode, portanto, ter viagens de utilizador com loAs variados (e LOPs).

- Os fornecedores de identidade e atributos, ou os prestadores de sinistros, na comunidade de interesses e os perfis técnicos que apoiam juntamente com a acreditação (fora da banda) LOA/LOP que lhes diz respeito.

- A integração com verificadores de atributos ou prestadores de sinistros.

- Os partidos que confiam na comunidade (por inferência).

- Os metadados para o estabelecimento de comunicações de rede entre os participantes. Estes metadados, juntamente com os perfis técnicos, são utilizados durante uma transação para canalizar interoperabilidade "no fio" entre a parte que depende e outros participantes da comunidade.

- A conversão do protocolo se houver (por exemplo, SAML 2.0, OAuth2, WS-Federation e OpenID Connect).

- Os requisitos de autenticação.

- A orquestração multifactor, se houver.

- Um esquema partilhado para todas as reivindicações que estão disponíveis e mapeamentopara os participantes de uma comunidade de interesse.

- Todas as transformações de sinistros, juntamente com a possível minimização de dados neste contexto, para sustentar a troca e utilização das reclamações.

- A ligação e a encriptação.

- O armazenamento de reclamações.

### <a name="understand-claims"></a>Compreender as reivindicações

> [!NOTE]
> Referimo-nos colectivamente a todos os tipos possíveis de informação de identidade que possam ser trocadas como "alegações": alegações sobre a credencial de autenticação de um utilizador final, verificação de identidade, dispositivo de comunicação, localização física, identificação pessoal de atributos, e assim por diante.  
>
> Usamos o termo "reivindicações"-- em vez de "atributos"-- porque em transações online, estes artefactos de dados não são factos que podem ser verificados diretamente pela parte que confia. Em vez disso, são afirmações, ou afirmações, sobre factos para os quais a parte que depende deve desenvolver confiança suficiente para conceder a transação solicitada pelo utilizador final.  
>
> Também usamos o termo "reivindicações" porque as políticas personalizadas do Azure AD B2C que utilizam o Quadro de Experiência de Identidade são projetadas para simplificar o intercâmbio de todos os tipos de informação de identidade digital de forma consistente, independentemente de o protocolo subjacente definido para autenticação do utilizador ou recuperação de atributos.  Da mesma forma, utilizamos o termo "prestadores de sinistros" para se referir colectivamente a fornecedores de identidade, atribuírem fornecedores e atribuírem verificadores quando não queremos distinguir as suas funções específicas.   

Assim, regem a forma como a informação de identidade é trocada entre um grupo de confiança, fornecedores de identidade e atributos, e atribuem verificadores. Controlam a identidade e os fornecedores de atributos necessários para a autenticação de uma parte que depende. Devem ser considerados como uma linguagem específica de domínio (DSL), isto é, uma linguagem informática especializada para um determinado domínio de aplicação com herança, *se* declarações, polimorfismo.

Estas políticas constituem a parte legível pela máquina da construção TF em Políticas personalizadas Azure AD B2C alavancando o Quadro de Experiência de Identidade. Incluem todos os detalhes operacionais, incluindo metadados e perfis técnicos dos fornecedores de sinistros, alega definições de esquemas, funções de transformação de sinistros e viagens de utilizador que são preenchidas para facilitar a orquestração operacional e a automação.  

Presume-se que são *documentos vivos* porque há boas hipóteses de o seu conteúdo mudar ao longo do tempo relativamente aos participantes ativos declarados nas políticas. Existe também o potencial de que os termos e condições para ser participante possam mudar.  

A configuração e manutenção da Federação são amplamente simplificadas protegendo as partes dependentes de reconfigurações contínuas de confiança e conectividade, à medida que diferentes fornecedores de sinistros/verificadores se juntam ou saem (a comunidade representada por) o conjunto de políticas.

A interoperabilidade é outro desafio significativo. Devem ser integrados fornecedores/verificadores de sinistros adicionais, uma vez que é pouco provável que as partes que dependem apoiem todos os protocolos necessários. As políticas personalizadas do Azure AD B2C resolvem este problema apoiando protocolos padrão da indústria e aplicando viagens específicas de utilizadores para transpor pedidos quando as partes que dependem e atribuem fornecedores não suportam o mesmo protocolo.  

As viagens dos utilizadores incluem perfis protocolares e metadados que são usados para mergulhar interoperabilidade "no fio" entre a parte que depende e outros participantes. Existem também regras operacionais de tempo de execução que são aplicadas às mensagens de pedido/resposta de troca de informações de identidade para a aplicação do cumprimento das políticas publicadas como parte do caderno de encargos da TF. A ideia das viagens de utilizador é fundamental para a personalização da experiência do cliente. Também esclarece como o sistema funciona a nível protocolário.

Nessa base, as aplicações e portais do partido podem, dependendo do seu contexto, invocar políticas personalizadas Azure AD B2C que alavancam o Quadro de Experiência de Identidade passando o nome de uma política específica e obter precisamente o comportamento e troca de informação querem sem muss, alarido ou risco.
