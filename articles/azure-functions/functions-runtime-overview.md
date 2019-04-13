---
title: Descrição geral do Runtime das funções do Azure | Documentos da Microsoft
description: Descrição geral da pré-visualização de tempo de execução das funções do Azure
services: functions
author: apwestgarth
manager: stefsch
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 2af9575c50ee522d6330ddf46c75b666132b7a84
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59546838"
---
# <a name="azure-functions-runtime-overview-preview"></a>Descrição geral de tempo de execução do Azure das funções (pré-visualização)

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

O tempo de execução de funções do Azure (pré-visualização) fornece uma nova forma para que possa tirar partido da simplicidade e a flexibilidade de funções do Azure no local do modelo de programação. Criado sobre as mesmas raízes de código-fonte aberto como as funções do Azure, o Runtime de funções do Azure é implementado no local para proporcionar uma experiência de desenvolvimento quase idêntico, como o serviço em nuvem.

![Portal de pré-visualização do tempo de execução de funções do Azure][1]

O Runtime das funções do Azure fornece uma maneira de experimentar as funções do Azure antes de consolidar para a cloud. Dessa forma, os ativos de código que cria, em seguida, efetuar com para a nuvem ao migrar.  O tempo de execução também abre novas opções para, por exemplo, usando o poder de computação livre dos computadores no local para executar processos em lote durante a noite. Também pode utilizar dispositivos na sua organização para enviar condicionalmente os dados para outros sistemas, tanto no local e na cloud.

O Runtime das funções do Azure consiste em duas partes:

* Função de gerenciamento de tempo de execução de funções do Azure
* Função de trabalho de tempo de execução das funções do Azure

## <a name="azure-functions-management-role"></a>Função de gestão de funções do Azure

A função de gestão de funções do Azure fornece um anfitrião para o gerenciamento de funções no local. Esta função executa as seguintes tarefas:

* A alojar o Portal de gestão de funções do Azure, que é o mesmo que pode ver na [portal do Azure](https://portal.azure.com). O portal fornece uma experiência consistente que lhe permitem desenvolver as suas funções da mesma forma, tal como faria no portal do Azure.
* Distribui as funções em vários operadores de funções.
* Fornecer um ponto de final de publicação para que pode publicar sua diretamente de funções no Microsoft Visual Studio, transferir e importar o perfil de publicação.

## <a name="azure-functions-worker-role"></a>Função de trabalho de funções do Azure

As funções de trabalho de funções do Azure são implementadas nos contentores do Windows e estão em que executa o código da função.  Pode implementar várias funções de trabalho em toda a organização e esta opção é uma forma chave na qual os clientes podem tomar utilizar o poder de computação de reserva.  Um exemplo de onde os componentes de reserva computação existe em muitas organizações é máquinas ligadas constantemente, mas não a ser utilizado por grandes períodos de tempo.

## <a name="minimum-requirements"></a>Requisitos mínimos

Para começar a utilizar com o Runtime das funções do Azure, tem de ter uma máquina com Windows Server 2016 ou Windows 10 Creators Update com acesso a uma instância do SQL Server.

## <a name="next-steps"></a>Próximos Passos

Instalar o [pré-visualização do Runtime de funções do Azure](https://aka.ms/azafrdoc)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
