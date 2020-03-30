---
title: Use ações gitHub com sincronização de configuração de aplicações azure
description: Use as ações do GitHub para desencadear uma atualização para a sua configuração de aplicações quando atualizar o seu repositório GitHub
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 46d4aa4d4d37e9cac928e8d1a9e5e77ca0f30f18
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384063"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>Sincronize a sua instância do App Configuration com o GitHub Actions

As equipas que pretendam continuar a utilizar as suas práticas de controlo de fontes existentes podem utilizar as Ações GitHub para sincronizar automaticamente o seu repositório GitHub com a sua loja de Configuração de Aplicações. Isto permite-lhe fazer alterações nos seus ficheiros config como normalmente faria, ao mesmo tempo que recebe benefícios de Configuração de Aplicações como: <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Configuração centralizada fora do seu código <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Atualizar a configuração sem recolocar toda a sua aplicação <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Integração com serviços como o Azure App Service e as Funções. 

Um [fluxo de trabalho](https://help.github.com/articles/about-github-actions#workflow) gitHub Actions define um processo automatizado num repositório GitHub. A Ação de Sincronização de Configuração de *Aplicações Azure* aciona atualizações para uma instância de configuração de aplicações quando são feitas alterações no repositório de origem. Utiliza um ficheiro YAML (.yml) `/.github/workflows/` encontrado no caminho do seu repositório para definir os passos e parâmetros. Pode ativar atualizações de configuração ao premir, rever ou ramificar ficheiros de configuração de aplicações, tal como faz com o código da aplicação.

A [documentação](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) gitHub proporciona uma visão aprofundada dos fluxos e ações de trabalho do GitHub. 

## <a name="enable-github-actions-in-your-repository"></a>Ativar ações gitHub no seu repositório
Para começar a utilizar esta ação GitHub, vá ao seu repositório e selecione o separador **Ações.** Clique em **Novo fluxo**de trabalho e, em seguida, instale um fluxo de trabalho **por si mesmo**. Finalmente, procure no mercado "Azure App Configuration Sync".
> [!div class="mx-imgBorder"]
> ![Selecione o separador Action](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Selecione a sincronização de configuração da aplicação Action](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Sync ficheiros de configuração após um impulso
Esta ação sincroniza ficheiros de configuração de `appsettings.json`aplicações do Azure quando uma alteração é empurrada para . Quando um desenvolvedor empurra `appsettings.json`uma mudança para , a ação de Configuração de Aplicações atualiza a instância de configuração da aplicação com os novos valores.

A primeira secção deste fluxo de trabalho especifica que `appsettings.json` a ação dispara *sobre* um *impulso* que contenha ao ramo *principal.* A segunda secção enumera os postos de trabalho executados assim que a ação for desencadeada. A ação verifica os ficheiros relevantes e atualiza a instância de Configuração da Aplicação utilizando a cadeia de ligação armazenada como segredo no repositório.  Para obter mais informações sobre o uso de segredos no GitHub, consulte o [artigo do GitHub](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) sobre a criação e utilização de segredos encriptados.

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

## <a name="use-a-dynamic-label-on-sync"></a>Use um rótulo dinâmico na sincronização
A ação anterior atualiza a `appsettings.json` instância de Configuração da Aplicação sempre que for atualizada. Esta ação insere um rótulo dinâmico em cada sincronização, garantindo que cada sincronia pode ser identificada de forma única e permitindo que alterações de código sejam mapeadas para alterações de config.

A primeira secção deste fluxo de trabalho especifica que `appsettings.json` a ação dispara *sobre* um *impulso* que contenha ao ramo *principal.* A segunda secção executa um trabalho que cria uma etiqueta única para a atualização de config com base no hash de compromisso. O trabalho atualiza então a instância de Configuração da Aplicação com os novos valores e a etiqueta única para esta atualização.

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

## <a name="use-strict-sync"></a>Use sincronização rigorosa
Quando o modo rígido está ativado, o sync garante que a instância de configuração da aplicação corresponde exatamente ao ficheiro de configuração para o prefixo e etiqueta dada. Os pares de valor-chave com o mesmo prefixo e etiqueta que não estão no ficheiro de configuração são eliminados. 
 
Se o modo rígido não estiver ativado, o sync apenas definirá os valores-chave do ficheiro de configuração. Nenhum par de valor-chave será eliminado. 

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

## <a name="use-max-depth-to-limit-github-action"></a>Use a profundidade máxima para limitar a Ação GitHub
O comportamento padrão para atributos JSON aninhados é aplainar todo o objeto.  O JSON abaixo define este par de valor-chave:

| Chave | Valor |
| --- | --- |
| Objeto:Interior:InnerKey | InnerValue |

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
| Objeto:Interior | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>Compreender os inputs de ação
Os parâmetros de entrada especificam os dados utilizados pela ação durante o tempo de execução.  A tabela seguinte contém parâmetros de entrada aceites pelo App Configuration Sync e os valores esperados para cada um.  Para obter mais informações sobre as inputs de ação para as ações do GitHub, consulte a [documentação](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)do GitHub.

> [!Note]
> As identificações de entrada são insensíveis.


| Nome de entrada | Necessário? | Valor |
|----|----|----|
| configuraçãoFile | Sim | Caminho relativo para o ficheiro de configuração no repositório.  Os padrões glob são suportados e podem incluir vários ficheiros. |
| formato | Sim | Formato de ficheiro do ficheiro de configuração.  Os formatos válidos são: JSON, YAML, propriedades. |
| conexãoString | Sim | Fio de ligação para a instância de configuração da aplicação. A cadeia de ligação deve ser armazenada como um segredo no repositório GitHub, e apenas o nome secreto deve ser usado no fluxo de trabalho. |
| separador | Sim | Separador utilizado ao aplainar o ficheiro de configuração para pares de valor-chave.  Valores válidos são: . , ; : - _ __ / |
| prefixo | Não | Prefixo a ser adicionado ao início das teclas. |
| label | Não | Etiqueta utilizada ao definir pares de valor-chave. Se não especificado, é utilizada uma etiqueta nula. |
| rígido | Não | Um valor booleano que determina se o modo rígido está ativado. O valor predefinido é false. |
| profundidade | Não | Profundidade máxima para aplainar o ficheiro de configuração.  Profundidade deve ser um número positivo.  O padrão não terá profundidade máxima. |
| etiquetas | Não | Especifica a etiqueta definida nos pares de valor-chave.  O formato esperado é uma forma stringizada de um objeto JSON da seguinte forma: { [propertyName: string]: string; } Cada valor de nome de propriedade torna-se uma etiqueta. |

## <a name="next-steps"></a>Passos seguintes

Neste artigo, soube da Configuração de Aplicações Sync GitHub Action e de como pode ser usado para automatizar atualizações para a sua instância de Configuração de Aplicações. Para saber como a Configuração de Aplicações Azure reage a mudanças nos pares de valor-chave, continue para o próximo [artigo](./concept-app-configuration-event.md).
