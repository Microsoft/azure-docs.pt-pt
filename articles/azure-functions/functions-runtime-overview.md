---
title: Visão geral do tempo de execução das funções Azure
description: Visão geral da pré-visualização do tempo de execução das funções Azure
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: ab04aa4ca7f54e8de120d078a313c3096a350aa5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74226634"
---
# <a name="azure-functions-runtime-overview-preview"></a>Visão geral do tempo de execução das funções Azure (pré-visualização)

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

O Tempo de Execução de Funções Azure (pré-visualização) proporciona uma nova forma de tirar partido da simplicidade e flexibilidade do modelo de programação Azure Functions no local. Construído com base nas mesmas raízes de código aberto que as Funções Azure, o Tempo de Execução das Funções Azure é implantado no local para proporcionar uma experiência de desenvolvimento quase idêntica como o serviço de nuvem.

![Portal de pré-visualização de tempo de execução de funções Azure Functions][1]

O tempo de execução das funções Azure proporciona uma forma de experimentar funções Azure antes de se comprometer com a nuvem. Desta forma, os ativos de código que constrói podem ser levados consigo para a nuvem quando migrar.  O tempo de funcionamento também abre novas opções para si, como utilizar o poder de computação sobressalente dos seus computadores no local para executar processos de lote durante a noite. Também pode utilizar dispositivos dentro da sua organização para enviar dados condicionalmente para outros sistemas, tanto no local como na nuvem.

O tempo de execução das funções Azure é composto por duas peças:

* Funções Azure Funções Funções Função Gestão de Tempo de Execução
* Funções Azure Funções Funções Função de Trabalhador de Tempo de Execução

## <a name="azure-functions-management-role"></a>Funções Azure Funções Papel de Gestão

A Função de Gestão de Funções Azure fornece um anfitrião para a gestão das suas Funções no local. Esta função desempenha as seguintes tarefas:

* Hospedagem do Portal de Gestão de Funções Azure, que é o mesmo que se vê no [portal Azure.](https://portal.azure.com) O portal proporciona uma experiência consistente que lhe permite desenvolver as suas funções da mesma forma que no portal Azure.
* Distribuição de funções em vários trabalhadores de Funções.
* Fornecendo um ponto final de publicação para que possa publicar as suas funções diretamente a partir do Microsoft Visual Studio, descarregando e importando o perfil de publicação.

## <a name="azure-functions-worker-role"></a>Funções Azure Funções Papel de Trabalhador

As funções de trabalhador do Azure Functions são implantadas em recipientes windows e são onde o seu código de função executa.  Você pode implementar várias Funções de Trabalhador em toda a sua organização e esta opção é uma forma chave para os clientes poderem utilizar o poder de computação sobressalente.  Um exemplo de onde existe o cálculo sobressalente em muitas organizações são as máquinas alimentadas constantemente, mas não sendo usadas por grandes períodos de tempo.

## <a name="minimum-requirements"></a>Requisitos Mínimos

Para começar com o Tempo de Execução de Funções Azure, tem de ter uma máquina com o Windows Server 2016 ou o Windows 10 Creators Update com acesso a uma instância do SQL Server.

## <a name="next-steps"></a>Passos Seguintes

Instale a [pré-visualização do tempo de execução de funções Azure](https://aka.ms/azafrdoc)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
