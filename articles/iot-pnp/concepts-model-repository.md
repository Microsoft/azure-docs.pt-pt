---
title: Compreender conceitos do repositório do modelo do dispositivo Microsoft Docs
description: Como desenvolvedor de soluções ou profissional de TI, conheça os conceitos básicos do repositório do modelo do dispositivo.
author: rido-min
ms.author: rmpablos
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: b567efe2541bb33c905def73bb78398799b4ed69
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920547"
---
# <a name="device-model-repository"></a>Repositório de modelo de dispositivo

O repositório de modelos do dispositivo (DMR) permite aos construtores de dispositivos gerir e partilhar modelos de dispositivos IoT Plug e Play. Os modelos do dispositivo são documentos JSON LD definidos utilizando a [Linguagem de Modelação Digital Twins (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

O DMR define um padrão para armazenar interfaces DTDL numa estrutura de pasta baseada no identificador de modelos duplos do dispositivo (DTMI). Pode localizar uma interface no DMR convertendo o DTMI num caminho relativo. Por exemplo, o `dtmi:com:example:Thermostat;1` DTMI traduz-se para `/dtmi/com/example/thermostat-1.json` .

## <a name="public-device-model-repository"></a>Repositório de modelo de dispositivo público

A Microsoft acolhe um DMR público com estas características:

- Modelos com curadoria. A Microsoft analisa e aprova todas as interfaces disponíveis utilizando um fluxo de trabalho de validação de pedido de pedido de solicitação (PR) do GitHub.
- Imutabilidade.  Depois de publicado, uma interface não pode ser atualizada.
- Hiperescala. A Microsoft fornece a infraestrutura necessária para criar um ponto final seguro e escalável onde pode publicar e consumir modelos de dispositivos.

## <a name="custom-device-model-repository"></a>Repositório de modelo de dispositivo personalizado

Utilize o mesmo padrão DMR para criar um DMR personalizado em qualquer meio de armazenamento, como sistema de ficheiros local ou servidores web HTTP personalizados. Pode recuperar os modelos do dispositivo a partir do DMR personalizado da mesma forma que a partir do DMR público, alterando o URL base utilizado para aceder ao DMR.

> [!NOTE]
> A Microsoft fornece ferramentas para validar modelos de dispositivos no DMR público. Pode reutilizar estas ferramentas em repositórios personalizados.

## <a name="public-models"></a>Modelos públicos

Os modelos de dispositivos públicos armazenados no repositório de modelos estão disponíveis para todos consumirem e integrarem nas suas aplicações. Os modelos de dispositivos públicos permitem um ecossistema aberto para os construtores de dispositivos e desenvolvedores de soluções partilharem e reutilizarem os seus modelos de dispositivos IoT Plug e Play.

Consulte a secção de [modelos](#publish-a-model) para obter instruções sobre como publicar um modelo no repositório do modelo para torná-lo público.

Os utilizadores podem navegar, pesquisar e ver interfaces públicas a partir do [repositório](https://github.com/Azure/iot-plugandplay-models)oficial do GitHub.

Todas as interfaces nas `dtmi` pastas também estão disponíveis a partir do ponto final público [https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>Resolver modelos

Para aceder programaticamente a estas interfaces, é necessário converter um DTMI num caminho relativo que pode utilizar para consultar o ponto final do público.

Para converter um DTMI num caminho absoluto, utilize a `DtmiToPath` função `IsValidDtmi` com:

```cs
static string DtmiToPath(string dtmi)
{
    if (!IsValidDtmi(dtmi))
    {
        return null;
    }
    // dtmi:com:example:Thermostat;1 -> dtmi/com/example/thermostat-1.json
    return $"/{dtmi.ToLowerInvariant().Replace(":", "/").Replace(";", "-")}.json";
}

static bool IsValidDtmi(string dtmi)
{
    // Regex defined at https://github.com/Azure/digital-twin-model-identifier#validation-regular-expressions
    Regex rx = new Regex(@"^dtmi:[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?(?::[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?)*;[1-9][0-9]{0,8}$");
    return rx.IsMatch(dtmi);
}
```

Com o caminho resultante e o URL base para o repositório podemos obter a interface:

```cs
const string _repositoryEndpoint = "https://devicemodels.azure.com";

string dtmiPath = DtmiToPath(dtmi.ToString());
string fullyQualifiedPath = $"{_repositoryEndpoint}{dtmiPath}";
string modelContent = await _httpClient.GetStringAsync(fullyQualifiedPath);
```

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
