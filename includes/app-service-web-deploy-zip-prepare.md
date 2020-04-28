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
ms.openlocfilehash: 6b5aa4f409b8c2f5a9125ab01e8587f4ac9c4ee5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75945169"
---
## <a name="create-a-project-zip-file"></a>Criar um ficheiro ZIP do projeto

>[!NOTE]
> Se descarregou os ficheiros num ficheiro ZIP, extrai os ficheiros primeiro. Por exemplo, se descarregou um ficheiro ZIP do GitHub, não pode implementar esse ficheiro como está. O GitHub adiciona diretórios aninhados adicionais, que não funcionam com o App Service. 
>

Numa janela de terminal local, navegue para o diretório raiz do seu projeto de aplicação. 

Este diretório deve conter o ficheiro de entrada na sua aplicação web, tais como _index.html,_ _index.php_, e _app.js_. Também pode conter ficheiros de gestão de pacotes como _project.json_, _composer.json,_ _package.json,_ _bower.json_, e _requirements.txt_.

A menos que pretenda que o Serviço de Aplicações execute `npm` `bower`a `gulp` `composer`automatização de implementação para si, execute todas as tarefas de construção (por exemplo, , , e ) e `pip`certifique-se de que tem todos os ficheiros necessários para executar a app. Este passo é necessário se quiser [executar o seu pacote diretamente](../articles/app-service/deploy-run-package.md).

Crie um arquivo ZIP de tudo no seu projeto. O comando seguinte utiliza a ferramenta predefinida no seu terminal:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

