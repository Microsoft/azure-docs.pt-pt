---
title: Compreender conceitos do Repositório de Modelos de Dispositivos Microsoft Docs
description: Como desenvolvedor de soluções ou profissional de TI, conheça os conceitos básicos do Repositório de Modelos de Dispositivo.
author: rido-min
ms.author: rmpablos
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 95c9b6dee402bc0c2dd2cab8ef3200cfd9213d61
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92126829"
---
# <a name="device-model-repository"></a>Repositório de modelo de dispositivo

O repositório de modelos do dispositivo (DMR) permite aos construtores de dispositivos gerir e partilhar modelos de dispositivos IoT Plug e Play. Os modelos do dispositivo são documentos JSON LD definidos utilizando a [Linguagem de Modelação Digital Twins (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

O DMR define um padrão para armazenar interfaces DTDL numa estrutura de pasta baseada no identificador de modelos duplos do dispositivo (DTMI). Pode localizar uma interface no DMR convertendo o DTMI num caminho relativo. Por exemplo, o `dtmi:com:example:Thermostat;1` DTMI traduz-se para `/dtmi/com/example/thermostat-1.json` .

## <a name="public-device-model-repository"></a>Repositório de modelo de dispositivo público

A Microsoft acolhe um DMR público com estas características:

- Modelos com curadoria. A Microsoft analisa e aprova todas as interfaces disponíveis utilizando um fluxo de trabalho de validação de PR gitHub aberto.
- Imutabilidade.  Depois de publicado, uma interface não pode ser atualizada.
- Hiperescala. A Microsoft fornece todas as infraestruturas necessárias para criar um ponto final seguro e altamente escalável.

## <a name="custom-device-model-repository"></a>Repositório de modelo de dispositivo personalizado

Pode utilizar o mesmo padrão DMR em qualquer meio de armazenamento, como sistema de ficheiros local ou servidores web HTTP personalizados, para criar um DMR personalizado. Pode obter os modelos a partir do DMR personalizado da mesma forma que a partir do DMR público simplesmente alterando o URL base utilizado para aceder ao DMR.

> [!NOTE]
> As ferramentas utilizadas para validar os modelos no DMR público podem ser reutilizadas em repositórios personalizados.

## <a name="public-models"></a>Modelos públicos

Os modelos digitais digitais de twin armazenados no repositório de modelos estão disponíveis para todos consumirem e integrarem nas suas aplicações. Os modelos públicos permitem um ecossistema aberto para os construtores de dispositivos e desenvolvedores de soluções partilharem e reutilizarem os seus modelos de dispositivos IoT Plug e Play.

Consulte a secção de [modelos](#publish-a-model) para obter instruções sobre como publicar um modelo no repositório do modelo para torná-lo público.

Os utilizadores podem navegar, pesquisar e ver interfaces públicas a partir do [repositório](https://github.com/Azure/iot-plugandplay-models)oficial do GitHub.

Todas as interfaces nas `dtmi` pastas também estão disponíveis a partir do ponto final público [https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>Resolver modelos

Para aceder programaticamente a estas interfaces, é necessário converter um dtmi num caminho relativo que pode utilizar para consultar o ponto final do público. A seguinte amostra de código mostra-lhe como fazê-lo:

Para converter um DTMI num caminho absoluto usamos a `DtmiToPath` função, `IsValidDtmi` com:

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

1. Fork o público GitHub repo: [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models) .
1. Clone o repo forcado. Opcionalmente criar um novo ramo para manter as suas alterações isoladas do `main` ramo.
1. Adicione as novas interfaces à `dtmi` pasta utilizando a convenção de pasta/nome de ficheiro. Consulte a ferramenta [de modelo de adicionar.](#add-model)
1. Valide os modelos localmente utilizando os [scripts para validar a secção de alterações.](#validate-files)
1. Cometa as alterações localmente e empurre para o seu garfo.
1. Do seu garfo, crie um pr que visa o `main` ramo. Consulte [a criação de um problema ou puxe](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request) os docs de pedido.
1. Reveja os [requisitos de Relações Públicas.](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)

O PR desencadeia uma série de ações do GitHub que validarão as novas interfaces submetidas e certifique-se de que o seu RP satisfaz todas as verificações.

A Microsoft responderá a um Pr com todos os cheques em três dias úteis.

### <a name="add-model"></a>adicionar modelo

Os passos seguintes mostram-lhe como o add-model.js script ajuda a adicionar uma nova interface. Este script requer Node.js para executar:

1. De um pedido de comando, navegue para o repo git local
1. Executar `npm install`
1. Executar `npm run add-model <path-to-file-to-add>`

Observe a saída da consola para obter eventuais mensagens de erro.

### <a name="local-validation"></a>Validação local

Pode executar os mesmos controlos de validação localmente antes de submeter o Pr para ajudar a diagnosticar problemas com antecedência.

#### <a name="validate-files"></a>validar ficheiros

`npm run validate-files <file1.json> <file2.json>` verifica o percurso do ficheiro corresponde à pasta esperada e aos nomes dos ficheiros.

#### <a name="validate-ids"></a>validar ids

`npm run validate-ids <file1.json> <file2.json>` verifica que todos os IDs definidos no documento usam a mesma raiz que o ID principal.

#### <a name="validate-deps"></a>validar-deps

`npm run validate-deps <file1.json> <file2.json>` verifica todas as dependências disponíveis na `dtmi` pasta.

#### <a name="validate-models"></a>validar modelos

Pode executar a [Amostra de Validação DTDL](https://github.com/Azure-Samples/DTDL-Validator) para validar os seus modelos localmente.

## <a name="next-steps"></a>Passos seguintes

O próximo passo sugerido é rever a [arquitetura IoT Plug and Play](concepts-architecture.md).
