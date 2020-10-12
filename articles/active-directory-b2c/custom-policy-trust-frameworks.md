---
title: Referência - quadros de confiança no Azure Ative Directory B2C / Microsoft Docs
description: Um tópico sobre as políticas personalizadas do Azure Ative Directory B2C e o Quadro de Experiência de Identidade.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/04/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d3d29bd05f67d00047499dc256e5e1a82f98693a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388804"
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Definir quadros de confiança com Azure AD B2C Identity Experience Framework

As políticas personalizadas Azure Ative Directory B2C (Azure AD B2C) que utilizam o Quadro de Experiência de Identidade fornecem à sua organização um serviço centralizado. Este serviço reduz a complexidade da federação de identidade numa grande comunidade de interesse. A complexidade é reduzida a uma única relação de confiança e a uma única troca de metadados.

As políticas personalizadas Azure AD B2C utilizam o Quadro de Experiência de Identidade para que possa responder às seguintes questões:

- Quais são as políticas legais, de segurança, privacidade e proteção de dados que devem ser respeitadas?
- Quem são os contactos e quais são os processos para se tornar um participante acreditado?
- Quem são os fornecedores de informação de identidade acreditados (também conhecidos como "fornecedores de sinistros") e o que oferecem?
- Quem são os interessados credenciados (e opcionalmente, o que é que eles precisam)?
- Quais são os requisitos técnicos de interoperabilidade "on the wire" para os participantes?
- Quais são as regras operacionais de "tempo de execução" que devem ser aplicadas para o intercâmbio de informações de identidade digital?

Para responder a todas estas questões, as políticas personalizadas Azure AD B2C que utilizam o Quadro de Experiência de Identidade utilizam a construção do Quadro Fiduciário (TF). Vamos considerar esta construção e o que ela fornece.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Compreender o Quadro de Confiança e a Fundação de Gestão da Federação

O Quadro Fiduciário é uma especificação escrita das políticas de identidade, segurança, privacidade e proteção de dados às quais os participantes de uma comunidade de interesses devem estar em conformidade.

A identidade federada fornece uma base para alcançar a garantia de identidade do utilizador final à escala da Internet. Ao delegar a gestão de identidade a terceiros, uma única identidade digital para um utilizador final pode ser reutilizada com várias partes que contam.

A garantia de identidade requer que os fornecedores de identidade (IdPs) e os fornecedores de atributos (AtPs) aderam a políticas e práticas específicas de segurança, privacidade e operacionais.  Se não puderem realizar inspeções diretas, as partes dependentes (RPs) devem desenvolver relações de confiança com os IdPs e AtPs com os quais escolhem trabalhar.

À medida que o número de consumidores e fornecedores de informação de identidade digital aumenta, é difícil continuar a gestão em pares destas relações de confiança, ou mesmo a troca em pares dos metadados técnicos necessários para a conectividade da rede.  Os centros da Federação alcançaram apenas um sucesso limitado na resolução destes problemas.

### <a name="what-a-trust-framework-specification-defines"></a>O que uma especificação do Quadro de Confiança define
Os TFs são os eixos do modelo de Enquadramento de Confiança da Open Identity Exchange (OIX), onde cada comunidade de interesses é regida por uma especificação específica da TF. Tal especificação de TF define:

- **As métricas de segurança e privacidade para a comunidade de interesse com a definição de:**
    - Os níveis de garantia (LOA) oferecidos/exigidos pelos participantes; por exemplo, um conjunto ordenado de classificações de confiança para a autenticidade da informação de identidade digital.
    - Os níveis de proteção (LOP) oferecidos/exigidos pelos participantes; por exemplo, um conjunto ordenado de classificações de confiança para a proteção de informações de identidade digital que são tratadas pelos participantes na comunidade de interesse.

- **A descrição da informação de identidade digital que é oferecida/exigida pelos participantes.**

