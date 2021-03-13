---
title: Azure Ative Directory SMART em proxy FHIR
description: Este tutorial descreve como usar um proxy para permitir smart em aplicações FHIR com a API Azure para FHIR.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 04/02/2019
ms.openlocfilehash: 2e13a9fc32964781dda07e5534e5cab79868ddf0
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020731"
---
# <a name="tutorial-azure-active-directory-smart-on-fhir-proxy"></a>Tutorial: Azure Ative Directory SMART em proxy FHIR

[SMART on FHIR](https://docs.smarthealthit.org/) é um conjunto de especificações abertas para integrar aplicações de parceiros com servidores FHIR e sistemas de registos médicos eletrônicos que têm interfaces FHIR. Um dos principais propósitos das especificações é descrever como uma aplicação deve descobrir pontos finais de autenticação para um servidor FHIR e iniciar uma sequência de autenticação. 

A autenticação baseia-se no OAuth2. Mas como smart on FHIR usa convenções de nomeação de parâmetros que não são imediatamente compatíveis com O Diretório Ativo Azure (Azure AD), a AZure API para FHIR tem um Azure AD SMART incorporado no proxy FHIR que permite um subconjunto do SMART em sequências de lançamento de FHIR. Especificamente, o proxy permite a sequência de lançamento do [EHR.](https://hl7.org/fhir/smart-app-launch/#ehr-launch-sequence)

Este tutorial descreve como usar o proxy para permitir smart em aplicações FHIR com a API Azure para FHIR.

## <a name="prerequisites"></a>Pré-requisitos

- Um caso da AZure API para fHIR
- [.NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2)

## <a name="configure-azure-ad-registrations"></a>Configurar registos Ad Azure

Smart on FHIR requer que `Audience` tenha um URI identificador igual ao URI do serviço FHIR. A configuração padrão da API Azure para FHIR utiliza um `Audience` valor de `https://azurehealthcareapis.com` . No entanto, também pode definir um valor correspondente ao URL específico do seu serviço FHIR (por `https://MYFHIRAPI.azurehealthcareapis.com` exemplo). Isto é necessário ao trabalhar com o SMART no proxy FHIR.

Também vai precisar de um registo de inscrição de cliente. A maioria das aplicações SMART em FHIR são aplicações JavaScript de uma só página. Por isso, deve seguir as instruções para configurar uma [aplicação pública de cliente Azure AD](register-public-azure-ad-client-app.md).

Depois de completar estes passos, deve ter:

- Um servidor FHIR com público de rge definido para `https://MYFHIRAPI.azurehealthcareapis.com` , onde está o nome da sua `MYFHIRAPI` AZure API para a instância FHIR.
- Inscrição de um cliente público. Tome nota da identificação do pedido para este pedido de cliente.

## <a name="enable-the-smart-on-fhir-proxy"></a>Ativar o SMART no proxy FHIR

Ativar o SMART no proxy FHIR nas definições **de Autenticação** para a sua API Azure para a instância FHIR selecionando o SMART na caixa **de verificação de procuração FHIR:**

![Seleções para permitir o SMART em proxy FHIR](media/tutorial-smart-on-fhir/enable-smart-on-fhir-proxy.png)

## <a name="enable-cors"></a>Ativar o CORS

Como a maioria das aplicações SMART em FHIR são aplicações JavaScript de uma só página, você precisa permitir a [partilha de recursos de origem cruzada (CORS)](configure-cross-origin-resource-sharing.md) para a API Azure para FHIR:

![Seleções para permitir o CORS](media/tutorial-smart-on-fhir/enable-cors.png)

## <a name="configure-the-reply-url"></a>Configure o URL de resposta

O smart on FHIR proxy atua como um intermediário entre o SMART na app FHIR e a Azure AD. A resposta de autenticação (o código de autenticação) deve ir para o SMART em proxy FHIR em vez da própria app. Em seguida, o representante reencaminha a resposta para a app. 

Devido a este retransmissor em duas etapas do código de autenticação, é necessário definir o URL de resposta (callback) para a sua aplicação de cliente Azure AD para um URL que é uma combinação do URL de resposta para o SMART no proxy FHIR e o URL de resposta para o SMART na aplicação FHIR. O URL de resposta combinada assume este formulário:

```http
https://MYFHIRAPI.azurehealthcareapis.com/AadSmartOnFhirProxy/callback/aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA
```

Nessa resposta, `aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA` encontra-se uma versão codificada por URL, com base64, do URL de resposta para o SMART na aplicação FHIR. Para o smart no lançador de aplicações FHIR, quando a aplicação está a funcionar localmente, o URL de resposta é `https://localhost:5001/sampleapp/index.html` . 

Pode gerar o URL de resposta combinado utilizando um script como este:

```PowerShell
$replyUrl = "https://localhost:5001/sampleapp/index.html"
$fhirServerUrl = "https://MYFHIRAPI.azurewebsites.net"
$bytes = [System.Text.Encoding]::UTF8.GetBytes($ReplyUrl)
$encodedText = [Convert]::ToBase64String($bytes)
$encodedText = $encodedText.TrimEnd('=');
$encodedText = $encodedText.Replace('/','_');
$encodedText = $encodedText.Replace('+','-');

$newReplyUrl = $FhirServerUrl.TrimEnd('/') + "/AadSmartOnFhirProxy/callback/" + $encodedText
```

Adicione o URL de resposta à aplicação de cliente público que criou anteriormente para a Azure AD:

![URL de resposta configurado para o cliente público](media/tutorial-smart-on-fhir/configure-reply-url.png)

## <a name="get-a-test-patient"></a>Arranja um paciente de teste.

Para testar a API Azure para FHIR e o SMART no proxy FHIR, terá de ter pelo menos um paciente na base de dados. Se ainda não interagiu com a API e não tem dados na base de dados, siga o tutorial do [Carteiro FHIR API](access-fhir-postman-tutorial.md) para carregar um paciente. Tome nota da identificação de um paciente específico.

## <a name="download-the-smart-on-fhir-app-launcher"></a>Descarregue o smart no launcher da aplicação FHIR

O servidor FHIR de código aberto [para o repositório de Azure](https://github.com/Microsoft/fhir-server) inclui um smart simples no lançador de aplicações FHIR e uma amostra SMART na aplicação FHIR. Neste tutorial, utilize este smart no lançador FHIR localmente para testar a configuração.

Pode clonar o repositório GitHub e ir à aplicação utilizando estes comandos:

```PowerShell
git clone https://github.com/Microsoft/fhir-server
cd fhir-server/samples/apps/SmartLauncher
```

A aplicação necessita de algumas definições de configuração, que pode configurar em `appsettings.json` :

```json
{
    "FhirServerUrl": "https://MYFHIRAPI.azurehealthcareapis.com",
    "ClientId": "APP-ID",
    "DefaultSmartAppUrl": "/sampleapp/launch.html"
}
```

Recomendamos que utilize a `dotnet user-secrets` funcionalidade:

```PowerShell
dotnet user-secrets set FhirServerUrl https://MYFHIRAPI.azurehealthcareapis.com
dotnet user-secrets set ClientId <APP-ID>
```

Utilize este comando para executar a aplicação:

```PowerShell
dotnet run
```

## <a name="test-the-smart-on-fhir-proxy"></a>Teste o SMART no proxy FHIR

Depois de iniciar o smart no launcher da aplicação FHIR, pode apontar o seu navegador para `https://localhost:5001` , onde deve ver o seguinte ecrã:

![SMART no launcher de aplicações FHIR](media/tutorial-smart-on-fhir/smart-on-fhir-app-launcher.png)

Quando introduzir informações **sobre pacientes,** **encontros** ou **praticantes,** irá notar que o **contexto de Lançamento** está atualizado. Quando você está usando a API Azure para FHIR, o contexto de lançamento é simplesmente um documento JSON que contém informações sobre paciente, praticante e muito mais. Este contexto de lançamento é codificado e transmitido para a aplicação SMART na app FHIR como `launch` parâmetro de consulta. De acordo com o smart sobre a especificação FHIR, esta variável é opaca para a aplicação SMART na FHIR e transmitida ao fornecedor de identidade. 

O smart em proxy FHIR utiliza esta informação para povoar campos na resposta simbólica. A aplicação SMART on FHIR *pode* usar estes campos para controlar para que paciente solicita dados e como torna a aplicação no ecrã. O smart em proxy FHIR suporta os seguintes campos:

* `patient`
* `encounter`
* `practitioner`
* `need_patient_banner`
* `smart_style_url`

Estes campos destinam-se a fornecer orientação para a app, mas não transmitem nenhuma informação de segurança. Uma aplicação SMART on FHIR pode ignorá-las.

Note que o lançador de aplicações SMART no FHIR atualiza as informações **de URL** de lançamento na parte inferior da página. Selecione **Lançamento** para iniciar a aplicação da amostra, e você deve ver algo como esta amostra:

![SMART na aplicação FHIR](media/tutorial-smart-on-fhir/smart-on-fhir-app.png)

Inspecione a resposta simbólica para ver como os campos de contexto de lançamento são transmitidos à aplicação.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configuraste o Azure Ative Directory SMART no proxy FHIR. Para explorar a utilização de SMART em aplicações FHIR com a API Azure para FHIR e o FHIR Server for Azure de código aberto, vá ao repositório de amostras de servidores FHIR no GitHub:

>[!div class="nextstepaction"]
>[Amostras de servidores FHIR](https://github.com/Microsoft/fhir-server-samples)
