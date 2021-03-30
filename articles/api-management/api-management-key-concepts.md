---
title: Descrição geral e conceitos-chave da Gestão de APIs do Azure | Microsoft Docs
description: Saiba mais sobre APIs, produtos, funções, grupos e outros conceitos-chave da API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 85fa79cdfc7036be5b0ab20e49986a1d075152c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86254661"
---
# <a name="about-api-management"></a>Acerca da Gestão de API

A Gestão de API (APIM) é uma forma de criar gateways de API consistentes e modernos para serviços de back-end existentes.

A API Management ajuda as organizações a publicar APIs a desenvolvedores externos, parceiros e internos para desbloquear o potencial dos seus dados e serviços. Um pouco por todo o lado, as empresas procuram expandir as suas operações como uma plataforma digital, criando novos canais, encontrando novos clientes e promovendo um envolvimento mais profundo com os clientes existentes. A Gestão de API fornece as competências essenciais para garantir um programa de API bem-sucedido através do envolvimento dos programadores, das informações de negócio, da análise, da segurança e da proteção. Pode utilizar a API Management do Azure para iniciar um programa de API totalmente funcional baseado em qualquer back-end.

Este artigo apresenta uma descrição geral dos cenários comuns que envolvem a APIM.  Também fornece uma breve descrição geral dos componentes principais do sistema de APIM. O artigo, em seguida, apresenta uma descrição geral mais detalhada de cada componente.

## <a name="overview"></a>Descrição Geral

Para utilizar a API Management, os administradores criam APIs. Cada API é constituída por uma ou mais operações e cada API pode ser adicionada a um ou mais produtos. Para utilizar uma API, os programadores subscrevem um produto que contém essa API e, em seguida, podem chamar a operação da API, estando sujeitos a todas as políticas de utilização que possam estar em vigor. Os cenários comuns incluem:

* **Proteção da infraestrutura móvel** mediante o controlo de acesso com chaves de API, prevenção de ataques DoS através da utilização de limitação ou utilização de políticas de segurança avançadas como a validação de token JWT.
* **Ativação de ecossistemas de parceiros ISV** ao proporcionar uma integração mais rápida dos parceiros através do portal do programador e construir uma fachada de API para a desassociação de implementações internas que não estão prontas para consumo por parte dos parceiros.
* **Execução de um programa de API interno** ao oferecer uma localização centralizada para que a organização possa comunicar sobre a disponibilidade e as alterações mais recentes às APIs ou controlar o acesso com base em contas organizacionais, tudo isto baseado num canal seguro entre o gateway da API e o back-end.

O sistema é constituído pelos seguintes componentes:

* O **portal da API** é o ponto final que:
  
  * Aceita as chamadas à API e as encaminha para os seus back-ends.
  * Verifica as chaves de API, os tokens JWT, os certificados e outras credenciais.
  * Impõe quotas de utilização e limites de velocidade.
  * Transforma a sua API imediatamente e sem modificações de código.
  * Armazena respostas de back-end em cache quando existir configuração para tal.
  * Regista metadados de chamadas para fins de análise.
* O **portal Azure** é a interface administrativa onde configura o seu programa API. Utilize-o para:
  
  * Definir ou importar o esquema de API.
  * Integrar APIs em produtos.
  * Configurar políticas como quotas ou transformações nas APIs.
  * Obter conhecimentos aprofundados a partir da análise.
  * Gerir utilizadores.
* O **Portal do programador** serve como a principal presença na Web para os programadores, onde podem:
  
  * Ler a documentação da API.
  * Experimente uma API através da consola interativa.
  * Crie uma conta e subscreva para obter as chaves de API.
  * Aceder a análises sobre a sua própria utilização.

