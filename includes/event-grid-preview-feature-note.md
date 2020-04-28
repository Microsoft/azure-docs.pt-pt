---
title: incluir ficheiro
description: incluir ficheiro
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 11/06/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: d32beb2d799a60cb9c5be061c39e4ec834da8dcf
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "66814708"
---
Esta funcionalidade está em pré-visualização. Para usá-lo, tem de instalar uma extensão de pré-visualização ou módulo.

### <a name="install-extension-for-azure-cli"></a>Instalar extensão para o Azure CLI

Para o Azure CLI, necessita da extensão da Grelha de [Eventos](/cli/azure/azure-cli-extensions-list).

Em [CloudShell:](/azure/cloud-shell/quickstart)

* Se tiver instalado a extensão anteriormente, atualize-a`az extension update -n eventgrid`
* Se não tiver instalado a extensão anteriormente, instale-a`az extension add -n eventgrid`

Para uma instalação local:

1. [Instale o Azure CLI](/cli/azure/install-azure-cli). Certifique-se de que tem a versão `az --version`mais recente, verificando com .
1. Desinstalar versões anteriores da extensão`az extension remove -n eventgrid`
1. Instalar `eventgrid` a extensão com`az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>Instale módulo para PowerShell

Para a PowerShell, necessita do [módulo AzureRM.EventGrid](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

Em [CloudShell:](/azure/cloud-shell/quickstart-powershell)

* Instalar o módulo`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Para uma instalação local:

1. Consola Open PowerShell como administrador
1. Instalar o módulo`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Se `-AllowPrerelease` o parâmetro não estiver disponível, utilize os seguintes passos:

1. Execute `Install-Module PowerShellGet -Force`
1. Execute `Update-Module PowerShellGet`
1. Feche a consola PowerShell
1. Reiniciar a PowerShell como administrador
1. Instalar o módulo`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`
