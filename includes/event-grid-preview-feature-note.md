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
ms.openlocfilehash: 85a1579e32b4c216f234f77c76316bedeaea77b0
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66119551"
---
Esta funcionalidade está em pré-visualização. Para usá-lo, tem de instalar uma extensão de pré-visualização ou um módulo.

### <a name="install-extension-for-azure-cli"></a>Instalar a extensão de CLI do Azure

Para a CLI do Azure, terá do [extensão do Event Grid](/cli/azure/azure-cli-extensions-list).

In [CloudShell](/azure/cloud-shell/quickstart):

* Se tiver instalado anteriormente a extensão, atualizá-lo `az extension update -n eventgrid`
* Se ainda não instalou a extensão anteriormente, instale-o `az extension add -n eventgrid`

Para uma instalação local:

1. Desinstale a CLI do Azure localmente.
1. Instalar o [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.
1. Inicie a janela de comando.
1. Desinstale as versões anteriores da extensão `az extension remove -n eventgrid`
1. Instalar a extensão `az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>Instalar o módulo do PowerShell

Para o PowerShell, terá do [AzureRM.EventGrid módulo](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

In [CloudShell](/azure/cloud-shell/quickstart-powershell):

* Instalar o módulo `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Para uma instalação local:

1. Abra a consola do PowerShell como administrador
1. Instalar o módulo `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Se o `-AllowPrerelease` parâmetro não estiver disponível, utilize os seguintes passos:

1. Execute `Install-Module PowerShellGet -Force`
1. Execute `Update-Module PowerShellGet`
1. Feche a consola do PowerShell
1. Reinicie o PowerShell como administrador
1. Instalar o módulo `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`
