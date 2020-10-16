---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/28/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 5ebbac39c8850737ea6f9ef333e45d305a520655
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "79461219"
---
## <a name="use-cli-shell"></a>Use a concha CLI

Recomenda-se a utilização [de Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) para executar comandos CLI. **Cloud Shell** é uma concha interativa e gratuita que pode usar para executar os passos neste artigo. As ferramentas comuns do Azure estão pré-instaladas e configuradas no Cloud Shell para que possa utilizá-las com a sua conta. Dá a flexibilidade de escolher a experiência de shell mais adequada ao seu método de trabalho. Os utilizadores do Linux podem optar por uma experiência do Bash, ao passo que os utilizadores do Windows podem optar pelo PowerShell.

Também pode instalar o CLI localmente. Consulte [a Instalação do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) para obter instruções para a sua plataforma.

### <a name="sign-in"></a>Iniciar sessão

A utilização de uma instalação local do CLI requer a inscrição no Azure. Este passo não é necessário para a Azure Cloud Shell. Inscreva-se com o `az login` comando.

Se a CLI conseguir abrir o seu browser predefinido, executa essa ação e carrega uma página de início de sessão. Caso contrário, é necessário abrir uma página de navegador e seguir as instruções da linha de comando para introduzir um código de autorização depois de navegar https://aka.ms/devicelogin no seu navegador.

### <a name="specify-location-of-files"></a>Especificar a localização dos ficheiros

Muitos comandos CLI de Serviços de Mídia permitem-lhe passar um parâmetro com um nome de ficheiro. Se estiver a utilizar **o Cloud Shell,** pode enviar o seu ficheiro para a sua clouddrive (utilizando Bash ou PowerShell). 

![Carregar ficheiros]

Quer esteja a utilizar um CLI local ou **uma Cloud Shell,** tem de especificar o caminho do ficheiro de acordo com o SISTEMA ou Cloud Shell (Bash ou PowerShell) que está a utilizar. Abaixo estão alguns exemplos:

Caminho relativo para o ficheiro (todos os SO)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Caminho de arquivo absoluto em Linux/Mac e Windows OS

* `@ "/usr/home/mytestfile.json"`
*    `@"c:\tmp\user\mytestfile.json"`

Use `{file}` se o comando estiver pedindo um caminho para o arquivo. Por exemplo, `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`. <br/> Utilize `@{file}` se o comando vai carregar o ficheiro especificado. Por exemplo, `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`.

[Carregar ficheiros]: ./media/media-services-cli/upload-download-files.png
