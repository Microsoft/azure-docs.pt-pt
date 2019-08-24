---
title: Campos personalizados no Azure Monitor | Microsoft Docs
description: O recurso campos personalizados do Azure Monitor permite que você crie seus próprios campos pesquisáveis de registros em um espaço de trabalho Log Analytics que são adicionados às propriedades de um registro coletado.  Este artigo descreve o processo para criar um campo personalizado e fornece uma explicação detalhada com um evento de exemplo.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 31572b51-6b57-4945-8208-ecfc3b5304fc
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/23/2019
ms.author: bwren
ms.openlocfilehash: f6b9c21a3d65e75abe11e705eba058b1d1fb17ff
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012724"
---
# <a name="create-custom-fields-in-a-log-analytics-workspace-in-azure-monitor"></a>Criar campos personalizados em um espaço de trabalho Log Analytics no Azure Monitor

> [!NOTE]
> Este artigo descreve como analisar dados de texto em um espaço de trabalho Log Analytics como ele é coletado. É recomendável analisar dados de texto em um filtro de consulta depois que ele é coletado seguindo as diretrizes descritas em [analisar dados de texto em Azure monitor](../log-query/parse-text.md). Ele fornece várias vantagens em relação ao uso de campos personalizados.

O recurso **campos personalizados** do Azure monitor permite que você estenda os registros existentes em seu espaço de trabalho log Analytics adicionando seus próprios campos pesquisáveis.  Os campos personalizados são preenchidos automaticamente a partir de dados extraídos de outras propriedades no mesmo registro.

![Descrição geral](media/custom-fields/overview.png)

Por exemplo, o registro de exemplo abaixo tem dados úteis enterrados na descrição do evento. A extração desses dados em uma propriedade separada torna-o disponível para ações como classificação e filtragem.

![Extração de amostra](media/custom-fields/sample-extract.png)

> [!NOTE]
> Na visualização, você está limitado a 100 campos personalizados em seu espaço de trabalho.  Esse limite será expandido quando esse recurso atingir a disponibilidade geral.

## <a name="creating-a-custom-field"></a>Criando um campo personalizado
Quando você cria um campo personalizado, Log Analytics deve entender quais dados usar para popular seu valor.  Ele usa uma tecnologia da Microsoft Research chamada FlashExtract para identificar rapidamente esses dados.  Em vez de exigir que você forneça instruções explícitas, Azure Monitor aprende sobre os dados que você deseja extrair dos exemplos que você fornece.

As seções a seguir fornecem o procedimento para criar um campo personalizado.  Na parte inferior deste artigo, há uma explicação sobre uma extração de amostra.

> [!NOTE]
> O campo personalizado é preenchido conforme os registros correspondentes aos critérios especificados são adicionados ao espaço de trabalho Log Analytics, portanto, ele só aparecerá nos registros coletados depois que o campo personalizado for criado.  O campo personalizado não será adicionado aos registros que já estão no armazenamento de dados quando ele é criado.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Etapa 1 – identificar os registros que terão o campo personalizado
A primeira etapa é identificar os registros que receberão o campo personalizado.  Você começa com uma [consulta de log padrão](../log-query/log-query-overview.md) e, em seguida, seleciona um registro para atuar como modelo do qual Azure monitor aprenderá.  Quando você especifica que vai extrair dados em um campo personalizado, o assistente de extração de **campo** é aberto onde você valida e refina os critérios.

1. Acesse **logs** e use uma [consulta para recuperar os registros](../log-query/log-query-overview.md) que terão o campo personalizado.
2. Selecione um registro que Log Analytics será usado para atuar como um modelo de extração de dados para popular o campo personalizado.  Você identificará os dados que deseja extrair deste registro e Log Analytics usará essas informações para determinar a lógica para preencher o campo personalizado para todos os registros semelhantes.
3. Expanda as propriedades do registro, clique na elipse à esquerda da propriedade Top do registro e selecione **extrair campos de**.
4. O **Assistente** de extração de campo é aberto e o registro selecionado é exibido na coluna **exemplo principal** .  O campo personalizado será definido para esses registros com os mesmos valores nas propriedades selecionadas.  
5. Se a seleção não for exatamente o que você deseja, selecione campos adicionais para restringir os critérios.  Para alterar os valores de campo para os critérios, você deve cancelar e selecionar um registro diferente correspondente aos critérios desejados.

### <a name="step-2---perform-initial-extract"></a>Etapa 2 – executar a extração inicial.
Depois de identificar os registros que terão o campo personalizado, você identifica os dados que deseja extrair.  Log Analytics usará essas informações para identificar padrões semelhantes em registros semelhantes.  Na etapa depois disso, você poderá validar os resultados e fornecer mais detalhes para Log Analytics usar em sua análise.

1. Realce o texto no registro de exemplo para o qual você deseja preencher o campo personalizado.  Em seguida, você verá uma caixa de diálogo para fornecer um nome e um tipo de dados para o campo e para executar a extração inicial.  Os caracteres  **\_CF** serão anexados automaticamente.
2. Clique em **extrair** para executar uma análise dos registros coletados.  
3. As seções **Resumo** e **resultados da pesquisa** exibem os resultados da extração para que você possa inspecionar sua precisão.  **Resumo** exibe os critérios usados para identificar os registros e uma contagem para cada um dos valores de dados identificados.  **Resultados da pesquisa** fornece uma lista detalhada de registros que correspondem aos critérios.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Etapa 3 – verificar a precisão do campo personalizado de extração e criação
Depois de executar a extração inicial, Log Analytics exibirá seus resultados com base nos dados que já foram coletados.  Se os resultados parecerem precisos, você poderá criar o campo personalizado sem nenhum trabalho adicional.  Caso contrário, você pode refinar os resultados para que Log Analytics possa melhorar sua lógica.

