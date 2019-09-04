---
title: Visão geral de Azure Cloud Shell | Microsoft Docs
description: Visão geral do Azure Cloud Shell.
services: ''
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/03/2019
ms.author: damaerte
ms.openlocfilehash: acdf52022d8498a186ed4cff90284cd6e299d6c0
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240849"
---
# <a name="overview-of-azure-cloud-shell"></a>Visão geral do Azure Cloud Shell
Azure Cloud Shell é um shell interativo, autenticado e acessível por navegador para gerenciar recursos do Azure.
Ele fornece a flexibilidade de escolher a experiência de shell que melhor se adapta à maneira como você trabalha, seja bash ou PowerShell.

Tente shell.azure.com clicando abaixo.

[![Lançamento de inserção](https://shell.azure.com/images/launchcloudshell.png "Iniciar Azure cloud Shell")](https://shell.azure.com)

Tente portal do Azure usando o ícone de Cloud Shell.

![Inicialização do portal](media/overview/portal-launch-icon.png)

## <a name="features"></a>Funcionalidades

### <a name="browser-based-shell-experience"></a>Experiência de shell baseada em navegador
Cloud Shell permite o acesso a uma experiência de linha de comando baseada em navegador criada com as tarefas de gerenciamento do Azure em mente.
Aproveite Cloud Shell para trabalhar de modo não compartilhamento de máquina local de uma forma que apenas a nuvem possa fornecer.

### <a name="choice-of-preferred-shell-experience"></a>Opção de experiência de shell preferencial
Os usuários podem escolher entre o bash ou o PowerShell na lista suspensa do Shell.

![Bash em Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell no Cloud Shell](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Estação de trabalho do Azure autenticada e configurada
O Cloud Shell é gerenciado pela Microsoft para que ele seja fornecido com ferramentas de linha de comando populares e suporte a idiomas. O Cloud Shell também é autenticado de forma segura automaticamente para acesso instantâneo aos seus recursos por meio dos cmdlets CLI do Azure ou Azure PowerShell.

Exiba a [lista completa de ferramentas instaladas no Cloud Shell.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Editor de Cloud Shell integrado
O Cloud Shell oferece um editor de texto gráfico integrado com base no editor de Mônaco de código-fonte aberto. Basta criar e editar arquivos de configuração executando `code .` para implantação direta por meio de CLI do Azure ou Azure PowerShell.

[Saiba mais sobre o editor de Cloud Shell](using-cloud-shell-editor.md).

### <a name="integrated-with-docsmicrosoftcom"></a>Integrado ao docs.microsoft.com

Você pode usar Cloud Shell diretamente da documentação hospedada em [docs.Microsoft.com](https://docs.microsoft.com). Ele está integrado no [Microsoft Learn](https://docs.microsoft.com/learn/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) e [CLI do Azure documentação](https://docs.microsoft.com/cli/azure) -clique no botão "Experimente" em um trecho de código para abrir a experiência de Shell de imersão. 

### <a name="multiple-access-points"></a>Vários pontos de acesso
Cloud Shell é uma ferramenta flexível que pode ser usada em:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Documentação do CLI do Azure](https://docs.microsoft.com/cli/azure)
* [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [aplicativo móvel do Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Visual Studio Code extensão de conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Conectar o armazenamento de arquivos de Microsoft Azure
Cloud Shell computadores são temporários, mas os arquivos são persistidos de duas maneiras: por meio de uma imagem de disco e por meio de `clouddrive`um compartilhamento de arquivos montado chamado.  Na primeira inicialização, Cloud Shell solicita a criação de um grupo de recursos, uma conta de armazenamento e um compartilhamento de arquivos do Azure em seu nome. Essa é uma etapa única e será automaticamente anexada a todas as sessões. Um único compartilhamento de arquivos pode ser mapeado e será usado pelo bash e pelo PowerShell em Cloud Shell.

Leia mais para saber como montar uma [conta de armazenamento nova ou existente](persisting-shell-storage.md) ou para saber mais sobre os [mecanismos de persistência usados no Cloud Shell](persisting-shell-storage.md#how-cloud-shell-storage-works).

## <a name="concepts"></a>Conceitos
* Cloud Shell é executado em um host temporário fornecido em uma base por sessão, por usuário
* Cloud Shell expira após 20 minutos sem atividade interativa
* Cloud Shell requer que um compartilhamento de arquivos do Azure seja montado
* Cloud Shell usa o mesmo compartilhamento de arquivos do Azure para o bash e o PowerShell
* O Cloud Shell é atribuído a uma máquina por conta de usuário
* Cloud Shell persiste $HOME usando uma imagem de 5 GB mantida em seu compartilhamento de arquivos
* As permissões são definidas como um usuário normal do Linux no bash

Saiba mais sobre os recursos do [bash no Cloud Shell](features.md) e [no PowerShell no Cloud Shell](features-powershell.md).

## <a name="pricing"></a>Preços
O computador que hospeda o Cloud Shell é gratuito, com um pré-requisito de um compartilhamento de arquivos do Azure montado. Os custos de armazenamento regulares se aplicam.

## <a name="next-steps"></a>Passos Seguintes
[Bash no guia de início rápido Cloud Shell](quickstart.md) <br>
[PowerShell no guia de início rápido Cloud Shell](quickstart-powershell.md)
