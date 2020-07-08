---
title: Visão geral da Azure Cloud Shell Microsoft Docs
description: Visão geral da Concha da Nuvem Azure.
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
ms.openlocfilehash: 513c3da8031774f5f111ee357b5a3c43e1d09d95
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75832491"
---
# <a name="overview-of-azure-cloud-shell"></a>Visão geral do Azure Cloud Shell
O Azure Cloud Shell é uma shell interativa, autenticada e compatível com browsers para gerir recursos do Azure.
Dá a flexibilidade de escolher a experiência de shell mais adequada ao seu método de trabalho, quer seja baseada em Bash ou no PowerShell.

Tente a partir de shell.azure.com clicando abaixo.

[![Lançamento incorporado](https://shell.azure.com/images/launchcloudshell.png "Iniciar o Azure Cloud Shell")](https://shell.azure.com)

Experimente a partir do portal Azure usando o ícone Cloud Shell.

![Lançamento do Portal](media/overview/portal-launch-icon.png)

## <a name="features"></a>Funcionalidades

### <a name="browser-based-shell-experience"></a>Experiência de concha baseada no navegador
A Cloud Shell permite o acesso a uma experiência de linha de comando baseada no navegador, construída com as tarefas de gestão Azure em mente.
Aproveite a Cloud Shell para trabalhar sem amarras de uma máquina local de uma forma que só a nuvem pode fornecer.

### <a name="choice-of-preferred-shell-experience"></a>Escolha da experiência de concha preferida
Os utilizadores podem escolher entre Bash ou PowerShell.
1. Selecione **Cloud Shell**.

    ![Ícone de concha de nuvem](media/overview/overview-cloudshell-icon.png)

2. Selecione **Bash** ou **PowerShell**.

    ![Escolha ou Bash ou PowerShell](media/overview/overview-choices.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Estação de trabalho Azure autenticada e configurada
A Cloud Shell é gerida pela Microsoft, por isso vem com ferramentas populares de linha de comando e suporte linguístico. A Cloud Shell também autentica automaticamente o acesso instantâneo aos seus recursos através dos cmdletes Azure CLI ou Azure PowerShell.

Veja a lista completa [de ferramentas instaladas na Cloud Shell.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Editor integrado da Cloud Shell
A Cloud Shell oferece um editor de texto gráfico integrado baseado no editor de código aberto do Mónaco. Basta criar e editar ficheiros de configuração executando `code .` para implementação perfeita através do Azure CLI ou do Azure PowerShell.

[Saiba mais sobre o editor da Cloud Shell.](using-cloud-shell-editor.md)

### <a name="integrated-with-docsmicrosoftcom"></a>Integrado com docs.microsoft.com

Pode utilizar a Cloud Shell diretamente a partir da documentação hospedada no [docs.microsoft.com](https://docs.microsoft.com). Está integrado na documentação [do Microsoft Learn](https://docs.microsoft.com/learn/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) e [Azure CLI](https://docs.microsoft.com/cli/azure) - clique no botão "Try It" num corte de código para abrir a experiência imersiva da concha. 

### <a name="multiple-access-points"></a>Vários pontos de acesso
Cloud Shell é uma ferramenta flexível que pode ser usada a partir de:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure)
* [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Aplicativo móvel Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Extensão da conta visual Studio Code Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Ligue o armazenamento do Microsoft Azure Files
As máquinas Cloud Shell são temporárias, mas os seus ficheiros persistem de duas formas: através de uma imagem de disco, e através de uma partilha de ficheiros montada chamada `clouddrive` .  Na primeira execução, o Cloud Shell pede para criar um grupo de recursos, a conta de armazenamento e a partilha de ficheiros do Azure em seu nome. Este é um passo único e será automaticamente anexado para todas as sessões. Uma única partilha de ficheiros pode ser mapeada e será usada tanto pela Bash como pela PowerShell na Cloud Shell.

Leia mais para saber como montar uma [conta de armazenamento nova ou existente](persisting-shell-storage.md) ou para aprender sobre os [mecanismos de persistência utilizados na Cloud Shell](persisting-shell-storage.md#how-cloud-shell-storage-works).

> [!NOTE]
> A firewall de armazenamento Azure não é suportada para contas de armazenamento de conchas em nuvem.

## <a name="concepts"></a>Conceitos
* Cloud Shell funciona em um anfitrião temporário fornecido por sessão, por utilizador
* Cloud Shell acaba após 20 minutos sem atividade interativa
* Cloud Shell requer uma partilha de ficheiros Azure para ser montada
* Cloud Shell usa a mesma partilha de ficheiros Azure tanto para Bash como PowerShell
* Cloud Shell é atribuída uma máquina por conta de utilizador
* Cloud Shell persiste $HOME usando uma imagem de 5 GB na sua partilha de ficheiros
* As permissões são definidas como um utilizador normal do Linux em Bash

Saiba mais sobre as funcionalidades em [Bash em Cloud Shell](features.md) e [PowerShell em Cloud Shell.](features-powershell.md)

## <a name="pricing"></a>Preços
A máquina que hospeda a Cloud Shell é gratuita, com um pré-requisito de uma partilha de Ficheiros Azure montados. Aplicam-se custos regulares de armazenamento.

## <a name="next-steps"></a>Próximos passos
[Bash em Cloud Shell quickstart](quickstart.md) <br>
[PowerShell em Cloud Shell quickstart](quickstart-powershell.md)
