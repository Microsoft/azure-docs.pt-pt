---
title: O que é o Azure API for FHIR? - Azure API para FHIR
description: A Azure API para FHIR permite uma rápida troca de dados através de FHIR APIs. Ingerir, gerir e persistir Proteed Health Information PHI com um serviço de nuvem gerido.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 11/13/2020
ms.author: matjazl
ms.openlocfilehash: 3b51a07281c7d6d9307cc802dfc9f149442e88b7
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177492"
---
# <a name="what-is-azure-api-for-fhirreg"></a>O que é Azure API para &reg; FHIR?

A Azure API para FHIR permite uma rápida troca de dados através de APIs de Interoperabilidade de Cuidados rápidos (FHIR®), apoiados por uma oferta gerida de Plataforma-as-A (PaaS) na nuvem. Torna mais fácil para qualquer pessoa que trabalhe com dados de saúde ingerir, gerir e persistir Informação de Saúde Protegida [PHI](https://www.hhs.gov/answers/hipaa/what-is-phi/index.html) na nuvem: 

- Serviço FHIR gerido, a provisionado na nuvem em minutos 
- Nível empresarial, FHIR® ponto final em Azure para acesso a dados e armazenamento em formato FHIR®
- Alto desempenho, baixa latência
- Gestão segura da Informação de Saúde Protegida (PHI) num ambiente de nuvem compatível
- SMART em FHIR para implementações móveis e web
- Controle os seus próprios dados em escala com o controlo de acesso baseado em funções (RBAC)
- Rastreio de registo de auditoria para acesso, criação, modificação e leituras dentro de cada loja de dados

A Azure API para FHIR permite-lhe criar e implementar um serviço FHIR em poucos minutos para alavancar a escala elástica da nuvem.  Paga-se apenas pela produção e armazenamento de que precisa. Os serviços Azure que alimentam a Azure API para fHIR são projetados para um desempenho rápido independentemente do tamanho dos conjuntos de dados que você está gerindo.

A API FHIR e a loja de dados em conformidade permitem-lhe conectar e interagir de forma segura com qualquer sistema que utilize APIs FHIR.  A Microsoft assume os requisitos de operações, manutenção, atualizações e conformidade na oferta paaS, para que possa libertar os seus próprios recursos operacionais e de desenvolvimento. 

O seguinte vídeo apresenta uma visão geral da Azure API para fHIR:

>[!VIDEO https://www.youtube.com/embed/5vS7Iq9vpXE]

## <a name="leveraging-the-power-of-your-data-with-fhir"></a>Aproveitando o poder dos seus dados com fHIR

A indústria dos cuidados de saúde está a transformar rapidamente os dados de saúde para o padrão emergente de [FHIR &reg; ](https://hl7.org/fhir) (Fast Healthcare Interoperability Resources). O FHIR permite um modelo de dados robusto e extensível com semântica e troca de dados padrão que permite que todos os sistemas que utilizam o FHIR trabalhem em conjunto.  Transformar os seus dados em FHIR permite-lhe ligar rapidamente as fontes de dados existentes, como os sistemas eletrónicos de registo de saúde ou bases de dados de pesquisa. O FHIR também permite o rápido intercâmbio de dados em implementações modernas de desenvolvimento móvel e web. Mais importante ainda, o FHIR pode simplificar a ingestão de dados e acelerar o desenvolvimento com ferramentas de análise e machine learning.  

### <a name="securely-manage-health-data-in-the-cloud"></a>Gerir de forma segura os dados de saúde na nuvem

A Azure API para FHIR permite a troca de dados através de APIs consistentes, RESTful, FHIR com base na especificação HL7 FHIR. Apoiado por uma oferta gerida de PaaS em Azure, também fornece um ambiente escalável e seguro para a gestão e armazenamento de dados de Informação de Saúde Protegida (PHI) no formato nativo FHIR.  

### <a name="free-up-your-resources-to-innovate"></a>Liberte os seus recursos para inovar

Você poderia investir recursos construindo e executando seu próprio serviço FHIR, mas com a API Azure para FHIR, a Microsoft assume a carga de trabalho de operações, manutenção, atualizações e requisitos de conformidade, permitindo-lhe libertar os seus próprios recursos operacionais e de desenvolvimento.

### <a name="enable-interoperability-with-fhir"></a>Permitir a interoperabilidade com fHIR

A utilização da API Azure para FHIR permite-lhe conectar-se com qualquer sistema que aproveite as APIs FHIR para ler, escrever, pesquisar e outras funções.  Pode ser usado como uma ferramenta poderosa para consolidar, normalizar e aplicar machine learning com dados clínicos de registos de saúde eletrónicos, dashboards de clínicos e pacientes, programas de monitorização remota, ou com bases de dados fora do seu sistema que têm APIs FHIR.

### <a name="control-data-access-at-scale"></a>Controlo do Acesso de Dados à Escala

Controla os seus dados. O controlo de acesso baseado em funções (RBAC) permite-lhe gerir a forma como os seus dados são armazenados e acedidos.  Proporcionando maior segurança e reduzindo a carga de trabalho administrativa, você determina quem tem acesso aos conjuntos de dados que cria, com base nas definições de funções que cria para o seu ambiente.  

### <a name="audit-logs-and-tracking"></a>Registos de auditoria e rastreio 

Acompanhe rapidamente para onde vão os seus dados com registos de auditoria incorporados. Acompanhe o acesso, criação, modificação e leituras dentro de cada loja de dados.

### <a name="secure-your-data"></a>Proteger os dados

Proteja a sua PHI com informações de segurança incomparáveis.  Os seus dados estão isolados numa base de dados única por instância da API e protegidos com falhas multi-regiões. A Azure API para FHIR implementa uma defesa em camadas, uma defesa aprofundada e uma proteção avançada de ameaças para os seus dados.  

## <a name="applications-for-a-fhir-service"></a>Candidaturas para um Serviço FHIR

Os servidores FHIR são ferramentas-chave para a interoperabilidade dos dados de saúde.  A Azure API para FHIR é projetada como uma API e serviço que você pode criar, implementar e começar a usar rapidamente.  À medida que a norma FHIR se expande nos cuidados de saúde, os casos de utilização continuarão a crescer, mas algumas aplicações iniciais de clientes em que a API AZure para FHIR é útil são abaixo: 

- **Desenvolvimento de Startup/IoT e App:**  Os clientes que desenvolvem uma aplicação centrada no paciente ou fornecedor (móvel ou web) podem alavancar a AZure API para o FHIR como um serviço de backend totalmente gerido. A Azure API para fHIR fornece um recurso valioso na medida em que os clientes podem gerir dados e trocar dados num ambiente de nuvem segura projetado para dados de saúde, alavancar smart nas diretrizes de implementação do FHIR e permitir que a sua tecnologia seja utilizada por todos os sistemas de fornecedores (por exemplo, a maioria dos EHRs permitiu que os FHIR lessem APIs).   
- **Ecossistemas de saúde:**  Embora os EHRs existam como a principal "fonte da verdade" em muitos contextos clínicos, não é incomum que os fornecedores tenham várias bases de dados que não estejam ligadas entre si ou armazenem dados em diferentes formatos.  A utilização da API Azure para fHIR como um serviço que se encontra em cima desses sistemas permite-lhe normalizar dados no formato FHIR.  Isto ajuda a permitir o intercâmbio de dados em vários sistemas com um formato de dados consistente. 

- **Investigação:** Os investigadores de saúde acharão útil a norma FHIR em geral e a API Azure para fHIR, uma vez que normaliza os dados em torno de um modelo comum de dados FHIR e reduz a carga de trabalho para a aprendizagem automática e partilha de dados.
A troca de dados através da AZure API para fHIR fornece registos de auditoria e controlos de acesso que ajudam a controlar o fluxo de dados e que têm acesso a que tipos de dados. 

## <a name="fhir-from-microsoft"></a>FHIR da Microsoft

As capacidades FHIR da Microsoft estão disponíveis em duas configurações:

* Azure API para FHIR – Uma oferta PaaS em Azure, facilmente ateada no portal Azure e gerida pela Microsoft.
* FHIR Server for Azure – um projeto de código aberto que pode ser implementado na sua subscrição Azure, disponível no GitHub em https://github.com/Microsoft/fhir-server .

Para a utilização de casos que exijam a extensão ou personalização do servidor FHIR ou que exijam acesso aos serviços subjacentes , como a base de dados, sem passar pelas APIs FHIR, os desenvolvedores devem escolher o Servidor FHIR de código aberto para o Azure.   Para a implementação de um serviço de API FHIR API pronto para a produção, pronto para a produção, onde os dados persistidos só devem ser acedidos através da API FHIR, os desenvolvedores devem escolher a API Azure para FHIR

## <a name="azure-iot-connector-for-fhir-preview"></a>Conector Azure IoT para FHIR (pré-visualização)

O Azure IoT Connector for Fast Healthcare Interoperability Resources (FHIR&#174;)* é uma característica opcional da API Azure para fHIR que fornece a capacidade de ingerir dados a partir de dispositivos Internet of Medical Things (IoMT). Internet of Medical Things é uma categoria de dispositivos IoT que capturam e trocam dados de saúde & bem-estar com outros sistemas de TI de saúde através da rede. Alguns exemplos de dispositivos IoMT incluem fitness e wearables clínicos, sensores de monitorização, rastreadores de atividade, quiosques de cuidados, ou mesmo uma pílula inteligente. O conector Azure IoT para fHIR permite-lhe configurar rapidamente um serviço para ingerir dados IoMT na Azure API para FHIR de forma escalável, segura e conforme.

O Conector Azure IoT para FHIR pode aceitar quaisquer mensagens baseadas em JSON enviadas por um dispositivo IoMT. Estes dados são primeiro transformados em recursos de [observação](https://www.hl7.org/fhir/observation.html) adequados baseados em FHIR e, em seguida, persistiram em Azure API para FHIR. A lógica de transformação de dados é definida através de um par de modelos de mapeamento que configura com base no seu esquema de mensagem e requisitos de FHIR. Os dados do dispositivo podem ser empurrados diretamente para o Conector Azure IoT para FHIR ou utilizados sem problemas em conjunto com outras soluções Azure IoT[(Azure IoT Hub](../iot-hub/index.yml) e [Azure IoT Central).](../iot-central/index.yml) O Conector Azure IoT para FHIR fornece um gasoduto de dados seguro, permitindo que as soluções Azure IoT gerem o fornecimento e manutenção dos dispositivos físicos.

### <a name="applications-of-azure-iot-connector-for-fhir-preview"></a>Aplicações do Conector Azure IoT para FHIR (pré-visualização)

A utilização de dispositivos IoMT está a expandir-se rapidamente nos cuidados de saúde e o Conector Azure IoT para FHIR foi concebido para colmatar a lacuna de trazer dados de vários dispositivos com segurança e conformidade na AZure API para fHIR. A introdução de dados IoMT num servidor FHIR permite insights holísticos de dados e fluxos de trabalho clínicos inovadores. Alguns cenários comuns para o Conector Azure IoT para fHIR são:
- **Monitorização/Telesaúde do Paciente Remoto:** A monitorização remota do paciente proporciona a capacidade de recolher dados de saúde do paciente fora das definições tradicionais de cuidados de saúde. As instituições de saúde podem usar o Conector Azure IoT para o FHIR para trazer dados de saúde gerados por dispositivos remotos para a Azure API para fHIR. Estes dados poderiam ser usados para acompanhar de perto o estado de saúde dos pacientes, monitorizar a adesão dos pacientes ao plano de tratamento e prestar cuidados personalizados.
- **Investigação e Ciências da Vida:** Os ensaios clínicos estão a adotar rapidamente dispositivos IoMT como sensores biológicos, wearables, aplicações móveis para capturar dados de ensaios. Estes ensaios podem aproveitar o Conector Azure IoT para o FHIR transmitir dados do dispositivo à Azure API para fHIR de forma segura, eficiente e eficaz. Uma vez na Azure API para fHIR, os dados do ensaio poderiam ser usados para executar análises em tempo real dos dados do ensaio.
- **Análise Avançada:** Os dispositivos IoMT podem fornecer grande volume e variedade de dados a alta velocidade, o que os torna um ótimo ajuste para servir dados de treino e teste para os seus modelos de aprendizagem automática. O Conector Azure IoT para FHIR é inerentemente construído para trabalhar com uma ampla gama de frequência de dados, esquema de dados flexível e escala de nuvem com baixa latência. Estes atributos fazem do Azure IoT Connector para fHIR uma excelente escolha para capturar dados do dispositivo para as suas necessidades avançadas de análise.
- **Hospitais/Clínicas Inteligentes:** Hoje, hospitais e clínicas inteligentes estão a criar uma infraestrutura de ativos digitais interligados. O Conector Azure IoT para FHIR pode ser utilizado para capturar e integrar dados destes componentes conectados. Os conhecimentos acccípíveis deste conjunto de dados permitem um melhor atendimento do paciente e eficiência operacional.

## <a name="next-steps"></a>Passos Seguintes

Para começar a trabalhar com a AZure API para fHIR, siga o quickstart de 5 minutos para implantar a API Azure para FHIR.

>[!div class="nextstepaction"]
>[Implementar o Azure API for FHIR](fhir-paas-portal-quickstart.md)

Para experimentar o Conector Azure IoT para a função FHIR, consulte o quickstart para implementar o Conector Azure IoT para FHIR utilizando o portal Azure.

>[!div class="nextstepaction"]
>[Implementar conector Azure IoT para FHIR](iot-fhir-portal-quickstart.md)

*No portal Azure, o Conector Azure IoT para FHIR é referido como Conector IoT (pré-visualização). FHIR é uma marca registada do HL7 e é usada com a permissão do HL7. 
