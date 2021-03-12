---
title: Conversão de dados para Azure API para FHIR
description: Utilize o ponto final de $convert dados e personalize modelos de conversor para converter dados em Azure API para FHIR.
services: healthcare-apis
author: ranvijaykumar
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 01/19/2021
ms.author: ranku
ms.openlocfilehash: 7518f5e2984029c087eec1e6697f3237410bda4b
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020423"
---
# <a name="how-to-convert-data-to-fhir-preview"></a>Como converter dados em FHIR (Pré-visualização)

> [!IMPORTANT]
> Esta capacidade está disponível em pré-visualização pública, é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O ponto final personalizado de dados $convert na API Azure para FHIR destina-se à conversão de dados de diferentes formatos para FHIR. Utiliza o motor do modelo Liquid e os modelos do projeto [FHIR Converter](https://github.com/microsoft/FHIR-Converter) como modelos padrão. Pode personalizar estes modelos de conversão conforme necessário. Atualmente suporta a conversão HL7v2 para FHIR.

## <a name="use-the-convert-data-endpoint"></a>Utilize o ponto final de $convert dados

`https://<<FHIR service base URL>>/$convert-data`

$convert-data requer um recurso [parâmetro](http://hl7.org/fhir/parameters.html) no organismo de pedido, conforme descrito abaixo:

**Recurso de parâmetro:**

| Nome do Parâmetro      | Description | Valores aceites |
| ----------- | ----------- | ----------- |
| inputData      | Dados a converter. | Um valor válido do tipo de dados de corda JSON|
| inputDataType   | Tipo de dados de entrada. | ```HL7v2``` |
| modelo Conferência de Referência | Referência a uma coleção de modelos. Pode ser uma referência aos **modelos Predefinidos,** ou a uma imagem de modelo personalizada que é registada na AZure API para FHIR. Veja abaixo para saber sobre personalizar os modelos, hospedar os que estão no ACR, e registar-se na API Azure para FHIR.  | ```microsofthealth/fhirconverter:default```, \<RegistryServer\>/\<imageName\>@\<imageDigest\> |
| rootTemplate | O modelo de raiz a utilizar durante a transformação dos dados. | ```ADT_A01```, ```OML_O21```, ```ORU_R01```, ```VXU_V04``` |  

> [!WARNING]
> Os modelos predefinidos ajudam-no a começar rapidamente. No entanto, estes podem ser atualizados quando atualizarmos a AZure API para fHIR. Para ter um comportamento consistente de conversão de dados em diferentes versões da AZure API para FHIR, deve hospedar a sua própria cópia de modelos num Registo de Contentores Azure, registe-os na API Azure para FHIR e utilizar nas suas chamadas API como descrito mais tarde.

**Pedido de amostra:**

```json
{
    "resourceType": "Parameters",
    "parameter": [
        {
            "name": "inputData",
            "valueString": "MSH|^~\\&|SIMHOSP|SFAC|RAPP|RFAC|20200508131015||ADT^A01|517|T|2.3|||AL||44|ASCII\nEVN|A01|20200508131015|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|\nPID|1|3735064194^^^SIMULATOR MRN^MRN|3735064194^^^SIMULATOR MRN^MRN~2021051528^^^NHSNBR^NHSNMBR||Kinmonth^Joanna^Chelsea^^Ms^^CURRENT||19870624000000|F|||89 Transaction House^Handmaiden Street^Wembley^^FV75 4GJ^GBR^HOME||020 3614 5541^HOME|||||||||C^White - Other^^^||||||||\nPD1|||FAMILY PRACTICE^^12345|\nPV1|1|I|OtherWard^MainRoom^Bed 183^Simulated Hospital^^BED^Main Building^4|28b|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|||CAR|||||||||16094728916771313876^^^^visitid||||||||||||||||||||||ARRIVED|||20200508131015||"
        },
        {
            "name": "inputDataType",
            "valueString": "Hl7v2"
        },
        {
            "name": "templateCollectionReference",
            "valueString": "microsofthealth/fhirconverter:default"
        },
        {
            "name": "rootTemplate",
            "valueString": "ADT_A01"
        }
    ]
}
```

**Resposta da amostra:**

```json
{
  "resourceType": "Bundle",
  "type": "transaction",
  "entry": [
    {
      "fullUrl": "urn:uuid:9d697ec3-48c3-3e17-db6a-29a1765e22c6",
      "resource": {
        "resourceType": "Patient",
        "id": "9d697ec3-48c3-3e17-db6a-29a1765e22c6",
        ...
        ...
      "request": {
        "method": "PUT",
        "url": "Location/50becdb5-ff56-56c6-40a1-6d554dca80f0"
      }
    }
  ]
}
```

## <a name="customize-templates"></a>Personalizar modelos

Pode utilizar a [extensão do Conversor FHIR](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter) para Código do Estúdio Visual para personalizar os modelos de acordo com as suas necessidades. A extensão proporciona uma experiência de edição interativa e facilita o download de modelos e dados de amostras publicados pela Microsoft. Consulte a documentação na extensão para mais detalhes.

## <a name="host-and-use-templates"></a>Modelos de anfitrião e uso

Recomenda-se vivamente que apresente a sua própria cópia de modelos em ACR. Existem quatro passos envolvidos no alojamento da sua própria cópia de modelos e na utilização dos da operação $convert dados:

1. Empurre os modelos para o registo do seu contentor Azure.
1. Ativar identidade gerida na sua AZure API para a instância FHIR.
1. Fornecer acesso da ACR à AZure API para identidade gerida FHIR.
1. Registe os servidores ACR na Azure API para FHIR.

### <a name="push-templates-to-azure-container-registry"></a>Modelos de empurrar para o registo do contentor de Azure

Depois de criar uma instância ACR, pode utilizar o _comando FHIR Converter: Push Templates_ na extensão do [Conversor FHIR](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter) para empurrar os modelos personalizados para o ACR. Em alternativa, pode utilizar a [ferramenta CLI de Gestão](https://github.com/microsoft/FHIR-Converter/blob/main/docs/TemplateManagementCLI.md) de Modelos para este fim.

### <a name="enable-managed-identity-on-azure-api-for-fhir"></a>Ativar identidade gerida na AZure API para FHIR

Navegue pelo seu exemplo de Azure API para o serviço FHIR no portal Azure e selecione a lâmina **identidade.**
Altere o estado para **On** para permitir a identidade gerida na Azure API para FHIR.

![Ativar identidade gerida](media/convert-data/fhir-mi-enabled.png)

### <a name="provide-access-of-the-acr-to-azure-api-for-fhir"></a>Fornecer acesso da ACR à AZure API para fHIR

Navegue para a lâmina do Controlo de Acesso (IAM) na sua instância ACR e selecione _Add Role Assignments_.

![Atribuição de funções ACR](media/convert-data/fhir-acr-role-assignment.png)

Conceda o papel da AcrPull à sua Azure API para a instância de serviço da FHIR.

![Adicionar papel](media/convert-data/fhir-acr-role-add.png)

### <a name="register-the-acr-servers-in-azure-api-for-fhir"></a>Registar os servidores ACR na Azure API para FHIR

Pode registar até vinte servidores ACR na API Azure para FHIR.

Instale o healthcareapis CLI da Azure PowerShell se necessário:

```powershell
az extension add -n healthcareapis
```

Registe os servidores acr da Azure API para fHIR seguindo os exemplos abaixo:

#### <a name="register-a-single-acr-server"></a>Registar um único servidor ACR

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

#### <a name="register-multiple-acr-servers"></a>Registar vários servidores ACR

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io fhiracr2020.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

### <a name="verify"></a>Verificação

Faça uma chamada para a API de dados de $convert especificando a referência do seu modelo no parâmetro de referência de modeloCollectionReference.

`<RegistryServer>/<imageName>@<imageDigest>`

## <a name="known-issues-and-workarounds"></a>Questões e soluções alternativas conhecidas

- Alguns ficheiros de modelo predefinidos contêm UTF-8 BOM. Como resultado, os valores de ID gerados conterão um caráter BOM. Isto pode criar um problema com o servidor FHIR. A solução é puxar os modelos da Microsoft utilizando a extensão do código VS e empurrá-los para o seu próprio ACR depois de remover os caracteres BOM de _ID/_Procedure.liquid,_ _ID/_Provenance.liquid_, e _ID/_Imunização.liquid_.