Para obter mais informações, consulte o documento técnico em PDF [API Management baseada na nuvem: tirar partido do poder das APIs](https://j.mp/ms-apim-whitepaper). Este documento técnico introdutório sobre a API Management da CITO Research abrange: 
 
 * Requisitos e desafios de API comuns
 * Desassociar APIs e apresentar fachadas
 * Ajudar os programadores a começar a trabalhar rapidamente
 * Proteger o acesso
 * Análise e métricas
 * Obtenção de controlo e conhecimentos aprofundados com uma plataforma de API Management
 * Utilização de soluções em nuvem versus no local
 * API Management do Azure
 
## <a name="apis-and-operations"></a><a name="apis"> </a>APIs e operações
As APIs são a base de uma instância de serviço de API Management. Cada API representa um conjunto de operações disponíveis para os programadores. Cada API contém uma referência ao serviço de back-end que implementa a API e as respetivas operações efetuam o mapeamento para as operações implementadas pelo serviço de back-end. As operações da API Management são altamente configuráveis, com controlo sobre o mapeamento de URL, parâmetros de caminho e consulta, conteúdo do pedido e da resposta e colocação em cache das respostas de operações. Também é possível implementar políticas de limite de taxa, quotas e restrição de IP ao nível da API ou da operação individual.

Para obter mais informações, consulte [Como criar APIs][How to create APIs] e [Como adicionar operações a uma API][How to add operations to an API].

## <a name="products"></a><a name="products"></a> Produtos
Os produtos são a forma como as APIs são apresentadas aos programadores. Os produtos na API Management têm uma ou mais APIs e são configurados com um título, uma descrição e termos de utilização. Os produtos podem ser **Abertos** ou **Protegidos**. Os produtos protegidos têm de ser subscritos antes de poderem ser utilizados, enquanto os produtos abertos podem ser utilizados sem uma subscrição. Quando um produto estiver pronto para ser utilizado pelos programadores, pode ser publicado. Assim que for publicado, pode ser visualizado (e subscrito, no caso dos produtos protegidos) pelos programadores. A aprovação de subscrição é configurada ao nível do produto e pode exigir a aprovação do administrador ou ser aprovada automaticamente.

Os grupos são utilizados para gerir a visibilidade dos produtos para os programadores. Os produtos concedem visibilidade aos grupos e os programadores podem ver e subscrever os produtos que estão visíveis para os grupos a que pertencem. 

## <a name="groups"></a><a name="groups"></a> Grupos
Os grupos são utilizados para gerir a visibilidade dos produtos para os programadores. A Gestão de API tem os seguintes grupos de sistema imutáveis:

* **Administradores** – os administradores da subscrição do Azure são membros deste grupo. Os administradores gerem as instâncias de serviço de API Management, criando as APIs, as operações e os produtos que são utilizados pelos programadores.
* **Programadores** – os utilizadores autenticados do portal do programador enquadram-se neste grupo. Os programadores são os clientes que criam aplicações utilizando as suas APIs. Os programadores têm acesso ao portal do programador e criam aplicações que chamam as operações de uma API
* **Convidados** – os utilizadores não autenticados do portal do programador, tais como potenciais clientes que visitam o portal do programador de uma instância de API Management, enquadram-se neste grupo. Estes podem ter determinados acessos só de leitura, como a capacidade de ver APIs mas não de as chamar.

Para além destes grupos de sistema, os administradores podem criar grupos personalizados ou [tirar partido de grupos externos em inquilinos do Azure Active Directory associados](api-management-howto-aad.md). É possível utilizar grupos personalizados e externos em conjunto com grupos de sistema para dar aos programadores acesso e visibilidade a produtos de API. Por exemplo, pode criar um grupo personalizado para os programadores associados a uma organização parceira específica e permitir-lhes o acesso às APIs a partir de um produto que contém apenas APIs relevantes. Um utilizador pode ser membro de mais do que um grupo.

Para obter mais informações, consulte [Como criar e utilizar grupos][How to create and use groups].

## <a name="developers"></a><a name="developers"></a> Desenvolvedores
Os programadores representam as contas de utilizador numa instância de serviço de API Management. Os programadores podem ser criados ou convidados a aderirem por administradores, ou podem inscrever-se a partir do [Portal do programador][Developer portal]. Cada programador é membro de um ou mais grupos e pode subscrever os produtos que concedem visibilidade a esses grupos.

Quando os programadores subscrevem um produto, é-lhes concedida a chave primária e secundária desse produto. Esta chave é utilizada ao efetuar chamadas para as APIs do produto.

Para obter mais informações, consulte [Como criar ou convidar programadores][How to create or invite developers] e [Como associar grupos a programadores][How to associate groups with developers].

## <a name="policies"></a><a name="policies"></a> Políticas
As políticas são uma funcionalidade poderosa da Gestão de API que permite ao portal do Azure alterar o comportamento da API através da configuração. As políticas são uma coleção de instruções que são executadas sequencialmente no pedido ou na resposta de uma API. As instruções populares incluem a conversão do formato de XML para JSON e a limitação de taxa de chamadas para restringir o número de chamadas recebidas de um programador, bem como várias outras políticas disponíveis.

As expressões de política podem ser utilizadas como valores de atributo ou valores de texto em qualquer uma das políticas de API Management, a menos que a política especifique o contrário. Algumas políticas, como [Fluxo de controlo](./api-management-advanced-policies.md#choose) e [Definir variável](./api-management-advanced-policies.md#set-variable), baseiam-se em expressões de política. Para obter mais informações, veja [Políticas avançadas](./api-management-advanced-policies.md#AdvancedPolicies) e [Expressões de política](./api-management-policy-expressions.md).


Para obter uma lista completa das políticas de Gestão de API, consulte [Referência de política][Policy reference]. Para obter mais informações sobre como utilizar e configurar as políticas, consulte [Políticas de API Management][API Management policies]. Para consultar um tutorial sobre a criação de um produto com políticas de limite de taxa e quota, consulte [Como criar e configurar definições de produto avançadas][How create and configure advanced product settings].


## <a name="developer-portal"></a><a name="developer-portal"></a> Portal do desenvolvedor
O portal do programador é o local onde os programadores podem saber mais sobre as suas APIs, ver e chamar operações e subscrever produtos. Os potenciais clientes podem visitar o portal do programador, ver APIs e operações e inscrever-se. O URL do seu portal do programador está localizado no dashboard do portal do Azure da sua instância de serviço de Gestão de API.

Pode personalizar o aspeto e a funcionalidade do portal do programador ao adicionar conteúdo personalizado, personalizar os estilos e adicionar a sua imagem corporativa.

## <a name="api-management-and-the-api-economy"></a>API Management e a economia da API

Para obter mais informações sobre a Gestão de API, veja a seguinte apresentação da conferência Microsoft Ignite 2017.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK2186/player]
> 
> 

## <a name="next-steps"></a>Passos seguintes

Conclua o guia de início rápido seguinte e comece a utilizar a Gestão de API do Azure:

> [!div class="nextstepaction"]
> [crie uma instância da Gestão de API do Azure](get-started-create-service-instance.md)

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Developer portal]: #developer-portal

[How to create APIs]: ./import-and-publish.md
[How to add operations to an API]: ./mock-api-responses.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[How create and configure advanced product settings]: transform-api.md
[How to create or invite developers]: api-management-howto-create-or-invite-developers.md
[Policy reference]: ./api-management-policies.md
[API Management policies]: api-management-howto-policies.md
[Create an API Management service instance]: get-started-create-service-instance.md
