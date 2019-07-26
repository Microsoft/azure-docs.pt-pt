---
title: Referência-estruturas confiáveis no Azure Active Directory B2C | Microsoft Docs
description: Um tópico sobre Azure Active Directory B2C políticas personalizadas e a estrutura de experiência de identidade.
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
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500018"
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Definir estruturas de confiança com Azure AD B2C estrutura de experiência de identidade

As políticas personalizadas de Azure Active Directory B2C (Azure AD B2C) que usam a estrutura de experiência de identidade fornecem à sua organização um serviço centralizado. Esse serviço reduz a complexidade da Federação de identidades em uma grande comunidade de interesse. A complexidade é reduzida a uma única relação de confiança e a uma única troca de metadados.

Azure AD B2C políticas personalizadas usam a estrutura de experiência de identidade para permitir que você responda às seguintes perguntas:

- Quais são as políticas legais, de segurança, de privacidade e de proteção de dados que devem ser seguidas?
- Quem são os contatos e quais são os processos para se tornar um participante reconhecido?
- Quem são os provedores de informações de identidade credenciados (também conhecidos como "provedores de declarações") e o que eles oferecem?
- Quem são as partes confiantes autorizadas (e, opcionalmente, o que precisam)?
- Quais são os requisitos de interoperabilidade técnica "na transmissão" para os participantes?
- Quais são as regras de "tempo de execução" operacionais que devem ser impostas para a troca de informações de identidade digital?

Para responder a todas essas perguntas, Azure AD B2C políticas personalizadas que usam a estrutura de experiência de identidade usam a construção da estrutura de confiança (TF). Vamos considerar esse constructo e o que ele fornece.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Entender a estrutura confiável e o Federation Management Foundation

A estrutura confiável é uma especificação escrita de identidade, segurança, privacidade e políticas de proteção de dados para as quais os participantes de uma comunidade de interesse devem estar em conformidade.

A identidade federada fornece uma base para atingir a garantia de identidade do usuário final na escala da Internet. Ao delegar o gerenciamento de identidade a terceiros, uma única identidade digital para um usuário final pode ser reutilizada com várias partes confiáveis.  

O Identity Assurance requer que os provedores de identidade (IdPs) e os provedores de atributos (AtPs) obedeçam a segurança, privacidade e políticas operacionais e práticas específicas.  Se eles não conseguirem realizar inspeções diretas, o RPs (terceiras partes confiáveis) deve desenvolver relações de confiança com o IdPs e AtPs com as quais eles optam por trabalhar.  

À medida que aumenta o número de consumidores e provedores de informações de identidade digital, é difícil continuar o gerenciamento de paridade dessas relações de confiança ou até mesmo a troca emparelhada dos metadados técnicos necessários para a conectividade de rede.  Os hubs de Federação atingiram apenas o êxito limitado na solução desses problemas.

### <a name="what-a-trust-framework-specification-defines"></a>O que uma especificação de estrutura confiável define
O TFs é o base do modelo de estrutura confiável de OIX (Open Identity Exchange), em que cada comunidade de interesse é regida por uma determinada especificação de TF. Essa especificação de TF define:

- **As métricas de segurança e privacidade para a comunidade de interesse com a definição de:**
    - Os níveis de garantia (LOA) que são oferecidos/exigidos pelos participantes; por exemplo, um conjunto ordenado de classificações de confiança para a autenticidade das informações de identidade digital.
    - Os níveis de proteção (LOP) que são oferecidos/exigidos pelos participantes; por exemplo, um conjunto ordenado de classificações de confiança para a proteção de informações de identidade digital que é tratada pelos participantes da comunidade de interesse.

- **A descrição das informações de identidade digital que são oferecidas/exigidas pelos participantes**.

- **As políticas técnicas para produção e consumo de informações de identidade digital e, portanto, para medir LOA e LOP. Essas políticas escritas normalmente incluem as seguintes categorias de políticas:**
    - Políticas de verificação de identidade, por exemplo: *Quão forte é a verificados das informações de identidade de uma pessoa?*
    - Políticas de segurança, por exemplo: *Quão fortemente é a integridade das informações e a confidencialidade protegidas?*
    - Políticas de privacidade, por exemplo: *Que controle um usuário tem sobre informações de identificação pessoal (PII)* ?
    - Políticas de sustentabilidade, por exemplo: *Se um provedor parar as operações, como a continuidade e a proteção da função PII?*

