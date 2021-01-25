---
title: Sync o seu repositório GitHub para a Configuração de Aplicações
description: Utilize as ações do GitHub para atualizar automaticamente a sua instância de Configuração de Aplicações quando atualizar o seu repositório GitHub.
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 05/28/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 1c290032f7a33079b560d3c4cc1fcb9526e70331
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762159"
---
# <a name="sync-your-github-repository-to-app-configuration"></a>Sync o seu repositório GitHub para a Configuração de Aplicações

As equipas que pretendam continuar a utilizar as suas práticas de controlo de origem existentes podem utilizar as Ações GitHub para sincronizar automaticamente o seu repositório GitHub com a sua loja de Configuração de Aplicações. Isto permite-lhe fazer alterações nos seus ficheiros config como normalmente faria, ao mesmo tempo que recebe benefícios de Configuração de Aplicações como: <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Configuração centralizada fora do seu código <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Atualizar a configuração sem recolocar toda a sua app <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Integração com serviços como o Azure App Service and Functions. 

Um fluxo [de trabalho](https://docs.github.com/en/actions/learn-github-actions/introduction-to-github-actions#the-components-of-github-actions) gitHub Actions define um processo automatizado num repositório GitHub. A *Azure App Configuration Sync* Action desencadeia atualizações para uma instância de Configuração de Aplicações quando são feitas alterações ao repositório de origem. Utiliza um ficheiro YAML (.yml) encontrado no `/.github/workflows/` caminho do seu repositório para definir os passos e parâmetros. Pode desencadear atualizações de configuração ao premir, rever ou ramificar ficheiros de configuração de aplicações, tal como faz com o código da aplicação.

A [documentação](https://docs.github.com/en/actions/learn-github-actions/introduction-to-github-actions) do GitHub fornece uma visão aprofundada dos fluxos de trabalho e ações do GitHub. 

## <a name="enable-github-actions-in-your-repository"></a>Ativar as ações do GitHub no seu repositório
Para começar a utilizar esta ação do GitHub, vá ao seu repositório e selecione o **separador Ações.** Selecione **novo fluxo de trabalho** e, em seguida, crie um fluxo de trabalho por si **mesmo**. Finalmente, procure no mercado "Azure App Configuration Sync".
> [!div class="mx-imgBorder"]
> ![Selecione o separador Ação](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Selecione a sincronização de configuração da aplicação Ação](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Sync ficheiros de configuração após um empurrão
Esta ação sincroniza ficheiros de configuração de aplicações Azure quando uma alteração é empurrada para `appsettings.json` . Quando um desenvolvedor empurra uma alteração para `appsettings.json` , a ação De Sincronização de Configuração de Aplicação de Aplicações atualiza a instância de Configuração da Aplicação com os novos valores.

A primeira secção deste fluxo de trabalho especifica que a ação desencadeia *um* *impulso* contendo o `appsettings.json` ramo *principal.* A segunda secção lista os postos de trabalho executados assim que a ação é desencadeada. A ação verifica os ficheiros relevantes e atualiza a instância de Configuração da Aplicação utilizando a cadeia de ligação armazenada como segredo no repositório.  Para obter mais informações sobre a utilização de segredos no GitHub, consulte o [artigo do GitHub](https://docs.github.com/en/actions/reference/encrypted-secrets) sobre a criação e utilização de segredos encriptados.

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your                        
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }} 
          separator: ':' 
```

## <a name="use-strict-sync"></a>Use sincronização rigorosa
Por predefinição, a ação do GitHub não permite o modo rigoroso, o que significa que a sincronização apenas adicionará valores-chave do ficheiro de configuração à instância de Configuração da Aplicação (nenhum par de valores-chave será eliminado). Ativar o modo rígido significa que os pares de valor-chave que não estão no ficheiro de configuração são eliminados da instância de Configuração da Aplicação, de modo a que corresponda ao ficheiro de configuração. Se estiver a sincronizar a partir de várias fontes ou utilizar o Cofre de Chaves Azure com configuração de aplicações, irá querer utilizar diferentes prefixos ou etiquetas com sincronização rigorosa para evitar eliminar as definições de configuração de outros ficheiros (ver amostras abaixo). 

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: 'Label' 
          prefix: 'Prefix:' 
          strict: true 
```
## <a name="sync-multiple-files-in-one-action"></a>Sincronizar vários ficheiros numa ação 

Se a sua configuração estiver em vários ficheiros, pode utilizar o padrão abaixo para desencadear uma sincronização quando um dos ficheiros for modificado. Este padrão usa a biblioteca https://www.npmjs.com/package/glob glob. Note que se o nome do seu ficheiro config contiver uma vírgula, pode usar uma contrala para escapar à vírgula. 

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'
      - 'appsettings2.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: '{appsettings.json,appsettings2.json}'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
```

## <a name="sync-by-prefix-or-label"></a>Sincronizar por prefixo ou etiqueta
Especificar prefixos ou etiquetas na sua ação de sincronização sincronizará apenas esse conjunto específico. Isto é importante para usar sincronização rigorosa com vários ficheiros. Dependendo da configuração da configuração, um prefixo ou um rótulo podem ser associados a cada ficheiro e, em seguida, cada prefixo ou etiqueta pode ser sincronizado separadamente para que nada seja substituído. Normalmente, os prefixos são usados para diferentes aplicações ou serviços e os rótulos são usados para diferentes ambientes. 

Sincronizar por prefixo: 

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          prefix: 'Prefix::'
```

Sincronizar por etiqueta: 

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          label: 'Label'

```

## <a name="use-a-dynamic-label-on-sync"></a>Use um rótulo dinâmico na sincronização
A ação a seguir insere um rótulo dinâmico em cada sincronização, garantindo que cada sincronização pode ser identificada de forma única e permitindo que as alterações de código sejam mapeadas para configurar alterações.

A primeira secção deste fluxo de trabalho especifica que a ação desencadeia *um* *impulso* contendo o `appsettings.json` ramo *principal.* A segunda secção executa um trabalho que cria um rótulo único para a atualização config com base no haxixe compromete-se. O trabalho atualiza então a instância de Configuração da Aplicação com os novos valores e o rótulo único para esta atualização.

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # Creates a label based on the branch name and the first 8 characters          
      # of the commit hash 
      - id: determine_label 
        run: echo ::set-output name=LABEL::"${GITHUB_REF#refs/*/}/${GITHUB_SHA:0:8}" 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: ${{ steps.determine_label.outputs.LABEL }} 
```

## <a name="use-azure-key-vault-with-github-action"></a>Use a adaboçada-chave Azure com ação GitHub
Os desenvolvedores que usam O Cofre de Chaves Azure com AppConfiguration devem usar dois ficheiros separados, normalmente um appsettings.jse uma secretreferences.js. O secretreferences.jsvai conter a url para o segredo do cofre.

{ "mySecret": "{ \" uri \" : \" https://myKeyVault.vault.azure.net/secrets/mySecret "}" }

A Ação GitHub pode então ser configurada para fazer uma sincronização rigorosa sobre o appsettings.js, seguida de uma sincronização não rigorosa sobre secretreferences.js. A amostra a seguir irá desencadear uma sincronização quando um dos ficheiros for atualizado:

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'
      - 'secretreferences.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          strict: true
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'secretreferences.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          contentType: 'application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8'

```

## <a name="use-max-depth-to-limit-github-action"></a>Use profundidade máxima para limitar a ação do GitHub
O comportamento padrão para atributos JSON aninhados é para achatar todo o objeto.  O JSON abaixo define este par de valores-chave:

| Chave | Valor |
| --- | --- |
| Objeto:Interior:InnerKey | Interior DeValue |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

Se o objeto aninhado se destinar a ser o valor empurrado para a instância configuração, pode utilizar o valor de *profundidade* para parar o achatamento na profundidade adequada. 

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          depth: 2 
```

Dada uma profundidade de 2, o exemplo acima devolve agora o seguinte par de valores-chave:

| Chave | Valor |
| --- | --- |
| Objeto:Interior | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>Compreender entradas de ação
Os parâmetros de entrada especificam os dados utilizados pela ação durante o tempo de funcionamento.  A tabela seguinte contém parâmetros de entrada aceites pelo App Configuration Sync e os valores esperados para cada um.  Para obter mais informações sobre entradas de ação para ações do GitHub Actions, consulte a [documentação](https://docs.github.com/en/actions/creating-actions/metadata-syntax-for-github-actions#inputs)do GitHub .

> [!Note]
> As entradas são insensíveis a casos.


| Nome de entrada | Necessário? | Valor |
|----|----|----|
| ficheiro de configuração | Sim | Caminho relativo para o ficheiro de configuração no repositório.  Os padrões glob são suportados e podem incluir vários ficheiros. |
| formato | Sim | Formato de ficheiro do ficheiro de configuração.  Os formatos válidos são: JSON, YAML, propriedades. |
| conexãoStragem | Sim | Cadeia de ligação para a instância de configuração da aplicação. A cadeia de ligação deve ser armazenada como um segredo no repositório GitHub, e apenas o nome secreto deve ser usado no fluxo de trabalho. |
| separador | Sim | Separador utilizado ao aplanar o ficheiro de configuração para pares de valor-chave.  Os valores válidos são: . , ; : - _ __ / |
| prefixo | Não | Prefixo a ser adicionado ao início das teclas. |
| etiqueta | Não | Etiqueta utilizada ao definir pares de valor de chave. Se não for especificado, é utilizada uma etiqueta nulo. |
| rigoroso | Não | Um valor booleano que determina se o modo rígido está ativado. O valor predefinido é false. |
| profundidade | Não | Profundidade máxima para achatar o ficheiro de configuração.  Profundidade deve ser um número positivo.  O padrão não terá profundidade máxima. |
| etiquetas | Não | Especifica a etiqueta definida em pares de valor-chave.  O formato esperado é uma forma stringified de um objeto JSON da seguinte forma: { [nome de propriedade: corda]: corda; } Cada valor-nome de propriedade torna-se uma etiqueta. |

## <a name="next-steps"></a>Próximos passos

Neste artigo, aprendeu sobre a Configuração da Aplicação Sync GitHub Action e como pode ser usada para automatizar atualizações para a sua instância de Configuração de Aplicações. Para saber como a Configuração da Aplicação Azure reage a alterações em pares de valor-chave, continue para o próximo [artigo](./concept-app-configuration-event.md).
