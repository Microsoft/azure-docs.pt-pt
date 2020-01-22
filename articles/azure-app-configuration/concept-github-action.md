---
title: Usar ações do GitHub com a sincronização de configuração do Azure App
description: Usar ações do GitHub para disparar uma atualização para sua instância de configuração de aplicativo quando as ações definidas forem executadas em um repositório GitHub
author: jpconnock
ms.author: jeconnoc
ms.date: 01/14/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 1b7586e6321318a3fe4566e8c744c17a1d7ee179
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310625"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>Sincronizar a instância de configuração do aplicativo usando as ações do GitHub
Azure App configuração usa ações do GitHub para atualizar uma instância de configuração de aplicativo quando disparada por uma ação executada em um repositório GitHub. Você pode aproveitar os fluxos de trabalho do GitHub para atualizar a configuração do aplicativo, permitindo a integração das atualizações de configuração do aplicativo no mesmo fluxo de trabalho usado para atualizar o código do aplicativo.

Um fluxo de [trabalho](https://help.github.com/articles/about-github-actions#workflow) de ações do GitHub é um processo automatizado definido em seu repositório github. Esse processo informa ao GitHub como criar e implantar seu projeto do GitHub. Azure App configuração fornece a ação de *sincronização de configuração de Azure app* para habilitar atualizações em uma instância de configuração de aplicativo quando são feitas alterações no repositório de origem. 

Um fluxo de trabalho é definido por um arquivo YAML (. yml) encontrado no caminho `/.github/workflows/` do seu repositório. Essa definição contém as várias etapas e parâmetros que definem o fluxo de trabalho.

Os eventos do GitHub, como um envio por push para um repositório, podem disparar um fluxo de trabalho de ação do GitHub.  O Azure fornece a ação de *sincronização de configuração Azure app* para permitir que você Acione uma atualização de uma instância de configuração de aplicativo quando ocorrer uma ação especificada do github. Isso permite que as equipes aproveitem os principais recursos do GitHub ao enviar, revisar ou ramificar arquivos de configuração de aplicativo da mesma forma que no código do aplicativo.

A [documentação](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) do GitHub fornece uma visão detalhada dos fluxos de trabalho e ações do github. 

## <a name="enable-github-actions-in-your-repository"></a>Habilitar ações do GitHub em seu repositório
Para começar a usar essa ação do GitHub, vá para o repositório e selecione a guia **ações** . Localize e selecione a ação do GitHub no Marketplace pesquisando "sincronização de configuração do Azure app". 

> [!div class="mx-imgBorder"]
> ![selecione a guia ação](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![selecione a ação syn de configuração de aplicativo](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Sincronizar arquivos de configuração após um envio por push
Essa ação sincroniza Azure App arquivos de configuração quando uma alteração é enviada por push para `appsettings.json`. Quando um desenvolvedor faz e envia uma alteração para `appsettings.json`, a ação de sincronização de configuração de aplicativo atualiza a instância de configuração de aplicativo com os novos valores.

A primeira seção desse fluxo de trabalho especifica que a ação é disparada *em* um *Push* contendo `appsettings.json` para a Branch *mestre* . A segunda seção lista os trabalhos executados quando a ação é disparada. A ação verifica os arquivos relevantes e atualiza a instância de configuração do aplicativo usando a cadeia de conexão armazenada como um segredo no repositório.  Para obter mais informações sobre como usar segredos no GitHub, consulte [Este artigo](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) sobre como criar e usar segredos criptografados.

```json
on: 
  push: 
    branches: 
      - 'master' 
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

## <a name="use-a-dynamic-label-on-sync"></a>Usar um rótulo dinâmico na sincronização
A ação anterior simplesmente atualizou a instância de configuração do aplicativo sempre que `appsettings.json` for atualizada. Essa ação insere um rótulo dinâmico em cada sincronização, garantindo que cada sincronização possa ser identificada exclusivamente.  Isso permite que as alterações de código sejam mapeadas rapidamente para alterações de configuração.

A primeira seção desse fluxo de trabalho especifica que a ação é disparada *em* um *Push* contendo `appsettings.json` para a Branch *mestre* . A segunda seção executa um trabalho que cria um rótulo exclusivo para a atualização de configuração com base no hash de confirmação. Em seguida, o trabalho atualiza a instância de configuração do aplicativo com os novos valores e o rótulo exclusivo para essa atualização.

```json
on: 
  push: 
    branches: 
      - 'master' 
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

## <a name="use-strict-sync"></a>Usar a sincronização estrita
Quando o modo estrito está habilitado, a sincronização garante que a instância de configuração do aplicativo corresponda ao arquivo de configuração para o prefixo e rótulo fornecidos exatamente. Pares de chave-valor com o mesmo prefixo e rótulo que não estão no arquivo de configuração são excluídos. 
 
Se o modo estrito não estiver habilitado, a sincronização definirá somente valores de chave do arquivo de configuração. Nenhum par chave-valor será excluído. 

```json
on: 
  push: 
    branches: 
      - 'master' 
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

## <a name="use-max-depth-to-limit-github-action"></a>Usar a profundidade máxima para limitar a ação do GitHub
O comportamento padrão para atributos JSON aninhados é mesclar o objeto inteiro.  O JSON abaixo define esse par de chave-valor:

| Chave | Valor |
| --- | --- |
| Objeto: interno: InnerKey | InnerException |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```
Se o objeto aninhado for destinado ao valor enviado por push para a instância de configuração, você poderá usar o valor de *profundidade* para interromper o nivelamento na profundidade apropriada. 

```json
on: 
  push: 
    branches: 
      - 'master' 
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

Dada uma profundidade de 2, o exemplo acima agora retorna o seguinte par de chave: valor:

| Chave | Valor |
| --- | --- |
| Objeto: interno | {"InnerKey": "InnerException"} |

## <a name="understand-action-inputs"></a>Entender as entradas da ação
Os parâmetros de entrada especificam os dados usados pela ação durante o tempo de execução.  A tabela a seguir contém parâmetros de entrada aceitos pela sincronização de configuração de aplicativo e os valores esperados para cada um.  Para obter mais informações sobre entradas de ação para ações do GitHub, consulte a [documentação](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)do github.

> [!Note]
> As IDs de entrada não diferenciam maiúsculas de minúsculas.


| Nome de entrada | Necessário? | Valor |
|----|----|----|
| configurationFile | Sim | Caminho para o arquivo de configuração no repositório, em relação à raiz do repositório.  Os padrões glob têm suporte e podem incluir vários arquivos. |
| format | Sim | Formato de arquivo do arquivo de configuração.  Os formatos válidos são: JSON, YAML, Properties. |
| connectionString | Sim | Cadeia de conexão para a instância de configuração do aplicativo. A cadeia de conexão deve ser armazenada como um segredo no repositório do GitHub e somente o nome do segredo deve ser usado no fluxo de trabalho. |
| separator | Sim | Separador usado ao mesclar o arquivo de configuração para pares de chave-valor.  Os valores válidos são:. , ; : - _ __ / |
| prefix | Não | Prefixo a ser adicionado ao início das chaves. |
| label | Não | Rótulo usado ao definir pares de chave-valor. Se não for especificado, será usado um rótulo nulo. |
| Strict | Não | Um valor booliano que determina se o modo estrito está habilitado. O valor predefinido é false. |
| Detalhado | Não | Profundidade máxima para mesclar o arquivo de configuração.  A profundidade deve ser um número positivo.  O padrão não terá nenhuma profundidade máxima. |
| etiquetas | Não | Especifica a marca definida em pares chave-valor.  O formato esperado é uma forma em cadeias de um objeto JSON da seguinte forma: {[propertyName: String]: String;} Cada nome de propriedade-valor torna-se uma marca. |

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre a ação de sincronização de configuração de aplicativo do GitHub e como ela pode ser usada para automatizar atualizações para sua instância de configuração de aplicativo. Para saber como Azure App configuração reage às alterações em pares chave-valor, vá para o próximo [artigo](./concept-app-configuration-event.md).