- **Os perfis técnicos para produção e consumo de informações de identidade digital. Esses perfis incluem:**
    - Interfaces de escopo para as quais as informações de identidade digital estão disponíveis em um LOA especificado.
    - Requisitos técnicos para interoperabilidade durante a transmissão.

- **As descrições das várias funções que os participantes da Comunidade podem executar e as qualificações necessárias para atender a essas funções.**

Portanto, uma especificação de TF rege como as informações de identidade são trocadas entre os participantes da comunidade de interesse: terceiras partes confiáveis, identidade e provedores de atributos e verificadores de atributos.

Uma especificação de TF é um ou vários documentos que servem como referência para a governança da comunidade de interesse que regula a asserção e o consumo de informações de identidade digital dentro da Comunidade. Trata-se de um conjunto documentado de políticas e procedimentos projetados para estabelecer confiança nas identidades digitais que são usadas para transações online entre membros de uma comunidade de interesse.  

Em outras palavras, uma especificação TF define as regras para criar um ecossistema de identidade federada viável para uma comunidade.

Atualmente, há um acordo generalizado sobre o benefício dessa abordagem. Não há dúvida de que as especificações da estrutura confiável facilitam o desenvolvimento de ecossistemas de identidade digital com segurança verificável, garantia e características de privacidade, o que significa que eles podem ser reutilizados em várias comunidades de interesse.

Por esse motivo, Azure AD B2C políticas personalizadas que usam a estrutura de experiência de identidade usam a especificação como a base de sua representação de dados para uma TF a fim de facilitar a interoperabilidade.  

Azure AD B2C políticas personalizadas que aproveitam a estrutura de experiência de identidade representam uma especificação de TF como uma combinação de dados humanos e legíveis por máquina. Algumas seções desse modelo (normalmente seções que são mais orientadas para governança) são representadas como referências à documentação de política de privacidade e segurança publicada junto com os procedimentos relacionados (se houver). Outras seções descrevem em detalhes os metadados de configuração e as regras de tempo de execução que facilitam a automação operacional.

## <a name="understand-trust-framework-policies"></a>Entender as políticas da estrutura de confiança

Em termos de implementação, a especificação TF consiste em um conjunto de políticas que permite o controle completo sobre os comportamentos e as experiências de identidade.  Azure AD B2C as políticas personalizadas que aproveitam a estrutura de experiência de identidade permitem a criação e a criação de sua própria TF por meio de políticas declarativas que podem definir e configurar:

- A referência de documento ou referências que definem o ecossistema de identidade federada da Comunidade que se relaciona com a TF. Eles são links para a documentação do TF. As regras de "tempo de execução" operacionais (predefinidas) ou as viagens do usuário que automatizam e/ou controlam a troca e o uso das declarações. Esses percursos do usuário estão associados a um LOA (e um LOP). Portanto, uma política pode ter percursos do usuário com diferentes LOAs (e LOPs).

- Os provedores de identidade e de atributo, ou os provedores de declarações, na Comunidade de interesse e os perfis técnicos aos quais eles dão suporte, junto com a certificação de LOA/LOP (fora de banda) relacionada a eles.

- A integração com verificadores de atributo ou provedores de declarações.

- As partes confiantes na Comunidade (por inferência).

- Os metadados para estabelecer comunicações de rede entre os participantes. Esses metadados, juntamente com os perfis técnicos, são usados durante uma transação para direcionar a interoperabilidade "na transmissão" entre a terceira parte confiável e outros participantes da Comunidade.

- A conversão de protocolo, se houver (por exemplo, SAML 2,0, OAuth2, WS-Federation e OpenID Connect).

- Os requisitos de autenticação.

- A orquestração multifator, se houver.

- Um esquema compartilhado para todas as declarações que estão disponíveis e mapeamentos para os participantes de uma comunidade de interesse.

