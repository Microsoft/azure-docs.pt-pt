---
title: O que é o Azure API for FHIR? - Azure API para FHIR
description: A Azure API para FHIR permite uma rápida troca de dados através de FHIR APIs. Ingerir, gerir e persistir Proteed Health Information PHI com um serviço de nuvem gerido.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: aca0d67326a5a0488d0108efa9acd0d01c7788cd
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "84820205"
---
# <a name="what-is-azure-api-for-fhirreg"></a>O que é Azure API para &reg; FHIR?

A Azure API para FHIR permite uma rápida troca de dados através de APIs de Interoperabilidade de Cuidados rápidos (FHIR®), apoiados por uma oferta gerida de Plataforma-as-A (PaaS) na nuvem. Torna mais fácil para qualquer pessoa que trabalhe com dados de saúde ingerir, gerir e persistir Informação de Saúde Protegida [PHI](https://www.hhs.gov/answers/hipaa/what-is-phi/index.html) na nuvem: 

- Serviço FHIR gerido, a provisionado na nuvem em minutos 
- Nível empresarial, FHIR® ponto final em Azure para acesso a dados e armazenamento em formato FHIR®
- Alto desempenho, baixa latência
- Gestão segura de dados de saúde protegidos (PHI) num ambiente de nuvem compatível
- SMART em FHIR para implementações móveis e web
- Controle os seus próprios dados em escala com o Controlo de Acesso Baseado em Fun (RBAC)
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

Controla os seus dados. O Controlo de Acesso Baseado em Função (RBAC) permite-lhe gerir a forma como os seus dados são armazenados e acedidos.  Proporcionando maior segurança e reduzindo a carga de trabalho administrativa, você determina quem tem acesso aos conjuntos de dados que cria, com base nas definições de funções que cria para o seu ambiente.  

### <a name="audit-logs-and-tracking"></a>Registos de auditoria e rastreio 

Acompanhe rapidamente para onde vão os seus dados com registos de auditoria incorporados. Acompanhe o acesso, criação, modificação e leituras dentro de cada loja de dados.

### <a name="secure-your-data"></a>Proteger os dados

Proteja a sua PHI com informações de segurança incomparáveis.  Os seus dados estão isolados numa base de dados única por instância da API e protegidos com falhas multi-regiões. A Azure API para FHIR implementa uma defesa em camadas, uma defesa aprofundada e uma proteção avançada de ameaças para os seus dados.  

## <a name="applications-for-a-fhir-service"></a>Candidaturas para um Serviço FHIR

Os servidores FHIR são ferramentas-chave para a interoperabilidade dos dados de saúde.  A Azure API para FHIR é projetada como uma API e serviço que você pode criar, implementar e começar a usar rapidamente.  À medida que a norma FHIR se expande nos cuidados de saúde, os casos de utilização continuarão a crescer, mas algumas aplicações iniciais de clientes em que a API AZure para FHIR é útil são abaixo: 

- **Desenvolvimento de Startup/IOT e Aplicações:**  Os clientes que desenvolvem uma aplicação centrada no paciente ou fornecedor (móvel ou web) podem alavancar a AZure API para o FHIR como um serviço de backend totalmente gerido. A Azure API para fHIR fornece um recurso valioso na medida em que os clientes podem gerir dados e trocar dados num ambiente de nuvem segura projetado para dados de saúde, alavancar smart nas diretrizes de implementação do FHIR e permitir que a sua tecnologia seja utilizada por todos os sistemas de fornecedores (por exemplo, a maioria dos EHRs permitiu que os FHIR lessem APIs).   
- **Ecossistemas de saúde:**  Embora os EHRs existam como a principal "fonte da verdade" em muitos contextos clínicos, não é incomum que os fornecedores tenham várias bases de dados que não estejam ligadas entre si ou armazenem dados em diferentes formatos.  A utilização da API Azure para fHIR como um serviço que se encontra em cima desses sistemas permite-lhe normalizar dados no formato FHIR.  Isto ajuda a permitir o intercâmbio de dados em vários sistemas com um formato de dados consistente. 

- **Investigação:** Os investigadores de saúde acharão útil a norma FHIR em geral e a API Azure para fHIR, uma vez que normaliza os dados em torno de um modelo comum de dados FHIR e reduz a carga de trabalho para a aprendizagem automática e partilha de dados.
A troca de dados através da AZure API para fHIR fornece registos de auditoria e controlos de acesso que ajudam a controlar o fluxo de dados e que têm acesso a que tipos de dados. 

## <a name="fhir-from-microsoft"></a>FHIR da Microsoft

As capacidades FHIR da Microsoft estão disponíveis em duas configurações:

* Azure API para FHIR – Uma oferta PaaS em Azure, facilmente ateada no portal Azure e gerida pela Microsoft.
* FHIR Server for Azure – um projeto de código aberto que pode ser implementado na sua subscrição Azure, disponível no GitHub em https://github.com/Microsoft/fhir-server .

Para a utilização de casos que exijam a extensão ou personalização do servidor FHIR ou que exijam acesso aos serviços subjacentes , como a base de dados, sem passar pelas APIs FHIR, os desenvolvedores devem escolher o Servidor FHIR de código aberto para o Azure.   Para a implementação de um serviço de API FHIR API pronto para a produção, pronto para a produção, onde os dados persistidos só devem ser acedidos através da API FHIR, os desenvolvedores devem escolher a API Azure para FHIR

## <a name="next-steps"></a>Passos Seguintes

Para começar a trabalhar com a AZure API para fHIR, siga o quickstart de 5 minutos para implantar a API Azure para FHIR.

>[!div class="nextstepaction"]
>[Implementar o Azure API for FHIR](fhir-paas-portal-quickstart.md)

FHIR é a marca registada do HL7 e é utilizada com a permissão do HL7.
