---
title: Introdução ao armazenamento do Azure Stack
description: Saiba mais sobre armazenamento do Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: 092aba28-04bc-44c0-90e1-e79d82f4ff42
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: d31066e52e8c8f7856cce5651ae7e491029f328b
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57782467"
---
# <a name="introduction-to-azure-stack-storage"></a>Introdução ao armazenamento do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

## <a name="overview"></a>Descrição geral

O armazenamento do Azure Stack é um conjunto de serviços de armazenamento na cloud que inclui a Blobs, tabelas e filas que estão em conformidade com os serviços de armazenamento do Azure.

## <a name="azure-stack-storage-services"></a>Serviços de pilha de armazenamento do Azure

O armazenamento do Azure Stack fornece os seguintes três serviços:

- **Armazenamento de Blobs**

    Armazenamento de BLOBs armazena os dados de objetos não estruturados. Um blob pode ser qualquer tipo de texto ou de dados binários, tal como um documento, um ficheiro multimédia ou o instalador da aplicação.

- **Armazenamento de tabelas**

    O Table storage armazena conjuntos de dados estruturados. O Table Storage é um arquivo de dados do atributo chave NoSQL, que permite um rápido desenvolvimento e um acesso rápido às grandes quantidades de dados.

- **Armazenamento de filas**

    Armazenamento de filas fornece mensagens fiáveis para processamento de fluxo de trabalho e para a comunicação entre os componentes dos serviços cloud.

Uma conta de armazenamento do Azure Stack é uma conta segura que lhe dá acesso a serviços no Storage do Azure Stack. A conta do Storage fornece o espaço de nomes exclusivo para os seus recursos de armazenamento. O diagrama seguinte mostra as relações entre os recursos de armazenamento do Azure Stack numa conta de armazenamento:

![Descrição geral de pilha de armazenamento do Azure](media/azure-stack-storage-overview/AzureStackStorageOverview.png)

### <a name="blob-storage"></a>Armazenamento de blobs

Para os utilizadores com uma grande quantidade de dados de objetos não estruturados armazenar na cloud, armazenamento de BLOBs oferece uma solução eficiente e dimensionável. Pode utilizar o armazenamento de BLOBs para armazenar o conteúdo, tais como:

- Documentos
- Dados de rede social, tais como fotografias, vídeos, música e blogues
- Cópias de segurança de ficheiros, computadores, bases de dados e dispositivos
- Imagens e texto para Web Apps
- Dados de configuração para aplicações em nuvem
- Macrodados, tais como registos e outros grandes conjuntos de dados

Cada blob está organizado num contentor. Os contentores também fornecem uma forma útil de atribuir políticas de segurança a grupos de objetos. Uma conta de armazenamento pode conter qualquer número de contentores e um contentor pode conter qualquer número de blobs, até ao limite da conta de armazenamento.

Armazenamento de BLOBs oferece três tipos de blobs:

- **Blobs de blocos**

    Blobs de blocos estão otimizados para transmissão em fluxo e o armazenamento de objetos da nuvem e são uma boa opção para armazenar documentos, ficheiros de multimédia, cópias de segurança e etc.

- **Blobs de acréscimo**

    Os blobs de acréscimo são semelhantes aos blobs de blocos, mas estão otimizados para as operações de acréscimo. Um blob de acréscimo apenas pode ser atualizado adicionando um novo bloco ao final. Os blobs de acréscimo são uma boa opção para cenários como o registo, onde novos dados têm de ser escritos apenas no final do blob.

- **Blobs de páginas**

    Blobs de páginas são otimizados para representar discos IaaS e suportar aleatório escreve é até 1 TB de tamanho. IaaS disco é um VHD armazenado como um blob de página de anexado a uma máquina virtual do Azure Stack.

### <a name="table-storage"></a>Armazenamento de tabela

As aplicações modernas exigem frequentemente arquivos de dados com maior escalabilidade e flexibilidade do que as gerações anteriores do software necessário. O Table Storage oferece um armazenamento de elevada disponibilidade e extremamente dimensionável para que a aplicação possa ser automaticamente dimensionada para satisfazer o pedido do utilizador. O Table storage é o arquivo de chaves/atributos NoSQL da Microsoft – tem um design sem esquemas, que o diferencia das tradicionais bases de dados relacionais. Com um arquivo de dados sem esquemas, é fácil adaptar os seus dados à medida que as necessidades da sua aplicação evoluem. O Table Storage é fácil de utilizar para que os programadores possam criar aplicações rapidamente.

O Table storage é um arquivo de atributos de chave, o que significa que cada valor numa tabela é armazenado com um nome de propriedade escrito. O nome da propriedade pode ser utilizado para filtrar e especificar critérios de seleção. Uma coleção de propriedades e os respetivos valores compõem uma entidade. Uma vez que o armazenamento de tabelas não tem esquema, duas entidades na mesma tabela podem conter diferentes coleções de propriedades e essas propriedades podem ser de diferentes tipos.

Pode utilizar o armazenamento de tabela para armazenar conjuntos de dados flexíveis, tais como os dados de utilizador para aplicações web, livros de endereços, informações do dispositivo e qualquer outro tipo de metadados que seu serviço requer. Para aplicativos baseados na Internet de hoje em dia, bases de dados NoSQL, como o armazenamento de tabela oferecem uma alternativa popular às bases de dados relacionais tradicionais.

Uma conta de armazenamento pode conter qualquer número de tabelas e uma tabela pode conter qualquer número de entidades, até ao limite de capacidade da conta de armazenamento.

### <a name="queue-storage"></a>Armazenamento de filas

Ao conceber aplicações para o dimensionamento, os componentes da aplicação, muitas vezes, são desacoplados para um dimensionamento independente. Armazenamento de filas fornece uma solução de mensagens fiável para comunicação assíncrona entre componentes da aplicação, quer sejam executadas na cloud, na área de trabalho, num servidor no local ou num dispositivo móvel. O Armazenamento de filas também suporta a gestão das tarefas assíncronas e a criação de fluxos de trabalho do processo.

Uma conta de armazenamento pode conter qualquer número de filas e uma fila pode conter qualquer número de mensagens, até ao limite de capacidade da conta de armazenamento. As mensagens individuais podem ter até 64 KB de tamanho.

## <a name="next-steps"></a>Passos Seguintes

- [Armazenamento consistentes do Azure: diferenças e considerações](azure-stack-acs-differences.md)

- Para saber mais sobre o armazenamento do Azure, veja [introdução ao armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md)