- Todas as transformações de declarações, juntamente com a possível minimização de dados neste contexto, para sustentar a troca e o uso das declarações.

- A associação e a criptografia.

- O armazenamento de declarações.

### <a name="understand-claims"></a>Entender as declarações

> [!NOTE]
> Coletivamente, nos referimos a todos os possíveis tipos de informações de identidade que podem ser trocadas como "declarações": declarações sobre a credencial de autenticação de um usuário final, identidade habilitação, dispositivo de comunicação, localização física, atributos de identificação pessoal, e assim por diante.  
>
> Usamos o termo "declarações", em vez de "atributos", porque em transações online, esses artefatos de dados não são fatos que podem ser verificados diretamente pela terceira parte confiável. Em vez disso, eles são asserções, ou declarações, sobre fatos para os quais a terceira parte confiável deve desenvolver confiança suficiente para conceder a transação solicitada do usuário final.  
>
> Também usamos o termo "declarações" porque Azure AD B2C políticas personalizadas que usam a estrutura de experiência de identidade foram projetadas para simplificar a troca de todos os tipos de informações de identidade digital de forma consistente, independentemente de o protocolo subjacente ser definido para a autenticação de usuário ou recuperação de atributo.  Da mesma forma, usamos o termo "provedores de declarações" para se referirmos coletivamente a provedores de identidade, provedores de atributos e verificadores de atributos quando não queremos distinguir entre suas funções específicas.   

Portanto, eles regem como as informações de identidade são trocadas entre uma terceira parte confiável, identidade e provedores de atributos e verificadores de atributos. Eles controlam quais identidade e provedores de atributo são necessários para a autenticação de uma terceira parte confiável. Elas devem ser consideradas como uma DSL (linguagem específica de domínio), ou seja, uma linguagem de computador especializada para um domínio de aplicativo específico com herança, instruções *If* , polimorfismo.

Essas políticas constituem a parte legível por computador da construção TF em Azure AD B2C políticas personalizadas, aproveitando a estrutura de experiência de identidade. Eles incluem todos os detalhes operacionais, incluindo metadados de provedores de declarações e perfis técnicos, definições de esquema de declarações, funções de transformação de declarações e percursos do usuário que são preenchidos para facilitar a automação e a orquestração operacional.  

Eles são considerados *documentos vivos* porque há uma boa chance de que seu conteúdo seja alterado ao longo do tempo em relação aos participantes ativos declarados nas políticas. Também há o potencial de que os termos e condições para ser um participante possam mudar.  

A configuração e a manutenção da Federação são amplamente simplificadas por meio da blindagem de partes confiantes das reconfigurações de conectividade e de confiança contínuas como diferentes provedores de declarações/verificadores ingressam ou deixam (a Comunidade representada pelo) o conjunto de políticas.

A interoperabilidade é outro desafio significativo. Provedores/verificadores de declarações adicionais devem ser integrados, pois as terceiras partes confiáveis têm pouca probabilidade de dar suporte a todos os protocolos necessários. Azure AD B2C políticas personalizadas resolvem esse problema oferecendo suporte a protocolos padrão do setor e aplicando percursos do usuário específicos para transpor solicitações quando as partes confiáveis e os provedores de atributo não dão suporte ao mesmo protocolo.  

Os percursos do usuário incluem perfis de protocolo e metadados que são usados para encanar a interoperabilidade "na rede" entre a terceira parte confiável e outros participantes. Também há regras de tempo de execução operacional que são aplicadas a mensagens de solicitação/resposta de informações de identidade para impor a conformidade com as políticas publicadas como parte da especificação do TF. A ideia de percursos do usuário é fundamental para a personalização da experiência do cliente. Ele também esclarece a luz sobre como o sistema funciona no nível do protocolo.

Dessa forma, os portais e os aplicativos de terceira parte confiável podem, dependendo do contexto, invocar Azure AD B2C políticas personalizadas que aproveitam a estrutura de experiência de identidade, passando o nome de uma política específica e obter precisamente o comportamento e a troca de informações Eles querem sem qualquer problema, confusão ou risco.
