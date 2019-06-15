---
title: Design de identidade híbrida - requisitos de proteção de dados do Azure | Documentos da Microsoft
description: Quando planear a sua solução de identidade híbrida, identificar os requisitos de proteção de dados para o seu negócio e quais opções estão disponíveis para melhor satisfazer estes requisitos.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64918784"
---
# <a name="plan-for-enhancing-data-security-through-a-strong-identity-solution"></a>Plano para melhorar a segurança de dados através de uma solução de identidade forte
A primeira etapa da proteção de dados é identificar quem pode aceder aos dados. Além disso, tem de ter uma solução de identidade que pode ser integrado com o sistema para oferecer capacidades de autenticação e autorização. Autenticação e autorização, freqüentemente são confundidos uns com os outros e respetivas funções mal compreendidos. Na realidade, elas são diferentes, conforme mostrado na figura abaixo:

![ciclo de vida do dispositivo móvel](./media/plan-hybrid-identity-design-considerations/mobile-devicemgt-lifecycle.png)

**Fases do ciclo de vida de gestão de dispositivos móveis**

Quando planear a sua solução de identidade híbrida, deve compreender os requisitos de proteção de dados para o seu negócio e quais opções estão disponíveis para a melhor cumprir estes requisitos.

> [!NOTE]
> Depois de concluir o planeamento de segurança de dados, reveja [determinar os requisitos de autenticação multifator](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md) garantir que as suas seleções sobre os requisitos de autenticação multifator não foram afetados pelas decisões que feitas nesta secção.
> 
> 

## <a name="determine-data-protection-requirements"></a>Determinar os requisitos de proteção de dados
Na era da mobilidade, a maioria das empresas tem um objetivo comum: permitir que os utilizadores sejam produtivos em dispositivos móveis, enquanto estão no local ou remotamente a partir de qualquer lugar para aumentar a produtividade. As empresas que têm esses requisitos também vão estar preocupadas com o número de ameaças que deve ser atenuado para proteger os dados da empresa e manter a privacidade do utilizador. Cada empresa pode ter requisitos diferentes nesse aspecto; regras de conformidade diferentes que irão variar, de acordo com o setor está atuando a empresa irão levar a decisões de conceção diferentes. 

No entanto, existem alguns aspetos de segurança que devem ser explorados e validados, independentemente da indústria.

## <a name="data-protection-paths"></a>Caminhos de proteção de dados
![Caminhos de proteção de dados](./media/plan-hybrid-identity-design-considerations/data-protection-paths.png)

**Caminhos de proteção de dados**

No diagrama acima, o componente de identidade que vai ser primeiro ser validado antes dos dados são acedidos. No entanto, estes dados podem ser nos Estados diferentes durante o tempo que ela foi acessada. Cada número neste diagrama representa um caminho em que dados podem ser localizados em algum momento no tempo. Esses números são explicados abaixo:

1. Proteção de dados ao nível do dispositivo.
2. Proteção de dados em trânsito.
3. Proteção de dados e ao rest no local.
4. Proteção de dados Inativos na cloud.

É necessário que a solução de identidade híbrida é capaz de aproveitar no local e recursos de gerenciamento de identidade para identificar o utilizador antes de ele concede acesso aos dados na cloud. Quando planear a sua solução de identidade híbrida, certifique-se de que as seguintes perguntas serão respondidas, de acordo com os requisitos da sua organização:

## <a name="data-protection-at-rest"></a>Proteção de dados em repouso
Independentemente de onde os dados estão inativos (dispositivo, na cloud ou no local), é importante efetuar uma avaliação para compreender as necessidades da organização nesse aspecto. Para esta área, certifique-se de que as seguintes perguntas são mais frequentes:

* A sua empresa precisa de proteger os dados Inativos?
  * Se Sim, é a solução de identidade híbrida consegue integrar com a sua infraestrutura no local atual?
  * Se Sim, é a solução de identidade híbrida consegue integrar com as suas cargas de trabalho localizadas na cloud?
* A gestão de identidade na cloud é capaz de proteger as credenciais do utilizador e outros dados armazenados na nuvem?

## <a name="data-protection-in-transit"></a>Proteção de dados em trânsito
Os dados em trânsito entre o dispositivo e o Centro de dados ou entre o dispositivo e a cloud têm de ser protegidos. No entanto, a ser em trânsito não significa necessariamente um processo de comunicação com um componente fora do seu serviço cloud; Move internamente, além disso, tal como entre duas redes virtuais. Para esta área, certifique-se de que as seguintes perguntas são mais frequentes:

* A sua empresa precisa de proteger dados em trânsito?
  * Se Sim, é a solução de identidade híbrida consegue integrar com controles seguros, tais como SSL/TLS?
* A gestão de identidade na cloud manter o tráfego de e para dentro do armazenamento de diretório (dentro e entre centros de dados) assinado?

## <a name="compliance"></a>Conformidade
Requisitos de conformidade a normas, leis e regulamentos varia de acordo com o setor que pertence a sua empresa. As empresas em indústrias reguladas elevadas devem resolver questões de gerenciamento de identidades relacionados com problemas de conformidade. Regulamentações como a Sarbanes-Oxley (SOX), o Health Insurance Portability e Accountability Act (HIPAA), o Act Gramm-Leach-Bliley (GLBA) e o pagamento Card Industry Data Security Standard (DSS da PCI) são rígidas sobre identidade e acesso. A solução de identidade híbrida que sua empresa irá adotar tem de ter os principais recursos de que irão satisfazer os requisitos de um ou mais desses regulamentos. Para esta área, certifique-se de que as seguintes perguntas são mais frequentes:

* A solução de identidade híbrida está em conformidade com as exigências normativas para a sua empresa?
* Faz que criou a solução de identidade híbrida 
* nos recursos que permitirão que a sua empresa estar em conformidade com requisitos regulamentares? 

> [!NOTE]
> Lembre-se de que anota cada resposta e compreender as razões implícitas para a resposta. [Definir a estratégia de proteção de dados](plan-hybrid-identity-design-considerations-data-protection-strategy.md) abordará as opções disponíveis e as vantagens/desvantagens de cada opção.  Ao responder a essas questões, vai selecionar qual opção melhor se adequa aos seu negócio precisa.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
 [Determinar os requisitos de gestão de conteúdos](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>Consultar Também
[Descrição geral das considerações de design](plan-hybrid-identity-design-considerations-overview.md)

