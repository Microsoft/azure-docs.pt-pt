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
ms.openlocfilehash: 31ae5b780bf451e29a97f04202f804db27fc387a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452941"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Perguntas frequentes sobre a AZure API para FHIR

## <a name="azure-api-for-fhir-the-basics"></a>AZure API para FHIR: O Básico

### <a name="what-is-fhir"></a>O que é FHIR?
O Fast Healthcare Interoperability Resources (FHIR - Pronunciado "fogo") é uma norma de interoperabilidade destinada a permitir o intercâmbio de dados de cuidados de saúde entre diferentes sistemas de saúde. Esta norma foi desenvolvida pela organização HL7 e está a ser adotada por organizações de saúde em todo o mundo. A versão mais atual do FHIR disponível é R4 (Lançamento 4). A Azure API para FHIR suporta o R4 e também suporta a versão anterior STU3 (Standard for Trial Use 3). Para mais informações sobre o FHIR, visite [HL7.org.](http://hl7.org/fhir/summary.html)

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Os dados por trás das APIs do FHIR são armazenados em Azure?

Sim, os dados são armazenados em bases de dados geridas em Azure. A Azure API para fHIR não fornece acesso direto à loja de dados subjacente.

### <a name="what-identity-provider-do-you-support"></a>Que fornecedor de identidade apoia?

Atualmente, apoiamos o Microsoft Azure Ative Directory como fornecedor de identidade.

### <a name="what-is-the-recovery-point-objective-rpo-for-the-azure-api-for-fhir"></a>Qual é o Objetivo do Ponto de Recuperação (RPO) para a API Azure para o FHIR?
A Azure API para FHIR é apoiada pela Cosmos DB como nossa provedora de persistência. Por isso, o RPO para o serviço é igual a [Cosmos DB (região única)](../cosmos-db/consistency-levels.md) e é < 240 minutos.

### <a name="what-fhir-version-do-you-support"></a>Que versão FHIR apoia?

Apoiamos as versões 4.0.0 e 3.0.1 tanto na API Azure para FHIR (PaaS) como no FHIR Server for Azure (open source).

Para mais detalhes, consulte [funcionalidades suportadas](fhir-features-supported.md). Leia sobre o que mudou entre as versões FHIR (ou seja, STU3 a R4) na história da [versão para HL7 FHIR](https://hl7.org/fhir/R4/history.html).

O Conector Azure IoT para FHIR (pré-visualização) suporta atualmente apenas a versão FHIR R4, e só é visível em instâncias R4 de API Azure para FHIR.

### <a name="whats-the-difference-between-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Qual é a diferença entre 'Microsoft FHIR Server for Azure' e o 'Azure API for FHIR'?

O Azure API para FHIR é uma versão hospedada e gerida do Microsoft FHIR Server para Azure. No serviço gerido, a Microsoft fornece todas as manutenção e atualizações. 

Quando executou o Servidor FHIR para o Azure, tem acesso direto aos serviços subjacentes, mas é responsável por manter e atualizar o servidor e todos os trabalhos de conformidade necessários se estiver a armazenar dados PHI.

Para um ponto de vista de desenvolvimento, todas as funcionalidades que não se aplicam apenas ao serviço gerido são implementadas pela primeira vez no Microsoft FHIR Server for Azure. Uma vez validado em código aberto, será lançado à API PaaS Azure para a solução FHIR. O tempo entre o lançamento em open-source e PaaS depende da complexidade da funcionalidade e de outras prioridades do roteiro. Este é o mesmo processo para todos os nossos serviços, como o Azure IoT Connector para FHIR (pré-visualização).

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>Onde posso ver o que está a libertar na API azul para a FHIR?

Para ver um pouco do que está a ser lançado na API Azure para FHIR, consulte a [versão](https://github.com/microsoft/fhir-server/releases) do Servidor FHIR de código aberto. A partir de novembro de 2020, temos itens marcados com Azure-API-for-FHIR se o item de código aberto será lançado para o serviço gerido. Estas funcionalidades estão normalmente disponíveis duas semanas após estarem na página de lançamento em código aberto. Também incluímos instruções sobre como testar a construção [aqui] https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) (se quiser testar no seu próprio ambiente. Estamos a avaliar a melhor forma de partilhar atualizações adicionais de serviço gerido.

### <a name="in-which-regions-is-azure-api-for-fhir-available"></a>Em que regiões está disponível a Azure API para a FHIR?

Atualmente, temos disponibilidade geral para o público e para o governo em [várias geo-regiões.](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir&regions=non-regional,us-east,us-east-2,us-central,us-north-central,us-south-central,us-west-central,us-west,us-west-2,canada-east,canada-central,usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia) Para obter informações sobre serviços de nuvem do governo na Microsoft, consulte [os serviços Azure da FedRAMP.](../azure-government/compliance/azure-services-in-fedramp-auditscope.md)

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>Onde posso ver o que está a libertar na API azul para a FHIR?

Para ver um pouco do que está a ser lançado na API Azure para FHIR, consulte a [versão](https://github.com/microsoft/fhir-server/releases) do Servidor FHIR de código aberto. Trabalhámos para marcar itens com a Azure-API-for-FHIR se eles vão lançar para o serviço gerido e estão geralmente disponíveis duas semanas após estarem na página de lançamento em código aberto. Também incluímos instruções sobre como testar a construção [aqui](https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) se quiser testar no seu próprio ambiente. Estamos a avaliar a melhor forma de partilhar atualizações adicionais de serviço gerido.

### <a name="what-is-smart-on-fhir"></a>O que é SMART no FHIR?

SMART (Aplicações Médicas Substituíveis e Tecnologia Reutilizável) em FHIR é um conjunto de especificações abertas para integrar aplicações de parceiros com servidores FHIR e outros sistemas de TI de saúde, tais como Registos Eletrónicos de Saúde e Intercâmbios de Informações de Saúde. Ao criar uma aplicação SMART sobre FHIR, pode garantir que a sua aplicação pode ser acedida e alavancada por uma infinidade de diferentes sistemas.
Autenticação e AZure API para FHIR. Para saber mais sobre smart, visite [smart Health IT.](https://smarthealthit.org/)

### <a name="where-can-i-find-what-version-of-fhir-is-running-on-my-database"></a>Onde posso encontrar que versão do FHIR está a ser na minha base de dados? 

Pode encontrar a versão exata do FHIR exposta na declaração de capacidade sob a propriedade "fhirVersion".

## <a name="fhir-implementations-and-specifications"></a>Implementações e Especificações fhir

### <a name="can-i-create-a-custom-fhir-resource"></a>Posso criar um recurso FHIR personalizado?

Não permitimos recursos FHIR personalizados. Se precisar de um recurso FHIR personalizado, pode construir um recurso personalizado em cima do [recurso Basic](http://www.hl7.org/fhir/basic.html) com extensões. 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>As extensões são [apoiadas](https://www.hl7.org/fhir/extensibility.html) na Azure API para a FHIR?

Permitimos que carregue quaisquer dados FHIR JSON válidos no servidor. Se quiser armazenar a definição de estrutura que define a extensão, poderá guardá-la como recurso de definição de estrutura. Atualmente, não é possível pesquisar extensões.

### <a name="what-is-the-limit-on-_count"></a>Qual é o limite para _count?

O limite atual para _count é de 100. Se colocar _count a mais de 100, receberá um aviso no pacote de que apenas 100 discos serão mostrados.

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

### <a name="what-is-the-default-sort-when-searching-for-resources-in-azure-api-for-fhir"></a>Qual é o tipo de incumprimento ao procurar recursos na Azure API para fHIR?

Apoiamos a triagem até à data atualizada pela última vez: _sort=_lastUpdated. Para obter mais informações sobre outros parâmetros de pesquisa suportados, consulte a [página de funcionalidades suportadas.](./fhir-features-supported.md#search)

### <a name="how-does-export-work"></a>Como funciona $export?

$export faz parte da especificação FHIR: https://hl7.org/fhir/uv/bulkdata/export/index.html . Se o serviço FHIR estiver configurado com uma identidade gerida e uma conta de armazenamento, e se a identidade gerida tiver acesso a essa conta de armazenamento - pode simplesmente ligar para $export na API do FHIR e todos os recursos FHIR serão exportados para a conta de armazenamento. Para mais informações, consulte o nosso [artigo sobre $export.](./export-data.md)

## <a name="using-azure-api-for-fhir"></a>Utilização da AZure API para FHIR

### <a name="how-do-i-enable-log-analytics-for-azure-api-for-fhir"></a>Como posso permitir a análise de registo para Azure API para FHIR?

Permitimos a verificação de registos de diagnóstico e permitimos a revisão de consultas de amostras para estes registos. Para obter mais informações sobre a ativação de registos de auditoria e consultas de amostras, consulte [esta secção](./enable-diagnostic-logging.md). Se pretender incluir informações adicionais nos registos, consulte [os cabeçalhos HTTP personalizados.](./use-custom-headers.md)

### <a name="where-can-i-see-some-examples-of-using-the-azure-api-for-fhir-within-a-workflow"></a>Onde posso ver alguns exemplos de utilização da API Azure para FHIR dentro de um fluxo de trabalho?

Dispomos de um conjunto de arquiteturas de referência disponíveis na [página GitHub de Arquitetura de Saúde.](https://github.com/microsoft/health-architectures)

### <a name="where-can-i-see-an-example-of-connecting-a-web-application-to-azure-api-for-fhir"></a>Onde posso ver um exemplo de ligação de uma aplicação web à Azure API para fHIR?

Temos uma [página gitHub de arquitetura de saúde](https://aka.ms/health-architectures) que contém aplicações e cenários de exemplo. Ilustra como ligar uma aplicação web à Azure API para fHIR.  

## <a name="azure-api-for-fhir-features-and-services"></a>Azure API para funcionalidades e serviços fHIR 

### <a name="is-there-a-way-to-encrypt-my-data-using-my-personal-key-not-a-default-key"></a>Existe uma maneira de encriptar os meus dados usando a minha chave pessoal e não uma chave padrão?

Sim, a Azure API para FHIR permite configurar chaves geridas pelo cliente, aproveitando o suporte da Cosmos DB. Para obter mais informações sobre encriptar os seus dados com uma chave pessoal, consulte [esta secção.](./customer-managed-key.md)

## <a name="azure-api-for-fhir-preview-features"></a>Azure API para FHIR: Funcionalidades de pré-visualização

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>Posso configurar a capacidade de escala para O Conector Azure IoT para FHIR (pré-visualização)?

Uma vez que o Conector Azure IoT para FHIR é gratuito durante a pré-visualização pública, a sua capacidade de escala é fixa e limitada. Espera-se que o Conector Azure IoT para a configuração FHIR disponível na pré-visualização pública forneça uma produção de cerca de 200 mensagens por segundo. Alguma forma de configuração da capacidade de recursos será disponibilizada em Disponibilidade Geral (GA).

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Por que não posso instalar o Conector Azure IoT para FHIR (pré-visualização) quando o Link Privado está ativado na AZure API para FHIR?

O Conector Azure IoT para FHIR não suporta a capacidade de Ligação Privada neste momento. Assim, se tiver o Link Privado ativado na Azure API para FHIR, não pode instalar o Conector Azure IoT para FHIR e vice-versa. Espera-se que esta limitação desapareça quando o Conector Azure IoT para FHIR estiver disponível para disponibilidade geral (GA).