- **As políticas técnicas de produção e consumo de informação de identidade digital, e assim a medição de LOA e LOP. Estas políticas escritas incluem tipicamente as seguintes categorias de políticas:**
    - Políticas de impermeabilização de identidade, por exemplo: *Quão fortemente é verificada a informação de identidade de uma pessoa?*
    - Políticas de segurança, por exemplo: *Quão fortemente a integridade da informação e a confidencialidade são protegidas?*
    - Políticas de privacidade, por exemplo: *Que controlo tem um utilizador sobre informações pessoais identificáveis (PII)*?
    - Políticas de sobrevivência, por exemplo: *Se um prestador cessa as suas operações, como funciona a continuidade e a proteção do PII?*

- **Os perfis técnicos para a produção e consumo de informação de identidade digital. Estes perfis incluem:**
    - Interfaces de âmbito para as quais a informação de identidade digital está disponível num LOA especificado.
    - Requisitos técnicos para a interoperabilidade no fio.

- **As descrições das várias funções que os participantes na comunidade podem desempenhar e as qualificações que são necessárias para o desempenho destas funções.**

Assim, uma especificação de TF regula a forma como as informações de identidade são trocadas entre os participantes da comunidade de interesse: as partes dependentes, os fornecedores de identidade e atributos, e atribuim verificadores.

Uma especificação de TF é um ou vários documentos que servem de referência para a governação da comunidade de interesses que regula a afirmação e consumo de informação de identidade digital dentro da comunidade. É um conjunto documentado de políticas e procedimentos projetados para estabelecer a confiança nas identidades digitais que são usadas para transações online entre membros de uma comunidade de interesse.

Por outras palavras, uma especificação de TF define as regras para a criação de um ecossistema de identidade federado viável para uma comunidade.

Atualmente, existe um acordo generalizado sobre o benefício de tal abordagem. Não há dúvida de que as especificações do quadro de confiança facilitam o desenvolvimento de ecossistemas de identidade digital com características de segurança, garantia e privacidade verificáveis, o que significa que podem ser reutilizados em várias comunidades de interesse.

Por essa razão, as políticas personalizadas Azure AD B2C que utilizam o Quadro de Experiência de Identidade utilizam a especificação como base da sua representação de dados para uma TF para facilitar a interoperabilidade.

As políticas personalizadas Azure AD B2C que alavancam o Quadro de Experiência de Identidade representam uma especificação TF como uma mistura de dados humanos e legíveis por máquinas. Algumas secções deste modelo (normalmente secções mais orientadas para a governação) são representadas como referências à documentação publicada sobre segurança e política de privacidade, juntamente com os procedimentos conexos (se houver). Outras secções descrevem detalhadamente os metadados de configuração e as regras de tempo de execução que facilitam a automatização operacional.

## <a name="understand-trust-framework-policies"></a>Compreender as políticas do Quadro de Confiança

Em termos de implementação, a especificação TF consiste num conjunto de políticas que permitem o controlo total sobre comportamentos e experiências identitárias.  As políticas personalizadas Azure AD B2C que alavancam o Quadro de Experiência de Identidade permitem-lhe autoria e criação do seu próprio TF através de políticas declarativas que podem definir e configurar:

- O documento refere ou referências que definem o ecossistema identitário federado da comunidade que se relaciona com a TF. São ligações com a documentação da TF. As regras de "tempo de execução" operacionais (predefinidas) ou as viagens de utilizador que automatizam e/ou controlam a troca e utilização das reclamações. Estas viagens de utilizador estão associadas a um LOA (e a um LOP). Uma política pode, portanto, ter viagens de utilizador com DIFERENTES LOAs (e LOPs).

- Os fornecedores de identidade e atributos, ou os prestadores de sinistros, na comunidade de interesse e os perfis técnicos que apoiam, juntamente com a acreditação LOA/LOP (fora de banda) que lhes diz respeito.

- A integração com verificadores de atributos ou fornecedores de sinistros.

- Os partidos que dependem da comunidade (por inferência).

- Os metadados para o estabelecimento de comunicações de rede entre os participantes. Estes metadados, juntamente com os perfis técnicos, são utilizados durante uma transação para canalizar a interoperabilidade "no fio" entre a parte de suporte e outros participantes da comunidade.

- A conversão do protocolo se houver (por exemplo, SAML 2.0, OAuth2, WS-Federation e OpenID Connect).

- Os requisitos de autenticação.

- A orquestração multifactor, se houver.

