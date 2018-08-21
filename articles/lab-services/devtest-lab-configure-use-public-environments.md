---
title: Configurar e utilizar ambientes públicos no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como configurar e utilizar ambientes públicos no Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2018
ms.author: spelluru
ms.openlocfilehash: d93818cd875c4050b1b35f21ce580933776c5bc5
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235028"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Configurar e utilizar ambientes públicos no Azure DevTest Labs
O Azure DevTest Labs tem um [repositório público dos modelos do Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) que pode utilizar para criar ambientes sem ter de ligar a uma origem externa do GitHub sozinho. Este repositório inclui modelos usados com freqüência, como aplicações Web do Azure, o Cluster do Service Fabric e o desenvolvimento de ambiente de Farm do SharePoint. Esta funcionalidade é semelhante para o repositório público de artefactos que é incluído para todos os laboratórios que criar. O repositório de ambiente permite-lhe começar a utilizar rapidamente com modelos de ambiente previamente criados com parâmetros de entrada mínimos para lhe fornecer uma experiência de introdução ao obter uniforme para os recursos de PaaS dentro de laboratórios. 

## <a name="configuring-public-environments"></a>Configurar ambientes públicos
Como proprietário de um laboratório, pode ativar o repositório de ambiente público para o laboratório durante a criação de laboratório. Para ativar ambientes públicos para seu laboratório, selecione **nos** para o **ambientes públicos** campo ao criar um laboratório. 

![Ativar o ambiente público para um novo laboratório](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)


Para obter laboratórios existentes, o repositório de ambiente público não está ativado. Manualmente ativá-la utilizar modelos no repositório. Para obter laboratórios criados com modelos do Resource Manager, o repositório está desativado por predefinição também.

Pode ativar/desativar ambientes públicos para seu laboratório e também tornar apenas ambientes específicos disponíveis para os utilizadores de laboratório utilizando os seguintes passos: 

1. Selecione **Konfigurace a zásady** para seu laboratório. 
2. Na **BASES de máquina VIRTUAL** secção, selecione **ambientes públicos**.
3. Para ativar ambientes públicos para o laboratório, selecione **Sim**. Caso contrário, selecione **não**. 
4. Se ativou a ambientes públicos, todos os ambientes no repositório estão ativados por padrões. Anular pode selecionar um ambiente para torná-lo não está disponível para os utilizadores de laboratório. 

![Página de ambientes públicos](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>Utilize modelos de ambiente como um utilizador de laboratório
Como um utilizador de laboratório, pode criar um novo ambiente da lista de modelos de ambiente ativada selecionando simplesmente **+ adicionar** da barra de ferramentas, na página do laboratório. A lista de bases de inclui os modelos de ambientes públicos ativados pelo seu administrador de laboratório na parte superior da lista.

![Modelos de ambiente público](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>Passos Seguintes
Este repositório é um repositório de código-fonte aberto que pode contribuir para adicionar, com frequência, útil e utilizados modelos do Resource Manager do seu próprio. Para contribuir, simplesmente submeta um pedido pull para o repositório.  
