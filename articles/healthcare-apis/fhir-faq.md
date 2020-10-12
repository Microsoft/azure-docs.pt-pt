---
title: PERGUNTAS Frequentes sobre serviços FHIR em Azure - Azure API para FHIR
description: Obtenha respostas a perguntas frequentes sobre a API Azure para FHIR, como a localização de armazenamento de dados por trás das APIs FHIR e suporte de versão.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2020
ms.author: matjazl
ms.openlocfilehash: 35b59fb0583911b5b9faee96276d1bb09a8d6679
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91269714"
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

### <a name="can-i-create-a-custom-fhir-resource"></a>Posso criar um recurso FHIR personalizado?

Não permitimos recursos FHIR personalizados. Se precisar de um recurso FHIR personalizado, pode construir um recurso personalizado em cima do [recurso Basic](http://www.hl7.org/fhir/basic.html) com extensões. 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>As extensões são [apoiadas](https://www.hl7.org/fhir/extensibility.html) na Azure API para a FHIR?

Permitimos que carregue quaisquer dados FHIR JSON válidos no servidor. Se quiser armazenar a definição de estrutura que define a extensão, poderá guardá-la como recurso de definição de estrutura. Atualmente, não é possível pesquisar extensões.

### <a name="what-is-the-limit-on-_count"></a>Qual é o limite para _count?

O limite atual na contagem é de 100.

### <a name="are-there-any-limitations-on-the-group-export-functionality"></a>Existem limitações na funcionalidade de Exportação do Grupo?

Para a Exportação do Grupo exportamos apenas as referências incluídas do grupo, e não todas as características do recurso de [grupo.](https://www.hl7.org/fhir/group.html)

### <a name="can-i-post-a-bundle-to-the-azure-api-for-fhir"></a>Posso colocar um pacote na Azure API para fHIR?

Atualmente, apoiamos a publicação [de pacotes de lotes,](https://www.hl7.org/fhir/valueset-bundle-type.html) mas não suportamos pacotes de transações postais na AZure API para fHIR. Pode utilizar o servidor FHIR de código aberto apoiado pela SQL para registar pacotes de transações.

### <a name="how-can-i-get-all-resources-for-a-single-patient-in-the-azure-api-for-fhir"></a>Como posso obter todos os recursos para um único paciente na API Azure para fHIR?

Apoiamos a procura de [compartimentos](https://www.hl7.org/fhir/compartmentdefinition.html) na Azure API para fHIR. Isto permite-lhe obter todos os recursos relacionados com um paciente específico. Note que neste momento o compartimento inclui todos os recursos relacionados com o paciente, mas não com o próprio paciente, pelo que também terá de procurar para obter o paciente se precisar do recurso do paciente nos seus resultados.

Alguns exemplos disso estão abaixo:

* GET Paciente/<id>/*
* GET Paciente/ <id> /Observação
* PACIENTE GET/ <id> /Observation?code=8302-2

### <a name="where-can-i-see-some-examples-of-using-the-azure-api-for-fhir-within-a-workflow"></a>Onde posso ver alguns exemplos de utilização da API Azure para FHIR dentro de um fluxo de trabalho?

Dispomos de um conjunto de arquiteturas de referência disponíveis na [página GitHub de Arquitetura de Saúde.](https://github.com/microsoft/health-architectures)

## <a name="azure-iot-connector-for-fhir-preview"></a>Conector Azure IoT para FHIR (pré-visualização)

### <a name="what-is-iomt"></a>O que é IoMT?
IoMT significa Internet of Medical Things e é uma categoria de dispositivos IoT que capturam e trocam dados de saúde e bem-estar com outros sistemas de TI de saúde através de uma rede. Alguns exemplos de dispositivos IoMT incluem fitness e wearables clínicos, sensores de monitorização, rastreadores de atividade, quiosques de cuidados, ou mesmo uma pílula inteligente.

### <a name="how-many-azure-iot-connector-for-fhir-preview-do-i-need"></a>Quantos Azure IoT Connector para FHIR (pré-visualização) preciso?
Um único Conector Azure IoT para FHIR* pode ser usado para ingerir dados de um grande número de diferentes tipos de dispositivos. Pode ainda decidir utilizar diferentes conectores pelas seguintes razões:
- **Escala**: Para visualização pública, o Conector Azure IoT para a capacidade de recursos FHIR é fixo e espera-se que forneça uma produção de cerca de 200 mensagens por segundo. Pode adicionar mais Conector Azure IoT para FHIR, se for necessário uma produção mais elevada.
- **Tipo de dispositivo**: Pode configurar um Conector Azure IoT separado para FHIR para cada tipo de dispositivos IoMT que tenha por razões de gestão do dispositivo.

### <a name="is-there-a-limit-on-number-of-azure-iot-connector-for-fhir-preview-during-public-preview"></a>Existe um limite para o número de Conector Azure IoT para FHIR (pré-visualização) durante a pré-visualização pública?
Sim, pode criar apenas dois Azure IoT Connector para FHIR por subscrição enquanto a funcionalidade está em pré-visualização pública. Este limite existe para evitar despesas inesperadas, uma vez que a funcionalidade está disponível gratuitamente durante a pré-visualização. A pedido, este limite pode ser elevado até um máximo de cinco conector Azure IoT para fHIR.

### <a name="what-azure-regions-azure-iot-connector-for-fhir-preview-feature-is-available-during-public-preview"></a>Que característica Azure Regiões Azure IoT Connector para FHIR (pré-visualização) está disponível durante a pré-visualização pública?
O Conector Azure IoT para FHIR está disponível em todas as regiões de Azure onde a API para FHIR está disponível.

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>Posso configurar a capacidade de escala para O Conector Azure IoT para FHIR (pré-visualização)?
Uma vez que o Conector Azure IoT para FHIR é gratuito durante a pré-visualização pública, a sua capacidade de escala é fixa e limitada. Espera-se que o Conector Azure IoT para a configuração FHIR disponível na pré-visualização pública forneça uma produção de cerca de 200 mensagens por segundo. Alguma forma de configuração da capacidade de recursos será disponibilizada em Disponibilidade Geral (GA).

### <a name="what-fhir-version-does-azure-iot-connector-for-fhir-preview-support"></a>Que versão FHIR faz Azure IoT Connector para suporte FHIR (pré-visualização) ?
Azure IoT Connector para FHIR suporta atualmente apenas a versão FHIR R4. Assim, esta funcionalidade é visível apenas nos casos R4 da Azure API para FHIR e a Microsoft não planeia suportar a versão STU3 neste momento.

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Por que não posso instalar o Conector Azure IoT para FHIR (pré-visualização) quando o Link Privado está ativado na AZure API para FHIR?
O Conector Azure IoT para FHIR não suporta a capacidade de Ligação Privada neste momento. Assim, se tiver o Link Privado ativado na Azure API para FHIR, não pode instalar o Conector Azure IoT para FHIR e vice-versa. Espera-se que esta limitação desapareça quando o Conector Azure IoT para FHIR estiver disponível para disponibilidade geral (GA).

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-azure-iot-connector-for-fhir-preview-feature-of-azure-api-for-fhir-service"></a>Qual é a diferença entre o conector IoMT FHIR de código aberto para OZURE e o Conector Azure IoT para fHIR (pré-visualização) da Azure API para o serviço FHIR?
Azure IoT Connector for FHIR é uma versão hospedada e gerida do conector IoMT FHIR de código aberto para a Azure. No serviço gerido, a Microsoft fornece todas as manutenção e atualizações.

Quando estiver a executar o Conector FHIR IoMT para o Azure, tem acesso direto aos recursos subjacentes. Mas também é responsável por manter e atualizar o servidor e todos os trabalhos de conformidade necessários se estiver a armazenar dados de PHI.

Do ponto de vista do desenvolvimento, todas as funcionalidades são implantadas no conector IoMT FHIR de código aberto para o Azure primeiro. Uma vez validado em código aberto, será lançado para o Conector PaaS Azure IoT para a funcionalidade FHIR da Azure API para o serviço FHIR. O tempo entre o lançamento em open-source e PaaS depende da complexidade da funcionalidade e de outras prioridades do roteiro.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você leu algumas das perguntas frequentes sobre a Azure API para FHIR. Leia sobre as funcionalidades suportadas no FHIR Server for Azure:
 
>[!div class="nextstepaction"]
>[Funcionalidades de FHIR suportadas](fhir-features-supported.md)

*No portal Azure, o Conector Azure IoT para FHIR é referido como Conector IoT (pré-visualização).

FHIR é a marca registada do HL7 e é utilizada com a permissão do HL7.