- Um esquema partilhado para todas as reivindicações que estão disponíveis e mapeamentos para os participantes de uma comunidade de interesse.

- Todas as transformações de sinistros, juntamente com a possível minimização de dados neste contexto, para sustentar a troca e utilização das reclamações.

- A ligação e encriptação.

- O armazém de reclamações.

### <a name="understand-claims"></a>Compreender as reclamações

> [!NOTE]
> Referimo-nos coletivamente a todos os tipos possíveis de informações de identidade que podem ser trocadas como "alegações": alegações sobre a credencial de autenticação de um utilizador final, verificação de identidade, dispositivo de comunicação, localização física, atributos de identificação pessoal, e assim por diante.
>
> Usamos o termo "alegações"-- em vez de "atributos"-- porque em transações online, estes artefactos de dados não são factos que podem ser verificados diretamente pela parte que conta. Pelo contrário, são afirmações, ou afirmações, sobre factos para os quais a parte responsável deve desenvolver confiança suficiente para conceder a transação solicitada pelo utilizador final.
>
> Também usamos o termo "alegações" porque as políticas personalizadas Azure AD B2C que utilizam o Quadro de Experiência de Identidade são projetadas para simplificar a troca de todos os tipos de informações de identidade digital de forma consistente, independentemente de o protocolo subjacente ser definido para a autenticação ou recolha de atributos do utilizador.  Da mesma forma, utilizamos o termo "fornecedores de sinistros" para se referirem colectivamente a fornecedores de identidade, atribuem fornecedores e atribuem verificadores quando não queremos distinguir entre as suas funções específicas.

Assim, regem a forma como a informação de identidade é trocada entre uma parte, identidade e fornecedores de atributos, e atribuem verificadores. Controlam a identidade e os fornecedores de atributos necessários para a autenticação de uma parte dependente. Devem ser considerados como uma língua específica do domínio (DSL), isto é, uma linguagem computacional especializada para um determinado domínio de aplicação com herança, *se* declarações, polimorfismo.

Estas políticas constituem a parte legível por máquina da construção da TF nas políticas personalizadas Azure AD B2C que aproveitam o Quadro de Experiência de Identidade. Incluem todos os detalhes operacionais, incluindo metadados e perfis técnicos dos fornecedores de sinistros, definições de esquemas de reclamações, funções de transformação de sinistros e viagens de utilizador que são preenchidas para facilitar a orquestração operacional e a automatização.

Presume-se que são *documentos vivos* porque há boas hipóteses de o seu conteúdo mudar ao longo do tempo relativamente aos participantes ativos declarados nas políticas. Existe também o potencial de que os termos e condições para ser participante possam mudar.

A configuração e manutenção da Federação são amplamente simplificadas protegendo as partes dependentes de reconfigurações de confiança e conectividade em curso, à medida que diferentes fornecedores/verificadores de sinistros se juntam ou saem (a comunidade representada por) o conjunto de políticas.

A interoperabilidade é outro desafio significativo. Os fornecedores/verificadores de sinistros adicionais devem ser integrados, uma vez que é pouco provável que as partes que contam apoiar todos os protocolos necessários. As políticas personalizadas Azure AD B2C resolvem este problema apoiando protocolos padrão do setor e aplicando viagens específicas de utilizadores para transpor pedidos quando as partes que confiam e os fornecedores de atributos não suportam o mesmo protocolo.

As viagens de utilizador incluem perfis de protocolo e metadados que são usados para canalizar a interoperabilidade "no fio" entre a parte que conta e outros participantes. Existem também regras de execução operacionais que são aplicadas a pedidos de intercâmbio de informações de identidade/mensagens de resposta para a aplicação do cumprimento das políticas publicadas como parte da especificação TF. A ideia de viagens de utilizador é fundamental para a personalização da experiência do cliente. Também esclarece como o sistema funciona ao nível do protocolo.

Nesta base, as aplicações e portais das partes podem, dependendo do seu contexto, invocar políticas personalizadas Azure AD B2C que alavancam o Quadro de Experiência de Identidade passando o nome de uma política específica e obter precisamente o comportamento e troca de informações que desejam sem qualquer muss, alarido ou risco.
