---
title: Tutorial - Crie um servidor Jenkins no Azure
description: Neste tutorial, você instala Jenkins numa máquina virtual Azure Linux a partir do modelo de solução Jenkins e constrói uma aplicação java de amostra.
keywords: jenkins, azure, devops, portal, virtual machine, solution template
ms.topic: tutorial
ms.date: 03/03/2020
ms.openlocfilehash: 3fa8dcbcc0edcc987fa9c4e76fbdb9d7a2f3396c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274901"
---
# <a name="tutorial-create-a-jenkins-server-on-an-azure-linux-vm"></a>Tutorial: Crie um servidor Jenkins num VM Azure Linux 

Este tutorial mostra como instalar [jenkins](https://jenkins.io) num Ubuntu Linux VM com as ferramentas e plug-ins configurados para trabalhar com o Azure. Quando tiver terminado, terá um servidor do Jenkins em execução no Azure e a compilar uma aplicação Java de exemplo a partir do [GitHub](https://github.com).

> [!div class="checklist"]
> * Instale e configure um servidor Jenkins no Azure
> * Aceda à consola Jenkins usando um túnel SSH
> * Criar um projeto Freestyle
> * Compilar o código e embalar a aplicação de amostras

[!INCLUDE [jenkins-install-solution-template-include](../../includes/jenkins-install-solution-template-steps.md)]