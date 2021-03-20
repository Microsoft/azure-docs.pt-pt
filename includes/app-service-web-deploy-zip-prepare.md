---
title: incluir ficheiro
description: incluir ficheiro
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/14/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 035399924216434de85865102db8838ea3fa15a3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85570175"
---
## <a name="create-a-project-zip-file"></a>Criar um ficheiro ZIP do projeto

>[!NOTE]
> Se descarregou os ficheiros num ficheiro ZIP, extraia os ficheiros primeiro. Por exemplo, se descarregou um ficheiro ZIP do GitHub, não poderá implementar esse ficheiro como está. O GitHub adiciona diretórios aninhados adicionais, que não funcionam com o Serviço de Aplicações. 
>

Numa janela de terminal local, navegue para o diretório de raiz do seu projeto de aplicações. 

Este diretório deve conter o ficheiro de entrada na sua aplicação web, como _index.html,_ _índice.php_ e _app.js_. Também pode conter ficheiros de gestão de pacotes como _project.jsem_ _,composer.js_ em , _package.js_ on , _bower.json_, e _requirements.txt_.

A menos que pretenda que o Serviço de Aplicações execute a automatização de implementação para si, execute todas as tarefas de construção (por exemplo, `npm` `bower` , e `gulp` `composer` `pip` certifique-se de que tem todos os ficheiros necessários para executar a aplicação. Este passo é necessário se quiser [executar a sua encomenda diretamente](../articles/app-service/deploy-run-package.md).

Crie um arquivo ZIP de tudo no seu projeto. Para `dotnet` os projetos, esta pasta é a pasta de saída do `dotnet publish` comando. O comando seguinte utiliza a ferramenta predefinida no seu terminal:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

