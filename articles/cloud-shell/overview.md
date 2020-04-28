---
title: Visão geral da Nuvem Azure [ Microsoft Docs
description: Visão geral da Casca de Nuvem Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75832491"
---
# <a name="overview-of-azure-cloud-shell"></a>Visão geral do Azure Cloud Shell
O Azure Cloud Shell é uma shell interativa, autenticada e compatível com browsers para gerir recursos do Azure.
Dá a flexibilidade de escolher a experiência de shell mais adequada ao seu método de trabalho, quer seja baseada em Bash ou no PowerShell.

Tente a partir de shell.azure.com clicando abaixo.

[![Lançamento emcamado](https://shell.azure.com/images/launchcloudshell.png "Iniciar o Azure Cloud Shell")](https://shell.azure.com)

Experimente a partir do portal Azure usando o ícone Cloud Shell.

![Lançamento do portal](media/overview/portal-launch-icon.png)

## <a name="features"></a>Funcionalidades

### <a name="browser-based-shell-experience"></a>Experiência de concha baseada no navegador
A Cloud Shell permite o acesso a uma experiência de linha de comando baseada no navegador, construída com tarefas de gestão Azure em mente.
Aproveite a Cloud Shell para trabalhar sem amarras a partir de uma máquina local de uma forma que só a nuvem pode fornecer.

### <a name="choice-of-preferred-shell-experience"></a>Escolha da experiência de concha preferida
Os utilizadores podem escolher entre Bash ou PowerShell.
1. Selecione **Cloud Shell**.

    ![Ícone de casca de nuvem](media/overview/overview-cloudshell-icon.png)

2. Selecione **Bash** ou **PowerShell**.

    ![Escolha bash ou PowerShell](media/overview/overview-choices.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Estação de trabalho Azure autenticada e configurada
Cloud Shell é gerido pela Microsoft por isso vem com ferramentas populares de linha de comando e suporte linguístico. A Cloud Shell também autentica automaticamente para acesso instantâneo aos seus recursos através dos cmdlets Azure CLI ou Azure PowerShell.

Veja a lista completa [de ferramentas instaladas na Cloud Shell.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Editor integrado da Cloud Shell
A Cloud Shell oferece um editor de texto gráfico integrado baseado no editor de código aberto do Mónaco. Basta criar e editar `code .` ficheiros de configuração executando para uma implementação sem emenda através do Azure CLI ou do Azure PowerShell.

[Saiba mais sobre o editor da Cloud Shell.](using-cloud-shell-editor.md)

### <a name="integrated-with-docsmicrosoftcom"></a>Integrado com docs.microsoft.com

Pode utilizar a Cloud Shell diretamente a partir da documentação hospedada na [docs.microsoft.com](https://docs.microsoft.com). Está integrado na documentação [Microsoft Learn](https://docs.microsoft.com/learn/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) e [Azure CLI](https://docs.microsoft.com/cli/azure) - clique no botão "Try It" num código para abrir a experiência imersiva da concha. 

### <a name="multiple-access-points"></a>Múltiplos pontos de acesso
Cloud Shell é uma ferramenta flexível que pode ser usada a partir de:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure)
* [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Aplicação móvel Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Extensão da conta de código do estúdio visual Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Conecte o armazenamento do Microsoft Azure Files
As máquinas Cloud Shell são temporárias, mas os seus ficheiros são persistidos `clouddrive`de duas formas: através de uma imagem de disco, e através de uma partilha de ficheiros montada chamada .  Na primeira execução, o Cloud Shell pede para criar um grupo de recursos, a conta de armazenamento e a partilha de ficheiros do Azure em seu nome. Este é um passo único e será automaticamente anexado para todas as sessões. Uma única parte de ficheiro pode ser mapeada e será usada tanto pela Bash como pela PowerShell na Cloud Shell.

Leia mais para aprender a montar uma [conta de armazenamento nova ou existente](persisting-shell-storage.md) ou para aprender sobre os mecanismos de persistência [usados na Cloud Shell](persisting-shell-storage.md#how-cloud-shell-storage-works).

> [!NOTE]
> A firewall de armazenamento Azure não é suportada para contas de armazenamento de conchas em nuvem.

## <a name="concepts"></a>Conceitos
* Cloud Shell funciona em um anfitrião temporário fornecido em uma base por sessão, por utilizador
* Cloud Shell sai após 20 minutos sem atividade interativa
* Cloud Shell exige que uma partilha de ficheiros Azure seja montada
* Cloud Shell usa a mesma quota de ficheiroS Azure tanto para a Bash como para a PowerShell
* Cloud Shell é atribuída uma máquina por conta de utilizador
* Cloud Shell persiste $HOME usando uma imagem de 5 GB realizada na sua partilha de ficheiros
* As permissões são definidas como um utilizador regular do Linux em Bash

Saiba mais sobre as funcionalidades em [Bash in Cloud Shell](features.md) e [PowerShell em Cloud Shell](features-powershell.md).

## <a name="pricing"></a>Preços
A máquina que hospeda a Cloud Shell é gratuita, com um pré-requisito de uma partilha de Ficheiros Azure montados. Aplicam-se os custos regulares de armazenamento.

## <a name="next-steps"></a>Passos seguintes
[Bash in Cloud Shell quickstart](quickstart.md) <br>
[PowerShell em Cloud Shell quickstart](quickstart-powershell.md)
