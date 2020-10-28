---
title: Como certificar os dispositivos IoT Plug e Play Microsoft Docs
description: Como construtor de dispositivos, aprenda a fazer testes e envie um dispositivo para certificação
author: konichi3
ms.author: koichih
ms.date: 08/21/2020
ms.topic: how-to
ms.custom: mvc, devx-track-azurecli
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: b20dab7719e4c55c5868032db3178bb7baaddda0
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92748475"
---
# <a name="how-to-certify-iot-plug-and-play-devices"></a>Como certificar dispositivos IoT Plug and Play

O programa de certificação de dispositivos IoT Plug and Play inclui ferramentas para verificar se um dispositivo cumpre os requisitos de certificação de IoT Plug and Play. As ferramentas também ajudam as organizações a impulsionar a consciencialização sobre a disponibilidade dos seus dispositivos IoT Plug e Play. Estes dispositivos certificados são adaptados para soluções IoT e ajudam a reduzir o tempo ao mercado.

Este artigo mostra-lhe como:

- Instale a extensão da linha de comando Azure IoT para o Azure CLI
- Executar os testes IoT Plug and Play para validar a aplicação do seu dispositivo durante a fase de desenvolvimento  
- Utilize o portal Azure Certified Device para validar a aplicação do dispositivo

## <a name="prepare-your-device"></a>Preparar o dispositivo

O código de aplicação que funciona no seu IoT Plug and Play deve:

- Ligue-se ao Azure IoT Hub utilizando o [Serviço de Provisionamento de Dispositivos (DPS)](../iot-dps/about-iot-dps.md).
- Siga as [convenções IoT Plug a Play](concepts-convention.md) para implementar a telemetria, propriedades e comandos.

A aplicação é um software que está instalado separadamente do sistema operativo ou que está agregado com o sistema operativo numa imagem de firmware que é piscada para o dispositivo.

O processo de certificação verifica que o dispositivo é compatível com IoT Plug e Play, validando que a implementação do dispositivo corresponde à telemetria, propriedades e comandos definidos no modelo do dispositivo [digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) e que o modelo está disponível no [Repositório de Modelo Público Azure IoT.](concepts-model-repository.md)

Para satisfazer os requisitos de certificação, o seu dispositivo deve:

