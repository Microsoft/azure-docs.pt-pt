---
title: Copiar aplicações e dados para juntar os nosdes
description: Saiba como copiar aplicações e dados para juntar nós.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: a5933a1c52e2848b6b414f1750bb24515fb9f28a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378507"
---
# <a name="copy-applications-and-data-to-pool-nodes"></a>Copiar aplicações e dados para juntar os nosdes

O Azure Batch suporta várias formas de obter dados e aplicações em nós computativos para que estejam disponíveis para serem usados por tarefas.

O método que escolher pode depender do âmbito do seu ficheiro ou aplicação. Os dados e aplicações podem ser necessários para executar todo o trabalho, pelo que precisam de ser instalados em todos os nós. Alguns ficheiros ou aplicações podem ser necessários apenas para uma tarefa específica. Outros podem ter de ser instalados para o trabalho, mas não precisam de estar em todos os nós. O lote tem ferramentas para cada um destes cenários.

## <a name="determine-the-scope-required-of-a-file"></a>Determinar o âmbito exigido de um ficheiro

Você precisa determinar o âmbito de um arquivo - é o arquivo necessário para uma piscina, um trabalho ou uma tarefa. Os ficheiros que são examinados na piscina devem usar pacotes de aplicação de piscina ou uma tarefa inicial. Os ficheiros a serem examinados para o trabalho devem utilizar uma tarefa de preparação de emprego. Um bom exemplo de ficheiros analisados no pool ou nível de emprego são aplicações. Os ficheiros a serem examinados para a tarefa devem utilizar ficheiros de recursos de tarefa.

## <a name="pool-start-task-resource-files"></a>Pool iniciar ficheiros de recursos de tarefa

Para aplicações ou dados que precisam de ser instalados em todos os nós da piscina, utilize ficheiros de recursos de tarefa de início de piscina. Utilize este método juntamente com um [pacote de aplicações](batch-application-packages.md) ou a recolha de ficheiros de recursos da tarefa inicial para executar um comando de instalação.  

Por exemplo, pode utilizar a linha de comando de tarefa inicial para mover ou instalar aplicações. Também pode especificar uma lista de ficheiros ou contentores numa conta de armazenamento Azure. Para obter mais informações, consulte [Add#ResourceFile na documentação REST](/rest/api/batchservice/pool/add#resourcefile).

Se cada trabalho que funciona na piscina executa uma aplicação (.exe) que deve ser instalada primeiro com um ficheiro .msi, terá de definir a espera da tarefa inicial para que a propriedade de **sucesso** seja **verdadeira.** Para obter mais informações, consulte [Add#StartTask na documentação REST](/rest/api/batchservice/pool/add#starttask).

## <a name="application-package-references"></a>Referências de pacote de aplicação

Para aplicações ou dados que precisam de ser instalados em todos os nós da piscina, considere a utilização de [pacotes de aplicações](batch-application-packages.md). Não existe um comando de instalação associado a um pacote de aplicações, mas pode utilizar uma tarefa inicial para executar qualquer comando de instalação. Se a sua aplicação não necessitar de instalação, ou consistir num grande número de ficheiros, pode utilizar este método.

Os pacotes de aplicações são úteis quando você tem um grande número de ficheiros, porque eles podem combinar muitas referências de arquivo em uma pequena carga útil. Se tentar incluir mais de 100 ficheiros de recursos separados numa única tarefa, o serviço Batch poderá encontrar limitações internas do sistema para uma única tarefa. Os pacotes de aplicações também são úteis quando você tem muitas versões diferentes da mesma aplicação e precisa escolher entre eles.

## <a name="extensions"></a>Extensões

[As extensões](create-pool-extensions.md) são pequenas aplicações que facilitam a configuração pós-provisão e a configuração nos nós de computação batch. Quando criar uma piscina, pode selecionar uma extensão suportada para ser instalada nos nós de computação à medida que forem a provisionadas. Depois disso, a extensão pode executar o funcionamento pretendido.

## <a name="job-preparation-task-resource-files"></a>Ficheiros de recursos de tarefa de preparação de emprego

Para aplicações ou dados que devem ser instalados para o trabalho funcionar, mas não precisam de ser instalados em toda a piscina, considere usar [ficheiros de recursos de tarefa de preparação de emprego](./batch-job-prep-release.md).

Por exemplo, se a sua piscina tem muitos tipos diferentes de empregos, e apenas um tipo de trabalho precisa de um ficheiro .msi para funcionar, faz sentido colocar a instalação numa tarefa de preparação de emprego.

## <a name="task-resource-files"></a>Ficheiros de recursos de tarefa

Os ficheiros de recursos de tarefa são adequados quando a sua aplicação ou dados são relevantes apenas para uma tarefa individual.

Por exemplo, pode ter cinco tarefas, cada um processando um ficheiro diferente e, em seguida, escrever a saída para o armazenamento de bolhas Neste caso, o ficheiro de entrada deve ser especificado na recolha de ficheiros de recursos de tarefa, porque cada tarefa tem o seu próprio ficheiro de entrada.

## <a name="additional-ways-to-get-data-onto-nodes"></a>Formas adicionais de obter dados em nódos

Como tem controlo sobre os nós do Azure Batch e pode executar executáveis personalizados, pode retirar dados de qualquer número de fontes personalizadas. Certifique-se de que o nó do Lote tem conectividade com o alvo e que tem credenciais para essa fonte no nó.

Alguns exemplos de formas de transferir dados para os nóns de Lote são:

- Descarregar dados do SQL
- Descarregar dados de outros serviços web/localizações personalizadas
- Mapear uma partilha de rede

## <a name="azure-storage"></a>Storage do Azure

Tenha em mente que o armazenamento de blob tem alvos de escalabilidade de descarregamento. Os alvos de escalabilidade da partilha de ficheiros de armazenamento Azure são os mesmos que para uma única bolha. O tamanho irá afetar o número de nós e piscinas que você precisa.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [utilização de pacotes de aplicações com o Batch.](batch-application-packages.md)
- Saiba mais sobre [trabalhar com nós e piscinas.](nodes-and-pools.md)
