---
title: PERGUNTAS Frequentes sobre serviços FHIR em Azure - Azure API para FHIR
description: Obtenha respostas a perguntas frequentes sobre a API Azure para FHIR, como a localização de armazenamento de dados por trás das APIs FHIR e suporte de versão.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: cdec5ade4fc72fba6fcfba131b69ca9eb373874c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871779"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Perguntas frequentes sobre a AZure API para FHIR

## <a name="what-is-fhir"></a>O que é FHIR?
O Fast Healthcare Interoperability Resources (FHIR - Pronunciado "fogo") é uma norma de interoperabilidade destinada a permitir o intercâmbio de dados de cuidados de saúde entre diferentes sistemas de saúde. Esta norma foi desenvolvida pela organização HL7 e está a ser adotada por organizações de saúde em todo o mundo. A versão mais atual do FHIR disponível é R4 (Lançamento 4). A Azure API para FHIR suporta o R4 e também suporta a versão anterior STU3 (Standard for Trial Use 3). Para mais informações sobre o FHIR, visite [HL7.org.](http://hl7.org/fhir/summary.html)

## <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Os dados por trás das APIs do FHIR são armazenados em Azure?

Sim, os dados são armazenados em bases de dados geridas em Azure. A Azure API para fHIR não fornece acesso direto à loja de dados subjacente.

## <a name="what-identity-provider-do-you-support"></a>Que fornecedor de identidade apoia?

Atualmente, apoiamos o Microsoft Azure Ative Directory como fornecedor de identidade.

## <a name="what-fhir-version-do-you-support"></a>Que versão FHIR apoia?

Apoiamos as versões 4.0.0 e 3.0.1 tanto na API Azure para FHIR (PaaS) como no FHIR Server for Azure (open source).

Para mais detalhes, consulte [funcionalidades suportadas](fhir-features-supported.md). Leia sobre o que mudou entre versões na história da [versão para HL7 FHIR](https://hl7.org/fhir/R4/history.html).

## <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Qual é a diferença entre o servidor de código aberto do Microsoft FHIR server para o Azure e o API Azure para fHIR?

O Azure API para FHIR é uma versão hospedada e gerida do Microsoft FHIR Server para Azure. No serviço gerido, a Microsoft fornece todas as manutenção e atualizações. 

Quando está a executar o FHIR Server for Azure, tem acesso direto aos serviços subjacentes. Mas também é responsável por manter e atualizar o servidor e todos os trabalhos de conformidade necessários se estiver a armazenar dados de PHI.

Do ponto de vista do desenvolvimento, todas as funcionalidades são implantadas no Microsoft FHIR Server de código aberto para o Azure primeiro. Uma vez validado em código aberto, será lançado à API PaaS Azure para a solução FHIR. O tempo entre o lançamento em open-source e PaaS depende da complexidade da funcionalidade e de outras prioridades do roteiro. 

## <a name="what-is-smart-on-fhir"></a>O que é SMART no FHIR?

SMART (Aplicações Médicas Substituíveis e Tecnologia Reutilizável) em FHIR é um conjunto de especificações abertas para integrar aplicações de parceiros com servidores FHIR e outros sistemas de TI de saúde, tais como Registos Eletrónicos de Saúde e Intercâmbios de Informações de Saúde. Ao criar uma aplicação SMART sobre FHIR, pode garantir que a sua aplicação pode ser acedida e alavancada por uma infinidade de diferentes sistemas.
Autenticação e AZure API para FHIR. Para saber mais sobre smart, visite [smart Health IT.](https://smarthealthit.org/)

## <a name="next-steps"></a>Próximos passos

Neste artigo, você leu algumas das perguntas frequentes sobre a Azure API para FHIR. Leia sobre as funcionalidades suportadas no FHIR Server for Azure:
 
>[!div class="nextstepaction"]
>[Funcionalidades de FHIR suportadas](fhir-features-supported.md)