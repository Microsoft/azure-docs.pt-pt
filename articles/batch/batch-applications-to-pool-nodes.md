---
title: Copiar aplicações e dados para juntar os nosdes
description: Saiba como copiar aplicações e dados para juntar nós.
ms.topic: how-to
ms.date: 02/17/2020
ms.openlocfilehash: e21b8551fb62c4335910fd05bb9590eaf6f7e35a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85954898"
---
# <a name="copy-applications-and-data-to-pool-nodes"></a>Copiar aplicações e dados para juntar os nosdes

O Azure Batch suporta várias formas de obter dados e aplicações nos nós computativos para que os dados e aplicações estejam disponíveis para uso por tarefas. Os dados e aplicações podem ser necessários para executar todo o trabalho, pelo que devem ser instalados em todos os nós. Alguns podem ser necessários apenas para uma tarefa específica, ou precisam de ser instalados para o trabalho, mas não precisam estar em todos os nós. O lote tem ferramentas para cada um destes cenários.

- **Fichas de recursos de tarefa de início**de piscina : Para aplicações ou dados que precisam de ser instalados em todos os nós do pool. Utilize este método juntamente com um pacote de aplicações ou a recolha de ficheiros de recursos da tarefa inicial para executar um comando de instalação.  

Exemplos: 
- Utilize a linha de comando de tarefa inicial para mover ou instalar aplicações

- Especifique uma lista de ficheiros ou recipientes específicos numa conta de armazenamento Azure. Para mais informações consulte [add#resourcefile na documentação REST](/rest/api/batchservice/pool/add#resourcefile)

- Cada trabalho que corre na piscina funciona MyApplication.exe que primeiro deve ser instalado com MyApplication.msi. Se utilizar este mecanismo, tem de definir a espera da tarefa inicial para que a propriedade de **sucesso** seja **verdadeira**. Para obter mais informações, consulte o [add#starttask na documentação REST](/rest/api/batchservice/pool/add#starttask).

- **Referências** do pacote de aplicação no pool: Para aplicações ou dados que precisam de ser instalados em todos os nós da piscina. Não existe um comando de instalação associado a um pacote de aplicações, mas pode utilizar uma tarefa inicial para executar qualquer comando de instalação. Se a sua aplicação não necessitar de instalação, ou consistir num grande número de ficheiros, pode utilizar este método. Os pacotes de aplicações são adequados para um grande número de ficheiros porque combinam um grande número de referências de ficheiros numa pequena carga útil. Se tentar incluir mais de 100 ficheiros de recursos separados numa única tarefa, o serviço Batch poderá encontrar limitações internas do sistema para uma única tarefa. Além disso, utilize pacotes de aplicações se tiver requisitos de versão rigorosos onde poderá ter muitas versões diferentes da mesma aplicação e precisar de escolher entre eles. Para obter mais informações, leia [implementar aplicações para calcular nós com pacotes de aplicações batch](./batch-application-packages.md).

- **Ficheiros de recursos de tarefa de preparação**de trabalho : Para aplicações ou dados que devem ser instalados para o trabalho funcionar, mas não precisam de ser instalados em toda a piscina. Por exemplo: se a sua piscina tem muitos tipos de empregos diferentes, e apenas um tipo de trabalho precisa de MyApplication.msi para executar, faz sentido colocar a instalação numa tarefa de preparação de emprego. Para obter mais informações sobre tarefas de preparação de emprego, consulte [tarefas de preparação de emprego e de libertação de emprego nos nós de cálculo batch](./batch-job-prep-release.md).

- **Ficheiros de recursos de**tarefa : Para quando uma aplicação ou dados é relevante apenas para uma tarefa individual. Por exemplo: Tem cinco tarefas, cada um processa um ficheiro diferente e, em seguida, escreve a saída para o armazenamento de bolhas.  Neste caso, o ficheiro de entrada deve ser especificado na recolha de **ficheiros de recursos** de tarefas porque cada tarefa tem o seu próprio ficheiro de entrada.

## <a name="determine-the-scope-required-of-a-file"></a>Determinar o âmbito exigido de um ficheiro

Você precisa determinar o âmbito de um arquivo - é o arquivo necessário para uma piscina, um trabalho ou uma tarefa. Os ficheiros que são examinados na piscina devem usar pacotes de aplicação de piscina ou uma tarefa inicial. Os ficheiros a serem examinados para o trabalho devem utilizar uma tarefa de preparação de emprego. Um bom exemplo de ficheiros analisados no pool ou nível de emprego são aplicações. Os ficheiros a serem examinados para a tarefa devem utilizar ficheiros de recursos de tarefa.

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>Outras formas de obter dados sobre os nó de computação batch

Existem outras formas de obter dados sobre os nós de computação batch que não estão oficialmente integrados na API do Lote REST. Como tem controlo sobre os nós do Azure Batch e pode executar executáveis personalizados, é capaz de extrair dados de qualquer número de fontes personalizadas, desde que o nó de Lote tenha conectividade com o alvo e tenha as credenciais dessa fonte no nó do Lote Azure. Alguns exemplos comuns são:

- Descarregar dados do SQL
- Descarregar dados de outros serviços web/localizações personalizadas
- Mapear uma partilha de rede

### <a name="azure-storage"></a>Storage do Azure

O armazenamento blob tem alvos de escalabilidade de download. Os alvos de escalabilidade da partilha de ficheiros de armazenamento Azure são os mesmos que para uma única bolha. O tamanho irá afetar o número de nós e piscinas que você precisa.

