---
title: Usar ações do GitHub com a sincronização de configuração do Azure App
description: Usar ações do GitHub para disparar uma atualização para sua instância de configuração de aplicativo quando as ações definidas forem executadas em um repositório GitHub
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 9d60f1885a85fd7d45090f1cb4905a3d95d9d1d6
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523718"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>Sincronize a sua instância do App Configuration com o GitHub Actions
A Configuração de Aplicações Azure utiliza as Ações GitHub para desencadear atualizações para uma instância de Configuração de Aplicações baseada em ações realizadas num repositório GitHub. Os fluxos de trabalho gitHub desencadeiam atualizações de configuração, permitindo a integração dessas atualizações no mesmo fluxo de trabalho utilizado para atualizar o código da aplicação.

Um [fluxo de trabalho](https://help.github.com/articles/about-github-actions#workflow) gitHub Actions define um processo automatizado num repositório GitHub. Esse processo informa ao GitHub como criar e implantar seu projeto do GitHub. A Configuração da Aplicação Azure fornece a Ação de Sincronização de Configuração de *Aplicações Azure* para permitir atualizações para uma instância de configuração de aplicações quando são feitas alterações no repositório de origem. 

Um ficheiro YAML (.yml) encontrado no caminho `/.github/workflows/` do seu repositório define o seu fluxo de trabalho. Esta definição contém os passos e parâmetros do fluxo de trabalho.

Os eventos do GitHub, como um envio por push para um repositório, podem disparar um fluxo de trabalho de ação do GitHub.  A ação De sincronização de configuração de *aplicações Azure* permite-lhe desencadear uma atualização de uma instância de configuração de aplicações quando ocorre uma ação específica do GitHub. Pode ativar atualizações de configuração ao premir, rever ou ramificar ficheiros de configuração de aplicações, tal como faz com o código da aplicação.

A [documentação](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) gitHub proporciona uma visão aprofundada dos fluxos e ações de trabalho do GitHub. 

## <a name="enable-github-actions-in-your-repository"></a>Habilitar ações do GitHub em seu repositório
Para começar a utilizar esta ação GitHub, vá ao seu repositório e selecione o separador **Ações.** Clique em **Novo fluxo**de trabalho e, em seguida, instale um fluxo de trabalho **por si mesmo**. Finalmente, procure no mercado "Azure App Configuration Sync".
> [!div class="mx-imgBorder"]
> ![Selecione o separador Action](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Selecione a configuração da aplicação Sync Action](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Sincronizar arquivos de configuração após um envio por push
Esta ação sincroniza ficheiros de configuração de aplicações do Azure quando uma alteração é empurrada para `appsettings.json`. Quando um desenvolvedor empurra uma mudança para `appsettings.json`, a ação de Configuração de Aplicações atualiza a instância de Configuração da Aplicação com os novos valores.

A primeira secção deste fluxo de trabalho especifica que a ação dispara *num* *impulso* contendo `appsettings.json` ao ramo *principal.* A segunda seção lista os trabalhos executados quando a ação é disparada. A ação verifica os arquivos relevantes e atualiza a instância de configuração do aplicativo usando a cadeia de conexão armazenada como um segredo no repositório.  Para obter mais informações sobre o uso de segredos no GitHub, consulte o [artigo do GitHub](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) sobre a criação e utilização de segredos encriptados.

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
A ação anterior atualiza a instância de Configuração da Aplicação sempre que `appsettings.json` é atualizada. Esta ação insere um rótulo dinâmico em cada sincronização, garantindo que cada sincronia pode ser identificada de forma única e permitindo que alterações de código sejam mapeadas para alterações de config.

A primeira secção deste fluxo de trabalho especifica que a ação dispara *num* *impulso* contendo `appsettings.json` ao ramo *principal.* A segunda secção executa um trabalho que cria uma etiqueta única para a atualização de config com base no hash de compromisso. Em seguida, o trabalho atualiza a instância de configuração do aplicativo com os novos valores e o rótulo exclusivo para essa atualização.

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
Quando o modo estrito está habilitado, a sincronização garante que a instância de configuração do aplicativo corresponda ao arquivo de configuração para o prefixo e rótulo fornecidos exatamente. Os pares de valor-chave com o mesmo prefixo e etiqueta que não estão no ficheiro de configuração são eliminados. 
 
Se o modo rígido não estiver ativado, o sync apenas definirá os valores-chave do ficheiro de configuração. Nenhum par chave-valor será excluído. 

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

Se o objeto aninhado se destina restar ao valor empurrado para a instância de Configuração, pode utilizar o valor de *profundidade* para parar o achatamento na profundidade adequada. 

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

Dada uma profundidade de 2, o exemplo acima devolve agora o seguinte par de valor-chave:

| Chave | Valor |
| --- | --- |
| Objeto: interno | {"InnerKey": "InnerException"} |

## <a name="understand-action-inputs"></a>Entender as entradas da ação
Os parâmetros de entrada especificam os dados usados pela ação durante o tempo de execução.  A tabela a seguir contém parâmetros de entrada aceitos pela sincronização de configuração de aplicativo e os valores esperados para cada um.  Para obter mais informações sobre as inputs de ação para as ações do GitHub, consulte a [documentação](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)do GitHub.

> [!Note]
> As IDs de entrada não diferenciam maiúsculas de minúsculas.


| Nome de entrada | Necessário? | Valor |
|----|----|----|
| configurationFile | Sim | Caminho relativo para o ficheiro de configuração no repositório.  Os padrões glob têm suporte e podem incluir vários arquivos. |
| format | Sim | Formato de arquivo do arquivo de configuração.  Os formatos válidos são: JSON, YAML, Properties. |
| connectionString | Sim | Cadeia de conexão para a instância de configuração do aplicativo. A cadeia de conexão deve ser armazenada como um segredo no repositório do GitHub e somente o nome do segredo deve ser usado no fluxo de trabalho. |
| separador | Sim | Separador usado ao mesclar o arquivo de configuração para pares de chave-valor.  Os valores válidos são:. , ; : - _ __ / |
| prefix | Não | Prefixo a ser adicionado ao início das chaves. |
| label | Não | Rótulo usado ao definir pares de chave-valor. Se não for especificado, será usado um rótulo nulo. |
| Strict | Não | Um valor booliano que determina se o modo estrito está habilitado. O valor predefinido é false. |
| Detalhado | Não | Profundidade máxima para mesclar o arquivo de configuração.  A profundidade deve ser um número positivo.  O padrão não terá nenhuma profundidade máxima. |
| etiquetas | Não | Especifica a marca definida em pares chave-valor.  O formato esperado é uma forma stringizada de um objeto JSON da seguinte forma: { [propertyName: string]: string; } Cada valor de nome de propriedade torna-se uma etiqueta. |

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre a ação de sincronização de configuração de aplicativo do GitHub e como ela pode ser usada para automatizar atualizações para sua instância de configuração de aplicativo. Para saber como a Configuração de Aplicações Azure reage a mudanças nos pares de valor-chave, continue para o próximo [artigo](./concept-app-configuration-event.md).