- Liga-se ao Azure IoT Hub utilizando o [DPS](../iot-dps/about-iot-dps.md).
- Implementar telemetria, propriedades ou comandos seguindo a convenção IoT Plug and Play.
- Descreva as interações do dispositivo com um modelo [DTDL v2.](https://aka.ms/dtdl)
- Publique o modelo, e todas as interfaces necessárias, no [Repositório de Modelo Público Azure IoT](https://devicemodels.azureiotsolutions.com/)
- Envie o ID do modelo durante o [registo DPS](concepts-developer-guide-device-csharp.md#dps-payload) na carga útil de provisionamento DPS.
- Anuncie o ID do modelo durante a [ligação MQTT](concepts-developer-guide-device-csharp.md#model-id-announcement).

## <a name="test-with-the-azure-iot-extension-cli"></a>Testar com a CLI da Extensão do Azure IoT

A [extensão Azure IoT CLI](/cli/azure/ext/azure-iot/iot/product?preserve-view=true&view=azure-cli-latest) permite validar que a implementação do dispositivo corresponde ao modelo antes de submeter o dispositivo para certificação através do portal Azure Certified Device.

Os seguintes passos mostram-lhe como preparar e executar os testes de certificação utilizando o CLI:

### <a name="install-the-azure-iot-extension-for-the-azure-cli"></a>Instale a extensão Azure IoT para o Azure CLI

Consulte as instruções de instalação para configurar o [Azure CLI](/cli/azure/?preserve-view=true&view=azure-cli-latest) no seu ambiente.

Para instalar a Extensão Azure IoT, executar o seguinte comando:

```azurecli
az extension add --name azure-iot
```

Para saber mais, consulte [Azure CLI para Azure IoT](/cli/azure/azure-cli-reference-for-iot?preserve-view=true&view=azure-cli-latest).

### <a name="create-a-new-product-test"></a>Criar um novo teste de produto

O seguinte comando cria um teste utilizando DPS com um método de atestado de chave simétrica:

- Cria um novo produto para testar e gera uma configuração de teste. A saída apresenta a informação DPS que o dispositivo deve utilizar para o provisionamento: a chave primária, o ID do dispositivo e o ID Scope.
- Especifica a pasta com os ficheiros DTDL que descrevem o seu modelo.

```azurecli
az iot product test create --badge-type Pnp --at SymmetricKey --device-type FinishedProduct --models {local folder name}
```

> [!NOTE]
> Tem de [iniciar súmis na](/cli/azure/authenticate-azure-cli?preserve-view=true&view=azure-cli-latest) sua subscrição quando utilizar o CLI.

A saída JSON do comando contém `primaryKey` o , e para usar quando liga o seu `registrationId` `scopeID` dispositivo.

Resultado esperado:

```json
"deviceType": "FinishedProduct",
"id": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
"provisioningConfiguration": {
  "symmetricKeyEnrollmentInformation": {
    "primaryKey":"Ci/Ghpqp0n4m8FD5PTicr6dEikIfM3AtVWzAhObU7yc=",
    "registrationId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
    "scopeId": "0ne000FFA42"
  }
}
```

### <a name="connect-your-device"></a>Ligar o dispositivo

Utilize a saída de informação DPS pelo comando anterior para ligar o seu dispositivo à instância IoT Hub de teste.

### <a name="manage-and-configure-the-product-tests"></a>Gerir e configurar os testes do produto

Quando o dispositivo estiver ligado e pronto a interagir com o hub IoT, gere um ficheiro de configuração de teste do produto. Para criar o ficheiro:

- Utilize o teste `id` a partir da saída do comando anterior.
- Use o `--wait` parâmetro para obter a caixa de teste.

```azurecli
az iot product test task create --type GenerateTestCases --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

Resultado esperado:

```json
{
  "deviceTestId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
  "error": null,
  "id": "526da38e-91fc-4e20-a761-4f04b392c42b",
  "resultLink": "/deviceTests/d45d53d9-656d-4be7-9bbf-140bc87e98dc/TestCases",
  "status": "Completed",
  "type": "GenerateTestCases"
}
```

Pode utilizar o `az iot product test case update` comando para modificar o ficheiro de configuração de teste.

### <a name="run-the-tests"></a>Executar os testes

Depois de gerar a configuração do teste, o próximo passo é executar os testes. Utilize o mesmo `devicetestId` dos comandos anteriores como parâmetro para executar os testes. Verifique os resultados dos testes para se certificar de que todos os testes têm um estado `Passed` .

```azurecli
az iot product test task create --type QueueTestRun --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

Saída de teste de exemplo

```json
      "validationTasks": [
        {
          "componentName": "Default component",
          "endTime": "2020-08-25T05:18:49.5224772+00:00",
          "interfaceId": "dtmi:com:example:TemperatureController;1",
          "logs": [
            {
              "message": "Waiting for telemetry from the device",
              "time": "2020-08-25T05:18:37.3862586+00:00"
            },
            {
              "message": "Validating PnP properties",
              "time": "2020-08-25T05:18:37.3875168+00:00"
            },
            {
              "message": "Validating PnP commands",
              "time": "2020-08-25T05:18:37.3894343+00:00"
            },
            {
              "message": "{\"propertyName\":\"serialNumber\",\"expectedSchemaType\":null,\"actualSchemaType\":null,\"message\":\"Property is successfully validated\",\"passed\":true,\"time\":\"2020-08-25T05:18:37.4205985+00:00\"}",
              "time": "2020-08-25T05:18:37.4205985+00:00"
            },
            {
              "message": "PnP interface properties validation passed",
              "time": "2020-08-25T05:18:37.4206964+00:00"
            },
```

## <a name="test-using-the-azure-certified-device-portal"></a>Testar com o portal do Microsoft Azure Certified for IoT

Os passos seguintes mostram-lhe como utilizar o [portal Azure Certified Device](https://aka.ms/acdp) para embarcar, registar detalhes do produto, enviar um guia de arranque e executar os testes de certificação.

### <a name="onboarding"></a>Inclusão

Para utilizar o portal de [certificação,](https://aka.ms/acdp)deve utilizar um Diretório Azure Ative do seu trabalho ou inquilino escolar.

Para publicar os modelos no Repositório de Modelos Públicos Azure IoT, a sua conta deve ser membro da [Microsoft Partner Network](https://partner.microsoft.com). O sistema verifica se existe o ID da Rede de Parceiros da Microsoft e a conta é totalmente examinada antes de ser publicada no catálogo do dispositivo.

### <a name="company-profile"></a>Perfil da empresa

Pode gerir o perfil da empresa a partir do menu de navegação à esquerda. O perfil da empresa inclui o URL da empresa, endereço de e-mail e logotipo da empresa. O contrato de programa deve ser aceite nesta página antes de executar quaisquer operações de certificação.

As informações sobre o perfil da empresa são utilizadas na descrição do dispositivo apresentada no catálogo do dispositivo.

### <a name="create-new-project"></a>Criar novo projeto

Para certificar um dispositivo, primeiro tem de criar um novo projeto.

Navegue até ao portal de [certificação.](https://aka.ms/acdp) Na página **Projetos,** selecione *+ Criar novo projeto.* Em seguida, insira um nome para o projeto, o nome do dispositivo, e selecione uma classe de dispositivo.

A informação do produto que fornece durante o processo de certificação enquadra-se em quatro categorias:

- Informação do dispositivo. Recolhe informações sobre o dispositivo, como o seu nome, descrição, certificações e sistema operativo.
- O guia **get start.** Tem de submeter o guia como documento PDF a ser aprovado pelo administrador do sistema antes de publicar o dispositivo.
- Detalhes de marketing. Forneça informações de marketing prontas para o cliente para o seu dispositivo. A informação de marketing inclui como descrição, uma foto e distribuidores.
- Certificações adicionais da indústria. Esta secção opcional permite-lhe fornecer informações adicionais sobre quaisquer outras certificações que o dispositivo tenha.

### <a name="connect-and-test"></a>Ligar e testar

A etapa de ligação e teste verifica se o seu dispositivo cumpre os requisitos de certificação IoT Plug and Play.

Há três passos a serem concluídos:

1. Conecte-se e descubra interfaces. O dispositivo deve ligar-se ao serviço de certificação Azure IoT através de DPS. Escolha o método de autenticação (certificado X.509, chaves simétricas ou módulo de plataforma fidedigna) para utilizar e atualizar a aplicação do dispositivo com as informações DPS.
1. Rever interfaces. Reveja a interface e certifique-se de que cada um tem entradas de carga útil que fazem sentido para testes.
1. O teste. O sistema testa cada modelo de dispositivo para verificar se a telemetria, propriedades e comandos descritos no modelo seguem as convenções IoT Plug and Play. Quando o teste estiver concluído, selecione a ligação **de registos** de visualização para ver a telemetria do dispositivo e os dados brutos enviados para as propriedades gémeas do dispositivo IoT Hub.

### <a name="submit-and-publish"></a>Submeter e publicar

A fase final exigida é submeter o projeto para revisão. Este passo notifica um membro da equipa de Dispositivos Certificados Azure para rever o seu projeto para a completude, incluindo os detalhes do dispositivo e marketing, e o guia get-start. Um membro da equipa pode contactá-lo no endereço de e-mail da empresa previamente fornecido com perguntas ou editar pedidos antes da aprovação.

Se o seu dispositivo necessitar de mais validação manual como parte da certificação, receberá um aviso neste momento.

Quando um dispositivo é certificado, pode optar por publicar os seus dados do produto no Catálogo de Dispositivos Certificados Azure utilizando a **Funcionalidade De Publicação para catalogar** funcionalidades na página de resumo do produto.

## <a name="next-steps"></a>Passos seguintes

Agora que a submissão do dispositivo está concluída, pode contactar a equipa de certificação do dispositivo [iotcert@microsoft.com](mailto:iotcert@microsoft.com) para continuar até aos próximos passos, que incluem validação de membros da Microsoft Partner Network e uma revisão dos guias iniciados. Quando todos os requisitos estiverem satisfeitos, pode optar por incluir o seu dispositivo no [catálogo de dispositivos Certified for Azure IoT](https://aka.ms/devicecatalog).
