---
title: Design de identidade híbrida - requisitos de proteção de dados Azure | Microsoft Docs
description: Ao planear a sua solução de identidade híbrida, identifique os requisitos de proteção de dados para o seu negócio e quais as opções disponíveis para melhor satisfazer estes requisitos.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 40dc4baa-fe82-4ab6-a3e4-f36fa9dcd0df
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 056d0caf2bfc6e99d5d659a2561bc41844feb79a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "64918784"
---
# <a name="plan-for-enhancing-data-security-through-a-strong-identity-solution"></a>Plano para reforçar a segurança dos dados através de uma solução de identidade forte
O primeiro passo para proteger os dados é identificar quem pode aceder a esses dados. Além disso, é necessário ter uma solução de identidade que possa integrar-se com o seu sistema para fornecer capacidades de autenticação e autorização. A autenticação e autorização são muitas vezes confundidas entre si e os seus papéis mal compreendidos. Na realidade, são diferentes, como mostra a figura abaixo:

![ciclo de vida do dispositivo móvel](./media/plan-hybrid-identity-design-considerations/mobile-devicemgt-lifecycle.png)

**Fases do ciclo de vida da gestão de dispositivos móveis**

Ao planear a sua solução de identidade híbrida, deve compreender os requisitos de proteção de dados para o seu negócio e quais as opções disponíveis para melhor satisfazer estes requisitos.

> [!NOTE]
> Assim que terminar o planeamento para a segurança dos [dados, reveja Os requisitos de autenticação de vários fatores](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md) para garantir que as suas seleções relativas aos requisitos de autenticação de vários fatores não foram afetadas pelas decisões que tomou nesta secção.
> 
> 

## <a name="determine-data-protection-requirements"></a>Determinar os requisitos de proteção de dados
Na era da mobilidade, a maioria das empresas tem um objetivo comum: permitir que os seus utilizadores sejam produtivos nos seus dispositivos móveis, enquanto estão no local, ou remotamente de qualquer lugar, de forma a aumentar a produtividade. As empresas que possuam tais requisitos também estarão preocupadas com o número de ameaças que devem ser atenuadas de forma a manter os dados da empresa seguros e manter a privacidade dos utilizadores. Cada empresa pode ter requisitos diferentes a este respeito; diferentes regras de conformidade que variarão de acordo com a indústria que a empresa está a agir levará a diferentes decisões de design. 

No entanto, existem alguns aspetos de segurança que devem ser explorados e validados, independentemente da indústria.

## <a name="data-protection-paths"></a>Percursos de proteção de dados
![caminhos de proteção de dados](./media/plan-hybrid-identity-design-considerations/data-protection-paths.png)

**Percursos de proteção de dados**

No diagrama acima, o componente de identidade será o primeiro a ser verificado antes de os dados serem acedidos. No entanto, estes dados podem estar em estados diferentes durante o período de acesso. Cada número neste diagrama representa um caminho no qual os dados podem ser localizados em algum momento. Estes números são explicados abaixo:

1. Proteção de dados ao nível do dispositivo.
2. Proteção de dados durante o transporte.
3. Proteção de dados em repouso no local.
4. Proteção de dados enquanto está em repouso na nuvem.

É necessário que a solução de identidade híbrida seja capaz de alavancar tanto os recursos de gestão de identidade no local como os recursos de gestão da identidade na nuvem para identificar o utilizador antes de conceder acesso aos dados. Ao planear a sua solução de identidade híbrida, certifique-se de que as seguintes questões são respondidas de acordo com os requisitos da sua organização:

## <a name="data-protection-at-rest"></a>Proteção de dados em repouso
Independentemente do local onde os dados estão em repouso (dispositivo, nuvem ou no local), é importante realizar uma avaliação para entender as necessidades da organização a este respeito. Para esta área, certifique-se de que são feitas as seguintes perguntas:

* A sua empresa precisa de proteger os dados em repouso?
  * Se sim, a solução de identidade híbrida é capaz de se integrar com a sua atual infraestrutura no local?
  * Se sim, a solução de identidade híbrida é capaz de se integrar com as suas cargas de trabalho localizadas na nuvem?
* A gestão da identidade em nuvem é capaz de proteger as credenciais do utilizador e outros dados armazenados na nuvem?

## <a name="data-protection-in-transit"></a>Proteção de dados em trânsito
Os dados em trânsito entre o dispositivo e o datacenter ou entre o dispositivo e a nuvem devem ser protegidos. No entanto, estar em trânsito não significa necessariamente um processo de comunicação com um componente fora do seu serviço na nuvem; move-se internamente, também, como entre duas redes virtuais. Para esta área, certifique-se de que são feitas as seguintes perguntas:

* A sua empresa precisa de proteger os dados em trânsito?
  * Se sim, a solução de identidade híbrida é capaz de se integrar com controlos seguros como o SSL/TLS?
* A gestão da identidade em nuvem mantém o tráfego dentro e dentro do diretório (dentro e entre centros de dados) assinado?

## <a name="compliance"></a>Conformidade
Os regulamentos, leis e requisitos de conformidade regulamentar variarão de acordo com a indústria a que a sua empresa pertence. As empresas de indústrias elevadas regulamentadas devem abordar as preocupações relacionadas com a gestão da identidade relacionadas com questões de conformidade. Regulamentos como Sarbanes-Oxley (SOX), a Lei de Portabilidade e Responsabilização dos Seguros de Saúde (HIPAA), a Lei Gramm-Leach-Bliley (GLBA) e a Norma de Segurança de Dados da Indústria de Cartões de Pagamento (PCI DSS) são estritas no que diz respeito à identidade e ao acesso. A solução de identidade híbrida que a sua empresa irá adotar deve ter as capacidades fundamentais que irão satisfazer os requisitos de um ou mais destes regulamentos. Para esta área, certifique-se de que são feitas as seguintes perguntas:

* A solução de identidade híbrida está em conformidade com os requisitos regulamentares para o seu negócio?
* A solução de identidade híbrida construiu 
* em capacidades que permitirão à sua empresa cumprir os requisitos regulamentares? 

> [!NOTE]
> Certifique-se de que anota cada resposta e que compreende os fundamentos das mesmas. [Definir a Estratégia de Proteção de Dados](plan-hybrid-identity-design-considerations-data-protection-strategy.md) irá sobre as opções disponíveis e vantagens/desvantagens de cada opção.  Ao responder a essas perguntas, irá selecionar qual a opção que melhor se adequa às necessidades do seu negócio.
> 
> 

## <a name="next-steps"></a>Passos seguintes
 [Determinar requisitos de gestão de conteúdos](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>Consulte também
[Visão geral de considerações de design](plan-hybrid-identity-design-considerations-overview.md)

