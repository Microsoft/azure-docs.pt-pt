---
title: Visão geral do tempo de funcionamento das funções azure
description: Visão geral da pré-visualização do tempo de funcionamento das funções azure
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: ab04aa4ca7f54e8de120d078a313c3096a350aa5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74226634"
---
# <a name="azure-functions-runtime-overview-preview"></a>Visão geral do tempo de funcionamento das funções azure (pré-visualização)

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

O Tempo de Funcionamento das Funções Azure (pré-visualização) fornece uma nova forma de tirar partido da simplicidade e flexibilidade do modelo de programação de Funções Azure no local. Construído sobre as mesmas raízes de código aberto que as Funções Azure, o Funcionamento das Funções Azure é implantado no local para proporcionar uma experiência de desenvolvimento quase idêntica como o serviço de nuvem.

![Portal de pré-visualização de funções azure][1]

O Tempo de Funcionamento das Funções Azure proporciona uma forma de experimentar as Funções Azure antes de se comprometer com a nuvem. Desta forma, os ativos de código que constrói podem ser levados consigo para a nuvem quando migrarem.  O tempo de execução também abre novas opções para si, como por exemplo usar a potência de computação sobressalente dos seus computadores no local para executar processos de lote durante a noite. Também pode utilizar dispositivos dentro da sua organização para enviar dados condicionalmente para outros sistemas, tanto no local como na nuvem.

O Tempo de Funcionamento das Funções Azure é composto por duas peças:

* Funções Azure Funções Funções Funções Funções Funções Função Runtime Management
* Funções Azure Funções Funções Funções Funções Funções Funções Funções Funções Funções Funções Funções

## <a name="azure-functions-management-role"></a>Função de Gestão de Funções Azure

A Função de Gestão de Funções Azure fornece um anfitrião para a gestão das suas Funções no local. Este papel desempenha as seguintes tarefas:

* Hospedagem do Portal de Gestão de Funções Azure, que é o mesmo que se vê no [portal Azure.](https://portal.azure.com) O portal proporciona uma experiência consistente que permite desenvolver as suas funções da mesma forma que no portal Azure.
* Distribuir funções em vários trabalhadores de Funções.
* Fornecendo um ponto final de publicação para que possa publicar as suas funções diretamente do Microsoft Visual Studio, baixando e importando o perfil editorial.

## <a name="azure-functions-worker-role"></a>Funções Azure Funções Funções Funções Funções Funções Funções Funções Funções

As funções azure Funções Funções Funções Funções Funções Funções Funções Funções Funções Funções Funções Funções Funções São implantadas em Recipientes windows e são onde o seu código de função executa.  Você pode implementar vários Papéis de Trabalhador em toda a sua organização e esta opção é uma forma chave de os clientes poderem utilizar o poder de computação sobressalente.  Um exemplo de onde existe computação sobressalente em muitas organizações é máquinas alimentadas constantemente, mas não sendo usadas por grandes períodos de tempo.

## <a name="minimum-requirements"></a>Requisitos Mínimos

Para começar com o Tempo de Funcionamento das Funções Azure, tem de ter uma máquina com Windows Server 2016 ou Windows 10 Creators Update com acesso a uma instância do Servidor SQL.

## <a name="next-steps"></a>Passos Seguintes

Instale a [pré-visualização do tempo](https://aka.ms/azafrdoc) de funcionamento das funções Azure

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
