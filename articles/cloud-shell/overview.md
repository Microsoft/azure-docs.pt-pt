---
title: Descrição geral do Azure Cloud Shell | Documentos da Microsoft
description: Descrição geral sobre o Azure Cloud Shell.
services: ''
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: damaerte
ms.openlocfilehash: 5608b3e0f9b98db62d22245de5a864f757f48799
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243980"
---
# <a name="overview-of-azure-cloud-shell"></a>Descrição geral do Azure Cloud Shell
O Azure Cloud Shell é um shell interativo, acessível para o browser para o gerenciamento de recursos do Azure.
Ele fornece a flexibilidade de escolher a experiência de shell mais adequada às estilo de que trabalho.
Os utilizadores de Linux podem optar por uma experiência de Bash, ao passo que os utilizadores de Windows podem escolher o PowerShell.

Experimente a partir de shell.azure.com clicando-se abaixo.

[![Lançamento de incorporação](https://shell.azure.com/images/launchcloudshell.png "iniciar o Azure Cloud Shell")](https://shell.azure.com)

Experimente a partir do portal do Azure com o ícone do Cloud Shell.

![Lançamento de portal](media/overview/portal-launch-icon.png)

## <a name="features"></a>Funcionalidades

### <a name="browser-based-shell-experience"></a>Experiência de shell baseada no browser
Cloud Shell permite o acesso a uma experiência da linha de comandos baseada no browser e criado com tarefas de gestão do Azure em mente.
Tire partido dos Cloud Shell para trabalhar untethered de um computador local de uma forma apenas para a cloud pode fornecer.

### <a name="choice-of-preferred-shell-experience"></a>Opção de experiência de shell preferida
Os utilizadores podem escolher entre o Bash ou o PowerShell no menu pendente do shell.

![Bash no Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell no Cloud Shell](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Autenticado e configurada estação de trabalho do Azure
Cloud Shell é gerida pela Microsoft para que ele vem com ferramentas de linha de comandos populares e suporte de idiomas. Cloud Shell segura é autenticado automaticamente para acesso imediato a seus recursos através dos cmdlets de CLI do Azure ou do Azure PowerShell.

Ver toda a [lista de ferramentas instaladas no Cloud Shell.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Editor do Cloud Shell integrado
Cloud Shell oferece um editor de texto gráfica integrada com base no Editor de Mónaco de código-fonte aberto. Basta criar e editar ficheiros de configuração ao executar `code .` para implementação totalmente integrada através da CLI do Azure ou do Azure PowerShell.

[Saiba mais sobre o editor do Cloud Shell](using-cloud-shell-editor.md).

### <a name="integrated-with-docsmicrosoftcom"></a>Integrado no docs.microsoft.com

Pode utilizar o Cloud Shell diretamente a partir de documentação alojada num [docs.microsoft.com](https://docs.microsoft.com). Ele está integrado no [Microsoft Learn](https://docs.microsoft.com/learn/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) e [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure) -clique no botão "Experimente-lo" um trecho de código para abrir a shell de imersão experiência. 

### <a name="multiple-access-points"></a>Vários pontos de acesso
O cloud Shell é uma ferramenta flexível que pode ser usada em:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure)
* [Documentação do PowerShell do Azure](https://docs.microsoft.com/powershell/azure/overview)
* [Aplicação móvel do Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Extensão do Visual Studio conta do Azure de código](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Ligar o seu armazenamento de ficheiros do Microsoft Azure
Cloud Shell máquinas são temporárias e requerem uma partilha de ficheiros do Azure nova ou existente para ser montado como `clouddrive` para manter os ficheiros.

Na primeira execução que cloud Shell pede-lhe para criar um recurso do grupo de conta de armazenamento e ficheiros do Azure partilham em seu nome. Este é um passo de uma única vez e será automaticamente anexado para todas as sessões. Uma partilha de ficheiros único pode ser mapeada e será utilizada pelo Bash e o PowerShell no Cloud Shell.

Leia mais para aprender a montar um [conta de armazenamento nova ou existente](persisting-shell-storage.md).

## <a name="concepts"></a>Conceitos
* Cloud Shell é executado num anfitrião temporário fornecido numa por sessão, por utilizador
* Cloud Shell exceder o tempo limite após 20 minutos sem atividade interativa
* Cloud Shell requer uma partilha de ficheiros do Azure de ser montado
* Cloud Shell utiliza a mesma partilha de ficheiros do Azure para o Bash e o PowerShell
* Cloud Shell é atribuído uma máquina por conta de utilizador
* Cloud Shell persistir $HOME com uma imagem de 5 GB mantida na partilha de ficheiros
* As permissões estão definidas como um usuário normal do Linux no Bash

Saiba mais sobre as funcionalidades nas [Bash no Cloud Shell](features.md) e [PowerShell no Cloud Shell](features-powershell.md).

## <a name="pricing"></a>Preços
A máquina que aloja o Cloud Shell é gratuita, um pré-requisito de uma partilha de ficheiros do Azure montada. Os custos de armazenamento normais aplicam-se.

## <a name="next-steps"></a>Passos Seguintes
[Guia de introdução do Cloud Shell de bash](quickstart.md) <br>
[PowerShell no início rápido do Cloud Shell](quickstart-powershell.md)
