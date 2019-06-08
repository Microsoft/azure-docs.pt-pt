---
title: incluir ficheiro
description: incluir ficheiro
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/28/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: db431d7815cfcc006563bd6da438154ef77ae6e2
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66814888"
---
## <a name="cli-shell"></a>Shell CLI

É recomendado que utilize [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) para executar comandos da CLI. **Cloud Shell** é uma shell gratuita e interativa que pode utilizar para executar os passos neste artigo. As ferramentas comuns do Azure estão pré-instaladas e configuradas no Cloud Shell para que possa utilizá-las com a sua conta. Ele fornece a flexibilidade de escolher a experiência de shell mais adequada às estilo de que trabalho. Os utilizadores de Linux podem optar por uma experiência de Bash, ao passo que os utilizadores de Windows podem escolher o PowerShell.

Também pode instalar a CLI localmente. Ver [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) para obter instruções para a sua plataforma.

### <a name="sign-in"></a>Iniciar sessão

Utilizar uma instalação local da CLI necessita de iniciar sessão no Azure. Este passo não é necessário para o Azure Cloud Shell. Inicie sessão com o `az login` comando.

Se a CLI conseguir abrir o seu browser predefinido, executa essa ação e carrega uma página de início de sessão. Caso contrário, terá de abrir uma página do browser e siga as instruções na linha de comandos para introduzir um código de autorização depois de navegar para https://aka.ms/devicelogin no seu browser.

### <a name="specify-location-of-files"></a>Especifique a localização dos ficheiros

Muitos comandos da CLI de serviços de multimédia permitem-lhe transmitir um parâmetro com um nome de ficheiro. Se estiver a utilizar **Cloud Shell**, pode carregar o ficheiro para o seu clouddrive (com o Bash ou o PowerShell). 

![Carregar ficheiros]

Se estiver a utilizar uma CLI local ou **Cloud Shell**, tem de especificar o caminho do ficheiro, de acordo com o sistema operacional ou o Cloud Shell (Bash ou o PowerShell) que está a utilizar. Abaixo, encontram-se alguns exemplos:

Caminho relativo para o ficheiro (todos os SO)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Caminho absoluto do ficheiro no SO Windows e no Linux/Mac

* `@ "/usr/home/mytestfile.json"`
*   `@"c:\tmp\user\mytestfile.json"`

Utilize `{file}` se o comando pede para um caminho para o ficheiro. Por exemplo, `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`. <br/> Utilize `@{file}` se o comando irá carregar o ficheiro especificado. Por exemplo, `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`.

[Carregar ficheiros]: ./media/media-services-cli/upload-download-files.png
