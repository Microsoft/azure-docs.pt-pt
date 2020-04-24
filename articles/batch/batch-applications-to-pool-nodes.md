---
title: Copiar aplicações e dados para conjuntos de nós
description: Saiba como copiar aplicações e dados para juntar os nódosos.
ms.topic: article
ms.date: 02/17/2020
ms.openlocfilehash: 700e9b80f8420266c0300b47bdd30bc271f8421c
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115589"
---
# <a name="copying-applications-and-data-to-pool-nodes"></a>Copiar aplicações e dados para conjuntos de nós

O Azure Batch suporta várias formas de obter dados e aplicações em nós de computação para que os dados e aplicações estejam disponíveis para uso através de tarefas. Os dados e aplicações podem ser necessários para executar todo o trabalho e, por isso, precisam de ser instalados em todos os nódosos. Alguns podem ser necessários apenas para uma tarefa específica, ou precisam de ser instalados para o trabalho, mas não precisam de estar em todos os nós. O lote tem ferramentas para cada um destes cenários.

- **Ficheiros**de recursos de tarefa de início de piscina : Para aplicações ou dados que precisam de ser instalados em todos os nós da piscina. Utilize este método juntamente com um pacote de aplicação ou a recolha de ficheiros de recursos da tarefa inicial para executar um comando de instalação.  

Exemplos: 
- Utilize a linha de comando de tarefa inicial para mover ou instalar aplicações

- Especifique uma lista de ficheiros ou contentores específicos numa conta de armazenamento Azure. Para mais informações consulte [adicionar#resourcefile na documentação REST](https://docs.microsoft.com/rest/api/batchservice/pool/add#resourcefile)

- Todos os trabalhos que correm na piscina executam MyApplication.exe que deve ser instalado primeiro com myApplication.msi. Se utilizar este mecanismo, precisa de definir a espera da tarefa inicial para que a propriedade de **sucesso** **seja verdadeira.** Para mais informações, consulte a [tarefa de início de adição em documentação REST](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask).

- **Referências** do pacote de aplicação na piscina: Para aplicações ou dados que precisam de ser instalados em todos os nós da piscina. Não existe um comando de instalação associado a um pacote de aplicação, mas pode utilizar uma tarefa de início para executar qualquer comando de instalação. Se a sua aplicação não necessitar de instalação, ou se for constituída por um grande número de ficheiros, pode utilizar este método. Os pacotes de aplicação são adequados para um grande número de ficheiros porque combinam um grande número de referências de ficheiros numa pequena carga útil. Se tentar incluir mais de 100 ficheiros de recursos separados numa única tarefa, o serviço Batch poderá enfrentar limitações internas do sistema para uma única tarefa. Além disso, utilize pacotes de aplicações se tiver requisitos de versão rigorosos onde poderá ter muitas versões diferentes da mesma aplicação e precisa escolher entre eles. Para mais informações, leia [Implementar aplicações para calcular nós com pacotes](https://docs.microsoft.com/azure/batch/batch-application-packages)de aplicação batch .

- **Ficheiros**de recursos de tarefa de preparação de trabalho : Para aplicações ou dados que devem ser instalados para o trabalho ser executado, mas não precisa de ser instalado em toda a piscina. Por exemplo: se a sua piscina tem muitos tipos diferentes de empregos, e apenas um tipo de trabalho precisa de MyApplication.msi para funcionar, faz sentido colocar o passo de instalação numa tarefa de preparação de emprego. Para obter mais informações sobre tarefas de preparação de emprego, consulte Executar tarefas de [preparação de emprego e de libertação](https://azure.microsoft.com/documentation/articles/batch-job-prep-release/)de emprego em nós de computação batch .

- **Ficheiros de recursos**de tarefas : Quando uma aplicação ou dados são relevantes apenas para uma tarefa individual. Por exemplo: Tem cinco tarefas, cada um processa um ficheiro diferente e, em seguida, escreve a saída para o armazenamento blob.  Neste caso, o ficheiro de entrada deve ser especificado na recolha de ficheiros de recursos de **tarefas,** uma vez que cada tarefa tem o seu próprio ficheiro de entrada.

## <a name="determine-the-scope-required-of-a-file"></a>Determine o âmbito exigido de um ficheiro

Você precisa determinar o âmbito de um arquivo - é o arquivo necessário para uma piscina, um trabalho, ou uma tarefa. Os ficheiros que são remetos para a piscina devem utilizar pacotes de aplicação de piscina, ou uma tarefa de início. Os ficheiros do trabalho devem utilizar uma tarefa de preparação de emprego. Um bom exemplo de ficheiros analisados na piscina ou nível de emprego são aplicações. Os ficheiros com recurso à tarefa devem utilizar ficheiros de recursos de tarefas.

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>Outras formas de obter dados para os nódosos computacionais do Batch

Existem outras formas de obter dados sobre os nós de computação batch que não estão oficialmente integrados na API do Lote REST. Como tem controlo sobre os nós do Lote Azure, e pode executar executáveis personalizados, é possível retirar dados de qualquer número de fontes personalizadas, desde que o nó do Lote tenha conectividade com o alvo e tenha as credenciais para essa fonte no nó do Lote Azure. Alguns exemplos comuns são:

- Descarregamento de dados da SQL
- Descarregamento de dados de outros serviços web/localizações personalizadas
- Mapear uma partilha de rede

### <a name="azure-storage"></a>Storage do Azure

O armazenamento blob tem alvos de escalabilidade de descarregamento. Os alvos de escalabilidade do ficheiro de armazenamento Azure são os mesmos que para uma única bolha. O tamanho vai afetar o número de nós e piscinas que você precisa.

