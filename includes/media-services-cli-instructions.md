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
ms.openlocfilehash: 5ebbac39c8850737ea6f9ef333e45d305a520655
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461219"
---
## <a name="use-cli-shell"></a>Use concha CLI

Recomenda-se utilizar a [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) para executar comandos CLI. **Cloud Shell** é uma concha interativa gratuita que pode usar para executar os passos deste artigo. As ferramentas comuns do Azure estão pré-instaladas e configuradas no Cloud Shell para que possa utilizá-las com a sua conta. Dá a flexibilidade de escolher a experiência de shell mais adequada ao seu método de trabalho. Os utilizadores do Linux podem optar por uma experiência do Bash, ao passo que os utilizadores do Windows podem optar pelo PowerShell.

Também pode instalar o CLI localmente. Consulte [a instalação do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) para obter instruções para a sua plataforma.

### <a name="sign-in"></a>Iniciar sessão

A utilização de uma instalação local do CLI requer a sua inscrição no Azure. Este passo não é necessário para a Casca de Nuvem Azure. Inscreva-se `az login` no comando.

Se a CLI conseguir abrir o seu browser predefinido, executa essa ação e carrega uma página de início de sessão. Caso contrário, tem de abrir uma página de navegador e seguir as instruções https://aka.ms/devicelogin na linha de comando para introduzir um código de autorização depois de navegar no seu navegador.

### <a name="specify-location-of-files"></a>Especificar a localização dos ficheiros

Muitos comandos CLI de Serviços de Mídia permitem-lhe passar um parâmetro com um nome de ficheiro. Se estiver a utilizar a **Cloud Shell,** pode carregar o seu ficheiro para o seu clouddrive (utilizando o Bash ou o PowerShell). 

![Carregar ficheiros]

Quer esteja a utilizar um CLI local ou **Cloud Shell,** precisa especificar o caminho do ficheiro de acordo com o OS ou Cloud Shell (Bash ou PowerShell) que está a utilizar. Abaixo estão alguns exemplos:

Caminho relativo para o ficheiro (todos os SISTEMA)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Caminho absoluto de ficheiros em Linux/Mac e Windows OS

* `@ "/usr/home/mytestfile.json"`
*    `@"c:\tmp\user\mytestfile.json"`

Use `{file}` se o comando estiver a pedir um caminho para o ficheiro. Por exemplo, `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`. <br/> Utilize `@{file}` se o comando vai carregar o ficheiro especificado. Por exemplo, `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`.

[Carregar ficheiros]: ./media/media-services-cli/upload-download-files.png
