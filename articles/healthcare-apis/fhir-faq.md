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
ms.openlocfilehash: af891935fd474e6f1f83ff1c2ce56ef71cd065c6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536730"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Perguntas frequentes sobre a AZure API para FHIR

## <a name="azure-api-for-fhir"></a>API do Azure para FHIR

### <a name="what-is-fhir"></a>O que é FHIR?
O Fast Healthcare Interoperability Resources (FHIR - Pronunciado "fogo") é uma norma de interoperabilidade destinada a permitir o intercâmbio de dados de cuidados de saúde entre diferentes sistemas de saúde. Esta norma foi desenvolvida pela organização HL7 e está a ser adotada por organizações de saúde em todo o mundo. A versão mais atual do FHIR disponível é R4 (Lançamento 4). A Azure API para FHIR suporta o R4 e também suporta a versão anterior STU3 (Standard for Trial Use 3). Para mais informações sobre o FHIR, visite [HL7.org.](http://hl7.org/fhir/summary.html)

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Os dados por trás das APIs do FHIR são armazenados em Azure?

Sim, os dados são armazenados em bases de dados geridas em Azure. A Azure API para fHIR não fornece acesso direto à loja de dados subjacente.

### <a name="what-identity-provider-do-you-support"></a>Que fornecedor de identidade apoia?

Atualmente, apoiamos o Microsoft Azure Ative Directory como fornecedor de identidade.

### <a name="what-fhir-version-do-you-support"></a>Que versão FHIR apoia?

Apoiamos as versões 4.0.0 e 3.0.1 tanto na API Azure para FHIR (PaaS) como no FHIR Server for Azure (open source).

Para mais detalhes, consulte [funcionalidades suportadas](fhir-features-supported.md). Leia sobre o que mudou entre versões na história da [versão para HL7 FHIR](https://hl7.org/fhir/R4/history.html).

### <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Qual é a diferença entre o servidor de código aberto do Microsoft FHIR server para o Azure e o API Azure para fHIR?

O Azure API para FHIR é uma versão hospedada e gerida do Microsoft FHIR Server para Azure. No serviço gerido, a Microsoft fornece todas as manutenção e atualizações. 

Quando está a executar o FHIR Server for Azure, tem acesso direto aos serviços subjacentes. Mas também é responsável por manter e atualizar o servidor e todos os trabalhos de conformidade necessários se estiver a armazenar dados de PHI.

Do ponto de vista do desenvolvimento, todas as funcionalidades são implantadas no Microsoft FHIR Server de código aberto para o Azure primeiro. Uma vez validado em código aberto, será lançado à API PaaS Azure para a solução FHIR. O tempo entre o lançamento em open-source e PaaS depende da complexidade da funcionalidade e de outras prioridades do roteiro. 

### <a name="what-is-smart-on-fhir"></a>O que é SMART no FHIR?

SMART (Aplicações Médicas Substituíveis e Tecnologia Reutilizável) em FHIR é um conjunto de especificações abertas para integrar aplicações de parceiros com servidores FHIR e outros sistemas de TI de saúde, tais como Registos Eletrónicos de Saúde e Intercâmbios de Informações de Saúde. Ao criar uma aplicação SMART sobre FHIR, pode garantir que a sua aplicação pode ser acedida e alavancada por uma infinidade de diferentes sistemas.
Autenticação e AZure API para FHIR. Para saber mais sobre smart, visite [smart Health IT.](https://smarthealthit.org/)


## <a name="iot-connector-preview"></a>Conector IoT (pré-visualização)

### <a name="what-is-iomt"></a>O que é IoMT?
IoMT significa Internet of Medical Things e é uma categoria de dispositivos IoT que capturam e trocam dados de saúde e bem-estar com outros sistemas de TI de saúde através de uma rede. Alguns exemplos de dispositivos IoMT incluem fitness e wearables clínicos, sensores de monitorização, rastreadores de atividade, quiosques de cuidados, ou mesmo uma pílula inteligente.

### <a name="how-many-iot-connectors-do-i-need"></a>De quantos conectores IoT preciso?
Um único Conector IoT pode ser usado para ingerir dados de um grande número de diferentes tipos de dispositivos. Pode ainda decidir utilizar diferentes conectores pelas seguintes razões:
- **Escala**: Para visualização pública, a capacidade de recursos do Conector IoT é fixada e espera-se que forneça uma produção de cerca de 200 mensagens por segundo. Pode adicionar mais Conectores IoT, se for necessário um rendimento mais elevado.
- **Tipo de dispositivo**: Pode configurar um conector IoT separado para cada tipo de dispositivos IoMT que tenha por razões de gestão do dispositivo.

### <a name="is-there-a-limit-on-number-of-iot-connectors-during-public-preview"></a>Existe um limite para o número de Conectores IoT durante a pré-visualização pública?
Sim, pode criar apenas dois Conectores IoT por subscrição enquanto a funcionalidade estiver em pré-visualização pública. Este limite existe para evitar despesas inesperadas, uma vez que a funcionalidade está disponível gratuitamente durante a pré-visualização. A pedido, este limite pode ser elevado até um máximo de cinco Conectores IoT.

### <a name="what-azure-regions-iot-connector-feature-is-available-during-public-preview"></a>Que funcionalidade de Conector IoT regiões Azure está disponível durante a pré-visualização pública?
O IoT Connector está disponível em todas as regiões do Azure onde a API para FHIR está disponível.

### <a name="can-i-configure-scaling-capacity-for-iot-connector"></a>Posso configurar a capacidade de escala para o Conector IoT?
Uma vez que o IoT Connector é gratuito durante a pré-visualização pública, a sua capacidade de escala é fixa e limitada. Espera-se que a configuração do Conector IoT disponível na pré-visualização pública forneça uma produção de cerca de 200 mensagens por segundo. Alguma forma de configuração da capacidade de recursos será disponibilizada em Disponibilidade Geral (GA).

### <a name="what-fhir-version-does-iot-connector-support"></a>Que versão FHIR suporta o IoT Connector?
O IoT Connector suporta atualmente apenas a versão FHIR R4. Assim, esta funcionalidade é visível apenas nos casos R4 da Azure API para FHIR e a Microsoft não planeia suportar a versão STU3 neste momento.

### <a name="why-cant-i-install-iot-connector-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Por que não posso instalar o Conector IoT quando o Link Privado está ativado na Azure API para fHIR?
O IoT Connector não suporta a capacidade de Ligação Privada neste momento. Assim, se tiver o Link Privado ativado na Azure API para FHIR, não pode instalar o IoT Connector e vice-versa. Espera-se que esta limitação desapareça quando o Conector IoT estiver disponível para disponibilidade geral (GA).

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-iot-connector-feature-of-azure-api-for-fhir-service"></a>Qual é a diferença entre o conector IoMT FHIR de código aberto para a Azure e o conector IoT da Azure API para o serviço FHIR?
O IoT Connector é uma versão hospedada e gerida do conector IoMT FHIR de código aberto para a Azure. No serviço gerido, a Microsoft fornece todas as manutenção e atualizações.

Quando estiver a executar o Conector FHIR IoMT para o Azure, tem acesso direto aos recursos subjacentes. Mas também é responsável por manter e atualizar o servidor e todos os trabalhos de conformidade necessários se estiver a armazenar dados de PHI.

Do ponto de vista do desenvolvimento, todas as funcionalidades são implantadas no conector IoMT FHIR de código aberto para o Azure primeiro. Uma vez validado em código aberto, será lançado para a funcionalidade De Conector PaaS IoT da Azure API para o serviço FHIR. O tempo entre o lançamento em open-source e PaaS depende da complexidade da funcionalidade e de outras prioridades do roteiro.

## <a name="next-steps"></a>Próximos passos

Neste artigo, você leu algumas das perguntas frequentes sobre a Azure API para FHIR. Leia sobre as funcionalidades suportadas no FHIR Server for Azure:
 
>[!div class="nextstepaction"]
>[Funcionalidades de FHIR suportadas](fhir-features-supported.md)