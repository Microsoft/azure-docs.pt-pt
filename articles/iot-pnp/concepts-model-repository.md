---
title: Compreenda os conceitos dos modelos do dispositivo repositório | Microsoft Docs
description: Como desenvolvedor de soluções ou profissional de TI, conheça os conceitos básicos do repositório de modelos de dispositivos.
author: rido-min
ms.author: rmpablos
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 33ff96b4e51dbf80bfdb924bc37786a344cdfdc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582651"
---
# <a name="device-models-repository"></a>Repositório de modelos de dispositivo

O repositório de modelos de dispositivos (DMR) permite aos construtores de dispositivos gerir e partilhar modelos de dispositivos IoT Plug e Play. Os modelos do dispositivo são documentos JSON LD definidos utilizando a [Linguagem de Modelação Digital Twins (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

O DMR define um padrão para armazenar interfaces DTDL numa estrutura de pasta baseada no identificador de modelos duplos do dispositivo (DTMI). Pode localizar uma interface no DMR convertendo o DTMI num caminho relativo. Por exemplo, o `dtmi:com:example:Thermostat;1` DTMI traduz-se para `/dtmi/com/example/thermostat-1.json` .

## <a name="public-device-models-repository"></a>Repositório de modelos de dispositivos públicos

A Microsoft acolhe um DMR público com estas características:

- Modelos com curadoria. A Microsoft analisa e aprova todas as interfaces disponíveis utilizando um fluxo de trabalho de validação de pedido de pedido de solicitação (PR) do GitHub.
- Imutabilidade.  Depois de publicado, uma interface não pode ser atualizada.
- Hiperescala. A Microsoft fornece a infraestrutura necessária para criar um ponto final seguro e escalável onde pode publicar e consumir modelos de dispositivos.

## <a name="custom-device-models-repository"></a>Repositório de modelos de dispositivo personalizado

Utilize o mesmo padrão DMR para criar um DMR personalizado em qualquer meio de armazenamento, como sistema de ficheiros local ou servidores web HTTP personalizados. Pode recuperar os modelos do dispositivo a partir do DMR personalizado da mesma forma que a partir do DMR público, alterando o URL base utilizado para aceder ao DMR.

> [!NOTE]
> A Microsoft fornece ferramentas para validar modelos de dispositivos no DMR público. Pode reutilizar estas ferramentas em repositórios personalizados.

## <a name="public-models"></a>Modelos públicos

Os modelos de dispositivos públicos armazenados nos modelos estão disponíveis para todos consumirem e integrarem nas suas aplicações. Os modelos de dispositivos públicos permitem um ecossistema aberto para os construtores de dispositivos e desenvolvedores de soluções partilharem e reutilizarem os seus modelos de dispositivos IoT Plug e Play.

Consulte a secção de [modelos](#publish-a-model) para obter instruções sobre como publicar um modelo no repositório dos modelos para torná-lo público.

Os utilizadores podem navegar, pesquisar e ver interfaces públicas a partir do [repositório](https://github.com/Azure/iot-plugandplay-models)oficial do GitHub.

Todas as interfaces nas `dtmi` pastas também estão disponíveis a partir do ponto final público [https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>Resolver modelos

Para aceder programáticamente a estas interfaces, pode utilizar o `ModelsRepositoryClient` pacote NuGet [Azure.IoT.ModelsRepository](https://www.nuget.org/packages/Azure.IoT.ModelsRepository). Este cliente é configurado por padrão para consultar o DMR público disponível em [devicemodels.azure.com](https://devicemodels.azure.com/) e pode ser configurado para qualquer repositório personalizado.

O cliente aceita um `DTMI` como entrada e devolve um dicionário com todas as interfaces necessárias:

```cs
using Azure.IoT.ModelsRepository;

var client = new ModelsRepositoryClient();
IDictionary<string, string> models = client.GetModels("dtmi:com:example:TemperatureController;1");
models.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

A saída esperada deve apresentar `DTMI` as três interfaces encontradas na cadeia de dependência:

```txt
dtmi:com:example:TemperatureController;1
dtmi:com:example:Thermostat;1
dtmi:azure:DeviceManagement:DeviceInformation;1
```

Pode `ModelsRepositoryClient` ser configurado para consultar um repositório de modelo personalizado - disponível através de http(s) - e especificar a resolução de dependência utilizando qualquer um dos disponíveis `ModelDependencyResolution` :

- Desativado. Devolve apenas a interface especificada, sem qualquer dependência.
- Ativado. Devolve todas as interfaces da cadeia de dependência
- TenteFromExpanded. Use o `.expanded.json` ficheiro para recuperar as dependências pré-calculadas 

> [!Tip] 
> Os repositórios personalizados podem não expor o `.expanded.json` ficheiro, quando não estiver disponível, o cliente recorrerá para processar cada dependência localmente.

O próximo código de amostra mostra como inicializar o `ModelsRepositoryClient` através de um URL de base de repositório personalizado, neste caso usando os `raw` URLs da API GitHub sem usar o `expanded` formulário - uma vez que não está disponível no `raw` ponto final. O `AzureEventSourceListener` é inicializado para inspecionar o pedido HTTP realizado pelo cliente:

```cs
using AzureEventSourceListener listener = AzureEventSourceListener.CreateConsoleLogger();

var client = new ModelsRepositoryClient(
    new Uri("https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main"),
    new ModelsRepositoryClientOptions(dependencyResolution: ModelDependencyResolution.Enabled));

IDictionary<string, string> models = client.GetModels("dtmi:com:example:TemperatureController;1");

models.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

Existem mais amostras disponíveis no código-fonte no repositório Azure SDK GitHub: [Azure.Iot.ModelsRepository/samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/modelsrepository/Azure.IoT.ModelsRepository/samples)

## <a name="publish-a-model"></a>Publicar um modelo

> [!Important]
> Deve ter uma conta GitHub para poder submeter modelos ao DMR público.

1. Fork o repositório público GitHub: [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models) .
1. Clone o repositório forcado. Opcionalmente criar um novo ramo para manter as suas alterações isoladas do `main` ramo.
1. Adicione as novas interfaces à `dtmi` pasta utilizando a convenção de pasta/nome de ficheiro. Para saber mais, consulte [Importar um Modelo para a `dtmi/` pasta](#import-a-model-to-the-dtmi-folder).
1. Valide os modelos localmente utilizando a `dmr-client` ferramenta. Para saber mais, consulte [modelos validados.](#validate-models)
1. Cometa as alterações localmente e empurre para o seu garfo.
1. Do seu garfo, crie um pedido de puxar que visa o `main` ramo. Consulte [a criação de um problema ou puxe](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request) os docs de pedido.
1. Reveja os [requisitos de pedido de puxar](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md).

O pedido de pull desencadeia um conjunto de ações do GitHub que validam as interfaces submetidas, e garante que o seu pedido de pull satisfaz todos os requisitos.

A Microsoft responderá a um pedido de retirada com todos os cheques em três dias úteis.

### <a name="dmr-client-tools"></a>`dmr-client` ferramentas

As ferramentas utilizadas para validar os modelos durante as verificações de RP também podem ser usadas para adicionar e validar as interfaces DTDL localmente.

> [!NOTE]
> Esta ferramenta requer a versão [.NET SDK](https://dotnet.microsoft.com/download) 3.1 ou maior.

### <a name="install-dmr-client"></a>Instalar `dmr-client`

```bash
curl -L https://aka.ms/install-dmr-client-linux | bash
```

```powershell
iwr https://aka.ms/install-dmr-client-windows -UseBasicParsing | iex
```

### <a name="import-a-model-to-the-dtmi-folder"></a>Importar um modelo para a `dtmi/` pasta

Se tiver o seu modelo já armazenado em ficheiros json, pode utilizar o `dmr-client import` comando para os adicionar à pasta com os `dtmi/` nomes corretos do ficheiro:

```bash
# from the local repo root folder
dmr-client import --model-file "MyThermostat.json"
```

> [!TIP]
> Pode utilizar o `--local-repo` argumento para especificar a pasta de raiz do repositório local.

### <a name="validate-models"></a>Validar modelos

Pode validar os seus modelos com o `dmr-client validate` comando:

```bash
dmr-client validate --model-file ./my/model/file.json
```

> [!NOTE]
> A validação utiliza a versão mais recente do parser DTDL para garantir que todas as interfaces são compatíveis com a especificação de idioma DTDL.

Para validar as dependências externas, devem existir no repositório local. Para validar modelos, utilize a `--repo` opção para especificar uma `local` ou pasta para resolver `remote` dependências:

```bash
# from the repo root folder
dmr-client validate --model-file ./my/model/file.json --repo .
```

### <a name="strict-validation"></a>Validação rigorosa

O DMR inclui [requisitos](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)adicionais, use a `stict` bandeira para validar o seu modelo contra eles:

```bash
dmr-client validate --model-file ./my/model/file.json --repo . --strict true
```

Verifique se a saída da consola é de eventuais mensagens de erro.

### <a name="export-models"></a>Exportar modelos

Os modelos podem ser exportados de um dado repositório (local ou remoto) para um único ficheiro utilizando um Conjunto JSON:

```bash
dmr-client export --dtmi "dtmi:com:example:TemperatureController;1" -o TemperatureController.expanded.json
```

## <a name="next-steps"></a>Passos seguintes

O próximo passo sugerido é rever a [arquitetura IoT Plug and Play](concepts-architecture.md).
