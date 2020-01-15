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
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945169"
---
## <a name="create-a-project-zip-file"></a>Criar um ficheiro ZIP do projeto

>[!NOTE]
> Se você baixou os arquivos em um arquivo ZIP, extraia os arquivos primeiro. Por exemplo, se você baixou um arquivo ZIP do GitHub, não poderá implantar esse arquivo no estado em que se encontra. O GitHub adiciona diretórios aninhados adicionais, que não funcionam com o serviço de aplicativo. 
>

Em uma janela de terminal local, navegue até o diretório raiz do seu projeto de aplicativo. 

Esse diretório deve conter o arquivo de entrada para seu aplicativo Web, como _index. html_, _index. php_e _app. js_. Ele também pode conter arquivos de gerenciamento de pacotes como _Project. JSON_, _Composer. JSON_, _Package. JSON_, _Bower. JSON_e _requirements. txt_.

A menos que você queira que o serviço de aplicativo execute a automação de implantação para você, execute todas as tarefas de compilação (por exemplo, `npm`, `bower`, `gulp`, `composer`e `pip`) e certifique-se de ter todos os arquivos necessários para executar o aplicativo. Esta etapa será necessária se você quiser [executar o pacote diretamente](../articles/app-service/deploy-run-package.md).

Crie um arquivo ZIP de tudo no seu projeto. O comando seguinte utiliza a ferramenta predefinida no seu terminal:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