1. Se qualquer valor na extração inicial não estiver correto, clique no ícone de **edição** ao lado de um registro impreciso e selecione **modificar este realce** para modificar a seleção.
2. A entrada é copiada para a seção **exemplos adicionais** abaixo do **exemplo principal**.  Você pode ajustar o realce aqui para ajudar Log Analytics entender a seleção que deveria ter feito.
3. Clique em **extrair** para usar essas novas informações para avaliar todos os registros existentes.  Os resultados podem ser modificados para registros diferentes daquele que você acabou de modificar com base nessa nova inteligência.
4. Continue a adicionar correções até que todos os registros na extração identifiquem corretamente os dados para preencher o novo campo personalizado.
5. Clique em **salvar extração** quando estiver satisfeito com os resultados.  O campo personalizado agora está definido, mas ele não será adicionado a nenhum registro ainda.
6. Aguarde até que novos registros correspondentes aos critérios especificados sejam coletados e execute a pesquisa de log novamente. Os novos registros devem ter o campo personalizado.
7. Use o campo personalizado como qualquer outra propriedade de registro.  Você pode usá-lo para agregar e agrupar dados e até mesmo usá-lo para produzir novas informações.

## <a name="viewing-custom-fields"></a>Exibindo campos personalizados
Você pode exibir uma lista de todos os campos personalizados no grupo de gerenciamento no menu **Configurações avançadas** do espaço de trabalho Log Analytics no portal do Azure.  Selecione **dados** e **campos personalizados** para obter uma lista de todos os campos personalizados em seu espaço de trabalho.  

![Campos Personalizados](media/custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Removendo um campo personalizado
Há duas maneiras de remover um campo personalizado.  A primeira é a opção **remover** para cada campo ao exibir a lista completa, conforme descrito acima.  O outro método é recuperar um registro e clicar no botão à esquerda do campo.  O menu terá uma opção para remover o campo personalizado.

## <a name="sample-walkthrough"></a>Instruções de exemplo
A seção a seguir percorre um exemplo completo de criação de um campo personalizado.  Este exemplo extrai o nome do serviço em eventos do Windows que indicam um estado de mudança de serviço.  Isso se baseia em eventos criados pelo Gerenciador de controle de serviço durante a inicialização do sistema em computadores Windows.  Se desejar seguir este exemplo, você deve [coletar eventos de informações para o log do sistema](data-sources-windows-events.md).

Inserimos a consulta a seguir para retornar todos os eventos do Gerenciador de controle de serviço que têm uma ID de evento de 7036, que é o evento que indica um serviço iniciando ou parando.

![Consulta](media/custom-fields/query.png)

Em seguida, selecionamos e expandimos qualquer registro com a ID de evento 7036.

![Registro de origem](media/custom-fields/source-record.png)

Definimos campos personalizados clicando na elipse ao lado da propriedade Top.

![Extrair campos](media/custom-fields/extract-fields.png)

O **Assistente** de extração de campo é aberto e os campos **EventLog** e **EventID** são selecionados na coluna **exemplo principal** .  Isso indica que o campo personalizado será definido para eventos do log do sistema com uma ID de evento de 7036.  Isso é suficiente para que não seja necessário selecionar nenhum outro campo.

![Exemplo principal](media/custom-fields/main-example.png)

Realçamos o nome do serviço na propriedade **RenderedDescription** e usamos **Service** para identificar o nome do serviço.  O campo personalizado será chamado de **Service_CF**. O tipo de campo, nesse caso, é uma cadeia de caracteres, portanto, podemos deixar isso inalterado.

![Título do campo](media/custom-fields/field-title.png)

Vemos que o nome do serviço é identificado corretamente para alguns registros, mas não para outros.   Os **resultados da pesquisa** mostram que parte do nome do **adaptador de desempenho WMI** não foi selecionada.  O **Resumo** mostra que um registro identificou o **instalador de módulos** em vez do **instalador de módulos do Windows**.  

![Resultados da pesquisa](media/custom-fields/search-results-01.png)

Começamos com o registro do **adaptador de desempenho WMI** .  Clicamos no ícone de edição e **modificamos esse realce**.  

![Modificar realce](media/custom-fields/modify-highlight.png)

Aumentamos o realce para incluir a palavra **WMI** e, em seguida, executamos novamente a extração.  

![Exemplo adicional](media/custom-fields/additional-example-01.png)

Podemos ver que as entradas do **adaptador de desempenho WMI** foram corrigidas e log Analytics também usou essas informações para corrigir os registros do **instalador de módulo do Windows**.

![Resultados da pesquisa](media/custom-fields/search-results-02.png)

Agora podemos executar uma consulta que verifica se **Service_CF** foi criado, mas ainda não foi adicionado a nenhum registro. Isso ocorre porque o campo personalizado não funciona com registros existentes, portanto, precisamos aguardar a coleta de novos registros.

![Contagem inicial](media/custom-fields/initial-count.png)

Depois de algum tempo ter passado para que novos eventos sejam coletados, podemos ver que o campo **Service_CF** agora está sendo adicionado aos registros que correspondem aos nossos critérios.

![Resultados finais](media/custom-fields/final-results.png)

Agora podemos usar o campo personalizado como qualquer outra propriedade de registro.  Para ilustrar isso, criamos uma consulta que agrupa pelo novo campo **Service_CF** para inspecionar quais serviços são os mais ativos.

![Agrupar por consulta](media/custom-fields/query-group.png)

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [consultas de log](../log-query/log-query-overview.md) para criar consultas usando campos personalizados para critérios.
* Monitore [arquivos de log personalizados](data-sources-custom-logs.md) que você analisa usando campos personalizados.

