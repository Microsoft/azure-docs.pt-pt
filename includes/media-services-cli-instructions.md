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
ms.openlocfilehash: 8439da94c770bee313a1ae1d1da5df30683cd2ad
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57964254"
---
## <a name="cli-shell"></a>Shell CLI

É recomendado que utilize [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) para executar comandos da CLI. **Cloud Shell** é uma shell gratuita e interativa que pode utilizar para executar os passos neste artigo. As ferramentas comuns do Azure estão pré-instaladas e configuradas no Cloud Shell para que possa utilizá-las com a sua conta. Ele fornece a flexibilidade de escolher a experiência de shell mais adequada às estilo de que trabalho. Os utilizadores de Linux podem optar por uma experiência de Bash, ao passo que os utilizadores de Windows podem escolher o PowerShell.

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

### <a name="login"></a>Iniciar sessão

Para começar a utilizar a shell CLI (na nuvem ou localmente), execute `az login` para criar uma ligação com o Azure.

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
