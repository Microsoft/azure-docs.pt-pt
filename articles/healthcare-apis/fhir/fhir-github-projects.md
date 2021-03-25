---
title: Relacionados GitHub Projetos para API Azure para FHIR
description: Lista todos os repositórios Open Source (GitHub) para Azure API para FHIR.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/01/2021
ms.author: ginle
ms.openlocfilehash: 9977765183bd567377592631d65f3e548bef4b34
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105097643"
---
# <a name="related-github-projects"></a>Projetos Relacionados GitHub

Temos muitos projetos de código aberto no GitHub que lhe fornecem o código fonte e instruções para implementar serviços para várias utilizações. Você é sempre bem-vindo a visitar os nossos repositórios GitHub para aprender e experimentar com as nossas características e produtos. 

## <a name="fhir-server"></a>Servidor FHIR
* [microsoft/fhir-server](https://github.com/microsoft/fhir-server/): Open-source FHIR Server, que é a base para AZure API para FHIR
* Para ver as ver as últimas versões, consulte as [Notas de Lançamento](https://github.com/microsoft/fhir-server/releases)
* [microsoft/fhir-server-samples](https://github.com/microsoft/fhir-server-samples): um ambiente de amostra

## <a name="data-conversion--anonymization"></a>Conversão de dados & Anonimização

#### <a name="fhir-converter"></a>Conversor FHIR
* [microsoft/FHIR-Converter:](https://github.com/microsoft/FHIR-Converter)um utilitário de conversão para traduzir formatos de dados legados em FHIR
* Integrado com a API Azure para fHIR, bem como o servidor FHIR para a Azure sob a forma de operação de $convert dados
* Melhorias contínuas no OSS e integração contínua nos servidores FHIR
 
#### <a name="fhir-converter---vs-code-extension"></a>Conversor FHIR - Extensão do código VS
* [microsoft/FHIR-Tools-for-Anonymization](https://github.com/microsoft/FHIR-Tools-for-Anonymization): um conjunto de ferramentas para ajudar com a anonimização de dados (em formato FHIR)
* Integrado com a API Azure para fHIR, bem como o servidor FHIR para o Azure sob a forma de "exportação desfoque identificada"

#### <a name="fhir-tools-for-anonymization"></a>Ferramentas FHIR para anonimização
* [microsoft/vscode-azurehealthcareapapis-tools](https://github.com/microsoft/vscode-azurehealthcareapis-tools): uma extensão do Código VS que contém uma coleção de ferramentas para trabalhar com APIs de cuidados de saúde Azure
* Lançado para Visual Studio Marketplace
* Utilizado para a autoria de modelos líquidos a utilizar no Conversor FHIR

## <a name="iot-connector"></a>Conector IoT

#### <a name="integration-with-iot-hub-and-iot-central"></a>Integração com ioT Hub e IoT Central
* [microsoft/iomt-fhir](https://github.com/microsoft/iomt-fhir): integração com ioT Hub ou IoT Central a FHIR com normalização de dados e conversão FHIR dos dados normalizados
* Normalização: a informação dos dados do dispositivo é extraída num formato comum para posterior tratamento
* Conversão fHIR: os dados normalizados e agrupados são mapeados para FHIR. As observações são criadas ou atualizadas de acordo com modelos configurados e ligadas ao dispositivo e ao paciente.
* [Ferramentas para ajudar a construir o mapa de conversação](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper): visualizar a configuração de mapeamento para normalizar os dados de entrada do dispositivo e transformá-lo nos recursos FHIR. Os desenvolvedores podem usar esta ferramenta para editar e testar os mapeamentos, mapeamento de dispositivos e mapeamento FHIR, e exportá-los para o upload para o Conector IoT no portal Azure.

#### <a name="healthkit-and-fhir-integration"></a>Integração HealthKit e FHIR
* [microsoft/healthkit-on-fhir](https://github.com/microsoft/healthkit-on-fhir): uma biblioteca Swift que automatiza a exportação de Dados Apple HealthKit para um Servidor FHIR

 