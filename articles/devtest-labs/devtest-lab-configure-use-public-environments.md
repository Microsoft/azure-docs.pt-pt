---
title: Configure e use ambientes públicos em Azure DevTest Labs Microsoft Docs
description: Este artigo descreve como configurar e usar ambientes públicos (modelos de Gestor de Recursos Azure em um git repo) em Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 61cabdb296c3fff75137c7ce7e87652241fd2926
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85482671"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Configure e use ambientes públicos em Azure DevTest Labs
A Azure DevTest Labs tem um [repositório público de modelos do Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) que pode usar para criar ambientes sem ter de se ligar a uma fonte externa do GitHub por si mesmo. Este repositório inclui modelos frequentemente usados, tais como Azure Web Apps, Service Fabric Cluster e desenvolvimento SharePoint Farm ambiente. Esta característica é semelhante ao repositório público de artefactos que está incluído para todos os laboratórios que você cria. O repositório de ambiente permite-lhe começar rapidamente com modelos de ambiente pré-autoria com parâmetros mínimos de entrada para lhe proporcionar uma experiência de início suave para os recursos do PaaS dentro de laboratórios. 

## <a name="configuring-public-environments"></a>Configurar ambientes públicos
Como dono de laboratório, pode ativar o repositório de ambiente público para o seu laboratório durante a criação do laboratório. Para ativar ambientes públicos para o seu laboratório, selecione **On** for the **Public environments** field enquanto cria um laboratório. 

![Permitir o ambiente público para um novo laboratório](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)


Para os laboratórios existentes, o repositório de ambiente público não está habilitado. Ative manualmente a utilização de modelos no repositório. Para laboratórios criados usando modelos de Gestor de Recursos, o repositório também é desativado por padrão.

Pode ativar/desativar ambientes públicos para o seu laboratório e também disponibilizar apenas ambientes específicos aos utilizadores de laboratório utilizando os seguintes passos: 

1. Selecione **Configuração e políticas** para o seu laboratório. 
2. Na secção **BASES DE MÁQUINAS VIRTUAIS,** selecione **ambientes públicos**.
3. Para ativar ambientes públicos para o laboratório, selecione **Sim**. Caso contrário, selecione **Não**. 
4. Se for habilitado ambientes públicos, todos os ambientes do repositório são habilitados por defeitos. Pode desescoducionar um ambiente para não o disponibilizar aos utilizadores do laboratório. 

![Página de ambientes públicos](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>Use modelos de ambiente como um utilizador de laboratório
Como utilizador de laboratório, pode criar um novo ambiente a partir da lista ativada de modelos de ambiente selecionando **simplesmente +Adicionar** a partir da barra de ferramentas na página de laboratório. A lista de bases inclui os modelos de ambientes públicos ativados pelo seu administrador de laboratório no topo da lista.

![Modelos de ambiente público](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>Passos seguintes
Este repositório é um repositório de código aberto que você pode contribuir para adicionar modelos de Gestor de Recursos frequentemente usados e úteis de seu próprio. Para contribuir, basta apresentar um pedido de atração contra o repositório.  
